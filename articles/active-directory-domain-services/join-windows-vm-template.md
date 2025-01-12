---
title: Windows VM 'yi Azure 'a katmak için şablon kullanma AD DS | Microsoft Docs
description: Yeni veya mevcut bir Windows Server VM 'sini Azure Active Directory Domain Services yönetilen bir etki alanına katmak için Azure Resource Manager şablonlarını nasıl kullanacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619445"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Windows Server sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına katma

Azure sanal makinelerinin (VM 'Ler) dağıtımını ve yapılandırmasını otomatik hale getirmek için bir Kaynak Yöneticisi şablonu kullanabilirsiniz. Bu şablonlar her seferinde tutarlı dağıtımlar oluşturmanıza olanak sağlar. Uzantılar, dağıtımın parçası olarak bir VM 'yi otomatik olarak yapılandırmak için şablonlara da dahil edilebilir. Bir faydalı uzantı VM 'Leri, Azure Active Directory Domain Services (Azure AD DS) tarafından yönetilen etki alanları ile kullanılabilecek bir etki alanına birleştirir.

Bu makalede, bir Windows Server VM 'sini Kaynak Yöneticisi şablonları kullanarak Azure AD DS yönetilen bir etki alanına nasıl oluşturacağınız ve katılalacağı gösterilmektedir. Ayrıca, mevcut bir Windows Server VM 'sini Azure AD DS etki alanına nasıl katılacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Yönetilen etki alanının bir parçası olan bir kullanıcı hesabı.

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager şablonuna genel bakış

Kaynak Yöneticisi şablonlar, Azure altyapısını kodda tanımlamanıza olanak sağlar. Gerekli kaynaklar, ağ bağlantıları ya da VM 'lerin yapılandırılması bir şablonda tanımlanabilir. Bu şablonlar her seferinde tutarlı, tekrarlanabilir dağıtımlar oluşturur ve değişiklik yaptığınız sürece sürümlenebilir. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarına genel bakış][template-overview].

Her kaynak JavaScript Nesne Gösterimi (JSON) kullanılarak bir şablonda tanımlanır. Aşağıdaki JSON örneği, Active Directory etki alanına JOIN uzantısını yüklemek için *Microsoft. COMPUTE/virtualMachines/Extensions* kaynak türünü kullanır. Dağıtım zamanında belirttiğiniz parametreler kullanılır. Uzantı dağıtıldığında, VM belirtilen yönetilen etki alanına katılır.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Aynı şablonda bir VM oluşturmazsanız, bu VM uzantısı da dağıtılabilir. Bu makaledeki örneklerde aşağıdaki yaklaşımların her ikisi de gösterilmektedir:

* [Windows Server VM oluşturma ve yönetilen bir etki alanına katma](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Mevcut bir Windows Server sanal makinesini yönetilen bir etki alanına katma](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server VM oluşturma ve yönetilen bir etki alanına katma

Bir Windows Server sanal makinesine ihtiyacınız varsa, bir Kaynak Yöneticisi şablonu kullanarak bir tane oluşturup yapılandırabilirsiniz. VM dağıtıldığında, VM 'yi yönetilen bir etki alanına katmak için bir uzantı yüklenir. Yönetilen bir etki alanına katmak istediğiniz bir VM zaten varsa, [var olan bir Windows Server sanal makinesini yönetilen bir etki alanına katma](#join-an-existing-windows-server-vm-to-a-managed-domain)' a atlayın.

Bir Windows Server sanal makinesi oluşturmak ve ardından onu yönetilen bir etki alanına katmak için aşağıdaki adımları izleyin:

1. [Hızlı başlangıç şablonuna](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)gidin. **Azure 'A dağıtma** seçeneğini belirleyin.
1. **Özel dağıtım** sayfasında, bir WINDOWS Server VM 'sini oluşturmak ve yönetilen etki alanına katmak için aşağıdaki bilgileri girin:

    | Ayar                   | Değer |
    |---------------------------|-------|
    | Abonelik              | Azure AD Domain Services etkinleştirdiğiniz Azure aboneliğini seçin. |
    | Kaynak grubu            | VM 'niz için kaynak grubunu seçin. |
    | Konum                  | SANAL makinenizin konumunu seçin. |
    | Mevcut VNET adı        | VM 'nin bağlanacağı, *Myvnet* gibi mevcut sanal ağın adı. |
    | Mevcut alt ağ adı      | Var olan sanal ağ alt ağının adı (örneğin, *Iş yükleri*). |
    | DNS etiketi öneki          | VM için kullanmak üzere *myvm* gıbı bir DNS adı girin. |
    | VM boyutu                   | *Standard_DS2_v2* gıbı bir VM boyutu belirtin. |
    | Katılacak etki alanı            | *Aaddscontoso.com* gibi yönetilen etkı alanı DNS adı. |
    | Etki alanı Kullanıcı adı           | Yönetilen etki alanındaki, VM 'yi yönetilen etki alanına (gibi) katmak için kullanılması gereken kullanıcı hesabı `contosoadmin@aaddscontoso.com` . Bu hesap, yönetilen etki alanının bir parçası olmalıdır. |
    | Etki alanı parolası           | Önceki ayarda belirtilen kullanıcı hesabının parolası. |
    | İsteğe bağlı OU yolu          | VM 'nin ekleneceği özel OU. Bu parametre için bir değer belirtmezseniz, VM varsayılan *AAD DC bilgisayarları* OU 'ya eklenir. |
    | VM Yöneticisi Kullanıcı adı         | VM 'de oluşturulacak bir yerel yönetici hesabı belirtin. |
    | VM yönetici parolası         | VM için bir yerel yönetici parolası belirtin. Parola deneme yanılma saldırılarına karşı korumak için güçlü bir yerel yönetici parolası oluşturun. |

1. Hüküm ve koşulları gözden geçirin, ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum** kutusunu işaretleyin. Hazırlandığınızda, sanal makineyi oluşturmak ve yönetilen etki alanına katmak için **satın al** ' ı seçin.

> [!WARNING]
> **Parolaları dikkatle işleyin.**
> Şablon parametre dosyası, yönetilen etki alanının bir parçası olan bir kullanıcı hesabının parolasını ister. Bu dosyaya el ile değer girmeyin ve dosya paylaşımlarında veya diğer paylaşılan konumlarda erişilebilir durumda bırakın.

Dağıtımın başarıyla tamamlanabilmesi birkaç dakika sürer. İşiniz bittiğinde, Windows VM oluşturulur ve yönetilen etki alanına birleştirilir. VM, etki alanı hesapları kullanılarak yönetilebilir veya oturum açabilir.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Mevcut bir Windows Server sanal makinesini yönetilen bir etki alanına katma

Yönetilen bir etki alanına katmak istediğiniz mevcut bir sanal makine veya VM grubunuz varsa, yalnızca VM uzantısını dağıtmak için bir Kaynak Yöneticisi şablonu kullanabilirsiniz.

Mevcut bir Windows Server sanal makinesini yönetilen bir etki alanına katmak için aşağıdaki adımları izleyin:

1. [Hızlı başlangıç şablonuna](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)gidin. **Azure 'A dağıtma** seçeneğini belirleyin.
1. **Özel dağıtım** SAYFASıNDA, VM 'yi yönetilen etki alanına katmak için aşağıdaki bilgileri girin:

    | Ayar                   | Değer |
    |---------------------------|-------|
    | Abonelik              | Azure AD Domain Services etkinleştirdiğiniz Azure aboneliğini seçin. |
    | Kaynak grubu            | Mevcut VM 'niz ile kaynak grubunu seçin. |
    | Konum                  | Var olan sanal makinenizin konumunu seçin. |
    | VM listesi                   | Yönetilen etki alanına katılacak mevcut VM 'leri ( *myVM1, myVM2* gibi) virgülle ayrılmış bir liste girin. |
    | Etki alanına katılması Kullanıcı adı     | Yönetilen etki alanındaki, VM 'yi yönetilen etki alanına (gibi) katmak için kullanılması gereken kullanıcı hesabı `contosoadmin@aaddscontoso.com` . Bu hesap, yönetilen etki alanının bir parçası olmalıdır. |
    | Etki alanına katılması Kullanıcı parolası | Önceki ayarda belirtilen kullanıcı hesabının parolası. |
    | İsteğe bağlı OU yolu          | VM 'nin ekleneceği özel OU. Bu parametre için bir değer belirtmezseniz, VM varsayılan *AAD DC bilgisayarları* OU 'ya eklenir. |

1. Hüküm ve koşulları gözden geçirin, ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum** kutusunu işaretleyin. Hazırlandığınızda, VM 'yi yönetilen etki alanına katmak için **satın al** ' ı seçin.

> [!WARNING]
> **Parolaları dikkatle işleyin.**
> Şablon parametre dosyası, yönetilen etki alanının bir parçası olan bir kullanıcı hesabının parolasını ister. Bu dosyaya el ile değer girmeyin ve dosya paylaşımlarında veya diğer paylaşılan konumlarda erişilebilir durumda bırakın.

Dağıtımın başarıyla tamamlanabilmesi birkaç dakika sürer. İşiniz bittiğinde, belirtilen Windows VM 'Leri yönetilen etki alanına katılır ve etki alanı hesapları kullanılarak yönetilebilir veya oturum açabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şablonları kullanarak kaynakları yapılandırmak ve dağıtmak için Azure portal kullandınız. Ayrıca, [Azure PowerShell][deploy-powershell] veya [Azure CLI][deploy-cli]kullanarak kaynak yöneticisi şablonlarıyla kaynak dağıtabilirsiniz.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
