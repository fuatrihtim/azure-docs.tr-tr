---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: e459ea1e9d8d7d51a62ba3ed1d2de8815a1b4222
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104608"
---
Azure Stack Edge cihazınızda VM 'Leri dağıtabilmeniz için önce, istemcinizi Azure PowerShell üzerinden Azure Resource Manager aracılığıyla cihaza bağlanacak şekilde yapılandırmanız gerekir. Ayrıntılı yönergeler için bkz. [Azure Stack Edge cihazınızda Azure Resource Manager bağlama](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).

İstemcinizden cihaza erişmek için aşağıdaki adımları kullandığınızdan emin olun. Azure Resource Manager bağlandığınızda bu yapılandırmayı zaten tamamladınız ve artık yapılandırmanın başarılı olduğunu doğrulıyoruz. 

1. Aşağıdaki komutu çalıştırarak Azure Resource Manager iletişiminin çalıştığını doğrulayın:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Kimlik doğrulaması için yerel cihaz API 'Lerini çağırmak için şunu girin: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager aracılığıyla bağlanmak için, Kullanıcı adı *Edgearmuser* ve parolanızı sağlayın.

1. Kubernetes için işlem yapılandırdıysanız, bu adımı atlayabilirsiniz. Aksi takdirde, aşağıdakileri yaparak işlem için bir ağ arabirimini etkinleştirdiğinizden emin olun: 

   a. Yerel Kullanıcı arabiriminize, **işlem** ayarları ' na gidin.  
   b. Sanal anahtar oluşturmak için kullanmak istediğiniz ağ arabirimini seçin. Oluşturduğunuz VM 'Ler, bu bağlantı noktasına ve ilişkili ağa bağlı bir sanal anahtara eklenecektir. VM için kullanacağınız IP adresiyle eşleşen bir ağ seçtiğinizden emin olun.  

    ![Işlem yapılandırması ağ ayarları bölmesinin ekran görüntüsü.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Ağ arabiriminde **işlem Için etkinleştir** ' ın altında **Evet**' i seçin. Azure Stack Edge, bu ağ arabirimine karşılık gelen bir sanal anahtar oluşturur ve yönetir. Şu anda Kubernetes için belirli IP 'Leri girmeyin. İşlem, işlemin etkinleştirilmesi birkaç dakika sürebilir.

    > [!NOTE]
    > GPU VM 'Leri oluşturuyorsanız, internet 'e bağlı bir ağ arabirimi seçin. Bunun yapılması cihazınıza bir GPU uzantısı yüklemenizi sağlar.