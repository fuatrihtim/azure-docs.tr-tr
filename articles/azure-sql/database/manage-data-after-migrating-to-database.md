---
title: Geçişten sonra Yönet
titleSuffix: Azure SQL Database
description: Azure SQL veritabanı 'na geçişten sonra tek ve havuza alınmış veritabanlarınızı yönetmeyi öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446113"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Bulutta yeni DBA: geçişten sonra Azure SQL veritabanı 'nı yönetme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Geleneksel olarak yönetilen, kendi kendine denetlenen ortamdan PaaS ortamına geçiş, ilk olarak biraz zaman alabilir. Bir uygulama geliştiricisi veya bir DBA olarak, uygulamanın kullanılabilir, performans, güvenli ve dayanıklı-her zaman korunmasına yardımcı olacak platformun temel yeteneklerini bilmek isteyebilirsiniz. Bu makale, tam olarak bunu yapar. Bu makale, kaynakları düzenler ve Azure SQL veritabanı 'nın, uygulamanızı verimli bir şekilde çalışır durumda tutmak ve bulutta en iyi sonuçları elde etmek için tek ve havuza alınmış veritabanlarıyla en iyi şekilde kullanılması konusunda size rehberlik sağlar. Bu makale için tipik kitleler şunlardır:

- Uygulamalarının Azure SQL veritabanı 'na geçişini değerlendiriyor ve uygulamanızı modernleştiriliyor.
- , Uygulama-geçiş senaryosunu geçirme sürecdir.
- Kısa bir süre önce Azure SQL veritabanı 'na geçişi tamamladı: bulutta yeni DBA.

Bu makalede, Azure SQL veritabanı 'nın bazı temel özellikleri, tek veritabanları ve elastik havuzlardaki havuza alınmış veritabanları ile çalışırken kullanabileceğiniz bir platform olarak açıklanmaktadır. Bunlar şunlardır:

- Azure portalını kullanarak veritabanlarını izleme
- İş sürekliliği ve olağanüstü durum kurtarma (BCDR)
- Güvenlik ve uyumluluk
- Akıllı veritabanı izleme ve bakım
- Veri taşıma

## <a name="monitor-databases-using-the-azure-portal"></a>Azure portalını kullanarak veritabanlarını izleme

[Azure Portal](https://portal.azure.com/), veritabanınızı seçip **izleme** grafiğine tıklayarak veritabanının kullanımını tek tek izleyebilirsiniz. Bu işlem sonrasında bir **Ölçüm** penceresi görüntülenir. **Grafiği düzenle** düğmesine tıklayarak değişiklik yapabilirsiniz. Şu ölçümleri ekleyin:

- CPU yüzdesi
- DTU yüzdesi
- Veri G/Ç yüzdesi
- Veri boyutu yüzdesi

Bu ölçümleri ekledikten sonra, **ölçüm** penceresi hakkında daha fazla bilgi Ile bunları **izleme** grafiğinde görüntülemeye devam edebilirsiniz. Dört ölçümün tümü de veritabanınızın ortalama **DTU** kullanım yüzdesini gösterir. Hizmet katmanları hakkında daha fazla bilgi için [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md) makalelerine bakın.  

![Hizmet katmanına göre veritabanı performansını izleme.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Performans ölçümlerine ilişkin uyarıları da yapılandırabilirsiniz. **Ölçüm** penceresindeki **Uyarı ekle** düğmesine tıklayın. Uyarınızı yapılandırmak için sihirbazı takip edin. Ölçümlerin belirli bir eşiği aşması veya belirli bir eşiğin altına düşmesi halinde uyarı alabilirsiniz.

Örneğin, veritabanınızdaki bir iş yükünün artmasını bekliyorsanız bir e-posta uyarısı yapılandırarak veritabanınızın herhangi bir performans ölçümünde %80 sınırına ulaşması halinde uyarı alabilirsiniz. Bunu, bir sonraki en yüksek işlem boyutuna geçmeniz gerekebilecek zaman anlamak için erken bir uyarı olarak kullanabilirsiniz.

Performans ölçümleri, daha düşük bir işlem boyutuna indirgeniyor olup olmadığınızı belirlemenize de yardımcı olabilir. Standart S2 veritabanını kullandığınızı ve tüm performans ölçümlerinin, veritabanının belirli bir zaman için ortalama %10'dan daha fazla kullanımda bulunmadığını gösterdiğini varsayın. Bu, veritabanının Standart S1'de de düzgün şekilde çalışabileceğini gösterir. Ancak, daha düşük bir işlem boyutuna geçme kararı vermeden önce, ani veya dalgalanmış olan iş yüklerinden haberdar olun.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>İş sürekliliği ve olağanüstü durum kurtarma (BCDR)

İş sürekliliği ve olağanüstü durum kurtarma becerileri, bir olağanüstü durum oluşması durumunda işinize devam edebilmeniz için size olanak sağlar. Olağanüstü durum veritabanı düzeyindeki bir olay olabilir (örneğin, birisi yanlışlıkla önemli bir tabloyu bırakır) veya bir veri merkezi düzeyi olayı (bölgesel felaketler, örneğin bir TSUNAMI) olabilir.

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>SQL veritabanı 'nda yedeklemeleri oluşturma ve yönetme Nasıl yaparım?

Azure SQL veritabanı üzerinde yedeklemeler oluşturmayın ve bu, ' ın olması gerekmez. SQL veritabanı, veritabanlarını sizin için otomatik olarak yedekler, böylelikle yedeklemeleri zamanlama, alma ve yönetme konusunda endişelenmenize gerek kalmaz. Platform her hafta bir tam yedekleme gerçekleştirir ve olağanüstü durum kurtarmanın verimli olmasını sağlamak için her 5 dakikada bir günlük yedeklemesi ve veri kaybı olması gerekir. İlk tam yedekleme, bir veritabanı oluşturandan hemen sonra gerçekleşir. Bu yedeklemeler, "bekletme süresi" adlı belirli bir süre için kullanılabilir ve seçtiğiniz hizmet katmanına göre farklılık gösterir. SQL veritabanı, bu bekletme döneminde zaman içindeki herhangi bir noktaya geri yükleme olanağını, zaman içindeki bir [noktaya kurtarma (sür)](recovery-using-backups.md#point-in-time-restore)kullanarak sağlar.

|Hizmet katmanı|Bekletme süresi (gün)|
|---|:---:|
|Temel|7|
|Standart|35|
|Premium|35|
|||

Ayrıca, [uzun süreli saklama (LTR)](long-term-retention-overview.md) özelliği, yedekleme dosyalarınıza daha uzun bir süre boyunca özel olarak, 10 yıla kadar, bu yedeklemelerdeki verileri ise bu süre içinde herhangi bir noktada geri yüklemenize olanak tanır. Ayrıca, esnekliği bölge felaketler adresinden emin olmak için veritabanı yedeklemeleri coğrafi olarak çoğaltılan depolamada tutulur. Bu yedeklemeleri, bekletme dönemi içinde istediğiniz zaman herhangi bir Azure bölgesinde da geri yükleyebilirsiniz. Bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Nasıl yaparım?, veri merkezi düzeyinde olağanüstü durum veya bölgesel felaketler durumunda iş sürekliliği sağlama

Veritabanı yedeklemeleriniz, bölgesel bir olağanüstü durum durumunda olduğundan emin olmak için coğrafi olarak çoğaltılan depolamada depolandığından, yedeklemeyi başka bir Azure bölgesine geri yükleyebilirsiniz. Bu, coğrafi geri yükleme olarak adlandırılır. Bunun için RPO (kurtarma noktası hedefi) < genellikle 1 saat ve ERT (tahmini kurtarma süresi – birkaç dakika-saat) olur.

Görev açısından kritik veritabanları için, Azure SQL veritabanı teklifleri, etkin coğrafi çoğaltma. Bunun temelde, başka bir bölgede orijinal veritabanınızın coğrafi olarak çoğaltılan bir ikincil kopyasını oluşturmasıdır. Örneğin, veritabanınız başlangıçta Azure Batı ABD bölgesinde barındırılıyorsa ve bölgesel olağanüstü durum esnekliği istiyorsanız. Doğu ABD söylemek için Batı ABD veritabanında etkin bir coğrafi çoğaltma oluşturursunuz. Calamity Batı ABD çalıştığında Doğu ABD bölgesine yük devretmek için yük devretme yapabilirsiniz. Bir otomatik yük devretme grubunda yapılandırma işlemi, veritabanının bir olağanüstü durum durumunda Doğu ABD otomatik olarak ikincil üzerinde yük devretmesini sağladığından daha da iyidir. Bu için RPO, 5 saniye ve ERT < 30 saniyedir <.

Bir otomatik yük devretme grubu yapılandırılmamışsa, uygulamanızın olağanüstü durum için etkin bir şekilde izlemesi ve ikincil için bir yük devretme başlatması gerekir. Farklı Azure bölgelerinde, en fazla 4 etkin coğrafi çoğaltma oluşturabilirsiniz. Daha da iyi hale gelir. Ayrıca, bu ikincil etkin coğrafi çoğaltmalara salt okuma erişimi için de erişebilirsiniz. Bu, coğrafi olarak dağıtılmış bir uygulama senaryosuna yönelik gecikmeyi azaltmak için çok kullanışlı bir uygulamadır.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Olağanüstü durum kurtarma planımın şirket içi sunucudan SQL veritabanı 'na nasıl değiştirileceği

Özet olarak, SQL Server Kurulum yük devretme kümelemesi, veritabanı yansıtma, Işlem çoğaltma veya günlük aktarma gibi özellikleri kullanarak ve Iş sürekliliği sağlamak için yedeklemelerin bakımını yapma ve yönetme gibi özellikleri kullanarak etkin bir şekilde yönetmenizi gerektirir. SQL veritabanı ile, platform bunları sizin için yönettiğinden, veritabanı uygulamanızı geliştirmeye ve iyileştirmenize ve olağanüstü durum yönetimi konusunda endişelenmenize odaklanmanız gerekir. Yedekleme ve olağanüstü durum kurtarma planlarınızın Azure portal (veya PowerShell API 'Lerini kullanarak birkaç komut) üzerinde yalnızca birkaç tıklamayla yapılandırıldığını ve çalışmasını sağlayabilirsiniz.

Olağanüstü durum kurtarma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı olağanüstü durum kurtarma 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Güvenlik ve uyumluluk

SQL veritabanı, güvenlik ve gizliliği çok önemli bir şekilde alır. SQL veritabanı içindeki güvenlik veritabanı düzeyinde ve platform düzeyinde kullanılabilir ve birçok katmana kategorize edildiğinde en iyi şekilde anlaşılır. Her katmanda, uygulamanız için en iyi güvenliği denetlemenizi ve sağlamanızı sağlarsınız. Katmanlar şunlardır:

- Kimlik & kimlik doğrulaması ([SQL kimlik doğrulaması ve Azure Active Directory [Azure AD] kimlik doğrulaması](logins-create-manage.md)).
- İzleme etkinliği ([Denetim](../../azure-sql/database/auditing-overview.md) ve [tehdit algılama](threat-detection-configure.md)).
- Gerçek verileri koruma ([Saydam veri şifrelemesi [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ve [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Gizli ve ayrıcalıklı verilere erişimi denetleme ([satır düzeyi güvenlik](/sql/relational-databases/security/row-level-security) ve [dinamik veri maskeleme](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/) , Azure 'da, şirket içinde ve diğer bulutlarda çalışan iş yükleri arasında merkezi güvenlik yönetimi sağlar. [Denetim](../../azure-sql/database/auditing-overview.md) ve [Saydam veri şifrelemesi [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) gibi önemli SQL veritabanı korumasının tüm kaynaklarda yapılandırılıp yapılandırılmadığını ve kendi gereksinimlerinize göre ilkeler oluşturup oluşturamayacağını görüntüleyebilirsiniz.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>SQL veritabanı 'nda hangi kullanıcı kimlik doğrulama yöntemlerini sunuluyor

SQL veritabanı 'nda sunulan iki kimlik doğrulama yöntemi vardır:

- [Azure Active Directory Kimlik Doğrulaması](authentication-aad-overview.md)
- [SQL kimlik doğrulaması](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Geleneksel Windows kimlik doğrulaması desteklenmez. Azure Active Directory (Azure AD) merkezi bir kimlik ve erişim yönetimi hizmetidir. Bu sayesinde, kuruluşunuzdaki tüm personele çok rahat bir oturum açma erişimi (SSO) sağlayabilirsiniz. Bu anlamı, kimlik bilgilerinin daha basit kimlik doğrulama için tüm Azure hizmetleri genelinde paylaşılmasıdır. 

Azure AD, Azure AD [Multi-Factor Authentication](authentication-mfa-ssms-overview.md) destekler ve [bırkaç tıklamayla](../../active-directory/hybrid/how-to-connect-install-express.md) Azure ad, Windows Server Active Directory ile tümleştirilebilir. SQL kimlik doğrulaması, tam olarak onu zaten kullandığınız gibi çalışmaktadır. Bir Kullanıcı adı/parola sağlarsınız ve belirli bir sunucudaki tüm veritabanları için kullanıcıların kimliğini doğrulayabilirsiniz. Bu ayrıca SQL veritabanı ve Azure SYNAPSE analizlerinin bir Azure AD etki alanı içinde Multi-Factor Authentication ve Konuk Kullanıcı hesapları sunmasını sağlar. Şirket içi bir Active Directory zaten varsa, dizininizi Azure 'a genişletmek için dizini Azure Active Directory federasyona bağlayabilirsiniz.

|**Eğer...**|**SQL veritabanı/Azure SYNAPSE Analizi**|
|---|---|
|Azure 'da Azure Active Directory (Azure AD) kullanmayı tercih etme|[SQL kimlik doğrulaması](security-overview.md) kullan|
|Şirket içi SQL Server kullanılan AD|[Ad 'Yi Azure AD Ile Federasyonun](../../active-directory/hybrid/whatis-hybrid-identity.md)ve Azure AD kimlik doğrulamasını kullanın. Bununla birlikte, çoklu oturum açma kullanabilirsiniz.|
|Multi-Factor Authentication zorunlu kılmak gerekir|[Microsoft koşullu erişimi](conditional-access-configure.md)ile ilke olarak Multi-Factor Authentication gerektir ve [MULTI-Factor AUTHENTICATION desteğiyle Azure AD evrensel kimlik doğrulaması](authentication-mfa-ssms-overview.md)kullanın.|
|Microsoft hesaplarından (live.com, outlook.com) veya diğer etki alanlarından (gmail.com) Konuk hesapları vardır|Azure AD [B2B Işbirliğinin](../../active-directory/external-identities/what-is-b2b.md)kullanıldığı SQL veritabanı/veri ambarı 'NDA [Azure AD evrensel kimlik doğrulaması](authentication-mfa-ssms-overview.md) kullanın.|
|, Federasyon etki alanındaki Azure AD kimlik bilgilerinizi kullanarak Windows 'da oturum açar|[Azure AD Tümleşik kimlik doğrulaması](authentication-aad-configure.md)kullanın.|
|Azure ile federe olmayan bir etki alanındaki kimlik bilgilerini kullanarak Windows 'da oturum açar|[Azure AD Tümleşik kimlik doğrulaması](authentication-aad-configure.md)kullanın.|
|SQL veritabanı veya Azure SYNAPSE Analytics 'e bağlanması gereken orta katman hizmetlere sahip|[Azure AD Tümleşik kimlik doğrulaması](authentication-aad-configure.md)kullanın.|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Veritabanına bağlantı erişimini Nasıl yaparım? limiti veya denetimi

Elden çıkarmada, uygulamanız için en uygun bağlantı kuruluşunu sağlamak için kullanabileceğiniz birden fazla teknik vardır.

- Güvenlik Duvarı Kuralları
- Sanal Ağ Hizmet Uç Noktaları
- Ayrılmış IP’ler

#### <a name="firewall"></a>Güvenlik Duvarı

Bir güvenlik duvarı, sunucunuza yalnızca belirli varlıkların erişmesine izin vererek bir dış varlıktan sunucunuza erişimi engeller. Varsayılan olarak, sunucu içindeki veritabanlarına yönelik tüm bağlantılara, diğer Azure hizmetlerinden gelen (optionally7) bağlantılar dışında izin verilmez. Bir güvenlik duvarı kuralıyla, bu bilgisayarın IP adresine güvenlik duvarı üzerinden izin vererek, yalnızca onayladığınız varlıklara (örneğin, bir geliştirici makinesi) erişimi açabilirsiniz. Ayrıca, sunucuya erişime izin vermek istediğiniz bir IP aralığı belirtmenize olanak tanır. Örneğin, kuruluşunuzdaki geliştirici makinesi IP adresleri, güvenlik duvarı ayarları sayfasında bir Aralık belirtilerek bir kerede eklenebilir.

Sunucu düzeyinde veya veritabanı düzeyinde güvenlik duvarı kuralları oluşturabilirsiniz. Sunucu düzeyi IP güvenlik duvarı kuralları Azure portal kullanılarak veya SSMS ile oluşturulabilir. Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralı ayarlama hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı 'NDA IP güvenlik duvarı kuralları oluşturma](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Hizmet uç noktaları

Varsayılan olarak, veritabanınız "Azure hizmetlerinin sunucuya erişmesine Izin ver" olarak yapılandırılmıştır. Bu, Azure 'daki herhangi bir sanal makinenin veritabanınıza bağlanmayı deneyebileceği anlamına gelir. Bu girişimlere yine de kimlik doğrulaması yapmaya devam edin. Ancak, veritabanınızın herhangi bir Azure IP tarafından erişilebilmesini istemiyorsanız, "Azure hizmetlerinin sunucuya erişmesine Izin ver" seçeneğini devre dışı bırakabilirsiniz. Ayrıca, [VNET hizmet uç noktalarını](vnet-service-endpoint-rule-overview.md)yapılandırabilirsiniz.

Hizmet uç noktaları (o), kritik Azure kaynaklarınızı yalnızca Azure 'daki özel sanal ağınıza sunmanıza olanak tanır. Bunu yaparak kaynaklarınıza genel erişimi ortadan kaldırabilirsiniz. Azure ile sanal ağınız arasındaki trafik Azure omurga ağında kalır. Zorunlu olmadan Zorlamalı tünel paket yönlendirmesi edinirsiniz. Sanal ağınız, internet trafiğini kuruluşunuza ve Azure hizmet trafiğine aynı rotaya göre devam etmeye zorlar. Hizmet uç noktaları ile, paketlerin sanal ağınızdan Azure omurga ağı üzerindeki hizmetine doğrudan akışı nedeniyle bunu iyileştirebilirsiniz.

![Sanal ağ hizmet uç noktaları](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Ayrılmış IP’ler

Diğer bir seçenek de sanal makinelerinize [ayrılmış IP 'ler](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) sağlamak ve bu VM IP adreslerini sunucu güvenlik duvarı ayarlarına eklemektir. Ayrılmış IP 'Ler atayarak, güvenlik duvarı kurallarını değişen IP adresleriyle güncelleştirmek zorunda olma sorunu kaydedilir.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Üzerinde SQL veritabanına hangi bağlantı noktası bağlanabilirim?

Bağlantı noktası 1433. SQL veritabanı bu bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmak için, kuruluşunuzun güvenlik duvarı ayarlarına bir giden kuralı eklemeniz gerekir. Kılavuz olarak, 1433 numaralı bağlantı noktasını Azure sınırının dışında açığa çıkarmaktan kaçının.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>SQL veritabanında sunucum ve veritabanımda etkinlikleri nasıl izleyebilirim ve yapılandırabilirim?

#### <a name="sql-database-auditing"></a>SQL Veritabanı Denetimi

SQL veritabanı ile veritabanı olaylarını izlemek için denetim özelliğini açabilirsiniz. [SQL veritabanı denetimi](../../azure-sql/database/auditing-overview.md) , veritabanı olaylarını kaydeder ve bunları Azure Storage hesabınızdaki bir denetim günlüğü dosyasına yazar. Denetim, özellikle olası güvenlik ve ilke ihlalleriyle ilgili Öngörüler elde etmek, mevzuata uyumluluklarını sağlamak için yararlıdır. Bu, Denetim gerektiren belirli olay kategorilerini tanımlamanıza ve yapılandırmanıza ve veritabanınızda gerçekleşen olaylara genel bir bakış almak için önceden yapılandırılmış raporları ve bir panoyu almanızı sağlar. Bu denetim ilkelerini veritabanı düzeyinde ya da sunucu düzeyinde uygulayabilirsiniz. Sunucu/veritabanınız için denetimin nasıl etkinleştirileceği hakkında bir kılavuz, bkz. [SQL veritabanı denetimini etkinleştirme](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Tehdit algılama

[Tehdit algılama](threat-detection-configure.md)sayesinde, çok kolay bir şekilde denetim aracılığıyla keşfedilen güvenlik veya ilke ihlalleri üzerinde işlem yapma imkanına sahip olursunuz. Sisteminizdeki olası tehditleri veya ihlalleri ele almak için bir güvenlik uzmanı olmanız gerekmez. Tehdit algılama için SQL ekleme algılaması gibi bazı yerleşik yetenekler de vardır. SQL ekleme, verileri değiştirme veya ele almanın bir yoludur ve genel olarak bir veritabanı uygulamasına saldırmaya yönelik oldukça yaygın bir yoldur. Tehdit algılama, olası güvenlik açıklarını ve SQL ekleme saldırılarını ve anormal veritabanı erişim düzenlerini (alışılmadık bir konumdan veya bilmediğiniz bir sorumlu ile erişim gibi) algılayan birden çok algoritma kümesini çalıştırır. Veritabanında bir tehdit algılanırsa Güvenlik ofisleri veya diğer belirlenen yöneticiler bir e-posta bildirimi alır. Her bildirim şüpheli etkinliğin ayrıntılarını ve tehdidi daha fazla araştırıp azaltmaya ilişkin önerileri sağlar. Tehdit algılamayı nasıl etkinleştireceğinizi öğrenmek için bkz: [tehdit algılamayı etkinleştirme](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>SQL veritabanı 'nda verilerimi genel olarak korumak Nasıl yaparım?

Şifreleme, yetkisiz kişilerden hassas verilerinizi korumak ve güvenli hale getirmek için güçlü bir mekanizma sağlar. Şifrelenmiş verileriniz şifre çözme anahtarı olmadan saldırgan tarafından kullanılamaz. Bu nedenle, SQL veritabanı 'nda yerleşik olan mevcut güvenlik katmanlarının üzerine ek bir koruma katmanı ekler. Verilerinizi SQL veritabanında korumanın iki yönü vardır:

- Veriler ve günlük dosyalarında bekleyen verileriniz
- Uçuşdaki verileriniz

SQL veritabanı 'nda varsayılan olarak, depolama alt sistemindeki veri ve günlük dosyalarındaki verileriniz tamamen ve her zaman [Saydam veri şifrelemesi [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)aracılığıyla şifrelenir. Yedeklemeleriniz de şifrelenir. TDE, bu verilere erişen uygulama tarafında gerekli değişiklik yok. Şifreleme ve şifre çözme saydam bir şekilde gerçekleşir; Bu nedenle ad.
Hassas verilerinizi uçuş sırasında ve bekleyen bir şekilde korumak için SQL veritabanı [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)adlı bir özellik sağlar. AE, veritabanınızdaki hassas sütunları şifreleyen bir istemci tarafı şifreleme biçimidir (Bu nedenle veritabanı yöneticilerine ve yetkisiz kullanıcılara şifreli olarak yer alırlar). Sunucu, ile başlamak için şifrelenmiş verileri alır. Always Encrypted anahtarı, istemci tarafında da depolanır. bu nedenle, yalnızca yetkili istemcilerin gizli sütunların şifresini çözebilmesini sağlayabilirsiniz. Şifreleme anahtarları istemcide depolandığından, sunucu ve veri yöneticileri hassas verileri göremez. AE, tablodaki hassas sütunları, yetkisiz istemcilerden fiziksel diske, son uca şifreler. AE, bugün eşitlik karşılaştırmaları destekler, bu nedenle DBAs, şifreli sütunları SQL komutlarının bir parçası olarak sorgulamaya devam edebilir. Always Encrypted, [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), Windows sertifika deposu ve yerel donanım güvenlik modülleri gibi çeşitli anahtar depolama seçenekleriyle kullanılabilir.

|**Özellikler**|**Always Encrypted**|**Saydam Veri Şifrelemesi**|
|---|---|---|
|**Şifreleme kapsamı**|Uçtan uca|Rest verileri|
|**Sunucu, hassas verilere erişebilir**|No|Evet, çünkü şifreleme bekleyen veriler için|
|**İzin verilen T-SQL işlemleri**|Eşitlik karşılaştırması|Tüm T-SQL Surface alanı kullanılabilir|
|**Özelliği kullanmak için gereken uygulama değişiklikleri**|En az|Çok küçük|
|**Şifreleme ayrıntı düzeyi**|Sütun düzeyi|Veritabanı düzeyinde Kimlik Bilgileri belirleme seçeneği|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Veritabanınızdaki gizli verilere erişimi nasıl kısıtlayabilirim?

Her uygulamanın, veritabanında herkese görünür olması gereken belirli bir hassas verileri vardır. Kuruluşun içindeki belirli personelin bu verileri görüntülemesi gerekir, ancak başkaları bu verileri görüntüleyemez. Çalışanların ücretleri bir örnektir. Yöneticinin doğrudan raporlarının ücret bilgilerine erişmesi gerekir, ancak bireysel takım üyelerinin, eşlerinin ücret bilgilerine erişimi olmaz. Başka bir senaryo, geliştirme aşamaları veya test sırasında hassas verilerle etkileşim kurmak isteyebileceğiniz veri geliştiricileridir, örneğin SSNs müşterileri. Bu bilgilerin yeniden geliştiriciye sunulmasıdır. Bu gibi durumlarda, hassas verilerinizin maskelenmiş olması veya hiç gösterilmemesi gerekir. SQL veritabanı, yetkisiz kullanıcıların hassas verileri görüntüleyebilmesini engellemek için iki tür yaklaşım sunar:

[Dinamik veri maskeleme](dynamic-data-masking-overview.md) , uygulama katmanındaki ayrıcalıklı olmayan kullanıcılarla maskeleyerek hassas veri pozlamasını sınırlandırmanızı sağlayan bir veri maskeleme özelliğidir. Bir maskeleme stili (örneğin, yalnızca bir ulusal kimliğin en son dört basamağını göstermek için) bir maskeleme kuralı tanımlayın (örneğin, yalnızca bir ulusal kimlik için en fazla dört basamak, her biri XS olarak işaretle) ve maskeleme kuralından hangi kullanıcıların dışlanacağını belirleyin. Maskeleme anında gerçekleşir ve çeşitli veri kategorileri için çeşitli maskeleme işlevleri mevcuttur. Dinamik veri maskeleme, veritabanınızdaki hassas verileri otomatik olarak algılamanıza ve ona maskeleme uygulamanıza olanak tanır.

[Satır düzeyi güvenliği](/sql/relational-databases/security/row-level-security) , satır düzeyinde erişimi denetlemenize olanak sağlar. Yani, sorguyu yürüten kullanıcıyı (Grup üyeliği veya yürütme bağlamı) temel alan bir veritabanı tablosundaki belirli satırlar gizlidir. Erişim kısıtlaması, Uygulama mantığınızı basitleştirmek için bir uygulama katmanında değil, veritabanı katmanında yapılır. Bir filtre koşulu oluşturarak başlar, gösterilmeyen satırları filtreleyip ve bu satırlara kimlerin erişebileceğini tanımlayan bir sonraki güvenlik ilkesi. Son olarak, Son Kullanıcı kendi sorgusuna sahiptir ve kullanıcının ayrıcalığına bağlı olarak, bu kısıtlı satırları görüntüler ya da onları hiç göremez.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Nasıl yaparım? buluttaki şifreleme anahtarlarını yönetme

Always Encrypted (istemci tarafı şifreleme) ve Saydam Veri Şifrelemesi (bekleyen şifreleme) için anahtar yönetim seçenekleri vardır. Şifreleme anahtarlarını düzenli olarak döndürmenizi öneririz. Döndürme sıklığı, hem iç kuruluş yönetmeliklerinizin hem de uyumluluk gereksinimleriyle hizalanmalıdır.

#### <a name="transparent-data-encryption-tde"></a>Saydam Veri Şifrelemesi (TDE)

TDE ' de iki tuşlu bir hiyerarşi vardır: her bir Kullanıcı veritabanındaki veriler, simetrik AES-256 veritabanı (DEK) tarafından şifrelenir ve bu, bir sunucu benzersiz asimetrik RSA 2048 ana anahtarıyla şifrelenir. Ana anahtar şu iki şekilde yönetilebilir:

- Platform-SQL veritabanı tarafından otomatik olarak.
- Ya da anahtar deposu olarak [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) kullanabilirsiniz.

Varsayılan olarak, Saydam Veri Şifrelemesi için ana anahtar SQL veritabanı hizmeti tarafından kolaylık sağlaması için yönetilir. Kuruluşunuz ana anahtar üzerinde denetim yapmak istiyorsanız, anahtar deposu olarak Azure Key Vault] (Always-şifrelenmiş-Azure-Key-kasa-configure.md) kullanma seçeneği vardır. Kuruluşunuz, Azure Key Vault kullanarak, anahtar sağlama, döndürme ve izin denetimleri üzerinde kontrol eder. [BIR TDE ana anahtar türünün dönüşü veya değiştirilmesi](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) , yalnızca dek yeniden şifrelediği için hızlıdır. Güvenlik ve veri yönetimi arasında rol ayrımı olan kuruluşlar için, bir güvenlik yöneticisi Azure Key Vault içindeki TDE ana anahtarı için anahtar malzemesini sağlayabilir ve veritabanı yöneticisine bir sunucuda bekleyen şifreleme için kullanılacak Azure Key Vault bir anahtar tanımlayıcı sağlar. Key Vault, Microsoft 'un herhangi bir şifreleme anahtarını görmediğinden veya ayıklamayacak şekilde tasarlanmıştır. Ayrıca, kuruluşunuz için anahtarların merkezi bir yönetimini de alırsınız.

#### <a name="always-encrypted"></a>Always Encrypted

Always Encrypted ' de [iki anahtar hiyerarşisi](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) de vardır. hassas verilerin bir sütunu, bir AES 256 sütunlu şifreleme anahtarı (cek) tarafından şifrelenir ve bu da bir sütun ana anahtarı (CMK) tarafından şifrelenir. Always Encrypted için belirtilen istemci sürücülerinde CMKs 'in uzunluğu üzerinde hiçbir kısıtlama yoktur. CEK şifreli değeri veritabanında depolanır ve CMK, Windows sertifika deposu, Azure Key Vault veya donanım güvenlik modülü gibi güvenilir bir anahtar deposunda depolanır.

- Hem [cek hem de CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) döndürülebilir.
- CEK döndürme bir veri işlemi boyutudur ve şifrelenmiş sütunları içeren tabloların boyutuna bağlı olarak zaman yoğun olabilir. Bu nedenle, CEK roçlerinin uygun şekilde planlandığından dolayı.
- Ancak CMK döndürme, veritabanı performansını engellemez ve ayrılmış rollerle yapılabilir.

Aşağıdaki diyagramda Always Encrypted içindeki sütun ana anahtarlarına yönelik anahtar deposu seçenekleri gösterilmektedir

![Her zaman şifreli CMK mağaza sağlayıcıları](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Kuruluşum ve SQL veritabanı arasındaki trafiği nasıl iyileştiriyorum ve güvenli hale getirebilirim?

Kuruluşunuz ve SQL veritabanı arasındaki ağ trafiği genellikle genel ağ üzerinden yönlendirilir. Ancak, bu yolu iyileştirmenizi ve daha güvenli hale getirmeyi seçerseniz Azure ExpressRoute 'a bakabilirsiniz. ExpressRoute temelde Şirket ağınızı özel bir bağlantı üzerinden Azure platformuna genişletmenizi sağlar. Bunu yaptığınızda, genel Internet üzerinden geçmeyin. Ayrıca, genellikle genel İnternet üzerinden ilerleyenden daha hızlı ağ gecikmeleri ve çok daha hızlı hızlara çeviren daha yüksek güvenlik, güvenilirlik ve yönlendirme iyileştirmesi da alırsınız. Kuruluşunuz ve Azure arasında önemli bir veri öbeğini aktarmayı planlıyorsanız, ExpressRoute 'u kullanarak maliyet avantajları elde edebilirsiniz. Kuruluşunuzun Azure 'a bağlantısı için üç farklı bağlantı modeli arasından seçim yapabilirsiniz:

- [Bulut Exchange ortak konumu](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Herhangi iki nokta arasında](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Noktadan noktaya](../../expressroute/expressroute-connectivity-models.md#Ethernet)

ExpressRoute Ayrıca, ek ücret ödemeden satın aldığınız bant genişliği sınırına kadar en fazla 2x veri bloğu oluşturmanızı sağlar. Ayrıca, ExpressRoute kullanarak çapraz bölge bağlantısını yapılandırmak da mümkündür. ExpressRoute bağlantı sağlayıcılarının listesini görmek için bkz: [ExpressRoute Iş ortakları ve eşleme konumları](../../expressroute/expressroute-locations.md). Aşağıdaki makalelerde Express Route daha ayrıntılı olarak açıklanır:

- [Express Route 'a giriş](../../expressroute/expressroute-introduction.md)
- [Önkoşullar](../../expressroute/expressroute-prerequisites.md)
- [İş Akışları](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL veritabanı, herhangi bir düzenleme gereksinimleriyle uyumludur ve kendi Kuruluşumun uyumluluğuyla nasıl yardımcı olur?

SQL veritabanı, bir dizi mevzuata göre uyumlu değildir. SQL veritabanı tarafından karşılanan en son zorlukların kümesini görüntülemek için [Microsoft Güven Merkezi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ' ni ziyaret edin ve kuruluşunuz için önemli olan karmaşıklıkları Inceleyerek, SQL veritabanının uyumlu Azure hizmetleri altına dahil edilip edilmediğini öğrenin. SQL veritabanı, uyumlu bir hizmet olarak sertifikalı olsa da, kuruluşunuzun hizmetinin uyumluluğuna yardımcı olur ancak otomatik olarak garanti etmez.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Geçişten sonra akıllı veritabanı izleme ve bakım

Veritabanınızı SQL veritabanına geçirdikten sonra, veritabanınızı izlemek isteyeceksiniz (örneğin, kaynak kullanımının nasıl olduğu veya DBCC denetimleri) ve düzenli bakım gerçekleştirme (örneğin, dizinleri yeniden oluşturma veya yeniden düzenleme, istatistikler vb.). Neyse ki, uygulamanızın her zaman en iyi şekilde çalışması için, SQL veritabanı, geçmiş eğilimleri ve kayıtlı ölçümleri ve istatistikleri, veritabanınızı izlemenize ve korumanıza yardımcı olmak için akıllı olarak kullanır. Azure SQL veritabanı, bazı durumlarda yapılandırma ayarlarınıza bağlı olarak bakım görevlerini otomatik olarak gerçekleştirebilir. SQL veritabanında veritabanınızı izlemenin üç yönü vardır:

- Performans izleme ve iyileştirme.
- Güvenlik iyileştirmesi.
- Maliyet iyileştirmesi.

### <a name="performance-monitoring-and-optimization"></a>Performans izleme ve iyileştirme

Sorgu performans öngörüleri sayesinde, uygulamalarınızın en uygun düzeyde çalışmaya devam edebilmesi için veritabanı iş yükünüz için özel öneriler elde edebilirsiniz. Ayrıca, Bu önerilerin otomatik olarak uygulanması için de ayarlayabilirsiniz ve bakım görevlerini gerçekleştirmek zorunda değilsiniz. SQL Veritabanı Danışmanı ile, iş yükünüze göre otomatik olarak dizin önerileri uygulayabilirsiniz; bu otomatik ayarlama olarak adlandırılır. Öneriler, uygulama iş yükünüz olarak geliştikçe en ilgili önerileri size sağlar. Ayrıca, bu önerileri el ile gözden geçirme ve bunları sizin takdirinize göre uygulama seçeneğini de alırsınız.  

### <a name="security-optimization"></a>Güvenlik iyileştirmesi

SQL veritabanı, veri ve tehdit algılamalarınızın veritabanına olası bir iş parçacığı oluşturabilecek şüpheli veritabanı etkinliklerini belirlemek ve araştırmak üzere güvenli hale getirmenize yardımcı olmak için eyleme dönüştürülebilir güvenlik önerileri sağlar. [Güvenlik açığı değerlendirmesi](sql-vulnerability-assessment.md) , veritabanlarınızın güvenlik durumunu ölçeklendirerek izlemenizi ve güvenlik risklerini tanımlamanızı ve sizin tarafınızdan tanımlanan bir güvenlik taban çizgisinden göre kullanımını belirlemenizi sağlayan bir veritabanı tarama ve raporlama hizmetidir. Her taramadan sonra, eyleme dönüştürülebilir adımların ve düzeltme betiklerinin özelleştirilmiş bir listesi ve uyumluluk gereksinimlerini karşılamak için kullanılabilecek bir değerlendirme raporu sağlanır.

Azure Güvenlik Merkezi ile, pano genelinde güvenlik önerilerini belirler ve tek bir tıklama ile uygularsınız.

### <a name="cost-optimization"></a>Maliyet iyileştirmesi

Azure SQL platformu, sizin için maliyet iyileştirme seçeneklerini değerlendirmek ve önermek üzere bir sunucudaki veritabanları genelinde kullanım geçmişini analiz eder. Bu analizler genellikle uygulanabilir önerileri çözümlemek ve derlemek için bir Fortnight alır. Elastik havuz, bu seçeneklerden biridir. Öneri, portalda başlık olarak görünür:

![Elastik havuz önerileri](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Bu Analizi "Advisor" bölümünde da görüntüleyebilirsiniz:

![Elastik havuz önerileri-danışman](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>SQL veritabanı 'nda performans ve kaynak kullanımını izleme Nasıl yaparım?

SQL veritabanında, performansı izlemek ve uygun şekilde ayarlamak için platformun akıllı öngörülerini kullanabilirsiniz. Aşağıdaki yöntemleri kullanarak SQL veritabanı 'nda performans ve kaynak kullanımını izleyebilirsiniz:

#### <a name="azure-portal"></a>Azure portalı

Azure portal, veritabanını seçip Genel Bakış bölmesinde grafiğe tıklayarak bir veritabanının kullanımını gösterir. Grafiği CPU yüzdesi, DTU yüzdesi, veri GÇ yüzdesi, oturum yüzdesi ve veritabanı boyutu yüzdesi gibi birden çok ölçümü gösterecek şekilde değiştirebilirsiniz.

![İzleme grafiği](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![İzleme CHART2](./media/manage-data-after-migrating-to-database/chart.png)

Bu grafikten, uyarıları kaynağa göre de yapılandırabilirsiniz. Bu uyarılar, bir e-posta ile kaynak koşullarına yanıt vermenize, bir HTTPS/HTTP uç noktasına yazmaya veya bir eylem gerçekleştirmenize olanak tanır. Daha fazla bilgi için bkz. [uyarı oluşturma](alerts-insights-configure-portal.md).

#### <a name="dynamic-management-views"></a>Dinamik yönetim görünümleri

Son 14 günün geçmişini döndürmek için son saatten ve [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Sistem kataloğu görünümünden kaynak tüketimi istatistikleri geçmişini döndürmek üzere [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamik yönetim görünümünü sorgulayabilirsiniz.

#### <a name="query-performance-insight"></a>Sorgu Performansı İçgörüleri

[Sorgu performansı içgörüleri](query-performance-insight-use.md) , belirli bir veritabanı için en üstteki kaynak kullanan sorguların ve uzun süre çalışan sorguların geçmişini görmenizi sağlar. EN ıyı sorguları kaynak kullanımı, süre ve yürütme sıklığıyla hızlı bir şekilde belirleyebilirsiniz. Sorguları izleyebilir ve gerileme tespit edebilirsiniz. Bu özellik, veritabanı için [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 'un etkin ve etkin olmasını gerektirir.

![Sorgu Performansı İçgörüleri](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure Izleyici günlüklerinde Azure SQL Analytics (Önizleme)

[Azure izleyici günlükleri](../../azure-monitor/insights/azure-sql.md) , temel Azure SQL veritabanı performans ölçümlerini toplamanıza ve görselleştirmenize olanak tanır. bu sayede, çalışma alanı başına en fazla 150.000 veritabanı ve 5.000 SQL elastik havuzu destekler Bunu, bildirimleri izlemek ve almak için kullanabilirsiniz. SQL veritabanı ve elastik havuz ölçümlerini birden çok Azure aboneliği ve elastik havuzlarda izleyebilir ve uygulama yığınının her katmanında sorunları belirlemek için kullanılabilir.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Yaşıyorsanız performans sorunları: SQL veritabanı sorun giderme yöntemlerimin SQL Server farklıdır

Sorgu ve veritabanı performans sorunlarını tanılamak için kullanacağınız sorun giderme tekniklerinin büyük bir bölümü aynı kalır. Aynı veritabanı altyapısının tümü bulutu güçlendirir. Ancak platform-Azure SQL veritabanı, ' Intelligence ' içinde yerleşik olarak bulunur. Performans sorunlarını daha kolay bir şekilde gidermenize ve tanılamanıza yardımcı olabilir. Ayrıca, bu düzeltme eylemlerinin bazılarını sizin adınıza gerçekleştirebilir ve bazı durumlarda onları otomatik olarak yeniden düzeltir.

Performans sorunlarını giderme yaklaşımınız, [sorgu performansı içgörüleri (QPı)](query-performance-insight-use.md) ve [veritabanı Danışmanı](database-advisor-implement-performance-recommendations.md) gibi akıllı özellikleri kullanarak önemli ölçüde faydalanabilir ve bu nedenle metodolojide farklılık fark etmenize yardımcı olabilecek önemli ayrıntıları el ile yapmak zorunda değilsiniz. Platform, sizin için sabit bir iş yapar. Yani QPı olan bir örnek. QPı ile, sorgu düzeyine kadar tüm şekilde ayrıntıya gidebilir ve geçmiş eğilimlerini arayabilir ve tam olarak sorgu ne zaman gerilediğini belirleyebilirsiniz. Veritabanı Danışmanı, genel performansında eksik dizinler, dizin bırakma, sorgularınızın parametreleştirilmesi gibi genel performansı artırmanıza yardımcı olabilecek şeylere yönelik öneriler sağlar.

Performans sorunlarını giderme sayesinde, uygulamanın yalnızca uygulamanın mı yoksa veritabanı yedeklemesi mi olduğunu belirlemek önemlidir. Bu, uygulama performansınızı etkiliyor. Genellikle, performans sorunu uygulama katmanında yer alır. Bu, mimari veya veri erişim deseninin olabilir. Örneğin, ağ gecikme süresine duyarlı bir geveze uygulamanız olduğunu düşünün. Bu durumda uygulamanız, uygulama ile sunucu arasında ve çok yönlü bir ağ üzerinde ileri ve geri dönerek ("geveze") bir çok kısa istek olduğu için, bu gidiş dönüşlerin hızlı bir şekilde daha hızlı bir şekilde bir şekilde yapıldığından, uygulamanız vardır. Bu durumda performansı artırmak için [Batch sorgularını](performance-guidance.md#batch-queries)kullanabilirsiniz. Artık istekleriniz bir toplu işte işlendiğinden, toplu işlem gecenin size yardımcı olur; Bu nedenle, gidiş dönüş gecikmesini kesip uygulama performansınızı iyileştirmenize yardımcı olur.

Ayrıca, veritabanınızın genel performansına karşı bir düşüş fark ederseniz CPU, GÇ ve bellek tüketimini anlamak için [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ve dinamik yönetim görünümlerini [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) izleyebilirsiniz. Veritabanınız kaynakları kullandığından, performanstan etkilenmiş olabilirsiniz. Artan ve daraltma iş yükü taleplerine göre işlem boyutunu ve/veya hizmet katmanını değiştirmeniz gerekebilir.

Performans sorunlarını ayarlama hakkında kapsamlı bir öneriler kümesi için bkz.: [veritabanınızı ayarlama](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Uygun hizmet katmanını ve işlem boyutunu kullanmaktan Nasıl yaparım? emin olun

SQL veritabanı, temel, standart ve Premium çeşitli hizmet katmanları sunmaktadır. Her hizmet katmanı, söz konusu hizmet katmanına bağlı garantili öngörülebilir bir performans alırsınız. İş yükünüze bağlı olarak, kaynak kullanımınıza ait olduğunuz geçerli işlem boyutunun üst kısmında yer alan etkinlik sayısının ani olarak olması olabilir. Bu gibi durumlarda, herhangi bir ayarlamanın (örneğin, bir dizin ekleme veya değiştirme gibi) yardım edebilir. Sınır sorunlarını yaşamaya devam ediyorsanız, daha yüksek bir hizmet katmanına veya işlem boyutuna geçmeyi düşünün.

|**Hizmet katmanı**|**Yaygın kullanım örneği senaryoları**|
|---|---|
|**Temel**|En yüksek eşzamanlılık, ölçek ve performans gereksinimlerine sahip olmayan, el kullanıcılarına ve veritabanına sahip uygulamalar. |
|**Standart**|Düşük ila orta GÇ taleplerine bağlanmış, önemli ölçüde eşzamanlılık, ölçek ve performans gereksinimlerine sahip uygulamalar. |
|**Premium**|Çok sayıda eşzamanlı kullanıcı, yüksek CPU/bellek ve yüksek GÇ taleplerine sahip uygulamalar. Yüksek eşzamanlılık, yüksek aktarım hızı ve gecikme süresi duyarlı uygulamalar, Premium düzeyinden faydalanabilir. |
|||

Doğru işlem boyutunda olduğunuzdan emin olmak için, Nasıl yaparım? "SQL veritabanı 'nda performansı ve kaynak kullanımını izleme" bölümünde, yukarıdaki belirtilen yollarla sorgu ve veritabanı kaynak tüketiminizi izleyebilirsiniz. Sorgular/veritabanlarınızın, CPU/bellek üzerinde düzenli olarak çalışır şekilde çalıştığını bulmanız gerekir. daha yüksek bir işlem boyutuna kadar ölçeklendirmeyi deneyebilirsiniz. Benzer şekilde, yoğun saatleriniz sırasında bile, kaynakları çok sayıda kullanmaya gerek kalmaz; geçerli işlem boyutundan ölçeği azaltmayı göz önünde bulundurun.

SaaS uygulama düzeniniz veya veritabanı birleştirme senaryonuz varsa, maliyet iyileştirmesi için esnek havuz kullanmayı düşünün. Elastik havuz, Veritabanı konsolidasyonu ve maliyet iyileştirmesi elde etmenin harika bir yoludur. Elastik havuz kullanarak birden çok veritabanını yönetme hakkında daha fazla bilgi için bkz.: [havuzları ve veritabanlarını yönetme](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Veritabanı için veritabanı bütünlüğü denetimlerini hangi sıklıkta çalıştırmalıyım?

SQL veritabanı, belirli veri bozulması sınıflarının otomatik olarak ve veri kaybı olmadan işlemesini sağlayan bazı akıllı teknikler kullanır. Bu teknikler hizmette yerleşik olarak bulunur ve gerektiğinde hizmet tarafından yararlanılabilir. Düzenli olarak, hizmet üzerindeki veritabanı yedeklemeleriniz, geri yükleyerek ve DBCC CHECKDB 'nin üzerinde çalıştırılarak test edilir. Sorunlar varsa, SQL veritabanı tarafından önceden kullanılabilir. [Otomatik sayfa onarımı](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) , bozuk veya veri bütünlüğü sorunları olan sayfaları düzeltmek için yararlanılabilir. Veritabanı sayfaları her zaman sayfanın bütünlüğünü doğrulayan varsayılan sağlama TOPLAMı ayarıyla doğrulanır. SQL veritabanı, veritabanınızın veri bütünlüğünü önceden izler ve inceler ve sorunlar ortaya çıkarsa, en yüksek önceliğe sahip olarak bunları ele alınmaktadır. Bunlara ek olarak, sizin için isteğe bağlı olarak kendi bütünlük denetimlerini çalıştırmayı tercih edebilirsiniz.  Daha fazla bilgi için bkz. [SQL veritabanı 'Nda veri bütünlüğü](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Geçişten sonra veri taşıma

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Azure portal kullanarak verileri SQL veritabanından BACPAC dosyaları olarak dışarı ve içeri aktarma Nasıl yaparım?

- **Dışarı aktarma**: Azure SQL veritabanı 'nda veritabanınızı Azure Portal bacpac dosyası olarak dışarı aktarabilirsiniz.

   ![veritabanı dışarı aktarma](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Içeri aktarma**: ayrıca, Azure Portal kullanarak VERILERI BIR bacpac dosyası olarak Azure SQL veritabanı 'nda veritabanınıza aktarabilirsiniz.

   ![veritabanı içeri aktarma](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Nasıl yaparım? SQL veritabanı ile SQL Server arasında veri eşitlemesi

Bunu başarmanın birkaç yolu vardır:

- **[Veri eşitleme](sql-data-sync-data-sql-server-sql-database.md)** – bu özellik, birden çok SQL Server VERITABANı ve SQL veritabanı arasında veri ve veritabanlarını eşitlemenize yardımcı olur. SQL Server veritabanlarıyla eşitleme yapmak için, bir yerel bilgisayara veya bir sanal makineye eşitleme Aracısı yükleyip yapılandırmanız ve giden TCP bağlantı noktası 1433 ' u açmanız gerekir.
- **[Işlem çoğaltma](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – işlem çoğaltma ile verilerinizi bir SQL Server veritabanından Azure SQL veritabanı ile, yayımcı ve Azure SQL veritabanı gibi SQL Server örnek ile Azure SQL veritabanı arasında eşitleme yapabilirsiniz. Şimdilik yalnızca bu kurulum desteklenir. Verilerinizi bir SQL Server veritabanından Azure SQL 'e en az kapalı kalma süresiyle geçirme hakkında daha fazla bilgi için bkz. [Işlem çoğaltmasını kullanma](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Sonraki adımlar

[SQL veritabanı](sql-database-paas-overview.md)hakkında bilgi edinin.