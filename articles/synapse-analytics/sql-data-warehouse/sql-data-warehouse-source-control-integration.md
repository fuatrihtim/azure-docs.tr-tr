---
title: Kaynak Denetimi Tümleştirmesi
description: Azure Repos (git ve GitHub) kullanarak yerel kaynak denetimi tümleştirmesiyle adanmış SQL havuzu için kurumsal sınıf veritabanı DevOps deneyimi.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 0864ab90f35e4af0443b418bc139ee5f8a7db665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585545"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu için kaynak denetimi tümleştirmesi

Bu öğreticide, SQL Server Veri Araçları (SSDT) veritabanı projenizi kaynak denetimiyle tümleştirme özetlenmektedir.  Kaynak denetimi tümleştirmesi, Azure SYNAPSE Analytics 'te adanmış SQL havuzu kaynağıyla sürekli tümleştirme ve dağıtım işlem hattınızı oluşturmanın ilk adımıdır.

## <a name="before-you-begin"></a>Başlamadan önce

- [Azure DevOps organizasyonu](https://azure.microsoft.com/services/devops/) için kaydolun
- [Oluşturma ve bağlanma](create-data-warehouse-portal.md) öğreticisine gidin
- [Visual Studio 2019 'yi yükler](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps 'ı ayarlama ve bu sunucuya bağlanma

1. Azure DevOps kuruluşunuzda, bir Azure depo deposu aracılığıyla SSDT veritabanı projenizi barındıracak bir proje oluşturun.

   ![Proje oluştur](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Proje oluştur")

2. Visual Studio 'Yu açın ve **Bağlantıyı Yönet**' i seçerek Azure DevOps kuruluşunuza ve projenize bir adım daha bağlanın.

   ![Bağlantıları Yönet](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Bağlantıları Yönet")

3. **Bağlantıları Yönet**' i seçip **projeye bağlanın**.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Bağlan")


4. Birinci adımda oluşturduğunuz projeyi bulun, **Bağlan**' ı seçin.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Bağlan")


3. Azure DevOps deponuzu projenizden yerel makinenize kopyalayın.

   ![Depoyu Kopyala](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Depoyu Kopyala")

Visual Studio kullanarak projeleri bağlama hakkında daha fazla bilgi için [Takım Gezgini içindeki projelere bağlanma](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true)bölümüne bakın. Visual Studio kullanarak bir depoyu kopyalamaya yönelik yönergeler için, [Çıkış deposunu Kopyala](/azure/devops/repos/git/clone?tabs=visual-studio) makalesini inceleyin. 

## <a name="create-and-connect-your-project"></a>Projenizi oluşturun ve bağlayın

1. Visual Studio 'da, **Yerel kopyalanmış deponuzda** hem dizin hem de yerel Git deposu ile yeni bir SQL Server veritabanı projesi oluşturun.

   ![Yeni proje oluşturma](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Yeni proje oluşturma")  

2. Boş sqlprojenize sağ tıklayın ve veri Ambarınızı veritabanı projesine aktarın.

   ![Projeyi içeri aktar](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projeyi içeri aktar")  

3. Visual Studio 'da Takım Gezgini, değişikliklerinizi yerel git deponuza işleyin.

   ![İşleme](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "İşleme")  

4. Kopyalanan depoda yerel olarak işlenen değişikliklere sahip olduğunuza göre, Azure DevOps projenizdeki Azure depo deponuzda yaptığınız değişiklikleri eşitleyin ve yeniden gönderin.

   ![Eşitleme ve Itme hazırlama](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Eşitleme ve itme hazırlama")

   ![Eşitle ve Gönder](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Eşitle ve Gönder")  

## <a name="validation"></a>Doğrulama

1. Visual Studio SQL Server Veri Araçları (SSDT) veritabanı projenizdeki bir tablo sütununu güncelleştirerek Azure deponuzda yapılan değişiklikleri doğrulayın.

   ![Güncelleştirme sütununu doğrula](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Güncelleştirme sütununu doğrula")

2. Yerel deponuzdaki değişikliği Azure deposuna yürütün ve gönderin.

   ![Değişiklikleri gönderme](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Değişiklikleri gönderme")

3. Azure depo deponuzda değişikliğin itildiğini doğrulayın.

   ![Doğrula](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Değişiklikleri doğrulama")

4. (**Isteğe bağlı**) Azure depo deponuzdaki nesne tanımlarının ve yerel deponun adanmış SQL havuzunuzu yansıttığından emin olmak için, şema karşılaştırması kullanın ve hedef adanmış SQL havuzunuzdaki değişiklikleri SSDT kullanarak güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Adanmış SQL havuzu için geliştirme](sql-data-warehouse-overview-develop.md)