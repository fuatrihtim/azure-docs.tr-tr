---
title: Apache Spark havuzlarıyla veri denetimi (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure SYNAPSE Analytics ve Azure Machine Learning ile veri denetimi için Apache Spark havuzları eklemeyi ve başlatmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 9ced4da7f71a0499e538e499644d89240611f1ea
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956222"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Veri denetimi (Önizleme) için Apache Spark havuzları (Azure SYNAPSE Analytics tarafından desteklenir) iliştirme

Bu makalede, ölçeklendirmekte olan veriler için [Azure SYNAPSE Analytics](/synapse-analytics/overview-what-is.md) tarafından desteklenen bir Apache Spark havuzunu nasıl ekleyeceğinizi ve başlatacağınızı öğreneceksiniz. 

Bu makale, bir Jupyter not defterindeki adanmış bir Synapse oturumunda veri denetimi görevlerini etkileşimli olarak gerçekleştirmeye yönelik rehberlik içerir. Azure Machine Learning işlem hatlarını kullanmayı tercih ediyorsanız, [Machine Learning işlem hattınızda (Önizleme) Apache Spark (Azure SYNAPSE Analytics tarafından desteklenir) nasıl](how-to-use-synapsesparkstep.md)kullanılacağını öğrenin.

>[!IMPORTANT]
> Azure Machine Learning ve Azure SYNAPSE Analytics tümleştirmesi önizlemededir. Bu makalede sunulan yetenekler, `azureml-synapse` herhangi bir zamanda değişebilir [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) Önizleme özelliklerini içeren paketi de kullanabilir.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning ve Azure SYNAPSE Analytics tümleştirmesi (Önizleme)

Azure Machine Learning (Önizleme) ile Azure SYNAPSE Analytics tümleştirmesi, etkileşimli veri araştırması ve hazırlığı için Azure SYNAPSE tarafından desteklenen bir Apache Spark havuzu eklemenize olanak tanır. Bu tümleştirmeyle, her türlü, makine öğrenimi modellerinizi eğitmek için kullandığınız Python Not defteri içinde ölçeklendirmekte olan veri wrangini için adanmış bir işlem olabilir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md?tabs=python).

* [Azure Portal ' de bir Azure SYNAPSE Analytics çalışma alanı oluşturun](../synapse-analytics/quickstart-create-workspace.md).

* [Azure portal, Web araçları veya SYNAPSE Studio kullanarak Apache Spark havuzu oluşturma](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure Machine Learning işlem örneği](concept-compute-instance.md#create) kullanın. 

* `azureml-synapse`Paketi (Önizleme) aşağıdaki kodla birlikte yüklersiniz:

  ```python
  pip install azureml-synapse
  ```

* [Azure Machine Learning çalışma alanını ve Azure SYNAPSE Analytics çalışma alanını bağlayın](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Mevcut bir bağlı hizmeti al
Veri wrangling için adanmış bir işlem iliştirebilmeniz için önce bir Azure SYNAPSE Analytics çalışma alanına bağlı bir ML çalışma alanınız olması gerekir, buna bağlı hizmet olarak başvurulur. 

Mevcut bir bağlı hizmeti almak ve kullanmak için Azure SYNAPSE Analytics çalışma alanına **Kullanıcı veya katkıda bulunan** izinleri gerekir.

Machine Learning çalışma alanınız ile ilişkili tüm bağlı hizmetleri görüntüleyin. 

```python
LinkedService.list(ws)
```

Bu örnek, mevcut bir bağlı hizmeti,, `synapselink1` çalışma alanından, `ws` yöntemiyle alır [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) .
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>İşlem olarak SYNAPSE Spark havuzunu iliştirme

Bağlı hizmeti aldıktan sonra, veri denetimi görevleriniz için adanmış bir işlem kaynağı olarak bir Synapse Apache Spark havuzu ekleyin. 

Aracılığıyla Apache Spark havuzları ekleyebilirsiniz
* Azure Machine Learning Studio
* [Azure Resource Manager (ARM) şablonları](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 'Sı 

### <a name="attach-a-pool-via-the-studio"></a>Studio aracılığıyla havuz iliştirme
Şu adımları izleyin: 

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.
1. Sol bölmedeki **Yönet** bölümünde **bağlı hizmetler** ' i seçin.
1. SYNAPSE çalışma alanınızı seçin.
1. Sol üstteki **ekli Spark havuzlarını** seçin. 
1. **Ekle**' yi seçin. 
1. Listeden Apache Spark havuzunuzu seçin ve bir ad sağlayın.  
    1. Bu liste, işlem için iliştirilebilecek kullanılabilir SYNAPSE Spark havuzlarını tanımlar. 
    1. Yeni bir Synapse Spark havuzu oluşturmak için bkz [. SYNAPSE Studio ile Apache Spark havuzu oluşturma](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. **Ekle seçili** öğesini seçin. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Python SDK ile havuz iliştirme

Apache Spark havuzunu eklemek için **Python SDK 'sını** de kullanabilirsiniz. 

Takip kodu, 
1. İle SynapseCompute yapılandırır,

   1. `linked_service`Önceki adımda oluşturduğunuz veya aldığınız LinkedService. 
   1. İliştirmek istediğiniz işlem hedefinin türü, `SynapseSpark`
   1. Apache Spark havuzunun adı. Bunun, Azure SYNAPSE Analytics çalışma alanınızdaki mevcut bir Apache Spark havuzuyla eşleşmesi gerekir.
   
1. İle geçirerek bir Machine Learning ComputeTarget oluşturur. 
   1. Kullanmak istediğiniz makine öğrenimi çalışma alanı, `ws`
   1. Azure Machine Learning çalışma alanındaki işlem için başvurmak istediğiniz ad. 
   1. SYNAPSE Işlem bilgilerinizi yapılandırırken belirttiğiniz attach_configuration.
       1. ComputeTarget. Attach () çağrısı zaman uyumsuzdur, bu nedenle çağrı tamamlanana kadar örnek bloklar olur.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Apache Spark havuzunun ekli olduğunu doğrulayın.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Veri hazırlama görevleri için SYNAPSE Spark havuzunu Başlat

Apache Spark havuzuyla veri hazırlığı başlatmak için, Apache Spark havuzu adını belirtin:

> [!IMPORTANT]
> Apache Spark havuzunu kullanmaya devam etmek için, `%synapse` tek satırlık kod ve `%%synapse` birden çok satır için olan veri denetimi görevleriniz genelinde hangi bilgi işlem kaynağını kullanacağınızı belirtmeniz gerekir. 

```python
%synapse start -c SynapseSparkPoolAlias
```

Oturum başladıktan sonra, oturumun meta verilerini kontrol edebilirsiniz.

```python
%synapse meta
```

Apache Spark oturumunuz sırasında kullanmak üzere bir [Azure Machine Learning ortamı](concept-environments.md) belirleyebilirsiniz. Yalnızca ortamda belirtilen Conda bağımlılıkları devreye girer. Docker görüntüsü desteklenmez.

>[!WARNING]
>  Ortam Conda bağımlılıklarında belirtilen Python bağımlılıkları Apache Spark havuzlarda desteklenmez. Şu anda yalnızca sabit Python sürümleri desteklenir. Betiğe ekleyerek Python sürümünüzü kontrol edin  `sys.version_info` .

Aşağıdaki kod, `myenv` `azureml-core` oturum başlamadan önce sürüm 1.20.0 ve sürüm 1.17.0 ' yi yükleyecek ortamı oluşturur `numpy` . Daha sonra bu ortamı Apache Spark Session `start` deyiminize ekleyebilirsiniz.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Apache Spark havuzu ve özel ortamınız ile veri hazırlığı başlatmak için, Apache Spark havuzu adını ve Apache Spark oturumu sırasında hangi ortamı kullanacağınızı belirtin. Ayrıca, abonelik KIMLIĞINIZI, Machine Learning çalışma alanı kaynak grubunu ve makine öğrenimi çalışma alanının adını da sağlayabilirsiniz.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Verileri depolamadan yükle

Apache Spark oturumunuz başladıktan sonra, hazırlamak istediğiniz verileri okuyun. Azure Blob depolama için veri yükleme desteklenir ve 1. ve 2. nesil Azure Data Lake Storage.

Bu depolama hizmetlerinden veri yüklemek için iki yol vardır: 

* Hadoop Dağıtılmış dosya sistemi (I1) yolunu kullanarak verileri depolamadan doğrudan yükleyin.

* Mevcut bir [Azure Machine Learning veri kümesinden](how-to-create-register-datasets.md)verileri okuyun.

Bu depolama hizmetlerine erişmek için **Depolama Blobu veri okuyucusu** izinlerinizin olması gerekir. Verileri bu depolama hizmetlerine geri yazmayı planlıyorsanız, **Depolama Blobu veri katılımcısı** izinlerinizin olması gerekir. [Depolama izinleri ve rolleri hakkında daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Hadoop Dağıtılmış dosya sistemi (IBU) yoluyla veri yükleme

Karşılık gelen bir bu yol ile depolama alanındaki verileri yüklemek ve okumak için, veri erişim kimlik doğrulama kimlik bilgilerinizi hazır olarak kullanabilirsiniz. Bu kimlik bilgileri, depolama türüne göre farklılık gösterir.  

Aşağıdaki kod, bir **Azure Blob depolama** alanından, paylaşılan erişim IMZASı (SAS) belirteci veya erişim anahtarınız Ile bir Spark dataframe 'e nasıl veri okunacağını gösterir. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Aşağıdaki kod, **Azure Data Lake Storage nesil 1 ' den (ADLS Gen 1)** hizmet sorumlusu kimlik bilgilerinizle verilerin nasıl okunacağını gösterir. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Aşağıdaki kod, hizmet sorumlusu kimlik bilgilerinizle **Azure Data Lake Storage oluşturma 2 ' den (ADLS Gen 2)** verilerin nasıl okunacağını gösterir. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Kayıtlı veri kümelerinden verilerde okuma

Ayrıca, çalışma alanınızda var olan bir kayıtlı veri kümesini alabilir ve bunu bir Spark veri çerçevesine dönüştürerek üzerinde veri hazırlığı gerçekleştirebilirsiniz.

Aşağıdaki örnek çalışma alanının kimliğini doğrular, kayıtlı bir TabularDataset alır, `blob_dset` BLOB depolama alanındaki dosyalara başvurur ve bunu bir Spark veri çerçevesine dönüştürür. Veri kümelerinizi Spark veri çerçevesine dönüştürdüğünüzde, `pyspark` veri araştırma ve hazırlık kitaplıklarından yararlanabilirsiniz.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Veri denetimi görevlerini gerçekleştirme

Verilerinizi aldıktan ve araştırdıktan sonra, veri denetimi görevlerini gerçekleştirebilirsiniz.

Aşağıdaki kod, önceki bölümde bulunan Age örneğine genişletilir ve Spark dataframe içindeki verileri, `df` **yaş** tarafından belirtilen diğer ve sütun ve gruplara göre filtreler 

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Verileri depolama alanına kaydetme ve Spark oturumunu durdurma

Veri araştırma ve hazırlama işlemi tamamlandıktan sonra, hazırlanan verilerinizi daha sonra Azure 'daki depolama hesabınızda saklayın.

Aşağıdaki örnekte, hazırlanan veriler Azure Blob depolama alanına geri yazılır ve dizindeki özgün dosyanın üzerine yazılır `Titanic.csv` `training_data` . Depolama alanına geri yazmak için, **Depolama Blobu veri katılımcısı** izinlerinizin olması gerekir. [Depolama izinleri ve rolleri hakkında daha fazla bilgi edinin](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Veri hazırlanmasını tamamlayıp hazırlanan verilerinizi depolamaya kaydettiğinizde, Apache Spark havuzunuzu aşağıdaki komutla kullanmayı durdurun.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Hazırlanan verileri temsil etmek için veri kümesi oluşturma

Hazırlanan verilerinizi model eğitimi için kullanmaya hazır olduğunuzda, depolama alanınızı [Azure Machine Learning bir veri deposu](how-to-access-data.md)ile bağlayın ve [Azure Machine Learning bir veri kümesiyle](how-to-create-register-datasets.md)hangi dosya (ler) kullanmak istediğinizi belirtin.

Aşağıdaki kod örneği,

* Önceden hazırlanan verilerinizi kaydettiğiniz depolama hizmetine bağlanan bir veri deposu oluşturmuş olduğunuzu varsayar.  
* Mevcut veri deposunu, `mydatastore` , çalışma alanından `ws` Al () yöntemiyle alır.
* İçindeki dizininde bulunan hazırlanan veri dosyalarına başvuran bir [dosya veri kümesi](how-to-create-register-datasets.md#filedataset)oluşturur `train_ds` `training_data` `mydatastore` .  
* `input1`Veri kümesinin veri dosyalarını `train_ds` bir işlem hedefi için kullanılabilir hale getirmek üzere daha sonra kullanılabilecek değişkeni oluşturur.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Örnek not defteri

Azure SYNAPSE Analytics ve Azure Machine Learning ile tek bir not defterinden veri hazırlama ve model eğitimi gerçekleştirme hakkında ayrıntılı kod örneği için bu [uçtan uca not defterine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir modeli eğitme](how-to-set-up-training-targets.md).
* [Azure Machine Learning veri kümesiyle eğitme](how-to-train-with-datasets.md)
