---
title: Azure Veri Kataloğu sorunlarını giderme
description: Bu makalede, Azure Veri Kataloğu kaynaklarıyla ilgili genel sorun giderme sorunları açıklanmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674997"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure Veri Kataloğu sorunlarını giderme

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Bu makalede, Azure Veri Kataloğu kaynaklarıyla ilgili genel sorun giderme sorunları açıklanmaktadır. 

## <a name="functionality-limitations"></a>İşlev sınırlamaları

Azure Veri Kataloğu kullanırken aşağıdaki işlev sınırlıdır:

- **Konuk rolü** türündeki hesaplar desteklenmez. Konuk hesaplarını Azure Veri Kataloğu 'nun kullanıcıları olarak ekleyemez ve Konuk kullanıcılar bu portalı üzerinde kullanamaz [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- Azure Resource Manager şablonlarını veya Azure PowerShell komutlarını kullanarak Azure Veri Kataloğu kaynakları oluşturma desteklenmez.

- Azure Veri Kataloğu kaynağı Azure kiracılar arasında taşınamaz.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory ilke yapılandırması

Azure Veri Kataloğu portalında oturum açabildiğiniz, ancak veri kaynağı kayıt aracında oturum açmak istediğinizde oturum açmanızı engelleyen bir hata iletisiyle karşılaştığınız bir durum yaşayabilirsiniz. Bu hata, şirket ağı veya şirket ağının dışından bağlantı kurulurken gerçekleşebilir.

Kayıt aracı, kullanıcının Azure Active Directory ile oturum açtığını doğrulamak için *form kimlik doğrulaması* kullanır. Başarılı bir oturum açma için Azure Active Directory yöneticisinin *genel kimlik doğrulama ilkesinde* form kimlik doğrulamasını etkinleştirmesi gerekir.

Genel kimlik doğrulama ilkesi ile aşağıdaki görüntüde gösterildiği gibi kimlik doğrulamasını intranet ve extranet bağlantıları için ayrı ayrı etkinleştirebilirsiniz. Bağlanmakta olduğunuz ağ için form kimlik doğrulaması etkinleştirilmemişse, oturum açma hataları oluşabilir.

 ![Azure Active Directory genel kimlik doğrulama ilkesi](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Daha fazla bilgi için bkz. [Kimlik doğrulama ilkelerini yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Kataloğu oluşturma](data-catalog-get-started.md)
