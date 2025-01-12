---
title: 'Öğretici: MySQL için Azure veritabanı-Azure Resource Manager şablonu oluşturma'
description: Bu öğreticide, Azure Resource Manager şablonu kullanarak MySQL için Azure veritabanı sunucu dağıtımlarını sağlama ve otomatik hale getirme açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705481"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Öğretici: Azure Resource Manager şablonu kullanarak MySQL için Azure veritabanı sunucusu sağlama

[MySQL Için Azure veritabanı REST API](/rest/api/mysql/) DevOps mühendislerinin Azure 'Daki yönetilen MySQL sunucularının ve veritabanlarının sağlama, yapılandırma ve işlemlerini otomatikleştirmesini ve tümleştirmesini sağlar.  API, MySQL için Azure veritabanı hizmeti için MySQL sunucularının ve veritabanlarının oluşturulmasına, numaralandırılmasına, yönetimine ve silinmesine izin verir.

Azure Resource Manager, altyapıda dağıtım için gereken Azure kaynaklarını bildirmek ve kod kavramı olarak altyapı ile hizalamak için temel REST API yararlanır. Şablon, Azure Kaynak adı, SKU, ağ, güvenlik duvarı yapılandırması ve ayarlarını bir kez oluşturup birden çok kez kullanılmasını sağlayacak şekilde parametreleştirir.  Azure Resource Manager şablonlar, [Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) veya [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI)kullanılarak kolayca oluşturulabilir. Bunlar, DevOps CI/CD ardışık düzeninde tümleştirilebilen uygulama paketleme, standartlaştırma ve Dağıtım Otomasyonu 'nu etkinleştirir.  Örneğin, bir Web uygulamasını MySQL için Azure veritabanı arka ucu ile hızlıca dağıtmak istiyorsanız, GitHub galerisinden bu [hızlı başlangıç şablonunu](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) kullanarak uçtan uca dağıtım gerçekleştirebilirsiniz.

Bu öğreticide, aşağıdakileri nasıl yapacağınızı öğrenmek için Azure Resource Manager şablonu ve diğer yardımcı programları kullanacaksınız:

> [!div class="checklist"]
> * Azure Resource Manager şablonunu kullanarak sanal ağ hizmeti uç noktası ile MySQL için Azure veritabanı sunucusu oluşturma
> * Veritabanı oluşturmak için [mysql komut satırı aracı](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Azure Resource Manager şablonunu kullanarak sanal ağ hizmeti uç noktası ile MySQL için Azure veritabanı sunucusu oluşturma

Bir MySQL için Azure veritabanı sunucusuna yönelik JSON şablonu başvurusunu almak için [Microsoft. Dbformyısql sunucuları](/azure/templates/microsoft.dbformysql/servers) şablon başvurusu ' na gidin. Aşağıda, VNet hizmeti uç noktası ile MySQL için Azure veritabanı 'nı çalıştıran yeni bir sunucu oluşturmak için kullanılabilecek örnek JSON şablonu verilmiştir.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
Bu istekte, özelleştirilmesi gereken değerler şunlardır:
+   `name` -MySQL sunucunuzun adını belirtin (etki alanı adı olmadan).
+   `location` -MySQL sunucunuz için geçerli bir Azure veri merkezi bölgesi belirtin. Örneğin, westus2.
+   `properties/version` -Dağıtılacak MySQL sunucu sürümünü belirtin. Örneğin, 5,6 veya 5,7.
+   `properties/administratorLogin` -Sunucu için MySQL yönetici oturum açma bilgilerini belirtin. Yönetici oturum açma adı azure_superuser, admin, administrator, root, guest veya public olamaz.
+   `properties/administratorLoginPassword` -Yukarıda belirtilen MySQL yönetici kullanıcısının parolasını belirtin.
+   `properties/sslEnforcement` -SslEnforcement etkinleştirmek/devre dışı bırakmak için etkin/devre dışı öğesini belirtin.
+   `storageProfile/storageMB` -Sunucu için gereken en fazla sağlanan depolama boyutunu megabayt cinsinden belirtin. Örneğin, 5120.
+   `storageProfile/backupRetentionDays` -İstenen yedekleme saklama süresini gün olarak belirtin. Örneğin, 7. 
+   `storageProfile/geoRedundantBackup` -Coğrafi DR gereksinimlerine bağlı olarak etkin/devre dışı olarak belirleyin.
+   `sku/tier` -Dağıtım için temel, Generalamacını veya Memoryoptıılanmış katmanı belirtin.
+   `sku/capacity` -VCore kapasitesini belirtin. Olası değerler arasında 2, 4, 8, 16, 32 veya 64 vardır.
+   `sku/family` -Sunucu dağıtımı için donanım oluşturmayı seçmek üzere 5. nesil belirtin.
+   `sku/name` -TierPrefix_family_capacity belirtin. Örneğin B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Her bölge ve katman için geçerli değerleri anlamak üzere [fiyatlandırma katmanları](./concepts-pricing-tiers.md) belgelerine bakın.
+   `resources/properties/virtualNetworkSubnetId` -Azure MySQL sunucusunun yerleştirilmesi gereken VNet 'teki alt ağın Azure tanıtıcısını belirtin. 
+   `tags(optional)` -İsteğe bağlı Etiketler, faturalandırmayla ilgili kaynakları kategorize etmek için kullanacağınız anahtar değer çiftleridir.

Kuruluşunuz için Azure veritabanı dağıtımlarını otomatik hale getirmek üzere bir Azure Resource Manager şablonu oluşturmak istiyorsanız, ilk olarak Azure hızlı başlangıç GitHub galerisinde örnek [Azure Resource Manager şablonundan](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) başlayıp üzerine inşa edilecek. 

Azure Resource Manager şablonlarına yeni başladıysanız ve denemek istiyorsanız aşağıdaki adımları izleyerek başlayabilirsiniz:
+   Örnek [Azure Resource Manager şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) Azure hızlı başlangıç galerisinden kopyalayın veya indirin.  
+   Tercihinize göre parametre değerlerini güncelleştirmek için azuredeploy.parameters.js' u değiştirin ve dosyayı kaydedin. 
+   Aşağıdaki komutları kullanarak Azure MySQL sunucusunu oluşturmak için Azure CLı 'yi kullanın

Bu öğreticide kod bloklarını çalıştırmak için tarayıcıda Azure Cloud Shell kullanabilir ya da kendi bilgisayarınıza Azure CLI yükleyebilirsiniz.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma
Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql kullanarak sunucuya bağlanma
MySQL sunucusu için Azure Veritabanınız ile bağlantı kurmak üzere [mysql komut satırı aracını](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kullanın. Bu örnekte, komut şöyledir:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Boş veritabanı oluşturma
Sunucuya bağlandıktan sonra, boş bir veritabanı oluşturun.
```sql
mysql> CREATE DATABASE mysampledb;
```

İstemde, bağlantıyı bu yeni oluşturulan veritabanına değiştirmek için şu komutu çalıştırın:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Veritabanında tablo oluşturma
Artık MySQL veritabanı için Azure Veritabanına nasıl bağlanacağınızı bildiğinize göre bazı temel görevleri tamamlayın.

İlk olarak, bir tablo oluşturun ve bu tabloya bazı veriler yükleyin. Envanter bilgilerini depolayan bir tablo oluşturalım.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Tablolara veri yükleme
Bir tablonuz olduğuna göre içine bazı veriler ekleyin. Açık komut istemi penceresinde, birkaç veri satırı eklemek için şu sorguyu çalıştırın.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Daha önce oluşturduğunuz tabloda artık iki satırlık örnek verileriniz var.

## <a name="query-and-update-the-data-in-the-tables"></a>Tablolardaki verileri sorgulama ve güncelleştirme
Veritabanı tablosundan bilgi almak için şu sorguyu yürütün.
```sql
SELECT * FROM inventory;
```

Ayrıca tablolardaki verileri de güncelleştirebilirsiniz.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Siz veri aldıkça satır güncelleştirilir.
```sql
SELECT * FROM inventory;
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubundaki kaynakları silen kaynak grubunu silin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin.

2. Kaynak grubu listesinde, kaynak grubunuzun adını seçin.

3. Kaynak grubunuzun **genel bakış** sayfasında **kaynak grubunu sil**' i seçin.

4. Onay iletişim kutusunda, kaynak grubunuzun adını yazın ve ardından **Sil**' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Azure Resource Manager şablonunu kullanarak sanal ağ hizmeti uç noktası ile MySQL için Azure veritabanı sunucusu oluşturma
> * Veritabanı oluşturmak için mysql komut satırı aracı kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme

> [!div class="nextstepaction"]
> [MySQL için Azure Veritabanına nasıl uygulama bağlanır](./howto-connection-string.md)