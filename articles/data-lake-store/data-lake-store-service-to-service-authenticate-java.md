---
title: Hizmetten hizmete kimlik doğrulaması-Data Lake Storage 1. – Java SDK
description: Java ile Azure Active Directory kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması elde etme hakkında bilgi edinin
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000394"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Java kullanarak Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması

> [!div class="op_single_selector"]
> * [Java'yı kullanma](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK’sını kullanma](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Bu makalede, Azure Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması yapmak için Java SDK 'sını nasıl kullanacağınızı öğreneceksiniz. Java SDK kullanarak Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Active Directory "Web" uygulaması oluşturun**. [Azure Active Directory kullanarak, Data Lake Storage 1. Ile hizmetten hizmete kimlik doğrulaması](data-lake-store-service-to-service-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

* [Maven](https://maven.apache.org/install.html). Bu eğiticide, yapı ve proje bağımlılıkları için Maven kullanılır. Maven veya Gradle gibi bir yapı sistemi kullanılmadan derlemek mümkün olsa da, bu sistemler bağımlılıkları yönetmeyi çok daha kolay hale getirir.

* Seçim [IntelliJ fikri](https://www.jetbrains.com/idea/download/) veya [Çakışan](https://www.eclipse.org/downloads/) veya benzer bir IDE.

## <a name="service-to-service-authentication"></a>Hizmetten hizmete kimlik doğrulaması

1. Komut satırından [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) veya bir IDE kullanarak Maven projesi oluşturun. IntelliJ kullanarak Java projesi oluşturma yönergeleri için [buraya](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) bakın. Eclipse kullanarak proje oluşturma yönergeleri için [buraya](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) bakın.

2. Maven **pom.xml** dosyanıza aşağıdaki bağımlılıkları ekleyin. Etiketinden önce aşağıdaki kod parçacığını ekleyin **\</project>** :

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    Birinci bağımlılık, `azure-data-lake-store-sdk` Maven deposundan Data Lake Storage 1. SDK () kullanmaktır. İkinci bağımlılık, bu uygulama için hangi günlük altyapısının (`slf4j-nop`) kullanılacağını belirtmektir. Data Lake Storage 1. SDK, Log4J, Java günlüğü, logback, vb. gibi birçok popüler günlük çerçevesi arasından seçim yapmanızı sağlayan [dolayısıyla slf4j](https://www.slf4j.org/) günlük façlade kullanır. Bu örnekte, günlük kaydını devre dışı bırakacak ve dolayısıyla **slf4j-nop** bağlamasını kullanacağız. Uygulamanızda diğer günlük seçeneklerini kullanmak için [buraya](https://www.slf4j.org/manual.html#projectDep) bakın.

3. Aşağıdaki içeri aktarma deyimlerini uygulamanıza ekleyin.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Daha önce oluşturduğunuz Active Directory Web uygulaması için belirteç almak üzere Java uygulamanızda aşağıdaki kod parçacığını kullanın `AccessTokenProvider` (aşağıdaki örnekte kullanır `ClientCredsTokenProvider` ). Belirteç sağlayıcısı, bellekteki belirteci almak için kullanılan kimlik bilgilerini önbelleğe alır ve süre sonu yaklaştığında belirteci otomatik olarak yeniler. Belirteçlerinin kendi alt sınıflarından oluşturulması olasıdır. bu `AccessTokenProvider` nedenle belirteçler müşteri kodunuz tarafından elde edilir. Şimdilik, SDK 'da sağlanandan yalnızca birini kullanalım.

    **BURAYI DOLDURUN** alanını, Azure Active Directory Web uygulaması için gerçek değerlerle değiştirin.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Data Lake Storage 1. SDK, Data Lake Storage 1. hesabıyla konuşmak için gereken güvenlik belirteçlerini yönetmenizi sağlayan uygun yöntemler sağlar. Bununla birlikte, SDK yalnızca bu yöntemlerin kullanılmasını zorunlu kılmaz. [Azure Active Directory SDK’sını](https://github.com/AzureAD/azure-activedirectory-library-for-java) veya kendi özel kodunuzu kullanma gibi diğer belirteç edinme yöntemlerinden yararlanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Java SDK kullanarak Data Lake Storage 1. kimlik doğrulaması yapmak için son kullanıcı kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık Data Lake Storage 1. çalışmak için Java SDK 'sını kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [Java SDK kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-get-started-java-sdk.md)
