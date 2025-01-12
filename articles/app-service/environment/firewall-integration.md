---
title: Giden trafiği kilitle
description: App Service ortamından giden trafiğin güvenliğini sağlamak için Azure Güvenlik Duvarı ile tümleştirmeyi öğrenin.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594121"
---
# <a name="locking-down-an-app-service-environment"></a>App Service Ortamı kilitleme

App Service Ortamı (Ao), düzgün bir şekilde çalışması için erişim gerektiren sayıda dış bağımlılıklara sahiptir. ASE, müşteri Azure sanal ağı 'nda (VNet) bulunur. Müşteriler, VNet 'ten tüm çıkışları kilitlemek isteyen müşteriler için bir sorun olan ASE bağımlılık trafiğine izin vermelidir.

Bir AI 'yi yönetmek için kullanılan bazı gelen uç noktaları vardır. Gelen yönetim trafiği bir güvenlik duvarı cihazından gönderilemez. Bu trafiğin kaynak adresleri bilinmektedir ve [App Service ortamı yönetim adresleri](./management-addresses.md) belgesinde yayımlanır. Ayrıca, gelen trafiğin güvenliğini sağlamak için ağ güvenlik grupları (NSG 'ler) ile birlikte kullanılabilecek AppServiceManagement adlı bir hizmet etiketi de vardır.

ASE giden bağımlılıkları, bunların arkasında statik adresler bulunmayan FQDN 'Ler ile neredeyse tamamen tanımlıdır. Statik adreslerin olmaması, ağ güvenlik gruplarının bir ASE 'den giden trafiği kilitlemek için kullanılamayacağı anlamına gelir. Adresler, geçerli çözünürlüğe göre kuralları ayarlayamayacak ve bunları NSG 'ler oluşturmak için kullanabileceğiniz kadar sık değişir. 

Giden adreslerin güvenliğini sağlamaya yönelik çözüm, etki alanı adlarına göre giden trafiği denetleyesağlayan bir güvenlik duvarı cihazının kullanılmasına yol açabilir. Azure Güvenlik Duvarı, giden HTTP ve HTTPS trafiğini hedefin FQDN 'sine göre kısıtlayabilir.  

## <a name="system-architecture"></a>Sistem mimarisi

Bir güvenlik duvarı cihazından giden trafik ile bir AKEN dağıtmak, AKEN alt ağında yolların değiştirilmesini gerektirir. Rotalar bir IP düzeyinde çalışır. Rotalarınızı tanımlamaya özen yoksa, TCP yanıt trafiğini başka bir adresten kaynağa zorlayabilirsiniz. Yanıt adresiniz, ' e gönderilen adres trafiğinden farklı olduğunda, soruna asimetrik yönlendirme adı verilir ve TCP 'nin bozulmasına neden olur.

ATıCı 'e gelen trafiğin trafikle aynı şekilde yanıt vermesi için tanımlanan yollar olmalıdır. Yolların gelen yönetim istekleri ve gelen uygulama istekleri için tanımlanması gerekir.

Ao 'dan gelen ve giden trafik aşağıdaki kurallara göre uymalıdır

* Azure SQL, depolama ve Olay Hub 'ına giden trafik, bir güvenlik duvarı cihazının kullanımıyla desteklenmez. Bu trafiğin doğrudan bu hizmetlere gönderilmesi gerekir. Bunu yapmanın yolu, bu üç hizmet için hizmet uç noktalarını yapılandırmaktır. 
* Gelen yönetim trafiğini geldiği yerden geri gönderen yol tablosu kuralları tanımlanmalıdır.
* Gelen uygulama trafiğini geldiği yerden geri gönderen yol tablosu kuralları tanımlanmalıdır. 
* Ao 'dan çıkan diğer tüm trafik, bir yol tablosu kuralıyla güvenlik duvarı cihazınıza gönderilebilir.

![Azure Güvenlik Duvarı bağlantı akışı ile aşırı][5]

## <a name="locking-down-inbound-management-traffic"></a>Gelen yönetim trafiğini kilitleme

Ao alt ağına atanmış bir NSG zaten yoksa, bir tane oluşturun. NSG içinde, 454, 455 bağlantı noktalarında AppServiceManagement adlı hizmet etiketinden gelen trafiğe izin vermek için ilk kuralı ayarlayın. AppServiceManagement etiketinden erişime izin verme kuralı, genel IP 'Lerde ATıCı 'nizi yönetmek için gerekli olan tek şeydir. Bu hizmet etiketinin arkasındaki adresler yalnızca Azure App Service yönetmek için kullanılır. Bu bağlantılar üzerinden akan yönetim trafiği, kimlik doğrulama sertifikalarıyla şifrelenir ve güvenlidir. Bu kanaldaki tipik trafik, müşteri tarafından başlatılan komutlar ve sistem durumu araştırmaları gibi şeyleri içerir. 

Portal üzerinden yeni bir alt ağ içeren ASE 'ler, AppServiceManagement etiketi için izin verme kuralını içeren bir NSG ile yapılır.  

ATıCı 'niz Ayrıca 16001 numaralı bağlantı noktasındaki Load Balancer etiketinden gelen isteklere izin vermelidir. 16001 numaralı bağlantı noktası üzerindeki Load Balancer istekleri, Load Balancer ve Ao ön uçları arasında canlı olarak denetimleri saklar. 16001 numaralı bağlantı noktası engellenirse, Ao 'niz sağlıksız duruma geçer.

## <a name="configuring-azure-firewall-with-your-ase"></a>Azure Güvenlik duvarını AŞIRLE yapılandırma 

Azure Güvenlik Duvarı ile mevcut Ao 'ınızdan çıkış kilitlemeyi kilitleme adımları şunlardır:

1. Hizmet uç noktalarını, AI alt ağınızda SQL, depolama ve Olay Hub 'ına etkinleştirin. Hizmet uç noktalarını etkinleştirmek için, ağ portalı > alt ağlarına gidin ve hizmet uç noktaları açılan menüsünde Microsoft. EventHub, Microsoft. SQL ve Microsoft. Storage ' ı seçin. Azure SQL için etkin hizmet uç noktaları varsa, uygulamalarınızın sahip olduğu tüm Azure SQL bağımlılıkları da hizmet uç noktaları ile yapılandırılmalıdır. 

   ![hizmet uç noktalarını seçin][2]
  
1. ASE 'nizin bulunduğu VNet 'te AzureFirewallSubnet adlı bir alt ağ oluşturun. Azure Güvenlik duvarını oluşturmak için [Azure Güvenlik Duvarı belgelerindeki](../../firewall/index.yml) yönergeleri izleyin.

1. Azure Güvenlik Duvarı Kullanıcı arabirimi > kuralları > uygulama kuralı koleksiyonu ' ndan uygulama kuralı koleksiyonu Ekle ' yi seçin. Ad, öncelik ve Izin ver ayarla ' yı belirtin. FQDN etiketleri bölümünde bir ad belirtin, kaynak adreslerini * olarak ayarlayın ve App Service Ortamı FQDN etiketini ve Windows Update seçin. 
   
   ![Uygulama kuralı ekle][1]
   
1. Azure Güvenlik Duvarı Kullanıcı arabirimi > kuralları > ağ kuralı koleksiyonu ' ndan ağ kuralı koleksiyonu Ekle ' yi seçin. Ad, öncelik ve Izin ver ayarla ' yı belirtin. IP adresleri altındaki kurallar bölümünde, bir ad girin, **herhangi** bir protokol seçin, * kaynak ve hedef adresleri ayarlayın ve bağlantı noktalarını 123 olarak ayarlayın. Bu kural, sistemin NTP kullanarak saat eşitlemesi gerçekleştirmesini sağlar. Herhangi bir sistem sorununu değerlendirmenize yardımcı olmak için bağlantı noktası 12000 ile aynı şekilde başka bir kural oluşturun. 

   ![NTP ağ kuralı ekle][3]
   
1. Azure Güvenlik Duvarı Kullanıcı arabirimi > kuralları > ağ kuralı koleksiyonu ' ndan ağ kuralı koleksiyonu Ekle ' yi seçin. Ad, öncelik ve Izin ver ayarla ' yı belirtin. Hizmet etiketleri altındaki kurallar bölümünde, bir ad girin, **herhangi** bir protokol seçin, * kaynak adreslerini ayarlayın, AzureMonitor bir hizmet etiketi seçin ve bağlantı noktalarını 80, 443 olarak ayarlayın. Bu kural, sistemin sistem durumu ve ölçüm bilgileriyle Azure Izleyici sağlamasına izin verir.

   ![NTP hizmeti etiketi ağ kuralı ekle][6]
   
1. Bir sonraki Internet duraklı [App Service ortamı yönetim adreslerinden]( ./management-addresses.md) yönetim adresleriyle bir yol tablosu oluşturun. Asimetrik yönlendirme sorunlarından kaçınmak için yol tablosu girdileri gereklidir. IP adresi bağımlılıklarında aşağıda belirtilen IP adresi bağımlılıkları için bir sonraki Internet duraklı yollar ekleyin. 0.0.0.0/0 için yol tablonuza bir sonraki atlamada Azure Güvenlik Duvarı özel IP adresiniz olacak şekilde bir Sanal Gereç yolu ekleyin. 

   ![Rota tablosu oluşturma][4]
   
1. Oluşturduğunuz yol tablosunu as alt ağına atayın.

#### <a name="deploying-your-ase-behind-a-firewall"></a>ATıCı 'nizi bir güvenlik duvarının arkasında dağıtma

ATıCı 'nizi bir güvenlik duvarının arkasında dağıtma adımları, bir Azure güvenlik duvarıyla, AI alt ağını oluşturmanız ve ardından önceki adımları uygulamanız gerekir. Önceden var olan bir alt ağda ATıCı 'nizi oluşturmak için, [bir kaynak yöneticisi şablonuyla atıcı 'Nizi oluşturma](./create-from-template.md)konusunda açıklandığı gibi bir kaynak yöneticisi şablonu kullanmanız gerekir.

## <a name="application-traffic"></a>Uygulama trafiği 

Yukarıdaki adımlar, ASE 'nizin sorunsuz bir şekilde çalışmasına olanak sağlayacak. Yine de uygulama gereksinimlerinizi karşılayacak şeyler yapılandırmanız gerekir. Bir Ao 'da Azure Güvenlik Duvarı ile yapılandırılan uygulamalar için iki sorun vardır.  

- Uygulama bağımlılıklarının Azure Güvenlik Duvarı 'na veya yol tablosuna eklenmesi gerekir. 
- Asimetrik yönlendirme sorunlarından kaçınmak için uygulama trafiği için yolların oluşturulması gerekir

Uygulamalarınızın bağımlılıkları varsa, bunların Azure güvenlik duvarınızdan eklenmesi gerekir. Diğer her şey için HTTP/HTTPS trafiğine ve ağ kurallarına izin vermek üzere uygulama kuralları oluşturun. 

Uygulamanız için gelen trafiğin geldiği adres aralığını biliyorsanız, bunu Ao alt ağına atanan yol tablosuna ekleyebilirsiniz. Adres aralığı büyükse veya belirtilmemişse, yol tablonuza eklemek için bir adres sağlamak üzere Application Gateway gibi bir ağ gerecini kullanabilirsiniz. ILB Ade Application Gateway yapılandırma hakkında ayrıntılı bilgi için, [ILB atıcı 'nizi bir Application Gateway tümleştirerek](./integrate-with-application-gateway.md) edinin

Bu Application Gateway kullanımı, sisteminizi yapılandırmaya yönelik yalnızca bir örnektir. Bu yolu izledikten sonra, Application Gateway gönderilen yanıt trafiğinin doğrudan gidebilmesi için ATıCı alt ağ yolu tablosuna bir yol eklemeniz gerekir. 

## <a name="logging"></a>Günlüğe Kaydetme 

Azure Güvenlik Duvarı, Azure depolama, Olay Hub 'ı veya Azure Izleyici günlüklerine Günlükler gönderebilir. Uygulamanızı desteklenen herhangi bir hedefle tümleştirmek için Azure Güvenlik Duvarı portalı > tanılama günlükleri ' ne gidin ve istediğiniz hedefe yönelik günlükleri etkinleştirin. Azure Izleyici günlükleri ile tümleştirirseniz, Azure Güvenlik Duvarı 'na gönderilen tüm trafik için günlüğe kaydetmeyi görebilirsiniz. Reddedilen trafiği görmek için Log Analytics çalışma alanı portalınızı > günlüklerine açın ve şu şekilde bir sorgu girin: 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Azure Güvenlik duvarını Azure Izleyici günlükleriyle tümleştirmek, uygulama bağımlılıklarının tümünün farkında olmadığında ilk olarak bir uygulama çalışırken yararlıdır. Azure izleyici günlükleri hakkında daha fazla bilgi edinmek için [Azure izleyici 'de günlük verilerini analiz](../../azure-monitor/logs/log-query-overview.md)edebilirsiniz.
 
## <a name="dependencies"></a>Bağımlılıklar

Aşağıdaki bilgiler yalnızca Azure Güvenlik Duvarı dışında bir güvenlik duvarı gereci yapılandırmak istiyorsanız gereklidir. 

- Hizmet uç noktası özellikli Hizmetleri, hizmet uç noktaları ile yapılandırılmalıdır.
- IP adresi bağımlılıkları HTTP/sn olmayan trafiğe yöneliktir (TCP ve UDP trafiği)
- FQDN HTTP/HTTPS uç noktaları, güvenlik duvarı cihazınıza yerleştirilebilir.
- Joker karakter HTTP/HTTPS uç noktaları, bir dizi niteleyicilere göre ASE 'niz ile değişebilen bağımlılıklardır. 
- Linux bağımlılıkları yalnızca ASE 'nize Linux uygulamaları dağıtıyorsanız bir sorun teşkil etmez. Linux uygulamalarını Ao uygulamanıza dağıtmayın, bu adreslerin güvenlik duvarınızdan eklenmesine gerek yoktur. 

#### <a name="service-endpoint-capable-dependencies"></a>Hizmet uç noktası özellikli bağımlılıklar 

| Uç Nokta |
|----------|
| Azure SQL |
| Azure Depolama |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP adresi bağımlılıkları

| Uç Nokta | Ayrıntılar |
|----------| ----- |
| \*: 123 | NTP saat denetimi. Trafik, 123 numaralı bağlantı noktasında birden çok uç noktaya denetlenir |
| \*: 12000 | Bu bağlantı noktası, bazı sistem izleme için kullanılır. Engellenirse, bazı sorunlar önceliklendirme daha zor olacaktır, ancak ASE 'niz çalışmaya devam edecektir |
| 40.77.24.27:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 40.77.24.27:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.90.249.229:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.90.249.229:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 104.45.230.69:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 104.45.230.69:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.82.184.151:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.82.184.151:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |

Azure Güvenlik Duvarı ile, aşağıdaki her şeyi, FQDN etiketleriyle yapılandırılmış şekilde otomatik olarak alırsınız. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS bağımlılıkları 

| Uç Nokta |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Joker karakter HTTP/HTTPS bağımlılıkları 

| Uç Nokta |
|----------|
|gr-prod- \* . cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
| \*. identity.azure.net:443 |
| \*. ctldl.windowsupdate.com:80 |
| \*. ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux bağımlılıkları 

| Uç Nokta |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*. cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov bağımlılıklar

US Gov bölgelerindeki ASE 'ler için, ASE 'niz ile bir Azure Güvenlik duvarı yapılandırmak için bu belgenin [ASE Ile Azure Güvenlik Duvarı 'Nı yapılandırma](#configuring-azure-firewall-with-your-ase) bölümündeki yönergeleri izleyin.

US Gov içinde Azure Güvenlik Duvarı dışında bir cihaz kullanmak istiyorsanız 

* Hizmet uç noktası özellikli Hizmetleri, hizmet uç noktaları ile yapılandırılmalıdır.
* FQDN HTTP/HTTPS uç noktaları, güvenlik duvarı cihazınıza yerleştirilebilir.
* Joker karakter HTTP/HTTPS uç noktaları, bir dizi niteleyicilere göre ASE 'niz ile değişebilen bağımlılıklardır.

Linux, US Gov bölgelerinde kullanılamaz ve bu nedenle isteğe bağlı bir yapılandırma olarak listelenmez.

#### <a name="service-endpoint-capable-dependencies"></a>Hizmet uç noktası özellikli bağımlılıklar ####

| Uç Nokta |
|----------|
| Azure SQL |
| Azure Depolama |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP adresi bağımlılıkları

| Uç Nokta | Ayrıntılar |
|----------| ----- |
| \*: 123 | NTP saat denetimi. Trafik, 123 numaralı bağlantı noktasında birden çok uç noktaya denetlenir |
| \*: 12000 | Bu bağlantı noktası, bazı sistem izleme için kullanılır. Engellenirse, bazı sorunlar önceliklendirme daha zor olacaktır, ancak ASE 'niz çalışmaya devam edecektir |
| 40.77.24.27:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 40.77.24.27:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.90.249.229:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.90.249.229:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 104.45.230.69:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 104.45.230.69:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.82.184.151:80 | AI sorunlarını izlemek ve uyarmak için gereklidir |
| 13.82.184.151:443 | AI sorunlarını izlemek ve uyarmak için gereklidir |

#### <a name="dependencies"></a>Bağımlılıklar ####

| Uç Nokta |
|----------|
| \*. ctldl.windowsupdate.com:80 |
| \*. management.usgovcloudapi.net:80 |
| \*. update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*. management.usgovcloudapi.net:443 |
|\*. update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
