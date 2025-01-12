---
title: Data Factory işlem hattı oluşturmak için Azure portal kullanma
description: Bu öğreticide işlem hattıyla veri fabrikası oluşturmak için Azure portalını kullanmaya yönelik adım adım yönergeler sağlanır. İşlem hattı, Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalamak için kopyalama etkinliğini kullanır.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 08ab3c9181f4fdcfc9b55bf80c53f88ff26f90ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584410"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Blob depolamadan Azure SQL veritabanı 'ndaki bir veritabanına veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure Data Factory kullanıcı arabirimini (UI) kullanarak bir veri fabrikası oluşturursunuz. Bu veri fabrikasındaki işlem hattı, verileri Azure Blob depolama alanından Azure SQL veritabanı 'ndaki bir veritabanına kopyalar. Bu öğreticideki yapılandırma düzeni, dosya tabanlı bir veri deposundan ilişkisel bir veri deposuna kopyalama için geçerlidir. Kaynak ve havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

> [!NOTE]
> İlk kez Data Factory kullanıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Kopyalama etkinliğiyle işlem hattı oluşturma.
> * İşlem hattında test çalıştırması yapma.
> * İşlem hattını el ile tetikleme.
> * İşlem hattını bir zamanlamaya göre tetikleme.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Önkoşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. Blob depolama alanını *kaynak* veri deposu olarak kullanabilirsiniz. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md).
* **Azure SQL veritabanı**. Veritabanını *havuz* veri deposu olarak kullanabilirsiniz. Azure SQL veritabanında bir veritabanınız yoksa, oluşturma adımları için [Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md) bölümüne bakın.

### <a name="create-a-blob-and-a-sql-table"></a>Bir blob ve SQL tablosu oluşturma

Aşağıdaki adımları uygulayarak öğretici için Blob depolama alanınızı ve SQL veritabanınızı hazırlayın.

#### <a name="create-a-source-blob"></a>Kaynak blob oluşturma

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayın ve diskinizde **emp.txt** dosyası olarak kaydedin:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Blob depolama alanınızda **adftutorial** adlı bir kapsayıcı oluşturun. Bu kapsayıcıda **input** adlı bir klasör oluşturun. Sonra, **emp.txt** dosyasını **input** klasörüne yükleyin. Bu görevleri yerine getirmek için Azure Portal'ı veya [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

#### <a name="create-a-sink-sql-table"></a>Havuz SQL tablosu oluşturma

1. Veritabanınızdaki **dbo.** bir tablosunu oluşturmak IÇIN aşağıdaki SQL betiğini kullanın:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Azure hizmetlerinin SQL Server’a erişmesine izin ver. Data Factory’nin SQL Server’ınıza veri yazabilmesi için SQL Server’ınız için **Azure hizmetlerine erişime izin ver** ayarının **AÇIK** olduğundan emin olun. Bu ayarı doğrulamak ve etkinleştirmek için, mantıksal SQL Server > genel bakış > sunucu güvenlik duvarını ayarla ' ya gidin> **Azure hizmetlerine erişime Izin ver** seçeneğini **Açık** olarak ayarlayın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, bir veri fabrikası oluşturacak ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory kullanıcı arabirimini başlatacaksınız.

1. **Microsoft Edge** veya **Google Chrome**'ı açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
2. Sol menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin.
3. **Data Factory oluştur** sayfasında, **temel bilgiler** sekmesinde, veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin.
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    a. Açılan listeden var olan bir kaynak grubunu seçin.

    b. **Yeni oluştur**' u seçin ve yeni bir kaynak grubunun adını girin.
    
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 
5. **Bölge** altında, Veri Fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.
6. **Ad** alanına **ADFTutorialDataFactory** girin.

   Azure veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. (örneğin, Adınızadftutorialdatafactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

7. **Sürüm** bölümünde **V2**'yi seçin.
8. Üstteki **Git yapılandırması** sekmesini seçin ve **Git 'ı daha sonra Yapılandır** onay kutusunu seçin.
9. **Gözden geçir + oluştur**' u seçin ve doğrulama geçtikten sonra **Oluştur** ' u seçin.
10. Oluşturma işlemi tamamlandıktan sonra, Bildirim Merkezi ' nde bildirimi görürsünüz. Data Factory sayfasına gitmek için **Kaynağa Git** ' i seçin.
11. Azure Data Factory Kullanıcı arabirimini ayrı bir sekmede başlatmak için **yazar & İzleyicisi** ' ni seçin.


## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, veri fabrikasında kopyalama etkinliği ile bir işlem hattı oluşturacaksınız. Kopyalama etkinliği, verileri Blob depolama alanından SQL Veritabanı'na kopyalar. [Hızlı başlangıç eğitiminde](quickstart-create-data-factory-portal.md), şu adımları izleyerek bir işlem hattı oluşturdunuz:

1. Bağlı hizmeti oluşturma.
1. Giriş ve çıkış veri kümelerini oluşturma.
1. İşlem hattı oluşturma.

Bu öğreticide işlem hattını oluşturmaya başlayacaksınız. Daha sonra işlem hattını yapılandırmanız gerektiğinde bağlı hizmetleri ve veri kümelerini oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin.

   ![İşlem hattı oluşturma](./media/doc-common-process/get-started-page.png)

1. Genel panelinde **Özellikler** altında, **ad** için **copypipeline** ' yı belirtin. Sonra sağ üst köşedeki Özellikler simgesine tıklayarak paneli daraltın.

1. **Etkinlikler** araç kutusunda **taşıma ve dönüştürme** kategorisini genişletin ve araç kutusundan **veri kopyalama** etkinliğini sürükleyin ve ardışık düzen Tasarımcısı yüzeyine bırakın. **Ad** için **CopyFromBlobToSql** adını belirtin.

    ![Kopyalama etkinliği](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Kaynağı yapılandırma

>[!TIP]
>Bu öğreticide, *hesap anahtarını* kaynak veri deponuzu kimlik doğrulama türü olarak kullanacaksınız, ancak desteklenen diğer kimlik doğrulama yöntemlerini seçebilirsiniz: *SAS URI 'Si*,*hizmet sorumlusu* ve gerekirse *yönetilen kimlik* . Ayrıntılar için [Bu makaledeki](./connector-azure-blob-storage.md#linked-service-properties) ilgili bölümlere bakın.
>Veri depolarının gizli dizilerini güvenli bir şekilde depolamak için bir Azure Key Vault kullanılması da önerilir. Ayrıntılı çizimler için [Bu makaleye](./store-credentials-in-key-vault.md) bakın.

1. **Kaynak** sekmesine gidin. Kaynak veri kümesi oluşturmak için **+ Yeni** seçeneğini belirleyin.

1. **Yeni veri kümesi** Iletişim kutusunda **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin. Kaynak veriler bir Blob depolama alanında olduğundan kaynak veri kümesi olarak **Azure Blob Depolama Alanı**'nı seçmeniz gerekir.

1. **Biçim Seç** iletişim kutusunda verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin.

1. **Özellikleri ayarla** Iletişim kutusunda ad Için **sourceblobdataset** girin. **Üst bilgi olarak ilk satır** onay kutusunu seçin. **Bağlı hizmet** metin kutusu altında **+ Yeni**' yi seçin.

1. **Yeni bağlı hizmet (Azure Blob depolama)** Iletişim kutusunda **AzureStorageLinkedService** as Name yazın, **depolama hesabı adı** listesinden depolama hesabınızı seçin. Bağlantıyı test edin, bağlantılı hizmeti dağıtmak için **Oluştur** ' u seçin.

1. Bağlı hizmet oluşturulduktan sonra, **özelliği ayarla** sayfasına geri gidilmesini sağlar. **Dosya yolu**’nun yanındaki **Gözat** seçeneğini belirleyin.

1. **Adföğreticisi/giriş** klasörüne gidin, **emp.txt** dosyasını seçin ve ardından **Tamam**' ı seçin.

1. **Tamam**’ı seçin. Otomatik olarak işlem hattı sayfasına gider. **Kaynak** sekmesinde **sourceblobdataset** ' in seçili olduğunu onaylayın. Bu sayfadaki verilerin önizlemesini görüntülemek için **Veri önizleme**‘yi seçin.

    ![Kaynak veri kümesi](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Havuzu yapılandırma
>[!TIP]
>Bu öğreticide, havuz veri deponuzda kimlik doğrulama türü olarak *SQL kimlik doğrulamasını* kullanırsınız, ancak desteklenen diğer kimlik doğrulama yöntemlerini seçebilirsiniz: *hizmet sorumlusu* ve gerekirse *yönetilen kimlik* . Ayrıntılar için [Bu makaledeki](./connector-azure-sql-database.md#linked-service-properties) ilgili bölümlere bakın.
>Veri depolarının gizli dizilerini güvenli bir şekilde depolamak için bir Azure Key Vault kullanılması da önerilir. Ayrıntılı çizimler için [Bu makaleye](./store-credentials-in-key-vault.md) bakın.

1. **Havuz** sekmesine gidin ve havuz veri kümesi oluşturmak için **+Yeni** seçeneğini belirleyin.

1. **Yeni veri kümesi** iletişim kutusunda, bağlayıcılar filtrelemek için arama kutusuna "SQL" girin, **Azure SQL veritabanı**' nı seçin ve ardından **devam**' ı seçin. Bu öğreticide verileri bir SQL veritabanına kopyalayacaksınız.

1. **Özellikleri ayarla** Iletişim kutusunda ad Için **outputsqldataset** girin. **Bağlı hizmet** açılan listesinden **+ Yeni**' yi seçin. Bağlı hizmeti bir veri kümesi ile ilişkilendirilmelidir. Bağlı hizmetin, Data Factory çalışma zamanında SQL veritabanına bağlanmak için kullandığı bağlantı dizesi vardır. Veri kümesi, verilerin kopyalanacağı kapsayıcıyı, klasörü ve dosyayı (isteğe bağlı) belirtilir.

1. **Yeni bağlı hizmet (Azure SQL veritabanı)** iletişim kutusunda aşağıdaki adımları uygulayın:

    a. **Ad** bölümüne **AzureSqlDatabaseLinkedService** girin.

    b. **Sunucu adı** bölümünde SQL Server örneğinizi seçin.

    c. **Veritabanı adı** altında, veritabanınızı seçin.

    d. **Kullanıcı adı** bölümüne kullanıcının adını girin.

    e. **Parola** bölümüne kullanıcının parolasını girin.

    f. Bağlantıyı test etmek için **Bağlantıyı sına**’yı seçin.

    örneğin: Bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

    ![Yeni bağlı hizmeti kaydedin](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Otomatik olarak **ayarlanan özellikler** iletişim kutusuna gider. **Tablo**’da **[dbo].[emp]** seçeneğini belirleyin. Ardından **Tamam**’ı seçin.

1. İşlem hattının bulunduğu sekmeye gidin ve **Havuz Veri Kümesi**’nde **OutputSqlDataset** seçeneğinin belirlendiğinden emin olun.

    ![İşlem hattı sekmesi](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

[Kopyalama etkinliğinde şema eşlemesini](copy-activity-schema-and-type-mapping.md)izleyerek kaynak şemasını isteğe bağlı hedef şemasına eşleyebilirsiniz.

## <a name="validate-the-pipeline"></a>İşlem hattını doğrulama
İşlem hattını doğrulamak için araç çubuğundan **Doğrula**'yı seçin.

Sağ üstteki **kod** ' a tıklayarak işlem hattı Ile ilişkili JSON kodunu görebilirsiniz.

## <a name="debug-and-publish-the-pipeline"></a>İşlem hattında hata ayıklama ve işlem hattını yayımlama
Yapıtları (bağlı hizmetler, veri kümeleri ve işlem hattı) Data Factory'de veya kendi Azure Repos Git deponuzda yayımlamadan önce işlem hattında hata ayıklayabilirsiniz.

1. İşlem hattında hata ayıklamak için araç çubuğunda **Hata Ayıkla**'yı seçin. Pencerenin altındaki **Çıkış** sekmesinde işlem hattı çalıştırmasının durumu görüntülenir.

1. İşlem hattı başarıyla çalıştırıldığında, üstteki araç çubuğunda **Tümünü Yayımla**' yı seçin. Bu eylem, oluşturduğunuz varlıkları (veri kümeleri ve işlem hatları) Data Factory'de yayımlar.

1. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. Bildirim iletilerini görmek için sağ üstteki **Bildirimleri Göster**'e (zil düğmesi) tıklayın.

## <a name="trigger-the-pipeline-manually"></a>İşlem hattını el ile tetikleme
Bu adımda, önceki adımda yayımladığınız işlem hattını el ile tetiklersiniz.

1. Araç çubuğunda **Tetikleyici**’yi ve sonra **Şimdi Tetikle**’yi seçin. İşlem **hattı çalıştırma** sayfasında **Tamam**' ı seçin.  

1. Soldaki **İzleyici** sekmesine gidin. El ile tetikleme tarafından tetiklenmiş bir işlem hattı çalıştırması görürsünüz. Etkinlik ayrıntılarını görüntülemek ve işlem hattını yeniden çalıştırmak için işlem **hattı adı** sütununun altındaki bağlantıları kullanabilirsiniz.

    [![İşlem hattı çalıştırmalarını izleme](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görmek için, işlem **hattı adı** sütununun altındaki **copypipeline** bağlantısını seçin. Bu örnekte yalnızca bir etkinlik bulunur, bu nedenle listede yalnızca bir giriş görürsünüz. Kopyalama işlemi hakkında daha fazla bilgi için **etkınlık adı** sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. İşlem hattı çalıştırmaları görünümüne dönmek için üstteki **tüm işlem hattı çalıştırmalarını** seçin. Görünümü yenilemek için **Yenile**’yi seçin.

    [![Etkinlik çalıştırmalarını izleme](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Veritabanındaki ısetable tablosuna iki satır daha eklendiğini  doğrulayın.

## <a name="trigger-the-pipeline-on-a-schedule"></a>İşlem hattını bir zamanlamaya göre tetikleme
Bu zamanlamada, işlem hattı için bir zamanlayıcı tetikleyicisi oluşturacaksınız. Tetikleyici, işlem hattını saatlik veya günlük gibi belirli bir zamanlamaya göre çalıştırır. Burada, belirtilen bitiş tarih/saati kadar tetikleyiciyi her dakikada çalışacak şekilde ayarlarsınız.

1. Sol üstte, izleyici sekmesinin üzerindeki **Yazar** sekmesine gidin.

1. İşlem hattınıza gidin, araç çubuğunda **Tetikleyici**'ye tıklayıp **Yeni/Düzenle**'yi seçin.

1. **Tetikleyiciler Ekle** iletişim kutusunda **tetikleyici alanı seç** için **+ Yeni** ' yi seçin.

1. **Yeni Tetikleyici** penceresinde aşağıdaki adımları uygulayın:

    a. **Ad** bölümüne **RunEveryMinute** girin.

    b. Tetikleyicinizin **başlangıç tarihini** güncelleştirin. Tarih, geçerli tarih saatten önce ise, tetikleyici, değişiklik yayımlandıktan sonra devreye girer. 

    c. **Saat dilimi** bölümünde açılan listeyi seçin.

    d. **Yinelemeyi** **her 1 dakikada** bir olarak ayarlayın.

    e. **Bitiş tarihini belirtme** onay kutusunu seçin ve geçerli tarih/saatten birkaç dakika sonra **son** parçayı güncelleştirin. Tetikleyicinin etkinleştirilmesi için, önce sizin değişiklikleri yayımlamanız gerekir. Bunu yalnızca birkaç dakika olarak ayarlarsanız ve daha sonra yayımlamazsanız, tetikleyici çalıştırmayı görmezsiniz.

    f. **Etkin** seçeneği için **Evet**' i seçin.

    örneğin: **Tamam**’ı seçin.

    > [!IMPORTANT]
    > Her bir işlem hattı çalıştırması ile bir maliyet ilişkilendirildiğinden bitiş tarihini uygun bir şekilde ayarlayın.

1. **Tetikleyiciyi düzenle** sayfasında, uyarıyı gözden geçirin ve ardından **Kaydet**' i seçin. Bu örnekteki işlem hattı hiçbir parametre almaz.

1. Değişikliği yayımlamak için **Tümünü Yayımla** ' ya tıklayın.

1. Tetiklenen işlem hattı çalıştırmalarını görmek için sol taraftaki **İzleyici** sekmesine gidin.

    [![Tetiklenen işlem hattı çalıştırmaları](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. İşlem **hattı çalıştırmaları** görünümünden **tetikleyici çalıştırmaları** görünümüne geçmek Için pencerenin sol tarafındaki **tetikleme çalıştırmaları** ' nı seçin.

1. Listede tetikleyici çalıştırmalarını görürsünüz.

1. Belirtilen bitiş saatine kadar **emp** tablosuna dakikada iki satır (her işlem hattı çalıştırması için) eklendiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Blob depolama alanındaki başka bir konuma kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Kopyalama etkinliğiyle işlem hattı oluşturma.
> * İşlem hattında test çalıştırması yapma.
> * İşlem hattını el ile tetikleme.
> * İşlem hattını bir zamanlamaya göre tetikleme.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.


Verileri şirket içinden buluta kopyalamayı öğrenmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Şirket içinden buluta veri kopyalama](tutorial-hybrid-copy-portal.md)
