---
title: Azure IoT Hub cihaz yönetimini kullanmaya başlama (Python) | Microsoft Docs
description: Uzak cihaz yeniden başlatma işlemini başlatmak için IoT Hub cihaz yönetimi 'ni kullanma. Doğrudan yöntemini çağıran bir doğrudan yöntem ve bir hizmet uygulaması içeren bir sanal cihaz uygulaması uygulamak için Python için Azure IoT SDK 'sını kullanırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87872727"
---
# <a name="get-started-with-device-management-python"></a>Cihaz yönetimini kullanmaya başlama (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* Bir IoT Hub oluşturmak ve IoT Hub 'ınızda bir cihaz kimliği oluşturmak için Azure portal kullanın.

* Bu cihazı yeniden yükleyen doğrudan bir yöntem içeren bir sanal cihaz uygulaması oluşturun. Doğrudan Yöntemler buluttan çağrılır.

* IoT Hub 'ınız aracılığıyla sanal cihaz uygulamasındaki önyükleme doğrudan yöntemini çağıran bir Python konsol uygulaması oluşturun.

Bu öğreticinin sonunda iki Python konsol uygulamanız vardır:

* daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanan **dmpatterns_getstarted_device. Kopyala**, bir yeniden başlatma doğrudan yöntemi alır, bir fiziksel yeniden başlatmanın benzetimini yapar ve son yeniden başlatmanın zamanını raporlar.

* sanal cihaz uygulamasında doğrudan bir yöntemi çağıran **dmpatterns_getstarted_service. Kopyala**, yanıtı görüntüler ve güncelleştirilmiş bildirilen özellikleri görüntüler.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu makaledeki cihaz örneği, 8883 numaralı bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma

Bu bölümde şunları yapacaksınız:

* Bulut tarafından çağrılan doğrudan bir yönteme yanıt veren bir Python konsol uygulaması oluşturma

* Cihazın yeniden başlatılmasını benzetme

* Cihazların ve en son yeniden başlatıldığı zaman cihaz ikizi sorgularını belirlemesine olanak tanımak için bildirilen özellikleri kullanın

1. Komut istemindeki **Azure-IoT-Device** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Bir metin düzenleyicisi kullanarak çalışma dizininizde **dmpatterns_getstarted_device. Kopyala** adlı bir dosya oluşturun.

3. `import` **Dmpatterns_getstarted_device. Kopyala** dosyasının başlangıcına aşağıdaki deyimlerini ekleyin.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. **CONNECTION_STRING** değişkenini ekleyin. `{deviceConnectionString}`Yer tutucu değerini, cihaz bağlantı dizeniz ile değiştirin. Daha önce Bu bağlantı dizesini [IoT Hub 'ına yeni bir cihaz kaydet](#register-a-new-device-in-the-iot-hub)bölümünde kopyaladınız.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Cihaza doğrudan yöntemi uygulamak için aşağıdaki işlev geri çağırmaları ekleyin.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Doğrudan yöntem dinleyicisini başlatın ve bekleyin.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. **Dmpatterns_getstarted_device. Kopyala** dosyasını kaydedin ve kapatın.

> [!NOTE]
> Sade ve basit bir anlatım gözetildiği için bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)makalesinde önerildiği gibi yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Doğrudan bir yöntem kullanarak cihazda Uzaktan yeniden başlatma tetikleyin

Bu bölümde, doğrudan bir yöntemi kullanarak bir cihazda Uzaktan yeniden başlatma işlemini başlatan bir Python konsol uygulaması oluşturacaksınız. Uygulama, bu cihazın son yeniden başlatma zamanını saptamak için Device ikizi sorgularını kullanır.

1. Komut istemindeki **Azure-IoT-Hub** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Bir metin düzenleyicisi kullanarak çalışma dizininizde **dmpatterns_getstarted_service. Kopyala** adlı bir dosya oluşturun.

3. `import` **Dmpatterns_getstarted_service. Kopyala** dosyasının başlangıcına aşağıdaki deyimlerini ekleyin.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Aşağıdaki değişken bildirimlerini ekleyin. `{IoTHubConnectionString}`Yer tutucu değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)' da daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin. `{deviceId}`Yer tutucu değerini, [IoT Hub 'ına yeni bir cihaz kaydetme](#register-a-new-device-in-the-iot-hub)bölümünde kaydettiğiniz cihaz kimliğiyle değiştirin.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Hedef cihazı yeniden başlatmak üzere cihaz yöntemini çağırmak için aşağıdaki işlevi ekleyin, sonra cihaz ikizlerini sorgulayın ve son yeniden başlatma süresini alın.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. **Dmpatterns_getstarted_service. Kopyala** dosyasını kaydedin ve kapatın.

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Komut isteminde, önyükleme doğrudan metodunu dinlemeye başlamak için aşağıdaki komutu çalıştırın.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Başka bir komut isteminde aşağıdaki komutu çalıştırarak, son yeniden başlatma zamanını bulmak üzere cihaz ikizi için uzaktan yeniden başlatma ve sorguyu tetikleyin.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Konsolda doğrudan yönteme cihaz yanıtı görürsünüz.

   Aşağıda yeniden başlatma doğrudan yöntemine yönelik cihaz yanıtı gösterilmektedir:

   ![Sanal cihaz uygulama çıkışı](./media/iot-hub-python-python-device-management-get-started/device.png)

   Aşağıda, önyükleme doğrudan yöntemini çağıran hizmet gösterilmektedir ve cihaz ikizi durumu için yoklama yapılır:

   ![Yeniden başlatma hizmeti çıkışını Tetikle](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
