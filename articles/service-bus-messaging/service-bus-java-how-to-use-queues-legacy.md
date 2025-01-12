---
title: Java ile Azure Service Bus kuyruklarını kullanma
description: Bu öğreticide, Azure Service Bus kuyruğuna ileti göndermek ve ileti almak için Java uygulamaları oluşturmayı öğreneceksiniz.
ms.devlang: Java
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 1f7c5f71e75a1b683433531a9d95905e9a7d81cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729734"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Hızlı başlangıç: ileti göndermek ve almak için Java ile Azure Service Bus kuyrukları kullanma

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Bu öğreticide, Azure Service Bus kuyruğuna ileti göndermek ve ileti almak için Java uygulamaları oluşturmayı öğreneceksiniz. 

> [!WARNING]
>  Bu hızlı başlangıç, eski `azure-servicebus` paketleri kullanır. En son paketi kullanan bir hızlı başlangıç için `azure-messaging-servicebus` bkz. [ `azure-messaging-servicebus` kullanarak ileti gönderme ve alma ](service-bus-java-how-to-use-queues.md). 


## <a name="prerequisites"></a>Önkoşullar
1. Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
2. Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin.
    1. Service Bus **kuyruklara** hızlı **genel bakış** konusunu okuyun. 
    2. Service Bus **ad alanı** oluşturun. 
    3. **Bağlantı dizesini** alın.
    4. Service Bus **kuyruğu** oluşturun.
3. [Java Için Azure SDK][Azure SDK for Java]'yı yükler. 


## <a name="configure-your-application-to-use-service-bus"></a>Uygulamanızı kullanmak için yapılandırma Service Bus
Bu örneği oluşturmadan önce [Java Için Azure SDK 'sını][Azure SDK for Java] yüklediğinizden emin olun. 

Çakışan Küreler kullanıyorsanız, Java için Azure SDK 'sını içeren [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] yükleyebilirsiniz. Ardından, projenize **Java için Microsoft Azure kitaplıklarını** ekleyebilirsiniz. IntelliJ kullanıyorsanız, bkz. [Azure Toolkit for IntelliJ yüklemesi](/azure/developer/java/toolkit-for-intellij/installation). 

![Çakışan Küreler projenize Java için Microsoft Azure kitaplıkları ekleyin](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Aşağıdaki `import` deyimlerini Java dosyasının en üstüne ekleyin:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme
Uygulamanız bir Service Bus kuyruğuna ileti göndermek için bir **Queueclient** nesnesi başlatır ve iletileri zaman uyumsuz olarak gönderir. Aşağıdaki kod, Portal üzerinden oluşturulan bir sıra için bir iletinin nasıl gönderileceğini gösterir.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Service Bus kuyruklarından gönderilen ve alınan iletiler [ileti](/java/api/com.microsoft.azure.servicebus.message) sınıfının örnekleridir. İleti nesneleri bir dizi standart Özellik (Label ve TimeToLive gibi), uygulamaya özgü özel özellikleri tutmak için kullanılan bir sözlük ve rastgele uygulama verileri gövdesi vardır. Bir uygulama, herhangi bir seri hale getirilebilir nesneyi Ileti oluşturucusuna geçirerek ileti gövdesini ayarlayabilir ve ardından nesneyi seri hale getirmek için uygun seri hale getirici kullanılacaktır. Alternatif olarak, **Java 'yı sağlayabilirsiniz. IO. InputStream** nesnesi.


Service Bus kuyrukları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Kuyrukta tutulan ileti sayısına ilişkin bir sınır yoktur ancak kuyruk tarafından tutulan iletilerin toplam boyutu için uç sınır vardır. Bu kuyruk boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir.

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma
Sıradan ileti almanın birincil yolu **Servicebuscontract** nesnesini kullanmaktır. Alınan iletiler iki farklı modda çalışabilir: **Receiveanddelete** ve **PeekLock**.

**Receiveanddelete** modunu kullanırken alma işlemi tek bir işlem olur. yani, Service Bus kuyruktaki bir ileti için okuma isteği aldığında, iletiyi tüketildiği gibi işaretler ve uygulamaya döndürür. **Receiveanddelete** modu (varsayılan mod) en basit modeldir ve bir uygulamanın hata durumunda bir iletiyi işlememesi için en iyisi senaryolar için geçerlidir. Bu durumu daha iyi anlamak için müşterinin bir alma isteği bildirdiğini ve bu isteğin işlenmeden çöktüğünü varsayın.
Service Bus ileti tüketildiği gibi işaretlendiğinden, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenme öncesinde tüketilen iletiyi kaçırmıştır.

**PeekLock** modunda, alma işlemi iki aşamalı bir işlem haline gelir, bu da eksik iletilere izin verilmeyen uygulamaları desteklemeyi olanaklı kılar. Service Bus bir istek aldığında bir sonraki kullanılacak iletiyi bulur, diğer tüketicilerin bu iletiyi almasını engellemek için kilitler ve ardından uygulamaya döndürür. Uygulama iletiyi işlemeyi tamamladıktan (veya gelecekteki işlemler için güvenilir bir şekilde depolar), alınan iletiye **tamamlandı ()** çağrısı yaparak alma işleminin ikinci aşamasını tamamlar. Service Bus, **tüm ()** çağrısını gördüğünde, iletiyi tüketildiği gibi işaretler ve kuyruktan kaldırır. 

Aşağıdaki örnek, iletilerin **PeekLock** modu kullanılarak nasıl alınacağını ve işlenebileceğinizi gösterir (varsayılan mod değil). Aşağıdaki örnek, kayıtlı bir ileti işleyicisiyle geri çağırma modelini kullanır ve mesajlarımıza geldikçe iletileri işler `TestQueue` . Bu mod, geri çağırma normal olarak döner ve geri çağırma bir özel durum oluşturursa **Abandon ()** **öğesini çağırır.** 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Uygulama çökmelerini ve okunmayan iletileri giderme
Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Bir alıcı uygulamasının iletiyi bazı nedenlerle işleyemezse, istemci nesnesinde, **Getlocktoken ()** ile alınan ileti kilit belirteci ile birlikte **Abandon ()** metodunu çağırabilir. Bu işlem Service Bus hizmetinin kuyruktaki iletinin kilidini açmasına ve iletiyi aynı veya başka bir kullanıcı uygulama tarafından tekrar alınabilir hale getirmesine neden olur.

Ayrıca, kuyruk içinde kilitlenen bir iletiyle ilişkili bir zaman aşımı vardır ve uygulamanın kilit zaman aşımı dolmadan önce iletiyi işleyemezse (örneğin, uygulama çökerse) Service Bus, otomatik olarak iletinin kilidini açar ve tekrar alınabilmesini sağlar.

Uygulamanın ileti işlendikten sonra çöktüğü durumda, ancak **tamamlanma ()** isteği verildikten önce, yeniden başlatıldığında ileti uygulamaya yeniden gönderilir. Bu durum *En Az Bir Kez İşleme* olarak adlandırılır. Her ileti en az bir kez işlenir ancak belirli durumlarda aynı ileti yeniden teslim edilebilir. Senaryo yinelenen işlemeyi kabul etmiyorsa yinelenen ileti teslimine izin vermek için uygulama geliştiricilerin uygulamaya ilave bir mantık eklemesi gerekir. Bu genellikle iletinin **Getmessageıd** yöntemi kullanılarak sağlanır ve bu durum, teslim girişimleri arasında sabit kalır.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki Adımlar
, [ `azure-service-bus` Depoda](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)GitHub 'da Java örnekleri bulabilirsiniz.

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
