---
title: ONNX ile tahmine dayalı hale getirin ve bu tahminleri yapın
titleSuffix: SQL machine learning
description: Bir modeli eğitme, ONNX 'e dönüştürme, Azure SQL Edge veya Azure SQL yönetilen örneği 'ne (Önizleme) dağıtma hakkında bilgi edinin ve ardından karşıya yüklenen ONNX modelini kullanarak verileri yerel olarak tahmın edin.
keywords: SQL Edge dağıtma
ms.prod: sql
ms.technology: machine-learning
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.date: 10/13/2020
ms.openlocfilehash: 755111b2fc48ec119c30d09f2e51b9db6c333848
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653219"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-and-sql-machine-learning"></a>ONNX modeliyle ve SQL Machine Learning ile tahmine dayalı hale getirin

Bu hızlı başlangıçta, bir modeli eğitme, ONNX 'e dönüştürme, [Azure SQL Edge](onnx-overview.md) veya [Azure SQL yönetilen örneği 'ne (Önizleme)](../azure-sql/managed-instance/machine-learning-services-overview.md)dağıtma hakkında bilgi edineceksiniz ve ardından karşıya yüklenen onnx MODELINI kullanarak verileri yerel tahmin etmeye nasıl çalıştıracağınızı öğreneceksiniz.

Bu hızlı başlangıç, **scikit-** ' i öğrenin ve [Boston Muhafazası veri kümesini](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)kullanır.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure SQL Edge kullanıyorsanız ve bir Azure SQL Edge modülü dağıtmadıysanız, [Azure Portal kullanarak SQL Edge dağıtma](deploy-portal.md)adımlarını izleyin.

* [Azure Data Studio](/sql/azure-data-studio/download)'i yükler.

* Bu hızlı başlangıç için gereken Python paketlerini yükler:

  1. Python 3 çekirdeğine bağlı [Yeni Not defteri](/sql/azure-data-studio/sql-notebooks) 'ni açın. 
  1. **Paketleri Yönet** 'e tıklayın
  1. **Yüklü** sekmesinde, yüklü paketler listesinde aşağıdaki Python paketlerini arayın. Bu paketlerden herhangi biri yüklü değilse, **Yeni Ekle** sekmesini seçin, paketi arayın ve **yükleme**' ye tıklayın.
     - **scikit-learn**
     - **sayısal tuş takımı**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* Aşağıdaki her komut bölümü için, Azure Data Studio not defterindeki bir hücreye girin ve hücreyi çalıştırın.

## <a name="train-a-pipeline"></a>İşlem hattını eğitme

Veri kümesini, bir evin ortanca değerini tahmin etmek için özellikleri kullanacak şekilde ayırın.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Çıkış**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Bir işlem hattı oluşturun ve bu model için bir işlem hattı oluşturun. Diğer gerileme modellerini de kullanabilirsiniz.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Modelin doğruluğunu kontrol edin ve ardından R2 Puanını ve ortalama kare hata hatasını hesaplayın.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Çıkış**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Modeli ONNX 'e Dönüştür

Veri türlerini desteklenen SQL veri türlerine dönüştürün. Bu dönüştürme diğer veri çerçeveleri için de gerekecektir.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Kullanarak `skl2onnx` , bir doğrsıya dönüştürme modelini ONNX biçimine dönüştürün ve yerel olarak kaydedin.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train), final_types=[('variable1',FloatTensorType([1,1]))])
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>ONNX modelini test etme

Modeli ONNX biçimine dönüştürdükten sonra, performans düşüklüğüne düşmek için modele puan gösterin.

> [!NOTE]
> ONNX çalışma zamanı Double değerleri yerine float kullanır, bu nedenle küçük tutarsızlıklar mümkündür.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Çıkış**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>ONNX modelini ekleme

Modeli, bir veritabanındaki bir tabloda Azure SQL Edge veya Azure SQL yönetilen örneği 'nde depolayın `models` `onnx` . Bağlantı dizesinde **sunucu adresini**, **Kullanıcı adını** ve **parolayı** belirtin.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Verileri yükleme

Verileri SQL 'e yükleyin.

İlk olarak, Boston Muhafazası veri kümesinin alt kümelerini depolamak için iki tablo, **özellik** ve **hedef** oluşturun.

* **Özellikler** , target, ortanca değerini tahmin etmek için kullanılan tüm verileri içerir. 
* **Hedef** , veri kümesindeki her bir kayıt için ortanca değerini içerir. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Son olarak, `sqlalchemy` `x_train` ve `y_train` sırasıyla tablolara ve Pandas dataframe 'leri eklemek için `features` kullanın `target` . 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Artık verileri veritabanında görüntüleyebilirsiniz.

## <a name="run-predict-using-the-onnx-model"></a>ONNX modelini kullanarak ÖNTAHMIN Çalıştır

SQL 'deki modeliyle, karşıya yüklenen ONNX modelini kullanarak verileri yerel olarak tahmın edin.

> [!NOTE]
> Kalan hücreyi çalıştırmak için Not defteri çekirdeğini SQL olarak değiştirin.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Sonraki Adımlar

* [SQL Edge 'de ONNX ile Machine Learning ve AI](onnx-overview.md)
* [Azure SQL yönetilen örneği 'nde Machine Learning Services (Önizleme)](../azure-sql/managed-instance/machine-learning-services-overview.md)
