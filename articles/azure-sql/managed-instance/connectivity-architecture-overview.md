---
title: Bağlantı mimarisi
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örneği iletişimi ve bağlantı mimarisi hakkında bilgi edinin ve bileşenlerin yönetilen örneğe trafiği nasıl yönlendirmiş olduğunu öğrenin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 58563629b30e7be764732a9810162e1a0b1931e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725845"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL Yönetilen Örneği için bağlantı mimarisi
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örneği ile iletişim açıklanır. Ayrıca, bağlantı mimarisini ve bileşenlerin yönetilen örneğe trafiği nasıl yönlendirdiğinden de açıklanmaktadır.  

SQL yönetilen örneği, Azure sanal ağının içine ve yönetilen örneklere ayrılmış alt ağa yerleştirilir. Bu dağıtım şunları sağlar:

- Güvenli bir özel IP adresi.
- Şirket içi bir ağı SQL yönetilen örneğine bağlama özelliği.
- SQL yönetilen örneği bağlantılı sunucuya veya başka bir şirket içi veri deposuna bağlama özelliği.
- SQL yönetilen örneğini Azure kaynaklarına bağlama özelliği.

## <a name="communication-overview"></a>İletişime genel bakış

Aşağıdaki diyagramda, SQL yönetilen örneğine bağlanan varlıklar gösterilmektedir. Ayrıca, yönetilen bir örnekle iletişim kurması gereken kaynakları gösterir. Diyagramın en altındaki iletişim işlemi, SQL yönetilen örneğine veri kaynakları olarak bağlanan müşteri uygulamalarını ve araçları temsil eder.  

![Bağlantı mimarisi varlıkları](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL yönetilen örneği bir hizmet olarak platform (PaaS) teklifi. Azure, bu hizmeti telemetri veri akışlarına göre yönetmek için otomatik aracıları (Yönetim, dağıtım ve bakım) kullanır. Azure yönetiminden sorumlu olduğundan, müşteriler SQL yönetilen örnek sanal küme makinelerine Uzak Masaüstü Protokolü (RDP) üzerinden erişemez.

Son kullanıcılar veya uygulamalar tarafından başlatılan bazı işlemler, platformla etkileşimde bulunmak için SQL yönetilen örneği gerektirebilir. Bir SQL yönetilen örnek veritabanının oluşturulması bir durumdur. Bu kaynak Azure portal, PowerShell, Azure CLı ve REST API aracılığıyla sunulur.

SQL yönetilen örneği, yedeklemeler için Azure depolama, telemetri için Azure Event Hubs, kimlik doğrulaması için Azure Active Directory (Azure AD), Saydam Veri Şifrelemesi için Azure Key Vault (TDE) ve güvenlik ve desteklenebilirlik özellikleri sağlayan birkaç Azure platform hizmeti olan Azure hizmetlerine bağımlıdır. SQL yönetilen örneği, bu hizmetlere bağlantılar oluşturur.

Tüm iletişimler şifrelenir ve sertifikalar kullanılarak imzalanır. İletişim kuran tarafların güvenilirliğini denetlemek için, SQL yönetilen örnek, sertifika iptal listeleri aracılığıyla bu sertifikaları sürekli olarak doğrular. Sertifikalar iptal edildiğinde, SQL yönetilen örnek, verileri korumak için bağlantıları kapatır.

## <a name="high-level-connectivity-architecture"></a>Üst düzey bağlantı mimarisi

Yüksek düzeyde, SQL yönetilen örneği bir hizmet bileşenleri kümesidir. Bu bileşenler, müşterinin sanal ağ alt ağı içinde çalışan ayrılmış bir yalıtılmış sanal makine kümesinde barındırılır. Bu makineler bir sanal küme oluşturur.

Bir sanal küme birden çok yönetilen örneği barındırabilirler. Gerekirse, müşteri alt ağdaki sağlanan örneklerin sayısını değiştirdiğinde küme otomatik olarak genişletilir veya anlaşmaları yapılır.

Müşteri Uygulamaları, SQL yönetilen örneğine bağlanabilir ve sanal ağ, eşlenen sanal ağ veya VPN ya da Azure ExpressRoute ile bağlı ağ içindeki veritabanlarını sorgulayabilir ve güncelleştirebilir. Bu ağ bir uç nokta ve özel bir IP adresi kullanmalıdır.  

![Bağlantı mimarisi diyagramı](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure yönetim ve Dağıtım Hizmetleri sanal ağ dışında çalışır. SQL yönetilen örneği ve Azure Hizmetleri, genel IP adreslerine sahip uç noktalara bağlanır. SQL yönetilen örneği bir giden bağlantı oluşturduğunda, alıcı son ağ adresi çevirisi (NAT), bağlantının bu genel IP adresinden geldiği gibi görünmesine neden olur.

Yönetim trafiği müşterinin sanal ağı üzerinden akar. Diğer bir deyişle, sanal ağın altyapısının öğeleri, örnek başarısız olur ve kullanılamaz hale getirerek Yönetim trafiğine zarar verebilir.

> [!IMPORTANT]
> Azure, müşteri deneyimini ve hizmet kullanılabilirliğini geliştirmek için Azure sanal ağ altyapısı öğelerinde bir ağ hedefi ilkesi uygular. İlke, SQL yönetilen örneğinin nasıl çalıştığını etkileyebilir. Bu platform mekanizması, ağ gereksinimlerini kullanıcılara saydam şekilde iletir. İlkenin ana hedefi, ağ yanlış yapılandırılmasını önlemektir ve normal SQL yönetilen örnek işlemlerini sağlamaktır. Yönetilen bir örneği sildiğinizde, ağ hedefi ilkesi de kaldırılır.

## <a name="virtual-cluster-connectivity-architecture"></a>Sanal küme bağlantısı mimarisi

SQL yönetilen örneği için bağlantı mimarisine daha ayrıntılı bir şekilde bakalım. Aşağıdaki diyagramda, sanal kümenin kavramsal düzeni gösterilmektedir.

![Sanal kümenin bağlantı mimarisi](./media/connectivity-architecture-overview/connectivityarch003.png)

İstemciler, form içeren bir ana bilgisayar adı kullanarak SQL yönetilen örneğine bağlanır `<mi_name>.<dns_zone>.database.windows.net` . Bu ana bilgisayar adı, ortak bir etki alanı adı sistemi (DNS) bölgesinde kayıtlı ve genel olarak çözümlenebildiği halde özel bir IP adresine çözümlenir. , `zone-id` Kümeyi oluşturduğunuzda otomatik olarak oluşturulur. Yeni oluşturulan bir küme, ikincil yönetilen bir örnek barındırıyorsa, birincil kümeyle bölge KIMLIĞINI paylaşır. Daha fazla bilgi için bkz. [otomatik yük devretme gruplarını kullanarak birden çok veritabanının saydam ve eşgüdümlü yük devretmesini etkinleştirme](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Bu özel IP adresi, SQL yönetilen örneği için iç yük dengeleyiciye aittir. Yük dengeleyici, trafiği SQL yönetilen örnek ağ geçidine yönlendirir. Aynı küme içinde birden çok yönetilen örnek çalıştırılabildiğinden ağ geçidi, trafiği doğru SQL altyapısı hizmetine yönlendirmek için SQL yönetilen örnek ana bilgisayar adını kullanır.

Yönetim ve Dağıtım Hizmetleri, dış yük dengeleyiciye eşlenen bir [Yönetim uç noktası](#management-endpoint) kullanarak SQL yönetilen örneği 'ne bağlanır. Trafik yalnızca SQL yönetilen örneğinin yönetim bileşenlerinin kullandığı önceden tanımlanmış bir bağlantı noktası kümesi üzerinde alındığında düğümlere yönlendirilir. Düğümlerdeki yerleşik bir güvenlik duvarı yalnızca Microsoft IP aralıklarından gelen trafiğe izin verecek şekilde ayarlanmıştır. Sertifikalar, Yönetim bileşenleri ile yönetim düzlemi arasındaki iletişimin hepsini karşılıklı olarak doğrular.

## <a name="management-endpoint"></a>Yönetim uç noktası

Azure, yönetim uç noktası kullanarak SQL yönetilen örneğini yönetir. Bu uç nokta, bir örneğin sanal kümesinin içindedir. Yönetim uç noktası, ağ düzeyindeki yerleşik bir güvenlik duvarı tarafından korunur. Uygulama düzeyinde, karşılıklı sertifika doğrulaması tarafından korunur. Uç noktanın IP adresini bulmak için, bkz. [Yönetim uç NOKTASıNıN IP adresini belirleme](management-endpoint-find-ip-address.md).

Bağlantılar SQL yönetilen örneği (yedeklemeler ve denetim günlükleri gibi) içinde başlatıldığında trafik yönetim uç noktasının genel IP adresinden başlar. Güvenlik duvarı kurallarını SQL yönetilen örneği için yalnızca IP adresine izin verecek şekilde ayarlayarak, SQL yönetilen örneğinden ortak hizmetlere erişimi sınırlayabilirsiniz. Daha fazla bilgi için bkz. [SQL yönetilen örneği yerleşik güvenlik duvarını doğrulama](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> SQL yönetilen örnek bölgesi içindeki Azure hizmetlerine giden trafik en iyi duruma getirilir ve bu nedenle yönetim uç noktası için genel IP adresine verilmez. Bu nedenle, genellikle depolama için, IP tabanlı güvenlik duvarı kuralları kullanmanız gerekiyorsa, hizmetin SQL yönetilen örneğinden farklı bir bölgede olması gerekir.

## <a name="service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması

Müşteri güvenliği ve yönetilebilirlik gereksinimlerini karşılamak için, SQL yönetilen örneği el ile hizmet destekli alt ağ yapılandırmasına geçiyor.

Hizmet destekli alt ağ yapılandırması sayesinde, Kullanıcı veri (TDS) trafiği üzerinde tam denetime sahiptir, ancak SQL yönetilen örneği bir SLA 'yı karşılamak için yönetim trafiğinin kesintisiz olarak akmasını sağlamak için sorumluluk kazanır.

Hizmet destekli alt ağ yapılandırması, otomatik ağ yapılandırma yönetimi sağlamak ve hizmet uç noktalarını etkinleştirmek için sanal ağ [alt ağı temsili](../../virtual-network/subnet-delegation-overview.md) özelliğinin üzerine oluşturulur. 

Hizmet uç noktaları, yedeklemeleri ve denetim günlüklerini tutan depolama hesaplarında sanal ağ güvenlik duvarı kurallarını yapılandırmak için kullanılabilir. Hizmet uç noktaları etkin olsa bile, müşterilerin hizmet uç noktaları üzerinde ek güvenlik sağlayan [özel bağlantı](../../private-link/private-link-overview.md) kullanması önerilir.

> [!IMPORTANT]
> Denetim düzlemi yapılandırma karmaşıklığı nedeniyle, hizmet destekli alt ağ yapılandırması Ulusal bulutlarda hizmet uç noktalarını etkinleştirmez. 

### <a name="network-requirements"></a>Ağ gereksinimleri

SQL yönetilen örneğini sanal ağın içindeki ayrılmış bir alt ağda dağıtın. Alt ağ şu özelliklere sahip olmalıdır:

- **Ayrılmış alt ağ:** SQL yönetilen örnek alt ağı kendisiyle ilişkili başka bir bulut hizmeti içeremez ve bir ağ geçidi alt ağı olamaz. Alt ağ, herhangi bir kaynak ancak SQL yönetilen örneği içeremez ve daha sonra alt ağdaki diğer kaynak türlerini ekleyemezsiniz.
- **Alt ağ temsili:** SQL yönetilen örnek alt ağının, kaynak sağlayıcısına atanmış olması gerekir `Microsoft.Sql/managedInstances` .
- **Ağ güvenlik grubu (NSG):** NSG 'nin SQL yönetilen örnek alt ağıyla ilişkilendirilmesi gerekir. SQL yönetilen örneği yeniden yönlendirme bağlantıları için yapılandırıldığında, bağlantı noktası 1433 ve bağlantı noktaları 11000-11999 üzerindeki trafiği filtreleyerek, SQL yönetilen örnek veri uç noktasına erişimi denetlemek için NSG kullanabilirsiniz. Hizmet, yönetim trafiğinin kesintisiz akışına izin vermek için gereken geçerli [kuralları](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) otomatik olarak sağlar ve saklar.
- **Kullanıcı tanımlı yol (UDR) tablosu:** Bir UDR tablosunun SQL yönetilen örnek alt ağıyla ilişkilendirilmesi gerekir. Şirket içi özel IP aralıklarına sahip trafiği, sanal ağ geçidi veya sanal ağ gereci (NVA) aracılığıyla bir hedef olarak yönlendirmek için yol tablosuna giriş ekleyebilirsiniz. Hizmet, kesintisiz yönetim trafiği akışına izin vermek için gerekli olan [girişleri](#user-defined-routes-with-service-aided-subnet-configuration) otomatik olarak sağlayacak ve güncel tutacaktır.
- **Yeterlı IP adresi:** SQL yönetilen örnek alt ağı en az 32 IP adresine sahip olmalıdır. Daha fazla bilgi için bkz. [SQL yönetilen örneği için alt ağın boyutunu belirleme](vnet-subnet-determine-size.md). Yönetilen örnekleri, [SQL yönetilen örneği için ağ gereksinimlerini](#network-requirements)karşılayacak şekilde yapılandırdıktan sonra [, var olan ağda](vnet-existing-add-subnet.md) dağıtabilirsiniz. Bunu yapamazsanız [yeni bir ağ ve alt ağ](virtual-network-subnet-create-arm-template.md) oluşturabilirsiniz.

> [!IMPORTANT]
> Yönetilen bir örnek oluşturduğunuzda, ağ kurulum üzerinde uyumsuz değişiklikler yapılmasını engellemek için alt ağa bir ağ hedefi ilkesi uygulanır. Son örnek alt ağdan kaldırıldıktan sonra, ağ hedefi ilkesi de kaldırılır. Aşağıdaki kurallar yalnızca bilgilendirme amaçlıdır ve ARM şablonu/PowerShell/CLı kullanarak bunları dağıtmamalıdır. En son resmi şablonu kullanmak istiyorsanız, her zaman [portaldan alabilirsiniz](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması ile zorunlu gelen güvenlik kuralları

| Name       |Bağlantı noktası                        |Protokol|Kaynak           |Hedef|Eylem|
|------------|----------------------------|--------|-----------------|-----------|------|
|yönetim  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |Mı ALT AĞı  |İzin Ver |
|            |9000, 9003                  |TCP     |Corpnetgördünüz       |Mı ALT AĞı  |İzin Ver |
|            |9000, 9003                  |TCP     |CorpnetPublic    |Mı ALT AĞı  |İzin Ver |
|mi_subnet   |Herhangi biri                         |Herhangi biri     |Mı ALT AĞı        |Mı ALT AĞı  |İzin Ver |
|health_probe|Herhangi biri                         |Herhangi biri     |AzureLoadBalancer|Mı ALT AĞı  |İzin Ver |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırması ile zorunlu giden güvenlik kuralları

| Name       |Bağlantı noktası          |Protokol|Kaynak           |Hedef|Eylem|
|------------|--------------|--------|-----------------|-----------|------|
|yönetim  |443, 12000    |TCP     |Mı ALT AĞı        |AzureCloud |İzin Ver |
|mi_subnet   |Herhangi biri           |Herhangi biri     |Mı ALT AĞı        |Mı ALT AĞı  |İzin Ver |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Hizmet destekli alt ağ yapılandırmasıyla Kullanıcı tanımlı rotalar

|Name|Adres ön eki|Sonraki atlama|
|----|--------------|-------|
|alt ağdan vnetlocal|Mı ALT AĞı|Sanal ağ|
|mi-13-64-11-sonrakii-Internet|13.64.0.0/11|İnternet|
|mi-13-104-14-sonrakii-Internet|13.104.0.0/14|İnternet|
|mi-20-33-16-sonrakii-Internet|20.33.0.0/16|İnternet|
|mi-20-34-15-sonrakii-Internet|20.34.0.0/15|İnternet|
|mi-20-36-14-sonrakii-Internet|20.36.0.0/14|İnternet|
|mi-20-40-13-sonrakii-Internet|20.40.0.0/13|İnternet|
|mi-20-48-12-sonrakii-Internet|20.48.0.0/12|İnternet|
|mi-20-64-10-sonrakii-Internet|20.64.0.0/10|İnternet|
|mi-20-128-16-sonrakii-Internet|20.128.0.0/16|İnternet|
|mi-20-135-16-sonrakii-Internet|20.135.0.0/16|İnternet|
|mi-20-136-16-sonrakii-Internet|20.136.0.0/16|İnternet|
|mi-20-140-15-sonrakii-Internet|20.140.0.0/15|İnternet|
|mi-20-143-16-sonrakii-Internet|20.143.0.0/16|İnternet|
|mi-20-144-14-sonrakii-Internet|20.144.0.0/14|İnternet|
|mi-20-150-15-sonrakii-Internet|20.150.0.0/15|İnternet|
|mi-20-160-12-sonrakii-Internet|20.160.0.0/12|İnternet|
|mi-20-176-14-sonrakii-Internet|20.176.0.0/14|İnternet|
|mi-20-180-14-sonrakii-Internet|20.180.0.0/14|İnternet|
|mi-20-184-13-sonrakii-Internet|20.184.0.0/13|İnternet|
|mi-20-192-10-sonrakii-Internet|20.192.0.0/10|İnternet|
|mi-40-64-10-sonrakii-Internet|40.64.0.0/10|İnternet|
|mi-51-4-15-sonrakii-Internet|51.4.0.0/15|İnternet|
|mi-51-8-16-sonrakii-Internet|51.8.0.0/16|İnternet|
|mi-51-10-15-sonrakii-Internet|51.10.0.0/15|İnternet|
|mi-51-18-16-sonrakii-Internet|51.18.0.0/16|İnternet|
|mi-51-51-16-sonrakii-Internet|51.51.0.0/16|İnternet|
|mi-51-53-16-sonrakii-Internet|51.53.0.0/16|İnternet|
|mi-51-103-16-sonrakii-Internet|51.103.0.0/16|İnternet|
|mi-51-104-15-sonrakii-Internet|51.104.0.0/15|İnternet|
|mi-51-132-16-sonrakii-Internet|51.132.0.0/16|İnternet|
|mi-51-136-15-sonrakii-Internet|51.136.0.0/15|İnternet|
|mi-51-138-16-sonrakii-Internet|51.138.0.0/16|İnternet|
|mi-51-140-14-sonrakii-Internet|51.140.0.0/14|İnternet|
|mi-51-144-15-sonrakii-Internet|51.144.0.0/15|İnternet|
|mi-52-96-12-sonrakii-Internet|52.96.0.0/12|İnternet|
|mi-52-112-14-sonrakii-Internet|52.112.0.0/14|İnternet|
|mi-52-125-16-sonrakii-Internet|52.125.0.0/16|İnternet|
|mi-52-126-15-sonrakii-Internet|52.126.0.0/15|İnternet|
|mi-52-130-15-sonrakii-Internet|52.130.0.0/15|İnternet|
|mi-52-132-14-sonrakii-Internet|52.132.0.0/14|İnternet|
|mi-52-136-13-sonrakii-Internet|52.136.0.0/13|İnternet|
|mi-52-145-16-sonrakii-Internet|52.145.0.0/16|İnternet|
|mi-52-146-15-sonrakii-Internet|52.146.0.0/15|İnternet|
|mi-52-148-14-sonrakii-Internet|52.148.0.0/14|İnternet|
|mi-52-152-13-sonrakii-Internet|52.152.0.0/13|İnternet|
|mi-52-160-11-sonrakii-Internet|52.160.0.0/11|İnternet|
|mi-52-224-11-sonrakii-Internet|52.224.0.0/11|İnternet|
|mi-64-4-18-sonrakii-Internet|64.4.0.0/18|İnternet|
|mi-65-52-14-sonrakii-Internet|65.52.0.0/14|İnternet|
|mi-66-119-144-20-sonrakii-Internet|66.119.144.0/20|İnternet|
|mi-70-37-17-sonrakii-Internet|70.37.0.0/17|İnternet|
|mi-70-37-128-18-sonrakii-Internet|70.37.128.0/18|İnternet|
|mi-91-190-216-21-sonrakii-Internet|91.190.216.0/21|İnternet|
|mi-94-245-64-18-sonrakii-Internet|94.245.64.0/18|İnternet|
|mi-103-9-8-22-sonrakii-Internet|103.9.8.0/22|İnternet|
|mi-103-25-156-24-sonrakii-Internet|103.25.156.0/24|İnternet|
|mi-103-25-157-24-sonrakii-Internet|103.25.157.0/24|İnternet|
|mi-103-25-158-23-sonrakii-Internet|103.25.158.0/23|İnternet|
|mi-103-36-96-22-sonrakii-Internet|103.36.96.0/22|İnternet|
|mi-103-255-140-22-sonrakii-Internet|103.255.140.0/22|İnternet|
|mi-104-40-13-sonrakii-Internet|104.40.0.0/13|İnternet|
|mi-104-146-15-sonrakii-Internet|104.146.0.0/15|İnternet|
|mi-104-208-13-sonrakii-Internet|104.208.0.0/13|İnternet|
|mi-111-221-16-20-sonrakii-Internet|111.221.16.0/20|İnternet|
|mi-111-221-64-18-sonrakii-Internet|111.221.64.0/18|İnternet|
|mi-129-75-16-sonrakii-Internet|129.75.0.0/16|İnternet|
|mi-131-107-16-sonrakii-Internet|131.107.0.0/16|İnternet|
|mi-131-253-1-24-sonrakii-Internet|131.253.1.0/24|İnternet|
|mi-131-253-3-24-sonrakii-Internet|131.253.3.0/24|İnternet|
|mi-131-253-5-24-sonrakii-Internet|131.253.5.0/24|İnternet|
|mi-131-253-6-24-sonrakii-Internet|131.253.6.0/24|İnternet|
|mi-131-253-8-24-sonrakii-Internet|131.253.8.0/24|İnternet|
|mi-131-253-12-22-sonrakii-Internet|131.253.12.0/22|İnternet|
|mi-131-253-16-23-sonrakii-Internet|131.253.16.0/23|İnternet|
|mi-131-253-18-24-sonrakii-Internet|131.253.18.0/24|İnternet|
|mi-131-253-21-24-sonrakii-Internet|131.253.21.0/24|İnternet|
|mi-131-253-22-23-sonrakii-Internet|131.253.22.0/23|İnternet|
|mi-131-253-24-21-sonrakii-Internet|131.253.24.0/21|İnternet|
|mi-131-253-32-20-sonrakii-Internet|131.253.32.0/20|İnternet|
|mi-131-253-61-24-sonrakii-Internet|131.253.61.0/24|İnternet|
|mi-131-253-62-23-sonrakii-Internet|131.253.62.0/23|İnternet|
|mi-131-253-64-18-sonrakii-Internet|131.253.64.0/18|İnternet|
|mi-131-253-128-17-sonrakii-Internet|131.253.128.0/17|İnternet|
|mi-132-245-16-sonrakii-Internet|132.245.0.0/16|İnternet|
|mi-134-170-16-sonrakii-Internet|134.170.0.0/16|İnternet|
|mi-134-177-16-sonrakii-Internet|134.177.0.0/16|İnternet|
|mi-137-116-15-sonrakii-Internet|137.116.0.0/15|İnternet|
|mi-137-135-16-sonrakii-Internet|137.135.0.0/16|İnternet|
|mi-138-91-16-sonrakii-Internet|138.91.0.0/16|İnternet|
|mi-138-196-16-sonrakii-Internet|138.196.0.0/16|İnternet|
|mi-139-217-16-sonrakii-Internet|139.217.0.0/16|İnternet|
|mi-139-219-16-sonrakii-Internet|139.219.0.0/16|İnternet|
|mi-141-251-16-sonrakii-Internet|141.251.0.0/16|İnternet|
|mi-146-147-16-sonrakii-Internet|146.147.0.0/16|İnternet|
|mi-147-243-16-sonrakii-Internet|147.243.0.0/16|İnternet|
|mi-150-171-16-sonrakii-Internet|150.171.0.0/16|İnternet|
|mi-150-242-48-22-sonrakii-Internet|150.242.48.0/22|İnternet|
|mi-157-54-15-sonrakii-Internet|157.54.0.0/15|İnternet|
|mi-157-56-14-sonrakii-Internet|157.56.0.0/14|İnternet|
|mi-157-60-16-sonrakii-Internet|157.60.0.0/16|İnternet|
|mi-167-105-16-sonrakii-Internet|167.105.0.0/16|İnternet|
|mi-167-220-16-sonrakii-Internet|167.220.0.0/16|İnternet|
|mi-168-61-16-sonrakii-Internet|168.61.0.0/16|İnternet|
|mi-168-62-15-sonrakii-Internet|168.62.0.0/15|İnternet|
|mi-191-232-13-sonrakii-Internet|191.232.0.0/13|İnternet|
|mi-192-32-16-sonrakii-Internet|192.32.0.0/16|İnternet|
|mi-192-48-225-24-sonrakii-Internet|192.48.225.0/24|İnternet|
|mi-192-84-159-24-sonrakii-Internet|192.84.159.0/24|İnternet|
|mi-192-84-160-23-sonrakii-Internet|192.84.160.0/23|İnternet|
|mi-192-197-157-24-sonrakii-Internet|192.197.157.0/24|İnternet|
|mi-193-149-64-19-sonrakii-Internet|193.149.64.0/19|İnternet|
|mi-193-221-113-24-sonrakii-Internet|193.221.113.0/24|İnternet|
|mi-194-69-96-19-sonrakii-Internet|194.69.96.0/19|İnternet|
|mi-194-110-197-24-sonrakii-Internet|194.110.197.0/24|İnternet|
|mi-198-105-232-22-sonrakii-Internet|198.105.232.0/22|İnternet|
|mi-198-200-130-24-sonrakii-Internet|198.200.130.0/24|İnternet|
|mi-198-206-164-24-sonrakii-Internet|198.206.164.0/24|İnternet|
|mi-199-60-28-24-sonrakii-Internet|199.60.28.0/24|İnternet|
|mi-199-74-210-24-sonrakii-Internet|199.74.210.0/24|İnternet|
|mi-199-103-90-23-sonrakii-Internet|199.103.90.0/23|İnternet|
|mi-199-103-122-24-sonrakii-Internet|199.103.122.0/24|İnternet|
|mi-199-242-32-20-sonrakii-Internet|199.242.32.0/20|İnternet|
|mi-199-242-48-21-sonrakii-Internet|199.242.48.0/21|İnternet|
|mi-202-89-224-20-sonrakii-Internet|202.89.224.0/20|İnternet|
|mi-204-13-120-21-sonrakii-Internet|204.13.120.0/21|İnternet|
|mi-204-14-180-22-sonrakii-Internet|204.14.180.0/22|İnternet|
|mi-204-79-135-24-sonrakii-Internet|204.79.135.0/24|İnternet|
|mi-204-79-179-24-sonrakii-Internet|204.79.179.0/24|İnternet|
|mi-204-79-181-24-sonrakii-Internet|204.79.181.0/24|İnternet|
|mi-204-79-188-24-sonrakii-Internet|204.79.188.0/24|İnternet|
|mi-204-79-195-24-sonrakii-Internet|204.79.195.0/24|İnternet|
|mi-204-79-196-23-sonrakii-Internet|204.79.196.0/23|İnternet|
|mi-204-79-252-24-sonrakii-Internet|204.79.252.0/24|İnternet|
|mi-204-152-18-23-sonrakii-Internet|204.152.18.0/23|İnternet|
|mi-204-152-140-23-sonrakii-Internet|204.152.140.0/23|İnternet|
|mi-204-231-192-24-sonrakii-Internet|204.231.192.0/24|İnternet|
|mi-204-231-194-23-sonrakii-Internet|204.231.194.0/23|İnternet|
|mi-204-231-197-24-sonrakii-Internet|204.231.197.0/24|İnternet|
|mi-204-231-198-23-sonrakii-Internet|204.231.198.0/23|İnternet|
|mi-204-231-200-21-sonrakii-Internet|204.231.200.0/21|İnternet|
|mi-204-231-208-20-sonrakii-Internet|204.231.208.0/20|İnternet|
|mi-204-231-236-24-sonrakii-Internet|204.231.236.0/24|İnternet|
|mi-205-174-224-20-sonrakii-Internet|205.174.224.0/20|İnternet|
|mi-206-138-168-21-sonrakii-Internet|206.138.168.0/21|İnternet|
|mi-206-191-224-19-sonrakii-Internet|206.191.224.0/19|İnternet|
|mi-207-46-16-sonrakii-Internet|207.46.0.0/16|İnternet|
|mi-207-68-128-18-sonrakii-Internet|207.68.128.0/18|İnternet|
|mi-208-68-136-21-sonrakii-Internet|208.68.136.0/21|İnternet|
|mi-208-76-44-22-sonrakii-Internet|208.76.44.0/22|İnternet|
|mi-208-84-21-sonrakii-Internet|208.84.0.0/21|İnternet|
|mi-209-240-192-19-sonrakii-Internet|209.240.192.0/19|İnternet|
|mi-213-199-128-18-sonrakii-Internet|213.199.128.0/18|İnternet|
|mi-216-32-180-22-sonrakii-Internet|216.32.180.0/22|İnternet|
|mi-216-220-208-20-sonrakii-Internet|216.220.208.0/20|İnternet|
|mi-23-96-13-sonrakii-Internet|23.96.0.0/13|İnternet|
|mi-42-159-16-sonrakii-Internet|42.159.0.0/16|İnternet|
|mi-51-13-17-sonrakii-Internet|51.13.0.0/17|İnternet|
|mi-51-107-16-sonrakii-Internet|51.107.0.0/16|İnternet|
|mi-51-116-16-sonrakii-Internet|51.116.0.0/16|İnternet|
|mi-51-120-16-sonrakii-Internet|51.120.0.0/16|İnternet|
|mi-51-120-128-17-sonrakii-Internet|51.120.128.0/17|İnternet|
|mi-51-124-16-sonrakii-Internet|51.124.0.0/16|İnternet|
|mi-102-37-18-sonrakii-Internet|102.37.0.0/18|İnternet|
|mi-102-133-16-sonrakii-Internet|102.133.0.0/16|İnternet|
|mi-199-30-16-20-sonrakii-Internet|199.30.16.0/20|İnternet|
|mi-204-79-180-24-sonrakii-Internet|204.79.180.0/24|İnternet|
||||

\* Mı alt ağı, x. x. x. x/y biçimindeki alt ağın IP adresi aralığını ifade eder. Bu bilgileri, Azure portal alt ağ özelliklerinde bulabilirsiniz.

\** Hedef adres Azure hizmetlerinden biri için ise, Azure trafiği Internet 'e yönlendirmek yerine doğrudan Azure omurga ağı üzerinden hizmete yönlendirir. Sanal ağın içinde bulunduğu Azure bölgesi veya Azure hizmeti örneğinin dağıtıldığı Azure bölgesi ne olursa olsun, Azure hizmetleri arasındaki trafik İnternet'i dolaşmaz. Daha fazla ayrıntı için [UDR belgeleri sayfasını](../../virtual-network/virtual-networks-udr-overview.md)inceleyin.

Ayrıca, sanal ağ geçidi veya sanal ağ gereci (NVA) aracılığıyla şirket içi özel IP aralıklarına sahip trafiği bir hedef olarak yönlendirmek için yol tablosuna giriş ekleyebilirsiniz.

Sanal ağ özel bir DNS içeriyorsa, özel DNS sunucusunun ortak DNS kayıtlarını çözümleyebilmesi gerekir. Azure AD kimlik doğrulaması gibi ek özelliklerin kullanılması ek FQDN 'lerin çözümlenmesini gerektirebilir. Daha fazla bilgi için bkz. [özel DNS ayarlama](custom-dns-configure.md).

### <a name="networking-constraints"></a>Ağ kısıtlamaları

**TLS 1,2, giden bağlantılarda zorlanır**: Microsoft 2020 ' de, tüm Azure hizmetlerinde hizmet içi trafik için MICROSOFT için TLS 1,2. Azure SQL yönetilen örneği için, bu, SQL Server ile çoğaltma ve bağlı sunucu bağlantıları için kullanılan giden bağlantılarda TLS 1,2 ile sonuçlandı. SQL yönetilen örneği ile 2016 'den eski SQL Server sürümlerini kullanıyorsanız, lütfen [TLS 1,2 özgü güncelleştirmelerin](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) uygulandığından emin olun.

Şu sanal ağ özellikleri şu anda SQL yönetilen örneği ile *desteklenmiyor* :

- **Microsoft eşlemesi**: ExpressRoute bağlantı hatları üzerinde [Microsoft EŞLEMESINI](../../expressroute/expressroute-faqs.md#microsoft-peering) etkinleştirme, SQL yönetilen örneğinin bulunduğu bir sanal ağ ile doğrudan veya geçişli bir şekilde etkinleştiriliyor, sanal ağ içindeki SQL yönetilen örnek bileşenleri ve bağlı olduğu hizmetler arasındaki trafik akışını etkiler ve kullanılabilirlik sorunlarına neden olur. Microsoft eşlemesi zaten etkinleştirilmiş olan sanal ağa SQL yönetilen örnek dağıtımları başarısız olması beklenir.
- **Küresel sanal ağ eşlemesi**: Azure bölgelerinde [sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md) bağlantısı, 9/22/2020 ' dan önce oluşturulan alt ağlara yerleştirilmiş SQL yönetilen örnekleri için çalışmaz.
- **AzurePlatformDNS**: platform DNS çözümlemesini engellemek için AzurePlatformDNS [HIZMETI etiketinin](../../virtual-network/service-tags-overview.md) kullanılması SQL yönetilen örneği kullanılamıyor olarak işleyebilir. SQL yönetilen örneği, altyapı içinde DNS çözümlemesi için müşteri tanımlı DNS 'i desteklese de platform için platform DNS işlemleri için bir bağımlılık vardır.
- **NAT ağ geçidi**: belirli BIR genel IP adresiyle giden bağlantıyı denetlemek Için [Azure sanal ağ NAT](../../virtual-network/nat-overview.md) kullanmak, SQL yönetilen örneği kullanılamaz hale gelirse. SQL yönetilen örnek hizmeti şu anda, sanal ağ NAT ile gelen ve giden akışların birlikte bulunmasını sağlamayan temel yük dengeleyicinin kullanımıyla sınırlıdır.
- **Azure sanal ağ Için IPv6**: [çift yığın IPv4/ıPV6 sanal ağlarına](../../virtual-network/ipv6-overview.md) SQL yönetilen örneği dağıtmanın başarısız olması beklenir. IPv6 adresi öneklerini içeren ağ güvenlik grubu (NSG) veya yol tablosu (UDR) SQL yönetilen örnek alt ağına ilişkilendirirken veya zaten yönetilen örnek alt ağıyla ilişkilendirilmiş olan NSG 'ye veya UDR 'ye IPv6 adres öneklerini eklemek, SQL yönetilen örneği kullanılamaz hale gelirse. NSG ve UDR içeren bir alt ağa yönelik SQL yönetilen örnek dağıtımları, zaten IPv6 ön eklerinin başarısız olması beklenir.

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure SQL yönetilen örneği nedir?](sql-managed-instance-paas-overview.md).
- [Yeni bir Azure sanal ağını](virtual-network-subnet-create-arm-template.md) veya SQL yönetilen örneği dağıtabileceğiniz [mevcut bir Azure sanal ağını](vnet-existing-add-subnet.md) ayarlamayı öğrenin.
- SQL yönetilen örneği dağıtmak istediğiniz [alt ağın boyutunu hesaplayın](vnet-subnet-determine-size.md) .
- Yönetilen bir örnek oluşturmayı öğrenin:
  - [Azure Portal](instance-create-quickstart.md).
  - [PowerShell](scripts/create-configure-managed-instance-powershell.md)kullanarak.
  - [Azure Resource Manager şablonu](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)kullanarak.
  - [Bir Azure Resource Manager şablonu kullanarak (SSMS ile birlikte bulunan JumpBox kullanarak)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).