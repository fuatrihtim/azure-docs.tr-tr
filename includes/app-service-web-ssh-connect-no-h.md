---
title: include dosyası
description: dosya dahil etme
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91822812"
---
Kapsayıcınızda doğrudan SSH oturumu başlatabilmek için uygulamanızın çalışıyor olması gerekir.

Aşağıdaki URL'yi tarayıcınıza yapıştırın ve `<app-name>` yerine kendi uygulamanızın adını yazın:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Kimlik doğrulamasından geçmediyseniz bağlantıyı kurabilmek için Azure aboneliğinizle kimliğinizi doğrulamanız gerekir. Kimliğiniz doğrulandıktan sonra kapsayıcınızda komut çalıştırmak için kullanabileceğiniz tarayıcı içi kabuk ortamını görürsünüz.

![SSH bağlantısı](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
