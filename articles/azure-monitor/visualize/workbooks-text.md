---
title: Azure Izleyici çalışma kitapları metin parametreleri
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın. Çalışma kitabı metin parametreleri hakkında daha fazla bilgi edinin.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725535"
---
# <a name="workbook-text-parameters"></a>Çalışma kitabı metin parametreleri

TextBox parametreleri, çalışma kitabı kullanıcılarından metin girişi toplamanın basit bir yolunu sağlar. Bu, girişi toplamak için bir açılan liste kullanımı pratik olmadığında kullanılır (örneğin, rastgele bir eşik veya genel filtreler). Çalışma kitapları, yazarların TextBox 'ın varsayılan değerini bir sorgudan almasını sağlar. Bu, ölçüm P95 göre varsayılan eşiği ayarlama gibi ilginç senaryolara olanak tanır.

Metin kutularının yaygın kullanımı, diğer çalışma kitabı denetimleri tarafından kullanılan iç değişkenlerdir. Bu, varsayılan değerler için bir sorgudan yararlanarak ve giriş denetiminin okuma modunda görünmez hale getirilmesi ile yapılır. Örneğin, bir Kullanıcı bir formülün (Kullanıcı değil) bir formülden gelmesini ve sonra eşiği sonraki sorgularda kullanmasını isteyebilir.

## <a name="creating-a-text-parameter"></a>Metin parametresi oluşturma
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `SlowRequestThreshold`
    2. Parametre türü: `Text`
    3. Gerekli: `checked`
    4. Sorgudan varsayılan değeri al: `unchecked`
5. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

    ![Metin parametresinin oluşturulmasını gösteren resim](./media/workbooks-text/text-create.png)

Çalışma kitabının okuma modunda nasıl görüneceğine bu şekilde bakabilirsiniz.

![Okuma modundaki bir metin parametresini gösteren resim](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Bir metin parametresine başvurma
1. Mavi bağlantıyı seçerek çalışma kitabına bir sorgu denetimi ekleyin `Add query` ve bir Application Insights kaynağı seçin.
2. KQL kutusunda şu kod parçacığını ekleyin:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Aşağıdaki sorguyu etkin bir şekilde çalıştırdığınız sorgu denetimiyle birlikte 500 değeriyle birlikte metin parametresini kullanarak:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Sonuçları görmek için sorguyu çalıştırın

    ![KQL 'de başvurulan metin parametresini gösteren resim](./media/workbooks-text/text-reference.png)

> [!NOTE]
> Yukarıdaki örnekte, `{SlowRequestThreshold}` bir tamsayı değeri temsil eder. Gibi bir dizeyi sorguluyorsanız, `{ComputerName}` `"{ComputerName}"` parametre alanı için tırnak işareti olmadan kabul etme girişine tırnak eklemek üzere kusto sorgunuzu değiştirmeniz gerekir.

## <a name="setting-default-values"></a>Varsayılan değerler ayarlanıyor
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `SlowRequestThreshold`
    2. Parametre türü: `Text`
    3. Gerekli: `checked`
    4. Sorgudan varsayılan değeri al: `checked`
5. KQL kutusunda şu kod parçacığını ekleyin:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Bu sorgu, uygulamadaki tüm istekler için metin kutusunun varsayılan değerini 95 yüzdebirlik süresine ayarlar.
6. Sonucu görmek için sorguyu çalıştırın
7. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

    ![KQL 'den varsayılan değere sahip bir metin parametresini gösteren resim](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Bu örnek Application Insights verileri sorgularken, yaklaşım herhangi bir günlük tabanlı veri kaynağı için kullanılabilir-Log Analytics, Azure Kaynak Grafiği vb. olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](./workbooks-overview.md#visualizations) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](./workbooks-access-control.md) ve paylaşma.