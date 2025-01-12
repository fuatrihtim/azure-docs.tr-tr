---
title: Azure portal, Datacenter ortamını Azure Stack Edge Pro GPU dağıtılacak şekilde hazırlama öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU 'SU dağıtmaya yönelik ilk öğretici, Azure portal hazırlamayı içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: a58146c2c8121f3f0e3b564caafbb09396d39672
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568554"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Öğretici: Azure Stack Edge Pro 'YU GPU ile dağıtmaya hazırlanma 

Bu öğretici, GPU ile Azure Stack Edge Pro 'yu tamamen dağıtmak için gerekli olan dağıtım öğreticilerinde ilkidir. Bu öğreticide, Azure portal Azure Stack Edge kaynağını dağıtmaya yönelik nasıl hazırlanılacağı açıklanmaktadır.

Kurulum ve yapılandırma işlemini tamamlamak için yönetici ayrıcalıkları gerekir. Portal hazırlığı 10 dakikadan kısa sürer.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni kaynak oluşturma
> * Etkinleştirme anahtarı alma

### <a name="get-started"></a>başlarken

Azure Stack Edge Pro dağıtımı için öncelikle ortamınızı hazırlamanız gerekir. Ortam çalışmaya başladıktan sonra, cihazı tam olarak dağıtmak için gerekli adımları ve gerekirse isteğe bağlı adımları ve yordamları izleyin. Adım adım dağıtım yönergeleri, bu gerekli ve isteğe bağlı adımların her birini ne zaman gerçekleştirmeniz gerektiğini gösterir.

| Adım | Description |
| --- | --- |
| **Hazırlama** |Bu adımların, yaklaşan dağıtım için hazırlanmasından tamamlanması gerekir. |
| **[Dağıtım yapılandırma denetim listesi](#deployment-configuration-checklist)** |Dağıtımdan önce ve dağıtım sırasında bilgi toplamak ve bilgileri kaydetmek için bu denetim listesini kullanın. |
| **[Dağıtım önkoşulları](#prerequisites)** |Bu Önkoşullar, ortamın dağıtım için hazırlandığını doğrular. |
|  | |
|**Dağıtım öğreticileri** |Azure Stack Edge Pro cihazınızı üretime dağıtmak için bu öğreticiler gereklidir. |
|**[1. Azure Stack Edge Pro için Azure portal hazırlama](azure-stack-edge-gpu-deploy-prep.md)** |Azure Stack Edge kaynağını bir Azure Stack Box Edge fiziksel cihazı yüklemeden önce oluşturun ve yapılandırın. |
|**[2. Azure Stack Edge Pro 'Yu Install](azure-stack-edge-gpu-deploy-install.md)**|Azure Stack Edge Pro fiziksel cihazının paketini açın, raf ve kablo.  |
|**[3. Azure Stack Edge Pro 'ya bağlanma](azure-stack-edge-gpu-deploy-connect.md)** |Cihaz yüklendikten sonra, cihaz yerel Web Kullanıcı arabirimine bağlanın.  |
|**[4. Azure Stack Edge Pro için ağ ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Cihazınızın işlem ağı ve Web proxy ayarları dahil olmak üzere ağı yapılandırın.   |
|**[5. Azure Stack Edge Pro için cihaz ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Bir cihaz adı ve DNS etki alanı atayın, güncelleştirme sunucusunu ve cihaz zamanını yapılandırın. |
|**[6. Azure Stack Edge Pro için güvenlik ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Cihazınız için sertifikaları yapılandırın. Cihaz tarafından oluşturulan sertifikaları kullanın veya kendi sertifikalarınızı getirin.   |
|**[7. Azure Stack Edge Pro 'Yu etkinleştirin](azure-stack-edge-gpu-deploy-activate.md)** |Cihazı etkinleştirmek için hizmetten etkinleştirme anahtarını kullanın. Cihaz, SMB veya NFS paylaşımlarını ayarlamaya veya REST aracılığıyla bağlamaya hazırlanıyor. |
|**[8. işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)** |Cihazınızda işlem rolünü yapılandırın. Bir Kubernetes kümesi de oluşturulur. |
|**[9A. Kenar paylaşımları ile veri aktarma](./azure-stack-edge-gpu-deploy-add-shares.md)** |Paylaşımları ekleyin ve SMB veya NFS üzerinden paylaşımlara bağlanın. |
|**[9B. Edge depolama hesaplarıyla veri aktarma](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)** |Depolama hesapları ekleyin ve REST API 'Ler aracılığıyla blob depolamaya bağlanın. |


Artık Azure Stack Edge Pro cihazınız için yazılım yapılandırmasıyla ilgili bilgi toplamaya başlayabilirsiniz.

## <a name="deployment-configuration-checklist"></a>Dağıtım yapılandırma denetim listesi

Cihazınızı dağıtmadan önce, Azure Stack Edge Pro cihazınızda yazılımı yapılandırmak için bilgi toplamanız gerekir. Bu bilgilerin bir bölümünü önceden hazırlamak, cihazı ortamınızda dağıtma işlemini kolaylaştırmaya yardımcı olur. Cihazınızı dağıtırken yapılandırma ayrıntılarını göz önünde bırakmak için [Azure Stack Edge Pro dağıtım yapılandırması denetim listesini](azure-stack-edge-gpu-deploy-checklist.md) kullanın.


## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge kaynağınız, Azure Stack Edge Pro cihazınız ve veri merkezi ağı için yapılandırma önkoşulları aşağıda verilmiştir.

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge kaynağı için

Başlamadan önce aşağıdakilerden emin olun:

- Microsoft Azure aboneliğiniz Azure Stack Edge kaynağı için etkinleştirildi. [Microsoft kurumsal anlaşma (EA)](https://azure.microsoft.com/overview/sales-number/), [bulut çözümü sağlayıcısı (CSP)](/partner-center/azure-plan-lp)veya [Microsoft Azure sponsorluğu](https://azure.microsoft.com/offers/ms-azr-0036p/)gibi desteklenen bir abonelik kullandığınızdan emin olun. Kullandıkça Öde abonelikleri desteklenmez. Sahip olduğunuz Azure aboneliğinin türünü belirlemek için bkz. [Azure teklifi nedir?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer).
- Azure Stack Edge Pro/Data Box Gateway, IoT Hub ve Azure depolama kaynakları için kaynak grubu düzeyinde sahip veya katkıda bulunan erişiminiz var.

    - Azure Stack Edge/Data Box Gateway kaynağı oluşturmak için, kaynak grubu düzeyinde katkıda bulunan (veya üzeri) izinlere sahip olmanız gerekir. 
    - Ayrıca, `Microsoft.DataBoxEdge` ve kaynak sağlayıcılarının kayıtlı olduğundan emin olmanız gerekir `MicrosoftKeyVault` . Herhangi bir IoT Hub kaynağı oluşturmak için, `Microsoft.Devices` sağlayıcının kayıtlı olması gerekir. 
        - Kaynak sağlayıcısını kaydetmek için Azure portal, **abonelik > kaynak sağlayıcılarına > giriş > abonelikleri**' ne gidin. 
        - Belirli kaynak sağlayıcısını arayın, örneğin, `Microsoft.DataBoxEdge` ve kaynak sağlayıcısını kaydedin. 
    - Bir depolama hesabı kaynağı oluşturmak için, kaynak grubu düzeyinde katkıda bulunan veya daha yüksek erişim kapsamına ihtiyacınız vardır. Azure depolama, varsayılan olarak kayıtlı bir kaynak sağlayıcısıdır.
- Etkinleştirme anahtarı veya bir depolama hesabı kullanan paylaşma oluşturma gibi kimlik bilgisi işlemleri oluşturmak için Azure Active Directory Graph API yönetici veya Kullanıcı erişimi vardır. Daha fazla bilgi için bkz. [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro cihazı için

Fiziksel cihazı dağıtmadan önce şunlardan emin olun:

- Sevkiyat paketine dahil edilen güvenlik bilgilerini gözden geçirdiniz.
- Veri merkezinizde bulunan ve cihazı bağlamak için standart 19 "rafındaki bir 1U yuvasına sahipsiniz.
- Cihazın güvenle geri kalanında düz, kararlı ve düzeyi bir iş yüzeyine erişebilirsiniz.
- Cihazı ayarlamayı planladığınız sitenin, bağımsız bir kaynaktan veya kesintisiz güç kaynağı (UPS) olan bir raf güç dağıtım biriminden (PDU) standart AC gücü vardır.
- Fiziksel cihaza erişebiliyor olmanız gerekir.


### <a name="for-the-datacenter-network"></a>Veri merkezi ağı için

Başlamadan önce aşağıdakilerden emin olun:

- Veri merkezinizdeki ağ, Azure Stack Edge Pro cihazınız için ağ gereksinimleri uyarınca yapılandırılır. Daha fazla bilgi için bkz. [Edge Pro sistem gereksinimleri Azure Stack](azure-stack-edge-system-requirements.md).

- Azure Stack Edge Pro 'nun normal işletim koşullarında şunları yapabilirsiniz:

    - Cihazın güncel kalmasını sağlamak için en az 10 Mbps indirme bant genişliği.
    - Dosyaları aktarmak için en az 20 Mbps adanmış karşıya yükleme ve indirme bant genişliği.

## <a name="create-a-new-resource"></a>Yeni kaynak oluşturma

Fiziksel cihazınızı yönetmek için mevcut bir Azure Stack Edge kaynağınız varsa, bu adımı atlayın ve [etkinleştirme anahtarını almak](#get-the-activation-key)için gidin.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Stack Edge kaynağı oluşturmak için Azure portal aşağıdaki adımları uygulayın.

1. Bu URL 'de Azure portal oturum açmak için Microsoft Azure kimlik bilgilerinizi kullanın: [https://portal.azure.com](https://portal.azure.com) .

2. Sol bölmede **+ kaynak oluştur**' u seçin. **Azure Stack Edge/Data Box Gateway** için arama yapın ve seçin. **Oluştur**’u seçin. 

3. Azure Stack Edge Pro cihazı için kullanmak istediğiniz aboneliği seçin. Bu fiziksel cihazı göndermek istediğiniz ülkeyi seçin. **Cihazları göster**' i seçin.

    ![Kaynak oluşturma 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Cihaz türü seçin. **Azure Stack Edge Pro** altında **GPU Ile Azure Stack Edge Pro** ' yı seçin ve ardından **Seç**' i seçin. Herhangi bir sorun görürseniz veya cihaz türünü seçmezseniz, [sorun giderme sorunları gider](azure-stack-edge-troubleshoot-ordering.md)bölümüne gidin.

    ![Kaynak oluşturma 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. İş ihtiyaınıza göre, NVIDIA 'dan 1 veya 2 grafik Işleme birimi (GPU) Azure Stack Edge Pro 'Yu seçebilirsiniz. 

    ![Kaynak oluşturma 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. **Temel bilgiler** sekmesinde, aşağıdaki **proje ayrıntılarını** girin veya seçin.
    
    |Ayar  |Değer  |
    |---------|---------|
    |Abonelik    |Abonelik, önceki seçime göre otomatik olarak doldurulur. Abonelik fatura hesabınıza bağlıdır. |
    |Kaynak grubu  |Mevcut grubu seçin veya yeni bir grup oluşturun.<br>[Azure Kaynak Grupları](../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin.     |

7. Aşağıdaki **örnek ayrıntılarını** girin veya seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Ad   | Kaynağı tanımlamak için kolay bir ad.<br>Ad, harf, sayı ve kısa çizgi içeren 2 ile 50 karakter arasında.<br> Ad bir harf veya rakamla başlar ve biter.        |
    |Region     |Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için bkz. [bölgeye göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Kamu kullanıyorsanız, tüm kamu bölgeleri [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/regions/)gösterildiği gibi kullanılabilir.<br> Cihazınızı dağıtmak istediğiniz coğrafi bölgeye yakın bir konum seçin.|

    ![Kaynak oluştur 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. **İleri ' yi seçin: sevkiyat adresi**.

    - Zaten bir cihazınız varsa, **zaten bir cihazım varsa** Birleşik giriş kutusunu seçin.

        ![Kaynak oluştur 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Bu, sipariş ettiğiniz yeni bir cihaz ise, ilgili kişi adını, şirketi, cihazı göndermek için adresi ve iletişim bilgilerini girin.

        ![Kaynak oluşturma 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. **Sonraki: Etiketler**' i seçin. İsteğe bağlı olarak kaynakları kategorilere ayırarak ve faturalandırmayı birleştirmek için Etiketler sağlayın. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin.

10. **Gözden geçir + oluştur** sekmesinde, **fiyatlandırma ayrıntılarını**, **kullanım koşulları** ve kaynağınızın ayrıntılarını gözden geçirin. **Gizlilik koşullarını Incelediğim** Birleşik giriş kutusunu seçin.

    ![Kaynak oluşturma 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    Ayrıca, kaynak oluşturma sırasında, bulut hizmetlerinde kimlik doğrulaması yapmanızı sağlayan bir Yönetilen Hizmet Kimliği (MSI) etkin olduğunu da size bildirilir. Bu kimlik, kaynak mevcut olduğu sürece için mevcut.

11. **Oluştur**’u seçin.

    Kaynağın oluşturulması birkaç dakika sürer. Azure Stack Edge cihazının Azure 'da kaynak sağlayıcısıyla iletişim kurmasına imkan tanıyan bir MSI de oluşturulur.

    Kaynak başarıyla oluşturulup dağıtıldıktan sonra bilgilendirirsiniz. **Kaynağa git**’i seçin.

    ![Azure Stack Edge Pro kaynağına git](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Sipariş yerleştirildikten sonra, Microsoft siparişi gözden geçirir ve sizinle (e-posta aracılığıyla) teslimat ayrıntıları ile iletişim kurar.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> Aynı anda birden çok sipariş oluşturmak veya var olan bir siparişi kopyalamak istiyorsanız, [Azure örnekleri içindeki betikleri](https://github.com/Azure-Samples/azure-stack-edge-order)kullanabilirsiniz. Daha fazla bilgi için bkz. README dosyası.

Sipariş işlemi sırasında herhangi bir sorunla karşılaşırsanız, bkz. [sorun giderme sorunları giderme](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gerekirse, ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure Stack Edge kaynağı oluşturmak için Azure CLı 'de aşağıdaki komutları çalıştırın.

1. [Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir kaynak grubu oluşturun veya var olan bir kaynak grubunu kullanın:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Bir cihaz oluşturmak için [az databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) komutunu kullanın:

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   Cihazınızı dağıtmak istediğiniz coğrafi bölgeye yakın bir konum seçin. Bölge yalnızca cihaz yönetimi için meta verileri depolar. Gerçek veriler herhangi bir depolama hesabında depolanabilir.

   Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için bkz. [bölgeye göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Kamu kullanıyorsanız, tüm kamu bölgeleri [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/regions/)gösterildiği gibi kullanılabilir.

1. Sipariş oluşturmak için [az databoxedge Order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) komutunu çalıştırın:

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Kaynağın oluşturulması birkaç dakika sürer. Sıralamayı görmek için [az databoxedge Order Show](/cli/azure/databoxedge/order#az_databoxedge_order_show) komutunu çalıştırın:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Sipariş verdikten sonra, Microsoft siparişi gözden geçirir ve teslim ayrıntıları ile e-posta ile sizinle iletişim kurar.

---

## <a name="get-the-activation-key"></a>Etkinleştirme anahtarı alma

Azure Stack Edge kaynağı çalışır duruma geçtikten sonra etkinleştirme anahtarını almanız gerekir. Bu anahtar Azure Stack Edge Pro cihazınızı etkinleştirmek ve kaynakla bağlamak için kullanılır. Bu anahtarı şimdi, Azure portalındayken alabilirsiniz.

1. Oluşturduğunuz kaynağı seçin ve **genel bakış**' ı seçin.

2. Sağ bölmede, Azure Key Vault için bir ad girin veya varsayılan adı kabul edin. Anahtar Kasası adı 3 ila 24 karakter arasında olabilir.

   Cihazınızla etkinleştirilen her Azure Stack Edge kaynağı için bir Anahtar Kasası oluşturulur. Anahtar Kasası, gizli dizileri depolamanıza ve erişmenize izin verir, örneğin, hizmetin kanal bütünlüğü anahtarı (CIK) anahtar kasasında depolanır. 

   Bir Anahtar Kasası adı belirledikten sonra, etkinleştirme anahtarı oluşturmak için **anahtar oluştur** ' u seçin. 

   ![Etkinleştirme anahtarını alma](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   Anahtar Kasası ve etkinleştirme anahtarı oluşturulurken birkaç dakika bekleyin. Anahtarı kopyalamak için Kopyala simgesini seçin ve daha sonra kullanmak üzere kaydedin.<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - Etkinleştirme anahtarı üretilmeden üç gün sonra dolar.
> - Anahtarın süresi dolmuşsa yeni bir anahtar oluşturun. Eski anahtar geçerli olmaz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki gibi Azure Stack Edge Pro konuları hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Yeni kaynak oluşturma
> * Etkinleştirme anahtarı alma

Azure Stack Edge Pro 'Yu nasıl yükleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro 'Yu yükler](./azure-stack-edge-gpu-deploy-install.md)