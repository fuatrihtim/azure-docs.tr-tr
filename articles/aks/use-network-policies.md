---
title: Ağ ilkesiyle güvenli Pod trafiği
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service 'te (AKS) Kubernetes ağ ilkelerini kullanarak, düğüm içinde ve dışında akan trafiği güvenli hale getirme hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 17e14859ecdfe11872d5b0526d755d01bc1b034a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577861"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme

Kubernetes 'te modern, mikro hizmet tabanlı uygulamalar çalıştırdığınızda, genellikle hangi bileşenlerin birbirleriyle iletişim kurabildiğini denetlemek istersiniz. En az ayrıcalık ilkesi, trafiğin bir Azure Kubernetes hizmeti (aks) kümesindeki Pod arasında nasıl akatuladığının uygulanması gerekir. Büyük olasılıkla trafiği doğrudan arka uç uygulamalarına engellemek istediğinizi varsayalım. Kubernetes içindeki *ağ ilkesi* özelliği, bir kümedeki düğüm 'ler arasındaki giriş ve çıkış trafiği için kurallar tanımlamanızı sağlar.

Bu makalede ağ ilkesi altyapısının nasıl yükleneceği ve Kubernetes ağ ilkelerinin nasıl oluşturulduğu gösterilir ve aks 'deki Pod 'ler arasındaki trafik akışını denetleyebilirsiniz Ağ ilkesi yalnızca, AKS 'deki Linux tabanlı düğümler ve düğüm 'ler için kullanılmalıdır.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.61 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

> [!TIP]
> Önizleme sırasında ağ ilkesi özelliğini kullandıysanız, [Yeni bir küme oluşturmanızı](#create-an-aks-cluster-and-enable-network-policy)öneririz.
> 
> Önizleme sırasında ağ ilkesi kullanan varolan test kümelerini kullanmaya devam etmek istiyorsanız, kümenizi en son GA sürümü için yeni bir Kubernetes sürümüne yükseltin ve ardından kilitlenen ölçüm sunucusunu ve Kubernetes panosunu onarmak için aşağıdaki YAML bildirimini dağıtın. Bu çözüm yalnızca Calıco Ağ İlkesi altyapısını kullanan kümeler için gereklidir.
>
> En iyi güvenlik uygulaması olarak, AKS kümesine nelerin dağıtıldığını anlamak için [Bu YAML bildiriminin içeriğini gözden geçirin][calico-aks-cleanup] .
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Ağ ilkesine genel bakış

AKS kümesindeki tüm FID 'ler, varsayılan olarak kısıtlama olmadan trafik gönderebilir ve alabilir. Güvenliği artırmak için trafik akışını denetleyen kurallar tanımlayabilirsiniz. Arka uç uygulamalar genellikle yalnızca gerekli ön uç hizmetlerine sunulur, örneğin. Ya da, veritabanı bileşenlerine yalnızca bunlara bağlanan uygulama katmanları erişilebilir.

Ağ Ilkesi, düğüm arasındaki iletişim için erişim ilkelerini tanımlayan bir Kubernetes belirtimidir. Ağ ilkelerini kullanarak, trafiği göndermek ve almak ve bunları bir veya daha fazla etiket seçiciyle eşleşen bir pod koleksiyonuna uygulamak için sıralı bir kural kümesi tanımlarsınız.

Bu ağ ilkesi kuralları YAML bildirimleri olarak tanımlanır. Ağ ilkeleri, ayrıca bir dağıtım veya hizmet oluşturan daha geniş bir bildirimin parçası olarak dahil edilebilir.

### <a name="network-policy-options-in-aks"></a>AKS 'deki ağ ilkesi seçenekleri

Azure, ağ ilkesini uygulamak için iki yol sağlar. Bir AKS kümesi oluştururken bir ağ ilkesi seçeneği belirleyin. İlke seçeneği küme oluşturulduktan sonra değiştirilemez:

* Azure 'un *Azure ağ ilkeleri* olarak adlandırılan kendi uygulamasıdır.
* *Calıco ağ ilkeleri*, [tigera][tigera]tarafından sağlanan açık kaynaklı ağ ve ağ güvenlik çözümüdür.

Her iki uygulama da belirtilen ilkeleri zorlamak için Linux *Iptables* kullanır. İlkeler izin verilen ve izin verilmeyen IP çiftleri kümesine çevrilir. Bu çiftler daha sonra IPTable filtre kuralları olarak programlanır.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure ile Calıco ilkeleri ve özellikleri arasındaki farklılıklar

| Özellik                               | Azure                      | Calıco                      |
|------------------------------------------|----------------------------|-----------------------------|
| Desteklenen platformlar                      | Linux                      | Linux, Windows Server 2019 (Önizleme)  |
| Desteklenen ağ seçenekleri             | Azure CNı                  | Azure CNı (Windows Server 2019 ve Linux) ve Kubernetes kullanan (Linux)  |
| Kubernetes belirtimiyle uyumluluk | Desteklenen tüm ilke türleri |  Desteklenen tüm ilke türleri |
| Ek özellikler                      | Yok                       | Küresel ağ Ilkesi, küresel ağ kümesi ve konak uç noktasından oluşan genişletilmiş ilke modeli. `calicoctl`Bu genişletilmiş özellikleri yönetmek için CLI kullanma hakkında daha fazla bilgi için bkz. [calicoctl User Reference][calicoctl]. |
| Destek                                  | Azure desteği ve mühendislik ekibi tarafından desteklenir | Calıco topluluk desteği. Ücretli ek destek hakkında daha fazla bilgi için bkz. [Proje Calıco destek seçenekleri][calico-support]. |
| Günlüğe Kaydetme                                  | Iptables 'da eklenen/silinen kurallar, */var/log/Azure-NPM.log* altındaki her konakta günlüğe kaydedilir. | Daha fazla bilgi için bkz. [Calıco bileşen günlükleri][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS kümesi oluşturma ve ağ ilkesini etkinleştirme

Ağ ilkelerini sürüyor olarak görmek için, trafik akışını tanımlayan bir ilkeyi oluşturalım ve genişletelim:

* Pod 'a giden tüm trafiği reddedin.
* Pod etiketlerine göre trafiğe izin verin.
* Ad alanına göre trafiğe izin ver.

İlk olarak, ağ ilkesini destekleyen bir AKS kümesi oluşturalım.

> [!IMPORTANT]
>
> Ağ İlkesi özelliği yalnızca küme oluşturulduğunda etkinleştirilebilir. Mevcut bir AKS kümesinde ağ ilkesini etkinleştiremezsiniz.

Azure ağ Ilkesini kullanmak için [Azure CNI eklentisini][azure-cni] kullanmanız ve kendi sanal ağınızı ve alt ağlarını tanımlamanız gerekir. Gerekli alt ağ aralıklarının nasıl planlanacağı hakkında daha ayrıntılı bilgi için bkz. [Gelişmiş ağı yapılandırma][use-advanced-networking]. Calıco ağ Ilkesi, aynı Azure CNı eklentisiyle ya da Kubenet CNı eklentisiyle birlikte kullanılabilir.

Aşağıdaki örnek komut dosyası:

* Bir sanal ağ ve alt ağ oluşturur.
* AKS kümesiyle kullanılmak üzere bir Azure Active Directory (Azure AD) hizmet sorumlusu oluşturur.
* Sanal ağdaki AKS küme hizmeti sorumlusu için *katkıda bulunan* izinleri atar.
* Tanımlı sanal ağda bir AKS kümesi oluşturur ve ağ ilkesini sunar.
    * _Azure ağ_ ilkesi seçeneği kullanılır. Bunun yerine Calıco 'yı ağ ilkesi seçeneği olarak kullanmak için `--network-policy calico` parametresini kullanın. Note: Calıco, veya ile birlikte `--network-plugin azure` kullanılabilir `--network-plugin kubenet` .

Hizmet sorumlusu kullanmak yerine, izinler için yönetilen bir kimlik kullanabileceğinizi unutmayın. Daha fazla bilgi için bkz. [yönetilen kimlikleri kullanma](use-managed-identity.md).

Kendi güvenli *sp_password* sağlayın. *RESOURCE_GROUP_NAME* ve *CLUSTER_NAME* değişkenlerini değiştirebilirsiniz:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Azure ağ ilkeleri için AKS kümesi oluşturma

AKS kümesini oluşturun ve ağ eklentisi ve ağ ilkesi için sanal ağ, hizmet sorumlusu bilgilerini ve *Azure* 'u belirtin.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Kümenin oluşturulması birkaç dakika sürer. Küme hazır olduğunda, `kubectl` [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Calıco ağ ilkeleri için AKS kümesi oluşturma

AKS kümesini oluşturun ve sanal ağı, hizmet sorumlusu bilgilerini, ağ eklentisi için *Azure* 'u ve ağ ilkesi için *calıco* 'yı belirtin. Ağ ilkesi olarak *calıco* kullanılması, hem Linux hem de Windows düğüm havuzlarında calıco ağı sunar.

Kümenize Windows düğüm havuzları eklemeyi planlıyorsanız, `windows-admin-username` `windows-admin-password` [Windows Server parola gereksinimlerini][windows-server-password]karşılayan ile ve parametrelerini dahil edin. Calıco 'yi Windows düğüm havuzlarıyla birlikte kullanmak için, ' yi de kaydetmeniz gerekir `Microsoft.ContainerService/EnableAKSWindowsCalico` .

`EnableAKSWindowsCalico`Aşağıdaki örnekte gösterildiği gibi [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu denetleyebilirsiniz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Şu anda, Calıco ağ ilkelerinin Windows düğümleri ile kullanılması, Calıco 3.17.2 ile Kubernetes sürüm 1,20 veya sonraki bir sürümünü kullanan yeni kümelerde kullanılabilir ve Azure CNı ağ kullanımı gerektirir. Calıco 'lar ile AKS kümelerindeki Windows düğümleri, varsayılan olarak etkinleştirilen [doğrudan sunucu dönüşü (DSR)][dsr] sahiptir.
>
> Calıco 'un önceki sürümleriyle Kubernetes 1,20 çalıştıran yalnızca Linux düğüm havuzlarının bulunduğu kümeler için, Calıco sürümü otomatik olarak 3.17.2 sürümüne yükseltilir.

Windows düğümleri ile calıco ağ ilkeleri Şu anda önizlemededir.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Kümenizdeki Windows Server kapsayıcılarınız için yönetici kimlik bilgileri olarak kullanılacak bir Kullanıcı adı oluşturun. Aşağıdaki komutlar bir Kullanıcı adı ister ve daha sonraki bir komutta kullanılmak üzere WINDOWS_USERNAME ayarlamanız gerekir (Bu makaledeki komutların BASH kabuğu 'na girildiğini unutmayın).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Kümenin oluşturulması birkaç dakika sürer. Varsayılan olarak, kümeniz yalnızca bir Linux düğüm havuzuyla oluşturulur. Windows düğüm havuzlarını kullanmak istiyorsanız, bir tane ekleyebilirsiniz. Örnek:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Küme hazır olduğunda, `kubectl` [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak Kubernetes kümenize bağlanacak şekilde yapılandırın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Pod 'a gelen tüm trafiği reddetme

Belirli ağ trafiğine izin vermek için kurallar tanımladıktan önce, ilk olarak tüm trafiği reddetmek için bir ağ ilkesi oluşturun. Bu ilke, yalnızca istenen trafik için bir izin oluşturmaya başlamak üzere başlangıç noktası sağlar. Ayrıca, ağ ilkesi uygulandığında trafiğin bırakıldığının açıkça görebilirsiniz.

Örnek uygulama ortamı ve trafik kuralları için öncelikle örnek pods 'yi çalıştırmak üzere *geliştirme* adlı bir ad alanı oluşturalım:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGıNX çalıştıran örnek bir arka uç Pod oluşturun. Bu arka uç Pod, örnek arka uç Web tabanlı bir uygulamanın benzetimini yapmak için kullanılabilir. Bu Pod 'ı *geliştirme* ad alanında oluşturun ve *80* numaralı bağlantı noktasını Web trafiğini sunacak şekilde açın. Bir sonraki bölümde bir ağ ilkesiyle hedeflemenize olanak sağlamak için pod *öğesini App = WebApp, role = arka uç* ile etiketleyin:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Farklı bir pod oluşturun ve varsayılan NGıNX Web sayfasına başarıyla ulaşabilmeyi test etmek için bir terminal oturumu ekleyin:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Kabuk isteminde, `wget` varsayılan NGINX web sayfasına erişebildiğinizden emin olmak için kullanın:

```console
wget -qO- http://backend
```

Aşağıdaki örnek çıktıda varsayılan NGıNX Web sayfasının döndürdüğü gösterilmektedir:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ekli Terminal oturumundan çıkın. Test Pod 'u otomatik olarak silinir.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Ağ ilkesi oluşturma ve uygulama

Artık, örnek arka uç pod üzerinde temel NGıNX Web sayfasını kullanabilirsiniz, tüm trafiği reddetmek için bir ağ ilkesi oluşturun. Adlı bir dosya oluşturun `backend-policy.yaml` ve aşağıdaki YAML bildirimini yapıştırın. Bu bildirim, ilkeyi örnek NGINX Pod 'unuz gibi *App: WebApp, role: arka uç* etiketi olan Pod 'ye eklemek için bir *Pod Seçicisi* kullanır. Giriş altında hiçbir kural tanımlanmadığı için *Pod 'a giden* tüm trafik reddedilir:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

[https://shell.azure.com](https://shell.azure.com)Tarayıcınızda Azure Cloud Shell açmak için bölümüne gidin.

[Kubectl Apply][kubectl-apply] komutunu kullanarak ağ ilkesini uygulayın ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Ağ ilkesini test etme

Arka uç pod üzerinde NGıNX Web sayfasını yeniden kullanıp kullanabileceizin görelim. Başka bir test Pod oluşturun ve bir terminal oturumu ekleyin:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Kabuk isteminde `wget` varsayılan NGINX web sayfasına erişip erişemayabilmeniz için öğesini kullanın. Bu kez, zaman aşımı değerini *2* saniyeye ayarlayın. Ağ ilkesi artık tüm gelen trafiği engeller, bu nedenle aşağıdaki örnekte gösterildiği gibi sayfa yüklenemez:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ekli Terminal oturumundan çıkın. Test Pod 'u otomatik olarak silinir.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Pod etiketine dayalı gelen trafiğe izin ver

Önceki bölümde, bir arka uç NGıNX Pod zamanlandı ve tüm trafiği reddedecek bir ağ ilkesi oluşturuldu. Ön uç Pod oluşturalım ve ağ ilkesini ön uç kapalarından trafiğe izin verecek şekilde güncellim.

Ağ ilkesini, Etiketler *Uygulama: WebApp, rol: ön uç* ve herhangi bir ad alanında yer alan etiketlere giden trafiğe izin verecek şekilde güncelleştirin. Önceki *arka uç ilkesi. YAML* dosyasını düzenleyin ve bildirim aşağıdaki örnekte olduğu gibi, *matchlabels* giriş kurallarını ekleyin:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Bu ağ ilkesi, giriş kuralı için bir *namespaceselector* ve bir *Pod seçici* öğesi kullanır. YAML sözdizimi, giriş kurallarının eklenebilir olması için önemlidir. Bu örnekte, giriş kuralının uygulanması için her iki öğenin de eşleşmesi gerekir. *1,12* ' dan önceki Kubernetes sürümleri, bu öğeleri doğru şekilde yorumlayamayabilir ve ağ trafiğini beklendiği gibi kısıtlayabilir. Bu davranış hakkında daha fazla bilgi için bkz. [seçicilerin ve seçicilerin davranışı][policy-rules].

[Kubectl Apply][kubectl-apply] komutunu kullanarak güncelleştirilmiş ağ ilkesini uygulayın ve YAML bildirimin adını belirtin:

```console
kubectl apply -f backend-policy.yaml
```

*App = WebApp, role = ön uç* ve bir terminal oturumu iliştirme olarak etiketlenmiş bir pod zamanlayın:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Kabuk isteminde, `wget` varsayılan NGINX web sayfasına erişip erişemayabilmeniz için kullanın:

```console
wget -qO- http://backend
```

Giriş kuralı, Etiketler *Uygulama: WebApp, rol: ön uç* olan IP 'leri olan trafiğe izin verdiğinden, ön uç Pod 'daki trafiğe izin verilir. Aşağıdaki örnek çıktıda döndürülen varsayılan NGıNX Web sayfası gösterilmektedir:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ekli Terminal oturumundan çıkın. Pod otomatik olarak silinir.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Eşleşen bir etiket olmadan Pod 'u test etme

Ağ ilkesi, Pod etiketli *Uygulama: WebApp, rol: ön uç* için trafiğe izin verir, ancak diğer tüm trafiği reddetmelidir. Bu etiketlerin olmadığı başka bir pod 'ın arka uç NGıNX Pod 'a erişip erişemeyeceğini görmek için test edelim. Başka bir test Pod oluşturun ve bir terminal oturumu ekleyin:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Kabuk isteminde `wget` varsayılan NGINX web sayfasına erişip erişemayabilmeniz için öğesini kullanın. Ağ ilkesi gelen trafiği engeller, bu nedenle aşağıdaki örnekte gösterildiği gibi sayfa yüklenemez:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ekli Terminal oturumundan çıkın. Test Pod 'u otomatik olarak silinir.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Yalnızca tanımlı bir ad alanı içinden trafiğe izin ver

Önceki örneklerde, tüm trafiği reddeden bir ağ ilkesi oluşturdunuz ve ardından belirli bir etikete sahip olmayan trafiğe izin vermek için ilkeyi güncelleştirmiş olursunuz. Diğer bir yaygın gereksinim, trafiği yalnızca belirli bir ad alanı dahilinde sınırlandırmaya yönelik bir seçenektir. Önceki örneklerde bir *geliştirme* ad alanındaki trafik varsa, başka bir ad alanından gelen trafiğin (örneğin, *Üretim*) yük uzayına ulaşmasını engelleyen bir ağ ilkesi oluşturun.

İlk olarak, bir üretim ad alanının benzetimini yapmak için yeni bir ad alanı oluşturun:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*App = WebApp, role = ön uç* olarak etiketlenmiş *Üretim* ad alanında bir test Pod 'u zamanlayın. Terminal oturumu ekleyin:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Kabuk isteminde, `wget` varsayılan NGINX web sayfasına erişebildiğinizden emin olmak için kullanın:

```console
wget -qO- http://backend.development
```

Pod 'un etiketleri, ağ ilkesinde Şu anda izin verilen şekilde eşleştiğinden, trafiğe izin verilir. Ağ İlkesi ad alanlarına ve yalnızca Pod etiketlerine bakar. Aşağıdaki örnek çıktıda döndürülen varsayılan NGıNX Web sayfası gösterilmektedir:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ekli Terminal oturumundan çıkın. Test Pod 'u otomatik olarak silinir.

```console
exit
```

### <a name="update-the-network-policy"></a>Ağ ilkesini güncelleştirme

Giriş kuralı *Namespaceselector* bölümünü yalnızca *geliştirme* ad alanı içinden gelen trafiğe izin verecek şekilde güncelleştirelim. Aşağıdaki örnekte gösterildiği gibi, *arka uç ilkesi. YAML* bildirim dosyasını düzenleyin:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Daha karmaşık örneklerde, bir *namespaceselector* ve sonra bir *Pod Seçicisi* gibi birden çok giriş kuralı tanımlayabilirsiniz.

[Kubectl Apply][kubectl-apply] komutunu kullanarak güncelleştirilmiş ağ ilkesini uygulayın ve YAML bildirimin adını belirtin:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Güncelleştirilmiş ağ ilkesini test etme

*Üretim* ad alanında başka bir pod zamanlayın ve bir terminal oturumu ekleyin:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Kabuk isteminde, `wget` ağ ilkesinin trafiği reddetmeye yönelik olduğunu görmek için kullanın:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Test Pod 'dan çıkma:

```console
exit
```

*Üretim* ad alanından gelen trafik reddedildiğinde, *geliştirme* ad alanında bir test Pod 'u yeniden zamanlayın ve bir terminal oturumu ekleyin:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Kabuk isteminde `wget` ağ ilkesinin trafiğe izin verdiğini görmek için kullanın:

```console
wget -qO- http://backend
```

Pod, ağ ilkesinde izin verilen şekilde eşleşen ad alanında zamanlandığı için trafiğe izin verilir. Aşağıdaki örnek çıktıda döndürülen varsayılan NGıNX Web sayfası gösterilmektedir:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ekli Terminal oturumundan çıkın. Test Pod 'u otomatik olarak silinir.

```console
exit
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede, iki ad alanı oluşturduk ve bir ağ ilkesi uyguladık. Bu kaynakları temizlemek için [kubectl Delete][kubectl-delete] komutunu kullanın ve kaynak adlarını belirtin:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ kaynakları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) içindeki uygulamalar Için ağ kavramları][concepts-network].

İlkeler hakkında daha fazla bilgi edinmek için bkz. [Kubernetes ağ ilkeleri][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip