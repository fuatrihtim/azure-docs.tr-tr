---
title: Machine Learning veri kümeleriyle eğitme
titleSuffix: Azure Machine Learning
description: Verilerinizi, Azure Machine Learning veri kümeleriyle model eğitimi için yerel veya uzak işlem için nasıl kullanılabilir hale yapacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 8b984a17c8c10c3dff7c57b7d0223ba8b4197012
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640115"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning veri kümeleriyle modelleri eğitme 

Bu makalede makine öğrenimi modellerini eğitmek için [Azure Machine Learning veri kümeleriyle](/python/api/azureml-core/azureml.core.dataset%28class%29) nasıl çalışacağınızı öğreneceksiniz.  Bağlantı dizeleri veya veri yolları hakkında endişelenmeden, yerel veya uzaktan işlem Hedefinizdeki veri kümelerini kullanabilirsiniz. 

Azure Machine Learning veri kümeleri, [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive) ve [Azure Machine Learning işlem hatları](./how-to-create-machine-learning-pipelines.md)gibi Azure Machine Learning eğitim işlevleriyle sorunsuz bir tümleştirme sağlar.

Verilerinizi model eğitimi için kullanılabilir hale getirmek için hazır değilseniz, ancak verilerinizi veri araştırması için Not defterinize yüklemek istiyorsanız, bkz. veri [kümenizdeki verileri keşfetme](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Önkoşullar

Veri kümeleri oluşturup eğitmeniz için şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).

* Paketi içeren [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install) (>= 1.13.0) `azureml-datasets` .

> [!Note]
> Bazı veri kümesi sınıflarının [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) paketine bağımlılıkları vardır. Linux kullanıcıları için, bu sınıflar yalnızca şu dağıtımlarda desteklenir: Red Hat Enterprise Linux, Ubuntu, Fedora ve CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Machine Learning eğitim betiklerine veri kümelerini kullanma

Henüz bir veri kümesi olarak kayıtlı olmayan yapılandırılmış veriler varsa, bir TabularDataset oluşturun ve bunu yerel veya uzaktan denemenize yönelik eğitim betiğinizdeki doğrudan kullanın.

Bu örnekte, kayıtlı olmayan bir [Tabulardataset](/python/api/azureml-core/azureml.data.tabulardataset) oluşturun ve bunu eğitim için ScriptRunConfig nesnesinde bir betik bağımsız değişkeni olarak belirtirsiniz. Bu TabularDataset 'i çalışma alanınızdaki diğer denemeleri birlikte yeniden kullanmak istiyorsanız, bkz. [veri kümelerini çalışma alanınıza kaydetme](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>TabularDataset oluşturma

Aşağıdaki kod, bir Web URL 'sinden kayıtlı olmayan bir TabularDataset oluşturur.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset nesneleri, Not defterinize çıkmadan tanıdık veri hazırlama ve eğitim kitaplıklarıyla çalışabilmeniz için TabularDataset 'teki verileri bir Pandas veya Spark veri çerçevesine yükleme olanağı sağlar.

### <a name="access-dataset-in-training-script"></a>Eğitim betiğinde veri kümesine erişme

Aşağıdaki kod, eğitim çalıştırmanızı yapılandırırken belirlediğiniz bir betik bağımsız değişkenini yapılandırır `--input-data` (sonraki bölüme bakın). Tablosal veri kümesi bağımsız değişken değeri olarak geçirildiğinde, Azure ML, veri kümesinin KIMLIĞINI çözer ve daha sonra eğitim betiğinizdeki veri kümesine erişmek için kullanabilirsiniz (betiğinizdeki veri kümesinin adını veya KIMLIĞINI kodlamadan bağımsız olarak kodlayın). Daha sonra, [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) eğitim öncesinde daha fazla veri araştırması ve hazırlığı için bu veri kümesini bir Pandas dataframe 'e yüklemek için yöntemini kullanır.

> [!Note]
> Özgün veri kaynağınız NaN, boş dizeler veya boş değerler içeriyorsa, kullandığınızda `to_pandas_dataframe()` Bu değerler *null* değer olarak değişir.

Hazırlanan verileri, bellek içi Pandas dataframe 'ten yeni bir veri kümesine yüklemeniz gerekiyorsa, verileri bir Parquet gibi yerel bir dosyaya yazın ve bu dosyadan yeni bir veri kümesi oluşturun. [Veri kümeleri oluşturma](how-to-create-register-datasets.md)hakkında daha fazla bilgi edinin.

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Eğitim çalıştırmasını yapılandırma

Eğitim çalıştırmasını yapılandırmak ve göndermek için bir [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) nesnesi kullanılır.

Bu kod, şunu belirten bir ScriptRunConfig nesnesi oluşturur `src`

* Betikleriniz için bir komut dosyası dizini. Bu dizindeki dosyaların tümü yürütülmek üzere küme düğümlerine yüklenir.
* Eğitim betiği, *train_titanic. Kopyala*.
* Eğitim için giriş veri kümesi, `titanic_ds` komut dosyası bağımsız değişkeni olarak. Azure ML, betiğe geçirildiğinde bu veri kümesinin karşılık gelen KIMLIĞINE çözüm olarak çözer.
* Çalıştırma için işlem hedefi.
* Çalıştırmanın ortamı.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Dosyaları uzaktan işlem hedeflerine bağlama

Yapılandırılmamış verileriniz varsa, bir [dosya veri kümesi](/python/api/azureml-core/azureml.data.filedataset) oluşturun ve veri dosyalarınızı, eğitim için uzaktan işlem hedefi için kullanılabilir hale getirmek üzere bağlayın veya indirin. Uzaktan Eğitim denemeleri için [bağlama ve indirme](#mount-vs-download) bilgilerini ne zaman kullanacağınızı öğrenin. 

Aşağıdaki örnek bir dosya veri kümesi oluşturur ve veri kümesini, eğitim betiğine bir bağımsız değişken olarak geçirerek işlem hedefine bağlar. 

> [!Note]
> Özel bir Docker temel görüntüsü kullanıyorsanız, `apt-get install -y fuse` veri kümesi bağlama 'nın çalışması için bir bağımlılık olarak kullanarak sigortası yüklemeniz gerekir. [Özel bir yapı görüntüsü](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)oluşturmayı öğrenin.

### <a name="create-a-filedataset"></a>Dosya veri kümesi oluşturma

Aşağıdaki örnek, Web URL 'lerinden kaydedilmemiş bir dosya veri kümesi oluşturur. Diğer kaynaklardan [veri kümeleri oluşturma](how-to-create-register-datasets.md) hakkında daha fazla bilgi edinin.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>Eğitim çalıştırmasını yapılandırma

Oluşturucunun parametresi aracılığıyla bağlanırken veri kümesini bir bağımsız değişken olarak geçirmeyi öneririz `arguments` `ScriptRunConfig` . Bunu yaptığınızda, eğitim betiğinizdeki veri yolunu (takma noktası) bağımsız değişkenler aracılığıyla alacaksınız. Bu şekilde, yerel hata ayıklama ve herhangi bir bulut platformunda uzaktan eğitim için aynı eğitim betiğini kullanacaksınız.

Aşağıdaki örnek ile FileDataset içinde geçen bir ScriptRunConfig oluşturur `arguments` . Çalışmayı gönderdikten sonra, veri kümesi tarafından başvurulan veri dosyaları `mnist_ds` işlem hedefine bağlanır.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>Eğitim betiğinizdeki verileri alma

Aşağıdaki kod, betiğinizdeki verilerin nasıl alınacağını gösterir.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Bağlama vs indirmesi

Azure Blob depolama, Azure dosyaları, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure SQL veritabanı ve PostgreSQL için Azure veritabanı tarafından oluşturulan veri kümelerinde herhangi bir biçimdeki dosyaları bağlama veya indirme işlemi desteklenir. 

**Bir veri kümesini bağladığınızda,** veri kümesinin başvurduğu dosyaları bir dizine (bağlama noktası) ekler ve işlem hedefinde kullanılabilir hale getirin. Bağlama, Azure Machine Learning Işlem, sanal makineler ve HDInsight dahil olmak üzere Linux tabanlı hesaplar için desteklenir. 

Bir veri kümesini **indirdiğinizde** , veri kümesi tarafından başvurulan tüm dosyalar işlem hedefine indirilir. Tüm işlem türleri için indirme desteklenir. 

Betiğinizin veri kümesi tarafından başvurulan tüm dosyaları işliyorsa ve işlem diskiniz tam veri kümesine uyuyorsa, depolama hizmetlerinden veri akışı yükünü ortadan kaldırmak için indirme önerilir. Veri boyutunuz işlem diski boyutunu aşarsa, indirme mümkün değildir. Bu senaryo için, işleme sırasında yalnızca komut dosyası tarafından kullanılan veri dosyaları yüklendiğinden, bağlama yapmanız önerilir.

Aşağıdaki kod, şu `dataset` adreste geçici dizine bağlar: `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Makine öğrenimi betiklerine veri kümeleri al

Kayıtlı veri kümelerine, Azure Machine Learning işlem gibi işlem kümelerinde hem yerel olarak hem de uzaktan erişilebilir. Kayıtlı veri kümenize denemeleri üzerinden erişmek için aşağıdaki kodu kullanarak çalışma alanınıza erişin ve daha önce gönderdiğiniz çalıştırmada kullanılan veri kümesini alın. Varsayılan olarak, [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) sınıfındaki yöntemi, `Dataset` çalışma alanına kayıtlı veri kümesinin en son sürümünü döndürür.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Eğitim sırasında kaynak koduna erişin

Azure Blob depolama, bir Azure dosya paylaşımından daha yüksek işleme hızına sahiptir ve paralel olarak başlatılan çok sayıda işi ölçeklendirecektir. Bu nedenle, çalıştırmaların kaynak kodu dosyalarını aktarmak için blob depolamayı kullanacak şekilde yapılandırılmasını öneririz.

Aşağıdaki kod örneği, kaynak kodu aktarımları için kullanılacak blob veri deposuna ait çalıştırma yapılandırması ' nda belirtir.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Not defteri örnekleri

+ [Veri kümesi Not defterleri](https://aka.ms/dataset-tutorial) bu makaledeki kavramları gösterir ve genişletir.
+ Bkz. ML işlem hatlarında [parametrize veri kümelerini](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)öğrenin.

## <a name="troubleshooting"></a>Sorun giderme

* **DataSet başlatılamadı: bağlama noktasının hazır olması beklenirken zaman aşımı oluştu**: 
  * Herhangi bir giden [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md) kuralına sahip değilseniz ve `azureml-sdk>=1.12.0` `azureml-dataset-runtime` onun bağımlılıklarını, belirli bir alt sürüm için en son olacak şekilde güncelleştirebilir veya bir çalıştırmada kullanıyorsanız, bu düzeltme ile en son düzeltme ekine sahip olması için ortamınızı yeniden oluşturun. 
  * Kullanıyorsanız `azureml-sdk<1.12.0` , en son sürüme yükseltin.
  * Giden NSG kurallarınız varsa, hizmet etiketi için tüm trafiğe izin veren bir giden kuralı olduğundan emin olun `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Aşırı yüklenmiş AzureFile depolama

Bir hata alırsanız `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , aşağıdaki geçici çözümleri uygulayın.

Veri aktarımı gibi diğer iş yükleri için dosya paylaşma 'yı kullanıyorsanız, dosya paylaşımının çalıştırmaları için kullanılabilmesi için blob 'ları kullanmak, bu nedenle söz konusu çalışma. İş yükünü iki farklı çalışma alanı arasında da bölebilirsiniz.

### <a name="passing-data-as-input"></a>Verileri giriş olarak geçirme

*  **TypeError: Fılenotfound: böyle bir dosya veya dizin yok**: sağladığınız dosya yolu dosyanın bulunduğu yere değilse bu hata oluşur. Dosyaya başvurduğunuzdan emin olmanız gerekir. bu şekilde, veri kümenizi işlem Hedefinizdeki bağladığınız konum ile tutarlıdır. Belirleyici bir durum sağlamak için, bir veri kümesini bir işlem hedefine bağlamak için soyut yolun kullanılmasını öneririz. Örneğin, aşağıdaki kodda, veri kümesini işlem hedefinin FileSystem kökünün altına bağlamamız gerekir `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Önde gelen eğik çizgi '/' dahil değilseniz, `/mnt/batch/.../tmp/dataset` veri kümesinin bağlanmasını istediğiniz yeri belirtmek için işlem hedefi üzerinde çalışma dizinini (.) ön eki uygulamanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar

* Tabulardataset ile [makine öğrenimi modellerini otomatik olarak eğitme](how-to-auto-train-remote.md) .

* Dosya veri kümeleri ile [görüntü sınıflandırma modellerini eğitme](https://aka.ms/filedataset-samplenotebook) .

* İşlem [hatları kullanarak veri kümeleriyle eğitme](./how-to-create-machine-learning-pipelines.md).
