---
title: Azure Işlem-Linux Tanılama uzantısı 4,0
description: Azure 'da çalışan Linux sanal makinelerinden ölçümleri ve günlük olaylarını toplamak için Azure Linux Tanılama uzantısı 'nı (LAD 4,0) yapılandırma.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 4c4851ab28e5da74e7f1fa36f087ecfdabb1c638
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560132"
---
# <a name="use-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Ölçümleri ve günlükleri izlemek için Linux Tanılama uzantısı 4,0 kullanın

Bu belgede Linux Tanılama uzantısının sürüm 4,0 ve daha yeni bir sürümü açıklanmaktadır.

> [!IMPORTANT]
> Sürüm 3. * hakkında daha fazla bilgi için  [Bu belgeye](./diagnostics-linux-v3.md)bakın. Sürüm 2,3 ve üzeri hakkında daha fazla bilgi için [Bu belgeye](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)bakın.

## <a name="introduction"></a>Giriş

Linux Tanılama uzantısı, bir kullanıcının Microsoft Azure üzerinde çalışan bir Linux sanal makinesinin sistem durumunu izlemesine yardımcı olur. Aşağıdaki özellikleri içerir:

* VM 'den sistem performans ölçümlerini toplar ve bunları belirlenen depolama hesabındaki belirli bir tabloda depolar.
* Syslog 'dan günlük olaylarını alır ve bunları belirlenen depolama hesabındaki belirli bir tabloya depolar.
* Kullanıcıların toplanan ve yüklenen veri ölçümlerini özelleştirmesini sağlar.
* Kullanıcıların, toplanan ve yüklenen olayların Syslog özelliklerini ve önem düzeyleri özelleştirmesini sağlar.
* Kullanıcıların belirtilen günlük dosyalarını belirlenmiş bir depolama tablosuna karşıya yüklemesine olanak sağlar.
* , Belirlenen depolama hesabındaki rastgele EventHub uç noktalarına ve JSON biçimli bloblara ölçüm ve günlük olayları göndermeyi destekler.

Bu uzantı, hem Azure dağıtım modelleriyle birlikte çalışmaktadır.

## <a name="installing-the-extension-in-your-vm"></a>Uzantıyı sanal makinenize yükleme

Bu uzantıyı Azure PowerShell cmdlet 'lerini, Azure CLı betikleri, ARM şablonlarını veya Azure portal kullanarak etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [uzantı özellikleri](features-linux.md).

>[!NOTE]
>Tanılama sanal makine uzantısının belirli bileşenleri, [log ANALYTICS VM Uzantısı](./oms-linux.md)'nda da gönderilir. Bu mimaride, her iki uzantı aynı ARM şablonunda örneklendiğinden çakışmalar meydana çıkabilir. Bu yükleme zamanı çakışmalarını önlemek için, uzantıların sırayla yüklendiğinden emin olmak için [ `dependsOn` yönergesini](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) kullanın. Uzantılar her iki sırayla yüklenebilir.

Bu yükleme yönergeleri ve [indirilebilir bir örnek yapılandırma](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) lad 4,0 'yi şu şekilde yapılandırın:

* aynı ölçümleri, LAD 2,3, 3 *; tarafından sağlandığı ile yakalayın ve saklayın.
* LAD 4,0 ' de yeni olan Azure depolama 'ya yönelik her zamanki havuz ile Azure Izleyici havuzuna ölçümler gönderin
* LAD 3,0 tarafından sağlandığı gibi faydalı bir dosya sistemi ölçümleri kümesi yakalayın.
* LAD 2,3 ile etkinleştirilen varsayılan Syslog koleksiyonunu yakala;
* VM ölçümlerinde grafik oluşturma ve uyarı verme için Azure portal deneyimini etkinleştirin.

İndirilebilir yapılandırma yalnızca bir örnektir; kendi gereksinimlerinize uyacak şekilde değiştirin.

### <a name="supported-linux-distributions"></a>Desteklenen Linux dağıtımları

Linux Tanılama uzantısı, aşağıdaki dağıtımları ve sürümleri destekler. Dağıtımların ve sürümlerin listesi yalnızca Azure tarafından onaylanan Linux satıcı görüntüleri için geçerlidir. Gereçler gibi üçüncü taraf KLG ve KCG görüntüleri genellikle Linux Tanılama uzantısı için desteklenmez.

Yalnızca deler 7 gibi ana sürümleri listeleyen bir dağıtım, tüm küçük sürümler için de desteklenir. Belirli bir alt sürüm belirtilmişse, yalnızca belirli bir sürüm desteklenir; "+" eklendiğinde, belirtilen sürümden daha büyük olan alt sürümler desteklenir.

Desteklenen dağıtımlar ve sürümler:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- 9, 8, 7
- RHEL 7, 6.7 +

### <a name="prerequisites"></a>Önkoşullar

* **Azure Linux Aracısı sürüm 2.2.0 veya üzeri**. Azure VM Linux Galeri görüntülerinin çoğu, sürüm 2.2.7 veya üstünü içerir. `/usr/sbin/waagent -version`VM 'de yüklü sürümü onaylamak için ' i çalıştırın. VM, Konuk aracısının eski bir sürümünü çalıştırıyorsa, güncelleştirmek için [Bu yönergeleri](./update-linux-agent.md) izleyin.
* **Azure CLI**. Makinenizde [Azure CLI ortamını ayarlayın](/cli/azure/install-azure-cli) .
* Henüz yoksa wget komutu: komutunu çalıştırın `sudo apt-get install wget` .
* Mevcut bir Azure aboneliği ve verilerin depolanması için mevcut bir genel amaçlı depolama hesabı.  Genel amaçlı depolama hesapları, gereken tablo depolamayı destekler.  BLOB depolama hesabı çalışmayacak.
* Python 2

### <a name="python-requirement"></a>Python gereksinimi

Linux Tanılama uzantısı Python 2 gerektirir. Sanal makineniz, varsayılan olarak Python 2 ' yi içermeyen bir demi kullanıyorsa, bunu kurmanız gerekir. Aşağıdaki örnek komutlar farklı distros üzerinde Python 2 ' ye yüklenir.    

 - Red hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, debir: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Python2 yürütülebilir dosyası *Python*'un diğer adı olmalıdır. Aşağıda, bu diğer adı ayarlamak için kullanabileceğiniz bir yöntem verilmiştir:

1. Mevcut diğer adları kaldırmak için aşağıdaki komutu çalıştırın.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Diğer adı oluşturmak için aşağıdaki komutu çalıştırın.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Örnek yükleme

> [!NOTE]
> Örneklerden herhangi biri için, çalıştırmadan önce ilk bölümdeki değişkenler için doğru değerleri girin. 

Bu örneklerde indirilen örnek yapılandırma bir dizi standart veri toplar ve bunları tablo depolamaya gönderir. Örnek yapılandırma ve içeriği için URL, değişikliğe tabidir. Çoğu durumda, portal ayarları JSON dosyasının bir kopyasını indirmeniz ve gereksinimlerinize göre özelleştirmeniz gerekir, ardından oluşturduğunuz herhangi bir şablon veya Otomasyon, bu URL 'YI her seferinde indirmek yerine yapılandırma dosyası sürümünüzü kullanır.

> [!NOTE]
> Yeni Azure Izleyici havuzunu etkinleştirmek için, VM 'Lerin MSI kimlik doğrulama belirteci oluşturma için sistem tarafından atanan kimliğin etkinleştirilmiş olması gerekir. Bu, VM oluşturma sırasında veya VM oluşturulduktan sonra yapılabilir. Portal, CLı, PowerShell ve Resource Manager aracılığıyla sistem tarafından atanan kimliği etkinleştirme adımları.  [burada](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)ayrıntılı olarak listelenmiştir. 

#### <a name="azure-cli-sample"></a>Azure CLı örneği

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VM
az vm identity assign -g $my_resource_group -n $my_linux_vm

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-extension-on-the-virtual-machine-scale-set-instance"></a>Sanal makine ölçek kümesi örneğine LAD 4,0 uzantısını yüklemek için Azure CLı örneği

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Enable System Assigned Identity to the existing VMSS
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell örneği

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable System Assigned Identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="updating-the-extension-settings"></a>Uzantı ayarları güncelleştiriliyor

Korumalı veya ortak ayarlarınızı değiştirdikten sonra aynı komutu çalıştırarak bunları sanal makineye dağıtın. Ayarlarda her şey değiştirilmişse, güncelleştirilmiş ayarlar uzantıya gönderilir. LAD yapılandırmayı yeniden yükler ve kendisini yeniden başlatır.

### <a name="migration-from-previous-versions-of-the-extension"></a>Uzantının önceki sürümlerinden geçiş

Uzantının en son sürümü, **Şu anda genel önizlemede olan 4,0 '** dir. **2. x sürümleri 31 temmuz 2018 tarihinden itibaren kullanım dışı olduğundan, 3. x 'In daha eski sürümleri hala desteklenmektedir**.

> [!IMPORTANT]
> 3. x ' ten uzantının bu yeni sürümüne geçiş yapmak için eski uzantıyı kaldırmalı ve ardından uzantının 4. sürümünü (Azure Izleyici havuzuna ölçüm göndermek için sistem tarafından atanan kimlik ve havuzlar için güncelleştirilmiş yapılandırmayla) yüklemelisiniz.

Öneriler:

* Uzantıyı otomatik ikincil sürüm yükseltmesi etkin olarak yükler.
  * Klasik dağıtım modeli VM 'lerinde, uzantıyı Azure XPLAT CLı veya PowerShell aracılığıyla yüklüyorsanız sürüm olarak ' 4. * ' belirtin.
  * Azure Resource Manager dağıtım modeli VM 'lerinde, VM Dağıtım şablonuna ' "Oto Upgrademinorversion": true ' ekleyin.
* LAD 4,0 için aynı depolama hesabı ile LAD 3. * kullanabilirsiniz. 

## <a name="protected-settings"></a>Korumalı ayarlar

Bu yapılandırma bilgileri kümesi, genel görünümden korunması gereken hassas bilgiler içerir (örneğin, depolama kimlik bilgileri). Bu ayarlar, uzantısı tarafından şifrelenmiş biçimde iletilir ve saklanır.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Değer
---- | -----
storageAccountName | Verilerin uzantı tarafından yazıldığı depolama hesabının adı.
storageAccountEndPoint | seçim Depolama hesabının bulunduğu bulutu tanımlayan uç nokta. Bu ayar yoksa, LAD varsayılan olarak Azure genel bulutu 'na sahiptir `https://core.windows.net` . Azure Almanya, Azure Kamu veya Azure Çin 'de bir depolama hesabı kullanmak için bu değeri uygun şekilde ayarlayın.
storageAccountSasToken | [](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) `ss='bt'` `srt='co'` Ekleme, oluşturma, listeleme, güncelleştirme ve yazma izinleri () veren kapsayıcılar ve nesneler () için geçerli olan blob ve tablo Hizmetleri () için bir hesap SAS belirteci `sp='acluw'` . Önde gelen soru işareti (? *) eklemeyin.*
mdsdHttpProxy | seçim Uzantının belirtilen depolama hesabına ve uç noktaya bağlanmasını sağlamak için HTTP proxy bilgileri gerekir.
sinksConfig | seçim Ölçüm ve olayların sunulabilecek alternatif hedeflerin ayrıntıları. Uzantı tarafından desteklenen her bir veri havuzunun belirli ayrıntıları, izleyen bölümlerde ele alınmıştır.

Bir Kaynak Yöneticisi şablonu içinde SAS belirteci almak için **Listaccountsas** işlevini kullanın. Örnek bir şablon için bkz. [list işlev örneği](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Gerekli SAS belirtecini Azure portal aracılığıyla kolayca oluşturabilirsiniz.

1. Uzantının yazmasını istediğiniz genel amaçlı depolama hesabını seçin
1. Sol menünün ayarlar bölümünden "paylaşılan erişim imzası" seçeneğini belirleyin
1. Daha önce açıklanan uygun bölümleri yapın
1. "SAS oluştur" düğmesine tıklayın.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Ekran görüntüsü, oluşturma S S ile paylaşılan erişim imzası sayfasını gösterir.":::

Oluşturulan SAS 'yi storageAccountSasToken alanına kopyalayın; Baştaki soru işaretini ("?") kaldırın.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Bu isteğe bağlı bölüm, uzantının topladığı bilgileri gönderdiği ek hedefleri tanımlar. "Sink" dizisi her ek veri havuzu için bir nesne içerir. "Type" özniteliği nesnedeki diğer öznitelikleri belirler.

Öğe | Değer
------- | -----
name | Bu havuza uzantı yapılandırmasında başka bir yerde başvurmak için kullanılan bir dize.
tür | Tanımlanmakta olan havuz türü. Bu türün örneklerinde diğer değerleri (varsa) belirler.

Linux Tanılama uzantısının 4,0 sürümü iki havuz türünü destekliyor: EventHub ve JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub havuzu

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

"SasURL" girdisi, verilerin yayımlanması gereken olay hub 'ı için SAS belirteci dahil olmak üzere tam URL 'yi içerir. LAD, gönderme talebini sağlayan bir ilkeyi adlandırma için bir SAS gerektirir. Örnek:

* Adlı bir Event Hubs ad alanı oluşturun `contosohub`
* Adlı ad alanında bir olay hub 'ı oluşturma `syslogmsgs`
* Adlı olay hub 'ında, `writer` gönderme talebini sağlayan bir paylaşılan erişim ilkesi oluşturun

1 Ocak 2018 ' de gece yarısı UTC 'ye kadar bir SAS oluşturduysanız, sasURL değeri şu olabilir:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs için SAS belirteçleriyle ilgili bilgi oluşturma ve alma hakkında daha fazla bilgi için [Bu Web sayfasına](/rest/api/eventhub/generate-sas-token#powershell)bakın.

#### <a name="the-jsonblob-sink"></a>JsonBlob havuzu

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

JsonBlob havuzuna yöneltilen veriler, Azure depolama 'daki bloblarda depolanır. Her bir LAD örneği her bir havuz adı için her saat bir blob oluşturur. Her blob her zaman sözdizimi geçerli bir JSON dizisi içerir. Yeni girdiler diziye göre otomatik olarak eklenir. Blob 'lar, havuzla aynı ada sahip bir kapsayıcıda depolanır. Blob kapsayıcı adları için Azure depolama kuralları, JsonBlob havuzları adları için geçerlidir: 3 ila 63 küçük harfli alfasayısal karakterler veya tireler.

## <a name="public-settings"></a>Ortak ayarlar

Bu yapı, uzantı tarafından toplanan bilgileri denetleyen çeşitli ayarlar bloklarını içerir. Her ayar (bir merdiveni hariç) isteğe bağlıdır. ' De ölçüm veya Syslog koleksiyonu belirtirseniz `ladCfg` , öğesini de belirtmeniz gerekir `StorageAccount` . LAD 4,0 ölçümleri için Azure Izleyici havuzunu etkinleştirmek üzere sinksConfig öğesinin belirtilmesi gerekir

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Öğe | Değer
------- | -----
StorageAccount | Verilerin uzantı tarafından yazıldığı depolama hesabının adı. [Korunan ayarlarda](#protected-settings)belirtilen adla aynı olmalıdır.
mdsdHttpProxy | seçim [Korumalı ayarlarınızdaki](#protected-settings)gibi. Ayarlanırsa, ortak değer özel değer tarafından geçersiz kılınır. [Korumalı ayarlar](#protected-settings)' da, parola gibi gizli dizi içeren ara sunucu ayarlarını yerleştirin.

Kalan öğeler aşağıdaki bölümlerde ayrıntılı olarak açıklanmıştır.

### <a name="ladcfg"></a>El cfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Bu yapı, Azure ölçümleri hizmetine ve diğer veri havuzları 'na teslime yönelik ölçüm ve günlüklerin toplanması için denetim sağlar. Ya da ya da `performanceCounters` `syslogEvents` ikisini birden belirtmeniz gerekir. Yapıyı belirtmeniz gerekir `metrics` .

Syslog veya ölçüm toplamayı etkinleştirmek istemiyorsanız, aşağıda gösterildiği gibi, yalnızca bir IElement cfg öğesi için boş bir yapı belirtebilirsiniz. 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Öğe | Değer
------- | -----
eventVolume | seçim Depolama tablosu içinde oluşturulan bölüm sayısını denetler. `"Large"`,, Veya ' den biri olmalıdır `"Medium"` `"Small"` . Belirtilmemişse, varsayılan değer `"Medium"` .
Samplerateınseconds | seçim Ham (toplanmayan) ölçümler koleksiyonu arasındaki varsayılan Aralık. Desteklenen en küçük örnek oranı 15 saniyedir. Belirtilmemişse, varsayılan değer `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Öğe | Değer
------- | -----
resourceId | VM 'nin veya VM 'nin ait olduğu sanal makine ölçek kümesinin Azure Resource Manager kaynak KIMLIĞI. Bu ayar, yapılandırmada bir JsonBlob havuzu kullanılıyorsa da belirtilmelidir.
scheduledTransferPeriod | Toplam ölçümlerin hesaplanacağı ve Azure ölçümlerine aktarılacağı sıklık, bir 8601 zaman aralığı olarak ifade edilir. En küçük aktarım süresi 60 saniyedir, yani PT1M. En az bir scheduledTransferPeriod belirtmeniz gerekir.

PerformanceCounters bölümünde belirtilen ölçümlerin örnekleri, her 15 saniyede bir veya sayaç için açıkça tanımlanmış örnek hızda toplanır. Birden çok scheduledTransferPeriod frekansları görünürse (örnekte olduğu gibi), her toplama bağımsız olarak hesaplanır.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Bu isteğe bağlı bölüm, ölçüm koleksiyonunu denetler. Ham örnekler her bir [Scheduledtransferperiod](#metrics) için toplanır ve bu değerleri üretir:

* ortalama
* minimum
* maksimum
* Son toplanan değer
* toplamı hesaplamak için kullanılan ham örnek sayısı

Öğe | Değer
------- | -----
yapma | seçim LAD 'nin toplanmış ölçüm sonuçları gönderdiği havuz adlarının virgülle ayrılmış bir listesi. Tüm toplanan ölçümler listelenen her havuza yayımlanır. Bkz. [Sinksconfig](#sinksconfig). Örnek: `"EHsink1, myjsonsink"`.
tür | Ölçümün gerçek sağlayıcısını tanımlar.
sınıf | "Counter" ile birlikte, sağlayıcının ad alanı içinde belirli ölçümü tanımlar.
counter | "Class" ile birlikte, sağlayıcının ad alanı içinde belirli bir ölçümü tanımlar.
Onay Belirleyicisi | Azure ölçümleri ad alanı içindeki belirli ölçüyü tanımlar.
koşul | seçim Bir nesnenin geçerli bir örneğini seçer veya bu nesnenin tüm örneklerinde toplamayı seçer. Daha fazla bilgi için bkz `builtin` . Ölçüm tanımları.
Örnekleray | Bu ölçüm için ham örneklerin toplandığı oranı ayarlayan 8601 aralığıdır. Ayarlanmamışsa, koleksiyon aralığı [Samplerateınseconds](#ladcfg)değerine göre ayarlanır. Desteklenen en kısa örnek hızı 15 saniyedir (PT15S).
unit | Şu dizelerden biri olmalıdır: "Count", "bytes", "saniye", "percent", "CountPerSecond", "BytesPerSecond", "milisaniyelik". Ölçüm için birimi tanımlar. Toplanan verilerin tüketicileri, toplanan veri değerlerinin bu birimle eşleşmesini bekler. LAD bu alanı yoksayar.
displayName | Azure ölçümlerinde bu verilere eklenecek olan etiket (ilişkili yerel ayar tarafından belirtilen dilde). LAD bu alanı yoksayar.

Counterbelirleyicisi, rastgele bir tanımlayıcıdır. Ölçüm tüketicileri, Azure portal grafik oluşturma ve uyarı özelliği gibi, ölçüm veya ölçüm örneğini tanımlayan "anahtar" olarak Counterbelirleyicisi kullanın. `builtin`Ölçümler için, ile başlayan Counterbelirleyicisi değerlerini kullanmanızı öneririz `/builtin/` . Bir ölçümün belirli bir örneğini topluyorsanız, örneğin tanımlayıcısını Counterıdentifier değerine iliştirmenizi öneririz. Bazı örnekler:

* `/builtin/Processor/PercentIdleTime` -Tüm vCPU 'larda ortalama boşta geçen süre
* `/builtin/Disk/FreeSpace(/mnt)` -/MNT dosya sistemi için boş alan
* `/builtin/Disk/FreeSpace` -Tüm bağlı dosya sistemleri genelinde ortalama boş alan

Ne de ne de Azure portal, Counterbelirtici değerinin herhangi bir kalıpla eşleşmesini bekler. Onay tanımlayıcısı değerlerini nasıl oluşturabileceğinize göre tutarlı olun.

Belirttiğinizde `performanceCounters` , Lad verileri her zaman Azure Storage 'daki bir tabloya yazar. JSON bloblarına ve/veya Event Hubs yazılmış verilere sahip olabilirsiniz, ancak verileri bir tabloya depolamayı devre dışı bırakabilirsiniz. Aynı depolama hesabı adını ve uç noktasını kullanmak üzere yapılandırılan tanılama uzantısının tüm örnekleri, ölçümlerini ve günlüklerini aynı tabloya ekler. Aynı tablo bölümüne çok fazla sayıda VM yazıyorsanız Azure bu bölüme yazma işlemlerini kısıtlayabilir. EventVolume ayarı, girdilerin 1 (küçük), 10 (orta) veya 100 (büyük) farklı bölümler arasında yayılmasına neden olur. Genellikle trafiğin kısıtlanmadığından emin olmak için "Orta" yeterlidir. Azure portal Azure ölçümleri özelliği, grafik oluşturmak veya uyarıları tetiklemek için bu tablodaki verileri kullanır. Tablo adı, Bu dizelerin bitiştirilmesi:

* `WADMetrics`
* Tabloda depolanan toplanmış değerler için "scheduledTransferPeriod"
* `P10DV2S`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `WADMetricsPT1HP10DV2S20170410` ve içerir `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Bu isteğe bağlı bölüm, syslog 'dan gelen günlük olayları koleksiyonunu denetler. Bölüm atlanırsa, syslog olayları hiç yakalanmaz.

SyslogEventConfiguration koleksiyonunda, ilgilendiğiniz her Syslog özelliği için bir giriş vardır. Bu özellik belirli bir tesis için Minönem derecesi "NONE" ise veya bu tesis öğede görünmüyorsa, bu olandakilerden hiçbir olay yakalanmaz.

Öğe | Değer
------- | -----
yapma | Ayrı günlük olaylarının yayımlandığı havuz adlarının virgülle ayrılmış listesi. SyslogEventConfiguration ' deki kısıtlamalarla eşleşen tüm günlük olayları listelenen her havuza yayımlanır. Örnek: "EHforsyslog"
facilityName | Syslog tesis adı ("LOG \_ User" veya "log \_ LOCAL0" gibi). Tam liste için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html) "tesis" bölümüne bakın.
Minönem derecesi | Syslog önem derecesi düzeyi ("LOG \_ ERR" veya "log \_ Info" gibi). Tam liste için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html) "düzey" bölümüne bakın. Uzantı, belirtilen düzeyin üzerinde veya üzerinde tesise gönderilen olayları yakalar.

Belirttiğinizde `syslogEvents` , Lad verileri her zaman Azure Storage 'daki bir tabloya yazar. JSON bloblarına ve/veya Event Hubs yazılmış verilere sahip olabilirsiniz, ancak verileri bir tabloya depolamayı devre dışı bırakabilirsiniz. Bu tablo için bölümleme davranışı, konusunda açıklandığı gibi aynıdır `performanceCounters` . Tablo adı, Bu dizelerin bitiştirilmesi:

* `LinuxSyslog`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `LinuxSyslog20170410` ve içerir `LinuxSyslog20170609` .

### <a name="sinksconfig"></a>sinksConfig

Bu isteğe bağlı bölüm, depolama hesabı ve varsayılan Konuk ölçümleri dikey penceresine ek olarak Azure Izleyici havuzuna ölçüm göndermeyi etkinleştirmeyi sağlar.

> [!NOTE]
> Bu, sistem tarafından atanan kimliğin VM 'Lerde/VMSS 'de etkinleştirilmesini gerektirir. Bu, Portal, CLı, PowerShell ve Resource Manager aracılığıyla yapılabilir. Adımlar [burada](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)ayrıntılı olarak listelenmiştir. Bunu etkinleştirme adımları, yukarıdaki AZ CLı, PowerShell vb. yükleme örneklerinde de listelenmiştir. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>Dosya günlükleri

Günlük dosyalarının yakalanmasını denetler. LAD, dosyaya yazıldığı ve bunları tablo satırlarına ve/veya belirtilen herhangi bir havuza (JsonBlob veya EventHub) yazan yeni metin satırlarını yakalar.

> [!NOTE]
> Dosya günlükleri, LAD 'nin çağrılan bir alt bileşeni tarafından yakalanır `omsagent` . Dosya günlüklerini toplamak için, `omsagent` kullanıcının belirttiğiniz dosyalar üzerinde okuma izinlerine sahip olduğundan ve bu dosyanın yolundaki tüm dizinlerde izinleri yürütdiğinizden emin olmanız gerekir. Bu `sudo su omsagent -c 'cat /path/to/file'` işlemi, LAD yüklendikten sonra çalıştırarak denetleyebilirsiniz.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Öğe | Değer
------- | -----
 dosyası | İzlenen ve yakalanan günlük dosyasının tam yol adı. Yol adının tek bir dosya adı olmalıdır; bir dizini veya joker karakter içeremez. ' Omsagent ' Kullanıcı hesabının dosya yoluna okuma erişimi olmalıdır.
tablo | seçim Belirtilen depolama hesabında (korumalı yapılandırmada belirtildiği gibi), dosyanın "Tail" içindeki yeni satırların yazıldığı Azure Storage tablosu.
yapma | seçim Günlük satırlarının gönderildiği ek havuz adlarının virgülle ayrılmış bir listesi.

"Table" veya "Havuzlar" ya da her ikisi de belirtilmelidir.

## <a name="metrics-supported-by-the-builtin-provider"></a>Yerleşik sağlayıcı tarafından desteklenen ölçümler

> [!NOTE]
> LAD tarafından desteklenen varsayılan ölçümler, tüm dosya sistemleri/diskler/ad genelinde toplanır. Toplu olmayan ölçümler için, lütfen daha yeni Azure Izleyici havuz ölçümleri desteğine başvurun.

Yerleşik ölçüm sağlayıcısı, geniş bir Kullanıcı kümesiyle en ilginç ölçüm kaynağıdır. Bu ölçümler beş geniş sınıfa ayrılır:

* İşlemci
* Bellek
* Ağ
* Dosya Sistemi
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>Işlemci sınıfı için yerleşik ölçümler

Ölçümlerin Işlemci sınıfı, sanal makinede işlemci kullanımı hakkında bilgi sağlar. Yüzdeleri toplayarak, sonuç tüm CPU 'larda ortalama olur. İki vCPU sanal makinesinde, bir vCPU %100 meşgulse ve diğeri 100% boşta ise, bildirilen PercentIdleTime 50. Her vCPU aynı dönemde %50 meşgulse, bildirilen sonuç de 50 olacaktır. Dört sanal CPU sanal makinesinde, tek bir vCPU 100% meşgul ve diğerleri boşta, bildirilen PercentIdleTime 75 olacaktır.

counter | Anlamı
------- | -------
PercentIdleTime | Toplama penceresinde işlemcilerin çekirdek boşta döngüsünü yürüttüğünden geçen sürenin yüzdesi
PercentProcessorTime | Boşta olmayan iş parçacığı yürütme zaman yüzdesi
PercentIOWaitTime | GÇ işlemlerinin tamamlanması için bekleyen sürenin yüzdesi
PercentInterruptTime | Donanım/yazılım kesintileri ve DPC 'leri yürütme zaman yüzdesi (ertelenmiş yordam çağrıları)
PercentUserTime | Toplama penceresi sırasında boşta olmayan süre, Kullanıcı için normal öncelikte harcanan sürenin yüzdesi
PercentNiceTime | Boşta olmayan süre, düşürülen (iyi) öncelikte harcanan yüzde
PercentPrivilegedTime | Boşta olmayan süre, ayrıcalıklı (çekirdek) modda harcanan yüzde

İlk dört sayaç %100 olmalıdır. Son üç sayaç ayrıca %100 ' a kadar toplam PercentProcessorTime, PercentIOWaitTime ve PercentInterruptTime toplamını alt bölümlere bölüler.

### <a name="builtin-metrics-for-the-memory-class"></a>Bellek sınıfı için yerleşik ölçümler

Ölçüm bellek sınıfı bellek kullanımı, sayfalama ve değiştirme hakkında bilgi sağlar.

counter | Anlamı
------- | -------
Kullanılabilirlik Blememory | MIB 'de kullanılabilir fiziksel bellek
Yüztavailablememory | Toplam belleğin yüzdesi olarak kullanılabilir fiziksel bellek
UsedMemory | Kullanımda olan fiziksel bellek (MIB)
Yüztusedmemory | Toplam belleğin yüzdesi olarak kullanılan fiziksel bellek
PagesPerSec | Toplam sayfalama (okuma/yazma)
PagesReadPerSec | Yedekleme deposundan okunan sayfalar (takas dosyası, program dosyası, eşlenmiş dosya vb.)
PagesWrittenPerSec | Yedekleme deposuna yazılan sayfalar (takas dosyası, eşlenmiş dosya vb.)
AvailableSwap | Kullanılmayan takas alanı (MIB)
Yüztavailableswap | Toplam değiştirmenin yüzdesi olarak kullanılmayan takas alanı
UsedSwap | Kullanımda değiştirme alanı (MIB)
Yüztusedswap | İçindeki değiştirme alanını toplam değiştirmenin yüzdesi olarak kullanın

Bu ölçüm sınıfının yalnızca tek bir örneği vardır. "Condition" özniteliğinin hiçbir kullanışlı ayarı yoktur ve atlanmalıdır.

### <a name="builtin-metrics-for-the-network-class"></a>Ağ sınıfı için yerleşik ölçümler

Ölçüm ağ sınıfı, önyükleme sonrasında tek bir ağ arabirimindeki ağ etkinliği hakkında bilgi sağlar. LAD, ana bilgisayar ölçümlerinden alınabilecek bant genişliği ölçümlerini sunmaz.

counter | Anlamı
------- | -------
Bytestransmderlenen | Önyüklemeden bu yana gönderilen toplam bayt sayısı
BytesReceived | Önyüklemeden bu yana alınan toplam bayt sayısı
BytesTotal | Önyüklemeden bu yana gönderilen veya alınan toplam bayt sayısı
Packetstransmderlenen | Önyüklemeden bu yana gönderilen toplam paket sayısı
Paket alındı | Önyüklemeden bu yana alınan toplam paket sayısı
Toplam Rxerrors | Önyüklemeden bu yana alma hatalarının sayısı
TotalTxErrors | Önyüklemeden bu yana aktarılan hataların sayısı
Toplam çarpışmalar | Önyüklemeden bu yana ağ bağlantı noktaları tarafından raporlanan çakışmaların sayısı

### <a name="builtin-metrics-for-the-filesystem-class"></a>Dosya sistemi sınıfı için yerleşik ölçümler

Ölçümlerin dosya sistemi sınıfı, dosya sistemi kullanımı hakkında bilgi sağlar. Mutlak ve yüzde değerleri sıradan bir kullanıcıya (kök değil) gösterildiklerinde raporlanır.

counter | Anlamı
------- | -------
FreeSpace | Bayt cinsinden kullanılabilir disk alanı
UsedSpace | Bayt cinsinden kullanılan disk alanı
Yüztfreesi | Boş alan yüzdesi
Yüztusedspace | Kullanılan yüzde alanı
Yüztfreeinodes | Kullanılmayan ınomdes yüzdesi
Yüztusedinodes | Tüm dosya sistemleri genelinde toplanan ayrılmış (kullanımda) ınomof 'ların yüzdesi
BytesReadPerSecond | Saniye başına okunan bayt
BytesWrittenPerSecond | Saniye başına yazılan bayt sayısı
BytesPerSecond | Saniye başına okunan veya yazılan bayt sayısı
ReadsPerSecond | Saniye başına okuma işlemi
WritesPerSecond | Saniye başına yazma işlemi
TransfersPerSecond | Saniye başına okuma veya yazma işlemi

### <a name="builtin-metrics-for-the-disk-class"></a>Disk sınıfı için yerleşik ölçümler

Ölçümlerin disk sınıfı disk cihazı kullanımı hakkında bilgi sağlar. Bu istatistikler tüm sürücü için geçerlidir. Bir cihazda birden çok dosya sistemi varsa, bu cihaz için sayaçlar tüm bunlar arasında toplanır.

counter | Anlamı
------- | -------
ReadsPerSecond | Saniye başına okuma işlemi
WritesPerSecond | Saniye başına yazma işlemi
TransfersPerSecond | Saniyede toplam işlem sayısı
AverageReadTime | Okuma işlemi başına ortalama saniye
AverageWriteTime | Yazma işlemi başına ortalama saniye
AverageTransferTime | İşlem başına ortalama saniye
AverageDiskQueueLength | Sıraya alınan disk işlemlerinin ortalama sayısı
ReadBytesPerSecond | Saniye başına okunan bayt sayısı
WriteBytesPerSecond | Saniye başına yazılan bayt sayısı
BytesPerSecond | Saniye başına okunan veya yazılan bayt sayısı

## <a name="installing-and-configuring-lad-40"></a>LAD 4,0 yükleme ve yapılandırma

### <a name="azure-cli"></a>Azure CLI’si

Korunan ayarlarınızın dosyada ProtectedSettings.jsolduğu varsayıldığında ve genel yapılandırma bilgileriniz üzerinde PublicSettings.js, şu komutu çalıştırın:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bu komut, Azure CLı 'nın Azure Kaynak yönetimi modunu kullandığınızı varsayar. Klasik dağıtım modeli (ASM) VM 'Leri için LAD 'yi yapılandırmak üzere, "asm" moduna geçin ( `azure config mode asm` ) ve komutta kaynak grubu adını atlayın. Daha fazla bilgi için [platformlar arası CLI belgelerine](/cli/azure/authenticate-azure-cli)bakın.

### <a name="powershell"></a>PowerShell

Korunan ayarlarınızın değişkende olduğu varsayıldığında `$protectedSettings` ve ortak yapılandırma bilgileriniz `$publicSettings` değişkeninde ise şu komutu çalıştırın:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="an-example-lad-40-configuration"></a>Örnek LAD 4,0 yapılandırması

Önceki tanımları temel alarak, bazı açıklamayla örnek bir LAD 4,0 uzantı yapılandırması aşağıda verilmiştir. Bu örneği çalışmanıza uygulamak için kendi depolama hesabı adınızı, hesap SAS belirtecinizi ve EventHubs SAS belirteçlerini kullanmanız gerekir.

> [!NOTE]
> LAD 'yi yüklemek için Azure CLı veya PowerShell 'i kullanmanıza bağlı olarak, ortak ve korumalı ayarların sağlanması yöntemi farklılık gösterir. Azure CLı kullanıyorsanız, yukarıdaki örnek komutla birlikte kullanmak için ProtectedSettings.jsve PublicSettings.jsiçin aşağıdaki ayarları kaydedin. PowerShell kullanıyorsanız, ayarları çalıştırarak ve ile kaydedin `$protectedSettings` `$publicSettings` `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Korumalı ayarlar

Bu korumalı ayarlar yapılandırılır:

* depolama hesabı
* eşleşen bir hesap SAS belirteci
* birkaç havuz (SAS belirteçlerine sahip JsonBlob veya EventHubs)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Ortak ayarlar

Bu genel ayarlar, LAD 'ye neden olur:

* Yüzde işlemci zamanı ve kullanılan disk alanı ölçümlerini `WADMetrics*` tabloya yükleme
* Syslog tesis "Kullanıcı" ve önem derecesi "bilgi" den tabloya ileti yükleyin `LinuxSyslog*`
* Dosyadaki eklenmiş satırları tabloya Yükle `/var/log/myladtestlog` `MyLadTestLog`

Her durumda, veriler öğesine de yüklenir:

* Azure Blob depolama (kapsayıcı adı, JsonBlob havuzunda tanımlanmıştır)
* EventHubs uç noktası (EventHubs havuzunda belirtilen şekilde)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId`Yapılandırmadaki ÖĞESININ VM veya sanal makine ölçek kümesi ile aynı olması gerekir.

* Azure platformu ölçümleri grafik oluşturma ve uyarı, üzerinde çalıştığınız VM 'nin RESOURCEID olduğunu bilir. RESOURCEID arama anahtarını kullanarak VM 'niz için verileri bulmayı bekler.
* Azure otomatik ölçeklendirme kullanırsanız, otomatik ölçeklendirme yapılandırmasındaki RESOURCEID, LAD tarafından kullanılan RESOURCEID ile aynı olmalıdır.
* RESOURCEID, LAD tarafından yazılan Jsonblob 'ların adlarına yerleşiktir.

## <a name="view-your-data"></a>Verilerinizi görüntüleme

Performans verilerini görüntülemek veya uyarıları ayarlamak için Azure portal kullanın:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Ekran görüntüsü, seçili ölçüm üzerinde kullanılan disk alanı ve elde edilen grafik ile Azure portal gösterir.":::

`performanceCounters`Veriler her zaman bir Azure depolama tablosunda depolanır. Azure depolama API 'Leri birçok dil ve platformda kullanılabilir.

JsonBlob havuzları 'na gönderilen veriler, [korunan ayarlarda](#protected-settings)adlı depolama hesabındaki bloblara depolanır. Blob verilerini Azure Blob depolama API 'Leri kullanarak kullanabilirsiniz.

Ayrıca, bu kullanıcı arabirimi araçlarını kullanarak Azure Storage 'daki verilere erişebilirsiniz:

* Visual Studio Sunucu Gezgini.
* [Ekran görüntüsü Azure Depolama Gezgini kapsayıcıları ve tabloları gösterir.](https://azurestorageexplorer.codeplex.com/ "Azure Depolama Gezgini").

Microsoft Azure Depolama Gezgini oturumunun bu anlık görüntüsü, test sanal makinesinde doğru yapılandırılmış bir LAD 3,0 uzantısının oluşturulan Azure depolama tablolarını ve kapsayıcılarını gösterir. Görüntü, [örnek LAD 3,0 yapılandırmasıyla](#an-example-lad-40-configuration)tam olarak eşleşmez.

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Ekran görüntüsü Azure Depolama Gezgini gösterir.":::

Bir EventHubs uç noktasına yayınlanan iletileri kullanmayı öğrenmek için ilgili [eventhubs belgelerine](../../event-hubs/event-hubs-about.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Topladığınız ölçümler için [Azure izleyici](../../azure-monitor/alerts/alerts-classic-portal.md) 'de ölçüm uyarıları oluşturun.
* Ölçümleriniz için [izleme grafikleri](../../azure-monitor/data-platform.md) oluşturun.
* Otomatik ölçeklendirmeyi denetlemek için ölçümlerinizi kullanarak [bir sanal makine ölçek kümesi oluşturmayı](../linux/tutorial-create-vmss.md) öğrenin.