---
title: Azure IoT Central uygulamasındaki kullanıcıları ve rolleri yönetme | Microsoft Docs
description: Yönetici olarak, Azure IoT Central uygulamanızda kullanıcıları ve rolleri yönetme
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: f6c45b8d9804f16c4e59d259f562cc03f187e6a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92122986"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>IoT Central uygulamanızdaki kullanıcıları ve rolleri yönetme

Bu makalede, yönetici olarak Azure IoT Central uygulamanızda Kullanıcı ekleme, düzenleme ve silme işlemlerinin nasıl yapılacağı açıklanır. Makalede ayrıca Azure IoT Central uygulamanızda rollerin nasıl yönetileceği açıklanır.

**Yönetim** bölümüne erişmek ve bunları kullanmak için, bir Azure IoT Central uygulaması için **yönetici** rolünde olmanız gerekir. Bir Azure IoT Central uygulaması oluşturursanız, bu uygulamanın **yönetici** rolüne otomatik olarak eklendiniz.

## <a name="add-users"></a>Kullanıcı ekle

Azure IoT Central uygulamasına oturum açabilmeniz ve bu uygulamaya erişebilmek için her kullanıcının bir kullanıcı hesabı olması gerekir. Microsoft hesapları ve Azure Active Directory hesapları Azure IoT Central 'da desteklenmektedir. Azure Active Directory gruplar Şu anda Azure IoT Central 'da desteklenmemektedir.

Daha fazla bilgi için bkz. [Microsoft hesabı yardım](https://support.microsoft.com/products/microsoft-account?category=manage-account) ve  [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Bir IoT Central uygulamasına kullanıcı eklemek için **Yönetim** bölümündeki **Kullanıcılar** sayfasına gidin.
    
    > [!div class="mx-imgBorder"]
    >![Kullanıcıları yönetme](media/howto-manage-users-roles/manage-users-pnp.png)

1. Kullanıcı eklemek için, **Kullanıcılar** sayfasında **+ Kullanıcı Ekle**' yi seçin.

1. **Rol** açılan menüsünden Kullanıcı için bir rol seçin. Roller hakkında daha fazla bilgi için bu makalenin [rolleri Yönet](#manage-roles) bölümünden daha fazla bilgi edinebilirsiniz.

    > [!div class="mx-imgBorder"]
    >![Kullanıcı ekleme ve rol seçme](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Özel bir rolde başka Kullanıcı ekleme izni veren bir Kullanıcı, yalnızca kendi rolünden aynı veya daha az izne sahip bir role Kullanıcı ekleyebilirler.

Azure Active Directory bir IoT Central kullanıcı KIMLIĞI silinirse ve sonra, Kullanıcı IoT Central uygulamasında oturum açamaz. Erişimi yeniden etkinleştirmek için IoT Central yöneticisinin uygulamayı silip yeniden eklemesi gerekir.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Kullanıcılara atanan rolleri düzenleme

Roller atandıktan sonra değiştirilemez. Bir kullanıcıya atanmış rolü değiştirmek için kullanıcıyı silin ve ardından kullanıcıyı farklı bir rolle yeniden ekleyin.

> [!NOTE]
> Atanan roller IoT Central uygulamaya özeldir ve Azure portalından yönetilemez.

## <a name="delete-users"></a>Kullanıcıları silme

Kullanıcıları silmek için **Kullanıcılar** sayfasında bir veya daha fazla onay kutusunu işaretleyin. Ardından **Sil**’i seçin.

## <a name="manage-roles"></a>Rolleri yönet

Roller, kuruluşunuzda kimlerin IoT Central çeşitli görevleri yapmasına izin verileceğini denetlemenize olanak tanır. Uygulamanızın kullanıcılarına atayabileceğiniz, üç yerleşik rol vardır. Daha ayrıntılı denetime ihtiyacınız varsa [özel roller de oluşturabilirsiniz](#create-a-custom-role) .

> [!div class="mx-imgBorder"]
> ![Rol seçimini Yönet](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Yönetici

**Yönetici** rolündeki kullanıcılar, faturalama dahil olmak üzere uygulamanın her bölümünü yönetebilir ve denetleyebilir.

Bir uygulamayı oluşturan kullanıcı otomatik olarak **yönetici** rolüne atanır. **Yönetici** rolünde her zaman en az bir kullanıcı olmalıdır.

### <a name="builder"></a>Oluşturucu

**Oluşturucu** rolündeki kullanıcılar uygulamanın her bölümünü yönetebilir, ancak yönetim veya sürekli veri dışa aktarma sekmelerinde değişiklik yapamaz.

### <a name="operator"></a>Operatör

**Operatör** rolündeki kullanıcılar, cihaz sistem durumunu ve durumunu izleyebilir. Cihaz şablonlarında değişiklik yapmasına veya uygulamayı yönetmeye izin verilmez. İşleçler cihaz ekleyip silebilir, cihaz kümelerini yönetebilir ve analiz ve işleri çalıştırabilir. 

## <a name="create-a-custom-role"></a>Özel rol oluşturma

Çözümünüz daha ayrıntılı erişim denetimleri gerektiriyorsa, özel izin kümeleriyle özel roller oluşturabilirsiniz. Özel bir rol oluşturmak için uygulamanızın **Yönetim** bölümünde **Roller** sayfasına gidin. Ardından **+ Yeni rol**' i seçin ve rolünüz için bir ad ve açıklama ekleyin. Rolünüzün gerektirdiği izinleri seçin ve ardından **Kaydet**' i seçin.

Kullanıcıları, yerleşik bir role eklediğiniz şekilde özel rolünüze ekleyebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Özel bir rol oluşturma](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Özel rol seçenekleri

Özel bir rol tanımladığınızda, bir kullanıcının rolün üyesi olmaları durumunda izin verilen izinler kümesini seçersiniz. Bazı izinler diğerlerine bağımlıdır. Örneğin, bir role **uygulama panoları güncelleştirme** iznini eklerseniz, **uygulama panoları görüntüle** izni otomatik olarak eklenir. Aşağıdaki tablolar, özel roller oluştururken kullanabileceğiniz kullanılabilir izinleri ve bunların bağımlılıklarını özetler.

#### <a name="managing-devices"></a>Cihazları yönetme

**Cihaz şablonu izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Yönetme | Görünüm <br/> Diğer bağımlılıklar: cihaz örneklerini görüntüle  |
| Tam Denetim | Görüntüleme, yönetme <br/> Diğer bağımlılıklar: cihaz örneklerini görüntüle |

**Cihaz örneği izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme |
| Güncelleştir | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Oluştur | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Yürütme komutları | Güncelleştirme, görüntüleme <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme, yürütme komutları <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |

**Cihaz grupları izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme |
| Güncelleştir | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Oluştur | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme |

**Cihaz bağlantısı yönetimi izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Örneği oku | Yok <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, cihaz örneklerini görüntüleme |
| Örneği Yönet | Yok |
| Küresel oku | Yok   |
| Küresel yönetme | Küresel oku |
| Tam Denetim | Örneği oku, örneği Yönet, genel oku, genel Yönet. <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, cihaz örneklerini görüntüleme |

**İşler izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Güncelleştir | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Oluştur | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Yürütme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleyin; Cihaz örneklerini güncelleştirme; Cihaz örneklerinde komutları yürütme |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme, yürütme <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleyin; Cihaz örneklerini güncelleştirme; Cihaz örneklerinde komutları yürütme |

**Kurallar izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Güncelleştir | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Oluştur | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |

#### <a name="managing-the-app"></a>Uygulamayı yönetme

**Uygulama ayarları izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Kopyala | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini, cihaz gruplarını, panoları, veri vermeyi, marka, yardım bağlantılarını, özel rolleri, kuralları görüntüle |
| Sil | Görünüm   |
| Tam Denetim | Görüntüleme, güncelleştirme, kopyalama, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, uygulama panoları, veri dışarı aktarma, marka, yardım bağlantıları, özel roller, kurallar |

**Uygulama şablonu dışarı aktarma izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Dışarı Aktarma | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini, cihaz gruplarını, panoları, veri vermeyi, marka, yardım bağlantılarını, özel rolleri, kuralları görüntüle |
| Tam Denetim | Görüntüle, dışarı aktar <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, uygulama panoları, veri dışarı aktarma, marka, yardım bağlantıları, özel roller, kurallar |

**Faturalandırma izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Yönetme | Yok     |
| Tam Denetim | Yönetme |

#### <a name="managing-users-and-roles"></a>Kullanıcıları ve rolleri yönetme

**Özel rol izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok |
| Güncelleştir | Görünüm |
| Oluştur | Görüntüle, Güncelleştir |
| Sil | Görünüm |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Kullanıcı yönetimi izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Ekle | Görünüm <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Tam Denetim | Görüntüleme, ekleme, silme <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |

> [!NOTE]
> Özel bir rolde başka Kullanıcı ekleme izni veren bir Kullanıcı, yalnızca kendi rolünden aynı veya daha az izne sahip bir role Kullanıcı ekleyebilirler.

#### <a name="customizing-the-app"></a>Uygulamayı özelleştirme

**Uygulama panosu izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Oluştur | Görüntüle, Güncelleştir |
| Sil | Görünüm   |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Kişisel panolar izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Oluştur | Görüntüle, Güncelleştir   |
| Sil | Görünüm   |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Marka, ayrıcalıklı simge ve renkler izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Tam Denetim | Görüntüle, Güncelleştir |

**Yardım bağlantıları izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Tam Denetim | Görüntüle, Güncelleştir |

#### <a name="extending-the-app"></a>Uygulamayı genişletme

**Veri dışarı aktarma izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Güncelleştir | Görünüm   |
| Oluştur | Görüntüle, Güncelleştir  |
| Sil | Görünüm   |
| Tam Denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**API belirteci izinleri**

| Name | Bağımlılıklar |
| ---- | -------- |
| Görünüm | Yok     |
| Oluştur | Görünüm   |
| Sil | Görünüm   |
| Tam Denetim | Görüntüleme, oluşturma, silme |

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda kullanıcıları ve rolleri yönetme hakkında bilgi edindiğinize göre, önerilen sonraki adım [faturanızı nasıl yöneteceğinizi](howto-view-bill.md)öğrenirsiniz.