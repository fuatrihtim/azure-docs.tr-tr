---
title: ACR görevinden çapraz kayıt defteri kimlik doğrulaması
description: Azure kaynakları için yönetilen bir kimlik kullanarak başka bir özel Azure Container Registry 'ye erişmek üzere bir Azure Container Registry görevi (ACR görevi) yapılandırma
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 789d2c141f8b7c3f2eb8daa31d99090e3d028a43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915837"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure tarafından yönetilen kimlik kullanan bir ACR görevinde çapraz kayıt defteri kimlik doğrulaması 

Bir [ACR görevinde](container-registry-tasks-overview.md), [Azure kaynakları için yönetilen bir kimliği etkinleştirebilirsiniz](container-registry-tasks-authentication-managed-identity.md). Görev, kimlik bilgilerini sağlamaya veya yönetmeye gerek kalmadan diğer Azure kaynaklarına erişmek için kimliği kullanabilir. 

Bu makalede, görevi çalıştırmak için kullanılan sunucudan farklı bir kayıt defterinden görüntü çekmek üzere bir görevde yönetilen bir kimliği nasıl etkinleştireceğinizi öğreneceksiniz.

Azure kaynaklarını oluşturmak için bu makale, Azure CLı sürüm 2.0.68 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

## <a name="scenario-overview"></a>Senaryoya genel bakış

Örnek görev, bir uygulama görüntüsü oluşturmak ve göndermek için başka bir Azure Container Registry 'den bir temel görüntü çeker. Temel görüntüyü çekmek için, görevi yönetilen bir kimlikle yapılandırıp ilgili izinleri buna atayabilirsiniz. 

Bu örnek, Kullanıcı tarafından atanan veya sistem tarafından atanan yönetilen kimlik kullanarak adımları gösterir. Kimlik seçiminiz, kuruluşunuzun ihtiyaçlarına bağlıdır.

Gerçek dünyada bir senaryoda kuruluş, tüm geliştirme ekipleri tarafından uygulamalarını oluşturmak için kullanılan bir dizi temel görüntü tutabilir. Bu temel görüntüler, her geliştirme ekibinin yalnızca çekme haklarına sahip olduğu bir kurumsal kayıt defterinde saklanır. 

## <a name="prerequisites"></a>Önkoşullar

Bu makalede iki Azure Container Registry gerekir:

* ACR görevlerini oluşturmak ve yürütmek için ilk kayıt defterini kullanın. Bu makalede, bu kayıt defteri *myregistry* olarak adlandırılmıştır. 
* İkinci kayıt defteri, görev için bir görüntü oluşturmak için kullanılan bir temel görüntü barındırır. Bu makalede ikinci kayıt defteri *mybaseregyıı* olarak adlandırılmıştır. 

Sonraki adımlarda kendi kayıt defteri adlarınızla değiştirin.

Gerekli Azure Container Registry 'ye zaten sahip değilseniz bkz. [hızlı başlangıç: Azure CLI kullanarak özel kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md). Görüntüleri kayıt defterine henüz göndermeniz gerekmez.

## <a name="prepare-base-registry"></a>Temel kayıt defterini hazırla

Tanıtım amacıyla, tek seferlik bir işlem olarak, Docker Hub 'ından temel kayıt defterinize ortak bir Node.js görüntüsünü içeri aktarmak için [az ACR Import] [az-ACR-import] öğesini çalıştırın. Uygulamada, kuruluştaki başka bir takım veya işlem, temel kayıt defterindeki görüntüleri koruyabilir.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>YAML dosyasında görev adımlarını tanımlama

Bu örnek [çoklu adım görevi](container-registry-tasks-multi-step.md) için adımlar bir [YAML dosyasında](container-registry-tasks-reference-yaml.md)tanımlanmıştır. Yerel çalışma dizininizde adlı bir dosya oluşturun `helloworldtask.yaml` ve aşağıdaki içeriği yapıştırın. `REGISTRY_NAME`Derleme adımındaki değerini, temel kayıt defterinizin sunucu adıyla güncelleştirin.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Derleme adımı, `Dockerfile-app` bir görüntü oluşturmak Için [Azure-Samples/ACR-Build-HelloWorld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) depoındaki dosyayı kullanır. Temel `--build-arg` görüntüyü çekmek için temel kayıt defterine başvurur. Başarılı bir şekilde oluşturulduğunda, görüntü, görevi çalıştırmak için kullanılan kayıt defterine gönderilir.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Seçenek 1: Kullanıcı tarafından atanan kimlikle görev oluşturma

Bu bölümdeki adımlar bir görev oluşturur ve Kullanıcı tarafından atanan bir kimliği etkinleştirir. Bunun yerine sistem tarafından atanan bir kimliği etkinleştirmek istiyorsanız, bkz. [seçenek 2: sistem tarafından atanan kimlik ile görev oluşturma](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Görev Oluştur

Aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek bir görev *Merhaba Dünya görevi* oluşturun. Görev, kaynak kodu bağlamı olmadan çalışır ve komut `helloworldtask.yaml` çalışma dizinindeki dosyasına başvurur. `--assign-identity`Parametresi, Kullanıcı tarafından atanan kimliğin kaynak kimliğini geçirir. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Temel kayıt defterine kimlik çekme izinleri verme

Bu bölümde, *mybaseregyıı* temel kayıt defterinden çekme için yönetilen kimlik izinlerini verin.

Temel kayıt defterinin kaynak KIMLIĞINI almak ve bir değişkende depolamak için [az ACR Show][az-acr-show] komutunu kullanın:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Rolün kimliğini temel kayıt defterine atamak için [az role atama Create][az-role-assignment-create] komutunu kullanın `acrpull` . Bu rol yalnızca kayıt defterinden görüntüleri çekmek için izinlere sahiptir.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

[Göreve hedef kayıt defteri kimlik bilgilerini eklemeye](#add-target-registry-credentials-to-task)devam edin.

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. seçenek: sistem tarafından atanan kimlikle görev oluşturma

Bu bölümdeki adımlar bir görev oluşturur ve sistem tarafından atanan bir kimliği etkinleştirir. Bunun yerine Kullanıcı tarafından atanan bir kimliği etkinleştirmek istiyorsanız, bkz. [1. seçenek: Kullanıcı tarafından atanan kimlik ile görev oluşturma](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Görev Oluştur

Aşağıdaki [az ACR Task Create][az-acr-task-create] komutunu yürüterek bir görev *Merhaba Dünya görevi* oluşturun. Görev, kaynak kodu bağlamı olmadan çalışır ve komut `helloworldtask.yaml` çalışma dizinindeki dosyasına başvurur. `--assign-identity`Değer içermeyen parametre, görevde sistem tarafından atanan kimliği etkinleştirmesine izin vermez. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Temel kayıt defterine kimlik çekme izinleri verme

Bu bölümde, *mybaseregyıı* temel kayıt defterinden çekme için yönetilen kimlik izinlerini verin.

Temel kayıt defterinin kaynak KIMLIĞINI almak ve bir değişkende depolamak için [az ACR Show][az-acr-show] komutunu kullanın:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Rolün kimliğini temel kayıt defterine atamak için [az role atama Create][az-role-assignment-create] komutunu kullanın `acrpull` . Bu rol yalnızca kayıt defterinden görüntüleri çekmek için izinlere sahiptir.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Göreve hedef kayıt defteri kimlik bilgilerini ekle

Şimdi, görevin kimlik bilgilerini kullanarak temel kayıt defteri ile kimlik doğrulaması yapmasını sağlamak için [az ACR Task Credential Add][az-acr-task-credential-add] komutunu kullanın. Görevde etkinleştirdiğiniz yönetilen kimliğin türüne karşılık gelen komutu çalıştırın. Kullanıcı tarafından atanan bir kimliği etkinleştirdiyseniz, `--use-identity` kimliğin ISTEMCI kimliğiyle geçiş yapın. Sistem tarafından atanan bir kimliği etkinleştirdiyseniz, geçirin `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Görevi el ile çalıştırın

Yönetilen bir kimliği etkinleştirdiğiniz görevin başarıyla çalıştığını doğrulamak için, görevi [az ACR Task Run][az-acr-task-run] komutuyla el ile tetikleyin. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Görev başarıyla çalışırsa, çıkış şuna benzerdir:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Oluşturulan görüntünün ve *myregistry*'e başarıyla gönderildiğinden emin olmak için [az ACR Repository Show-Tags][az-acr-repository-show-tags] komutunu çalıştırın:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Örnek çıktı:

```console
cf10
```

## <a name="next-steps"></a>Sonraki adımlar

* Bir [ACR görevinde yönetilen kimliği etkinleştirme](container-registry-tasks-authentication-managed-identity.md)hakkında daha fazla bilgi edinin.
* [ACR görevlerine YAML başvurusunu](container-registry-tasks-reference-yaml.md) gör

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
