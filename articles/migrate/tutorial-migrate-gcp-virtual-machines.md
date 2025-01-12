---
title: Google Cloud Platform (GCP) sanal makine örneklerini bulun, değerlendirin ve Azure 'a geçirin
description: Bu makalede, Azure geçişi ile GCP VM 'lerinin Azure 'a nasıl geçirileceği açıklanır.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 12b8e30b0107b6b008cbd6467ada7c2d44f5e6d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98871646"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Google Cloud Platform (GCP) sanal makinelerini Azure 'a bulma, değerlendirme ve geçirme

Bu öğreticide, Azure geçişi: Sunucu değerlendirmesi ve Azure geçişi: sunucu geçiş araçları aracılığıyla Google Cloud Platform (GCP) sanal makinelerini (VM) Azure VM 'lerine bulma, değerlendirme ve geçirme işlemlerinin nasıl yapılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
>
> * Geçiş için önkoşulları doğrulayın.
> * Azure geçişi ile Azure kaynakları hazırlama: sunucu geçişi. Azure hesabınız ve kaynaklarınızın Azure geçişi ile çalışması için izinleri ayarlayın.
> * Geçiş için GCP VM örnekleri hazırlayın.
> * Azure geçişi hub 'ına Azure geçişi: sunucu geçiş aracı 'nı ekleyin.
> * Çoğaltma gereci ayarlama ve yapılandırma sunucusunu dağıtma.
> * Taşımak istediğiniz GCP VM 'lerine Mobility hizmetini yükleyebilirsiniz.
> * Sanal makineler için çoğaltmayı etkinleştirme.
> * Çoğaltma durumunu izleyin ve izleyin. 
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Azure 'a tam geçiş gerçekleştirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="discover-and-assess"></a>Bulma ve değerlendirme

Azure 'a geçirmeden önce, bir VM bulma ve geçiş değerlendirmesi gerçekleştirmenizi öneririz. Bu değerlendirme, GCP sanal makinelerinizin Azure 'a geçiş için doğru boyutlandırmanıza ve potansiyel Azure çalışma maliyetlerini tahmin etmesine yardımcı olur.

Bir değerlendirmeyi aşağıdaki şekilde ayarlayın:

1. Azure 'u ayarlama ve GCP sanal makinelerinizi bir değerlendirme için hazırlama [öğreticisini](./tutorial-discover-gcp.md) izleyin. Şunlara dikkat edin:

    - Azure geçişi, GCP VM örnekleri bulunurken parola kimlik doğrulamasını kullanır. GCP örnekleri parola kimlik doğrulamasını varsayılan olarak desteklemez. Keşfedebilmeniz için önce parola kimlik doğrulamasını etkinleştirmeniz gerekir.
        - Windows makineleri için WinRM bağlantı noktası 5985 (HTTP) izin verin. Bu, uzak WMI çağrılarına izin verir.
        - Linux makineleri için:
            1. Her bir Linux makinesinde oturum açın.
            2. Sshd_config dosyasını açın: VI/etc/ssh/sshd_config
            3. Dosyasında, **Passwordaduthentication** satırını bulun ve değeri **Evet** olarak değiştirin.
            4. Dosyayı kaydedin ve kapatın. SSH hizmetini yeniden başlatın.
    - Linux sanal makinelerinizi keşfettiğiniz bir kök Kullanıcı kullanıyorsanız, VM 'lerde kök oturum açmaya izin verildiğinden emin olun.
        1. Her Linux makinesinde oturum açın
        2. Sshd_config dosyasını açın: VI/etc/ssh/sshd_config
        3. Dosyasında, **PermitRootLogin** satırını bulun ve değeri **Evet** olarak değiştirin.
        4. Dosyayı kaydedin ve kapatın. SSH hizmetini yeniden başlatın.

2. Ardından, GCP sanal makinelerinizi bulup değerlendirmek üzere bir Azure geçişi projesi ve gereci ayarlamak için bu [öğreticiyi](./tutorial-assess-gcp.md) izleyin.

Bir değerlendirmeyi denemenizi öneririz, ancak değerlendirme gerçekleştirmek VM 'Leri geçirebilmek için zorunlu bir adım değildir.



## <a name="prerequisites"></a>Önkoşullar 

- Geçirmek istediğiniz GCP VM 'lerinin desteklenen bir işletim sistemi sürümünü çalıştırdığından emin olun. GCP VM 'Leri geçiş amacıyla fiziksel makineler gibi değerlendirilir. Fiziksel sunucu geçişi iş akışı için [desteklenen işletim sistemlerini ve çekirdek sürümlerini](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) gözden geçirin. Linux sanal makinelerinize yönelik işletim sistemi ve çekirdek sürümlerini denetlemek için *hostnamectl* veya *uname-a* gibi standart komutları kullanabilirsiniz.  Gerçek geçişe devam etmeden önce VM 'nin beklendiği gibi çalışıp çalışmadığını doğrulamak için bir test geçişi gerçekleştirmenizi öneririz.
- GCP sanal makinelerinizin Azure 'a geçiş için [desteklenen yapılandırmalara](./migrate-support-matrix-physical-migration.md#physical-server-requirements) uyduğundan emin olun.
- Azure 'a çoğaltılan GCP VM 'lerinin [Azure VM gereksinimleriyle](./migrate-support-matrix-physical-migration.md#azure-vm-requirements) uyumlu olduğunu doğrulayın.
- Azure 'a geçirmeden önce VM 'lerde gereken bazı değişiklikler vardır.
    - Bazı işletim sistemleri için Azure geçişi bu değişiklikleri otomatik olarak yapar.
    - Geçişe başlamadan önce bu değişiklikleri yapmak önemlidir. Değişikliği yapmadan önce VM 'yi geçirirseniz, VM Azure 'da önyüklenemeyebilir.
Yapmanız gereken [Windows](prepare-for-migration.md#windows-machines) ve [Linux](prepare-for-migration.md#linux-machines) değişikliklerini gözden geçirin.

### <a name="prepare-azure-resources-for-migration"></a>Azure kaynaklarını geçiş için hazırlama

Azure geçişi: sunucu geçiş aracı ile geçiş için Azure 'u hazırlayın.

**Görev** | **Ayrıntılar**
--- | ---
**Azure Geçişi projesi oluşturma** | [Yeni bir proje oluşturmak](./create-manage-projects.md)için Azure hesabınızda katkıda bulunan veya sahip izinlerinin olması gerekir.
**Azure hesabınız için izinleri doğrulama** | Azure hesabınızın bir VM oluşturmak ve Azure yönetilen diskine yazmak için izinleri olması gerekir.

### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama

1. Azure portalında ilgili aboneliği açın ve **Erişim denetimi (IAM)** öğesini seçin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz.
    - Aboneliğin sahibi siz değilseniz sahibiyle iletişime geçerek gerekli rolün atanmasını sağlayın.

### <a name="assign-azure-account-permissions"></a>Azure hesabı izinleri atama

Sanal makine katılımcısı rolünü Azure hesabına atayın. Bu izinler şunları sağlar:

- Seçilen kaynak grubunda sanal makine oluşturma.
- Seçilen sanal ağda sanal makine oluşturma.
- Azure yönetilen diskine yazın. 

### <a name="create-an-azure-network"></a>Azure ağı oluşturma

Bir Azure sanal ağı (VNet) [ayarlayın](../virtual-network/manage-virtual-network.md#create-a-virtual-network) . Azure 'a çoğaltma yaptığınızda oluşturulan Azure VM 'Ler, geçişi ayarlarken belirttiğiniz Azure VNet 'e birleştirilir.

## <a name="prepare-gcp-instances-for-migration"></a>Geçiş için GCP örneklerini hazırlama

GCP 'yi Azure 'a geçişe hazırlamak için, geçiş için bir çoğaltma gereci hazırlamanız ve dağıtmanız gerekir.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Çoğaltma gereci için bir makine hazırlama

Azure geçişi: sunucu geçişi, makineleri Azure 'a çoğaltmak için bir çoğaltma gereci kullanır. Çoğaltma gereci aşağıdaki bileşenleri çalıştırır.

- **Yapılandırma sunucusu**: yapılandırma sunucusu, GCP VM 'Leri ile Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.
- **İşlem sunucusu**: işlem sunucusu bir çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alır, önbelleğe alma, sıkıştırma ve şifreleme ile iyileştirir ve Azure 'da bir önbellek depolama hesabına gönderir.

Gereç dağıtımı için aşağıdaki gibi hazırlanın:

- Çoğaltma gerecini barındırmak için ayrı bir GCP sanal makinesi ayarlayın. Bu örnek, Windows Server 2012 R2 veya Windows Server 2016 çalıştırıyor olmalıdır. Gereç için donanım, yazılım ve ağ gereksinimlerini [gözden geçirin](./migrate-replication-appliance.md#appliance-requirements) .
- Gereç, çoğaltmak istediğiniz bir kaynak sanal makineye veya daha önce yüklemiş olduğunuz Azure geçişi bulma ve değerlendirme gerecine yüklenmelidir. Farklı bir VM üzerinde dağıtılmalıdır.
- Geçirilecek kaynak GCP VM 'lerinin, çoğaltma gerecine bir ağ hattı olması gerekir. Bunu etkinleştirmek için gerekli güvenlik duvarı kurallarını yapılandırın. Çoğaltma gerecinin geçirilecek kaynak VM 'Lerle aynı VPC ağında dağıtılması önerilir. Çoğaltma gerecinin farklı bir VPC 'de olması gerekiyorsa, VPCs 'nin VPC eşlemesi aracılığıyla bağlanması gerekir.
- Kaynak GCP VM 'Leri, çoğaltma yönetimi ve çoğaltma veri aktarımı için gelen, HTTPS 443 (denetim kanalı düzenleme) ve TCP 9443 (veri aktarımı) bağlantı noktalarında çoğaltma gereci ile iletişim kurar. ' Deki çoğaltma gereci, Azure 'da HTTPS 443 giden bağlantı noktası üzerinden çoğaltma verilerini düzenler ve Azure 'a gönderir. Bu kuralları yapılandırmak için, güvenlik grubu gelen/giden kurallarını uygun bağlantı noktalarıyla ve kaynak IP bilgileriyle düzenleyin.

   ![GCP güvenlik duvarı kuralları](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Güvenlik duvarı kurallarını Düzenle](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- Çoğaltma gereci MySQL kullanır. Gereçte MySQL yükleme [seçeneklerini](migrate-replication-appliance.md#mysql-installation) gözden geçirin.
- Çoğaltma gerecinin [ortak](migrate-replication-appliance.md#url-access) ve [kamu](migrate-replication-appliance.md#azure-government-url-access) bulutları 'Na erişmesi Için gereken Azure URL 'lerini gözden geçirin.

## <a name="set-up-the-replication-appliance"></a>Çoğaltma gereç ayarı

Geçişin ilk adımı, çoğaltma gerecini ayarlamaya yönelik. GCP VM 'Leri geçişi için gereci ayarlamak üzere gereç için yükleyici dosyasını indirmeniz ve ardından bunu [hazırladığınız sanal](#prepare-a-machine-for-the-replication-appliance)makinede çalıştırmanız gerekir.

### <a name="download-the-replication-appliance-installer"></a>Çoğaltma gereç yükleyicisini indirin

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **bul**' a tıklayın.

    ![VM'leri bulma](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. Makinelerde **bulunan makineler**  >  **sanallaştırılmış mi?**, **sanallaştırılmamış/diğer**' e tıklayın.
3. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin.
4. **Geçiş için hedef bölgenin <bölge adı>olduğunu onaylayın**' i seçin.
5. **Kaynak oluştur**' a tıklayın. Bu, arka planda bir Azure Site Recovery Kasası oluşturur.
    - Azure geçişi sunucu geçişi ile geçiş zaten ayarladıysanız, kaynaklar daha önce ayarlandığı için hedef seçenek yapılandırılamaz.
    - Bu düğmeye tıkladıktan sonra bu proje için hedef bölgeyi değiştiremezsiniz.
    - Sanal makinelerinizi farklı bir bölgeye geçirmek için yeni/farklı bir Azure geçişi projesi oluşturmanız gerekir.

6. **Yeni bir çoğaltma gereci yüklemek** istiyor musunuz?, **çoğaltma gereci yüklensin**' i seçin.
7. **Çoğaltma gereci yazılımını indirip yükleyin** bölümünde gereç yükleyicisini ve kayıt anahtarını indirin. Gereci kaydettirmek için anahtar gerekir. Anahtar indirildikten beş gün sonra geçerlidir.

    ![Sağlayıcıyı indir](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Gereç kurulum dosyasını ve anahtar dosyasını, çoğaltma gereci için oluşturduğunuz Windows Server 2016 veya Windows Server 2012 GCP VM 'ye kopyalayın.
9. Sonraki yordamda açıklandığı gibi, çoğaltma gereç kurulum dosyasını çalıştırın.  
    9.1. **Başlamadan Önce** bölümünde **Yapılandırma sunucusunu ve işlem sunucusunu yükleme**’yi seçin ve ardından **İleri**’yi seçin.   
    9,2 **üçüncü taraf yazılım lisansında**, **üçüncü taraf lisans sözleşmesini kabul ediyorum**' u seçin ve ardından **İleri**' yi seçin.   
    9,3 **kayıt** sırasında, git **' i seçin ve** ardından kasa kayıt anahtarı dosyasını yerleştirdiğiniz yere gidin. **İleri**’yi seçin.  
    9,4 **Internet ayarları**'nda, **proxy sunucusu olmadan Azure Site Recovery Bağlan**' ı seçin ve ardından **İleri**' yi seçin.  
    9,5 **Önkoşul denetimi** sayfası, birkaç öğe için Denetim çalıştırır. Tamamlandığında, **İleri**’yi seçin.  
    **MySQL yapılandırmasında** 9,6 MySQL db için bir parola girin ve ardından **İleri**' yi seçin.  
    9,7 **ortam ayrıntılarında** **Hayır**' ı seçin. VM 'lerinizi korumanız gerekmez. Ardından **İleri**' yi seçin.  
    9,8 **Install location** bölümünde, Varsayılanı kabul etmek için **İleri** ' yi seçin.  
    9,9 **ağ seçiminde**, Varsayılanı kabul etmek için **İleri** ' yi seçin.  
    9,10 **Özet** bölümünde, **yüklensin**' i seçin.   
    9,11 **Yükleme ilerlemesi** , yükleme işlemi hakkındaki bilgileri gösterir. Tamamlandığında, **Bitir**’i seçin. Bir pencere sistemin yeniden başlatılması hakkında bir ileti görüntüler. **Tamam**’ı seçin.   
    9,12 sonra, bir pencerede yapılandırma sunucusu bağlantı parolası hakkında bir ileti görüntülenir. Parolayı panonuza kopyalayın ve kaynak VM 'lerdeki geçici bir metin dosyasına kaydedin. Bu parola, Mobility hizmeti yükleme işlemi sırasında daha sonra gerekecektir.
10. Yükleme tamamlandıktan sonra gereç Yapılandırma Sihirbazı otomatik olarak başlatılır (gerecin masaüstünde oluşturulan Cspsconfigtool kısayolunu kullanarak Sihirbazı el ile de başlatabilirsiniz). Mobility hizmetinin göndererek yüklenmesi için kullanılacak hesap ayrıntılarını eklemek için sihirbazın hesapları Yönet sekmesini kullanın. Bu öğreticide, çoğaltılacak kaynak VM 'Lere Mobility hizmetini el ile yükleyeceğiz, bu nedenle bu adımda bir kukla hesap oluşturun ve devam edin. "Konuk" adlı kukla hesabı, Kullanıcı adı olarak "Kullanıcı adı" ve hesabın parolası olarak "parola" olarak oluşturmak için aşağıdaki ayrıntıları sağlayabilirsiniz. Çoğaltmayı etkinleştir aşamasında bu kukla hesabı kullanacaksınız. 
11. Kurulum sonrasında gereç yeniden başlatıldıktan sonra, **makine bul**' da, **yapılandırma sunucusu**' nda yeni gereç ' ı seçin ve **kaydı Sonlandır**' a tıklayın. Kayıt işlemini sonuçlandırma, çoğaltma gerecini hazırlamak için birkaç son görevi gerçekleştirir.

    ![Kaydı Sonlandır](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Mobility hizmetini yükleme

Geçirilecek kaynak GCP sanal makinelerinde Mobility hizmeti aracısının yüklü olması gerekir. Aracı yükleyicileri, çoğaltma aracısında kullanılabilir. Doğru yükleyiciyi bulur ve geçirmek istediğiniz her makineye aracıyı yüklersiniz. Aşağıdaki gibi yapın:

1. Çoğaltma gereci 'nda oturum açın.
2. **%ProgramData%\asr\home\svsystems\pushınstallsvc\repository dizinine** gidin.
3. Kaynak GCP VM 'Leri işletim sistemi ve sürümü için yükleyiciyi bulun. [Desteklenen işletim sistemlerini](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)gözden geçirin.
4. Yükleyici dosyasını geçirmek istediğiniz kaynak GCP sanal makinesine kopyalayın.
5. Çoğaltma gereci yüklerken oluşturulmuş olan kaydedilmiş parola metin dosyasına sahip olduğunuzdan emin olun.
    - Parolayı kaydetmeyi unuttuysanız, bu adımla çoğaltma gereci üzerinde parolayı görüntüleyebilirsiniz. Geçerli parolayı görüntülemek için komut satırından **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** ' yi çalıştırın.
    - Şimdi bu parolayı panonuza kopyalayın ve kaynak VM 'lerde geçici bir metin dosyasına kaydedin.

### <a name="installation-guide-for-windows-gcp-vms"></a>Windows GCP VM 'Leri için Yükleme Kılavuzu

1. Yükleyici dosyasının içeriğini GCP sanal makinesi üzerindeki yerel bir klasöre (örneğin, C:\Temp) ayıklayın, aşağıdaki gibi:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Mobility hizmeti yükleyicisini çalıştırın:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Aracıyı çoğaltma gereci ile kaydedin:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-gcp-vms"></a>Linux GCP VM 'Leri için Yükleme Kılavuzu

1. Yükleyici tarbol içeriğini, GCP VM 'sinde aşağıdaki gibi bir yerel klasöre (örneğin,/tmp/mobsvcınstaller) ayıklayın:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Yükleyici betiğini çalıştırın:
    ```
    sudo ./install -r MS -q
    ```  

3. Aracıyı çoğaltma gereci ile kaydedin:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-gcp-vms"></a>GCP VM 'Leri için çoğaltmayı etkinleştirme

> [!NOTE]
> Portal aracılığıyla, çoğaltma için aynı anda en fazla 10 VM ekleyebilirsiniz. Aynı anda daha fazla VM çoğaltmak için, bunları 10 toplu iş olarak ekleyebilirsiniz.

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **Çoğalt**' a tıklayın.

    ![Sanal makineleri çoğaltma](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. **Çoğaltma**' da, makinelerinizde > **kaynak ayarları**  >  **sanallaştırılır mi?**, **sanallaştırılmamış/diğer** seçeneğini belirleyin.
3. **Şirket içi gereç** bölümünde, ayarladığınız Azure geçiş gerecinin adını seçin.
4. **Işlem sunucusu**' nda, çoğaltma gerecinin adını seçin. 
5. **Konuk kimlik bilgileri**' nde, Mobility hizmetini el ile yüklemek için lütfen daha önce [çoğaltma yükleyicisi kurulumu](#download-the-replication-appliance-installer) sırasında oluşturulan kukla hesabı seçin (gönderme yüklemesi desteklenmez). Ileri ' ye tıklayın **: sanal makineler**.   
 
    ![Çoğaltma ayarları](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. **Sanal makinelerde** **geçiş ayarlarını bir değerlendirmede içeri aktar** bölümünde, varsayılan ayar Hayır olarak kalsın **, geçiş ayarlarını el ile belirteceğiz**.
7. Geçirmek istediğiniz her VM 'yi denetleyin. Ardından Ileri ' ye tıklayın **: hedef ayarlar**.

    ![VM 'Leri seçin](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. **Hedef ayarları**’nda aboneliği ve geçiş yapacağınız hedef bölgeyi seçin. Daha sonra Azure VM’lerinin geçişten sonra bulunacağı kaynak grubunu belirtin.
9. **Sanal Ağ**’da Azure VM’lerinin geçişten sonra katılacağı Azure sanal ağını/alt ağını seçin.
10. **Kullanılabilirlik seçenekleri**' nde şunları seçin:
    -  Bölge içindeki belirli bir kullanılabilirlik bölgesine geçirilen makineyi sabitlemek için kullanılabilirlik alanı. Kullanılabilirlik Alanları arasında çok düğümlü bir uygulama katmanı oluşturan sunucuları dağıtmak için bu seçeneği kullanın. Bu seçeneği belirlerseniz, Işlem sekmesinde seçilen makinenin her biri için kullanılacak kullanılabilirlik alanını belirtmeniz gerekir. Bu seçenek yalnızca geçiş için seçilen hedef bölge Kullanılabilirlik Alanları destekliyorsa kullanılabilir
    -  Geçirilen makinenin bir kullanılabilirlik kümesine yerleştirileceği kullanılabilirlik kümesi. Bu seçeneği kullanabilmek için seçilen hedef kaynak grubunun bir veya daha fazla kullanılabilirlik kümesi olmalıdır.
    - Geçirilen makineler için bu kullanılabilirlik yapılandırmalarının herhangi birine ihtiyacınız yoksa, altyapı artıklığı gerekli değildir.
11. **Disk şifreleme türü**' nde şunları seçin:
    - Platform tarafından yönetilen anahtar ile bekleyen şifreleme
    - Müşteri tarafından yönetilen anahtarla bekleyen şifreleme
    - Platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla Çift şifreleme

   > [!NOTE]
   > VM 'Leri CMK ile çoğaltmak için hedef kaynak grubu altında [bir disk şifreleme kümesi oluşturmanız](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) gerekir. Bir disk şifreleme kümesi nesnesi yönetilen diskleri, SSE için kullanılacak CMK 'yi içeren bir Key Vault eşler.
  
12. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-vmware/target-settings.png)

13. **İşlem** bölümünde VM adı, boyutu, işletim sistemi disk türü ve kullanılabilirlik yapılandırmasını (önceki adımda seçildiyse) gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-physical-migration.md#azure-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: değerlendirme önerilerini KULLANıYORSANıZ, VM boyutu açılan listesi önerilen boyutu gösterir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu**’nda el ile bir boyut seçin.
    - **Işletim sistemi diski**: VM için işletim sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir.
    - **Kullanılabilirlik alanı**: kullanılacak kullanılabilirlik bölgesini belirtin.
    - **Kullanılabilirlik kümesi**: kullanılacak kullanılabilirlik kümesini belirtin.

![İşlem ayarları](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. **Diskler**' de, VM disklerinin Azure 'da çoğaltılıp çoğaltılmayacağını belirtin ve Azure 'da disk türünü (Standart SSD/HDD veya Premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

    ![Disk ayarları](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma ayarlarını, çoğaltma başlamadan önce dilediğiniz zaman güncelleştirebilirsiniz,   >  **çoğaltılan makineleri** yönetin. Çoğaltma başladıktan sonra ayarlar değiştirilemez.

## <a name="track-and-monitor-replication-status"></a>Çoğaltma durumunu izleme ve izleme

- **Çoğalt** ' a tıkladığınızda çoğaltma Başlat işi başlar.
- Çoğaltma Başlat işi başarıyla tamamlandığında, VM 'Ler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. GCP VM disklerinde artımlı değişiklikler düzenli aralıklarla Azure 'daki çoğaltma disklerine çoğaltılır.

Portal bildirimlerinde iş durumunu izleyebilirsiniz.

Çoğaltma durumunu, **Azure geçişi: sunucu geçişi**' nde **sunucuları** çoğaltma ' ya tıklayarak izleyebilirsiniz.  

![Çoğaltmayı izleme](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma

Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Test geçişi kesinlikle önerilir ve olası sorunları bulmaya ve gerçek geçişe devam etmeden önce bunları gidermeye yönelik bir fırsat sağlar. Bunu geçirmeden önce her VM için en az bir kez yapmanız önerilir.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem çalışır durumda kalır ve çoğaltılmaya devam eder.
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:

1. **Geçiş hedefleri**  >  **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde **geçirilen sunucuları test et**' e tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-gcp-vms"></a>GCP VM 'lerini geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra, GCP sanal makinelerini geçirebilirsiniz.

1. Azure geçişi proje > **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde, **sunucuları çoğaltma**' ya tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. **Makineleri çoğaltma** bölümünde VM > **Geçir** üzerine sağ tıklayın.
3.   >  **Sanal makineleri Kapat ' a geçiş yapın ve veri kaybı olmadan planlı bir geçiş gerçekleştirin**, **Evet**  >  **Tamam**' ı seçin.
    - VM 'yi kapatmak istemiyorsanız **Hayır**' ı seçin.
4. VM için bir geçiş işlemi başlar. Portalın sağ üst köşesindeki bildirim zil simgesine tıklayarak veya sunucu geçiş aracının işler sayfasına giderek (araç kutucu> ğunda genel bakış ' a tıklayarak (sol menüden Işler ' i seçerek) iş durumunu görüntüleyebilirsiniz.
5. İşlem bittikten sonra Sanal Makineler sayfasında VM’yi görüntüleyebilir ve yönetebilirsiniz.

### <a name="complete-the-migration"></a>Geçişi tamamlamayı

1. Geçiş yapıldıktan sonra, **geçişi durdurmak**> VM 'ye sağ tıklayın. Bu, şunları yapar:
    - GCP sanal makinesi için çoğaltmayı durduruyor.
    - Azure geçişi: sunucu geçişi içindeki **çoğaltma sunucusu** sayısından GCP VM 'yi kaldırır.
    - VM için çoğaltma durumu bilgilerini temizler.
2. Geçirilen makinelere Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) veya [Linux](../virtual-machines/extensions/agent-linux.md) Aracısı 'nı yükler.
3. Veritabanı bağlantısı dizelerini ve web sunucusu yapılandırmalarını güncelleştirme gibi herhangi bir geçiş sonrası uygulama ayarı gerçekleştirin.
4. Geçirilen uygulamada son uygulama ve geçiş kabul testi gerçekleştirme işlemi şimdi Azure’da çalıştırılmaktadır.
5. Geçirilen Azure VM örneğine giden trafiği kesin.
6. Azure sanal makinelerinin yeni konumunu ve IP adresini göstermek için herhangi bir iç belgeyi güncelleştirin. 




## <a name="post-migration-best-practices"></a>Geçiş sonrası en iyi uygulamalar

- Daha fazla esneklik için:
    - Azure Backup hizmetini kullanarak Azure sanal makinelerini yedekleyip verileri güvende tutun. [Daha fazla bilgi edinin](../backup/quick-backup-vm-portal.md).
    - Site Recovery ile Azure sanal makinelerini ikincil bölgeye çoğaltarak iş yüklerinin çalışmaya devam etmesini ve sürekli kullanılabilir olmasını sağlayın. [Daha fazla bilgi edinin](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](../security-center/security-center-just-in-time.md).
    - [Ağ Güvenlik Grupları](../virtual-network/network-security-groups-overview.md) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
    - [Azure Maliyet Yönetimi](../cost-management-billing/cloudyn/overview.md)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.



## <a name="troubleshooting--tips"></a>Sorun giderme/Ipuçları

**Soru:** Geçiş için bulunan sunucular listesinde GCP sanal makinesini göremiyorum   
**Cevap:** Çoğaltma gerecinizin gereksinimleri karşılayıp karşılamadığını denetleyin. Geçirilecek kaynak VM 'de Mobility aracısının yüklü olduğundan ve yapılandırma sunucusu 'nun kaydedildiğinden emin olun. Çoğaltma gereci ve kaynak GCP VM 'Leri arasında bir ağ yolunu etkinleştirmek için güvenlik duvarı kurallarını denetleyin.  

**Soru:** Nasıl yaparım? VM 'nin başarıyla geçirilip geçirilmediğini öğrenin   
**Cevap:** Geçiş sonrası, sanal makineler sayfasından VM 'yi görüntüleyebilir ve yönetebilirsiniz. Doğrulamak için geçirilen VM 'ye bağlanın.  

**Soru:** Daha önce oluşturulmuş sunucu değerlendirmesi sonuçlarım 'dan geçiş için VM 'Leri içeri aktaramıyorum   
**Cevap:** Şu anda bu iş akışı için değerlendirme içeri aktarmayı desteklemiyoruz. Geçici bir çözüm olarak, değerlendirmeyi dışarı aktarabilir ve ardından çoğaltmayı etkinleştir adımı sırasında VM önerisini el ile seçebilirsiniz.
  
**Soru:** GCP VM 'lerimi bulmaya çalışırken "BIOS GUID 'SI getirilemedi" hatasını alıyorum   
**Cevap:** Kimlik doğrulaması için kök oturum açma kullanın, herhangi bir sözde Kullanıcı değil. Kök Kullanıcı kullanmıyorsanız, [destek matrisinde](migrate-support-matrix-physical.md#physical-server-requirements)belirtilen yönergelere göre Kullanıcı üzerinde gerekli yetenekler ayarlandığından emin olun. Ayrıca, GCP VM 'Leri için desteklenen işletim sistemlerini gözden geçirin.  

**Soru:** Çoğaltma durumum ilerlemiyor   
**Cevap:** Çoğaltma gerecinizin gereksinimleri karşılayıp karşılamadığını denetleyin. Çoğaltma gereci TCP bağlantı noktası 9443 ve HTTPS 443 veri aktarımı için gerekli bağlantı noktalarını etkinleştirdiğinizden emin olun. Aynı projeye bağlı çoğaltma gerecinin eski bir sürümü olmadığından emin olun.   

**Soru:** Uzak Windows Yönetim hizmeti 'nden 504 HTTP durum kodu nedeniyle Azure geçişi 'ni kullanarak GCP örneklerini bulamadım    
**Cevap:** Azure geçiş gereci gereksinimlerini ve URL erişim gereksinimlerini gözden geçirdiğinizden emin olun. Hiçbir proxy ayarlarının gereç kaydını engellemediğinden emin olun.

**Soru:** GCP sanal makinelerinizi Azure 'a geçirmeden önce herhangi bir değişiklik yapmam gerekir   
**Cevap:** EC2 VM 'lerinizi Azure 'a geçirmeden önce bu değişiklikleri yapmanız gerekebilir:

- VM sağlama için Cloud-init kullanıyorsanız, VM 'yi Azure 'a çoğaltmadan önce VM üzerinde Cloud-init devre dışı bırakmak isteyebilirsiniz. VM üzerinde Cloud-init tarafından gerçekleştirilen sağlama adımları GCP 'ye özgü olabilir ve Azure 'a geçişten sonra geçerli olmayacaktır.  
- Azure 'a geçirmeden önce işletim sistemi için gerekli herhangi bir değişiklik olup olmadığını öğrenmek için [Önkoşullar](#prerequisites) bölümünü gözden geçirin.
- Son geçişten önce her zaman bir test geçişi çalıştırmanızı öneririz.  

## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.