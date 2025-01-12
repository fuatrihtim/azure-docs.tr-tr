---
title: Azure Cloud Services (klasik) def. WebRole şeması | Microsoft Docs
description: Azure Web rolü, ASP.NET, PHP, WCF ve FastCGI destekleyici Web uygulaması programlaması için özelleştirilir. Bir Web rolünün hizmet tanımı öğeleri hakkında bilgi edinin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3c5811649d7d6c0aa1e90ed34c61be6a7f9339f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743160"
---
# <a name="azure-cloud-services-classic-definition-webrole-schema"></a>Azure Cloud Services (klasik) tanım WebRole şeması

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Azure Web rolü, IIS 7 tarafından desteklenen ASP.NET, PHP, Windows Communication Foundation ve FastCGI gibi Web uygulaması programlamasında özelleştirilmiş bir roldür.

Hizmet tanım dosyası için varsayılan uzantı. csdef ' dir.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Web rolü için temel hizmet tanımı şeması  
Bir Web rolü içeren bir hizmet tanımı dosyasının temel biçimi aşağıdaki gibidir.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Şema öğeleri  
Hizmet tanımı dosyası, bu konunun sonraki bölümlerinde ayrıntılı olarak açıklanan bu öğeleri içerir:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Ayar](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Uç Noktalar](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[Instanceınputendpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Sertifikalar](#Certificates)

[Sertifika](#Certificate)

[İşlemlerinin](#Imports)

[İçeri Aktar](#Import)

[Çalışma Zamanı](#Runtime)

[Ortam](#Environment)

[Değişken](#Variable)

[Roleınstancevalue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Siteler](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bağlamalar](#Bindings)

[Bağlama](#Binding)

[Başlangıç](#Startup)

[Görev](#Task)

[İçerik](#Contents)

[İçerik](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a> WebRole  
`WebRole`Öğesi, IIS 7 ve ASP.NET tarafından desteklendiği şekilde Web uygulaması programlama için özelleştirilmiş bir rol tanımlar. Bir hizmet, sıfır veya daha fazla web rolü içerebilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `WebRole` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Web rolünün adı. Rolün adı benzersiz olmalıdır.|  
|enableNativeCodeExecution|boolean|İsteğe bağlı. Varsayılan değer `true` ; yerel kod yürütme ve tam güven varsayılan olarak etkindir. `false`Web rolü için yerel kod yürütmeyi devre dışı bırakmak üzere bu özniteliği olarak ayarlayın ve bunun yerine Azure kısmi güven kullanın.|  
|VMSize|string|İsteğe bağlı. Role ayrılan sanal makinenin boyutunu değiştirmek için bu değeri ayarlayın. `Small` varsayılan değerdir. Daha fazla bilgi için bkz. [sanal makine boyutları Cloud Services](cloud-services-sizes-specs.md).|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings  
`ConfigurationSettings`Öğesi, bir Web rolü için yapılandırma ayarları koleksiyonunu açıklar. Bu öğe, `Setting` öğesinin üst öğesidir.

##  <a name="setting"></a><a name="Setting"></a> Ayarlanmasını  
`Setting`Öğesi bir rol örneği için yapılandırma ayarını belirten bir ad ve değer çiftini tanımlar.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Setting` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Yapılandırma ayarı için benzersiz bir ad.|  

Bir rolün yapılandırma ayarları, hizmet tanım dosyasında belirtilen ve hizmet yapılandırma dosyasında ayarlanan ad ve değer çiftleridir.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources  
`LocalResources`Öğesi, bir Web rolü için yerel depolama kaynakları koleksiyonunu açıklar. Bu öğe, `LocalStorage` öğesinin üst öğesidir.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage  
`LocalStorage`Öğesi, çalışma zamanında hizmet için dosya sistemi alanı sağlayan bir yerel depolama kaynağı tanımlar. Rol, sıfır veya daha fazla yerel depolama kaynağı tanımlayabilir.

> [!NOTE]
>  `LocalStorage`Öğesi, `WebRole` Azure SDK 'sının önceki sürümleriyle uyumluluğu desteklemek için öğesinin bir alt öğesi olarak görünebilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `LocalStorage` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Yerel depo için benzersiz bir ad.|  
|cleanOnRoleRecycle|boolean|İsteğe bağlı. Rol yeniden başlatıldığında yerel deponun temizlenmesinin gerekip gerekmediğini gösterir. Varsayılan değer `true` olarak belirlenmiştir.|  
|Sizeınmb|int|İsteğe bağlı. Yerel depo için ayrılacak, MB cinsinden istenen depolama alanı miktarı. Belirtilmemişse, ayrılan varsayılan depolama alanı 100 MB 'tır. Ayrılabilen en az depolama alanı miktarı 1 MB 'tır.<br /><br /> Yerel kaynakların en büyük boyutu sanal makine boyutuna bağımlıdır. Daha fazla bilgi için bkz. [sanal makine boyutları Cloud Services](cloud-services-sizes-specs.md).|  
  
Yerel depolama kaynağına ayrılan dizinin adı, Name özniteliği için belirtilen değere karşılık gelir.

##  <a name="endpoints"></a><a name="Endpoints"></a> Noktalarının  
`Endpoints`Öğesi, bir rol için giriş (dış), iç ve örnek giriş uç noktaları koleksiyonunu açıklar. Bu öğe `InputEndpoint` ,, ve öğelerinin üst öğesidir `InternalEndpoint` `InstanceInputEndpoint` .

Giriş ve Iç uç noktalar ayrı olarak ayrılır. Bir hizmette toplam 25 giriş, iç ve örnek giriş uç noktası bulunabilir ve bu da bir hizmette izin verilen 25 rol arasında ayrılabilir. Örneğin, 5 rol varsa, rol başına 5 giriş uç noktası ayırabilirsiniz veya tek bir role 25 giriş uç noktası ayırabilirsiniz veya her biri 25 rol için 1 giriş uç noktası ayırabilirsiniz.

> [!NOTE]
>  Dağıtılan her rolün rol başına bir örnek olması gerekir. Abonelik için varsayılan sağlama 20 çekirdekle sınırlıdır ve bu nedenle bir rolün 20 örneğiyle sınırlıdır. Uygulamanız varsayılan sağlama tarafından sağlanmasından daha fazla örnek gerektiriyorsa, kotayı artırma hakkında daha fazla bilgi için [faturalandırma, abonelik yönetimi ve kota desteği '](https://azure.microsoft.com/support/options/) ne bakın.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> Inputendpoint  
`InputEndpoint`Öğesi bir Web rolü için bir dış uç nokta tanımlar.

HTTP, HTTPS, UDP ve TCP uç noktalarının birleşimi olan birden fazla uç nokta tanımlayabilirsiniz. Bir giriş uç noktası için seçtiğiniz herhangi bir bağlantı noktası numarasını belirtebilirsiniz, ancak hizmette her rol için belirtilen bağlantı noktası numaraları benzersiz olmalıdır. Örneğin, bir Web rolünün HTTP için 80 bağlantı noktasını ve HTTPS için bağlantı noktası 443 kullandığını belirtirseniz, ikinci bir Web rolünün HTTP için bağlantı noktası 8080 ' i ve HTTPS için bağlantı noktası 8043 kullandığını belirtebilirsiniz.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `InputEndpoint` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Dış uç nokta için benzersiz bir ad.|  
|protokol|string|Gereklidir. Dış uç nokta için Aktarım Protokolü. Bir Web rolü için olası değerler,, `HTTP` veya ' dir `HTTPS` `UDP` `TCP` .|  
|port|int|Gereklidir. Dış uç nokta için bağlantı noktası. Seçtiğiniz herhangi bir bağlantı noktası numarasını belirtebilirsiniz, ancak hizmette her bir rol için belirtilen bağlantı noktası numaraları benzersiz olmalıdır.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  
|sertifika|string|Bir HTTPS uç noktası için gereklidir. Bir öğe tarafından tanımlanan sertifikanın adı `Certificate` .|  
|localPort|int|İsteğe bağlı. Uç noktada iç bağlantılar için kullanılan bir bağlantı noktasını belirtir. `localPort`Öznitelik, uç noktasındaki dış bağlantı noktasını bir roldeki iç bağlantı noktasına eşler. Bu, bir rolün dışarıdan açığa çıkarılan bir bağlantı noktasında iç bileşen ile iletişim kurması gereken senaryolarda yararlıdır.<br /><br /> Belirtilmemişse, değeri `localPort` özniteliğiyle aynı olur `port` . `localPort`Çalışma zamanı API 'si kullanılarak keşfedilen ayrılmamış bir bağlantı noktasını otomatik olarak atamak için değerini "*" olarak ayarlayın.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.<br /><br /> `localPort`Özniteliği yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.|  
|ıgnoreroleınstancestatus|boolean|İsteğe bağlı. Bu özniteliğin değeri olarak ayarlandığında `true` , bir hizmetin durumu yok sayılır ve uç nokta yük dengeleyici tarafından kaldırılmaz. Bu değer `true` , bir hizmetin meşgul örneklerinde hata ayıklama için yararlı olarak ayarlanıyor. `false` varsayılan değerdir. **Note:**  Bir uç nokta, rol kullanılmaya devam eden bir durumda olsa bile trafiği alabilir.|  
|Loadbalanceraraştırması|string|İsteğe bağlı. Giriş uç noktasıyla ilişkili yük dengeleyici araştırmasının adı. Daha fazla bilgi için bkz. [Loadbalanceraraştırma şeması](schema-csdef-loadbalancerprobe.md).|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint  
`InternalEndpoint`Öğesi bir Web rolüne iç uç nokta tanımlar. İç uç nokta yalnızca hizmet içinde çalışan diğer rol örnekleri için kullanılabilir; hizmet dışındaki istemciler için kullanılamaz. Öğesi içermeyen Web rollerinin `Sites` yalnızca tek BIR http, UDP veya TCP iç uç noktası olabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `InternalEndpoint` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. İç uç nokta için benzersiz bir ad.|  
|protokol|string|Gereklidir. İç uç nokta için Aktarım Protokolü. Olası değerler şunlardır,, `HTTP` `TCP` `UDP` veya `ANY` .<br /><br /> Bir değeri `ANY` herhangi bir protokolün, herhangi bir bağlantı noktasına izin verildiğini belirtir.|  
|port|int|İsteğe bağlı. Uç noktada iç yük dengeli bağlantılar için kullanılan bağlantı noktası. Yük dengeli bir uç nokta iki bağlantı noktası kullanır. Genel IP adresi için kullanılan bağlantı noktası ve özel IP adresinde kullanılan bağlantı noktası. Bunlar genellikle bunlar aynı şekilde ayarlanmıştır, ancak farklı bağlantı noktaları kullanmayı seçebilirsiniz.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.<br /><br /> `Port`Özniteliği yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> Instanceınputendpoint  
`InstanceInputEndpoint`Öğesi bir Web rolüne örnek giriş uç noktası tanımlar. Örnek giriş uç noktası, yük dengeleyicide bağlantı noktası iletme kullanılarak belirli bir rol örneğiyle ilişkilendirilir. Her örnek giriş uç noktası, olası bir bağlantı noktası aralığından belirli bir bağlantı noktasıyla eşleştirilir. Bu öğe, `AllocatePublicPortFrom` öğesinin üst öğesidir.

`InstanceInputEndpoint`Öğesi yalnızca Azure SDK 1,7 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `InstanceInputEndpoint` .
  
| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Uç nokta için benzersiz bir ad.|  
|localPort|int|Gereklidir. Yük dengeleyiciden iletilen gelen trafiği almak için tüm rol örneklerinin dinleyeceği iç bağlantı noktasını belirtir. Olası değerler 1 ile 65535 (dahil) arasında değişir.|  
|protokol|string|Gereklidir. İç uç nokta için Aktarım Protokolü. Olası değerler: `udp` veya `tcp`. `tcp`Http/https tabanlı trafik için kullanın.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
`AllocatePublicPortFrom`Öğesi, dış müşteriler tarafından her örnek giriş uç noktasına erişmek için kullanılabilen genel bağlantı noktası aralığını açıklar. Ortak (VIP) bağlantı noktası numarası bu aralıktan ayrılır ve kiracı dağıtımı ve güncelleştirmesi sırasında her bir rol örneği uç noktasına atanır. Bu öğe, `FixedPortRange` öğesinin üst öğesidir.

`AllocatePublicPortFrom`Öğesi yalnızca Azure SDK 1,7 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort  
`FixedPort`Öğesi, iç uç nokta için uç noktada yük dengeli bağlantıları sağlayan bağlantı noktasını belirtir.

`FixedPort`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `FixedPort` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|port|int|Gereklidir. İç uç nokta için bağlantı noktası. Bu, `FixedPortRange` En düşük ve en fazla aynı bağlantı noktasına ayarlanması ile aynı etkiye sahiptir.<br /><br /> Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange  
`FixedPortRange`Öğesi, iç uç noktaya veya örnek giriş uç noktasına atanan bağlantı noktası aralığını belirtir ve uç noktada yük dengeli bağlantılar için kullanılan bağlantı noktasını ayarlar.

> [!NOTE]
>  `FixedPortRange`Öğesi, bulunduğu öğeye göre farklı şekilde çalışır. Öğe `FixedPortRange` `InternalEndpoint` öğesi içindeyse, yük dengeleyicideki tüm bağlantı noktalarını, rolün çalıştığı tüm sanal makineler için min ve Max özniteliklerinin aralığı içinde açar. Öğesi `FixedPortRange` `InstanceInputEndpoint` öğesinde olduğunda, rolü çalıştıran her bir sanal makinede en az ve en fazla öznitelik aralığı içinde yalnızca bir bağlantı noktası açılır.

`FixedPortRange`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `FixedPortRange` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|dk|int|Gereklidir. Aralıktaki en küçük bağlantı noktası. Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  
|max|string|Gereklidir. Aralıktaki en fazla bağlantı noktası. Olası değerler 1 ile 65535 (Azure SDK sürüm 1,7 veya üzeri) arasında değişir.|  

##  <a name="certificates"></a><a name="Certificates"></a> Sertifika  
`Certificates`Öğesi bir Web rolü için sertifika koleksiyonunu açıklar. Bu öğe, `Certificate` öğesinin üst öğesidir. Bir rol, ilişkili sayıda sertifikaya sahip olabilir. Sertifikalar öğesini kullanma hakkında daha fazla bilgi için bkz. [hizmet tanımı dosyasını bir sertifikayla değiştirme](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Sertifika  
`Certificate`Öğesi, bir Web rolüyle ilişkili bir sertifika tanımlar.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Certificate` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Bir HTTPS öğesiyle ilişkilendirildiğinde bu sertifikaya başvurmak için kullanılan bir ad `InputEndpoint` .|  
|storeLocation|string|Gereklidir. Bu sertifikanın yerel makinede bulunabileceği sertifika deposunun konumu. Olası değerler şunlardır `CurrentUser` `LocalMachine` .|  
|storeName|string|Gereklidir. Bu sertifikanın yerel makinede bulunduğu sertifika deposunun adı. Olası değerler, yerleşik depo adlarını,,, `My` , `Root` ,,,, `CA` `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` veya herhangi bir özel mağaza adını içerir. Özel bir mağaza adı belirtilmişse mağaza otomatik olarak oluşturulur.|  
|permissionLevel|string|İsteğe bağlı. Rol işlemlerine verilen erişim izinlerini belirtir. Yalnızca yükseltilmiş işlemlerin özel anahtara erişip erişebilmesini istiyorsanız `elevated` izin belirtin. `limitedOrElevated` izin, tüm rol işlemlerinin özel anahtara erişmesine izin verir. Olası değerler: `limitedOrElevated` veya `elevated`. `limitedOrElevated` varsayılan değerdir.|  

##  <a name="imports"></a><a name="Imports"></a> İşlemlerinin  
`Imports`Öğesi, Konuk işletim sistemine bileşenler ekleyen bir Web rolü için içeri aktarma modülleri koleksiyonunu açıklar. Bu öğe, `Import` öğesinin üst öğesidir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir içeri aktarma bloğuna sahip olabilir. 

`Imports`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="import"></a><a name="Import"></a> Aktarmaya  
`Import`Öğesi, Konuk işletim sistemine eklenecek bir modül belirtir.

`Import`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Import` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|Ladı|string|Gereklidir. İçeri aktarılacak modülün adı. Geçerli içeri aktarma modülleri şunlardır:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Tanılama<br /><br /> RemoteAccess ve RemoteForwarder modülleri, rol örneğinizi Uzak Masaüstü bağlantıları için yapılandırmanıza olanak tanır. Daha fazla bilgi için bkz. [Uzak Masaüstü bağlantısı etkinleştirme](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Tanılama modülü bir rol örneği için tanılama verileri toplamanızı sağlar.|  

##  <a name="runtime"></a><a name="Runtime"></a> Çalışma zamanı  
`Runtime`Öğesi, Azure ana bilgisayar işleminin çalışma zamanı ortamını denetleyen bir Web rolü için ortam değişkeni ayarlarının bir koleksiyonunu açıklar. Bu öğe, `Environment` öğesinin üst öğesidir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir çalışma zamanı bloğuna sahip olabilir.

`Runtime`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Runtime` :  

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|executionContext|string|İsteğe bağlı. Rol Işleminin başlatıldığı bağlamı belirtir. Varsayılan bağlam `limited` .<br /><br /> -   `limited` – İşlem, yönetici ayrıcalıkları olmadan başlatılır.<br />-   `elevated` – İşlem, yönetici ayrıcalıklarıyla başlatılır.|  

##  <a name="environment"></a><a name="Environment"></a> Ortamınızın  
`Environment`Öğesi, bir Web rolü için ortam değişkeni ayarlarının koleksiyonunu açıklar. Bu öğe, `Variable` öğesinin üst öğesidir. Rol herhangi bir sayıda ortam değişkeni kümesine sahip olabilir.

##  <a name="variable"></a><a name="Variable"></a> Değişken  
`Variable`Öğesi, Konuk işletim içinde ayarlanacak bir ortam değişkenini belirtir.

`Variable`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Variable` :  

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Ayarlanacak ortam değişkeninin adı.|  
|değer|string|İsteğe bağlı. Ortam değişkeni için ayarlanacak değer. Bir değer özniteliği veya bir öğesi dahil etmeniz gerekir `RoleInstanceValue` .|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> Roleınstancevalue  
`RoleInstanceValue`Öğesi, değişkenin değerinin alınacağı XPath 'i belirtir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `RoleInstanceValue` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|XPath|string|İsteğe bağlı. Örnek için dağıtım ayarlarının konum yolu. Daha fazla bilgi için bkz. [XPath Ile yapılandırma değişkenleri](cloud-services-role-config-xpath.md).<br /><br /> Bir değer özniteliği veya bir öğesi dahil etmeniz gerekir `RoleInstanceValue` .|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a> Noktası  
`EntryPoint`Öğesi, bir rolün giriş noktasını belirtir. Bu öğe, öğelerinin üst öğesidir `NetFxEntryPoint` . Bu öğeler, rol giriş noktası olarak görev yapmak için varsayılan WaWorkerHost.exe dışında bir uygulama belirtmenize olanak tanır.

`EntryPoint`Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint  
`NetFxEntryPoint`Öğesi, bir rol için çalıştırılacak programı belirtir.

> [!NOTE]
>  `NetFxEntryPoint`Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `NetFxEntryPoint` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|assemblyName|string|Gereklidir. Giriş noktasını içeren derlemenin yolu ve dosya adı. Yol, **\\ %roleroot%\approot** klasörüne göredir (içinde **\\ %roleroot%\approot** belirtmeyin `commandLine` , kabul edilir). **% Roleroot%** , Azure tarafından tutulan bir ortam değişkenidir ve rolünüz için kök klasör konumunu temsil eder. **\\ %Roleroot%\approot** klasörü, rolünüzün uygulama klasörünü temsil eder.<br /><br /> HWC rolleri için yol her zaman **\\ %Roleroot%\approot\bin** klasörüne görelidir.<br /><br /> Tam IIS ve IIS Express Web rolleri için, derleme **\\ %Roleroot%\approot** klasörüne göre bulunamıyorsa, **\\ %Roleroot%\approot\bin** aranır.<br /><br /> Tam IIS için bu geri dönüş davranışı önerilen bir uygulama değildir ve gelecekteki sürümlerde kaldırılmış olabilir.|  
|targetFrameworkVersion|string|Gereklidir. Derlemenin oluşturulduğu .NET Framework sürümü. Örneğin, `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a> Barındıra  
`Sites`Öğesi, bir Web rolünde barındırılan Web siteleri ve Web uygulamaları koleksiyonunu açıklar. Bu öğe, `Site` öğesinin üst öğesidir. Bir `Sites` öğe belirtmezseniz Web rolünüzün eski web rolü olarak barındırıldığı ve Web rolünüzde barındırılan bir Web siteniz olabilir. Bu öğe isteğe bağlıdır ve bir rol yalnızca bir siteler bloğuna sahip olabilir.

`Sites`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="site"></a><a name="Site"></a> Bölgesi  
`Site`Öğesi, Web rolünün bir parçası olan bir Web sitesini veya Web uygulamasını belirtir.

`Site`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Site` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Web sitesinin veya uygulamanın adı.|  
|physicalDirectory|string|Site kökünün içerik dizininin konumu. Konum, mutlak bir yol veya. csdef konumuna göre belirtilebilir.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a> VirtualApplication  
`VirtualApplication`Öğesi Internet Information Services (IIS) 7 ' de bir uygulamayı tanımlar, içerik teslim eden veya http gibi protokoller üzerinden hizmetler sağlayan bir dosya grubudur. IIS 7 ' de bir uygulama oluşturduğunuzda, uygulamanın yolu site URL 'sinin bir parçası haline gelir.

`VirtualApplication`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `VirtualApplication` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Sanal uygulamayı tanımlayacak bir ad belirtir.|  
|physicalDirectory|string|Gereklidir. Geliştirme makinesindeki sanal uygulamayı içeren yolu belirtir. İşlem öykünücüsünde IIS bu konumdan içerik almak üzere yapılandırılmıştır. Azure 'a dağıtım yaparken, fiziksel dizin içeriği hizmetin geri kalanı ile birlikte paketlenir. Hizmet paketi Azure 'a dağıtıldığında, IIS, paketi açılan içeriklerin konumuyla yapılandırılır.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a> Dizin  
`VirtualDirectory`Öğesi, IIS 'de belirttiğiniz bir dizin adı (yol da denir) belirtir ve yerel veya uzak bir sunucuda fiziksel bir dizine eşlenir.

`VirtualDirectory`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `VirtualDirectory` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Sanal dizini tanımlayacak bir ad belirtir.|  
|değer|physicalDirectory|Gereklidir. Geliştirme makinesindeki Web sitesini veya sanal dizin içeriğini içeren yolu belirtir. İşlem öykünücüsünde IIS bu konumdan içerik almak üzere yapılandırılmıştır. Azure 'a dağıtım yaparken, fiziksel dizin içeriği hizmetin geri kalanı ile birlikte paketlenir. Hizmet paketi Azure 'a dağıtıldığında, IIS, paketi açılan içeriklerin konumuyla yapılandırılır.|  

##  <a name="bindings"></a><a name="Bindings"></a> Lara  
`Bindings`Öğesi, bir Web sitesi için bağlama koleksiyonunu açıklar. Bu, öğesinin üst öğesidir `Binding` . Öğesi her öğe için gereklidir `Site` . Uç noktaları yapılandırma hakkında daha fazla bilgi için bkz. [rol örnekleri Için Iletişimi etkinleştirme](cloud-services-enable-communication-role-instances.md).

`Bindings`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="binding"></a><a name="Binding"></a> Bağlayıcısı  
`Binding`Öğesi, bir Web sitesi veya Web uygulamasıyla iletişim kurmak için istekler için gereken yapılandırma bilgilerini belirtir.

`Binding`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|name|string|Gereklidir. Bağlamayı tanımlayacak bir ad belirtir.|  
|Uçnoktaadı|string|Gereklidir. Bağlanacak bitiş noktası adını belirtir.|  
|hostHeader|string|İsteğe bağlı. Tek bir IP adresi/bağlantı noktası numarası birleşimine sahip birden fazla siteyi, farklı ana bilgisayar adlarıyla barındırmanıza olanak tanıyan bir ana bilgisayar adı belirtir.|  

##  <a name="startup"></a><a name="Startup"></a> Başlangıç  
`Startup`Öğesi, rol başlatıldığında çalışan bir görev koleksiyonu tanımlar. Bu öğe, `Variable` öğesinin üst öğesi olabilir. Rol başlangıç görevlerini kullanma hakkında daha fazla bilgi için bkz. [başlangıç görevlerini yapılandırma](cloud-services-startup-tasks.md). Bu öğe isteğe bağlıdır ve bir rol yalnızca bir başlangıç bloğuna sahip olabilir.

Aşağıdaki tabloda öğesi özniteliği açıklanmaktadır `Startup` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|Priority|int|Yalnızca dahili kullanım içindir.|  

##  <a name="task"></a><a name="Task"></a> Görevinin  
`Task`Öğesi, rol başladığında gerçekleşen başlangıç görevini belirler. Başlangıç görevleri, rolü bu Install yazılım bileşenlerini çalıştırmak veya diğer uygulamaları çalıştırmak için hazırlayan görevleri gerçekleştirmek için kullanılabilir. Görevler, öğe bloğunda göründükleri sırada yürütülür `Startup` .

`Task`Öğesi yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Task` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|Komut satırı|string|Gereklidir. Çalıştırılacak komutları içeren bir komut dosyası (örneğin, CMD dosyası). Başlangıç komutu ve toplu iş dosyalarının ANSI biçiminde kaydedilmesi gerekir. Dosyanın başlangıcında bir bayt düzeni işaretçisi ayarlanan dosya biçimleri düzgün şekilde çalışmaz.|  
|executionContext|string|Betiğin çalıştırıldığı bağlamı belirtir.<br /><br /> -   `limited` [Varsayılan] – işlemi barındıran rolle aynı ayrıcalıklarla çalıştırın.<br />-   `elevated` – Yönetici ayrıcalıklarıyla çalıştırın.|  
|taskType|string|Komutun yürütme davranışını belirtir.<br /><br /> -   `simple` [Varsayılan] – sistem, diğer tüm görevler başlatılmadan önce görevin çıkmasını bekler.<br />-   `background` – Sistem, görevin çıkış gelmesini beklemez.<br />-   `foreground` – Arka plana benzer, ancak tüm ön plan görevleri çıkana kadar rol yeniden başlatılmaz.|  

##  <a name="contents"></a><a name="Contents"></a> Dekiler  
`Contents`Öğesi bir Web rolü için içerik koleksiyonunu açıklar. Bu öğe, `Content` öğesinin üst öğesidir.

`Contents`Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

##  <a name="content"></a><a name="Content"></a> İçeriði  
`Content`Öğesi, Azure sanal makinesine kopyalanacak içeriğin kaynak konumunu ve kopyalandığı hedef yolu tanımlar.

`Content`Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `Content` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|hedef|string|Gereklidir. Azure sanal makinesinde içeriğin yerleştirildiği konum. Bu konum **%Roleroot%\approot** klasörüne göredir.|  

Bu öğe, öğesinin üst öğesidir `SourceDirectory` .

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory  
`SourceDirectory`Öğesi, içeriğin kopyalandığı yerel dizini tanımlar. Azure sanal makinesine kopyalanacak yerel içeriği belirtmek için bu öğeyi kullanın.

`SourceDirectory`Öğesi yalnızca Azure SDK 1,5 veya üzeri sürümler kullanılarak kullanılabilir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `SourceDirectory` .

| Öznitelik | Tür | Description |  
| --------- | ---- | ----------- |  
|path|string|Gereklidir. İçerikleri Azure sanal makinesine kopyalanacak olan yerel bir dizinin göreli veya mutlak yolu. Dizin yolundaki ortam değişkenlerinin genişletilmesi desteklenir.|  
  
## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md)




