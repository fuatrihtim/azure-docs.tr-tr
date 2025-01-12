---
title: Azure Izleyici 'de Azure ağ analizi çözümü | Microsoft Docs
description: Azure ağ güvenlik grubu günlüklerini ve Azure Application Gateway günlüklerini gözden geçirmek için Azure Izleyici 'de Azure ağ analizi çözümünü kullanabilirsiniz.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: b8b03378e82810bc2b9680805bacf8360f322a94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708144"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure Izleyici 'de Azure ağ izleme çözümleri

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Izleyici, ağlarınızı izlemek için aşağıdaki çözümleri sunar:
* Ağ Performansı İzleyicisi (NPM)
    * Ağınızın sistem durumunu izleme
* Gözden geçirilecek Azure Application Gateway Analizi
    * Azure Application Gateway günlükleri
    * Azure Application Gateway ölçümleri
* Bulut ağınızda ağ etkinliğini izlemeye ve denetlemeye yönelik çözümler
    * [Trafik Analizi](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Ağ Güvenlik Grubu Analizi

## <a name="network-performance-monitor-npm"></a>Ağ Performansı İzleyicisi (NPM)

[Ağ performansı İzleyicisi](../../networking/network-monitoring-overview.md) yönetim çözümü, ağların sistem durumunu, kullanılabilirliğini ve erişilebilirliğini izleyen bir ağ izleme çözümüdür.  Arasındaki bağlantıyı izlemek için kullanılır:

* Genel bulut ve şirket içi
* Veri merkezleri ve kullanıcı konumları (şube ofisleri)
* Çok katmanlı bir uygulamanın çeşitli katmanlarını barındıran alt ağlar.

Daha fazla bilgi için bkz. [ağ performansı İzleyicisi](../../networking/network-monitoring-overview.md).

## <a name="network-security-group-analytics"></a>Ağ güvenlik grubu Analizi

1. Yönetim çözümünü Azure Izleyici 'ye ekleyin ve
2. Tanılamayı Azure Izleyici 'de bir Log Analytics çalışma alanına yönlendirmek için tanılamayı etkinleştirin. Günlükleri Azure Blob depolamaya yazmak gerekli değildir.

Tanılama günlükleri etkinleştirilmemişse, bu kaynağın Pano dikey pencereleri boştur ve bir hata iletisi görüntüler.

## <a name="azure-application-gateway-analytics"></a>Azure Application Gateway Analizi

1. Tanılamayı Azure Izleyici 'de bir Log Analytics çalışma alanına yönlendirmek için tanılamayı etkinleştirin.
2. Application Gateway çalışma kitabı şablonunu kullanarak kaynağınız için ayrıntılı özet kullanın.

Tanılama günlükleri Application Gateway için etkinleştirilmemişse, çalışma kitabı içinde yalnızca varsayılan ölçüm verileri doldurulur.


> [!NOTE]
> 2017 Ocak 'ta uygulama ağ geçitlerinden ve ağ güvenlik gruplarından günlüklerin Log Analytics çalışma alanına gönderilmesi için desteklenen yol değişti. **Azure Ağ Analizi (kullanım dışı)** çözümünü görürseniz, izlemeniz gereken adımlar Için [eski ağ analizi çözümünden geçiş](#migrating-from-the-old-networking-analytics-solution) konusuna bakın.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Azure ağ verileri toplama ayrıntılarını gözden geçirme
Azure Application Gateway Analytics ve ağ güvenlik grubu Analizi yönetim çözümleri, tanılama günlüklerini doğrudan Azure uygulama ağ geçitleri ve ağ güvenlik gruplarından toplar. Günlükleri Azure Blob depolama alanına yazmak gerekli değildir ve veri toplama için hiçbir aracı gerekmez.

Aşağıdaki tabloda, veri toplama yöntemleri ve Azure Application Gateway Analytics ve ağ güvenlik grubu analizi için verilerin toplanmasına ilişkin diğer ayrıntılar gösterilmektedir.

| Platform | Doğrudan aracı | Systems Center Operations Manager Aracısı | Azure | Operations Manager gerekli mi? | Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager | Toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |günlüğe kaydedildiğinde |


### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Portalda Azure Application Gateway tanılamayı etkinleştirme

1. Azure portal izlemek için Application Gateway kaynağına gidin.
2. *Tanılama ayarları* ' nı seçerek aşağıdaki sayfayı açın.

   ![Application Gateway kaynağı için Tanılama ayarları yapılandırmasının ekran görüntüsü.](media/azure-networking-analytics/diagnostic-settings-1.png)

   [![Tanılama ayarlarını yapılandırma sayfasının ekran görüntüsü.](media/azure-networking-analytics/diagnostic-settings-2.png)](media/azure-networking-analytics/application-gateway-diagnostics-2.png#lightbox)

5. *Log Analytics gönder* onay kutusuna tıklayın.
6. Mevcut bir Log Analytics çalışma alanını seçin veya bir çalışma alanı oluşturun.
7. Toplanacak günlük türlerinin her biri için **günlük** altında onay kutusuna tıklayın.
8. Azure Izleyici 'de tanılamayı günlüğe kaydetmeyi etkinleştirmek için *Kaydet* ' e tıklayın.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell kullanarak Azure Ağ Tanılama 'yı etkinleştirme

Aşağıdaki PowerShell betiği, uygulama ağ geçitleri için kaynak günlüğüne nasıl etkinleştireceğinizi gösteren bir örnek sağlar.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

#### <a name="accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights"></a>Azure Izleyici ağ öngörüleri aracılığıyla Azure Application Gateway Analytics 'e erişme

Application Insights 'a Application Gateway kaynağınız içindeki içgörüler sekmesi aracılığıyla erişilebilir.

![Application Gateway öngörülerinin ekran görüntüsü ](media/azure-networking-analytics/azure-appgw-insights.png
)

"Ayrıntılı ölçümleri görüntüle" sekmesi, Application Gateway verileri özetleyerek önceden doldurulmuş çalışma kitabını açar.

[![Application Gateway çalışma kitabının ekran görüntüsü](media/azure-networking-analytics/azure-appgw-workbook.png)](media/azure-networking-analytics/application-gateway-workbook.png#lightbox)

### <a name="new-capabilities-with-azure-monitor-network-insights-workbook"></a>Azure Izleyici Network Insights çalışma kitabıyla yeni yetenekler

> [!NOTE]
> Azure Monitor Insights çalışma kitabıyla ilişkili ek maliyet yoktur. Log Analytics çalışma alanı kullanım başına faturalandırılmaya devam edecektir.

Network Insights çalışma kitabı, Azure Izleyici 'nin en son özelliklerini ve aşağıdakileri de içeren Log Analytics yararlanmanızı sağlar:

* Hem [ölçüm](../insights/network-insights-overview.md#resource-health-and-metrics) hem de günlük verileri ile izleme ve sorun giderme için merkezi konsol.

* Özel zengin [görselleştirmeler](../visualize/workbooks-overview.md#visualizations)oluşturmayı desteklemek için esnek tuval.

* [Çalışma kitabı şablonlarını](../visualize/workbooks-overview.md#workbooks-versus-workbook-templates) daha geniş bir topluluk ile kullanabilme ve paylaşma özelliği.

Yeni çalışma kitabı çözümünün özellikleri hakkında daha fazla bilgi edinmek için kullanıma alma [çalışma kitapları-genel bakış](../visualize/workbooks-overview.md)

## <a name="migrating-from-azure-gateway-analytics-solution-to-azure-monitor-workbooks"></a>Azure ağ geçidi Analizi çözümünden Azure Izleyici çalışma kitaplarına geçiş

> [!NOTE]
> Azure Izleyici ağ öngörüleri çalışma kitabı, Application Gateway kaynaklarınızın ölçüm ve Log Analytics 'e erişmesi için önerilen çözümdür.

1. Günlükleri bir Log Analytics çalışma alanında depolamak için [Tanılama ayarlarının etkinleştirildiğinden](#enable-azure-application-gateway-diagnostics-in-the-portal) emin olun. Zaten yapılandırıldıysa, Azure Izleyici Network Insights çalışma kitabı aynı konumdaki verileri tüketebilir ve ek değişiklik gerektirmez.

> [!NOTE]
> Önceki tüm veriler, nokta tanılama ayarlarından önce çalışma kitabında zaten kullanılabilir. Veri aktarımı gerekli değildir.

2. Application Gateway kaynağınız için [varsayılan Öngörüler çalışma kitabına](#accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights) erişin. Application Gateway Analytics çözümü tarafından desteklenen tüm mevcut Öngörüler çalışma kitabında zaten mevcut olacaktır. Bu, ölçüm & günlük verilerine göre özel [görselleştirmeler](../visualize/workbooks-overview.md#visualizations) ekleyerek genişletebilirsiniz.

3. Tüm ölçüm ve günlük öngörülerinizi görebildikten sonra, çalışma alanınızdan Azure Gateway Analytics çözümünü temizlemek için çözüm kaynağı sayfasından çözümü silebilirsiniz.

[![Azure Application Gateway Analytics çözümü için silme seçeneğinin ekran görüntüsü.](media/azure-networking-analytics/azure-appgw-analytics-delete.png)](media/azure-networking-analytics/application-gateway-analytics-delete.png#lightbox)

## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure Izleyici 'de Azure ağ güvenlik grubu analizi çözümü

![Azure Ağ Güvenlik Grubu Analizi simgesi](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Ağ güvenlik grubu analizi çözümü, işlevselliği [Trafik Analizi](../../network-watcher/traffic-analytics.md)tarafından değiştirildiğinden topluluk desteğine taşınıyor.
> - Bu çözüm artık [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) kullanılabilir ve yakında Azure Marketi 'nde kullanılamaz.
> - Çözümü çalışma alanına zaten ekleyen mevcut müşteriler için hiçbir değişiklik yapmadan çalışmaya devam edecektir.
> - Microsoft, tanılama ayarlarını kullanarak çalışma alanınıza NSG kaynak günlüklerinin gönderilmesini desteklemeye devam edecektir.

Aşağıdaki Günlükler ağ güvenlik grupları için desteklenir:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Azure ağ analizi çözümünü yüklemek ve yapılandırmak için aşağıdaki yönergeleri kullanın:

1. [Çözüm Galerisi Azure izleyici çözümlerini ekleme](./solutions.md)bölümünde açıklanan Işlemi kullanarak Azure ağ güvenlik grubu Analizi çözümünü etkinleştirin.
2. İzlemek istediğiniz [ağ güvenlik grubu](../../virtual-network/virtual-network-nsg-manage-log.md) kaynakları için tanılama günlüğünü etkinleştirin.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Portalda Azure ağ güvenlik grubu tanılamayı etkinleştirme

1. Azure portal izlemek için ağ güvenlik grubu kaynağına gidin
2. *Tanılama günlüklerini* seçerek aşağıdaki sayfayı açın

   ![Tanılamayı açma seçeneğini gösteren bir ağ güvenlik grubu kaynağı için tanılama günlükleri sayfasının ekran görüntüsü.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. *Tanılamayı* aç ' a tıklayarak aşağıdaki sayfayı açın

   ![Tanılama ayarlarını yapılandırma sayfasının ekran görüntüsü. Durum açık olarak ayarlanır, Log Analytics gönder seçilidir ve iki günlük türü seçilidir.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Tanılamayı açmak için, *durum* ' *a tıklayın.*
5. *Log Analytics gönder* onay kutusuna tıklayın
6. Mevcut bir Log Analytics çalışma alanını seçin veya bir çalışma alanı oluşturun
7. Toplanacak günlük türlerinin her biri için **günlük** altında onay kutusuna tıklayın
8. Tanılamayı Log Analytics için günlüğe kaydetmeyi etkinleştirmek üzere *Kaydet* ' e tıklayın

### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell kullanarak Azure Ağ Tanılama 'yı etkinleştirme

Aşağıdaki PowerShell betiği, ağ güvenlik grupları için kaynak günlüğü oluşturmayı nasıl etkinleştireceğinizi gösteren bir örnek sağlar
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Azure ağ güvenlik grubu Analizi 'ni kullanma
Genel bakışta **Azure ağ güvenlik grubu Analizi** kutucuğuna tıkladıktan sonra günlüklerinizin özetlerini görüntüleyebilir ve ardından aşağıdaki kategoriler için ayrıntıya gidebilirsiniz:

* Ağ güvenlik grubu engellenen akışlar
  * Engellenen akışlarla ağ güvenlik grubu kuralları
  * Engellenen akışlar içeren MAC adresleri
* Ağ güvenlik grubu izin verilen akışlar
  * İzin verilen akışlara sahip ağ güvenlik grubu kuralları
  * İzin verilen akışlar içeren MAC adresleri

![Engellenen akışlara sahip engellenen akışlara ve MAC adreslerine sahip kurallar da dahil olmak üzere ağ güvenlik grubu engellenen akışlar için veri içeren kutucukların ekran görüntüsü.](media/azure-networking-analytics/log-analytics-nsg01.png)

![İzin verilen akışlara izin verilen akışlar ve MAC adresleri dahil olmak üzere, ağ güvenlik grubu için verileri olan kutucukların ekran görüntüsü.](media/azure-networking-analytics/log-analytics-nsg02.png)

**Azure ağ güvenlik grubu Analizi** panosunda, dikey pencerelerden birindeki Özet bilgilerini gözden geçirin ve günlük araması sayfasında ayrıntılı bilgileri görüntülemek için bir tane tıklatın.

Günlük arama sayfalarında, sonuçları zamana, ayrıntılı sonuçlara ve günlük arama geçmişinize göre görüntüleyebilirsiniz. Ayrıca, sonuçları daraltmak için de modellerle filtre uygulayabilirsiniz.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Eski ağ analizi çözümünden geçiş
2017 Ocak 'ta, Azure Application Gateway ve Azure ağ güvenlik gruplarından günlüklerin bir Log Analytics çalışma alanına gönderilmesi için desteklenen yol değişti. Bu değişiklikler aşağıdaki avantajları sağlar:
+ Günlükler, depolama hesabı kullanılmasına gerek kalmadan doğrudan Azure Izleyici 'ye yazılır
+ Günlüklerin Azure Izleyici 'de kullanılabilir olması için üretilen zamandan daha az gecikme süresi
+ Daha az yapılandırma adımı
+ Tüm Azure tanılama türleri için ortak bir biçim

Güncelleştirilmiş çözümleri kullanmak için:

1. [Tanılamayı Azure Application Gateway 'lerin Azure Izleyici 'ye doğrudan gönderilecek şekilde yapılandırın](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Tanılamayı Azure ağ güvenlik gruplarından Azure Izleyici 'ye doğrudan gönderilecek şekilde yapılandırın](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. [Çözüm Galerisi Azure izleyici çözümlerini ekleme](solutions.md) bölümünde açıklanan işlemi kullanarak *Azure Application Gateway Analytics* ve *Azure ağ güvenlik grubu Analizi* çözümünü etkinleştirin
3. Yeni veri türünü kullanmak için kaydedilen sorguları, panoları veya uyarıları güncelleştirme
   + Tür AzureDiagnostics. Kaynak türü ' nu kullanarak Azure ağ günlüklerine filtre uygulayabilirsiniz.

     | Onun yerine: | Kullanırsınız |
     | --- | --- |
     | Networkapplicationgateway 'ler &#124;, OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; WHERE ResourceType = = "APPLICATIONGATEWAY" ve OperationName = = "ApplicationGatewayAccess" |
     | NetworkAdapter = = "ApplicationGatewayPerformance" olduğunda Networkapplicationgateway 'ler &#124; | AzureDiagnostics &#124; WHERE ResourceType = = "APPLICATIONGATEWAY" ve OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; WHERE ResourceType = = "NETWORKSECURITYGROUPS" |

   + \_Adında s, d veya g sonekine sahip olan her alan için \_ \_ , ilk karakteri küçük harf olarak değiştirin
   + Adında o soneki olan herhangi bir alan için \_ , veriler iç içe geçmiş alan adlarına göre tek tek alanlara bölünür.
4. *Azure Ağ Analizi (kullanım dışı)* çözümünü kaldırın.
   + PowerShell kullanıyorsanız, şunu kullanın `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Değişiklik, yeni çözümde görüntülenmeden önce toplanan veriler görünmez. Eski tür ve alan adlarını kullanarak bu verileri sorgulamaya devam edebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Azure tanılama verilerini görüntülemek için [Azure izleyici 'de günlük sorguları '](../logs/log-query-overview.md) nı kullanın.

