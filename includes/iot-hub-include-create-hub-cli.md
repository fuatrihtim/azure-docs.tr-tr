---
title: include dosyası
description: include dosyası
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 10bd2c4902157b9e01b1cb0ff10b3ebdf448568c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244840"
---
Aşağıdaki bölümlerde bir Terminal ayarlarsınız ve bir IoT Hub 'ı oluşturmak için Azure CLı 'yi kullanırsınız. Azure CLı komutlarını çalıştıran bir Terminal yapılandırmak için tarayıcı tabanlı Azure Cloud Shell kullanabilir ya da yerel bir Terminal kullanabilirsiniz.
* Cloud Shell kullanmak için sonraki bölüme gidin: [Cloud Shell başlatın](#launch-the-cloud-shell). 
* Yerel bir Terminal kullanmak için, bir sonraki bölümü atlayın ve [yerel bir Terminal aç ' a](#open-a-local-terminal)gidin.

## <a name="launch-the-cloud-shell"></a>Cloud Shell başlatın
Bu bölümde, Cloud Shell bir oturum oluşturup Terminal ortamınızı yapılandırırsınız.

https://portal.azure.com adresinden Azure portalında oturum açın.  

Cloud Shell başlatmak için:

1. Azure portal sağ üstteki menü çubuğunda **Cloud Shell** düğmesini seçin. 

    ![Azure portal Cloud Shell düğmesi](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Cloud Shell ilk kez kullandıysanız, Cloud Shell kullanmak için gerekli olan depolama alanı oluşturmanızı ister.  Depolama hesabı ve Microsoft Azure dosyaları paylaşma oluşturmak için bir abonelik seçin. 

2. **Ortam Seç** açılan menüsünde tercıh ettiğiniz CLI ortamınızı seçin. Bu hızlı başlangıç, **Bash** ortamını kullanır. Aşağıdaki CLı komutlarının hepsi PowerShell ortamında da çalışır. 

    ![CLı ortamı seçin](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Sonraki bölümü atlayın ve [Azure IoT uzantısını yüklemeye](#install-the-azure-iot-extension)gidin. 

## <a name="open-a-local-terminal"></a>Yerel bir Terminal açın
Cloud Shell yerine yerel bir Terminal kullanmayı seçerseniz, bu bölümü doldurun.  

1. Yerel bir Terminal açın.
1. [Az Login](/cli/azure/reference-index#az_login) komutunu çalıştırın:

   ```azurecli
   az login
   ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın https://aka.ms/devicelogin ve terminalinizde görünen yetkilendirme kodunu girin.

    Web tarayıcısı yoksa veya Web tarayıcısı açılamazsa, ile cihaz kod akışını kullanın `az login --use-device-code` .

1. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

    Farklı kimlik doğrulama yöntemleri hakkında daha fazla bilgi için bkz. [Azure CLI'de oturum açma]( /cli/azure/authenticate-azure-cli ).

1. Sonraki bölüme gidin: [Azure IoT uzantısını yükler](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Azure IoT uzantısını yükler
Bu bölümde, Azure CLı için Microsoft Azure IoT uzantısını CLı kabuğunuzun altına yüklersiniz. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

> [!IMPORTANT]
> Bu hızlı başlangıçtaki geri kalan terminal komutları Cloud Shell veya yerel terminalde aynı şekilde çalışır. Bir komut çalıştırmak için, bu hızlı başlangıçta bir kod bloğunu kopyalamak üzere **Kopyala** ' yı seçin. Daha sonra CLı kabuğunuzun içine yapıştırın ve çalıştırın.

[Az Extension Add](/cli/azure/extension#az-extension-add) komutunu çalıştırın. 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma
Bu bölümde, bir IoT Hub 'ı ve bir kaynak grubu oluşturmak için Azure CLı 'yi kullanırsınız.  Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. IoT Hub 'ı, IoT uygulamanız ve cihazlar arasında çift yönlü iletişim için bir merkezi ileti hub 'ı görevi görür. 

Bir IoT Hub 'ı ve bir kaynak grubu oluşturmak için:

1. Bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az-group-create) komutunu çalıştırın. Aşağıdaki komut *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur. 
    >[!NOTE]
    > İsteğe bağlı olarak alternatif bir konum ayarlayabilirsiniz. Kullanılabilir konumları görmek için öğesini çalıştırın `az account list-locations` . Bu öğretici, örnek komutta gösterildiği gibi *eastus* kullanır. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. IoT Hub 'ı oluşturmak için [az IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) komutunu çalıştırın. IoT Hub 'ı oluşturmak birkaç dakika sürebilir. 

    *Youriothubname*. IoT Hub 'ınız için seçtiğiniz adı kullanarak aşağıdaki komutta yer tutucuyu ve çevreleyen ayraçları değiştirin. IoT Hub 'ı adı Azure 'da genel olarak benzersiz olmalıdır. Yer tutucusunu gördüğünüz her yerde, bu hızlı başlangıçta IoT Hub 'ınızın adını kullanın.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Sanal cihaz oluşturma
Bu bölümde, IoT Hub 'ınıza bağlı bir sanal IoT cihazı oluşturursunuz. 

Sanal cihaz oluşturmak için:
1. CLı kabuğunuzun [az IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) komutunu çalıştırın. Bu, sanal cihaz kimliğini oluşturur. 

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    *Mydevice*. Bu adı, bu makalenin geri kalanında sanal cihaz KIMLIĞI için doğrudan kullanabilirsiniz. İsteğe bağlı olarak, farklı bir ad kullanın. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  [Az IoT Hub cihazı-Identity Connection-String Show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) komutunu çalıştırın. 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    Bağlantı dizesi çıktısı aşağıdaki biçimdedir:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Bağlantı dizesini güvenli bir konuma kaydedin. 

> [!NOTE]
> CLı kabuğunu açık tutun. Sonraki adımlarda kullanacaksınız.