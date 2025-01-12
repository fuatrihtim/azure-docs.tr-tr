---
title: Azure Active Directory uygulamalar için OıDC tabanlı çoklu oturum açma (SSO) anlayın
description: Azure Active Directory uygulamalar için OıDC tabanlı çoklu oturum açma (SSO) anlayın.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255258"
---
# <a name="understand-oidc-based-single-sign-on"></a>OıDC tabanlı çoklu oturum açmayı anlama
Uygulama yönetiminde [hızlı başlangıç serisinde](view-applications-portal.md) , Azure AD 'yi bir uygulama Için kimlik sağlayıcısı (IDP) olarak kullanmayı öğrendiniz. Bu makale, OpenID Connect standardını kullanan uygulamalar hakkında çoklu oturum açma uygulamak için daha fazla ayrıntıya gider. 

## <a name="before-you-begin"></a>Başlamadan önce
Azure Active Directory kiracınıza uygulama ekleme işlemi, uygulamanın uygulandığı çoklu oturum açma türüne bağlıdır. Kimlik yönetimi için Azure AD kullanan uygulamalar için kullanılabilen çoklu oturum açma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Çoklu oturum açma seçenekleri](sso-options.md). Bu makalede, OıDC tabanlı uygulamalar ele alınmaktadır.


## <a name="basic-oidc-configuration"></a>Temel OıDC yapılandırması
[Hızlı başlangıç serisinde](add-application-portal-setup-oidc-sso.md), çoklu oturum açmayı yapılandırma hakkında bir makale vardır. Burada, Azure kiracınıza OıDC tabanlı bir uygulama eklemeyi öğreneceksiniz.

Çoklu oturum açma için OıDC standardını kullanan bir uygulama eklemenin en iyi şeyi, yapılandırmanın en düşük düzeyde olmasıdır. Kiracınıza OıDC tabanlı bir uygulamanın nasıl ekleneceğini gösteren kısa bir video aşağıda verilmiştir.

Azure Active Directory bir OıDC tabanlı uygulama ekleme

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Kullanıcı ve yönetici onayı hakkında daha fazla bilgi edinmek için bkz. [Kullanıcı ve yönetici onayını anlama](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama yönetiminde hızlı başlangıç serisi](add-application-portal-setup-oidc-sso.md)
- [Çoklu oturum açma seçenekleri](sso-options.md)
- [Nasıl yapılır: Çok kiracılı uygulama desenini kullanarak istediğiniz bir Azure Active Directory kullanıcısıyla oturum açma](../develop/howto-convert-app-to-be-multi-tenant.md)
