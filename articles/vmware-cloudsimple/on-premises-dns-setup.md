---
title: CloudSimple tarafından Azure VMware çözümü-CloudSimple özel bulutu için DNS 'yi yapılandırma
description: Şirket içi iş istasyonlarından CloudSimple özel bulutu üzerinde vCenter Server erişimi için DNS ad çözümlemesi ayarlamayı açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899261"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Şirket içi iş istasyonlarından özel bulut vCenter erişimi için ad çözümlemesi için DNS yapılandırma

Şirket içi iş istasyonlarından CloudSimple özel bulutu üzerindeki vCenter Server 'a erişmek için, vCenter Server 'ın ana bilgisayar adının yanı sıra IP adresi ile adreslenmesi için DNS adresi çözümlemesini yapılandırmanız gerekir.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Özel bulutunuz için DNS sunucusunun IP adresini edinin

1. [Cloudsimple portalında](access-cloudsimple-portal.md)oturum açın.

2. **Kaynaklar**  >  **özel bulutları** ' na gidin ve bağlanmak istediğiniz özel bulutu seçin.

3. **Temel bilgi** altında özel bulutun **Özet** SAYFASıNDA, özel bulut DNS sunucusu IP adresini kopyalayın.

    ![Özel bulut DNS sunucuları](media/private-cloud-dns-server.png)


DNS yapılandırması için bu seçeneklerden birini kullanın.

* [*. Cloudsimple.io için DNS sunucusunda bir bölge oluşturun](#create-a-zone-on-a-microsoft-windows-dns-server)
* [*. Cloudsimple.io çözümlemek için şirket içi DNS sunucunuzda koşullu bir iletici oluşturun](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>*. Cloudsimple.io için DNS sunucusunda bir bölge oluşturun

Bir bölgeyi saplama bölgesi olarak ayarlayabilir ve ad çözümlemesi için özel buluttaki DNS sunucularına işaret edebilirsiniz. Bu bölüm, BIND DNS sunucusu veya Microsoft Windows DNS sunucusu kullanma hakkında bilgi sağlar.

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS sunucusu üzerinde bölge oluşturma

Belirli dosya ve yapılandırılacak parametreler, bireysel DNS kuruluma göre farklılık gösterebilir.

Örneğin, varsayılan BIND sunucu yapılandırması için DNS sunucunuzdaki/etc/Named.exe dosyasını düzenleyin ve aşağıdaki bölge bilgilerini ekleyin.

> [!NOTE]
>Bu makale, Microsoft 'un artık kullandığı bir terim olan bağımlı dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS sunucusunda bölge oluşturma

1. DNS sunucusuna sağ tıklayın ve **Yeni bölge**' yi seçin. 
  
    ![Yeni bölge menü seçeneğini vurgulayan ekran görüntüsü.](media/DNS01.png)
2. **Saplama bölgesi** ' ni seçin ve **İleri**' ye tıklayın.

    ![Saplama bölgesi seçeneğini vurgulayan ekran görüntüsü.](media/DNS02.png)
3. Ortamınıza bağlı olarak uygun seçeneği belirleyin ve **İleri**' ye tıklayın.

    ![Bölge verileri çoğaltma seçeneklerini gösteren ekran görüntüsü.](media/DNS03.png)
4. **İleri arama bölgesi** ' ni seçin ve **İleri**' ye tıklayın.

    ![Ileriye doğru arama bölgesi seçeneğini vurgulayan ekran görüntüsü.](media/DNS01.png)
5. Bölge adını girin ve **İleri**' ye tıklayın.

    ![Bölge adının nereye girdiğinin gösterildiği ekran görüntüsü.](media/DNS05.png)
6. CloudSimple portalından edindiğiniz özel bulutunuz için DNS sunucularının IP adreslerini girin.

    ![Yeni bölge](media/DNS06.png)
7. Sihirbaz kurulumunu gerçekleştirmek için gerektiğinde **İleri** ' ye tıklayın.

## <a name="create-a-conditional-forwarder"></a>Koşullu iletici oluşturma

Koşullu iletici, tüm DNS ad çözümleme isteklerini belirtilen sunucuya iletir. Bu kurulumla, tüm *. cloudsimple.io istekleri özel bulutta bulunan DNS sunucularına iletilir. Aşağıdaki örneklerde, farklı türlerde DNS sunucularında ileticilerin nasıl ayarlanacağı gösterilmektedir.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>BIND DNS sunucusunda koşullu iletici oluşturma

Belirli dosya ve yapılandırılacak parametreler, bireysel DNS kuruluma göre farklılık gösterebilir.

Örneğin, varsayılan BIND sunucu yapılandırması için DNS sunucunuzdaki/etc/Named.exe dosyasını düzenleyin ve aşağıdaki koşullu iletme bilgilerini ekleyin.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS sunucusunda koşullu iletici oluşturma

1. DNS sunucusunda DNS Yöneticisi 'Ni açın.
2. **Koşullu ileticiler** ' e sağ tıklayın ve yeni bir koşullu iletici ekleme seçeneğini belirleyin.

    ![Koşullu Iletici 1 Windows DNS](media/DNS08.png)
3. DNS etki alanını ve özel buluttaki DNS sunucularının IP adresini girip **Tamam**' a tıklayın.
