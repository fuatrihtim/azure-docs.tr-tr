---
title: Terminoloji-özel çevirici
titleSuffix: Azure Cognitive Services
description: Özel çevirici makalelerinde kullanılan koşulların listesi.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657833"
---
# <a name="custom-translator-terminology"></a>Özel çevirmen terminolojisi

Aşağıdaki tabloda, [özel çevirmenle](https://portal.customtranslator.azure.ai)çalışırken bulabileceğiniz terimlerin bir listesi sunulmaktadır.

| Sözcük veya tümcecik|Tanım|
|------------------|-----------|
| Kaynak dili | Kaynak dili, başka bir dile ("hedef") dönüştürmek istediğiniz başlangıç dilidir.|
| Hedef dil| Hedef dil, makine çevirisi 'nin kaynak dili aldıktan sonra sağlamasını istediğiniz dildir. |
| Monolingual dosyası | Bir monolingual dosyası farklı dildeki başka bir dosyayla eşleştirildiği tek bir dile sahiptir. |
| Paralel dosyalar | Paralel dosya, ilgili metin ile iki dosyanın birleşimidir. Bir dosyanın kaynak dili vardır. Diğeri hedef dile sahiptir.|
| Tümce hizalaması| Paralel veri kümesi, her iki dilde de aynı metni temsil eden cümleler için hizalanmış cümleler içermelidir. Örneğin, bir kaynak paralel dosyasında ilk cümle, teorik olarak, hedef paralel dosyadaki ilk cümleden eşlenir.|
| Hizalanmış metin | Dosya doğrulamasından en önemli adımlardan biri, tümceleri paralel belgelerde hizalamadır. Farklı dillerdeki şeyler farklı ifade edilir. Ayrıca farklı dillerin farklı sözcük düzenleri vardır. Bu adım, bir eğitim için kullanılabilmesi için cümleleri aynı içerikle hizalama işi yapar. Düşük cümle hizalaması, dosyaların bir veya her ikisinde de bir sorun olabileceğini gösterir. |
| Sözcük bölme/parçalama | Sözcük bölünmesi, kelimeler arasındaki sınırları işaretleyen işlevdir. Birçok yazma sistemi sözcükler arasındaki sınırı göstermek için bir boşluk kullanır. Sözcük sıkıştırması, önceki adımda yer alan sözcükler arasına eklenmiş olabilecek görünür işaretin kaldırılmasını ifade eder. |
| Icı   | Sınırlayıcılar, bir cümlenin kesimlere bölündüğü ya da tümceler arasındaki kenar boşluğunu sınırlandıran yollardır. Örneğin, Ingilizce alanlarda sözcük, iki nokta üst üste ve noktalı virgül işaretleri ve dönemler arasındaki noktalar |
| Eğitim dosyaları | Bir eğitim dosyası, makine çevirisi sistemine bir dilden (kaynak) bir hedef dile (hedef) nasıl eşleme yapılacağını öğretmek için kullanılır. Daha fazla veri sağlarsanız, sistemin gerçekleştireceği daha iyi olur. |
| Dosyaları ayarlama | Bu dosyalar genellikle eğitim kümesinden (bir ayarlama kümesi seçmezseniz) rastgele türetilir. Cümleler oto seçilir ve sistemi ayarlamak ve düzgün çalıştığından emin olmak için kullanılır. Genel amaçlı bir çeviri modeli oluşturmak ve kendi ayarlama dosyalarınızı oluşturmak isterseniz, etki alanları arasında rastgele bir cümle kümesi olduklarından emin olun |
| Dosyaları test etme| Bu dosyalar genellikle, eğitim kümesinden rastgele seçilmiş olan türetilmiş dosyalardır (herhangi bir test kümesi seçmezseniz). Bu Tümcelerin amacı, çeviri modelinin doğruluğunu değerlendirmek için kullanılır. Bu cümleler, sistemin doğru şekilde çevrilip çevirdiğinden emin olmak istediğiniz için bir test kümesi oluşturmak ve bunu çevirmene yüklemek isteyebilirsiniz. Bunun yapılması, bu Tümcelerin sistem değerlendirmesinde (BLEU puanı üretimi) kullanılmasını güvence altına alacak.   |
| Birleşik giriş dosyası   | Kaynak ve çevrilmiş Tümcelerin aynı dosyada bulunduğu bir dosya türü. Desteklenen dosya biçimleri (. tmx,. XLIFF,. xlf,. yazıcı,. xlsx). |
| Arşiv dosyası | Diğer dosyaları içeren bir dosya. Desteklenen dosya biçimleri (zip, GZ, tgz).  |
| BLEU Puanı   | [Bleu](what-is-bleu-score.md) , çeviri modelinin "duyarlık" veya doğruluğunu değerlendirmek için sektör standardı yöntemidir. Diğer değerlendirme yöntemleri de mevcut olsa da, Microsoft Translator proje sahiplerine doğruluğu raporlamak için BLEU yöntemini kullanır.
