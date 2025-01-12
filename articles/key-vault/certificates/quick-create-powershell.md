---
title: Hızlı başlangıç-Azure PowerShell & görüntüleme Azure Key Vault sertifikaları ayarlama
description: Azure PowerShell kullanarak Azure Key Vault bir sertifikanın nasıl ayarlanacağını ve alınacağını gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 587815cf9628df35f1e1efdbc6a7a3c89a27ed55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071926"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak Azure Key Vault bir sertifikayı ayarlama ve alma

Bu hızlı başlangıçta, Azure PowerShell Azure Key Vault ' de bir Anahtar Kasası oluşturacaksınız. Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [genel bakışı](../general/overview.md)gözden geçirebilirsiniz. Azure PowerShell komutları veya betikleri kullanarak Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bunu tamamladıktan sonra bir sertifika depolayacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üzerini gerektirir. `$PSVersionTable.PSVersion`Sürümü bulmak için yazın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Key Vault bir sertifika ekleyin

Kasaya bir sertifika eklemek için, birkaç ek adım yapmanız yeterlidir. Bu sertifika bir uygulama tarafından kullanılabilir. 

**Örnek sertifika** adlı ilkeyle otomatik olarak imzalanan bir sertifika oluşturmak için aşağıdaki komutları yazın:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Artık Azure Key Vault ' a eklediğiniz sertifikaya, URI 'sini kullanarak başvurabilirsiniz. Geçerli sürümü almak için **"https://<-Unique-keykasa-adı>. Vault.Azure.net/Certificates/ExampleCertificate"** kullanın. 

Daha önce depolanan sertifikayı görüntülemek için:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Şimdi bir Key Vault oluşturdunuz, bir sertifikayı depolamıştır ve geri almıştır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup içinde bir sertifika depoladığınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure PowerShell Key Vault cmdlet 'lerine](/powershell/module/az.keyvault/) yönelik başvuruya bakın
- [Key Vault güvenliğine genel bakış](../general/security-overview.md) konusunu gözden geçirin
