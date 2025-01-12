---
title: Azure Işlevlerinde dağıtım teknolojileri
description: Azure Işlevlerine kod dağıtabilmeniz için farklı yollar edinin.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943643"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Işlevlerinde dağıtım teknolojileri

Azure Işlevleri proje kodunuzu Azure 'a dağıtmak için birkaç farklı teknolojiyi kullanabilirsiniz. Bu makalede, size sunulan dağıtım yöntemlerine ilişkin bir genel bakış ve çeşitli senaryolarda kullanılacak en iyi yöntem önerileri sunulmaktadır. Ayrıca, temel alınan dağıtım teknolojileri hakkında ayrıntılı bir liste ve önemli ayrıntılar sağlar. 

## <a name="deployment-methods"></a>Dağıtım yöntemleri

Azure 'da kod yayımlamak için kullandığınız dağıtım teknolojisi, genellikle uygulamanızı yayımlama yöntemiyle belirlenir. Uygun dağıtım yöntemi, belirli gereksinimlere ve geliştirme döngüsündeki noktaya göre belirlenir. Örneğin, geliştirme ve test sırasında, Visual Studio Code gibi doğrudan geliştirme aracınızdan dağıtım yapabilirsiniz. Uygulamanız üretimde olduğunda, kaynak denetiminden sürekli olarak veya ek doğrulama ve test içeren bir otomatik yayımlama işlem hattı kullanarak yayımlamak daha olasıdır.  

Aşağıdaki tabloda, Işlev projeniz için kullanılabilir dağıtım yöntemleri açıklanmaktadır.

| Dağıtım &nbsp; türü | Yöntemler | En iyi... |
| -- | -- | -- |
| Araçlar tabanlı | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; Yayımlama](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio yayımlama](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Temel araçlar yayımlama](functions-run-local.md#publish) | Geliştirme ve diğer ad-Hock dağıtımları sırasında dağıtımlar. Dağıtımlar araç tarafından yerel olarak yönetilir. | 
| App Service yönetilen| &bull;&nbsp;[Dağıtım &nbsp; Merkezi &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Kapsayıcı &nbsp; dağıtımları](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Kaynak denetiminden veya bir kapsayıcı kayıt defterinden sürekli dağıtım (CI/CD). Dağıtımlar App Service platformu (kudu) tarafından yönetilir.|
| Dış işlem hatları|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub eylemleri](functions-how-to-github-actions.md) | Ek doğrulama, test ve diğer eylemleri içeren üretim ve DevOps işlem hatları otomatik dağıtımın bir parçası olarak çalıştırılır. Dağıtımlar işlem hattı tarafından yönetilir. |

Belirli Işlev dağıtımları, kendi bağlamına göre en iyi teknolojiyi kullanır, ancak çoğu dağıtım yöntemi [ZIP dağıtımına](#zip-deploy)dayalıdır.

## <a name="deployment-technology-availability"></a>Dağıtım teknolojisinin kullanılabilirliği

Azure Işlevleri, platformlar arası yerel geliştirme ve Windows ve Linux üzerinde barındırma desteği sunmaktadır. Şu anda üç barındırma planı kullanılabilir:

+ [Tüketim](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Adanmış (App Service)](dedicated-plan.md)

Her planın farklı davranışları vardır. Her Azure Işlevleri özelliği için tüm dağıtım teknolojileri bulunmaz. Aşağıdaki grafikte, her bir işletim sistemi ve barındırma planı birleşimi için hangi dağıtım teknolojilerinin desteklendiği gösterilmektedir:

| Dağıtım teknolojisi | Windows tüketimi | Windows Premium | Windows ayrılmış  | Linux tüketimi | Linux Premium | Linux adanmış |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Dış paket URL 'SI<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| ZIP dağıtımı |✔|✔|✔|✔|✔|✔|
| Docker kapsayıcısı | | | | |✔|✔|
| Web Dağıtımı |✔|✔|✔| | | |
| Kaynak denetimi |✔|✔|✔| |✔|✔|
| Yerel git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Bulut eşitleme<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portal düzenlemesi |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

[el ile tetikleyici eşitlemesi](#trigger-syncing)gerektiren <sup>1</sup> dağıtım teknolojisi.
<sup>2</sup> Portal düzenlemesi yalnızca Premium ve adanmış planlar kullanılarak Linux üzerinde IŞLEVLERE yönelik http ve Zamanlayıcı Tetikleyicileri için etkindir.

## <a name="key-concepts"></a>Önemli kavramlar

Bazı temel kavramlar, dağıtımların Azure Işlevlerinde nasıl çalıştığını anlamak için önemlidir.

### <a name="trigger-syncing"></a>Eşitlemeyi Tetikle

Tetikleyicilerden herhangi birini değiştirdiğinizde, Işlevler altyapısının değişiklikler farkında olmalıdır. Eşitleme, birçok dağıtım teknolojisi için otomatik olarak gerçekleşir. Ancak, bazı durumlarda tetikleyiclerinizi el ile eşitlemeniz gerekir. Güncelleştirmelerinizi bir dış paket URL 'sine, yerel git 'e, bulut eşitlemesine veya FTP 'ye başvurarak dağıttığınızda, tetikleyiclerinizi el ile eşitlemeniz gerekir. Tetikleyicileri üç yönden biriyle eşitleyebilirsiniz:

* İşlev uygulamanızı Azure portal yeniden başlatın
* `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` [Ana anahtarı](functions-bindings-http-webhook-trigger.md#authorization-keys)kullanarak bir http post isteği gönderin.
* Uygulamasına bir HTTP POST isteği gönderin `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Yer tutucuları abonelik KIMLIĞINIZ, kaynak grubu adı ve işlev uygulamanızın adıyla değiştirin.

### <a name="remote-build"></a>Uzak derleme

Azure Işlevleri, ZIP dağıtımlarından sonra aldığı kodda derlemeleri otomatik olarak gerçekleştirebilir. Bu derlemeler, uygulamanızın Windows veya Linux üzerinde çalışıyor olmasına bağlı olarak biraz farklı davranır. Bir uygulama daha önce [paket modundan Çalıştır](run-functions-from-deployment-package.md) modunda çalışmak üzere ayarlandığında uzak derlemeler gerçekleştirilmez. Uzak derlemeyi kullanmayı öğrenmek için, [ZIP dağıtımı](#zip-deploy)' na gidin.

> [!NOTE]
> Uzak derleme ile ilgili sorun yaşıyorsanız, bu durum uygulamanızın özellik kullanıma alınmadan önce oluşturulması olabilir (1 Ağustos 2019). Yeni bir işlev uygulaması oluşturmayı veya `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` işlev uygulamanızı güncelleştirmek için çalıştırmayı deneyin. Bu komutun başarılı olması iki denemeden kaynaklanabilir.

#### <a name="remote-build-on-windows"></a>Windows üzerinde uzak derleme

Windows üzerinde çalışan tüm işlev uygulamalarının SCM (veya [kudu](https://github.com/projectkudu/kudu)) sitesi küçük bir yönetim uygulamasına sahiptir. Bu site, Azure Işlevleri için dağıtım ve derleme mantığının çoğunu işler.

Bir uygulama Windows 'a dağıtıldığında, `dotnet restore` (C#) veya (JavaScript) gibi dile özgü komutlar `npm install` çalıştırılır.

#### <a name="remote-build-on-linux"></a>Linux üzerinde uzak derleme

Linux üzerinde uzak derlemeyi etkinleştirmek için aşağıdaki [uygulama ayarlarının](functions-how-to-use-azure-function-app-settings.md#settings) ayarlanmış olması gerekir:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Varsayılan olarak, Visual Studio Code için hem [Azure Functions Core Tools](functions-run-local.md) hem de [Azure Işlevleri uzantısı](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) , Linux 'a dağıtım yaparken uzak derlemeler gerçekleştirir. Bu nedenle, her iki araç da Azure 'da sizin için otomatik olarak bu ayarları oluşturur.

Uygulamalar Linux üzerinde uzaktan oluşturulduğunda, [dağıtım paketinden çalıştırılırlar](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Tüketim planı

Tüketim planında çalışan Linux işlev uygulamalarının, dağıtım seçeneklerini sınırlayan bir SCM/kudu sitesi yok. Ancak, tüketim planında çalışan Linux 'ta işlev uygulamaları uzak yapıları destekler.

##### <a name="dedicated-and-premium-plans"></a>Adanmış ve Premium planlar

Linux üzerinde çalışan işlev uygulamalarının [adanmış (App Service) planı](dedicated-plan.md) ve [Premium planda](functions-premium-plan.md) Ayrıca sınırlı bir SCM/kudu sitesi de vardır.

## <a name="deployment-technology-details"></a>Dağıtım teknolojisi ayrıntıları

Aşağıdaki dağıtım yöntemleri Azure Işlevleri 'nde kullanılabilir.

### <a name="external-package-url"></a>Dış paket URL 'SI

İşlev uygulamanızı içeren bir uzak paket (. zip) dosyasına başvurmak için bir dış paket URL 'SI kullanabilirsiniz. Dosya, girilen URL 'den indirilir ve uygulama [paket modundan Çalıştır](run-functions-from-deployment-package.md) bölümünde çalışır.

>__Nasıl kullanılır:__ [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) Uygulama ayarlarınıza ekleyin. Bu ayarın değeri bir URL olmalıdır (çalıştırmak istediğiniz belirli paket dosyasının konumu). Ayarları [portalda](functions-how-to-use-azure-function-app-settings.md#settings) ya da [Azure CLI kullanarak](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)ekleyebilirsiniz.
>
>Azure Blob depolama kullanırsanız, IŞLEVLERE pakete erişim sağlamak için [paylaşılan erişim imzası (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) olan bir özel kapsayıcı kullanın. Uygulamanın her yeniden başlatıldığı zaman içeriğin bir kopyasını getirir. Başvurunuz, uygulamanın ömrü için geçerli olmalıdır.

>__Ne zaman kullanılır:__ Dış paket URL 'SI, Kullanıcı [uzak bir derlemeyi](#remote-build) istememesi durumunda, tüketim planında Linux üzerinde çalışan Azure işlevleri için desteklenen tek dağıtım yöntemidir. Bir işlev uygulamasının başvurduğu paket dosyasını güncelleştirdiğinizde, Azure 'u uygulamanızın değiştiğini bildirmek için [Tetikleyicileri el ile eşitlemeniz](#trigger-syncing) gerekir.

### <a name="zip-deploy"></a>ZIP dağıtımı

İşlev uygulamanızı içeren bir. zip dosyasını Azure 'a göndermek için ZIP Deploy kullanın. İsteğe bağlı olarak, uygulamanızı [paketten çalışmaya](run-functions-from-deployment-package.md)başlayacak şekilde ayarlayabilir veya [uzak bir derleme](#remote-build) gerçekleşmektir.

>__Nasıl kullanılır:__ En sevdiğiniz istemci aracını kullanarak dağıtın: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)veya [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)kullanarak komut satırı. Varsayılan olarak, bu araçlar ZIP dağıtımını kullanır ve [paketinden çalıştırılır](run-functions-from-deployment-package.md). Temel araçlar ve Visual Studio Code uzantısı, Linux 'a dağıtım yaparken [uzak derlemeyi](#remote-build) etkinleştirir. Bir. zip dosyasını işlev uygulamanıza el ile dağıtmak için, [bir. zip dosyasından veya URL 'Den dağıtma](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)' daki yönergeleri izleyin.

>ZIP dağıtımını kullanarak dağıtırken, uygulamanızı [paketten çalışacak](run-functions-from-deployment-package.md)şekilde ayarlayabilirsiniz. Paketten çalıştırmak için [ `WEBSITE_RUN_FROM_PACKAGE` ] (işlevler-App-settings. MD # website_run_from_package uygulama ayarı değerini olarak ayarlayın `1` . ZIP dağıtımı önerilir. Uygulamalarınız için daha hızlı yükleme süreleri verir ve VS Code, Visual Studio ve Azure CLı için varsayılandır.

>__Ne zaman kullanılır:__ ZIP dağıtımı, Azure Işlevleri için önerilen dağıtım teknolojisidir.

### <a name="docker-container"></a>Docker kapsayıcısı

İşlev uygulamanızı içeren bir Linux kapsayıcı görüntüsü dağıtabilirsiniz.

>__Nasıl kullanılır:__ Premium veya adanmış planda bir Linux işlev uygulaması oluşturun ve hangi kapsayıcı görüntüsünün çalıştırılacağını belirtin. Bunu iki şekilde yapabilirsiniz:
>
>* Azure portal bir Azure App Service planında bir Linux işlev uygulaması oluşturun. **Yayımla** Için **Docker görüntüsü**' nü seçin ve kapsayıcıyı yapılandırın. Resmin barındırıldığı konumu girin.
>* Azure CLı kullanarak App Service planında bir Linux işlev uygulaması oluşturun. Nasıl yapılacağını öğrenmek için bkz. [özel bir görüntü kullanarak Linux üzerinde Işlev oluşturma](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Özel bir kapsayıcı kullanarak mevcut bir uygulamaya dağıtmak için, [Azure Functions Core Tools](functions-run-local.md)bölümünde [`func deploy`](functions-run-local.md#publish) komutunu kullanın.

>__Ne zaman kullanılır:__ İşlev uygulamanızın çalıştırıldığı Linux ortamı üzerinde daha fazla denetime ihtiyacınız olduğunda Docker kapsayıcı seçeneğini kullanın. Bu dağıtım mekanizması yalnızca Linux üzerinde çalışan Işlevler için kullanılabilir.

### <a name="web-deploy-msdeploy"></a>Web Dağıtımı (MSDeploy)

Web Dağıtımı paketler ve Windows uygulamalarınızı Azure 'da Windows üzerinde çalışan işlev uygulamalarınız da dahil olmak üzere herhangi bir IIS sunucusuna dağıtır.

>__Nasıl kullanılır:__ [Azure işlevleri Için Visual Studio Araçları](functions-create-your-first-function-visual-studio.md)'nı kullanın. Paket dosyasından **Çalıştır (önerilir)** onay kutusunu temizleyin.
>
>Ayrıca, [3,6 Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) indirebilir ve doğrudan çağırabilirsiniz `MSDeploy.exe` .

>__Ne zaman kullanılır:__ Web Dağıtımı desteklenir ve sorun yoktur, ancak tercih edilen mekanizma, [paketten Çalıştır özelliği etkinken ZIP dağıtmaktır](#zip-deploy). Daha fazla bilgi için bkz. [Visual Studio geliştirme Kılavuzu](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kaynak denetimi

İşlev uygulamanızı bir git deposuna bağlamak için kaynak denetimi kullanın. Bu depodaki koda yönelik bir güncelleştirme dağıtımı tetikler. Daha fazla bilgi için bkz. [kudu wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Nasıl kullanılır:__ Kaynak denetiminden yayımlamayı ayarlamak için portalın Işlevler alanında Dağıtım Merkezi ' ni kullanın. Daha fazla bilgi için bkz. [Azure işlevleri Için sürekli dağıtım](functions-continuous-deployment.md).

>__Ne zaman kullanılır:__ Kaynak denetimini kullanmak, işlev uygulamalarında işbirliği yapan takımlar için en iyi uygulamadır. Kaynak denetimi, daha karmaşık dağıtım işlem hatlarını sağlayan iyi bir dağıtım seçeneğidir.

### <a name="local-git"></a>Yerel Git

Yerel bir git 'i kullanarak yerel makinenizden Azure Işlevlerine kod göndererek git 'i kullanabilirsiniz.

>__Nasıl kullanılır:__ [Azure App Service Için yerel git dağıtımı](../app-service/deploy-local-git.md)'ndaki yönergeleri izleyin.

>__Ne zaman kullanılır:__ Genel olarak, farklı bir dağıtım yöntemi kullanmanızı öneririz. Yerel git 'ten yayımladığınızda, [Tetikleyicileri el ile eşitlemeniz](#trigger-syncing)gerekir.

### <a name="cloud-sync"></a>Bulut eşitleme

İçeriğinizi Dropbox ve OneDrive 'dan Azure Işlevlerine eşitlemek için bulut eşitlemesi kullanın.

>__Nasıl kullanılır:__ [Bir bulut klasöründen Içerik eşitleme](../app-service/deploy-content-sync.md)bölümündeki yönergeleri izleyin.

>__Ne zaman kullanılır:__ Genel olarak, diğer dağıtım yöntemlerini öneririz. Bulut eşitlemesini kullanarak yayımladığınızda, [Tetikleyicileri el ile eşitlemeniz](#trigger-syncing)gerekir.

### <a name="ftp"></a>FTP

FTP 'yi kullanarak doğrudan Azure Işlevlerine dosya aktarabilirsiniz.

>__Nasıl kullanılır:__ [FTP/s kullanarak Içerik dağıtma](../app-service/deploy-ftp.md)' daki yönergeleri izleyin.

>__Ne zaman kullanılır:__ Genel olarak, diğer dağıtım yöntemlerini öneririz. FTP kullanarak yayımladığınızda, [Tetikleyicileri el ile eşitlemeniz](#trigger-syncing)gerekir.

### <a name="portal-editing"></a>Portal düzenlemesi

Portal tabanlı düzenleyicide, işlev uygulamanızda olan dosyaları doğrudan düzenleyebilirsiniz (aslında yaptığınız değişiklikleri her kaydedişinizde dağıtım yapabilirsiniz).

>__Nasıl kullanılır:__ Azure portal işlevlerinizi düzenleyebilmek için, [işlevlerinizi portalda oluşturmuş](./functions-get-started.md)olmanız gerekir. Tek bir gerçeği kaynağını korumak için, başka bir dağıtım yöntemi kullanmak, işlevinizi salt okunurdur ve Portal düzenlemesini devam ettirmesini önler. Azure portal dosyalarınızı düzenleyebileceğiniz bir duruma geri dönmek için, düzenleme modunu el ile yeniden açıp `Read/Write` dağıtım ile ilgili tüm uygulama ayarlarını kaldırabilirsiniz (örneğin, [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Ne zaman kullanılır:__ Portal, Azure Işlevleri 'ni kullanmaya başlamak için iyi bir yoldur. Daha yoğun bir geliştirme çalışması için aşağıdaki istemci araçlarından birini kullanmanızı öneririz:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (komut satırı)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Aşağıdaki tabloda, Portal düzenlemesini destekleyen işletim sistemleri ve diller gösterilmektedir:

| Dil | Windows tüketimi | Windows Premium | Windows ayrılmış | Linux tüketimi | Linux Premium | Linux adanmış |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# betiği |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Önizleme) | | | | | | |
| PowerShell (Önizleme) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Portal düzenlemesi yalnızca Premium ve adanmış planlar kullanılarak Linux üzerinde IŞLEVLERE yönelik HTTP ve Zamanlayıcı Tetikleyicileri için etkindir.

## <a name="deployment-behaviors"></a>Dağıtım davranışları

Bir dağıtım gerçekleştirdiğinizde, istekleri işlemeye başlamak için yeni kod yüklendikten sonra, mevcut tüm yürütmelerin tamamlanmasını veya zaman aşımına izin verilir.

Bu geçiş üzerinde daha fazla denetime ihtiyacınız varsa dağıtım yuvalarını kullanmanız gerekir.

## <a name="deployment-slots"></a>Dağıtım yuvaları

İşlev uygulamanızı Azure 'a dağıttığınızda, doğrudan üretime eklemek yerine ayrı bir dağıtım yuvasına dağıtım yapabilirsiniz. Dağıtım yuvaları hakkında daha fazla bilgi için bkz. Ayrıntılar için [Azure Işlevleri dağıtım yuvaları](functions-deployment-slots.md) belgeleri.

## <a name="next-steps"></a>Sonraki adımlar

İşlev uygulamalarınızı dağıtma hakkında daha fazla bilgi edinmek için şu makaleleri okuyun:

+ [Azure Işlevleri için sürekli dağıtım](functions-continuous-deployment.md)
+ [Azure DevOps kullanarak sürekli teslim](functions-how-to-azure-devops.md)
+ [Azure Işlevleri için ZIP dağıtımları](deployment-zip-push.md)
+ [Azure Işlevlerinizi bir paket dosyasından çalıştırın](run-functions-from-deployment-package.md)
+ [Azure Işlevlerinde işlev uygulamanız için kaynak dağıtımını otomatikleştirme](functions-infrastructure-as-code.md)
