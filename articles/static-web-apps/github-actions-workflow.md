---
title: Azure statik Web Apps için GitHub eylemleri iş akışları
description: Azure statik Web Apps sürekli dağıtımı ayarlamak için GitHub depolarını nasıl kullanacağınızı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: cshoe
ms.openlocfilehash: d561c72bb1c6e6e67de7698d7b21901fcf8d1f7c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544426"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemesi için GitHub eylemleri iş akışları

Yeni bir Azure statik Web uygulaması kaynağı oluşturduğunuzda Azure, uygulamanın sürekli dağıtımını denetlemek için bir GitHub eylemleri iş akışı oluşturur. İş akışı bir YAML dosyası tarafından yönlendiriliyor. Bu makalede, iş akışı dosyasının yapısı ve seçenekleri ayrıntılı olarak açıklanır.

Dağıtımlar, tek tek [adımlarla](#steps)tanımlanan [işleri](#jobs) çalıştıran [Tetikleyiciler](#triggers)tarafından başlatılır.

## <a name="file-location"></a>Dosya konumu

GitHub deponuzu Azure statik Web Apps bağladığınızda depoya bir iş akışı dosyası eklenir.

Oluşturulan iş akışı dosyasını görüntülemek için bu adımları izleyin.

1. Uygulamanın deposunu GitHub üzerinde açın.
1. _Kod_ sekmesinde, `.github/workflows` klasöre tıklayın.
1. Dosyaya benzeyen bir ada tıklayın `azure-static-web-apps-<RANDOM_NAME>.yml` .

Deponuzdaki YAML dosyası aşağıdaki örneğe benzer olacaktır:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        output_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Tetikleyiciler

GitHub eylemleri [tetikleyicisi](https://help.github.com/actions/reference/events-that-trigger-workflows) , bir GitHub eylemleri iş akışının olay tetikleyicilerini temel alarak bir işi çalıştırmasını bildirir. Tetikleyiciler, `on` iş akışı dosyasındaki özelliği kullanılarak listelenir.

```yml
on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main
```

Özelliği ile ilişkili ayarlar sayesinde `on` , hangi dalların bir işi tetikleyeceğini tanımlayabilir ve farklı çekme isteği durumları için Tetikleyicileri tetiklenecek şekilde ayarlayabilirsiniz.

Bu örnekte, _ana_ dal değiştikçe bir iş akışı başlatılır. İş akışını başlatan değişiklikler, yürütmelerin ve çekme isteklerinin seçili dala göre açılmasını içerir.

## <a name="jobs"></a>İşler

Her olay tetikleyicisi bir olay işleyicisi gerektirir. [İşler](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) , bir olay tetiklendiğinde ne olacağını tanımlar.

Statik Web Apps iş akışı dosyasında, kullanılabilir iki iş vardır.

| Ad  | Açıklama |
|---------|---------|
|`build_and_deploy_job` | Yürütmeler gönderdiğinizde veya özellikte listelenen dala karşı bir çekme isteği açtığınızda yürütülür `on` . |
|`close_pull_request_job` | , Çekme isteklerinden oluşturulan hazırlama ortamını kaldıran yalnızca bir çekme isteğini kapattığınızda yürütülür. |

## <a name="steps"></a>Adımlar

Adımlar, bir iş için sıralı görevlerdir. Bir adım bağımlılıklar yükleme, testleri çalıştırma ve uygulamanızı üretime dağıtma gibi eylemleri gerçekleştirir.

Bir iş akışı dosyası aşağıdaki adımları tanımlar.

| İş  | Adımlar  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Işlemin ortamındaki depoyu kontrol eder.<li>Depoyu oluşturup Azure statik Web Apps dağıtır.</ol>|
| `close_pull_request_job` | <ol><li>Azure statik Web Apps bir çekme isteğinin kapatıldığını bildirir.</ol>|

## <a name="build-and-deploy"></a>Oluşturma ve dağıtma

Adlı adım, `Build and Deploy` Azure statik Web Apps örneğinize yapılar ve dağıtır. `with`Bölümünde, dağıtımınız için aşağıdaki değerleri özelleştirebilirsiniz.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    output_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

`repo_token`, `action` , Ve `azure_static_web_apps_api_token` değerleri Azure static tarafından sizin için ayarlanır Web Apps el ile değiştirilmemelidir.

## <a name="custom-build-commands"></a>Özel derleme komutları

Dağıtım sırasında hangi komutların çalıştığı hakkında ayrıntılı denetim sahibi olabilirsiniz. Aşağıdaki komutlar bir iş bölümünün altında tanımlanabilir `with` .

Dağıtım her zaman `npm install` özel bir komuttan önce çağırır.

| Komut            | Açıklama |
|---------------------|-------------|
| `app_build_command` | Statik içerik uygulamasının dağıtımı sırasında çalışacak özel bir komut tanımlar.<br><br>Örneğin, angular uygulamasına yönelik bir üretim yapısını yapılandırmak için, çalıştırmak üzere adlı bir NPM betiği oluşturun `build-prod` `ng build --prod` ve `npm run build-prod` özel komut olarak girin. Boş bırakılırsa, iş akışı `npm run build` veya komutlarını çalıştırmayı dener `npm run build:azure` .  |
| `api_build_command` | Azure Işlevleri API uygulaması dağıtımı sırasında çalışacak özel bir komut tanımlar. |

## <a name="route-file-location"></a>Yol dosyası konumu

Deponuzdaki herhangi bir klasörde [routes.js](routes.md) aramak için iş akışını özelleştirebilirsiniz. Aşağıdaki özellik bir iş bölümünün altında tanımlanabilir `with` .

| Özellik            | Açıklama |
|---------------------|-------------|
| `routes_location` | Dosyadaki _routes.js_ bulunduğu dizin konumunu tanımlar. Bu konum, deponun köküne göredir. |

 _routes.jsdosya üzerinde_ açık olması özellikle, ön uç Framework derleme adımınız bu dosyayı varsayılan olarak ' a taşımadığından önemlidir `output_location` .

## <a name="environment-variables"></a>Ortam değişkenleri

Yapı için ortam değişkenlerini `env` bir iş yapılandırması bölümü aracılığıyla ayarlayabilirsiniz.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: "upload"
          ###### Repository/Build Configurations
          app_location: "/"
          api_location: "api"
          output_location: "public"
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo desteği

Tek depo, birden fazla uygulama için kod içeren bir depodur. Varsayılan olarak, statik bir Web Apps iş akışı dosyası bir depodaki tüm dosyaları izler, ancak bunu tek bir uygulamayı hedefleyecek şekilde ayarlayabilirsiniz. Bu nedenle, tek depolarda her bir statik uygulama, deponun *. GitHub/iş akışları* klasöründe yan yana bulunan kendi yapılandırma dosyasına sahiptir.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Bir iş akışı dosyasını tek bir uygulamaya hedeflemek için `push` ve bölümlerinde yollar belirtirsiniz `pull_request` .

Aşağıdaki örnek, `paths` `push` `pull_request` _Azure-static-Web-Apps-Purple-Pond. yıml_ adlı bir dosyanın ve bölümlerine nasıl bir düğüm ekleneceğini gösterir.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

Bu örnekte, yalnızca aşağıdaki dosyalarda yapılan değişiklikler yeni bir derlemeyi tetikler:

- *APP1* klasörünün içindeki dosyalar
- *Api1* klasörünün içindeki dosyalar
- Uygulamanın *Azure-static-Web-Apps-Purple-Pond. yıml* iş akışı dosyasında yapılan değişiklikler

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Üretim öncesi ortamlarında çekme isteklerini inceleme](review-publish-pull-requests.md)
