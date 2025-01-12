---
title: Azure 'da OKD dağıtma
description: Azure 'da OKD 'yi dağıtın.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667213"
---
# <a name="deploy-okd-in-azure"></a>Azure 'da OKD dağıtma

Azure 'da OKD (eski adıyla OpenShift Origin) dağıtmak için iki farklı yol kullanabilirsiniz:

- Tüm gerekli Azure altyapı bileşenlerini el ile dağıtabilir ve ardından [OKD belgelerini](https://docs.okd.io)takip edebilirsiniz.
- Ayrıca, OKD kümesinin dağıtımını kolaylaştıran mevcut bir [Kaynak Yöneticisi şablonunu](https://github.com/Microsoft/openshift-origin) da kullanabilirsiniz.

## <a name="deploy-using-the-okd-template"></a>OKD şablonunu kullanarak dağıtma

Kaynak Yöneticisi şablonu kullanarak dağıtmak için, giriş parametrelerini sağlamak üzere bir parametreler dosyası kullanırsınız. Dağıtımı daha fazla özelleştirmek için GitHub deposunun çatalını yapın ve uygun öğeleri değiştirin.

Bazı yaygın özelleştirme seçenekleri şunlardır, ancak bunlarla sınırlı değildir:

- Savunma VM boyutu (azuredeploy.jsdeğişken)
- Adlandırma kuralları (azuredeploy.jsdeğişkenler)
- OpenShift küme özellikleri, Hosts dosyası aracılığıyla değiştirildi (deployOpenShift.sh)

[OKD şablonunda](https://github.com/Microsoft/openshift-origin) farklı OKD sürümleri için kullanılabilen birden çok dal var.  Gereksinimlerinize bağlı olarak, doğrudan depodan dağıtım yapabilir veya depoyu çatalla dağıtmadan önce özel değişiklikler yapabilirsiniz.

`appId`Daha önce parametresi için oluşturduğunuz hizmet sorumlusunun değerini kullanın `aadClientId` .

Aşağıda, tüm gerekli girişlerle birlikte azuredeploy.parameters.jsadlı bir parametre dosyası örneği verilmiştir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Parametreleri kendi özel bilgileriniz ile değiştirin.

Farklı yayınlar farklı parametrelere sahip olabilir, bu nedenle lütfen kullandığınız dal için gerekli parametreleri doğrulayın.

### <a name="deploy-using-azure-cli"></a>Azure CLI’yi kullanarak dağıtma


> [!NOTE] 
> Aşağıdaki komut, Azure CLı 2.0.8 veya üstünü gerektirir. CLı sürümünü `az --version` komutuyla doğrulayabilirsiniz. CLı sürümünü güncelleştirmek için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

Aşağıdaki örnek, OKD kümesini ve tüm ilgili kaynakları myOpenShiftCluster dağıtım adı ile openkaydırıcı Trg adlı bir kaynak grubuna dağıtır. Şablonuna, üzerinde azuredeploy.parameters.jsadlı yerel bir parametre dosyası kullanılırken doğrudan GitHub deposundan başvurulur.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Dağıtım, dağıtılan toplam düğüm sayısına bağlı olarak en az 30 dakika sürer. OpenShift konsolunun URL 'SI ve OpenShift ana öğesinin DNS adı, dağıtım tamamlandığında terminalde yazdırılır. Alternatif olarak, Azure portal dağıtım konusunun çıktılar bölümünü görüntüleyebilirsiniz.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Dağıtımın tamamlanmasını bekleyen komut satırını bağlamak istemiyorsanız, `--no-wait` Grup dağıtımına yönelik seçeneklerden birini ekleyin. Dağıtımdan alınan çıkış, kaynak grubunun dağıtım bölümündeki Azure portal alabilir.

## <a name="connect-to-the-okd-cluster"></a>OKD kümesine bağlanma

Dağıtım tamamlandığında, ' yi kullanarak, tarayıcınızla OpenShift konsoluna bağlanın `OpenShift Console Url` . Alternatif olarak, OKD ana için SSH kullanabilirsiniz. Dağıtımdan alınan çıktıyı kullanan bir örnek aşağıda verilmiştir:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu, OpenShift kümesini ve artık gerekli olmadığında tüm ilgili kaynakları kaldırmak için [az Group Delete](/cli/azure/group) komutunu kullanın.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Sonraki adımlar

- [Dağıtım sonrası görevler](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift dağıtımında sorun giderme](./openshift-container-platform-3x-troubleshooting.md)
- [OKD 'yi kullanmaya başlama](https://docs.okd.io)
