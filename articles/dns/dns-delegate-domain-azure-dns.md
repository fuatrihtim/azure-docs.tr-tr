---
title: 'Öğretici: etki alanınızı ve alt etki alanınızı barındırma-Azure DNS'
description: Bu öğreticide, DNS Bölgelerinizi barındırmak için Azure DNS yapılandırmayı öğreneceksiniz.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282168"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Öğretici: Azure DNS’te etki alanınızı barındırma

Azure DNS'yi DNS etki alanınızı barındırmak ve DNS kayıtlarınızı yönetmek için kullanabilirsiniz. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz.

Örneğin, contoso.net etki alanını bir etki alanı adı kayıt şirketinden satın aldığınızı ve Azure DNS'de contoso.net adlı bir bölge oluşturduğunuzu varsayalım. Etki alanının sahibi olduğunuzdan, kayıt şirketiniz size etki alanınız için ad sunucusu (NS) kayıtlarını yapılandırma seçeneğini sunar. Kaydedici, NS kayıtlarını .NET üst bölgesine depolar. Dünyanın her yerindeki Internet kullanıcıları, contoso.net 'deki DNS kayıtlarını çözümlemeye çalıştıklarında Azure DNS bölgesindeki etki alanına yönlendirilir.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir DNS bölgesi oluşturun.
> * Ad sunucularının bir listesini alın.
> * Etki alanını devredebilirsiniz.
> * Temsilinin çalıştığını doğrulayın.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure DNS barındırabilmeniz için, test etmek için kullanılabilir bir etki alanı adına sahip olmanız gerekir. Bu etki alanı üzerinde tam denetime sahip olmanız gerekir. Tam denetim, etki alanı için ad sunucusu (NS) kayıtlarını ayarlama olanağını kapsar.

Bu örnekte, üst etki alanına **contoso.net** olarak başvuracağız

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

1. Bir DNS bölgesi oluşturmak için [Azure Portal](https://portal.azure.com/) gidin. **DNS bölgelerini** arayın ve seçin.

   ![DNS bölgesi](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. **DNS bölgesi oluştur**' u seçin.
1. **DNS bölgesi oluştur** sayfasında aşağıdaki değerleri girin ve **Oluştur**' u seçin. Örneğin, **contoso.net**
      > [!NOTE] 
      > Oluşturmakta olduğunuz yeni bölge bir alt bölgedir (örneğin, üst bölge = contoso.net alt bölgesi = child.contoso.net), lütfen [Yeni bir alt DNS bölgesi oluşturma öğreticimize](./tutorial-public-dns-zones-child.md) başvurun

    | **Ayar** | **Değer** | **Ayrıntılar** |
    |--|--|--|
    | **Proje ayrıntıları:**  |  |  |
    | **Kaynak grubu**    | ContosoRG | Bir kaynak grubu oluşturun. Kaynak grubu adı, seçtiğiniz abonelik içinde benzersiz olmalıdır. Kaynak grubunun konumu, DNS bölgesini etkilemez. DNS bölgesi konumu her zaman "geneldir" olur ve gösterilmez. |
    | **Örnek ayrıntıları:** |  |  |
    | **Bölge alt öğesi**        | İşaretlenmeyen bırak | Bu bölge bir [alt bölge](./tutorial-public-dns-zones-child.md) **olmadığından** , bu işareti kaldırılmış olarak bırakmalısınız |
    | **Ad**              | contoso.net | Üst bölge adınızın alanı      |
    | **Konum**          | Doğu ABD | Bu alan, kaynak grubu oluşturma işlemi kapsamında seçilen konuma göre belirlenir.  |
    

## <a name="retrieve-name-servers"></a>Ad sunucularını alma

DNS bölgenizi Azure DNS'ye devretmeden önce, bölgenizin ad sunucularını bilmeniz gerekir. Azure DNS, her bölge oluşturmada bir havuzdan ad sunucuları ayırır.

1. Oluşturulan DNS bölgesiyle, Azure Portal **Sık Kullanılanlar** bölmesinde, **Tüm kaynaklar**’ı seçin. **Tüm kaynaklar** sayfasında DNS bölgenizi seçin. Seçtiğiniz abonelikte zaten çeşitli kaynaklar varsa, uygulama ağ geçidine kolayca erişebilmek için etki alanı adınızı **ada göre filtrele** kutusuna girebilirsiniz. 

1. DNS bölgesi sayfasından ad sunucularını alın. Bu örnekte, contoso.net bölgesine ad Servers *ns1-01.Azure-DNS.com*, *NS2-01.Azure-DNS.net*, *NS3-01.Azure-DNS.org* ve *NS4-01.Azure-DNS.info* atanmıştır:

   ![Ad sunucularının listesi](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS, bölgenizdeki yetkili NS kayıtlarını atanan ad sunucularını içerecek şekilde otomatik olarak oluşturur.

## <a name="delegate-the-domain"></a>Etki alanını devretme

Artık DNS bölgesi oluşturulduğuna ve ad sunucularınız olduğuna göre, üst etki alanını Azure DNS ad sunucularıyla güncelleştirmeniz gerekir. Her kayıt şirketi, bir etki alanının ad sunucusu kayıtlarını değiştirmek için kendi DNS yönetim araçlarına sahiptir. 

1. Kayıt şirketinin DNS yönetim sayfasında NS kayıtlarını düzenleyin ve NS kayıtlarını Azure DNS ad sunucularıyla değiştirin.

1. Azure DNS için bir etki alanı temsilcisi seçtiğinizde, Azure DNS tarafından sağlanan ad sunucularını kullanmanız gerekir. Etki alanınız adının ne olursa olsun, tüm dört ad sunucularını kullanın. Etki alanı temsili, etki alanınız ile aynı üst düzey etki alanını kullanmak için bir ad sunucusu gerektirmez.

> [!NOTE]
> Ad sunucusu adreslerini kopyalarken adresinin sonundaki noktayı da kopyaladığınızdan emin olun. Sondaki nokta bir tam etki alanı adının sonuna gösterir. NS adı sonunda yoksa, bazı kayıt şirketlerinde dönemi ekler. DNS RFC ile uyumlu olmak için sondaki dönemi dahil edin.

Kendi bölgenizdeki ad sunucularını kullanan Temsilciler (bazen *Gösterim ad sunucuları* olarak adlandırılır) Azure DNS 'de Şu anda desteklenmemektedir.

## <a name="verify-the-delegation"></a>Temsilciyi doğrulama

Temsilciyi tamamladıktan sonra, bölgeniz için yetki başlangıcı (SOA) kaydını sorgulamak üzere *nslookup* gibi bir araç kullanarak çalıştığını doğrulayabilirsiniz. SOA kaydı, bölge oluşturulurken otomatik olarak oluşturulur. Çalışıp çalışmadığını başarıyla doğrulayabilmeniz için, temsilciyi tamamladıktan sonra 10 dakika veya daha fazla beklemeniz gerekebilir. Değişikliklerin DNS sisteminde yayılması daha uzun sürebilir.

Azure DNS ad sunucularını belirtmeniz gerekmez. Temsilci seçimi doğru ayarlanmışsa normal DNS çözümleme işlemi ad sunucularını otomatik olarak bulur.

1. Bir komut isteminden, aşağıdaki örneğe benzer bir Nslookup komutu girin:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Yanıtlarınızın aşağıdaki nslookup çıktısına benzer göründüğünü doğrulayın:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçmeyi düşünüyorsanız **contosoRG** kaynak grubunu tutabilirsiniz. Aksi halde **contosoRG** kaynak grubunu silerek bu öğreticide oluşturulan kaynakları silebilirsiniz.

- **ContosoRG** kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, etki alanınız için bir DNS bölgesi oluşturdunuz ve Azure DNS için temsilciniz. Azure DNS ve web uygulamaları hakkında daha fazla bilgi için web uygulaması öğreticileriyle devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Özel etki alanında web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)
