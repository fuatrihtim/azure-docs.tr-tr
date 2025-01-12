---
title: Microsoft Authenticator uygulaması nedir? -Azure Active Directory | Microsoft Docs
description: Ne olduğu, nasıl çalıştığı ve içeriğin bu bölümüne hangi bilgilerin dahil olduğu dahil olmak üzere Microsoft Authenticator uygulaması hakkında bilgi edinin.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90055240"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulaması nedir?

Microsoft Authenticator uygulaması, iki öğeli doğrulama kullanırsanız hesaplarınızda oturum açmanıza yardımcı olur. İki öğeli doğrulama, özellikle de hassas bilgileri görüntülerken hesaplarınıza güvenli bir şekilde erişmenize yardımcı olur. Parolaların unutulabileceği, çalındığı veya güvenliği aşılmış olabileceğinden, iki öğeli doğrulama, diğer kişilerin kesintiye uğraması için hesabınızı korumanıza yardımcı olan ek bir güvenlik adımıdır.

Microsoft Authenticator uygulamasını, aşağıdakiler dahil olmak üzere birden çok şekilde kullanabilirsiniz:

- Kullanıcı adınızla ve parolanızla oturum açtıktan sonra kimlik doğrulama istemine yanıtlayın.

- Kullanıcı adınızı, kimlik doğrulayıcı uygulamanızı ve mobil cihazınızı parmak izinizle, yüzizine veya PIN 'iniz ile kullanarak oturum açın.

- Kimlik doğrulayıcı uygulamalarını destekleyen diğer hesaplar için kod Oluşturucu olarak.

> [!Important]
> Microsoft Authenticator uygulama, iki öğeli doğrulama kullanan herhangi bir hesapla birlikte çalışarak, zaman tabanlı bir kerelik parola (TOTP) standartlarını destekler.
>
>Bu makale, Microsoft Authenticator uygulamasını bir güvenlik doğrulama yöntemi olarak indirmeye ve kullanmaya çalışan kullanıcılara yöneliktir. Çalışanlarınız ve diğer kullanımlar için kimlik doğrulayıcı uygulamasını kullanarak parolasız oturum açma 'yı nasıl etkinleştireceğinizi öğrenmek için bir yöneticiyseniz, [Microsoft Authenticator App (Önizleme) ile parolasız oturum açmayı etkinleştirme](../authentication/howto-authentication-passwordless-phone.md)makalesine bakın.

## <a name="terminology"></a>Terminoloji

| Süre|Açıklama|
| ----|-----------|
| İki öğeli doğrulama | Bir parola ve PIN gibi özellikle yalnızca iki doğrulama bilgisi parçasını kullanmanızı gerektiren bir doğrulama işlemi. Microsoft Authenticator uygulaması hem standart iki öğeli doğrulamayı hem de passwordless oturum açmayı destekler. |
| Çok faktörlü kimlik doğrulaması (MFA) | İki öğeli kimlik doğrulama işlemi, kuruluşunuzun gereksinimlerine bağlı olarak, *en az* iki adet doğrulama bilgileri kullanmanızı gerektiren Multi-Factor Authentication ' dır. |
| Microsoft hesabı (da denir, MSA) | Müşteri odaklı Microsoft ürünlerine ve Outlook, OneDrive, Xbox LIVE veya Microsoft 365 gibi bulut hizmetlerine erişim sağlamak için kendi kişisel hesaplarınızı oluşturursunuz. Microsoft hesabı, Microsoft tarafından çalıştırılan Microsoft tüketici kimliği hesap sisteminde oluşturulur ve depolanır. |
| İş veya okul hesabı | Kuruluşunuz, alain@contoso.com Microsoft Azure, Windows Intune ve Microsoft 365 gibi iç ve potansiyel olarak kısıtlanmış kaynaklara erişmenizi sağlamak için iş veya okul hesabınızı (gibi) oluşturur. |
| Doğrulama kodu | Her eklenen hesap altında kimlik doğrulayıcı uygulamasında görünen altı basamaklı kod. Doğrulama kodu, birinin bir kodu birden çok kez kullanmasını önlemek için 30 saniyede bir değişir. Bu, tek seferlik geçiş kodu (OTP) olarak da bilinir. |

## <a name="how-two-factor-verification-works-with-the-app"></a>İki öğeli doğrulamanın uygulamayla nasıl çalıştığı

İki öğeli doğrulama Microsoft Authenticator uygulamayla aşağıdaki yollarla çalışmaktadır:

- **Uyarıyı.** İş veya okul hesabınız ya da kişisel Microsoft hesabı için oturum açmak istediğiniz cihaza Kullanıcı adınızı ve parolanızı yazın ve ardından Microsoft Authenticator uygulama, **oturum açmayı onaylamanızı** isteyen bir bildirim gönderir. Oturum açma girişimini tanıdıysanız **Onayla** ' yı seçin. Aksi takdirde **Reddet**' i seçin. **Reddet**' i seçerseniz, isteği sahte olarak da işaretleyebilirsiniz.

- **Doğrulama kodu.** İş veya okul hesabınız ya da kişisel Microsoft hesabı için oturum açmak istediğiniz cihaza Kullanıcı adınızı ve parolanızı yazın ve ardından Microsoft Authenticator uygulamasının **hesaplar** ekranından ilişkili doğrulama kodunu kopyalayın. Doğrulama kodu, tek seferlik geçiş kodu (OTP) kimlik doğrulaması olarak da bilinir.

- **Parolasız oturum açma.** İş veya okul hesabınız ya da kişisel Microsoft hesabı için oturum açarken kullandığınız cihaza Kullanıcı adınızı yazın ve ardından parmak izinizi, yüzünüzü veya PIN 'inizi kullanarak bunu doğrulamak için mobil cihazınızı kullanın. Bu yöntem için parolanızı girmeniz gerekmez.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Cihazınızın biyometrik yeteneklerini kullanıp kullanmayacağınızı belirtir

Kimlik doğrulama işlemini gerçekleştirmek için bir PIN kullanırsanız, Microsoft Authenticator uygulamasını bunun yerine cihazınızın parmak izi veya yüz tanıma (biyometrik) yeteneklerini kullanmak için ayarlayabilirsiniz. Bunu, bilgisayarınızı doğrulamak için kimlik doğrulayıcı uygulamasını ilk kez kullandığınızda, PIN 'iniz yerine cihaz Biyometri yeteneklerini kimlik olarak kullanma seçeneğini belirleyerek ayarlayabilirsiniz.

## <a name="who-decides-if-you-use-this-feature"></a>Bu özelliği kullanıp kullanmayacağına karar veriyor musunuz?

Kuruluşunuz, hesap türüne bağlı olarak iki öğeli doğrulama kullanmanız gerektiğine karar verebilir veya kendiniz karar veremeyebilirsiniz.

- **İş veya okul hesabı.** Bir iş veya okul hesabı kullanıyorsanız (örneğin, alain@contoso.com ), bu, belirli doğrulama yöntemleriyle birlikte iki öğeli doğrulama kullanmanız gerekip gerekmediğini kuruluşunuza göre yapılır. Microsoft Authenticator uygulamasına iş veya okul hesabınızı ekleme hakkında daha fazla bilgi için bkz. [iş veya okul hesaplarınızı ekleme](user-help-auth-app-add-work-school-account.md).

- **Kişisel Microsoft hesabı.** Kişisel Microsoft hesaplarınız için iki öğeli doğrulama ayarlamayı seçebilirsiniz (örneğin, alain@outlook.com ). Kişisel Microsoft hesabı ekleme hakkında daha fazla bilgi için bkz. [Kişisel hesaplarınızı ekleme](user-help-auth-app-add-personal-ms-account.md).

- **Microsoft hesabı olmayan.** Microsoft dışı hesaplarınız için iki öğeli doğrulama ayarlamayı tercih edebilirsiniz (örneğin, alain@gmail.com ). Microsoft dışı hesaplarınız, iki öğeli doğrulama terimini kullanamaz, ancak **güvenlik** veya **oturum açma** ayarları içinde özelliği bulabilmelisiniz. Microsoft Authenticator uygulama, TOTP standartlarını destekleyen hesaplarla birlikte çalışmaktadır. Microsoft dışı hesaplarınız ekleme hakkında daha fazla bilgi için bkz. [Microsoft dışı hesaplarınız ekleme](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Bu bölümde

| Makale | Açıklama |
| ------ | ------------ |
| [Uygulamayı indirme ve yükleme](user-help-auth-app-download-install.md) | Android ve iOS çalıştıran cihazlar için Microsoft Authenticator uygulamasının nereden ve nasıl alınacağını ve yükleneceğini açıklar. |
| [İş veya okul hesaplarınızı ekleyin](user-help-auth-app-add-work-school-account.md) | Çeşitli iş veya okul ve kişisel hesaplarınızın Microsoft Authenticator uygulamasına nasıl ekleneceğini açıklar. |
| [Kişisel hesaplarınızı ekleyin](user-help-auth-app-add-personal-ms-account.md) | Kişisel Microsoft hesaplarınızın Microsoft Authenticator uygulamasına nasıl ekleneceğini açıklar. |
| [Microsoft dışı hesaplarınızı ekleyin](user-help-auth-app-add-non-ms-account.md) | Microsoft dışı hesaplarınızın Microsoft Authenticator uygulamasına nasıl ekleneceğini açıklar. |
| [Hesaplarınızı el ile ekleyin](user-help-auth-app-add-account-manual.md) | Belirtilen QR kodunu tarayamıyorsanız, hesaplarınızın Microsoft Authenticator uygulamasına el ile nasıl ekleneceğini açıklar. |
| [Uygulamayı kullanarak oturum açın](user-help-auth-app-sign-in.md) | Microsoft Authenticator uygulamasını kullanarak çeşitli hesaplarınızda nasıl oturum açılacağını açıklar.|
| [Hesap kimlik bilgilerini yedekleme ve kurtarma](user-help-auth-app-backup-recovery.md) | Microsoft Authenticator uygulamasını kullanarak hesap kimlik bilgilerinizi yedekleme ve kurtarma hakkında bilgi sağlar. |
| [Microsoft Authenticator uygulaması hakkında SSS](user-help-auth-app-faq.md) | Uygulamayla ilgili sık sorulan soruların yanıtlarını sunar. |