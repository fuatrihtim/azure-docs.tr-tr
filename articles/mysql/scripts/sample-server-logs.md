---
title: CLı betiği-yavaş sorgu günlüklerini Indirme-MySQL için Azure veritabanı
description: Bu örnek Azure CLI betiği, bir MySQL için Azure Veritabanı sunucusunun sunucu günlüklerini etkinleştirmeyi ve indirmeyi gösterir.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 28dbf51fa58dfdfc375bf8f44964a6b66db008e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94539167"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI kullanarak bir MySQL için Azure Veritabanı sunucusunun yavaş sorgu günlüklerini etkinleştirme ve indirme
Bu örnek CLI betiği, tek bir MySQL için Azure Veritabanı sunucusunun yavaş sorgu günlüklerini etkinleştirir ve indirir.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür. 

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin. &lt; &gt; `az monitor` Komutdaki Log_file_name kendi sunucu günlük dosyası adınızla değiştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komut** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Veritabanlarını barındıran bir MySQL sunucusu oluşturur. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Bir sunucu için yapılandırma değerlerini listeleyin. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Bir sunucunun yapılandırmasını güncelleştirin. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) | Bir sunucunun günlük dosyalarını listeleyin. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) | Günlük dosyalarını indirin. |
| [az group delete](/cli/azure/group#az-group-delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MySQL için Azure Veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
