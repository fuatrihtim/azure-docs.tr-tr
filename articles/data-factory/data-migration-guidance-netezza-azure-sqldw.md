---
title: Şirket içi Netezza sunucusundan Azure 'a veri geçirme
description: Şirket içi Netezza sunucusundan Azure 'a veri geçirmek için Azure Data Factory kullanın.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367784"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Şirket içi Netezza sunucusundan Azure 'a veri geçirmek için Azure Data Factory kullanma 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory, verileri şirket içi Netezza sunucusundan Azure depolama hesabınıza veya Azure SYNAPSE Analytics veritabanına geçirmek için performans, sağlam ve ekonomik bir mekanizma sağlar. 

Bu makalede veri mühendisleri ve geliştiriciler için aşağıdaki bilgiler sağlanmaktadır:

> [!div class="checklist"]
> * Performans 
> * Esnekliği Kopyala
> * Ağ güvenliği
> * Üst düzey çözüm mimarisi 
> * En iyi uygulama uygulamaları  

## <a name="performance"></a>Performans

Azure Data Factory, çeşitli düzeylerde paralellik sağlayan sunucusuz bir mimari sağlar. Geliştirici iseniz bu, ortamınız için veri taşıma aktarım hızını en üst düzeye çıkarmak üzere hem ağ hem de veritabanı bant genişliğini tam olarak kullanmak için işlem hatları oluşturabileceğiniz anlamına gelir.

![Performans diyagramı](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Yukarıdaki diyagram aşağıdaki gibi yorumlanabilir:

- Tek bir kopyalama etkinliği, ölçeklenebilir işlem kaynaklarından yararlanabilir. Azure Integration Runtime kullandığınızda, her kopyalama etkinliği için sunucusuz bir şekilde [en fazla 256 DIUs](./copy-activity-performance.md#data-integration-units) belirtebilirsiniz. Şirket içinde barındırılan bir tümleştirme çalışma zamanı (Şirket içinde barındırılan IR) sayesinde, makineyi el ile ölçeklendirebilir veya birden fazla makineye ([en fazla dört düğüme](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ölçeklendirebilirsiniz ve tek bir kopyalama etkinliği bölümünü tüm düğümlerde dağıtır. 

- Tek bir kopyalama etkinliği, birden çok iş parçacığı kullanarak veri deposundan okur ve yazar. 

- Azure Data Factory denetim akışı, paralel olarak birden çok kopyalama etkinliği başlatabilir. Örneğin, [her döngü için](./control-flow-for-each-activity.md)bir kullanarak bunları başlatabilir. 

Daha fazla bilgi için bkz. [kopyalama etkinliği performans ve ölçeklenebilirlik Kılavuzu](./copy-activity-performance.md).

## <a name="resilience"></a>Esnekliği

Tek bir kopyalama etkinliği çalıştırmasında Azure Data Factory, bir yerleşik yeniden deneme mekanizmasına sahiptir ve bu da veri depolarında veya temel ağdaki belirli bir geçici başarısızlık düzeyini işlemesini sağlar.

Azure Data Factory kopyalama etkinliği ile kaynak ve havuz veri depoları arasında veri kopyaladığınızda, uyumsuz satırları işlemek için iki yol vardır. Kopyalama etkinliğini durdurabilir ve devreder veya uyumsuz veri satırlarını atlayarak verilerin geri kalanını kopyalamaya devam edebilirsiniz. Ayrıca, hatanın nedenini öğrenmek için Azure Blob depolamada uyumsuz satırları günlüğe kaydedebilir veya Azure Data Lake Store, veri kaynağındaki verileri giderebilir ve kopyalama etkinliğini yeniden deneyebilirsiniz.

## <a name="network-security"></a>Ağ güvenliği 

Azure Data Factory, varsayılan olarak, şirket içi Netezza sunucusundan verileri bir Azure depolama hesabına veya Azure SYNAPSE Analytics veritabanına aktarır ve Köprü Metni Aktarım Protokolü güvenli (HTTPS) üzerinden şifreli bir bağlantı kullanın. HTTPS, aktarım sırasında veri şifrelemesi sağlar ve gizlice dinleme ve ortadaki adam saldırıları önler.

Alternatif olarak, verilerin genel İnternet üzerinden aktarılmasını istemiyorsanız, verileri Azure Express Route aracılığıyla özel bir eşleme bağlantısı üzerinden aktararak daha yüksek güvenlik elde etmenize yardımcı olabilirsiniz. 

Sonraki bölümde daha yüksek güvenlik elde etme anlatılmaktadır.

## <a name="solution-architecture"></a>Çözüm mimarisi

Bu bölüm, verilerinizi geçirmenin iki yolunu açıklamaktadır.

### <a name="migrate-data-over-the-public-internet"></a>Verileri genel İnternet üzerinden geçirme

![Verileri genel İnternet üzerinden geçirme](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Yukarıdaki diyagram aşağıdaki gibi yorumlanabilir:

- Bu mimaride, genel İnternet üzerinden HTTPS kullanarak verileri güvenli bir şekilde aktarırsınız.

- Bu mimariye ulaşmak için, kurumsal güvenlik duvarının arkasındaki bir Windows makinesine Azure Data Factory tümleştirme çalışma zamanı (kendiliğinden konak) yüklemeniz gerekir. Bu tümleştirme çalışma zamanının Netezza sunucusuna doğrudan erişmesini sağlayın. Ağınızı tam olarak kullanabilmek ve veri kopyalamak için bant genişliğini kullanmak için, makinenizi el ile ölçeklendirebilir veya birden çok makineye dönüştürebilirsiniz.

- Bu mimariyi kullanarak hem ilk anlık görüntü verilerini hem de Delta verilerini geçirebilirsiniz.

### <a name="migrate-data-over-a-private-network"></a>Özel ağ üzerinden veri geçirme 

![Özel ağ üzerinden veri geçirme](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Yukarıdaki diyagram aşağıdaki gibi yorumlanabilir:

- Bu mimaride, Azure Express Route aracılığıyla özel bir eşleme bağlantısı üzerinden veri geçirmenize ve veriler hiçbir şekilde genel İnternet üzerinden geçirilmez. 

- Bu mimariye ulaşmak için, Azure sanal ağınız içindeki bir Windows sanal makinesine (VM) Azure Data Factory tümleştirme çalışma zamanı (kendiliğinden konak) yüklemeniz gerekir. Ağınızı tamamen kullanmak ve veri kopyalamak için bant genişliği depolamayı sağlamak üzere, sanal makinelerinizi el ile ölçeklendirebilir veya birden çok VM 'ye ayırabilirsiniz.

- Bu mimariyi kullanarak hem ilk anlık görüntü verilerini hem de Delta verilerini geçirebilirsiniz.

## <a name="implement-best-practices"></a>En iyi uygulamaları uygulama 

### <a name="manage-authentication-and-credentials"></a>Kimlik doğrulama ve kimlik bilgilerini yönetme 

- Netezza kimlik doğrulaması yapmak için [bağlantı dizesi aracılığıyla ODBC kimlik doğrulamasını](./connector-netezza.md#linked-service-properties)kullanabilirsiniz. 

- Azure Blob depolamada kimlik doğrulaması yapmak için: 

   - [Azure kaynakları için yönetilen kimliklerin](./connector-azure-blob-storage.md#managed-identity)kullanılması önemle önerilir. Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen Azure Data Factory kimliğin üzerine inşa, Yönetilen kimlikler, bağlantılı hizmet tanımında kimlik bilgilerini sağlamak zorunda kalmadan işlem hatlarını yapılandırmanıza olanak tanır.  

   - Alternatif olarak, [hizmet sorumlusu](./connector-azure-blob-storage.md#service-principal-authentication), [paylaşılan erişim imzası](./connector-azure-blob-storage.md#shared-access-signature-authentication)veya [depolama hesabı anahtarı](./connector-azure-blob-storage.md#account-key-authentication)kullanarak Azure Blob depolama alanında kimlik doğrulaması yapabilirsiniz. 

- Azure Data Lake Storage 2. doğrulamak için: 

   - [Azure kaynakları için yönetilen kimliklerin](./connector-azure-data-lake-storage.md#managed-identity)kullanılması önemle önerilir.
   
   - [Hizmet sorumlusu](./connector-azure-data-lake-storage.md#service-principal-authentication) veya bir [depolama hesabı anahtarı](./connector-azure-data-lake-storage.md#account-key-authentication)da kullanabilirsiniz. 

- Azure SYNAPSE Analytics 'te kimlik doğrulaması yapmak için:

   - [Azure kaynakları için yönetilen kimliklerin](./connector-azure-sql-data-warehouse.md#managed-identity)kullanılması önemle önerilir.
   
   - [Hizmet sorumlusu](./connector-azure-sql-data-warehouse.md#service-principal-authentication) veya [SQL kimlik doğrulaması](./connector-azure-sql-data-warehouse.md#sql-authentication)da kullanabilirsiniz.

- Azure kaynakları için Yönetilen kimlikler kullanmadığınız durumlarda, Azure Data Factory bağlı hizmetleri değiştirmeye gerek kalmadan anahtarları merkezi olarak yönetmeyi ve döndürmeyi kolaylaştırmak için [Azure Key Vault kimlik bilgilerini depolamayı](./store-credentials-in-key-vault.md) kesinlikle öneririz. Bu Ayrıca, [CI/CD için en iyi uygulamalardan](./continuous-integration-deployment.md#best-practices-for-cicd)biridir. 

### <a name="migrate-initial-snapshot-data"></a>İlk anlık görüntü verilerini geçirme 

Küçük tablolar (yani, 100 GB 'tan daha az bir birimi olan veya iki saat içinde Azure 'a geçirilebilecek tablolar) için her bir kopyalama işi tablo başına veri yükleme yapabilirsiniz. Daha fazla verimlilik için, aynı anda ayrı tabloları yüklemek üzere birden çok Azure Data Factory kopyalama işi çalıştırabilirsiniz. 

Her kopyalama işinde, paralel sorgular çalıştırmak ve verileri bölümlere göre kopyalamak için aşağıdaki veri bölümü seçeneklerinden biriyle [ `parallelCopies` özellik ayarını](./copy-activity-performance.md#parallel-copy) kullanarak, bazı paralellik düzeyine erişebilirsiniz:

- Daha fazla verimlilik elde etmek için bir veri diliminden başlamanız önerilir.  `parallelCopies`Ayardaki değerin, Netezza sunucusundaki tablonuzdaki veri dilimi bölümlerinin toplam sayısından küçük olduğundan emin olun.  

- Her bir veri dilimi bölümünün hacmi hala büyükse (örneğin, 10 GB veya üzeri), dinamik bir Aralık bölümüne geçiş yapmanız önerilir. Bu seçenek, bölüm sütunu, üst sınır ve alt sınır ile her bölümün birim sayısını ve bölüm sayısını tanımlamaya yönelik daha fazla esneklik sağlar.

Daha büyük tablolar (yani, 100 GB veya daha büyük bir birimi olan veya iki saat içinde Azure 'a *geçirilemeyen tablolar* ) için, verileri özel sorgu ile bölümleyip her bir kopya işi tek seferde bir bölüm kopyalaması yapmanızı öneririz. Daha iyi işleme için birden çok Azure Data Factory kopyalama işini eşzamanlı olarak çalıştırabilirsiniz. Özel sorgu tarafından bir bölüm yüklemenin her bir kopyalama işi hedefi için, veri dilimi veya Dinamik Aralık aracılığıyla paralellik sağlayarak aktarım hızını artırabilirsiniz. 

Bir ağ veya veri deposu geçici sorunu nedeniyle herhangi bir kopyalama işi başarısız olursa, tablodaki belirli bir bölümü yeniden yüklemek için başarısız kopyalama işini yeniden çalıştırabilirsiniz. Diğer bölümleri yükleyen diğer kopyalama işleri etkilenmez.

Verileri bir Azure SYNAPSE Analytics veritabanına yüklediğinizde, hazırlama sırasında Azure Blob depolama ile kopyalama işi içinde PolyBase 'i etkinleştirmenizi öneririz.

### <a name="migrate-delta-data"></a>Delta verilerini geçirme 

Tabloınızdan yeni veya güncelleştirilmiş satırları belirlemek için, şema içindeki bir zaman damgası sütunu veya bir artırma anahtarı kullanın. Ardından, en son değeri bir dış tabloda yüksek bir eşik olarak saklayabilir ve sonra verileri bir dahaki sefer yüklediğinizde Delta verilerini filtrelemek için kullanabilirsiniz. 

Her tablo, yeni veya güncelleştirilmiş satırlarını tanımlamak için farklı bir filigran sütunu kullanabilir. Bir dış denetim tablosu oluşturmanızı öneririz. Tabloda, her satır Netezza sunucusundaki belirli bir filigran sütun adı ve yüksek eşik değeri ile bir tabloyu temsil eder. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı yapılandırma

Netezza sunucusundan Azure 'a veri geçiriyorsanız, sunucunun şirket güvenlik duvarınızın arkasında veya bir sanal ağ ortamında şirket içinde olup olmadığı, verileri taşımak için kullanılan altyapı olan bir Windows makinesine veya VM 'ye kendi kendine barındırılan bir IR yüklemeniz gerekir. Şirket içinde barındırılan IR 'yi yüklerken aşağıdaki yaklaşımı öneririz:

- Her bir Windows makinesi veya VM için, 32 vCPU ve 128 GB bellek yapılandırması ile başlayın. Veri geçişi sırasında IR makinesinin CPU ve bellek kullanımının izlenmesini, daha iyi performans için makineye daha fazla ölçeklendirme yapılıp yapılmayacağını veya maliyeti kazanmak için makinenin ölçeğini ölçeklendirmeniz gerekip gerekmediğini görmek için izleyebilirsiniz.

- Ayrıca, tek bir şirket içinde barındırılan bir IR ile en fazla dört düğüm ilişkilendirerek ölçeği genişletebilirsiniz. Şirket içinde barındırılan bir IR 'ye karşı çalışan tek bir kopyalama işi, verileri paralel olarak kopyalamak için otomatik olarak tüm VM düğümlerini uygular. Yüksek kullanılabilirlik için, veri geçişi sırasında tek bir hata noktası oluşmasını önlemek için dört VM düğümü ile başlayın.

### <a name="limit-your-partitions"></a>Bölümlerinizi sınırlayın

En iyi uygulama olarak, temsili bir örnek veri kümesiyle bir kavram kanıtı (POC), her kopyalama etkinliği için uygun bölüm boyutunu belirleyebilmeniz için Her bir bölümü Azure 'a iki saat içinde yüklemeniz önerilir.  

Bir tabloyu kopyalamak için tek ve şirket içinde barındırılan bir IR makinesiyle tek bir kopyalama etkinliği ile başlayın. `parallelCopies`Tablodaki veri dilimi bölümlerinin sayısına göre ayarı kademeli olarak artırın. Tüm tablonun, kopyalama işinden kaynaklanan aktarım hızına bağlı olarak iki saat içinde Azure 'a yüklenip yüklenemeyeceğini öğrenin. 

Bu, iki saat içinde Azure 'a yüklenemediğinde ve şirket içinde barındırılan IR düğümünün ve veri deposunun kapasitesi tam olarak kullanılmazsa, ağınızın sınırına veya veri depolarının bant genişliği sınırına ulaşana kadar eşzamanlı kopyalama etkinliklerinin sayısını kademeli olarak artırın. 

Şirket içinde barındırılan IR makinesinde CPU ve bellek kullanımını izlemeye devam edin ve CPU ve belleğin tam olarak kullanıldığını gördüğünüzde makinenin ölçeğini ölçeklendirmeye veya birden çok makineye ölçeğe hazır olun. 

Azure Data Factory kopyalama etkinliği tarafından bildirilen azaltma hatalarıyla karşılaşdığınızda, Azure Data Factory eşzamanlılık veya ayarı azaltın ya da `parallelCopies` Ağ ve veri depolarının bant genişliği veya g/ç işlemleri/sn (IOPS) sınırlarını artırmayı düşünün. 


### <a name="estimate-your-pricing"></a>Fiyatlandırmanızı tahmin etme 

Şirket içi Netezza sunucusundan bir Azure SYNAPSE Analytics veritabanına veri geçirmek için oluşturulan aşağıdaki işlem hattını göz önünde bulundurun:

![Fiyatlandırma işlem hattı](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Aşağıdaki deyimlerin doğru olduğunu varsayalım: 

- Toplam veri hacmi 50 terabayta (TB) sahiptir. 

- İlk çözüm mimarisini kullanarak verileri geçiriyoruz (Netezza sunucusu şirket içi ve güvenlik duvarının arkasında).

- 50-TB birimi 500 bölüme bölünmüştür ve her kopyalama etkinliği bir bölüm taşıdır.

- Her kopyalama etkinliği, dört makineye karşı şirket içinde barındırılan bir IR ile yapılandırılır ve saniyede 20 megabayta (MB/sn) bir işleme ulaşır. (Kopyalama etkinliği içinde, `parallelCopies` 4 olarak ayarlanır ve tablodaki verileri yüklemeye yönelik her iş parçacığı 5 MB/sn 'lik bir işleme erişir.)

- ForEach eşzamanlılık 3 olarak ayarlanır ve toplam verimlilik 60 MB/sn 'dir.

- Toplamda, geçişin tamamlandığı 243 saat sürer.

Önceki varsayımlar temelinde, tahmini fiyat aşağıda verilmiştir: 

![Fiyatlandırma tablosu](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Yukarıdaki tabloda gösterilen fiyatlandırma kuramsal olarak belirlenir. Gerçek fiyatlandırağınız, ortamınızdaki gerçek işleme göre değişir. Windows makinenin fiyatı (Şirket içinde barındırılan IR yüklü) dahil değildir. 

### <a name="additional-references"></a>Ek başvurular

Daha fazla bilgi için aşağıdaki makalelere ve kılavuzlara bakın:

- [Azure Data Factory kullanarak şirket içi ilişkisel veri ambarı veritabanından Azure 'a veri geçirme](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza Bağlayıcısı](./connector-netezza.md)
- [ODBC bağlayıcısı](./connector-odbc.md)
- [Azure Blob depolama Bağlayıcısı](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage 2. Nesil bağlayıcısı](./connector-azure-data-lake-storage.md)
- [Azure SYNAPSE Analytics Bağlayıcısı](./connector-azure-sql-data-warehouse.md)
- [Kopyalama etkinliği performans ayarlama Kılavuzu](./copy-activity-performance.md)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](./create-self-hosted-integration-runtime.md)
- [Şirket içinde barındırılan tümleştirme çalışma zamanı HA ve ölçeklenebilirliği](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Veri taşırken güvenlikle ilgili dikkat edilmesi gerekenler](./data-movement-security-considerations.md)
- [Kimlik bilgilerini Azure Key Vault’ta depolama](./store-credentials-in-key-vault.md)
- [Verileri bir tablodan artımlı olarak kopyalama](./tutorial-incremental-copy-portal.md)
- [Verileri birden çok tablodan artımlı olarak kopyalama](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Azure Data Factory fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory kullanarak birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)