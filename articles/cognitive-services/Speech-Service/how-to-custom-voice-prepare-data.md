---
title: Özel ses okuma hizmeti için verileri hazırlama
titleSuffix: Azure Cognitive Services
description: Konuşma hizmetiyle markanız için özel bir ses oluşturun. Stüdyo kayıtlarını ve ilişkili betikleri sağlarsınız, hizmet kayıtlı sesle ayarlanmış benzersiz bir ses modeli oluşturur. Ürün, araç ve uygulamalarınızda konuşmayı sentezleştirmek için bu sesi kullanın.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 22db0006e6abb38439a2db46584559dcd9ce3f3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722373"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Özel bir ses oluşturmak için verileri hazırlama

Uygulamanız için özel bir metin okuma sesi oluşturmaya hazırsanız, ilk adım ses kaydı ve ilişkili betikleri toplamaktır. böylece ses modeli eğitimi başlatılır. Konuşma hizmeti bu verileri, kayıtlarda bulunan sesle eşleşecek şekilde ayarlanmış benzersiz bir ses oluşturmak için kullanır. Sesi eğitdikten sonra, uygulamalarınızda konuşmayı senizme başlatabilirsiniz.

Kendi metinden konuşmaya ses modelinizi eğmeden önce, ses kayıtları ve ilişkili metin dökümlerinin olması gerekir. Bu sayfada, veri türlerini, bunların nasıl kullanıldığını ve bunların nasıl yönetileceğini inceleyeceğiz.

> [!NOTE]
> Bir sinir sesi eğmek isterseniz, özel bir ses modeli eğmek için kendi konuşma verilerini kullanma hakkında daha fazla ses onay dosyası içeren bir ses tatatsyon profili belirtmeniz gerekir. Kayıt betiğinizi hazırlarken aşağıdaki tümceyi eklediğinizden emin olun. 

> "I [soyadınız ve soyadınız], sesimin yapay bir sürümünü oluşturmak ve kullanmak için sesimin kayıtlarının [Şirket adı olarak durum] tarafından kullanılacağını biliyor."
Bu cümle, eğitim verilerinin onay yapan kişi tarafından gerçekleştirilip yapılkullanılmadığını doğrulamak için kullanılacaktır. [Sesli TATATK doğrulaması](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) hakkında daha fazla bilgi edinmek için buraya bakın.

> Özel sinir Voice sınırlı erişimle kullanılabilir. [Sorumlu AI gereksinimlerini](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) anladığınızdan emin olun ve [erişimi buraya uygulayın](https://aka.ms/customneural). 

## <a name="data-types"></a>Veri türleri

Sesli eğitim veri kümesi, ses kayıtları ve ilişkili dökümlere sahip bir metin dosyası içerir. Her ses dosyası tek bir söylenişi (tek bir cümle veya bir iletişim sistemi için tek bir adet) içermelidir ve 15 saniyeden az uzunluğunda olmalıdır.

Bazı durumlarda, doğru veri kümesi hazır olmayabilir ve uygun ses eğitimini, kısa veya uzun olan ve yazılı betikler olmadan, kısa veya uzun bir şekilde test etmek isteyeceksiniz. [Toplu Iş dökümü API](batch-transcription.md)'sini kullanarak sesinizi, betiğinin parçalara ayırmanıza ve bu betiklerin hazırlanmasına yardımcı olacak araçlar (Beta) sunuyoruz.

Bu tabloda, veri türleri ve bunların her birinin özel metin okuma ses modeli oluşturmak için nasıl kullanıldığı listelenmektedir.

| Veri türü | Açıklama | Kullanılması gereken durumlar | Ek işlem gerekiyor | 
| --------- | ----------- | ----------- | --------------------------- |
| **Bireysel söyleyle + eşleşen döküm** | Tek tek bildirimler olarak ses dosyalarının (. wav) bir koleksiyonu (. zip). Her ses dosyası, biçimlendirilen bir döküm dosyası (. txt) ile eşleştirilmiş 15 saniye veya daha az uzunlukta olmalıdır. | Eşleşen yazılı betiklerle profesyonel kayıtlar | Eğitim için hazırlayın. |
| **Uzun ses + döküm (Beta)** | Tüm konuşulan kelimeleri içeren bir döküm dosyası (. txt) ile eşleştirilmiş ve uzun, bölünmeden (20 saniyeden uzun) bir koleksiyon (. zip). | Ses dosyalarınız ve eşleşen yazılı betikleriniz var, ancak bunlar utterler halinde bölünmemelidir. | Segmentleme (toplu iş dökümü kullanılarak).<br>Gereken yerlerde ses biçimi dönüşümü. | 
| **Yalnızca ses (Beta)** | Döküm dosyası olmayan ses dosyalarının koleksiyonu (. zip). | Yazılı betikler olmadan yalnızca kullanılabilir ses dosyalarınız vardır. | Segment + döküm oluşturma (toplu iş dökümü kullanılarak).<br>Gereken yerlerde ses biçimi dönüşümü.| 

Dosyalar bir veri kümesine türlerine göre gruplanmalı ve zip dosyası olarak karşıya yüklenir. Her veri kümesi yalnızca tek bir veri türü içerebilir.

> [!NOTE]
> Abonelik başına içeri aktarılmaya izin verilen en fazla veri kümesi sayısı, ücretsiz abonelik (F0) kullanıcıları için 10 ZIP dosyası ve Standart abonelik (S0) kullanıcıları için 500 ' dir.

## <a name="individual-utterances--matching-transcript"></a>Bireysel söyleyle + eşleşen döküm

Tek tek dıklarınızın ve eşleşen döküm dosyalarının kayıtlarını iki şekilde hazırlayabilirsiniz. Bir komut dosyası yazın ve bir sesli taçın tarafından okunmasını ister İkincisini yaparsanız, "um" ve diğer dolgu sesleri, geri alma, geri alınan sözcükler veya yanlış söylenler gibi ses dosyalarından birliklerini düzenleyin.

İyi bir ses modeli oluşturmak için, yüksek kaliteli bir mikrofona sahip bir sessiz odada kayıtlar oluşturun. Tutarlı birim, konuşma oranı, konuşma sıklığı ve önemli öneme sahip bir konuşma

> [!TIP]
> Üretim kullanımı için bir ses oluşturmak üzere bir profesyonel Kayıt Studio ve ses taçı kullanmanızı öneririz. Daha fazla bilgi için bkz. [özel bir ses için ses örneklerini kaydetme](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ses dosyaları

Her ses dosyası, 15 saniyeden az uzunlukta tek bir söylenişi (tek bir cümle veya bir iletişim sisteminin tek bir kümesi) içermelidir. Tüm dosyalar aynı konuşulan dilde olmalıdır. Çok dilli özel metin okuma sesleri, Chinese-English bı-dilli dışında, desteklenmez. Her ses dosyası,. wav dosya adı uzantısına sahip benzersiz bir sayısal dosya adına sahip olmalıdır.

Ses hazırlanırken bu yönergeleri izleyin.

| Özellik | Değer |
| -------- | ----- |
| Dosya biçimi | Bir. zip dosyasında gruplandırılan RIFF (. wav) |
| Örnekleme oranı | En az 16.000 Hz |
| Örnek biçimi | PCM, 16 bit |
| Dosya adı | . Wav uzantısıyla sayısal. Yinelenen dosya adlarına izin verilmez. |
| Ses uzunluğu | 15 saniyeden kısa |
| Arşiv biçimi | .zip |
| Maksimum Arşiv boyutu | 2048 MB |

> [!NOTE]
> örnekleme hızına 16.000 Hz 'den düşük olan. wav dosyaları reddedilir. Bir. zip dosyası farklı örnek oranlarına sahip. wav dosyaları içeriyorsa, yalnızca 16.000 Hz 'den büyük veya buna eşit olanlar içeri aktarılır. Portal Şu anda. zip arşivlerini 200 MB 'a kadar aktarır. Ancak, birden çok arşiv karşıya yüklenebilir.

### <a name="transcripts"></a>Dökümleri

Döküm dosyası bir düz metin dosyasıdır. Bu yönergeleri kullanarak dökümlerinizi hazırlayın.

| Özellik | Değer |
| -------- | ----- |
| Dosya biçimi | Düz metin (. txt) |
| Kodlama biçimi | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE veya UTF-16-of. Zh-CN için ANSI/ASCII ve UTF-8 kodlamaları desteklenmez. |
| Satır başına konuşma sayısı | Döküm dosyasının her **bir** satırı, ses dosyalarından birinin adını ve ardından karşılık gelen dökümü içermelidir. Dosya adı ve transkripsiyon sekme (\t) ile ayrılmalıdır. |
| En büyük dosya boyutu | 2048 MB |

Aşağıda, döküm dosyalarının tek bir. txt dosyasında söylenişi tarafından nasıl düzenlendiği hakkında bir örnek verilmiştir:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Bu, döküm dosyalarının karşılık gelen sesin %100 doğru dökümünü gerektirdiğinden önemlidir. Dökümleri hataları eğitim sırasında kalite kaybı ortaya çıkaracak.

## <a name="long-audio--transcript-beta"></a>Uzun ses + döküm (Beta)

Bazı durumlarda, uygun bölümsel ses olmayabilir. Uzun ses dosyalarını segmentetmenize ve döküm oluşturmanıza yardımcı olması için özel ses portalı aracılığıyla bir hizmet (Beta) sunuyoruz. Göz önünde bulundurun. bu hizmet, konuşma-metin abonelik kullanımınıza doğru ücretlendirilecektir.

> [!NOTE]
> Uzun süreli segmentasyon hizmeti, yalnızca Standart abonelik (S0) kullanıcılarını destekleyen, konuşmadan metne ait toplu iş dökümü özelliğinden yararlanır. Segmentlerin işlenmesi sırasında, ses dosyalarınız ve yazılı betikler, tanınma modelini iyileştirmek için Özel Konuşma Tanıma hizmetine de gönderilir, böylece veriler için doğruluk artırılabilir. Bu işlem sırasında hiçbir veri korunmaz. Segmentleme gerçekleştirildikten sonra, karşıdan yükleme ve eğitim için yalnızca uttasyonları ve onların eşleme dökümü depolanır.

### <a name="audio-files"></a>Ses dosyaları

Bu yönergeleri izleyerek ses segmentlemeye hazırlanırken izleyin.

| Özellik | Değer |
| -------- | ----- |
| Dosya biçimi | Bir. zip dosyasında gruplandırılan, en az 256 KBps 'e sahip PCM veya. mp3 olarak en az 16 kHz-16 bit örnekleme hızına sahip RIFF (. wav) |
| Dosya adı | ASCII ve Unicode karakterleri desteklenir. Yinelenen adlara izin verilmez. |
| Ses uzunluğu | 20 saniyeden uzun |
| Arşiv biçimi | .zip |
| Maksimum Arşiv boyutu | 2048 MB |

Tüm ses dosyaları bir ZIP dosyasında gruplandırılmalıdır. . Wav dosyalarını ve. mp3 dosyalarını tek bir ses zip dosyasına yerleştirmek tamam. Örneğin, ' kingöykü. wav ', 45-ikinci-uzun ve ' queenstory.mp3 ', 200-ikinci-Long adlı bir ses dosyası içeren bir zip dosyasını karşıya yükleyebilirsiniz. Tüm. mp3 dosyaları işlendikten sonra. WAV biçimine dönüştürülür.

### <a name="transcripts"></a>Dökümleri

Transcripts bu tabloda listelenen belirtimlerle hazırlanmalıdır. Her ses dosyası bir dökümünü bir döküm ile eşleştirmelidir.

| Özellik | Değer |
| -------- | ----- |
| Dosya biçimi | Düz metin (. txt), bir. zip olarak gruplandırılır |
| Dosya adı | Eşleşen ses dosyası ile aynı adı kullan |
| Kodlama biçimi | UTF-8-yalnızca BOM |
| Satır başına konuşma sayısı | Sınır yok |
| En büyük dosya boyutu | 2048 MB |

Bu veri türündeki tüm döküm dosyaları bir ZIP dosyasında gruplandırılmalıdır. Örneğin, ' kingöykü. wav ' adlı bir ses dosyası, 45 saniye uzunluğunda ve ' queenstory.mp3 ', 200 saniye uzunluğunda başka bir dosya içeren bir zip dosyası yüklediniz. Biri ' kingstory.txt ', diğeri de ' queenstory.txt ' olan iki dökümü içeren başka bir ZIP dosyasını karşıya yüklemeniz gerekir. Her düz metin dosyasında, eşleşen ses için tam doğru dökümü sağlarsınız.

Veri kümeniz başarıyla karşıya yüklendikten sonra, sağlanan dökümü temel alarak ses dosyasını el ile segmentlere ayırmanıza yardımcı olacak. Veri kümesini indirerek, kesimli ve eşleşen dökümü kontrol edebilirsiniz. Benzersiz kimlikler otomatik olarak kesimli şekilde atanır. Sağladığınız dökümlerin %100 doğru olduğundan emin olmanız önemlidir. Döküm dosyalarındaki hatalar, ses kesimlemesi sırasında doğruluğu azaltabilir ve daha sonra eğitim aşamasında daha fazla kalite kaybı ortaya çıkarabilir.

## <a name="audio-only-beta"></a>Yalnızca ses (Beta)

Ses kayıtlarınız için döküm yoksa, verilerinizi karşıya yüklemek için **yalnızca ses** seçeneğini kullanın. Sistemimiz, ses dosyalarınızı segmentinize ve uygulamanıza yardımcı olabilir. Göz önünde bulundurun. bu hizmet, konuşmadan metne abonelik kullanımınıza doğru sayılır.

Ses hazırlanırken bu yönergeleri izleyin.

> [!NOTE]
> Uzun süreli segmentasyon hizmeti, yalnızca Standart abonelik (S0) kullanıcılarını destekleyen, konuşmadan metne ait toplu iş dökümü özelliğinden yararlanır.

| Özellik | Değer |
| -------- | ----- |
| Dosya biçimi | Bir. zip dosyasında gruplandırılan, en az 256 KBps 'e sahip PCM veya. mp3 olarak en az 16 kHz-16 bit örnekleme hızına sahip RIFF (. wav) |
| Dosya adı | ASCII ve Unicode karakterleri desteklenir. Yinelenen ada izin verilmez. |
| Ses uzunluğu | 20 saniyeden uzun |
| Arşiv biçimi | .zip |
| Maksimum Arşiv boyutu | 2048 MB |

Tüm ses dosyaları bir ZIP dosyasında gruplandırılmalıdır. Veri kümeniz başarıyla karşıya yüklendikten sonra, konuşma toplu iş dökümü hizmeti ' ne bağlı olarak ses dosyasını parçalara ayırmanıza yardımcı olacak. Benzersiz kimlikler otomatik olarak kesimli şekilde atanır. Eşleşen yazılı betikler, konuşma tanıma aracılığıyla oluşturulacaktır. Tüm. mp3 dosyaları işlendikten sonra. WAV biçimine dönüştürülür. Veri kümesini indirerek, kesimli ve eşleşen dökümü kontrol edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel bir ses oluşturun](how-to-custom-voice-create-voice.md)
- [Kılavuz: ses örneklerinizi kaydetme](record-custom-voice-samples.md)