---
title: Komut satırından bir PowerShell işlevi oluşturma-Azure Işlevleri
description: Komut satırından bir PowerShell işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937238"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından bir PowerShell işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Bu makalede, HTTP isteklerine yanıt veren bir PowerShell işlevi oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-powershell.md) de vardır.

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

+ Azure kaynakları oluşturmak için aşağıdaki araçlardan biri:

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Önkoşul denetimi

Azure CLı veya Azure kaynakları oluşturmak için Azure PowerShell kullanıp kullanmayacağınızı temel alan önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `(Get-Module -ListAvailable Az).Version`Sürüm 5,0 veya üstünü çalıştırın ve doğrulayın. 

+ `Connect-AzAccount`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

---

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutu aşağıdaki gibi çalıştırın:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Proje klasörüne gidin:

    ```console
    cd LocalFunctionProj
    ```
    
    Bu klasör, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.
    
1. Aşağıdaki komutu kullanarak, `--name` bağımsız değişkenin işlevinizin (HttpExample) benzersiz adı olduğu ve `--template` bağımsız değişkeninin işlevin TETIKLEYICISINI (http) belirttiği, projenize bir işlev ekleyin. 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` Projenin seçtiği dile uygun bir kod dosyası ve *üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

### <a name="optional-examine-the-file-contents"></a>Seçim Dosya içeriğini inceleyin

İsterseniz, [işlevi yerel olarak çalıştırmayı](#run-the-function-locally) atlayıp dosya içeriğini daha sonra incelemenizi sağlayabilirsiniz.

#### <a name="runps1"></a>run.ps1

*run.ps1* , *function.jsüzerindeki* yapılandırmaya göre tetiklenen bir işlev betiği tanımlar.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Bir HTTP tetikleyicisi için işlev, `$Request` *function.jsüzerinde* tanımlanan param öğesine geçirilen istek verilerini alır. function.jsüzerinde olduğu gibi tanımlanan dönüş nesnesi, `Response`  `Push-OutputBinding` cmdlet 'e yanıt olarak geçirilir. 

#### <a name="functionjson"></a>function.json

*function.js* , `bindings` tetikleyici türü de dahil olmak üzere işlevin giriş ve çıkışını tanımlayan bir yapılandırma dosyasıdır. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Her bağlama bir yön, tür ve benzersiz bir ad gerektirir. HTTP tetikleyicisinin tür ve çıkış bağlaması türünde bir giriş bağlaması vardır [`httpTrigger`](functions-bindings-http-webhook-trigger.md) [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure 'da işlev uygulaması oluşturma:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutu, Azure 'da işlev uygulaması oluşturur. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 'i Azure 'da işlev uygulaması oluşturur. 
    
    ---
    
    Önceki örnekte, değerini, `<STORAGE_NAME>` önceki adımda kullandığınız hesabın adıyla değiştirin ve öğesini `<APP_NAME>` sizin için uygun olan bir genel benzersiz adla değiştirin. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](consumption-plan.md)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma]

[Azure depolama kuyruğuna bağlanma]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
