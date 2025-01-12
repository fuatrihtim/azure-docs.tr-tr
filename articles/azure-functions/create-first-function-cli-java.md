---
title: Komut satırından Java işlevi oluşturma-Azure Işlevleri
description: Komut satırından Java işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 0ef52da8400cfadfaaa26664d984d8b898a3b9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729309"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: komut satırından Azure 'da Java işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Bu makalede, HTTP isteklerine yanıt veren bir Java işlevi oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

> [!NOTE]
> Maven, tercih ettiğiniz geliştirme aracınız değilse, [Gradle](./functions-create-first-java-gradle.md), [IntelliJ fikir](/azure/developer/java/toolkit-for-intellij/quickstart-functions) ve [Visual Studio Code](create-first-function-vs-code-java.md)kullanarak Java geliştiricileri için benzer öğreticilerimize göz atın.

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

+ [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya sonraki bir sürümü.

+ [Java geliştirici seti](/azure/developer/java/fundamentals/java-jdk-long-term-support), sürüm 8 veya 11. `JAVA_HOME`Ortam değişkeni, JDK 'nin doğru sürümünün yükleneceği konum olarak ayarlanmalıdır.     

+ [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri.

### <a name="prerequisite-check"></a>Önkoşul denetimi

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. İşlevler projesini bir [Maven arketipinden](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) oluşturmak için boş bir klasörde aşağıdaki komutu çalıştırın. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + İşlevlerinizin Java 11 üzerinde çalışmasını istiyorsanız `-DjavaVersion=11` ifadesini kullanın. Daha fazla bilgi için bkz. [Java sürümleri](functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Bu makaleyi tamamlayabilmeniz için ortam değişkeni, JDK 'nin doğru sürümünün yükleneceği konum olarak ayarlanmalıdır.

1. Maven, dağıtımda projenin oluşturulmasını tamamlaması için gereken değerleri ister.   
    İstendiğinde aşağıdaki değerleri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Java için [paket adlandırma kurallarını](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) takip eden tüm projelerde projenizi benzersiz bir şekilde tanımlayan bir değer. |
    | **ArtifactId** | `fabrikam-functions` | Bir sürüm numarası olmadan jar adı olan bir değer. |
    | **Sürüm** | `1.0-SNAPSHOT` | Varsayılan değeri seçin. |
    | **leyebilir** | `com.fabrikam` | Oluşturulan işlev kodu için Java paketi olan bir değer. Varsayılan değeri kullanın. |

1. `Y`Onaylamak için yazın veya ENTER tuşuna basın.

    Maven, proje dosyalarını, bu örnekte olduğu gibi, _ArtifactId_ adında yeni bir klasörde oluşturur `fabrikam-functions` . 

1. Proje klasörüne gidin:

    ```console
    cd fabrikam-functions
    ```

    Bu klasör, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.

### <a name="optional-examine-the-file-contents"></a>Seçim Dosya içeriğini inceleyin

İsterseniz, [işlevi yerel olarak çalıştırmayı](#run-the-function-locally) atlayıp dosya içeriğini daha sonra incelemenizi sağlayabilirsiniz.

#### <a name="functionjava"></a>Function. Java
*Function. Java* , `run` değişkende istek verilerini alan bir yöntem içerir `request` . Bu, tetikleme davranışını tanımlayan [httptrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) ek açıklaması ile donatılmış bir [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) 'dir. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Yanıt iletisi [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API 'si tarafından oluşturulur.

#### <a name="pomxml"></a>pom.xml

Uygulamanızı barındırmak üzere oluşturulan Azure kaynakları için ayarlar, eklentinin **yapılandırma** öğesinde oluşturulan pom.xml dosyasında bir **GroupID** ile tanımlanmıştır `com.microsoft.azure` . Örneğin, aşağıdaki yapılandırma öğesi, bir Maven tabanlı dağıtıma `java-functions-group` bölge içindeki kaynak grubunda bir işlev uygulaması oluşturmasını söyler `westus` . İşlev uygulaması, planda barındırılan Windows üzerinde çalışır `java-functions-app-service-plan` ; Bu, varsayılan olarak sunucusuz bir tüketim plandır.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Bu ayarları, kaynakların Azure 'da nasıl oluşturulduğunu denetlemek için, `runtime.os` `windows` ilk dağıtımdan önce ' dan önce ' ye geçiş yaparak yapabilirsiniz `linux` . Maven eklentisi tarafından desteklenen ayarların tam listesi için [yapılandırma ayrıntılarına](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)bakın.

#### <a name="functiontestjava"></a>FunctionTest. Java

Ayrıca, bu, işleviniz için bir birim testi de oluşturur. Bağlama eklemek veya projeye yeni işlevler eklemek için işlevinizi değiştirdiğinizde, *Functiontest. Java* dosyasındaki testleri de değiştirmeniz gerekir.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

1. Yerel Azure Işlevleri çalışma zamanı konağını *Localfunctionproj* klasöründen başlatarak işlevinizi çalıştırın:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    Çıktının sonuna doğru, aşağıdaki satırlar görünmelidir:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > HttpExample yukarıda gösterildiği gibi görünmezse, büyük olasılıkla ana bilgisayarı projenin kök klasörü dışından başlatmış olursunuz. Bu durumda,  + Konağı durdurmak için CTRL **C** 'yi kullanın, projenin kök klasörüne gidin ve önceki komutu yeniden çalıştırın.

1. `HttpExample`İşlevinizin URL 'sini bu çıktıdan tarayıcıya kopyalayın ve sorgu dizesini ekleyerek `?name=<YOUR_NAME>` tam URL 'yi yapın `http://localhost:7071/api/HttpExample?name=Functions` . Tarayıcı şöyle bir ileti görüntülemelidir `Hello Functions` :

    ![İşlevin sonuçları tarayıcıda yerel olarak çalıştırıldı](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Projenizi başlattığınız Terminal Ayrıca, istek yaptığınız sürece günlük çıktısını gösterir.

1. İşiniz bittiğinde **CTRL** + **C** ' yi kullanın ve `y` işlevler ana bilgisayarını durdurmayı seçin.

## <a name="deploy-the-function-project-to-azure"></a>İşlev projesini Azure 'a dağıtma

İşlevler projenizi ilk kez dağıttığınızda Azure 'da bir işlev uygulaması ve ilgili kaynaklar oluşturulur. Uygulamanızı barındırmak üzere oluşturulan Azure kaynakları için ayarlar [pom.xml dosyasında](#pomxml)tanımlanmıştır. Bu makalede, Varsayılanları kabul edersiniz.

> [!TIP]
> Windows yerine Linux üzerinde çalışan bir işlev uygulaması oluşturmak için, `runtime.os` pom.xml dosyasındaki öğesini `windows` olarak değiştirin `linux` . [Bu bölgelerde](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)Linux çalıştırmak bir tüketim planında desteklenir. Linux ve uygulamalar üzerinde çalışan ve aynı kaynak grubunda Windows üzerinde çalışan uygulamalar olamaz.

1. Dağıtım yapabilmeniz için önce Azure CLı veya Azure PowerShell kullanarak Azure aboneliğinizde oturum açın. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [Az Login](/cli/azure/reference-index#az-login) komutu sizi Azure hesabınızda oturum açar.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'i Azure hesabınızda oturum açar.

    ---

1. Projenizi yeni bir işlev uygulamasına dağıtmak için aşağıdaki komutu kullanın.

    ```console
    mvn azure-functions:deploy
    ```
    
    Bu, Azure 'da aşağıdaki kaynakları oluşturur:
    
    + Kaynak grubu. _Java-Functions-Group_ olarak adlandırılır.
    + Depolama hesabı. Işlevleri için gereklidir. Ad, depolama hesabı adı gereksinimlerine göre rastgele oluşturulur.
    + Barındırma planı. _Westus_ bölgesindeki işlev uygulamanız için sunucusuz barındırma. Ad, _Java-Functions-App-Service-plan_' dır.
    + İşlev uygulaması. İşlev uygulaması, işlevleriniz için dağıtım ve yürütme birimidir. Ad, rastgele oluşturulmuş bir sayı eklenerek, _ArtifactId_ temel alınarak rastgele oluşturulur.
    
    Dağıtım proje dosyalarını paketler ve bunları [ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy)kullanarak yeni işlev uygulamasına dağıtır. Kod, Azure 'daki dağıtım paketinden çalışır.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir [sonraki adımla](#next-steps) devam ederseniz ve bir Azure depolama kuyruğu çıktı bağlaması eklerseniz, daha önce yapmış olduğunuz yerde oluşturacağınız sürece tüm kaynaklarınızı saklayın.

Aksi takdirde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
