---
title: Microsoft bağlı önbelleği, giden kimliği doğrulanmamış ara sunucu içeren iki düzey iç içe Azure IoT Edge Ağ | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft bağlı önbellek iki düzey iç içe geçmiş Azure IoT Edge ağ geçidi, giden kimliği doğrulanmamış proxy öğreticisi
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568826"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft bağlı önbellek önizleme dağıtım senaryosu örneği: giden kimliği doğrulanmamış proxy ile Iki düzey iç içe Azure IoT Edge Ağ Geçidi

Aşağıdaki diyagramda, bu senaryoda bir Azure IoT Edge ağ geçidi ve bir aşağı akış Azure IoT Edge cihazı bulunur, bir Azure IoT Edge ağ geçidi, başka bir Azure IoT Edge ağ geçidine ve BT DMZ bir ara sunucuya eklenir. Aşağıda, Azure IoT Edge ağ geçitlerine dağıtılan MCC modüllerinin her ikisi için Azure portal UX içinde ayarlanacak Microsoft bağlı önbellek ortamı değişkenlerine bir örnek verilmiştir. Gösterilen örnekte, Azure IoT Edge ağ geçitlerinin iki düzeyi için yapılandırma gösterilmektedir, ancak Microsoft bağlı önbelleğinin destekleyeceği yukarı akış ana bilgisayarlarının derinliğine yönelik bir sınır yoktur. Yukarıdaki örneklerde MCC Container oluşturma seçeneklerinde fark yoktur.

Azure IoT Edge ağ geçitlerinin katmanlı dağıtımlarını yapılandırma hakkında daha fazla bilgi için bkz. Şirket [yönündeki bağlantı IoT Edge cihazları-Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Ayrıca Azure IoT Edge, Microsoft bağlı önbelleği ve özel modüller dağıtıldığında tüm modüllerin aynı kapsayıcı kayıt defterinde bulunması gerektiğini unutmayın.

Aşağıdaki diyagramda, bir Azure IoT Edge ağ geçidinin CDN kaynaklarına doğrudan erişimi olarak üst öğe olarak, Raspberry Pi gibi bir Azure IoT yaprak cihazının ana görevi gören başka bir Azure IoT Edge ağ geçidi olarak davrandığı senaryo açıklanmaktadır. Yalnızca Azure IoT Edge ağ geçidi üst öğesi CDN kaynaklarına internet bağlantısına sahiptir ve hem Azure IoT Edge alt hem de Azure IoT cihazı Internet yalıtılmış ' dir. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft bağlı önbellek Iç Içe" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Üst ağ geçidi yapılandırması

1. Microsoft bağlı önbellek modülünü Azure IoT Hub Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin.
2. Dağıtım için ortam değişkenlerini ekleyin. Aşağıda ortam değişkenlerinin bir örneği verilmiştir.

    **Ortam değişkenleri**

    | Name                 | Değer                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Yukarıdaki ortam değişkeni açıklamasına bakın. |
    | CUSTOMER_ID                   | Yukarıdaki ortam değişkeni açıklamasına bakın. |
    | CUSTOMER_KEY                  | Yukarıdaki ortam değişkeni açıklamasına bakın. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Proxy sunucusu IP veya FQDN                     |

3. Dağıtım için kapsayıcı oluşturma seçeneklerini ekleyin. Yukarıdaki örnekteki MCC kapsayıcısı oluşturma seçeneklerinde fark yoktur. Kapsayıcı oluşturma seçeneklerine bir örnek aşağıda verilmiştir.

### <a name="container-create-options"></a>Kapsayıcı oluşturma seçenekleri

```markdown
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

## <a name="child-gateway-configuration"></a>Alt ağ geçidi yapılandırması

>[!Note]
>Kendi özel kayıt defterinizde yapılandırmanızda kullanılan kapsayıcıları çoğaltdıysanız, modül dağıtımınızdaki config. TOML ayarları ve çalışma zamanı ayarları üzerinde bir değişiklik olması gerekir. Daha fazla bilgi için bkz. [öğretici-IoT Edge cihaz hiyerarşisi oluşturma-](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device) daha fazla ayrıntı için Azure IoT Edge.

1. Edge aracısının görüntü yolunu aşağıdaki örnekte gösterildiği gibi değiştirin:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Azure IoT Edge dağıtımında Edge hub ve Edge Aracısı çalışma zamanı ayarlarını şu örnekte gösterildiği gibi değiştirin:
    
    * Edge hub 'ında görüntü alanına şunu girin ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Edge Aracısı altında, görüntü alanına şunu girin ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Microsoft bağlı önbellek modülünü Azure IoT Hub Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin.

   * Modülünüzün adını seçin: ```ConnectedCache```
   * Görüntü URI 'sini değiştirin: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Ana dağıtımda kullanılan aynı ortam değişkenlerini ve kapsayıcı oluşturma seçeneklerini ekleyin.

Microsoft bağlı önbelleğinin düzgün şekilde çalıştığını doğrulamak için, modülü barındıran IoT Edge cihazın terminalinde aşağıdaki komutu yürütün veya ağdaki herhangi bir cihazı çalıştırın.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```