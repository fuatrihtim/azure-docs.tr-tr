---
title: Kavramlar-Azure Kubernetes hizmetlerinde (AKS) depolama
description: Azure Kubernetes Service 'teki (AKS) birimler, kalıcı birimler, depolama sınıfları ve talepler dahil depolama hakkında bilgi edinin
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127966"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki uygulamalar için depolama seçenekleri

Azure Kubernetes Service (AKS) içinde çalışan uygulamaların veri depolaması ve alması gerekebilir. Bazı uygulama iş yükleri için bu veri depolama alanı, düğüm silindiğinde artık gerekli olmayan yerel ve hızlı depolama alanını kullanabilir. Diğer uygulama iş yükleri, Azure platformunda daha düzenli veri birimlerinde devam eden bir depolama alanı gerektirebilir. Birden çok düğüm aynı veri birimlerini paylaşması veya Pod, farklı bir düğümde yeniden zamanlanırsa veri birimlerini yeniden iliştirmeniz gerekebilir. Son olarak, önemli verileri veya uygulama yapılandırma bilgilerini pods 'ye eklemeniz gerekebilir.

![Azure Kubernetes Services (AKS) kümesindeki uygulamalar için depolama seçenekleri](media/concepts-storage/aks-storage-options.png)

Bu makalede AKS 'de uygulamalarınıza depolama sağlayan temel kavramlar tanıtılmaktadır:

- [Birimler](#volumes)
- [Kalıcı birimler](#persistent-volumes)
- [Depolama sınıfları](#storage-classes)
- [Kalıcı birim talepleri](#persistent-volume-claims)

## <a name="volumes"></a>Birimler

Uygulamalar genellikle veri depolayabilmek ve alabilmesi gerekir. Kubernetes tipik olarak tek tek kaynakları, atılabilir kaynakları olarak değerlendirir ve uygulamaların verileri gerektiği şekilde kullanabilmesi ve kalıcı hale getirilebilmesi için farklı yaklaşımlar mevcuttur. Bir *birim* , veri ve uygulama yaşam döngüsü boyunca verileri depolamanın, almanın ve kalıcı hale getirmenin bir yolunu temsil eder.

Verileri depolamak ve almak için geleneksel birimler, Azure depolama tarafından desteklenen Kubernetes kaynakları olarak oluşturulur. Bu veri birimlerini doğrudan veya Pod 'ye atanmak üzere el ile oluşturabilirsiniz veya Kubernetes onları otomatik olarak oluşturabilir. Bu veri birimleri, Azure disklerini veya Azure dosyalarını kullanabilir:

- *Azure diskleri* , bir Kubernetes *veri diski* kaynağı oluşturmak için kullanılabilir. Diskler, yüksek performanslı SSD 'Ler tarafından desteklenen Azure Premium Depolama veya normal HDD 'Ler tarafından desteklenen Azure Standart depolama kullanabilir. Çoğu üretim ve geliştirme iş yükleri için Premium depolama kullanın. Azure diskleri *Readwriteonce* olarak bağlanır, bu nedenle yalnızca tek bir pod için kullanılabilir. Aynı anda birden çok dizin tarafından erişilebilen depolama birimlerinde Azure dosyalarını kullanın.
- *Azure dosyaları* , Azure depolama hesabı tarafından desteklenen bir SMB 3,0 paylaşımının pods 'ye bağlanması için kullanılabilir. Dosyalar, verileri birden çok düğümde ve düğüm genelinde paylaşmanızı sağlar. Dosyalar, yüksek performanslı SSD 'Ler tarafından desteklenen normal HDD 'Ler veya Azure Premium Depolama tarafından desteklenen Azure Standart depolama alanını kullanabilir.

Kubernetes 'te birimler, bilgilerin saklanabileceği ve alınabileceği yalnızca geleneksel bir diskten fazlasını temsil edebilir. Kubernetes birimleri, kapsayıcılar tarafından kullanılmak üzere bir pod 'a veri eklemenin bir yolu olarak da kullanılabilir. Kubernetes 'te ortak ek birim türleri şunlardır:

- *Emptydir* -bu birim genellikle pod için geçici alan olarak kullanılır. Pod içindeki tüm kapsayıcılar birimdeki verilere erişebilir. Bu birim türüne yazılan veriler yalnızca Pod 'un kullanım ömrü boyunca devam ettirir-Pod silindiğinde birim silinir. Bu birim genellikle temel alınan yerel düğüm Disk depolamayı kullanır, ancak yalnızca düğümün belleğinde de bulunabilir.
- *gizli* -bu birim parola gibi önemli verileri pods 'ye eklemek için kullanılır. İlk olarak Kubernetes API 'sini kullanarak bir gizli dizi oluşturursunuz. Pod veya dağıtımınızı tanımladığınızda, belirli bir gizli dizi istenebilir. Gizli diziler yalnızca, bir zamanlanmış Pod içeren düğümlere sağlanır ve gizli dizi diske yazılmadı ve *tmpfs*'de depolanır. Gizli anahtar gerektiren bir düğümdeki son Pod silindiğinde, parola, düğümün tmpfs 'den silinir. Gizli diziler belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.
- *Configmap* -bu birim türü, anahtar-değer çifti özelliklerini pods 'ye eklemek için kullanılır (örneğin, uygulama yapılandırma bilgileri). Bir kapsayıcı görüntüsü içinde uygulama yapılandırma bilgilerini tanımlamak yerine, bu dosyayı kolayca güncelleştirilebileceği ve dağıtıldığı için yeni düğüm örneklerine uygulanabilecek bir Kubernetes kaynağı olarak tanımlayabilirsiniz. Gizli anahtar kullanma gibi, ilk olarak Kubernetes API 'sini kullanarak bir ConfigMap oluşturursunuz. Bu ConfigMap, daha sonra bir pod veya dağıtım tanımladığınızda istenebilir. Configmaps, belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.

## <a name="persistent-volumes"></a>Kalıcı birimler

Pod yaşam döngüsünün bir parçası olarak tanımlanan ve oluşturulan birimler yalnızca Pod silinene kadar bulunur. Bir pod, özellikle de StatefulSets 'de bakım olayı sırasında farklı bir konakta yeniden zamanlanırsa, genellikle depolama alanının kalmasını bekler. *Kalıcı bir birim* (BD), Kubernetes API 'si tarafından oluşturulan ve yönetilen bir depolama kaynağıdır ve tek bir pod 'ın yaşam süresinden daha fazla olabilir.

Azure diskleri veya dosyaları, PersistentVolume sağlamak için kullanılır. Birimlerin önceki bölümünde belirtildiği gibi, disk veya dosya seçimi genellikle verilere veya performans katmanına eşzamanlı erişim gereksinimi ile belirlenir.

![Azure Kubernetes Services (AKS) kümesindeki kalıcı birimler](media/concepts-storage/persistent-volumes.png)

PersistentVolume bir küme yöneticisi tarafından *statik* olarak veya Kubernetes API sunucusu tarafından *dinamik olarak* oluşturulabilir. Bir pod zamanlanırsa ve şu anda kullanılamayan depolama alanı istiyorsa Kubernetes, temel alınan Azure disk veya dosya depolama alanını oluşturabilir ve pod 'a ekler. Dinamik sağlama, hangi tür Azure depolama biriminin oluşturulması gerektiğini belirlemek için bir *Storageclass* kullanır.

## <a name="storage-classes"></a>Depolama sınıfları

Premium ve standart gibi farklı depolama katmanlarını tanımlamak için bir *Storageclass* oluşturabilirsiniz. StorageClass Ayrıca *reclaimPolicy* tanımlar. Bu reclaimPolicy, Pod silindiğinde ve kalıcı birim artık gerekmiyorsa, temel alınan Azure depolama kaynağının davranışını denetler. Temel alınan depolama kaynağı silinebilir veya gelecekteki bir pod ile kullanım için korunabilir.

AKS 'de, ağaç içi `StorageClasses` depolama eklentileri kullanılarak küme için dört başlangıç oluşturulur:

- `default` -Yönetilen bir disk oluşturmak için Azure StandardSSD depolama kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure diskinin silinmesini sağlar.
- `managed-premium` -Yönetilen bir disk oluşturmak için Azure Premium depolama kullanır. Geri kazanma ilkesi, kendisini kullanan kalıcı birim silindiğinde temeldeki Azure diskinin silinmesini sağlar.
- `azurefile` -Azure Standart depolama kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.
- `azurefile-premium` -Azure dosya paylaşımının oluşturulması için Azure Premium depolama kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.

Yeni kapsayıcı depolama arabirimi (CSı) dış eklentilerini (Önizleme) kullanan kümeler için aşağıdaki ek `StorageClasses` oluşturulur:
- `managed-csi` -Yönetilen bir disk oluşturmak için Azure Standartssd yerel olarak yedekli depolama (LRS) kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure diskinin silinmesini sağlar. Depolama sınıfı ayrıca kalıcı birimleri genişletilebilir olacak şekilde yapılandırır, yalnızca yeni boyutla kalıcı birim talebi düzenlemeniz gerekir.
- `managed-csi-premium` -Yönetilen bir disk oluşturmak için Azure Premium yerel olarak yedekli depolama (LRS) kullanır. Geri kazanma ilkesi, kendisini kullanan kalıcı birim silindiğinde temeldeki Azure diskinin silinmesini sağlar. Benzer şekilde, bu depolama sınıfı kalıcı birimlerin genişletilmesini sağlar.
- `azurefile-csi` -Azure Standart depolama kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.
- `azurefile-csi-premium` -Azure dosya paylaşımının oluşturulması için Azure Premium depolama kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.

Kalıcı birim için bir StorageClass belirtilmemişse, varsayılan StorageClass kullanılır. Kalıcı birimler istemek için gereken uygun depolama alanını kullanmaları için dikkatli olmanız gerekir. Kullanarak ek gereksinimler için bir StorageClass oluşturabilirsiniz `kubectl` . Aşağıdaki örnek, Premium yönetilen diskleri kullanır ve pod silindiğinde temel alınan Azure diskinin *korunması* gerektiğini belirtir:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS varsayılan depolama sınıflarını uzlaştırır ve bu depolama sınıflarında yaptığınız tüm değişikliklerin üzerine yazar.

## <a name="persistent-volume-claims"></a>Kalıcı birim talepleri

PersistentVolumeClaim, belirli bir StorageClass, erişim modu ve boyutun disk veya dosya depolamasını ister. Kubernetes API sunucusu, tanımlanan StorageClass temelinde talebi yerine getirmek için mevcut bir kaynak yoksa Azure 'da temel alınan depolama kaynağını dinamik olarak sağlayabilir. Pod tanımı, birim Pod 'a bağlandıktan sonra birim bağlama içerir.

![Azure Kubernetes Services (AKS) kümesinde kalıcı birim talepleri](media/concepts-storage/persistent-volume-claims.png)

Bir PersistentVolume, kullanılabilir bir depolama kaynağı tarafından istenen Pod 'a atandıktan sonra bir PersistentVolumeClaim 'e *bağlanır* . Talepler için kalıcı birimlerin 1:1 eşlemesi vardır.

Aşağıdaki örnek YAML bildirimi, *yönetilen-Premium* storageclass kullanan kalıcı bir birim talebi gösterir ve şu boyutta bir disk *5 gi* ister:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Pod tanımı oluşturduğunuzda, kalıcı birim talebi istenen depolamayı istemek için belirtilir. Ayrıca, veri okumak ve yazmak için uygulamalarınız için *Volumemount* öğesini de belirtirsiniz. Aşağıdaki örnek YAML bildirimi, önceki kalıcı birim talebinin, */mnt/Azure*' da bir birimi bağlamak için nasıl kullanılabileceğini gösterir:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Bir Windows kapsayıcısında bir birimi bağlamak için, sürücü harfini ve yolunu belirtin. Örnek:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

Azure diskleri veya Azure dosyaları kullanan dinamik ve statik birimler oluşturmayı öğrenmek için aşağıdaki nasıl yapılır makalelerine bakın:

- [Azure disklerini kullanarak statik birim oluşturma][aks-static-disks]
- [Azure dosyalarını kullanarak statik birim oluşturma][aks-static-files]
- [Azure disklerini kullanarak dinamik birim oluşturma][aks-dynamic-disks]
- [Azure dosyalarını kullanarak dinamik birim oluşturma][aks-dynamic-files]

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS kimliği][aks-concepts-identity]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
