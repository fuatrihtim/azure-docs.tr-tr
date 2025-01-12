---
title: Linux 'u keşfet
titleSuffix: Azure Data Science Virtual Machine
description: Linux Veri Bilimi Sanal Makinesi kullanarak çeşitli yaygın veri bilimi görevlerini tamamlamayı öğrenin.
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 42136d0d58dbc318aab0e111fcef46f80751ca88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517680"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Azure 'da Ubuntu Veri Bilimi Sanal Makinesi veri bilimi

Bu izlenecek yol, Ubuntu Veri Bilimi Sanal Makinesi (DSVM) kullanarak birkaç ortak veri bilimi görevinin nasıl tamamlandığını gösterir. Ubuntu DSVM, Azure 'da bulunan ve veri analizi ve makine öğrenimi için yaygın olarak kullanılan bir araç koleksiyonuyla önceden yüklenmiş bir sanal makine görüntüsüdür. Temel yazılım bileşenleri [Ubuntu veri bilimi sanal makinesi sağlama](./dsvm-ubuntu-intro.md)bölümünde yer verilir. DSVM görüntüsü, her bir aracının tek tek yüklenip yapılandırılması gerekmeden veri bilimi 'ni dakikalar içinde kullanmaya başlamanızı kolaylaştırır. Gerekirse DSVM 'yi kolayca ölçeklendirebilirsiniz ve kullanımda olmadığında durdurabilirsiniz. DSVM kaynağı esnek ve uygun maliyetli bir yöntemdir.

Bu izlenecek yolda, [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) veri kümesini çözümliyoruz. Spambase, istenmeyen veya ham (istenmeyen posta değil) olarak işaretlenmiş bir e-posta kümesidir. Spambase, e-postaların içeriğiyle ilgili bazı istatistikler de içerir. Anlatımın ilerleyen kısımlarında yer aldığı istatistikleri konuşuyoruz.

## <a name="prerequisites"></a>Önkoşullar

Bir Linux DSVM kullanabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

* **Azure aboneliği**. Azure aboneliği almak için, bkz. [ücretsiz Azure hesabınızı hemen oluşturun](https://azure.microsoft.com/free/).

* [**Ubuntu veri bilimi sanal makinesi**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Sanal makineyi sağlama hakkında daha fazla bilgi için bkz. [Ubuntu veri bilimi sanal makinesi sağlama](./release-notes.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) açık bir XFCE oturumuyla bilgisayarınızda yüklü. Daha fazla bilgi için bkz. [X2Go Istemcisini yükleyip yapılandırma](dsvm-ubuntu-intro.md#x2go).
* Daha yumuşak bir kaydırma deneyimi için DSVM 'nin Firefox Web tarayıcısında `gfx.xrender.enabled` bayrağını değiştirin `about:config` . [Daha fazla bilgi edinin](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). ' İ de `mousewheel.enable_pixel_scrolling` olarak ayarlamayı düşünün `False` . [Daha fazla bilgi edinin](https://support.mozilla.org/questions/981140).

## <a name="download-the-spambase-dataset"></a>Spambase veri kümesini indirin

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) veri kümesi, 4.601 örnek içeren görece küçük bir veri kümesidir. Veri kümesi, DSVM 'nin bazı önemli özelliklerini gösteren, kaynak gereksinimlerini Modest olarak tutan uygun boyutlarıdır.

> [!NOTE]
> Bu izlenecek yol, D2 v2 boyutlu bir Linux DSVM (Ubuntu 18,04 Edition) kullanılarak oluşturulmuştur. Bu kılavuzda gösterilen yordamları tamamlamak için bu boyutu DSVM kullanabilirsiniz.

Daha fazla depolama alanı gerekiyorsa, ek diskler oluşturabilir ve bunları DSVM 'nize iliştirebilirsiniz. Diskler kalıcı Azure depolama kullanır, bu nedenle, yeniden boyutlandırma veya kapatma nedeniyle sunucu yeniden sağlansa bile, verileri korunur. Bir disk eklemek ve DSVM 'nize eklemek için bir [LINUX sanal makinesine disk ekleme](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bölümündeki adımları uygulayın. Disk ekleme adımları, DSVM 'de zaten yüklü olan Azure CLı 'yi kullanır. Bu adımları tamamen DSVM 'den tamamlayabilirsiniz. Depolamayı arttırmaya yönelik başka bir seçenek de [Azure dosyalarını](../../storage/files/storage-how-to-use-files-linux.md)kullanmaktır.

Verileri indirmek için bir Terminal penceresi açın ve ardından şu komutu çalıştırın:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

İndirilen dosyada başlık satırı yok. Üst bilgisine sahip başka bir dosya oluşturalım. Uygun üstbilgileriyle bir dosya oluşturmak için bu komutu çalıştırın:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Sonra iki dosyayı birlikte birleştirin:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Veri kümesinde her e-posta için çeşitli istatistik türleri vardır:

* **Sözcük \_ FREQ \_ _sözcüğü_** gibi sütunlar, e- *postadaki kelimeyle* eşleşen sözcüklerin yüzdesini gösterir. Örneğin, **sözcük \_ FREQ \_** **1** ise, e-postadaki tüm sözcüklerin %1 ' i *haline* getirin.
* Karakter **\_ FREQ \_ _karakteri_** gibi sütunlar, e-postadaki *karakter olan tüm* karakterlerin yüzdesini belirtir.
* **büyük \_ çalışma \_ uzunluğu \_ en uzun** , büyük harflerin bir dizisinin en uzun uzunluğudur.
* **büyük \_ çalışma \_ uzunluğu \_ ortalaması** , büyük harflerin tüm sıralarının ortalama uzunluğudur.
* **sermaye \_ çalışma \_ uzunluğu \_ toplamı** , tüm büyük harflerin tüm sıralarının toplam uzunluğudur.
* **istenmeyen** posta, e-postanın istenmeyen posta olarak kabul edilip edilmeyeceğini belirtir (1 = istenmeyen posta, 0 = istenmeyen posta değil).

## <a name="explore-the-dataset-by-using-r-open"></a>R Open kullanarak veri kümesini keşfet

R kullanarak verileri inceleyerek bazı temel makine öğrenimi yapalim. DSVM, [Microsoft R açık](https://mran.revolutionanalytics.com/open/) önceden yüklenmiş olarak gelir. R 'nin önceden yüklenmiş sürümündeki çok iş parçacıklı matematik kitaplıkları, tek iş parçacıklı sürümlerden daha iyi performans sunar. R Open Ayrıca CRAN paket deposunun bir anlık görüntüsü aracılığıyla reproducibility sağlar.

Bu izlenecek yolda kullanılan kod örneklerinin kopyalarını almak için, git kullanarak Azure-Machine-Learning-Data-Science deposunu kopyalayın. Git, DSVM 'ye önceden yüklenir. Git komut satırında şunu çalıştırın:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Bir Terminal penceresi açın ve R etkileşimli konsolunda yeni bir R oturumu başlatın. DSVM 'de önceden yüklenmiş olan RStudio 'yu da kullanabilirsiniz.

Verileri içeri aktarmak ve ortamı ayarlamak için:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Her sütun hakkında özet istatistikleri görmek için:

```R
summary(data)
```

Verilerin farklı bir görünümü için:

```R
str(data)
```

Bu görünüm, veri kümesindeki her değişkenin ve ilk birkaç değerin türünü gösterir.

**İstenmeyen posta** sütunu bir tamsayı olarak okunmıştı, ancak aslında kategorik bir değişkendir (veya faktörü). Türünü ayarlamak için:

```R
data$spam <- as.factor(data$spam)
```

Araştırmacı analiz yapmak için, DSVM 'de önceden yüklenmiş R için popüler bir grafik kitaplığı olan [ggplot2](https://ggplot2.tidyverse.org/) paketini kullanın. Daha önce görüntülenmiş Özet verilere bağlı olarak, ünlem işareti karakterinin sıklığıyla ilgili özet istatistikleriz var. Aşağıdaki komutları çalıştırarak bu frekansları buraya çizelim:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Sıfır çubuk çizimi eğriltiğinden, bunu ortadan kaldıralim:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

İlginç olmayan, 1 ' den fazla önemsiz bir yoğunluk vardır. Yalnızca bu verilere göz atalım:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Ardından, e-postaya göre ayırarak ayırın:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Bu örnekler, benzer çizimler yapmanıza ve diğer sütunlardaki verileri incelemenize yardımcı olmalıdır.

## <a name="train-and-test-a-machine-learning-model"></a>Makine öğrenimi modelini eğitme ve test etme

Veri kümesindeki e-postaları istenmeyen veya ham olarak sınıflandırmak için birkaç makine öğrenimi modeli eğitelim. Bu bölümde, karar ağacı modeli ve rastgele bir orman modeli eğiyoruz. Ardından, tahminlerinin doğruluğunu test ediyoruz.

> [!NOTE]
> Aşağıdaki kodda kullanılan *rpart* (özyinelemeli bölümlendirme ve regresyon ağaçları) PAKETI dsvm 'de zaten yüklü.

İlk olarak, veri kümesini eğitim kümelerine ve test kümelerine bölelim:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Sonra, e-postaları sınıflandırmak için bir karar ağacı oluşturun:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Sonuç şu şekildedir:

![Oluşturulan karar ağacının diyagramı](./media/linux-dsvm-walkthrough/decision-tree.png)

Eğitim kümesi üzerinde ne kadar iyi performans olduğunu öğrenmek için aşağıdaki kodu kullanın:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Test kümesinde ne kadar iyi performans olduğunu anlamak için:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Ayrıca rastgele bir orman modeli de deneyelim. Rastgele ormanlar çok sayıda karar ağacının eğitei ve tüm bireysel karar ağaçlarından sınıflandırmaların modu olan bir sınıfı çıktı. Bunlar, bir eğitim veri kümesinin fazla olması için bir karar ağacı modelinin kullanımını düzelttiğinden daha güçlü bir makine öğrenimi yaklaşımı sağlar.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Derin öğrenme öğreticileri ve izlenecek yollar

Çerçeve tabanlı örneklere ek olarak, kapsamlı bir izlenecek yol kümesi de sağlanır. Bu izlenecek yollar, görüntü ve metin/dil anlama gibi etki alanlarında derin öğrenme uygulamalarınızın geliştirilmesine hızlı bir başlangıç yapmanıza yardımcı olur.

- [Farklı çerçeveler arasında sinir ağlarını çalıştırma](https://github.com/ilkarman/DeepLearningFrameworks): kodu bir çerçeveden diğerine geçirmeyi gösteren kapsamlı bir anlatım. Ayrıca, çerçeve genelinde model ve çalışma zamanı performansının nasıl karşılaştırılacağını gösterir. 

- [Görüntülerde ürünleri algılamaya yönelik uçtan uca bir çözüm oluşturmak için nasıl yapılır Kılavuzu](https://github.com/Azure/cortana-intelligence-product-detection-from-images): görüntü algılama, görüntüleri içindeki nesneleri bulup sınıflandırmanın bir tekniğidir. Teknoloji, çok sayıda gerçek hayatta iş etki alanında büyük bir rekele sunmayı mümkün hale getirir. Örneğin, satıcılar bu tekniği kullanarak bir müşterinin rafı hangi ürüne kullandığını tespit edebilir. Sırasıyla bu bilgiler, ürün envanterini yönetme mağazalarına yardımcı olur. 

- [Ses Için derin öğrenme](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): Bu öğreticide, [kentsel sesler veri kümesinde](https://urbansounddataset.weebly.com/)ses olayı algılaması için derin bir öğrenme modelinin nasıl eğeceği gösterilmektedir. Öğretici, ses verileriyle çalışma hakkında genel bakış sağlar.

- [Metin belgelerinin sınıflandırılması](https://github.com/anargyri/lstm_han): Bu kılavuzda iki farklı sinir ağ mimarisi oluşturma ve eğitme gösterilmektedir: hiyerarşik dikkat ağı ve uzun kısa süreli bellek (lstm). Bu sinir Networks, metin belgelerinin sınıflandırılmasında derin öğrenme için keras API 'sini kullanır. Keras, en popüler derin öğrenme çerçevelerinin üçüne (Microsoft Cognitive Toolkit, TensorFlow ve Ano) önde bir ön uçdır.

## <a name="other-tools"></a>Diğer araçlar

Kalan bölümler, Linux DSVM 'de yüklü olan araçların bazılarını nasıl kullanacağınızı gösterir. Bu araçları tartıştık:

* XGBoost
* Python
* Jupi Terhub
* Rattle
* PostgreSQL ve SQUIRREL SQL
* Azure Synapse Analytics (eski adı SQL DW)

### <a name="xgboost"></a>XGBoost

[Xgboost](https://xgboost.readthedocs.org/en/latest/) hızlı ve doğru bir şekilde artırılmış ağaç uygulamasını sağlar.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost, Python veya bir komut satırından da çağırabilir.

### <a name="python"></a>Python

Python geliştirmesi için, Anaconda Python dağıtımları 3,5 ve 2,7, DSVM 'ye yüklenir.

> [!NOTE]
> Anaconda dağıtımı [Conda](https://conda.pydata.org/docs/index.html)içerir. Farklı sürümlere veya paketlerine yüklenmiş özel Python ortamları oluşturmak için Conda kullanabilirsiniz.

Ayrıca, spambase veri kümesinden bazılarını okuyup e-postaları Scikit içindeki destek vektör makinelerle sınıflandıralım-öğrenme:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Tahminleri yapmak için:

```Python
clf.predict(X.ix[0:20, :])
```

Bir Azure Machine Learning uç noktasının nasıl yayımlanacağını göstermek için, daha basit bir model yapalim. R modelini daha önce yayımladığımızda kullandığımız üç değişkeni kullanacağız:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>Jupi Terhub

DSVM 'deki Anaconda dağıtımı, Python, R veya Julia kodunu ve analizini paylaşmak için platformlar arası bir ortam olan Jupyter Notebook birlikte gelir. Jupyter Notebook Jupyıterhub üzerinden erişilir. Https://adresinden yerel Linux Kullanıcı adınızı ve parolanızı kullanarak oturum açın \<DSVM DNS name or IP address\> : 8000/. Jupi Terhub için tüm yapılandırma dosyaları/etc/jupi terhubnda bulunur.

> [!NOTE]
> `pip`Geçerli çekirdekte bir Jupyter Notebook Python paket yöneticisini (komutu aracılığıyla) kullanmak için, kod hücresinde bu komutu kullanın:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> `conda`Geçerli Çekirdekte bulunan bir Jupyter Notebook Conda yükleyicisini (komutu aracılığıyla) kullanmak için, bir kod hücresinde bu komutu kullanın:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 'de birden çok örnek Not defteri zaten yüklü:

* Örnek Python Not defterleri:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* Örnek R Not Defteri:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Julia dili, Linux DSVM 'deki komut satırından da kullanılabilir.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A* Nalitik *T* OOL *T* o *L* kazanmak *)* veri araştırma için bir grafik R aracıdır. Rattle, verileri yüklemeyi, keşfetmenizi ve dönüştürmeyi, modelleri derlemeyi ve değerlendirmeyi kolaylaştıran sezgisel bir arabirime sahiptir. [Rattle: R Için bir veri madenciliği GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) , Rattle 'in özelliklerini gösteren bir anlatım sağlar.

Aşağıdaki komutları çalıştırarak Rattle 'i yükleyip başlatın:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> DSVM 'ye Rattle yüklemeniz gerekmez. Ancak, Rattle açıldığında ek paketler kurmanız istenebilir.

Rattle, sekme tabanlı bir arabirim kullanır. Sekmelerin çoğu, veri yükleme veya verileri keşfetme gibi [ekip veri bilimi işlemindeki](../team-data-science-process/index.yml)adımlara karşılık gelir. Veri bilimi işlemi, sekmeler aracılığıyla soldan sağa doğru akar. Son sekme, Rattle tarafından çalıştırılan R komutlarının günlüğünü içerir.

Veri kümesini yüklemek ve yapılandırmak için:

1. Dosyayı yüklemek için **veri** sekmesini seçin.
1. **Dosya adı**' nın yanındaki seçiciyi seçin ve ardından **Spambaseheaders. Data** öğesini seçin.
1. Dosyasını yükleyin. **Yürüt**' ü seçin. Tanımlı veri türü de dahil olmak üzere her bir sütunun özetini görmeniz gerekir. bir giriş, hedef ya da başka bir değişken türü olsun; ve benzersiz değer sayısı.
1. Rattle, **istenmeyen posta** sütununu hedef olarak doğru şekilde tanımladı. **İstenmeyen posta** sütununu seçin ve ardından **hedef veri türünü** **kategoric** olarak ayarlayın.

Verileri incelemek için:

1. **Keşfet** sekmesini seçin.
1. Değişken türleri ve bazı Özet istatistikleri hakkındaki bilgileri görmek için **Özet**  >  **yürütme**' yi seçin.
1. Her değişkenle ilgili diğer istatistik türlerini görüntülemek için, **açıkla** veya **temel bilgiler** gibi diğer seçenekleri belirleyin.

Ayrıca, öngörülü çizimler oluşturmak için **keşfet** sekmesini de kullanabilirsiniz. Verilerin histogramını çizmek için:

1. **Dağıtımları** seçin.
1. **Word_freq_remove** ve **word_freq_you** için **histogram**' ı seçin.
1. **Yürüt**’ü seçin. Tek bir grafik penceresinde her iki yoğunluk işaretini de görmeniz gerekir. burada, e-postalardan _Kaldır_' a kadar çok daha sık _gördüğünüz sözcük burada_ görünür.

**Bağıntı** çizimleri de ilginç. Bir çizim oluşturmak için:

1. **Tür** için **bağıntı**' yı seçin.
1. **Yürüt**’ü seçin.
1. Rattle, en fazla 40 değişken önerdiği konusunda sizi uyarır. Çizimi görüntülemek için **Evet** ' i seçin.

Daha fazla ilgi çekici bağıntılı ilişkiler vardır: _teknoloji_ , örneğin, _HP_ ve _Labs_'e özellikle yöneliktir. Ayrıca, veri kümesinin alan kodu 650 olduğundan, _650_ ile de kesinlikle bağıntılı olur.

Sözcükler arasındaki eş ilişkilerin sayısal değerleri, **keşfet** penceresinde kullanılabilir. Örneğin, bu _teknolojinin_ ve _paradan_ _olumsuz şekilde bağıntılı_ olduğunu unutmayın.

Rattle, bazı yaygın sorunları işleyecek şekilde veri kümesini dönüştürebilir. Örneğin, özellikleri yeniden düzenleyebilir, ımpute değerlerini eksik, aykırı değerleri işleyebilir ve eksik verileri olan değişkenleri veya gözlemleri kaldırabilir. Rattle, gözlemler ve değişkenler arasındaki ilişki kurallarını da tanımlayabilir. Bu sekmeler, bu giriş anlatımında ele alınmazlar.

Rattle, küme analizini de çalıştırabilir. Çıktının daha kolay okunmasını sağlamak için bazı özellikleri dışlayalım. **Veri** sekmesinde, bu 10 öğe hariç değişkenlerin her birinin yanında **Yoksay** ' ı seçin:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* istenmeyen posta

**Küme** sekmesine dönün. **Kortalamalar**' ı seçin ve ardından **küme sayısını** **4** olarak ayarlayın. **Yürüt**’ü seçin. Sonuçlar çıkış penceresinde görüntülenir. Bir kümede yüksek düzeyde _George_ ve _HP_ vardır ve büyük olasılıkla yasal bir iş e-postadır.

Temel bir karar ağacı makine öğrenimi modeli oluşturmak için:

1. **Model** sekmesini seçin,
1. **Tür** için **ağaç**' ı seçin.
1. Çıkış penceresinde ağacı metin biçiminde göstermek için **Yürüt** ' ü seçin.
1. Grafik sürümünü görüntülemek için **Çiz** düğmesini seçin. Karar ağacı, daha önce rpart kullanarak elde ettiğimiz ağaca benzer.

Rattle 'ın yararlı bir özelliği, birkaç makine öğrenimi yöntemi çalıştırma ve bunları hızlı bir şekilde değerlendirme yeteneğidir. Adımlar şunlardır:

1. **Tür** için **Tümü**' nü seçin.
1. **Yürüt**’ü seçin.
1. Rattle çalışmayı bitirdiğinde, **SVM** gibi herhangi bir **tür** değeri seçebilir ve sonuçları görüntüleyebilirsiniz.
1. Ayrıca, **değerlendirme** sekmesini kullanarak doğrulama kümesindeki modellerin performansını karşılaştırabilirsiniz. Örneğin, **hata matrisi** seçimi, doğrulama kümesindeki her bir model için karışıklık matrisi, genel hata ve Ortalama sınıf hatası gösterir. Ayrıca, ROC eğrileri çizdirebilirsiniz, duyarlılık analizini çalıştırabilir ve diğer model değerlendirmesi türlerini gerçekleştirebilirsiniz.

Model oluşturma işiniz bittiğinde, oturumunuz sırasında Rattle tarafından çalıştırılan R kodunu görüntülemek için **günlük** sekmesini seçin. **Dışarı aktar** düğmesini seçerek bunu kaydedebilirsiniz.

> [!NOTE]
> Geçerli Rattle sürümü bir hata içeriyor. Betiği değiştirmek veya adımları daha sonra tekrarlamak üzere kullanmak için **#** *Bu günlüğü... ' nin önüne bu günlüğü dışarı aktar* ' a bir karakter eklemeniz gerekir.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL ve SQUIRREL SQL

DSVM, PostgreSQL yüklüyken birlikte gelir. PostgreSQL, gelişmiş, açık kaynaklı bir ilişkisel veritabanıdır. Bu bölümde, spambase veri kümesinin PostgreSQL 'e nasıl yükleneceği ve sonra sorgulanması gösterilmektedir.

Verileri yükleyebilmeniz için önce localhost 'tan parola kimlik doğrulamasına izin vermeniz gerekir. Bir komut isteminde şunu çalıştırın:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Yapılandırma dosyasının en altına yakın olan, izin verilen bağlantıları ayrıntılandırır:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Bir Kullanıcı adı ve parola kullanarak oturum açabilmemiz için **IPv4 yerel bağlantıları** **satırını, yerine** **MD5** kullanacak şekilde değiştirin:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Sonra PostgreSQL hizmetini yeniden başlatın:

```Bash
sudo systemctl restart postgresql
```

Yerleşik Postgres kullanıcısı olarak *psql* (PostgreSQL için etkileşimli Terminal) başlatmak için şu komutu çalıştırın:

```Bash
sudo -u postgres psql
```

Oturum açmak için kullandığınız Linux hesabının kullanıcı adını kullanarak yeni bir kullanıcı hesabı oluşturun. Parola oluştur:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Psql 'de oturum aç:

```Bash
psql
```

Verileri yeni bir veritabanına aktarın:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Şimdi, verileri keşfedelim ve bir JDBC sürücü aracılığıyla veritabanlarıyla etkileşim kurmak için kullanabileceğiniz bir grafik araç olan SQUIRREL SQL kullanarak bazı sorgular çalıştıralım.

Başlamak için, **uygulamalar** menüsünde, SQUIRREL SQL ' i açın. Sürücüyü ayarlamak için:

1. **Windows**  >  **Görünüm sürücülerini** seçin.
1. **PostgreSQL** öğesine sağ tıklayıp **sürücüyü değiştir**' i seçin.
1. **Ek sınıf yolu**  >  **Ekle**' yi seçin.
1. **Dosya adı** için **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar** girin.
1. **Aç**’ı seçin.
1. **Liste sürücülerini** seçin. **Sınıf adı** için, **org. PostgreSQL. Driver**' ı seçin ve ardından **Tamam**' ı seçin.

Yerel sunucuyla bağlantı kurmak için:

1. **Windows**  >  **Görünüm diğer adlarını seçin.**
1. **+** Yeni bir diğer ad oluşturmak için düğmeyi seçin. Yeni diğer ad için **Istenmeyen posta veritabanı** girin. 
1. **Sürücü** Için **PostgreSQL**' i seçin.
1. URL 'YI **JDBC: PostgreSQL://localhost/spam** olarak ayarlayın.
1. Kullanıcı adınızı ve parolanızı girin.
1. **Tamam**’ı seçin.
1. **Bağlantı** penceresini açmak Için, **istenmeyen posta veritabanı** diğer adına çift tıklayın.
1. **Bağlan**’ı seçin.

Bazı sorguları çalıştırmak için:

1. **SQL** sekmesini seçin.
1. **SQL** sekmesinin üst kısmındaki sorgu kutusuna, gibi temel bir sorgu girin `SELECT * from data;` .
1. Sorguyu çalıştırmak için CTRL + ENTER tuşlarına basın. Varsayılan olarak, SQUIRREL SQL Sorgunuzdaki ilk 100 satırı döndürür.

Bu verileri araştırmak için çalıştırabileceğiniz birçok sorgu daha vardır. Örneğin *, sözcüğün sıklığı* istenmeyen ve ham nasıl farklıdır?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Ya da, genellikle *3B* içeren e-posta özellikleri nelerdir?

```SQL
SELECT * from data order by word_freq_3d desc;
```

Yüksek *3B* görünüşe sahip e-postaların çoğu istenmeyen postalardır. Bu bilgiler, e-postaları sınıflandırmak için bir tahmine dayalı model oluşturmak için yararlı olabilir.

Makine öğrenimini bir PostgreSQL veritabanında depolanan verileri kullanarak yapmak istiyorsanız, [Madlib](https://madlib.incubator.apache.org/)kullanmayı düşünün.

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (eski adı SQL DW)

Azure SYNAPSE Analytics, hem ilişkisel hem de ilişkisel olmayan çok büyük hacimli verileri işleyebilen bulut tabanlı, genişleme bir veritabanıdır. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics nedir?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Veri ambarına bağlanmak ve tabloyu oluşturmak için bir komut isteminden aşağıdaki komutu çalıştırın:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

Sqlcmd isteminde şu komutu çalıştırın:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

BCP kullanarak verileri kopyalama:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> İndirilen dosya Windows stili satır sonları içerir. BCP Aracı, UNIX stili satır sonları bekler. BCP 'ye söylemek için-r bayrağını kullanın.

Ardından, sqlcmd kullanarak sorgulayın:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Ayrıca, SQUIRREL SQL kullanarak da sorgulama yapabilirsiniz. SQL Server JDBC sürücüsünü kullanarak PostgreSQL 'e benzer adımları izleyin. JDBC sürücüsü/usr/share/Java/jdbcdrivers/sqljdbc42.jar klasöründedir.