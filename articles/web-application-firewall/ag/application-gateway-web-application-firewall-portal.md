---
title: 'Öğretici: Portal kullanarak oluşturma-Web uygulaması güvenlik duvarı'
description: Bu öğreticide, Azure portal kullanarak bir Web uygulaması güvenlik duvarı ile uygulama ağ geçidi oluşturmayı öğreneceksiniz.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: victorh
ms.openlocfilehash: 35bede052f06c0fcffe46460a376d10690fd4417
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559646"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak bir Web uygulaması güvenlik duvarı ile uygulama ağ geçidi oluşturma

Bu öğretici, bir Web uygulaması güvenlik duvarı (WAF) ile bir Application Gateway oluşturmak için Azure portal nasıl kullanacağınızı gösterir. WAF, uygulamanızı korumak için [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) kurallarını kullanır. Bu kurallar SQL ekleme, siteler arası betik saldırıları ve oturum ele geçirme gibi saldırılara karşı korumayı içerir. Uygulama ağ geçidini oluşturduktan sonra, doğru çalıştığından emin olmak için test edersiniz. Azure Application Gateway ile, bağlantı noktalarına dinleyici atayarak, kurallar oluşturarak ve bir arka uç havuzuna kaynak ekleyerek uygulama Web trafiğinizi belirli kaynaklara yönlendirebilirsiniz. Kolaylık sağlaması için bu öğreticide, genel ön uç IP 'si olan basit bir kurulum, bu uygulama ağ geçidinde tek bir siteyi barındırmak için temel bir dinleyici, arka uç havuzu için kullanılan iki sanal makine ve temel bir istek yönlendirme kuralı kullanılmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * WAF etkinken bir uygulama ağ geçidi oluşturma
> * Arka uç sunucuları olarak kullanılan sanal makineleri oluşturma
> * Bir depolama hesabı oluşturma ve tanılamaları yapılandırma
> * Uygulama ağ geçidini test etme

![Web uygulaması güvenlik duvarı örneği](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

1. Azure portal sol menüsünde **kaynak oluştur** ' u seçin. **Yeni** pencere görüntülenir.

2. **Ağ** ' ı seçin ve ardından **öne çıkan** listede **Application Gateway** ' yi seçin.

### <a name="basics-tab"></a>Temel bilgiler sekmesi

1. **Temel bilgiler** sekmesinde, aşağıdaki uygulama ağ geçidi ayarları için şu değerleri girin:

   - **Kaynak grubu**: kaynak grubu için **myResourceGroupAG** öğesini seçin. Yoksa, oluşturmak için **Yeni oluştur** ' u seçin.
   - **Uygulama ağ geçidi adı**: uygulama ağ geçidinin adı Için *myappgateway* girin.
   - **Katman**: **WAF v2**'yi seçin.

     ![Yeni uygulama ağ geçidi oluşturma: temel bilgiler](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır. Yeni bir sanal ağ oluşturabilir veya var olan bir ağı kullanabilirsiniz. Bu örnekte, uygulama ağ geçidini oluşturduğunuz sırada yeni bir sanal ağ oluşturacaksınız. Application Gateway örnekleri ayrı alt ağlarda oluşturulur. Bu örnekte iki alt ağ oluşturursunuz: bir tane uygulama ağ geçidi ve arka uç sunucuları için bir diğeri.

    **Sanal ağı Yapılandır** altında yeni bir sanal ağ oluşturmak Için **Yeni oluştur** ' u seçin. Açılan **sanal ağ oluştur** penceresinde, sanal ağ ve iki alt ağ oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: sanal ağın adı Için *myvnet* girin.

    - **Alt ağ adı** (Application Gateway alt ağ): **alt ağlar** Kılavuzu *varsayılan* olarak adlandırılan bir alt ağ gösterir. Bu alt ağın adını *Myagsubnet* olarak değiştirin.<br>Application Gateway alt ağı yalnızca uygulama ağ geçitleri içerebilir. Başka hiçbir kaynağa izin verilmez.

    - **Alt ağ adı** (arka uç sunucusu alt ağı): alt **ağlar** kılavuzunun Ikinci satırında, **alt ağ adı** sütununa *mybackendsubnet* yazın.

    - **Adres aralığı** (arka uç sunucusu alt ağı): **alt ağlar** kılavuzunun Ikinci satırına *myagsubnet* adres aralığıyla çakışmayacak bir adres aralığı girin. Örneğin, *Myagsubnet* adres aralığı 10.0.0.0/24 Ise, *Mybackendsubnet* adres aralığı için *10.0.1.0/24* girin.

    **Sanal ağ oluştur** penceresini kapatmak ve sanal ağ ayarlarını kaydetmek için **Tamam ' ı** seçin.

     ![Yeni uygulama ağ geçidi oluştur: sanal ağ](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. **Temel bilgiler** sekmesinde, diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ileri: ön uçlar**' ı seçin.

### <a name="frontends-tab"></a>Ön uçlar sekmesi

1. Ön **uçlar** sekmesinde, **ön uç IP adresi türünün** **genel** olarak ayarlandığını doğrulayın. <br>Ön uç IP 'sini kullanım çalışmanıza göre genel veya özel olacak şekilde yapılandırabilirsiniz. Bu örnekte, genel ön uç IP 'si seçersiniz.
   > [!NOTE]
   > Application Gateway v2 SKU 'SU için yalnızca **genel** ön uç IP yapılandırması ' nı seçebilirsiniz. Özel ön uç IP yapılandırması şu anda bu v2 SKU 'SU için etkin değil.

2. **Genel IP adresi** Için **Yeni Ekle** ' yı seçin ve genel IP adresi adı Için *myagpublicıpaddress* girin ve **Tamam**' ı seçin. 

     ![Yeni uygulama ağ geçidi oluştur: ön uçlar](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. **İleri ' yi seçin: Backenler**.

### <a name="backends-tab"></a>Backends sekmesi

Arka uç havuzu, isteği sunan arka uç sunucularına istekleri yönlendirmek için kullanılır. Arka uç havuzları, NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, uygulama ağ geçidiniz ile boş bir arka uç havuzu oluşturacak ve daha sonra arka uç havuzuna arka uç hedefleri ekleyeceğiz.

1. **Arka uç Havuzu Ekle**' yi seçin. 

2. Açılan **bir arka uç havuzu ekleyin** penceresinde, boş bir arka uç havuzu oluşturmak için aşağıdaki değerleri girin:

    - **Ad**: arka uç havuzunun adı Için *mybackendpool* girin.
    - **Hedefleri olmayan arka uç Havuzu Ekle**: hedefi olmayan bir arka uç havuzu oluşturmak için **Evet** ' i seçin. Uygulama ağ geçidini oluşturduktan sonra arka uç hedefleri ekleyeceksiniz.

3. Arka uç havuzu **Ekle** penceresinde, arka uç havuzu yapılandırmasını kaydetmek ve **backends** sekmesine dönmek için **Ekle** ' yi seçin.

     ![Yeni uygulama ağ geçidi oluştur: arka uçları](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. **Backends** sekmesinde **İleri: yapılandırma**' yı seçin.

### <a name="configuration-tab"></a>Yapılandırma sekmesi

**Yapılandırma** sekmesinde, bir yönlendirme kuralı kullanarak oluşturduğunuz ön uç ve arka uç havuzunu bağlayacaksınız.

1. **Yönlendirme kuralları** sütununda **bir yönlendirme kuralı ekle** ' yi seçin.

2. Açılan **yönlendirme kuralı ekle** penceresinde, **kural adı** için *myroutingrule* yazın.

3. Yönlendirme kuralı bir dinleyici gerektirir. **Yönlendirme kuralı ekle** penceresinin içindeki **dinleyici** sekmesinde, dinleyici için aşağıdaki değerleri girin:

    - **Dinleyici adı**: dinleyicinin adı Için *MyListener* girin.
    - **Ön uç IP**: ön uç için oluşturduğunuz ortak IP 'yi seçmek için **genel** ' i seçin.
  
      **Dinleyici** sekmesinde diğer ayarlar için varsayılan değerleri kabul edin, sonra yönlendirme kuralının geri kalanını yapılandırmak Için **arka uç hedefleri** sekmesini seçin.

   ![Yeni uygulama ağ geçidi oluşturma: dinleyici](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. **Arka uç hedefleri** sekmesinde, **arka uç hedefi** Için **mybackendpool** ' u seçin.

5. **Http ayarı** Için yeni **Ekle** ' yı seçerek yeni bir http ayarı oluşturun. HTTP ayarı, yönlendirme kuralının davranışını tespit eder. Açılan **http ayarı Ekle** penceresinde **http ayar adı** için *myhttpsetting* girin. **Http ayarı Ekle** penceresinde diğer ayarlar için varsayılan değerleri kabul edin ve ardından **Ekle** ' yi seçerek **yönlendirme kuralı ekle** penceresine dönün. 

     ![Yeni uygulama ağ geçidi oluştur: HTTP ayarı](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Yönlendirme kuralı **Ekle** penceresinde, yönlendirme kuralını kaydetmek ve **yapılandırma** sekmesine dönmek için **Ekle** ' yi seçin.

     ![Yeni uygulama ağ geçidi oluşturma: yönlendirme kuralı](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Ileri ' yi seçin **: Etiketler** ve sonra **İleri: İnceleme + oluştur**.

### <a name="review--create-tab"></a>Gözden geçir + Oluştur sekmesi

**Gözden geçir + oluştur** sekmesindeki ayarları gözden geçirin ve ardından **Oluştur** ' u seçerek sanal ağı, genel IP adresini ve uygulama ağ geçidini oluşturun. Azure 'da uygulama ağ geçidini oluşturmak birkaç dakika sürebilir. 

Bir sonraki bölüme geçmeden önce Dağıtım başarıyla bitene kadar bekleyin.

## <a name="add-backend-targets"></a>Arka uç hedefleri ekleme

Bu örnekte, sanal makineleri hedef arka uç olarak kullanacaksınız. Var olan sanal makineleri kullanabilir ya da yeni bir tane oluşturabilirsiniz. Azure 'un uygulama ağ geçidi için arka uç sunucusu olarak kullandığı iki sanal makine oluşturacaksınız.

Bunu yapmak için şunları yapmanız gerekir:

1. Arka uç sunucuları olarak kullanılmak üzere *Myvm* ve *myVM2* olmak üzere iki yeni VM oluşturun.
2. Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere IIS 'yi yükler.
3. Arka uç sunucularını arka uç havuzuna ekleyin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure portal **kaynak oluştur**' u seçin. **Yeni** pencere görüntülenir.
2. **Popüler** listede **Windows Server 2016 Datacenter** ' ı seçin. **Sanal makine oluştur** sayfası görüntülenir.<br>Application Gateway, arka uç havuzunda kullanılan herhangi bir sanal makine türüne trafiği yönlendirebilir. Bu örnekte, bir Windows Server 2016 Datacenter kullanırsınız.
3. Aşağıdaki sanal makine ayarları için **temel bilgiler** sekmesine bu değerleri girin:

    - **Kaynak grubu**: kaynak grubu adı için **myResourceGroupAG** öğesini seçin.
    - **Sanal makine adı**: sanal makinenin adı Için *myvm* girin.
    - **Kullanıcı** adı: Yönetici Kullanıcı adı için bir ad girin.
    - **Parola**: yönetici parolası için bir parola girin.
4. Diğer varsayılanları kabul edin ve ardından **İleri: diskler**' i seçin.  
5. **Diskler** sekmesi varsayılan değerlerini kabul edin ve ardından İleri ' yi seçin **: ağ**.
6. **Ağ** sekmesinde, **sanal ağ** için **Myvnet** öğesinin seçildiğini ve **alt ağın** **mybackendsubnet** olarak ayarlandığını doğrulayın. Diğer varsayılanları kabul edin ve ardından **İleri: yönetim**' i seçin.<br>Application Gateway, içinde bulunduğu sanal ağ dışındaki örneklerle iletişim kurabilir, ancak IP bağlantısı olduğundan emin olmanız gerekir.
7. **Yönetim** sekmesinde, **önyükleme tanılamayı** **devre dışı** olarak ayarlayın. Diğer varsayılanları kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
8. **Gözden geçir + oluştur** sekmesinde ayarları gözden geçirin, doğrulama hatalarını düzeltin ve ardından **Oluştur**' u seçin.
9. Devam etmeden önce sanal makine oluşturma işleminin tamamlanmasını bekleyin.

### <a name="install-iis-for-testing"></a>Test için IIS 'yi yükler

Bu örnekte, yalnızca Azure 'un Application Gateway 'i başarıyla oluşturduğunu doğrulamak için sanal makinelere IIS yüklersiniz.

1. [Azure PowerShell](../../cloud-shell/quickstart-powershell.md)açın. Bunu yapmak için, Azure portal üst gezinti çubuğundan **Cloud Shell** ' ı seçin ve ardından açılır listeden **PowerShell** ' i seçin. 

    ![Özel uzantıyı yükleme](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Ortamınız için location parametresini ayarlayın ve ardından aşağıdaki komutu çalıştırarak IIS 'yi sanal makineye yükleyebilirsiniz: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. İkinci bir sanal makine oluşturun ve daha önce tamamladığınız adımları kullanarak IIS 'yi yükleyebilirsiniz. Sanal makine adı ve **set-Azvmexgeri** cmdlet 'Inin **VMName** ayarı için *myVM2* kullanın.

### <a name="add-backend-servers-to-backend-pool"></a>Arka uç sunucularını arka uç havuzuna Ekle

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol menüden **arka uç havuzları** ' nı seçin.

3. **Mybackendpool** öğesini seçin.

4. **Hedef türü** altında, açılan listeden **sanal makine** ' yi seçin.

5. **Hedef** altında, açılan listeden **myvm** için ilişkili ağ arabirimini seçin.
1. **MyVM2** için yineleyin.

   :::image type="content" source="../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png" alt-text="Arka uç sunucuları ekleme":::


6. **Kaydet**’i seçin.

7. Sonraki adıma geçmeden önce dağıtımın tamamlanmasını bekleyin.

   
## <a name="create-and-link-a-web-application-firewall-policy"></a>Web uygulaması güvenlik duvarı ilkesi oluşturma ve bağlama

WAF özelleştirmeleri ve ayarları, WAF Ilkesi olarak adlandırılan ayrı bir nesnedir. İlkenin Application Gateway ilişkilendirilmesi gerekir. 

Yönetilen bir varsayılan kural kümesi (DRS) ile temel bir WAF ilkesi oluşturun.

1. Portalın sol üst kısmında **kaynak oluştur**' u seçin. **WAF** araması yapın, **Web uygulaması güvenlik duvarı**' nı seçin ve **Oluştur**' u seçin.
2. **BIR WAF Ilkesi oluştur** sayfasında, **temel** bilgiler sekmesinde, aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin:

   |Ayar  |Değer  |
   |---------|---------|
   |İlke     |Bölgesel WAF (Application Gateway)|
   |Abonelik     |Abonelik adınızı seçin|
   |Kaynak grubu     |**MyResourceGroupAG** seçin|
   |İlke adı     |WAF ilkeniz için benzersiz bir ad yazın.|
1. **İleri ' yi seçin: ilke ayarları**.
1. Varsayılanları kabul edin ve ardından Ileri ' yi seçin **: yönetilen kurallar**.
1. Varsayılanı kabul edin ve ardından **İleri: özel kurallar**' ı seçin.
1. **İleri: ilişkilendirme** öğesini seçin.
1. **Ilişki Ekle** ' yi ve ardından **Application Gateway**' yi seçin.
1. **Web uygulaması güvenlik duvarı ilkesi yapılandırmasını geçerli yapılandırmadan farklı olsa da Uygula** onay kutusunu seçin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. **İlişkilendirme** sekmesinde **ilişkilendirme Ekle**' yi seçin ve **Application Gateway**' ı seçin.

   > [!NOTE]
   > Zaten bir ilkeye sahip olan Application Gateway (veya dinleyiciye) ilke atarsanız, özgün ilkenin üzerine yazılır ve yeni ilke konur.
4. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin.
1. Şunu seçin: **İleri: Etiketler**.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidi oluşturmak için IIS gerekli olmasa da, Azure 'un uygulama ağ geçidini başarıyla oluşturup oluşturmadığını doğrulamak için bu uygulamayı yüklediniz. Uygulama ağ geçidini test etmek için IIS kullanın:

1. Uygulama ağ geçidinin genel IP adresini **genel bakış** sayfasında bulabilirsiniz. ![ Uygulama Ağ Geçidi genel IP adresini Kaydet](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Ya da, **tüm kaynaklar**' ı seçip Arama kutusuna *Myagpublicıpaddress* girebilir ve arama sonuçlarında bunu seçebilirsiniz. Azure **genel bakış** SAYFASıNDA genel IP adresini görüntüler.
1. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.
1. Yanıtı denetleyin. Geçerli bir yanıt, uygulama ağ geçidinin başarıyla oluşturulduğunu ve arka uca başarıyla bağlanabildiğini doğrular.

   ![Uygulama ağ geçidini test etme](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulama ağ geçidiyle oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu kaldırın. Kaynak grubunu kaldırarak, uygulama ağ geçidini ve ilgili tüm kaynakları da kaldırırsınız. 

Kaynak grubunu kaldırmak için:

1. Azure portal sol menüsünde **kaynak grupları**' nı seçin.
2. **Kaynak grupları** sayfasında, listede **myResourceGroupAG** araması yapın ve ardından seçin.
3. **Kaynak grubu sayfasında**, **kaynak grubunu sil**' i seçin.
4. **Kaynak grubu adını yazmak** için *MyResourceGroupAG* girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Web uygulaması güvenlik duvarı hakkında daha fazla bilgi edinin](../overview.md)