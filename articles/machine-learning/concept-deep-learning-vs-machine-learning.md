---
title: Derin öğrenme ve makine öğrenimi karşılaştırması
titleSuffix: Azure Machine Learning
description: Derin öğrenimi 'nin Machine Learning ve AI ile ilişkisini öğrenin. Azure Machine Learning, sahtekarlık algılama, nesne algılama ve daha fazlası için derin öğrenme modellerini kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 01/14/2020
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: 48de06d28442b4d05cd3a7ab287732c0999e434c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101659707"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Derin öğrenme ile Machine Learning Azure Machine Learning

Bu makalede, derin öğrenme ve makine öğrenimi ve yapay zeka 'nın daha geniş kategorisine nasıl uyduğunu açıklanmaktadır. Sahtekarlık algılama, ses ve yüz tanıma, yaklaşım Analizi ve zaman serisi tahmin gibi Azure Machine Learning oluşturabileceğiniz derin öğrenme çözümleri hakkında bilgi edinin.

Çözümleriniz için algoritmalar seçme konusunda rehberlik için, [Machine Learning algoritması](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)bir başvuru sayfasına bakın.

## <a name="deep-learning-machine-learning-and-ai"></a>Derin öğrenme, makine öğrenimi ve AI

![İlişki diyagramı: AI ve makine öğrenimi ile derin öğrenme karşılaştırması](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Derin öğrenme ve makine öğrenimi vs. AI ' i anlamak için aşağıdaki tanımları göz önünde bulundurun:

- **Derin öğrenme** , yapay sinir ağlarını temel alan makine öğrenmesinin bir alt kümesidir. Yapay sinir ağların yapısı birden çok giriş, çıkış ve gizli katmanlardan oluşuyorsa _öğrenme süreci_ _çok önemlidir._ Her katman, giriş verilerini bir sonraki katmanın belirli bir tahmin görevi için kullanabileceği bilgilere dönüştüren birimler içerir. Bu yapı sayesinde bir makine, kendi veri işleme aracılığıyla bilgi alabilir.

- **Makine öğrenimi** , makinelerin görevleri geliştirmek için deneyim kullanmasını sağlayan tekniklerin (derin öğrenme gibi) kullanıldığı yapay zekanın bir alt kümesidir. _Öğrenme süreci_ aşağıdaki adımlara dayalıdır:

   1. Verileri bir algoritmaya akışın. (Bu adımda, örneğin özellik ayıklama işlemini gerçekleştirerek modele ek bilgi sağlayabilirsiniz.)
   1. Bir modeli eğitmek için bu verileri kullanın.
   1. Modeli test edin ve dağıtın.
   1. Otomatik bir tahmine dayalı görev yapmak için dağıtılan modeli kullanın. (Başka bir deyişle, model tarafından döndürülen tahminleri almak için dağıtılan modeli çağırın ve kullanın.)

- **Yapay zeka (AI)** , bilgisayarların insan zekası taklit etmesini sağlayan bir tekniktir. Machine Learning 'i de içerir. 
 
Makine öğrenimi ve derin öğrenme tekniklerini kullanarak, genellikle insan zekası ile ilişkili görevleri gerçekleştiren bilgisayar sistemleri ve uygulamalar oluşturabilirsiniz. Bu görevler görüntü tanıma, konuşma tanıma ve dil çevirisini içerir.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Derin öğrenme ve makine öğrenimi teknikleri 

Machine Learning 'e genel bakış ve derin öğrenime sahip olduğunuza göre, iki tekniği karşılaştıralım. Machine Learning 'de, algoritmanın daha fazla bilgi (örneğin, özellik ayıklama gerçekleştirerek) ile doğru bir tahmin yapma yöntemi olması gerekir. Derin öğreniminde, algoritma yapay sinir ağ yapısı sayesinde kendi veri işleme aracılığıyla doğru bir tahmin yapmayı öğreniyor.

Aşağıdaki tabloda, daha ayrıntılı olarak iki teknik karşılaştırılmaktadır:

| |Tüm makine öğrenimi |Yalnızca derin öğrenme|
|---|---|---|
|  **Veri noktası sayısı** | , Tahminlerde küçük miktarlarda veri kullanabilir. | Tahmine dayalı hale getirmek için büyük miktarlarda eğitim verisi kullanması gerekir. |
|  **Donanım bağımlılıkları** | , Düşük kaliteli makinelerde çalışabilir. Büyük miktarda hesaplama gücüne gerek yoktur. | , Yüksek kaliteli makinelere bağlıdır. Bu, doğal olarak çok sayıda matris çarpma işlemi yapar. GPU, bu işlemleri verimli bir şekilde iyileştirebilir. |
|  **Korturlama işlemi** | Özelliklerin kullanıcılar tarafından doğru şekilde tanımlanması ve oluşturulması gerekir. | Verilerden yüksek düzey özellikleri öğrenir ve kendi kendine yeni özellikler oluşturuyor. |
|  **Öğrenme yaklaşımı** | Öğrenme işlemini daha küçük adımlara böler. Ardından, her adımın sonuçlarını tek bir çıktıda birleştirir. | Sorunu, bir uçtan uca temelinde çözerek öğrenme süreci boyunca ilerleyerek. |
|  **Yürütme süresi** | Birkaç saniye ile birkaç saat arasında uyum sağlamak için karşılaştırmayı daha az zaman alır. | Derin bir öğrenme algoritması birçok katman içerdiğinden, genellikle eğitmek uzun sürer. |
|  **Çıktı** | Çıktı genellikle bir puan veya sınıflandırma gibi sayısal bir değerdir. | Çıktıda metin, puan veya ses gibi birden çok biçim olabilir. |

## <a name="what-is-transfer-learning"></a>Aktarım öğrenimi nedir

Derinlemesine öğrenme modellerini eğitmek için genellikle büyük miktarda eğitim verisi, yüksek kaliteli işlem kaynakları (GPU, TPU) ve daha uzun bir eğitim süresi gerekir. Bunlardan herhangi birine sahip olmadığınız senaryolarda, eğitim sürecini *Aktarım öğrenimi* olarak bilinen bir teknik kullanarak kısayol ile deneyebilirsiniz.

Aktarım öğrenimi, bir problemi farklı ancak ilgili bir soruna çözmeyle elde edilen bilgileri uygulayan bir tekniktir.

Sinir Networks yapısı nedeniyle, ilk katman kümesi genellikle alt düzey özellikler içerir, ancak son katman kümesi söz konusu etki alanına yakın olan daha yüksek düzeyde bir özelliği içerir. Son katmanları yeni bir etki alanında veya bir sorun ile kullanmak üzere yeniden seçerek, yeni modeli eğmek için gereken süre, veri ve işlem kaynaklarının sayısını önemli ölçüde azaltabilirsiniz. Örneğin, otomobilleri tanıyan bir modeliniz zaten varsa, bu modeli aktarım öğrenimi 'ni kullanarak yeniden amaçlandırın, Ayrıca, aynı zamanda structuralks, otodöngüleri ve diğer araçlar türlerini de tanımak için kullanabilirsiniz.

Azure Machine Learning 'de açık kaynaklı bir çerçeve kullanarak görüntü sınıflandırması için aktarım öğrenimini nasıl uygulayacağınızı öğrenin: [Aktarım öğrenimi kullanarak derin bir öğrenme PyTorch modeli eğitme](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Derin öğrenme kullanım örnekleri

Yapay sinir ağ yapısı nedeniyle, derinlemesine öğrenme, görüntü, ses, video ve metin gibi yapılandırılmamış verilerde desenleri tanımlamaya. Bu nedenle, derin öğrenme, sağlık, enerji, finans ve taşıma gibi birçok sektörler hızla dönüştürülüyor. Bu endüstriler artık geleneksel iş süreçlerini yeniden düşünürken. 

Derin öğrenme için en yaygın uygulamalardan bazıları aşağıdaki paragraflarda açıklanmıştır. Azure Machine Learning ' de, bir açık kaynaklı çerçevede oluşturduğunuz bir modeli kullanabilir veya modeli, sunulan araçları kullanarak oluşturabilirsiniz.

### <a name="named-entity-recognition"></a>Adlandırılmış varlık tanıma

Adlandırılmış varlık tanıma, bir metin parçasını giriş olarak alan ve önceden belirtilen bir sınıfa dönüştüren derin bir öğrenme yöntemidir. Bu yeni bilgiler bir posta kodu, tarih ve ürün KIMLIĞI olabilir. Daha sonra bilgiler bir kimlik listesi oluşturmak için yapılandırılmış bir şemada depolanabilir veya bir kimlik doğrulama altyapısı için kıyaslama işlevi görebilir.

### <a name="object-detection"></a>Nesne algılama

Derin öğrenme birçok nesne algılama kullanım durumunda uygulandı. Nesne algılama iki bölümden oluşur: görüntü sınıflandırması ve sonra görüntü yerelleştirme. Görüntü _sınıflandırması_ , görüntünün, otomobiller veya kişiler gibi nesnelerini tanımlar. Görüntü _Yerelleştirme_ bu nesnelerin belirli konumunu sağlar. 

Nesne algılama, oyun, perakende, tourma ve kendi kendine çalışan otomobiller gibi sektörlerde zaten kullanılıyor.

### <a name="image-caption-generation"></a>Resim yazısı oluşturma

Görüntü tanıma gibi, resim yazısı içinde, belirli bir görüntü için sistem, görüntünün içeriğini açıklayan bir resim yazısı oluşturması gerekir. Fotoğraflarındaki nesneleri tespit edebilir ve etiketleyebilir, sonraki adım bu etiketleri açıklayıcı cümlelere dönüştürmek olur. 

Genellikle, görüntü resim yazısı uygulamaları, bir görüntüdeki nesneleri tanımlamak için sinir ağlarını kullanır ve ardından etiketleri tutarlı cümlelere dönüştürmek için yinelenen bir sinir ağı kullanır.

### <a name="machine-translation"></a>Makine çevirisi

Makine çevirisi, bir dilden sözcükler veya cümleler alır ve bunları otomatik olarak başka bir dile çevirir. Makine çevirisi uzun bir süredir, ancak derin öğrenme iki belirli alana etkileyici sonuçlar elde eder: otomatik metin çevirisi (ve konuşmayı metne çevirme) ve görüntülerin otomatik çevirisi.

Uygun veri dönüşümünde, bir sinir ağı metin, ses ve görsel sinyalleri anlayabilirler. Makine çevirisi, daha büyük ses dosyalarındaki ses kod parçacıklarını tanımlamak ve söylenen sözcük veya görüntüyü metin olarak eklemek için kullanılabilir.

### <a name="text-analytics"></a>Metin analizi

Derin öğrenme yöntemlerine dayalı metin analizi, büyük miktarlarda metin verisini (örneğin, tıbbi belgeler veya gider alındıları) analiz etmeyi, desenleri tanımayı ve daha sonra düzenlenmiş ve kısa bilgiler oluşturmayı içerir.

Şirketler, kamu yönetmelikleriyle Insider ticareti ve uyumluluğu algılamak üzere metin analizi gerçekleştirmek için derin öğrenme kullanır. Daha yaygın bir örnek sigorta dolandırıcılığı örneğidir: metin analizi, genellikle bir sigorta talebinin sahtekarlık olasılığını tanımak üzere büyük miktarda belgeyi çözümlemek için kullanılır. 

## <a name="artificial-neural-networks"></a>Yapay sinir ağları

Yapay sinir ağları bağlı düğümlerin katmanlarına göre biçimlendirilir. Derin öğrenme modelleri, çok sayıda katmana sahip sinir ağlarını kullanır. 

Aşağıdaki bölümlerde en popüler yapay sinir Network typologies vardır.

### <a name="feedforward-neural-network"></a>Feedforward sinir ağı

Feedforward sinir Network, yapay sinir Network 'ün en basit türüdür. Bir feedforward ağında, bilgiler giriş katmanından çıkış katmanına yalnızca bir yönde gider. Feedforward sinir Networks bir girişi bir dizi gizli katmana yerleştirerek bir girişi dönüştürür. Her katman bir dizi değilden oluşur ve her katman, daha önce katmandaki tüm neks 'e tam olarak bağlanır. Son tam bağlı katman (çıkış katmanı) oluşturulan tahminleri temsil eder.

### <a name="recurrent-neural-network"></a>Yinelenen sinir ağı

Yinelenen sinir Networks, yaygın olarak kullanılan yapay bir sinir ağı. Bu ağlar bir katmanın çıkışını kaydeder ve katmanın sonucunun tahmin edilmesine yardımcı olması için giriş katmanına geri akışı sağlar. Yinelenen sinir ağlarında harika öğrenme becerileri vardır. Zaman serisi tahmin, öğrenme el yazısı ve dil tanıma gibi karmaşık görevler için yaygın olarak kullanılır.

### <a name="convolutional-neural-network"></a>Evsel sinir ağı

Bir evsel sinir ağı, özellikle etkili bir yapay sinir ağı ve benzersiz bir mimari sunar. Katmanlar üç boyutta düzenlenmiştir: genişlik, yükseklik ve derinlik. Bir katmandaki katlara, bir sonraki katmandaki tüm neks 'ler bağlanır, ancak yalnızca katmanın nekatlarındaki küçük bir bölgedir. Son çıktı, derinlik boyutunun tek bir vektörüne indirgenecek ve derinlik boyutu üzerinde düzenlenmiştir. 

Video tanıma, görüntü tanıma ve öneren sistemleri gibi alanlarda, evsel sinir ağları kullanılmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri)içindeki açık kaynaklı derin öğrenme modellerini kullanmaya yönelik daha fazla seçenek gösterilmektedir:


- [Bir TensorFlow modeli kullanarak el ile yazılmış rakamları sınıflandırın](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Bir TensorFlow tahmin aracı ve keras kullanarak el ile yazılmış rakamları sınıflandır](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Bir Chainer modeli kullanarak el ile yazılmış rakamları sınıflandır](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
