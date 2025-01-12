---
title: Azure Application Gateway özel hata sayfaları oluşturma
description: Bu makalede, Application Gateway özel hata sayfaları oluşturma gösterilmektedir. Özel hata sayfası sayesinde kendi logonuzu ve sayfa düzeninizi kullanabilirsiniz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592729"
---
# <a name="create-application-gateway-custom-error-pages"></a>Application Gateway özel hata sayfaları oluşturma

Application Gateway, varsayılan hata sayfalarını göstermek yerine özel hata sayfaları oluşturmanızı sağlar. Özel hata sayfası sayesinde kendi logonuzu ve sayfa düzeninizi kullanabilirsiniz.

Örneğin, Web uygulamanız ulaşılabilir değilse kendi bakım sayfanızı tanımlayabilirsiniz. Veya, bir Web uygulamasına kötü amaçlı bir istek gönderiliyorsa, bir yetkisiz erişim sayfası da oluşturabilirsiniz.

Özel hata sayfaları aşağıdaki iki senaryo için desteklenir:

- **Bakım sayfası** -bu özel hata sayfası, 502 hatalı bir ağ geçidi sayfası yerine gönderilir. Application Gateway trafiği yönlendirmek için arka uç olmadığında gösterilir. Örneğin, zamanlanmış bakım veya öngörülemeyen bir sorun, arka uç havuzu erişimini etkiler.
- **Yetkisiz erişim sayfası** -bu özel hata sayfası, 403 yetkisiz erişim sayfası yerine gönderilir. Application Gateway WAF kötü amaçlı trafiği algıladığında ve bunu engellediğinde gösterilir.

Arka uç sunucularından kaynaklanan bir hata varsa, çağrı yapana geri dönerek geri gönderilir. Özel bir hata sayfası görüntülenmiyor. Application Gateway, bir istek arka uca ulaşamadığınızda özel bir hata sayfası gösterebilir.

Özel hata sayfaları genel düzeyde ve dinleyici düzeyinde tanımlanabilir:

- **Genel düzey** -hata sayfası, bu uygulama ağ geçidinde dağıtılan tüm Web uygulamalarının trafiği için geçerlidir.
- **Dinleyici düzeyi** -hata sayfası, bu dinleyicide alınan trafiğe uygulanır.
- **Her Ikisi de** , dinleyici düzeyinde tanımlanan özel hata sayfası, genel düzeyde bir kümeyi geçersiz kılar.

Özel bir hata sayfası oluşturmak için şunları yapmanız gerekir:

- HTTP yanıt durum kodu.
- hata sayfası için ilgili konum. 
- konum için genel olarak erişilebilir bir Azure Depolama Blobu.
- bir *. htm veya *. html uzantı türü. 

Hata sayfasının boyutu 1 MB 'tan az olmalıdır. Hata sayfasında bağlantılı görüntüler varsa, özel hata sayfasında genel olarak erişilebilen mutlak URL 'Ler veya Base64 ile kodlanmış görüntü olmalıdır. Aynı blob konumundaki görüntülerle ilişkili bağlantılar Şu anda desteklenmiyor. 

Bir hata sayfası belirttikten sonra, Application Gateway bunu Depolama Blobu konumundan indirir ve yerel uygulama ağ geçidi önbelleğine kaydeder. Ardından hata sayfası doğrudan uygulama ağ geçidinden sunulur. Varolan bir özel hata sayfasını değiştirmek için, uygulama ağ geçidi yapılandırmasında farklı bir blob konumuna işaret etmeniz gerekir. Uygulama ağ geçidi, yeni sürümleri getirmek için blob konumunu düzenli aralıklarla denetlemez.

## <a name="portal-configuration"></a>Portal Yapılandırması

1. Portalda Application Gateway gidin ve bir uygulama ağ geçidi seçin.

    ![Ekran görüntüsünde bir uygulama ağ geçidi için genel bakış sayfası gösterilir.](media/custom-error/ag-overview.png)
2. **Dinleyiciler** ' e tıklayın ve bir hata sayfası belirtmek istediğiniz belirli bir dinleyiciye gidin.

    ![Application Gateway dinleyicileri](media/custom-error/ag-listener.png)
3. Bir 403 WAF hatası için özel hata sayfası veya dinleyici düzeyinde 502 bakım sayfası yapılandırın.

    > [!NOTE]
    > Azure portal genel düzey özel hata sayfaları oluşturma şu anda desteklenmiyor.

4. Belirli bir hata durum kodu için herkese açık bir şekilde erişilebilir blob URL 'SI belirtin ve **Kaydet**' e tıklayın. Application Gateway artık özel hata sayfasıyla yapılandırılmıştır.

   ![Application Gateway hata kodları](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell yapılandırması

Özel hata sayfasını yapılandırmak için Azure PowerShell kullanabilirsiniz. Örneğin, genel bir özel hata sayfası:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Veya dinleyici düzeyi hata sayfası:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Daha fazla bilgi için bkz. [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) ve [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror).

## <a name="next-steps"></a>Sonraki adımlar

Application Gateway tanılama hakkında daha fazla bilgi için bkz. [arka uç sistem durumu, tanılama günlükleri ve Application Gateway ölçümleri](application-gateway-diagnostics.md).
