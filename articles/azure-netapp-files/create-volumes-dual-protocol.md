---
title: Azure NetApp Files için bir çift protokol (NFSv3 ve SMB) birimi oluşturun | Microsoft Docs
description: LDAP kullanıcı eşlemesi desteğiyle NFSv3 ve SMB 'nin ikili protokolünü kullanan bir birimin nasıl oluşturulduğunu açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/28/2020
ms.author: b-juche
ms.openlocfilehash: 0079c123f908a38cc1e4923790439f18352bf3ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574635"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files için bir çift protokol (NFSv3 ve SMB) birimi oluşturun

Azure NetApp Files, NFS (NFSv3 ve NFSv 4.1), SMB3 veya Dual Protocol kullanarak birim oluşturmayı destekler. Bu makalede, LDAP kullanıcı eşlemesi desteğiyle NFSv3 ve SMB 'nin ikili protokolünü kullanan bir birimin nasıl oluşturulacağı gösterilmektedir.  


## <a name="before-you-begin"></a>Başlamadan önce 

* Zaten bir kapasite havuzu oluşturmuş olmanız gerekir.  
    Bkz. [Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md).   
* Azure NetApp Files için bir alt ağ atanmış olmalıdır.  
    [Azure NetApp Files için bir alt ağ temsilcisine](azure-netapp-files-delegate-subnet.md)bakın.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* [Active Directory bağlantıları Için gereksinimleri](create-active-directory-connections.md#requirements-for-active-directory-connections)karşıladığınızdan emin olun. 
* DNS sunucusunda bir geriye doğru arama bölgesi oluşturun ve ardından bu geriye doğru arama bölgesine AD ana makinesi için bir işaretçi (PTR) kaydı ekleyin. Aksi halde, çift protokol birimi oluşturma işlemi başarısız olur.
* NFS istemcisinin güncel olduğundan ve işletim sistemi için en son güncelleştirmeleri çalıştırdığından emin olun.
* AD üzerinde Active Directory (AD) LDAP sunucusunun açık ve çalışıyor olduğundan emin olun. Bunu, AD makinesine [Active Directory Basit Dizin Hizmetleri (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) rolünü yükleyip yapılandırarak yapabilirsiniz.
* Çift protokol birimleri Şu anda Azure Active Directory Domain Services desteklemez (AEKLEMELERI).  
* Bir çift protokol birimi tarafından kullanılan NFS sürümü NFSv3 ' dir. Bu nedenle, aşağıdaki önemli noktalar geçerlidir:
    * İkili protokol, NFS istemcilerinden gelen Windows ACL genişletilmiş özniteliklerini desteklemez `set/get` .
    * NFS istemcileri NTFS güvenlik stili için izinleri değiştiremezler ve Windows istemcileri UNIX stili çift protokol birimlerinin izinlerini değiştiremezler.   

    Aşağıdaki tabloda güvenlik stilleri ve etkileri açıklanmaktadır:  
    
    | Güvenlik stili    | İzinleri değiştirebilecek istemciler   | İstemcilerin kullanabileceği izinler  | Ortaya çıkan etkin güvenlik stili    | Dosyalara erişebilen istemciler     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 modu bitleri   | UNIX  | NFS ve Windows   |
    | `Ntfs`    | Windows   | NTFS ACL 'Leri     | NTFS  |NFS ve Windows|
* NFS kullanarak NTFS güvenlik stili birimini bağlama UNIX kullanıcıları, `root` UNIX Için Windows kullanıcısı `root` ve `pcuser` diğer tüm kullanıcılar için doğrulanır. Bu Kullanıcı hesaplarının, NFS kullanırken birimi bağlamadan önce Active Directory bulunduğundan emin olun. 
* Büyük topolojiniz varsa ve `Unix` güvenlik stilini bir çift protokol birimiyle veya genişletilmiş gruplar Ile LDAP ile kullanıyorsanız, topolojinizdeki tüm sunuculara erişemeyebilirsiniz Azure NetApp Files.  Bu durum oluşursa, yardım için hesap ekibinize başvurun.  <!-- NFSAAS-15123 --> 
* Çift protokol birimi oluşturmak için sunucu kök CA sertifikasına ihtiyacınız yoktur. Yalnızca TLS üzerinden LDAP etkinleştirildiğinde gereklidir.


## <a name="create-a-dual-protocol-volume"></a>Çift protokollü birim oluşturma

1.  Kapasite havuzları dikey penceresinden **birimler** dikey penceresine tıklayın. Birim oluşturmak için **+ Birim ekle**'ye tıklayın. 

    ![Birimlere git](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Birim Oluştur penceresinde **Oluştur**' a tıklayın ve temel bilgiler sekmesinde aşağıdaki alanlar için bilgi sağlayın:   
    * **Birim adı**      
        Oluşturmakta olduğunuz birim için ad belirtin.   

        Birim adı her bir kapasite havuzu içinde benzersiz olmalıdır. En az üç karakter uzunluğunda olmalıdır. Herhangi bir alfasayısal karakter kullanabilirsiniz.   

        `default` `bin` Birim adı olarak veya kullanamazsınız.

    * **Kapasite havuzu**  
        Birimin oluşturulmasını istediğiniz kapasite havuzunu belirtin.

    * **Kota**  
        Birime ayrılmış mantıksal depolama miktarını belirtin.  

        **Kullanılabilir kota** alanı, yeni birimi oluştururken kullanabildiğiniz, seçilen kapasite havuzundaki kullanılmamış alan miktarını gösterir. Yeni birimin boyutu kullanılabilir kotayı aşamaz.  

    * **Aktarım hızı (MIB/S)**   
        Birim el ile QoS kapasite havuzunda oluşturulduysa, birim için istediğiniz aktarım hızını belirtin.   

        Birim bir otomatik QoS kapasite havuzunda oluşturulduysa, bu alanda görünen değer (Kota x hizmet düzeyi aktarım hızı) olur.   

    * **Sanal ağ**  
        Birime erişmek istediğiniz Azure sanal ağını (VNet) belirtin.  

        Belirttiğiniz VNET Azure NetApp Files için bir alt ağa sahip olmalıdır. Azure NetApp Files hizmetine yalnızca aynı VNET 'ten veya VNET eşlemesi ile aynı bölgedeki bir VNET 'ten erişilebilir. Ayrıca, hızlı rota aracılığıyla şirket içi ağınızdan birime da erişebilirsiniz.   

    * **Alt ağ**  
        Birim için kullanmak istediğiniz alt ağı belirtin.  
        Belirttiğiniz alt ağ Azure NetApp Files için temsilci atanmış olmalıdır. 
        
        Bir alt ağ temsilcisi yoksa, birim oluştur sayfasında **Yeni oluştur** ' a tıklayabilirsiniz. Sonra alt ağ oluştur sayfasında alt ağ bilgilerini belirtin ve alt ağın Azure NetApp Files için temsilci olarak **Microsoft. NetApp/birimler** ' i seçin. Her VNET 'te Azure NetApp Files için yalnızca bir alt ağ atanabilir.   
 
        ![Birim oluşturun](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alt ağ oluşturma](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Birime var olan bir anlık görüntü ilkesi uygulamak istiyorsanız, genişletmek için **Gelişmiş bölümü göster** ' e tıklayın, anlık görüntü yolunu gizlemek isteyip istemediğinizi belirtin ve açılır menüden bir anlık görüntü ilkesi seçin. 

        Anlık görüntü ilkesi oluşturma hakkında daha fazla bilgi için bkz. [anlık görüntü Ilkelerini yönetme](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Gelişmiş seçimi göster](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **Protokol**' e tıklayın ve ardından aşağıdaki eylemleri tamamlamayı seçin:  
    * Birimin protokol türü olarak **çift protokol (NFSv3 ve SMB)** seçeneğini belirleyin.   

    * Birimin **birim yolunu** belirtin.   
    Bu birim yolu, Paylaşılan birimin adıdır. Ad alfabetik bir karakterle başlamalıdır ve her abonelik ve her bölge içinde benzersiz olmalıdır.  

    * Kullanılacak **güvenlik stilini** BELIRTIN: NTFS (varsayılan) veya UNIX.

    * İsteğe bağlı olarak, [birim için dışa aktarma ilkesi yapılandırın](azure-netapp-files-configure-export-policy.md).

    ![Çift protokol belirtin](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Birim ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın. Birimi oluşturmak için **Oluştur** ' a tıklayın.

    Oluşturduğunuz birim birimler sayfasında görünür. 
 
    Birim, kapasite havuzundan aboneliği, kaynak grubunu ve konum özniteliklerini devralır. Birimin dağıtım durumunu izlemek için Bildirimler sekmesini kullanabilirsiniz.

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX özniteliklerini yönetme

UID, Ana Dizin ve diğer değerler gibi POSIX özniteliklerini, Active Directory Kullanıcıları ve bilgisayarları MMC ek bileşenini kullanarak yönetebilirsiniz.  Aşağıdaki örnek Active Directory öznitelik düzenleyicisini gösterir:  

![Active Directory öznitelik Düzenleyicisi](../media/azure-netapp-files/active-directory-attribute-editor.png) 

LDAP Kullanıcıları ve LDAP grupları için aşağıdaki öznitelikleri ayarlamanız gerekir: 
* LDAP kullanıcıları için gerekli öznitelikler:   
    `uid`: Çiğdem, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* LDAP grupları için gerekli öznitelikler:   
    `objectClass`: "posixGroup", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>NFS istemcisini yapılandırma 

NFS istemcisini yapılandırmak için [Azure NetApp FILES NFS Istemcisi yapılandırma](configure-nfs-clients.md) konusundaki yönergeleri izleyin.  

## <a name="next-steps"></a>Sonraki adımlar  

* [Azure NetApp Files için NFS istemcisini yapılandırma](configure-nfs-clients.md)
* [SMB veya çift protokol birimlerinde sorun giderme](troubleshoot-dual-protocol-volumes.md)
