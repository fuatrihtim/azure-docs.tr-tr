---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620198"
---
## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](../../how-to-create-immersive-reader.md) izleyin.  Ortam özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.
* [Node.js](https://nodejs.org/) ve [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/) gıbı bir IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express ile Node.js Web uygulaması oluşturma

Araçla bir Node.js Web uygulaması oluşturun `express-generator` .

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Yarn bağımlılıklarını yükleyip `request` `dotenv` daha sonra öğreticide kullanılacak bağımlılıkları ekleyin.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD kimlik doğrulaması belirteci alma

Ardından, bir Azure AD kimlik doğrulama belirteci almak için bir arka uç API 'SI yazın.

Bu bölüm için yukarıdaki Azure AD auth yapılandırması önkoşul adımından bazı değerlere ihtiyacınız vardır. Bu oturumu kaydettiğiniz metin dosyasına geri bakın.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Bu değerleri aldıktan sonra, _. env_ adlı yeni bir dosya oluşturun ve yukarıdaki özel özellik değerlerinizi sağlayarak aşağıdaki kodu içine yapıştırın. Tırnak işaretlerini veya "{" ve "}" karakterlerini eklemeyin.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ortak olmaması gereken gizli dizileri içerdiğinden, bu dosyayı kaynak denetimine yürütmemeyi unutmayın.

Sonra, _app.js_ açın ve aşağıdaki dosyayı dosyanın en üstüne ekleyin. Bu,. env dosyasında tanımlanan özellikleri, düğümüne ortam değişkenleri olarak yükler.

```javascript
require('dotenv').config();
```

_routes\index.js_ dosyasını açın ve içeriğini aşağıdaki kodla değiştirin.

Bu kod, hizmet sorumlusu parolanızı kullanarak bir Azure AD kimlik doğrulama belirteci alan bir API uç noktası oluşturur. Alt etki alanı da alır. Sonra belirteci ve alt etki alanını içeren bir nesne döndürür.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

**Getimmersivereaderlaunchparams** API uç noktası, yetkisiz kullanıcıların, bir dizi kimlik doğrulaması (örneğin, [OAuth](https://oauth.net/2/)) arkasında güvenli hale gelmelidir Bu iş, Bu öğreticinin kapsamı dışındadır.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Örnek içerikle modern okuyucu başlatma

1. _Views\layout.exe_' nı açın ve etiketinden önce etiketin altına aşağıdaki kodu ekleyin `head` `body` . Bu `script` Etiketler, [tam ekran okuyucu SDK 'Sını](https://github.com/microsoft/immersive-reader-sdk) ve jQuery 'yi yükler.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. _Views\ındex.Pug_ dosyasını açın ve içeriğini aşağıdaki kodla değiştirin. Bu kod, sayfayı bazı örnek içerikle doldurur ve tam ekran okuyucuyu Başlatan bir düğme ekler.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Web Uygulamam artık hazır. Uygulamayı çalıştırarak başlatın:

    ```bash
    npm start
    ```

4. Tarayıcınızı açın ve adresine gidin _http://localhost:3000_ . Sayfada Yukarıdaki içeriği görmeniz gerekir. İçeriğinizdeki modern okuyucuyu başlatmak için **tam ekran okuyucu** düğmesine tıklayın.

## <a name="specify-the-language-of-your-content"></a>İçeriğinizin dilini belirtin

Tam ekran okuyucu birçok farklı dil için destek içerir. Aşağıdaki adımları izleyerek, içeriğinizin dilini belirtebilirsiniz.

1. _Views\ındex.Pug_ ' i açın ve `p(id=content)` önceki adımda eklediğiniz etiketin altına aşağıdaki kodu ekleyin. Bu kod, sayfanıza bazı içerik Ispanyolca içerikleri ekler.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. JavaScript kodunda, çağrısının üzerine aşağıdakini ekleyin `ImmersiveReader.launchAsync` . Bu kod, Ispanyolca içeriğini tam ekran okuyucusuna geçirir.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Tekrar gidin _http://localhost:3000_ . Sayfada Ispanyolca metin görmeniz gerekir ve **tam ekran okuyucu**' ya tıkladığınızda, tam ekran okuyucu 'da da görünür.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Tam ekran okuyucu arabiriminin dilini belirtin

Varsayılan olarak, tam ekran okuyucu arabiriminin dili tarayıcının dil ayarlarıyla eşleşir. Ayrıca, aşağıdaki kodla tam ekran okuyucu arabirimi dilini de belirtebilirsiniz.

1. _Views\ındex.Pug_ içinde, çağrısını `ImmersiveReader.launchAsync(token, subdomain, content)` aşağıdaki kodla değiştirin.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Öğesine gidin _http://localhost:3000_ . Modern okuyucuyu başlattığınızda, arabirim Fransızca olarak gösterilir.

## <a name="launch-the-immersive-reader-with-math-content"></a>Matematik içerikli modern okuyucuyu başlatın

[MathML](https://developer.mozilla.org/en-US/docs/Web/MathML)'yi kullanarak, matematik Içeriğini tam ekran okuyucusuna dahil edebilirsiniz.

1. Aşağıdaki kodu çağrısının üzerine eklemek için _views\ındex.Pug_ öğesini değiştirin `ImmersiveReader.launchAsync` :

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Öğesine gidin _http://localhost:3000_ . Tam ekran okuyucuyu başlatıp en alta kaydırdığınızda matematik formülünü görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](../../reference.md) keşfet
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) 'daki kod örneklerini görüntüle
