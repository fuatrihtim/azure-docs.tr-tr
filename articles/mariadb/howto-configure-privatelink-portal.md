---
title: Özel bağlantı-Azure portal-MariaDB için Azure veritabanı
description: Azure portal 'ten MariaDB için Azure veritabanı için özel bağlantıyı yapılandırma hakkında bilgi edinin
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 79b3c3f8eca2fa4442a7845ca4aa3921d0302453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659633"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Portal kullanarak MariaDB için Azure veritabanı için özel bağlantı oluşturma ve yönetme

Özel uç nokta, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak sağlar.  Bu makalede, Azure sanal ağında bir sanal makıne oluşturmak için Azure portal kullanmayı ve Azure özel uç noktası ile MariaDB sunucusu için Azure veritabanını nasıl kullanacağınızı öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!NOTE]
> Özel bağlantı özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Veritabanı sunucusunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma
[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-an-azure-vm"></a>Azure VM oluşturma

Bu bölümde, özel bağlantı kaynağına (Azure 'da bir MariaDB sunucusu) erişmek için kullanılan VM 'yi barındırmak için sanal ağ ve alt ağ oluşturacaksınız.

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma
Bu bölümde, özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırmak için bir sanal ağ ve alt ağ oluşturacaksınız.

1. Ekranın sol üst kısmında **kaynak oluştur**  >  **ağ**  >  **sanal ağ**' ı seçin.
2. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | *MyVirtualNetwork* girin. |
    | Adres alanı | *10.1.0.0/16* girin. |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *myresourcegroup* yazın ve ardından **Tamam**' ı seçin. |
    | Konum | **Batı Avrupa**'yı seçin.|
    | Alt ağ adı | *Mysubnet* yazın. |
    | Alt Ağ - Adres aralığı | *10.1.0.0/24* girin. |
    |||
3. Rest 'i varsayılan olarak bırakın ve **Oluştur**' u seçin.

### <a name="create-virtual-machine"></a>Sanal makine oluştur

1. Azure Portal ekranın sol üst tarafında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin.

2. **Sanal makine oluşturma-temel bilgiler** bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.  |
    | **ÖRNEK AYRıNTıLARı** |  |
    | Sanal makine adı | *Myvm*' i girin. |
    | Region | **Batı Avrupa**'yı seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan **altyapı yedekliliği gerekli değildir**. |
    | Görüntü | **Windows Server 2019 Datacenter** öğesini seçin. |
    | Boyut | Varsayılan **Standart DS1 v2**' i bırakın. |
    | **YÖNETİCİ HESABI** |  |
    | Kullanıcı adı | Seçmekten bir Kullanıcı adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    | Parolayı Onayla | Parolayı yeniden girin. |
    | **GELEN BAĞLANTI NOKTASI KURALLARI** |  |
    | Genel gelen bağlantı noktaları | Varsayılanı **yok** olarak bırakın. |
    | **TASARRUF EDİN** |  |
    | Zaten bir Windows lisansınız var mı? | Varsayılan **Hayır** olarak bırakın. |
    |||

1. **İleri ' yi seçin: diskler**.

1. **Sanal makine oluştur - Diskler** bölümünde varsayılan değerleri değiştirmeyin ve **Sonraki: Ağ** seçeneğini belirleyin.

1. **Sanal makine oluşturma-ağ oluşturma** bölümünde şu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | Varsayılan **MyVirtualNetwork** bırakın.  |
    | Adres alanı | Varsayılan **10.1.0.0/24**' i bırakın.|
    | Alt ağ | Varsayılan **Mysubnet (10.1.0.0/24)** olarak bırakın.|
    | Genel IP | Varsayılan **(yeni) myVm-ip**' i bırakın. |
    | Genel gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
    | Gelen bağlantı noktalarını seçin | **Http** ve **RDP**' yi seçin.|
    |||


1. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

1. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

## <a name="create-an-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı oluşturma

Bu bölümde, Azure 'da bir MariaDB sunucusu için Azure veritabanı oluşturacaksınız. 

1. Azure Portal ekranın sol üst tarafında, **kaynak oluştur**  >  **veritabanları**' nı  >  **MariaDB için Azure veritabanı**' nı seçin.

1. **MariaDB Için Azure veritabanı** 'nda şu bilgileri sağlayın:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **Sunucu ayrıntıları** |  |
    |Sunucu adı  | *Sunucum* girin. Bu ad alındıysanız, benzersiz bir ad oluşturun.|
    | Yönetici kullanıcı adı| Tercih etmek için bir yönetici adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 8 karakter uzunluğunda olmalı ve tanımlanan gereksinimleri karşılamalıdır. |
    | Konum | MariaDB sunucunuzun bulunmasını istediğiniz bir Azure bölgesi seçin. |
    |Sürüm  | Gerekli olan MariaDB sunucusunun veritabanı sürümünü seçin.|
    | İşlem + depolama| İş yüküne göre sunucu için gereken fiyatlandırma katmanını seçin. |
    |||

7. **Tamam**’ı seçin. 
8. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz. 
9. Doğrulama başarılı iletisini gördüğünüzde **Oluştur**' u seçin. 
10. Doğrulama başarılı iletisini gördüğünüzde oluştur ' u seçin. 

> [!NOTE]
> Bazı durumlarda, MariaDB için Azure veritabanı ve sanal ağ alt ağı farklı aboneliklerdedir. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelikte da **Microsoft. Dbformarıdb** kaynak sağlayıcısının kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma

Bu bölümde, MariaDB sunucusuna özel bir uç nokta oluşturacaksınız. 

1. Azure Portal ekranın sol üst tarafında, **kaynak oluştur**  >  **ağ**  >  **özel bağlantısı**' nı seçin.
2. **Özel bağlantı merkezi 'Ne genel bakış**' da, **bir hizmete özel bağlantı oluşturma** seçeneğinde, **Başlat**' ı seçin.

    ![Özel bağlantıya genel bakış](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. **Özel uç nokta oluşturma-temel** bilgiler için, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **Örnek ayrıntıları** |  |
    | Name | *myPrivateEndpoint* değerini girin. Bu ad alındıysanız, benzersiz bir ad oluşturun. |
    |Region|**Batı Avrupa**'yı seçin.|
    |||
5. **Sonraki: kaynak**' ı seçin.
6. **Özel uç nokta oluştur-kaynak** bölümünde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | Dizinimde bir Azure kaynağına bağlan ' ı seçin.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. Dbformarıdb/sunucuları**' nı seçin. |
    | Kaynak |*Sunucum* seçin|
    |Hedef alt kaynak |*Mariadbserver* seçin|
    |||
7. Ileri 'yi seçin **: yapılandırma**.
8. **Özel uç nokta oluşturma-yapılandırma**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**AĞ**| |
    | Sanal ağ| *MyVirtualNetwork* öğesini seçin. |
    | Alt ağ |  *Mysubnet* öğesini seçin. |
    |**ÖZEL DNS TÜMLEŞTİRMESİ**||
    |Özel DNS bölgesi ile tümleştirme |**Evet**’i seçin. |
    |Özel DNS Bölgesi |Seçin *(yeni) Privatelink. MariaDB. Database. Azure. com* |
    |||

    > [!Note] 
    > Hizmetiniz için önceden tanımlanmış özel DNS bölgesini kullanın veya tercih ettiğiniz DNS bölge adını sağlayın. Ayrıntılar için [Azure HIZMETLERI DNS bölge yapılandırması](../private-link/private-endpoint-dns.md) ' na bakın.

1. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz. 
2. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin. 

    ![Özel bağlantı oluşturuldu](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > Müşteri DNS ayarındaki FQDN, yapılandırılan özel IP 'ye çözümlenmez. [Burada](../dns/dns-operations-recordsets-portal.md)gösterildiği gibi, yapılandırılmış FQDN IÇIN bir DNS bölgesi oluşturmanız gerekir.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Uzak Masaüstü (RDP) kullanarak sanal makineye bağlanma


**Myvm**'yi oluşturduktan sonra Internet 'ten şu şekilde bağlanın: 

1. Portalın arama çubuğuna *myVm* değerini girin.

1. **Bağlan** düğmesini seçin. **Bağlan** düğmesini seçtikten sonra **sanal makineye bağlan** açılır.

1. **RDP Dosyasını İndir**’i seçin. Azure bir Uzak Masaüstü Protokolü (*. rdp*) dosyası oluşturur ve bilgisayarınıza indirir.

1. *İndirilen. rdp* dosyasını açın.

    1. İstendiğinde **Bağlan**’ı seçin.

    1. VM oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

        > [!NOTE]
        >   >  VM oluştururken girdiğiniz kimlik bilgilerini belirtmek için **farklı bir hesap kullan**' ı seçmeniz gerekebilir.

1. **Tamam**’ı seçin.

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bir sertifika uyarısı alırsanız **Evet**’i veya **Devam**’ı seçin.

1. VM masaüstü seçildikten sonra, bunu yerel masaüstünüze geri dönmek için simge durumuna küçültün.

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>MariaDB sunucusuna VM 'den özel olarak erişin

1. *Myvm* uzak masaüstünde PowerShell ' i açın.

2. Girin  `nslookup mydemomserver.privatelink.mariadb.database.azure.com` . 

    Şuna benzer bir ileti alırsınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Kullanılabilir herhangi bir istemciyi kullanarak MariaDB sunucusu için özel bağlantı bağlantısını test edin. Aşağıdaki örnekte, işlemi yapmak için [MySQL çalışma ekranı](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) kullandım.


4. **Yeni bağlantı**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu türü| **MariaDB** öğesini seçin.|
    | Sunucu adı| *Mydemoserver.Privatelink.MariaDB.Database.Azure.com* seçin |
    | Kullanıcı adı | username@servernameMariaDB sunucu oluşturma sırasında belirtilen kullanıcı adını girin. |
    |Parola |MariaDB sunucu oluşturma sırasında bir parola girin. |
    |SSL|**Gerekli**' yi seçin.|
    ||

5. **Bağlantıyı Sına** veya **Tamam ' ı** seçin.

6. I Sol menüden veritabanlarına gözatıp MariaDB veritabanından bilgi oluşturma veya sorgulama

7. MyVm ile uzak masaüstü bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Özel uç nokta, MariaDB sunucusu ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin:

1.  **   Portalın üst kısmındaki **arama** kutusuna myresourcegroup yazın ve arama sonuçlarından  *myresourcegroup* öğesini seçin   .
2. **Kaynak grubunu sil**'i seçin.
3. **Kaynak grubu adını yazın** ve **Sil**' i seçmek için myresourcegroup girin.

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır, bir sanal ağ üzerinde bir VM oluşturdunuz, MariaDB için Azure veritabanı ve özel erişim için özel bir uç nokta. İnternet 'ten bir VM 'ye bağlandınız ve özel bağlantı kullanarak MariaDB sunucusuna güvenli bir şekilde iletilecaksınız. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel uç noktası nedir?](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md