---
author: baanders
description: Azure dijital TWINS örnekleri içindeki DefaultAzureCredential için dosya ekleme-Note
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473631"
---
>[!NOTE]
> Bu örnek [](/dotnet/api/azure.identity.defaultazurecredential) `Azure.Identity` , yerel makinenizde çalıştırdığınızda Azure dijital TWINS örneğiyle kullanıcıların kimliğini doğrulamak için DefaultAzureCredential (kitaplığın parçası) kullanır. Bu tür bir kimlik doğrulamasıyla, örnek yerel bir [Azure CLI](/cli/azure/install-azure-cli) veya Visual Studio/Visual Studio Code oturum açma gibi yerel ortamınızda Azure kimlik bilgilerini arar.
>
> `DefaultAzureCredential`Ve diğer kimlik doğrulama seçenekleri hakkında daha fazla bilgi için bkz. [*nasıl yapılır: yazma uygulaması kimlik doğrulaması kodu*](../articles/digital-twins/how-to-authenticate-client.md).