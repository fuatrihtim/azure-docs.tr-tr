---
title: Azure Relay nedir? | Microsoft Belgeleri
description: Bu makale, bir güvenlik duvarı bağlantısı açmak ya da kurumsal ağ altyapısına müdahale eden değişiklikler yapmak zorunda kalmadan kurumsal ağınızda çalışan şirket içi hizmetleri kullanan bulut uygulamaları geliştirmenizi sağlayan Azure Relay hizmetine genel bakış sağlamaktadır.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fbf1b2134a4c2dce7a3e6a62668d0852dc08c18a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955391"
---
# <a name="what-is-azure-relay"></a>Azure Relay nedir?
Azure Relay hizmeti, kurumsal ağınızda çalışan hizmetleri güvenli bir şekilde genel buluta açmanızı sağlar. Bu şekilde, güvenlik duvarınızda bir bağlantı noktasını açmadan veya kurumsal ağ altyapınızda izinsiz değişiklikler yapmaya gerek kalmadan yapabilirsiniz. 

Geçiş hizmeti, şirket içi hizmetler ile bulutta veya başka bir şirket içi ortamda çalışan uygulamalar arasında aşağıdaki senaryoları destekler. 

- Geleneksel tek yönlü, istek/yanıt ve eşler arası iletişim 
- Yayımlama/abone olma senaryolarını desteklemek için internet kapsamında olay dağıtımı 
- Ağ sınırları genelinde iki yönlü ve arabelleğe alınmamış yuva iletişimi

Azure Relay hizmeti, VPN gibi ağ düzeyindeki tümleştirme teknolojilerinden farklıdır. Bir Azure geçişinin kapsamı, tek bir makinedeki tek bir uygulama uç noktası olarak belirlenebilir. VPN teknolojisinde çok daha fazla müdahale vardır ve ağ ortamının değiştirilmesine dayanır. 

## <a name="basic-flow"></a>Temel akış
Geçişli veri aktarımı desenindeki temel adımlar şunlardır:

1. Şirket içi hizmet, giden bağlantı noktası aracılığıyla geçiş hizmetine bağlanır. 
2. Belirli bir adrese bağlı çift yönlü iletişim yuvası oluşturulur. 
3. Ardından istemci, bu adresi hedef alan geçiş hizmetine trafik göndererek şirket içi hizmet ile iletişim kurabilir. 
4. Geçiş hizmeti daha sonra istemciye ayrılmış çift yönlü yuva aracılığıyla verileri şirket içi hizmete *geçirir*. İstemcinin şirket içi hizmetle doğrudan bağlantı kurmasına gerek yoktur. Hizmetin konumunu bilmesine gerek yoktur. Ayrıca şirket içi hizmetin güvenlik duvarında herhangi bir gelen bağlantı noktasının açılmasına ihtiyacı yoktur.


## <a name="features"></a>Özellikler 
Azure Geçiş iki özelliğe sahiptir:

- [Karma bağlantılar](#hybrid-connections) -çoklu platform senaryolarını etkinleştirmek için açık standart Web yuvalarını kullanır.
- WCF Geçişleri - Windows Communication Foundation’ı (WCF) kullanarak uzak yordam çağrılarını etkinleştirir. WCF Geçişi, birçok müşterinin WCF programlama modelleriyle kullanmakta olduğu eski geçiş teklifidir.

## <a name="hybrid-connections"></a>Karma Bağlantılar

Azure Relay hizmetindeki Karma Bağlantılar özelliği, daha önceden sunulan geçiş özelliklerinin güvenli ve açık protokol kullanan sürümüdür. İstediğiniz platformda ve istediğiniz programlama diliyle kullanabilirsiniz. Azure Relay hizmetindeki Karma Bağlantılar özelliği, HTTP ve WebSockets protokollerini kullanır. Web yuvaları veya HTTP(S) üzerinden istek gönderip yanıt almanızı sağlar. Bu özellik yaygın web tarayıcılarındaki WebSocket API’si ile uyumludur. 

Karma Bağlantılar protokolü hakkında ayrıntılı bilgi için bkz.[Karma Bağlantılar protokolü kılavuzu](relay-hybrid-connections-protocol.md). Karma Bağlantıları herhangi bir web yuvası kitaplığıyla ve herhangi bir çalışma zamanı/programlama dili ile kullanabilirsiniz.

> [!NOTE]
> Azure Relay hizmetinin Karma Bağlantılar özelliği, eski BizTalk Services Karma Bağlantılar özelliğinin yerini almıştır. BizTalk Services Karma Bağlantılar özelliği, Azure Service Bus WCF Geçişi üzerine geliştirilmişti. Azure Relay hizmetindeki Karma Bağlantılar özelliği önceki WCF Geçişi özelliğini tamamlamaktadır. Bu iki hizmet özelliği (WCF Geçişi ve Karma Bağlantılar), Azure Relay hizmetinde birlikte kullanılabilir. Ortak bir ağ geçidine sahip bu iki özellik, diğer açılardan farklı olan uygulamalardır.

## <a name="wcf-relay"></a>WCF Geçişi
WCF Geçişi tam .NET Framework ve WCF ile birlikte çalışır. Geçiş hizmeti ile şirket içi hizmetiniz arasındaki bağlantıyı bir WCF "geçiş" bağlamaları paketi kullanarak başlatırsınız. Geçiş bağlamaları, bulutta Service Bus tümleştirilebilen WCF kanal bileşenleri oluşturmak için tasarlanan yeni taşıma bağlama öğelerine eşlenir. Daha fazla bilgi için bkz. [WCF Geçişi ile çalışmaya başlama](service-bus-relay-tutorial.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Karma Bağlantılar ve WCF Geçişi karşılaştırması
Hem Karma Bağlantılar hem de WCF Geçişi bir kuruluş ağı içinde bulunan varlıklara güvenli bağlantı olanağı sağlar. Hangisinin diğerine tercih edileceği, aşağıdaki tabloda açıklandığı gibi özel gereksinimlerinize bağlıdır:

|  | WCF Geçişi | Karma Bağlantılar |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **Standart Tabanlı açık protokol** | |x |
| **RPC programlama modelleri** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Mimari: Gelen geçiş isteklerinin işlenmesi
Aşağıdaki diyagramda gelen geçiş isteklerinin Azure Relay tarafından nasıl işlendiği gösterilmektedir:

![Gelen WCF Geçiş İsteklerinin işlenmesi](./media/relay-what-is-it/ic690645.png)

1. Dinleyen istemci Azure Relay hizmetine bir dinleme isteği gönderir. Azure Load Balancer isteği ağ geçidi düğümlerinden birine yönlendirir. 
2. Azure Relay hizmeti, ağ geçidi deposunda bir geçiş oluşturur. 
3. Gönderen istemci dinleyen istemciye bağlanma isteği gönderir. 
4. İsteği alan ağ geçidi, ağ geçidi deposunda geçişi arar. 
5. Ağ geçidi bağlantı isteğini ağ geçidi deposundaki doğru ağ geçidine yönlendirir. 
6. Ağ geçidi, dinleyen istemciye bir istek göndererek gönderen istemciye en yakın ağ geçidi düğümünde geçici bir kanal oluşturmasını sağlar. 
7. Dinleyen istemci, gönderen istemciye en yakın olan ağ geçidine geçici bir kanal oluşturur. Ağ geçidi ile istemciler arasında bağlantı kurulduğuna göre istemciler ileti alışverişi gerçekleştirebilir. 
8. Ağ Geçidi, dinleme istemcisinden gönderilen iletileri gönderen istemciye iletir. 
9. Ağ geçidi gönderen istemcideki iletileri gönderen dinleyen istemciye yönlendirir.  

## <a name="next-steps"></a>Sonraki adımlar
* [.NET WebSockets kullanmaya başlama](relay-hybrid-connections-dotnet-get-started.md)
* [.NET HTTP İstekleri kullanmaya başlayın](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Node WebSockets kullanmaya başlama](relay-hybrid-connections-node-get-started.md)
* [Node HTTP İstekleri kullanmaya başlayın](relay-hybrid-connections-http-requests-node-get-started.md)
* [Geçiş hakkında SSS](relay-faq.md)

