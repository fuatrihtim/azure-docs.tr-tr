---
title: Azure Işlevlerinde Python işlev uygulamalarının sorunlarını giderme
description: Python işlevleriyle ilgili sorunları nasıl giderebileceğinizi öğrenin.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87846046"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Azure İşlevleri'nde Python hatalarını giderme

Aşağıda, Python işlevlerinde yaygın sorunlara yönelik sorun giderme kılavuzlarının bir listesi verilmiştir:

* [Modulenotfoun, ımporterror](#troubleshoot-modulenotfounderror)
* [' Cygrpc ' içeri aktarılamıyor](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>Modulenotfoun, sorunlarını giderme

Bu bölüm, Python işlev uygulamanızda modülle ilgili hataları gidermenize yardımcı olur. Bu hatalar genellikle aşağıdaki Azure Işlevleri hata iletisine neden olacaktır:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Bu hata sorunu, bir Python işlevi uygulamasının bir Python modülünü yükleyemediğinde meydana gelir. Bu hatanın kök nedeni aşağıdaki sorunlardan biridir:

- [Paket bulunamıyor](#the-package-cant-be-found)
- [Paket, uygun Linux tekerleği ile çözümlenmiyor](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Paket, Python yorumlayıcı sürümüyle uyumsuz](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Paket diğer paketlerle çakışıyor](#the-package-conflicts-with-other-packages)
- [Paket yalnızca Windows veya macOS platformlarını destekler](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Proje dosyalarını görüntüle

Sorununuzla ilgili gerçek nedeni belirlemek için, işlev uygulamanızda çalışan Python proje dosyalarını almanız gerekir. Yerel bilgisayarınızda proje dosyalarınız yoksa, bunları aşağıdaki yollarla alabilirsiniz:

- İşlev uygulamasının `WEBSITE_RUN_FROM_PACKAGE` uygulama ayarı varsa ve değeri BIR URL ise, URL 'yi kopyalayıp tarayıcınıza yapıştırarak dosyayı indirin.
- İşlev uygulaması `WEBSITE_RUN_FROM_PACKAGE` ve olarak ayarlanmışsa `1` , ' a gidin `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` ve dosyayı en son URL 'den indirin `href` .
- İşlev uygulamasının yukarıda belirtilen uygulama ayarı yoksa, ' a gidin `https://<app-name>.scm.azurewebsites.net/api/settings` ve URL 'yi bulun `SCM_RUN_FROM_PACKAGE` . URL 'YI kopyalayıp tarayıcınıza yapıştırarak dosyayı indirin.
- Bunlardan hiçbiri sizin için uygun değilse, ' a gidin `https://<app-name>.scm.azurewebsites.net/DebugConsole` ve içeriğini ortaya çıkar `/home/site/wwwroot` .

Bu makalenin geri kalanında, işlev uygulamanızın içeriğini inceleyerek, kök nedeni tanımlayarak ve belirli sorunu çözmenize yardımcı olan bu hatanın olası nedenlerine ilişkin sorunları gidermenize yardımcı olur.

### <a name="diagnose-modulenotfounderror"></a>Modulenotfoun, tanılayın

Bu bölümde modülle ilgili hataların olası temel nedenleri ayrıntılı olarak ele alınmasına neden olur. Büyük olasılıkla kök neden olduğunu belirledikten sonra ilgili hafifletme bölümüne gidebilirsiniz.

#### <a name="the-package-cant-be-found"></a>Paket bulunamıyor

Veya konumuna `.python_packages/lib/python3.6/site-packages/<package-name>` gidin `.python_packages/lib/site-packages/<package-name>` . Dosya yolu yoksa, bu yol büyük olasılıkla ana neden olur.

Dağıtım sırasında üçüncü taraf veya güncel olmayan araçların kullanılması bu soruna neden olabilir.

Bkz. azaltma için [uzak derlemeyi etkinleştirme](#enable-remote-build) veya [Yerel bağımlılıkları yapılandırma](#build-native-dependencies) .

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Paket, uygun Linux tekerleği ile çözümlenmiyor

Veya ' a gidin `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . **Tekerlek** dosyasını açmak için en sevdiğiniz metin düzenleyiciyi kullanın ve **etiketi:** bölümünü kontrol edin. Etiketin değeri **Linux** içermiyorsa, bu sorun olabilir.

Python işlevleri yalnızca Azure 'da Linux üzerinde çalışır: Işlevler Runtime v2. x, Deuter Esnette ve v3. x çalışma zamanı üzerinde çalışır. Yapıtın doğru Linux ikililerini içermesi beklenir. `--build local`Temel araçlar, üçüncü taraf veya güncel olmayan araçlarda bayrak kullanımı, eski ikililerin kullanılmasına neden olabilir.

Bkz. azaltma için [uzak derlemeyi etkinleştirme](#enable-remote-build) veya [Yerel bağımlılıkları yapılandırma](#build-native-dependencies) .

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Paket, Python yorumlayıcı sürümüyle uyumsuz

Veya ' a gidin `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Bir metin düzenleyicisi kullanarak meta VERI dosyasını açın ve **sınıflandırıcılar:** bölümüne bakın. Bölüm,, veya içermiyorsa `Python :: 3` , `Python :: 3.6` `Python :: 3.7` `Python :: 3.8` paket sürümü çok eski veya büyük olasılıkla paketin zaten bakım dışı olduğu anlamına gelir.

[Azure Portal](https://portal.azure.com)Işlev uygulamanızın Python sürümünü kontrol edebilirsiniz. İşlev uygulamanıza gidin, **Kaynak Gezgini**' ni seçin ve **Git**' i seçin.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Portalda işlev uygulaması için Kaynak Gezgini açın":::

Gezgin yüklendikten sonra, Python sürümünü gösteren **Linuxfxversion**' ı arayın.

Bkz. [paketinizi en son sürüme güncelleştirme](#update-your-package-to-the-latest-version) ya da [paketi azaltma Için eşdeğerleriyle değiştirme](#replace-the-package-with-equivalents) .

#### <a name="the-package-conflicts-with-other-packages"></a>Paket diğer paketlerle çakışıyor

Paketin uygun Linux tekerlekiyle doğru şekilde çözümlendiğini doğruladıysanız, diğer paketlerle bir çakışma olabilir. Bazı paketlerde, PyPi belgeleri uyumsuz modülleri açıklığa kavuşturmayabilir. Örneğin, içinde [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) aşağıdaki gibi bir ifade vardır:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Paket sürümünüzün belgelerini ' de bulabilirsiniz `https://pypi.org/project/<package-name>/<package-version>` .

Bkz. [paketinizi en son sürüme güncelleştirme](#update-your-package-to-the-latest-version) ya da [paketi azaltma Için eşdeğerleriyle değiştirme](#replace-the-package-with-equivalents) .

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Paket yalnızca Windows veya macOS platformlarını destekler

`requirements.txt`' İ bir metin düzenleyici ile açın ve içindeki paketi kontrol edin `https://pypi.org/project/<package-name>` . Bazı paketler yalnızca Windows veya macOS platformlarında çalışır. Örneğin, pywin32 yalnızca Windows üzerinde çalışır.

`Module Not Found`Yerel geliştirme Için Windows veya macOS kullanırken hata oluşmayabilir. Ancak, paket, çalışma zamanında Linux kullanan Azure Işlevleri üzerine içeri aktaramazsa. Bu durum, `pip freeze` Proje başlatma sırasında Windows veya macOS makinenizden requirements.txt sanal ortamı dışarı aktarmak için kullanılması olabilir.

Risk azaltma için [paketi eşdeğerleri veya el Ile değiştirme](#replace-the-package-with-equivalents) [requirements.txt](#handcraft-requirementstxt) bakın.

### <a name="mitigate-modulenotfounderror"></a>Modulenotfounbir şekilde azaltma

Aşağıda modülle ilgili sorunlar için olası azaltmalardır. Bu azaltıcı etkenleri kullanmayı öğrenmek için [Yukarıdaki tanılar](#diagnose-modulenotfounderror) kullanın.

#### <a name="enable-remote-build"></a>Uzak derlemeyi etkinleştir

Uzak derleme özelliğinin etkinleştirildiğinden emin olun. Bunu yapmanın yolu, dağıtım yönteğinize bağlıdır.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
[Visual Studio Code Için Azure işlevleri uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) en son sürümünün yüklü olduğundan emin olun. Var olduğunu `.vscode/settings.json` ve ayarı içerdiğini doğrulayın `"azureFunctions.scmDoBuildDuringDeployment": true` . Aksi takdirde, bu dosyayı `azureFunctions.scmDoBuildDuringDeployment` etkin ayarıyla oluşturun ve projeyi yeniden dağıtın.

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) 'nin en son sürümünün yüklü olduğundan emin olun. Yerel işlev proje klasörünüze gidin ve `func azure functionapp publish <app-name>` dağıtım için kullanın.

## <a name="manual-publishing"></a>[El ile yayımlama](#tab/manual)

Paketinizi uç noktaya el ile yayımlıyorsanız `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` , hem **SCM_DO_BUILD_DURING_DEPLOYMENT** hem de **ENABLE_ORYX_BUILD** **doğru** olarak ayarlandığından emin olun. Daha fazla bilgi için bkz. [uygulama ayarları ile çalışma](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Yerel bağımlılıkları oluştur

Hem **Docker** hem de [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) 'nin en son sürümünün yüklü olduğundan emin olun. Yerel işlev proje klasörünüze gidin ve `func azure functionapp publish <app-name> --build-native-deps` dağıtım için kullanın.

#### <a name="update-your-package-to-the-latest-version"></a>Paketinizi en son sürüme güncelleştirin

' Deki en son paket sürümüne göz atın `https://pypi.org/project/<package-name>` ve **sınıflandırıcılar:** bölümüne bakın. Paketin ya `OS Independent` da `POSIX` `POSIX :: Linux` **işletim sistemi** ile uyumlu olması gerekir. Ayrıca, programlama dili,,, `Python :: 3` `Python :: 3.6` veya içermelidir `Python :: 3.7` `Python :: 3.8` .

Bunlar doğruysa, requirements.txt satırı değiştirerek paketi en son sürüme güncelleştirebilirsiniz `<package-name>~=<latest-version>` .

#### <a name="handcraft-requirementstxt"></a>Eller requirements.txt

Bazı geliştiriciler, `pip freeze > requirements.txt` geliştirme ortamları Için Python paketlerinin listesini oluşturmak için kullanır. Çoğu durumda bu kolaylık de çalışır, ancak Windows veya macOS 'ta işlev geliştirme, ancak Linux üzerinde çalışan bir işlev uygulamasına yayımlama gibi platformlar arası dağıtım senaryolarında sorunlar olabilir. Bu senaryoda, `pip freeze` yerel geliştirme ortamınız için beklenmeyen işletim sistemine özgü bağımlılıkları veya bağımlılıkları ortaya çıkarabilir. Bu bağımlılıklar, Linux üzerinde çalışırken Python işlev uygulamasını bozabilir.

En iyi uygulama, proje kaynak kodunuzda bulunan her bir. Kopyala dosyasından içeri aktarma ekstresini denetme ve yalnızca requirements.txt dosyasında bu modülleri iade etme yöntemidir. Bu, paketlerin çözümlenme işleminin farklı işletim sistemlerinde düzgün işlenebilmesini güvence altına alır.

#### <a name="replace-the-package-with-equivalents"></a>Paketi eşdeğerleriyle değiştirme

İlk olarak, içindeki paketin en son sürümüne bakmamız gerekir `https://pypi.org/project/<package-name>` . Genellikle bu paketin kendi GitHub sayfası vardır, GitHub 'daki **sorunlar** bölümüne gidin ve sorununuz düzeltildiğinde arama yapın. Bu durumda, paketi en son sürüme güncelleştirin.

Bazen paket, [Python Standart Kitaplığı](https://docs.python.org/3/library/) (pathlib gibi) ile tümleştirilmiş olabilir. Bu durumda, Azure Işlevlerinde belirli bir Python dağıtımı sağladığımızdan (Python 3,6, Python 3,7 ve Python 3,8), requirements.txt paketin kaldırılması gerekir.

Bununla birlikte, düzeltilmeyen bir sorunla karşılaşdıysanız ve son tarih olduğunuzda. Bir araştırma yapıp projeniz için benzer bir paket bulmanız önerilir. Genellikle, Python topluluğu kullanabileceğiniz çok çeşitli benzer kitaplıklar sağlar.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>Sorun giderme ' cygrpc ' içeri aktarılamıyor

Bu bölüm, Python işlev uygulamanızda ' cygrpc ' ile ilgili hataları gidermenize yardımcı olur. Bu hatalar genellikle aşağıdaki Azure Işlevleri hata iletisine neden olacaktır:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

Bu hata sorunu, bir Python işlev uygulaması uygun bir Python yorumlayıcısıyla başlayamazsa oluşur. Bu hatanın kök nedeni aşağıdaki sorunlardan biridir:

- [Python yorumlayıcı işletim sistemi mimarisiyle uyuşmuyor](#the-python-interpreter-mismatches-os-architecture)
- [Python yorumlayıcı, Azure Işlevleri Python Worker tarafından desteklenmiyor](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>' Cygrpc ' başvuru hatasını Tanıla

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Python yorumlayıcı işletim sistemi mimarisiyle uyuşmuyor

Bu durum büyük olasılıkla 32 bitlik bir Python yorumlayıcısı 64 bit işletim sisteminize yüklenir.

X64 işletim sisteminde çalıştırıyorsanız, lütfen Python 3,6, 3,7 veya 3,8 yorumlayıcının de 64 bit sürümde olduğundan emin olun.

Aşağıdaki komutlarla Python yorumlayıcı bit durumunu kontrol edebilirsiniz:

PowerShell 'de Windows 'ta: `py -c 'import platform; print(platform.architecture()[0])'`

UNIX benzeri kabukta: `python3 -c 'import platform; print(platform.architecture()[0])'`

Python yorumlayıcı bit kullanımı ve işletim sistemi mimarisi arasında uyuşmazlık varsa, lütfen [Python Software Foundation](https://python.org/downloads/release)'dan uygun bir Python yorumlayıcı indirin.

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Python yorumlayıcı, Azure Işlevleri Python Worker tarafından desteklenmiyor

Azure Işlevleri Python Worker yalnızca Python 3,6, 3,7 ve 3,8 ' yi destekler.
Lütfen Python yorumlayıcının `py --version` Windows veya UNIX benzeri sistemlerde beklenen sürümümüzle eşleşip eşleşmediğini denetleyin `python3 --version` . Döndürülen sonucun Python 3.6. x, Python 3.7. x veya Python 3.8. x olduğundan emin olun.

Python yorumlayıcı sürümünüz beklentimizi karşılamıyorsa, lütfen [Python Software Foundation](https://python.org/downloads/release)'da Python 3,6, 3,7 veya 3,8 yorumlayıcısını indirin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu çözemezse lütfen Işlevler ekibine bildirin:

> [!div class="nextstepaction"]
> [Çözümlenmemiş bir sorunu bildir](https://github.com/Azure/azure-functions-python-worker/issues)
