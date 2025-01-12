---
title: include dosyası
description: include dosyası
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244788"
---
## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Kapsayıcı Kayıt defterinize erişimi olan bir hizmet sorumlusu oluşturmak için, [Azure Cloud Shell](../articles/cloud-shell/overview.md) veya [Azure CLI](/cli/azure/install-azure-cli)'nin yerel yüklemesinde aşağıdaki betiği çalıştırın. Komut dosyası bash kabuğu için biçimlendirilir.

Betiği çalıştırmadan önce, `ACR_NAME` değişkeni kapsayıcı kayıt defterinizin adıyla güncelleştirin. `SERVICE_PRINCIPAL_NAME`Değerin Azure Active Directory kiracınız dahilinde benzersiz olması gerekir. " `'http://acr-service-principal' already exists.` " Hatası alırsanız hizmet sorumlusu için farklı bir ad belirtin.

`--role`Farklı izinler vermek istiyorsanız, isteğe bağlı olarak [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] komutundaki değeri değiştirebilirsiniz. Rollerin tüm listesi için bkz. [ACR rolleri ve izinleri](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Betiği çalıştırdıktan sonra, hizmet sorumlusunun **kimliğini** ve **parolasını** göz önünde atın. Kimlik bilgilerine sahip olduktan sonra, uygulama ve hizmetlerinizi hizmet sorumlusu olarak kapsayıcı kayıt defterinizde kimlik doğrulaması yapacak şekilde yapılandırabilirsiniz.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Mevcut bir hizmet sorumlusunu kullan

Mevcut bir hizmet sorumlusuna kayıt defteri erişimi vermek için hizmet sorumlusuna yeni bir rol atamanız gerekir. Yeni bir hizmet sorumlusu oluştururken, diğerleri arasında çekme, gönderme ve çekme ve sahip erişimine izin verebilirsiniz.

Aşağıdaki betik, değişkende belirttiğiniz bir hizmet sorumlusuna *çekme* izinleri vermek için [az role atama Create][az-role-assignment-create] komutunu kullanır `SERVICE_PRINCIPAL_ID` . `--role`Farklı bir erişim düzeyi vermek istiyorsanız değeri ayarlayın.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
