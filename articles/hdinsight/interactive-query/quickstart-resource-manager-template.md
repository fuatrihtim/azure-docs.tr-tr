---
title: 'Hızlı başlangıç: şablon kullanarak etkileşimli sorgu kümesi oluşturma-Azure HDInsight'
description: Bu hızlı başlangıçta, Azure HDInsight 'ta etkileşimli bir sorgu kümesi oluşturmak için Kaynak Yöneticisi şablonunun nasıl kullanılacağı gösterilmektedir.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 59209bd1b3b6026a229527c1fb0d4b6346a5f3f1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869693"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure HDInsight 'ta etkileşimli sorgu kümesi oluşturma

Bu hızlı başlangıçta, Azure HDInsight 'ta [etkileşimli bir sorgu](./apache-interactive-query-get-started.md) kümesi oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. Etkileşimli sorgu (Apache Hive LLAP ya da [düşük gecikme süreli analitik işlem](https://cwiki.apache.org/confluence/display/Hive/LLAP)), bir Azure HDInsight [kümesi türüdür](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'a dağıtma" border="true":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-hdinsight-interactive-hive/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): bir Azure depolama hesabı oluşturun.
* [Microsoft. HDInsight/Cluster](/azure/templates/microsoft.hdinsight/clusters): HDInsight kümesi oluşturma.

### <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve ARM şablonunu açmak için aşağıdaki **Azure 'A dağıt** düğmesini seçin.

    [:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure’a dağıtın" border="true":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Aşağıdaki değerleri yazın veya seçin:

    |Özellik |Açıklama |
    |---|---|
    |Abonelik|Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin.|
    |Kaynak grubu|Aşağı açılan listeden, mevcut kaynak grubunuzu seçin veya **Yeni oluştur**' u seçin.|
    |Konum|Değer, kaynak grubu için kullanılan konum ile tekrar doldurulur.|
    |Küme Adı|Genel olarak benzersiz bir ad girin. Bu şablon için yalnızca küçük harfler ve rakamlar kullanın.|
    |Küme Oturum Açma Kullanıcı Adı|Kullanıcı adını belirtin, varsayılan olarak **admin**' dir.|
    |Küme Oturum Açma Parolası|Bir parola belirtin. Parola en az 10 karakter uzunluğunda olmalıdır ve en az bir rakam, bir büyük harf ve bir küçük harf, bir alfasayısal olmayan karakter (' "' karakterleri dışında) içermelidir. |
    |SSH Kullanıcı adı|Kullanıcı adını belirtin, varsayılan olarak sshuser|
    |SSH parolası|Parolayı girin.|

    :::image type="content" source="./media/quickstart-resource-manager-template/resource-manager-template-hive.png" alt-text="Kaynak Yöneticisi şablonu dağıtma HBase" border="true":::

1. **Hüküm ve koşulları** gözden geçirin. Ardından, **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u ve ardından **satın al**' ı seçin. Dağıtımınızın devam ettiğini belirten bir bildirim alırsınız. Bir küme oluşturmak yaklaşık 20 dakika sürer.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Küme oluşturulduktan sonra **Kaynağa Git** bağlantısına sahip bir **dağıtım başarılı** bildirimi alırsınız. Kaynak grubu sayfanız yeni HDInsight kümenizi ve kümeyle ilişkili varsayılan depolamayı listeler. Her kümenin bir [Azure Blob depolama](../hdinsight-hadoop-use-blob-storage.md) hesabı, [Azure Data Lake Storage 1.](../hdinsight-hadoop-use-data-lake-storage-gen1.md)veya  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) bağımlılığı vardır. Bu, varsayılan depolama hesabı olarak adlandırılır. HDInsight kümesi ve varsayılan depolama hesabı aynı Azure bölgesinde birlikte bulunmalıdır. Küme silme, depolama hesabını silmez.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Azure portal kümenize gidin ve **Sil**' i seçin.

[Kaynak Yöneticisi şablonu silme HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek, hem HDInsight kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir ARM şablonu kullanarak HDInsight 'ta etkileşimli bir sorgu kümesi oluşturmayı öğrendiniz. Sonraki makalede, Apache Hive sorguları çalıştırmak için Apache Zeppelin kullanmayı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Apache Zeppelin ile Azure HDInsight 'ta Apache Hive sorguları yürütme](./hdinsight-connect-hive-zeppelin.md)
