---
title: GitHub dağıtımı ile bir işlev uygulaması oluşturma-Azure CLı
description: Azure İşlevleri’ni kullanarak bir işlev uygulaması oluşturun ve bir GitHub deposundan işlev kodu dağıtın.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 219e993ad7132c90de6db680facc9b8f815947cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934399"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Azure’da GitHub’dan dağıtılmış bir işlev uygulaması oluşturma

Bu Azure Işlevleri örnek betiği, [Tüketim planını](../consumption-plan.md)kullanarak ilgili kaynaklarıyla birlikte bir işlev uygulaması oluşturur. Betik ayrıca işlev kodunuzu bir GitHub deposundan sürekli dağıtım için yapılandırır. 

Bu örnekte aşağıdakilere ihtiyacınız olacak:

* Yönetme izinlerine sahip olduğunuz işlevler kodu ile bir GitHub deposu.
* GitHub hesabınız için bir [Kişisel Erişim Belirteci (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik

Bu örnek, bir Azure İşlev uygulaması oluşturur ve işlev kodunu GitHub'dan dağıtır.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Tablodaki her komut, komuta özgü belgelere yönlendirir. Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | İşlev uygulaması için gereken depolama hesabını oluşturur. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Sunucusuz [Tüketim planında](../consumption-plan.md) bir işlev uygulaması oluşturur ve bunu bir git veya Mercurial deposu ile ilişkilendirir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure İşlevleri CLI betiği örnekleri, [Azure İşlevleri belgelerinde](../functions-cli-samples.md) bulunabilir.
