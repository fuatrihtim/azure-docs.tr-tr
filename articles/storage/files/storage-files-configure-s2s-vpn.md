---
title: Azure dosyaları ile kullanmak için siteden siteye (S2S) VPN yapılandırma | Microsoft Docs
description: Azure dosyaları ile kullanılmak üzere siteden siteye (S2S) VPN yapılandırma
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629300"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Azure dosyaları ile kullanmak için siteden siteye VPN yapılandırma
Azure dosya paylaşımlarınızı şirket içi ağınızdan SMB üzerinden bağlamak için, 445 numaralı bağlantı noktasını açmadan, siteden siteye (S2S) VPN bağlantısı kullanabilirsiniz. Azure [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak sıteden siteye VPN kurabilirsiniz. Bu bir Azure kaynağı, VPN hizmetleri sunar ve depolama hesapları veya diğer Azure kaynaklarıyla birlikte bir kaynak grubunda dağıtılır.

![Bir Azure VPN ağ geçidinin, bir Azure dosya paylaşımından bir S2S VPN kullanarak şirket içi bir siteye bağlanan bir topoloji grafiği](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Azure dosyaları için kullanılabilir ağ seçenekleri hakkında ayrıntılı bir tartışmaya devam etmeden önce [Azure dosyaları ağ genel bakış ' ı](storage-files-networking-overview.md) okumanızı kesinlikle öneririz.

Makalesinde, Azure dosya paylaşımlarını doğrudan şirket içinde bağlamak için siteden siteye VPN yapılandırma adımları ayrıntılı olarak açıklanır. Bir siteden siteye VPN üzerinden Azure Dosya Eşitleme eşitleme trafiğini yönlendirmek istiyorsanız, lütfen [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarını yapılandırma](storage-sync-files-firewall-and-proxy.md)konusuna bakın.

## <a name="prerequisites"></a>Önkoşullar
- Şirket içinde bağlamak istediğiniz bir Azure dosya paylaşımıdır. Azure dosya paylaşımları, birden çok dosya paylaşımı dağıtabileceğiniz ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarına ek olarak, bir paylaşılan depolama havuzunu temsil eden yönetim yapıları içindeki depolama hesaplarında dağıtılır. Azure dosya paylaşımları ve depolama hesaplarını [Azure dosya paylaşımı oluşturma](storage-how-to-create-file-share.md)bölümünde dağıtma hakkında daha fazla bilgi edinebilirsiniz.

- Şirket içinde bağlamak istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabı için özel bir uç nokta. Özel uç nokta oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları ağ uç noktalarını yapılandırma](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Şirket içi veri merkezinizde Azure VPN Gateway ile uyumlu bir ağ gereci veya sunucusu. Azure dosyaları, şirket içi ağ gerecinden bağımsız olarak seçilmiş ancak Azure VPN Gateway [Sınanan cihazların bir listesini](../../vpn-gateway/vpn-gateway-about-vpn-devices.md)tutar. Farklı ağ gereçleri farklı özellikler, performans özellikleri ve yönetim işlevleri sunar. bu nedenle, bir ağ gereci seçerken bunları göz önünde bulundurun.

    Mevcut bir ağ gereciniz yoksa, Windows Server, şirket içi ağ gereci olarak kullanılabilecek bir yerleşik sunucu rolü, Yönlendirme ve uzaktan erişim (RRAS) içerir. Windows Server 'da yönlendirme ve uzaktan erişim yapılandırma hakkında daha fazla bilgi edinmek için bkz. [RAS ağ geçidi](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Depolama hesabını VNet 'e Ekle
Azure portal, şirket içinde bağlamak istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabına gidin. Depolama hesabının içindekiler tablosunda **güvenlik duvarları ve sanal ağlar** girişini seçin. Oluşturduğunuz sırada depolama hesabınıza bir sanal ağ eklemediğiniz takdirde, ortaya çıkan bölmenin **tüm ağların** seçtiği radyo **erişimine izin ver** düğmesine sahip olması gerekir.

Depolama hesabınızı istenen sanal ağa eklemek için **Seçili ağlar**' ı seçin. **Sanal ağlar** alt başlığının altında, istenen duruma bağlı olarak **+ var olan sanal ağı ekle** veya **+ Yeni sanal ağ ekle** ' ye tıklayın. Yeni bir sanal ağ oluşturmak yeni bir Azure kaynağının oluşturulmasına neden olur. Yeni veya var olan VNet kaynağının depolama hesabı ile aynı kaynak grubunda veya abonelikte olması gerekmez, ancak depolama hesabı ile aynı bölgede ve VPN Gateway VNet 'iniz dağıttığınız kaynak grubu ile aynı bölgede olması gerekir. 

![Depolama hesabına var olan veya yeni bir sanal ağ ekleme seçeneği sunan Azure portal ekran görüntüsü](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Var olan sanal ağı eklerseniz, bu sanal ağın depolama hesabının ekleneceği bir veya daha fazla alt ağını seçmeniz istenir. Yeni bir sanal ağ seçerseniz, sanal ağın oluşturulmasının bir parçası olarak bir alt ağ oluşturacak ve daha sonra sanal ağ için elde edilen Azure kaynağı aracılığıyla daha fazla eklenebilir.

Daha önce aboneliğinize bir depolama hesabı eklemediyseniz, Microsoft. Storage hizmeti uç noktasının sanal ağa eklenmesi gerekir. Bu işlem biraz zaman alabilir ve bu işlem tamamlanana kadar, bu depolama hesabı içindeki Azure dosya paylaşımlarına VPN bağlantısı ile birlikte erişemeyeceksiniz. 

## <a name="deploy-an-azure-vpn-gateway"></a>Azure VPN Gateway dağıtma
Azure portal içindekiler tablosunda **Yeni kaynak oluştur** ' u seçin ve *sanal ağ geçidi* için arama yapın. Sanal ağ geçidinizin, önceki adımda dağıttığınız sanal ağ ile aynı abonelikte, Azure bölgesinde ve kaynak grubunda olmalıdır (sanal ağ çekildiğinde kaynak grubunun otomatik olarak seçili olduğunu unutmayın). 

Azure VPN Gateway dağıtma amacıyla, aşağıdaki alanları doldurmanız gerekir:

- **Ad**: VPN Gateway için Azure kaynağının adı. Bu ad, yönetilebilmeniz için yararlı bulduğunuz herhangi bir ad olabilir.
- **Bölge**: VPN Gateway dağıtılacağı bölge.
- **Ağ geçidi türü**: sıteden siteye VPN dağıtmak amacıyla **VPN**'yi seçmeniz gerekir.
- **VPN türü**: VPN cihazınıza bağlı olarak, *yönlendirme tabanlı** veya **ilke tabanlı** seçeneklerinden birini belirleyebilirsiniz. Rota tabanlı VPN 'Ler Ikev2 'yi destekler, ilke tabanlı VPN 'Ler yalnızca IKEv1 'yi destekler. İki tür VPN ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [ilke tabanlı ve rota tabanlı VPN ağ geçitleri hakkında](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **SKU**: SKU, Izin verilen siteden siteye tünel SAYıSıNı ve VPN 'nin istenen performansını denetler. Kullanım durumu için uygun SKU 'YU seçmek için [ağ GEÇIDI SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) listesine başvurun. VPN Gateway SKU 'SU daha sonra gerekirse değiştirilebilir.
- **Sanal ağ**: önceki adımda oluşturduğunuz sanal ağ.
- **Genel IP adresi**: internet 'e sunulacak VPN Gateway IP adresi. Büyük olasılıkla, yeni bir IP adresi oluşturmanız gerekecektir, ancak uygunsa mevcut bir kullanılmayan IP adresini de kullanabilirsiniz. Yeni bir IP adresi **oluşturmayı** seçerseniz, VPN Gateway aynı kaynak grubunda Azure kaynağı yeni bir IP adresi oluşturulur ve  **genel IP adresi adı** yeni oluşturulan IP adresinin adı olacaktır. **Mevcut olanı kullan**' ı seçerseniz, mevcut kullanılmayan IP adresini seçmeniz gerekir.
- **Etkin-etkin modu etkinleştir**: yalnızca etkin-etkin ağ geçidi yapılandırması oluşturuyorsanız **etkin** ' i seçin, aksi halde **devre dışı** bırakın. Etkin-etkin mod hakkında daha fazla bilgi edinmek için bkz. [yüksek oranda kullanılabilir şirket içi ve VNET 'Ten VNET 'e bağlantı](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **BGP ASN 'Yi yapılandırma**: yalnızca yapılandırmanızın bu ayarı gerektirmesi durumunda **etkin** ' i seçin. Bu ayar hakkında daha fazla bilgi edinmek için bkz. [Azure VPN Gateway Ile BGP hakkında](../../vpn-gateway/vpn-gateway-bgp-overview.md).

VPN Gateway oluşturmak için **gözden geçir + oluştur** ' u seçin. VPN Gateway, tam olarak oluşturulması ve dağıtılması 45 dakika sürebilir.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Şirket içi ağ geçidiniz için yerel ağ geçidi oluşturma 
Yerel ağ geçidi, şirket içi ağ gerecinizi temsil eden bir Azure kaynağıdır. Azure portal içindekiler tablosunda **Yeni kaynak oluştur** ' u seçin ve *yerel ağ geçidi* için arama yapın. Yerel ağ geçidi, depolama hesabınız, sanal ağınız ve VPN Gateway birlikte dağıtılacak bir Azure kaynağıdır, ancak depolama hesabı ile aynı kaynak grubunda veya abonelikte olması gerekmez. 

Yerel ağ geçidi kaynağını dağıtmak amacıyla, aşağıdaki alanları doldurmanız gerekir:

- **Ad**: yerel ağ geçidi için Azure kaynağının adı. Bu ad, yönetilebilmeniz için yararlı bulduğunuz herhangi bir ad olabilir.
- **IP adresi**: şirket içi yerel ağ geçidinizin genel IP adresi.
- **Adres alanı**: Bu yerel ağ geçidinin temsil ettiği ağ için adres aralıkları. Birden çok adres alanı aralığı ekleyebilirsiniz, ancak burada belirttiğiniz aralıkların, bağlanmak istediğiniz diğer ağların aralıklarıyla çakışmadığından emin olun. 
- **BGP ayarlarını yapılandırma**: yalnızca yapılandırma için bu ayar gerekiyorsa BGP ayarlarını yapılandırın. Bu ayar hakkında daha fazla bilgi edinmek için bkz. [Azure VPN Gateway Ile BGP hakkında](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Abonelik**: istenen abonelik. Bunun VPN Gateway veya depolama hesabı için kullanılan abonelikle eşleşmesi gerekmez.
- **Kaynak grubu**: istenen kaynak grubu. Bunun VPN Gateway veya depolama hesabı için kullanılan kaynak grubuyla eşleşmesi gerekmez.
- **Konum**: yerel ağ geçidi kaynağının oluşturulacağı Azure bölgesi. Bu, VPN Gateway ve depolama hesabı için seçtiğiniz bölgeyle eşleşmelidir.

Yerel ağ geçidi kaynağını oluşturmak için **Oluştur** ' u seçin.  

## <a name="configure-on-premises-network-appliance"></a>Şirket içi ağ gerecini yapılandırma
Şirket içi ağ gerecinizi yapılandırmanın belirli adımları, kuruluşunuzun seçtiği ağ gerecine bağlı olarak değişir. Kuruluşunuzun seçtiği cihaza bağlı olarak, [Sınanan cihazların listesi](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) , Azure VPN Gateway ile yapılandırma için cihaz satıcınızın yönergelerine giden bir bağlantıya sahip olabilir.

## <a name="create-the-site-to-site-connection"></a>Siteden siteye bağlantı oluşturma
Bir S2S VPN dağıtımını gerçekleştirmek için, şirket içi ağ gereciniz (yerel ağ geçidi kaynağıyla gösterilir) ve VPN Gateway arasında bir bağlantı oluşturmanız gerekir. Bunu yapmak için yukarıda oluşturduğunuz VPN Gateway gidin. VPN Gateway içindekiler tablosunda **Bağlantılar**' ı seçin ve **Ekle**' ye tıklayın. Elde edilen **bağlantı ekle** bölmesi aşağıdaki alanları gerektirir:

- **Ad**: bağlantının adı. Bir VPN Gateway birden çok bağlantıyı barındırabileceğinden, bu bağlantıyı ayırt edecek yönetimi için bir ad seçin.
- **Bağlantı türü**: Bu bir S2S bağlantısı olduğundan, açılan listeden **siteden siteye (IPSec)** öğesini seçin.
- **Sanal ağ geçidi**: Bu alan, bağlantıyı yaptığınız VPN Gateway için otomatik olarak seçilir ve değiştirilemez.
- **Yerel ağ geçidi**: bu, VPN Gateway bağlamak istediğiniz yerel ağ geçidindir. Sonuç seçim bölmesi, yukarıda oluşturduğunuz yerel ağ geçidinin adına sahip olmalıdır.
- **Paylaşılan anahtar (PSK)**: bağlantı için şifreleme oluşturmak için kullanılan harflerin ve sayıların karışımı. Aynı paylaşılan anahtarın hem sanal ağ hem de yerel ağ geçitlerinde kullanılması gerekir. Ağ Geçidi cihazınız bir sunmazsa, buradan bir tane oluşturabilir ve bunu cihazınıza sağlayabilirsiniz.

Bağlantıyı oluşturmak için **Tamam**'ı seçin. **Bağlantılar** sayfası aracılığıyla bağlantının başarıyla yapıldığını doğrulayabilirsiniz.

## <a name="mount-azure-file-share"></a>Azure dosya paylaşımından bağlama 
S2S VPN 'yi yapılandırmanın son adımı, Azure dosyaları için çalıştığını doğrulamakdır. Bunu, Azure dosya paylaşımınızı tercih ettiğiniz işletim sistemi ile şirket içi olarak bağlayarak yapabilirsiniz. İşletim sistemine göre bağlama için şu yönergelere bakın:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Ayrıca bkz.
- [Azure dosyaları ağlarına genel bakış](storage-files-networking-overview.md)
- [Azure dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-windows.md)
- [Linux üzerinde Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-linux.md)