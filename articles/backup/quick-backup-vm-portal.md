---
title: Hızlı başlangıç-Azure portal bir VM 'yi yedekleme
description: Bu hızlı başlangıçta, bir kurtarma hizmetleri Kasası oluşturmayı, bir Azure VM 'de korumayı etkinleştirmeyi ve Azure portal VM 'yi yedeklemeyi öğrenin.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/12/2020
ms.custom: mvc
ms.openlocfilehash: cd0d4928d5cd0eb15f4f1372a0a3417fc85d13bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328793"
---
# <a name="back-up-a-virtual-machine-in-azure"></a>Azure'daki bir sanal makineyi yedekleme

Azure yedekleri Azure portalı üzerinden oluşturulabilir. Bu yöntem, Azure yedeklerini ve tüm ilgili kaynakları oluşturup yapılandırmaya yönelik tarayıcı tabanlı bir kullanıcı arabirimi sağlar. Düzenli aralıklarla yedekleme yaparak verilerinizi koruyabilirsiniz. Azure Backup, coğrafi olarak yedekli kurtarma kasalarında saklanabilecek kurtarma noktaları oluşturur. Bu makalede Azure portalıyla bir sanal makinenin nasıl yedekleneceği anlatılmaktadır.

Bu hızlı başlangıç belgesi var olan bir Azure VM'de yedeklemeyi etkinleştirir. Bir sanal makine oluşturmanız gerekiyorsa [Azure portalıyla sanal makine oluşturabilirsiniz](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="select-a-vm-to-back-up"></a>Yedeklenecek sanal makineyi seçme

Bir kurtarma hizmetleri kasasında basit bir zamanlanmış günlük yedekleme oluşturun.

1. Sol taraftaki menüden **Sanal makineler**'i seçin.
2. Listeden yedekleyeceğiniz sanal makineyi seçin. Örnek VM hızlı başlangıç komutlarını kullandıysanız VM'nin adı *myVM*, kaynak grubunun adı ise *myResourceGroup* olacaktır.
3. **İşlemler** bölümünde **Yedekle**'yi seçin. **Yedeklemeyi etkinleştir** penceresi açılır.

## <a name="enable-backup-on-a-vm"></a>Bir sanal makinede yedeklemeyi etkinleştirme

Kurtarma Hizmetleri kasası, Azure sanal makineleri gibi koruma altındaki kaynakların yedeklenen verilerini saklayan bir mantıksal kapsayıcıdır. Koruma altındaki bir kaynak için yedekleme işi çalıştığında Kurtarma Hizmetleri kasasının içinde bir kurtarma noktası oluşturulur. Daha sonra bu kurtarma noktalarından birini kullanarak verileri dilediğiniz zaman geri yükleyebilirsiniz.

1. **Yeni oluştur**'u seçin ve yeni kasa için *myRecoveryServicesVault* gibi bir ad girin.
2. Seçili değilse **Var olanı kullan**'ı ve açılan menüden sanal makinenin kaynak grubunu seçin.

    ![Azure portalında sanal makine yedeklemeyi etkinleştirme](./media/quick-backup-vm-portal/enable-backup.png)

    Varsayılan olarak kasada Coğrafi Olarak Yedekli depolama özelliği etkindir. Verilerinizi daha fazla korumak için bu depolama artıklık düzeyi, yedekleme verilerinizin birincil bölgeden yüzlerce mil olan ikincil bir Azure bölgesine çoğaltılmasını sağlar.

    Bir yedekleme işinin çalışma zamanını ve kurtarma noktalarının saklama süresini tanımlamak için ilke oluşturur ve kullanırsınız. Varsayılan koruma ilkesi yedekleme işini her gün çalıştırır ve kurtarma noktalarını 30 gün boyunca tutar. Sanal makinenizi hızlı bir şekilde koruma altına almak için bu varsayılan ilke değerlerini kullanabilirsiniz.

3. Varsayılan yedekleme ilkesi değerlerini kabul etmek için **Yedeklemeyi Etkinleştir**'i seçin.

Kurtarma Hizmetleri kasasının oluşturulması birkaç dakika sürer.

## <a name="start-a-backup-job"></a>Bir yedekleme işi başlatma

Varsayılan ilkenin işi planlanan saatte başlatmasını beklemek yerine yedekleme işini hemen başlatabilirsiniz. İlk yedekleme işi tam kurtarma noktası oluşturur. Bu ilk yedekleme sonrasında çalıştırılan tüm yedekleme işleri artımlı kurtarma noktaları oluşturur. Yalnızca son yedekleme sonrasında yapılan değişiklikleri aktardığından artımlı kurtarma noktaları depolama alanı ve süre açısından verimlilik sağlar.

1. Sanal makinenizin **Yedekleme** penceresinde **Şimdi yedekle**'yi seçin.

    ![Azure portalında sanal makineyi hemen yedekleme](./media/quick-backup-vm-portal/backup-now.png)

2. 30 günlük yedekleme tutma ilkesini kabul etmek için varsayılan **Yedekleri Şu Tarihe Kadar Tut:** tarihini kullanın. İşi başlatmak için **Yedekle**'yi seçin.

## <a name="monitor-the-backup-job"></a>Yedekleme işini izleme

Sanal makinenizin **Yedekleme** penceresinde yedekleme durumu ve tamamlanan geri yükleme noktası sayısı gösterilir. Sanal makine yedekleme işi tamamlandıktan sonra **Son yedekleme zamanı**, **En son geri yükleme noktası** ve **En eski geri yükleme noktası**, **Genel Bakış** penceresinin sağ tarafında gösterilir.

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Artık gerekli değilse sanal makine korumasını devre dışı bırakabilir, kurtarma noktalarını ve Kurtarma Hizmetleri kasasını kaldırabilir ve ardından sanal makine kaynaklarıyla ilişkilendirilmiş kaynak grubunu silebilirsiniz

VM 'niz için verilerin nasıl geri yükleneceğini açıklayan bir yedekleme öğreticisine devam edecaksanız, bu bölümdeki adımları atlayın ve [sonraki adımlara](#next-steps)gidin.

1. Sanal makinenizle ilgili **Yedekleme** seçeneğini belirleyin.

2. **Yedeklemeyi Durdur**' ı seçin.

    ![Azure portalında sanal makine yedeklemesini durdurma](./media/quick-backup-vm-portal/stop-backup.png)

3. Açılan menüden **Yedekleme Verilerini Sil**'i seçin.

4. **Yedekleme öğesinin adını yazın** iletişim kutusuna sanal makinenizin adını girin. Örneğin: *myVM*. **Yedeklemeyi Durdur**' u seçin.

    Sanal makine yedekleme işlemi durdurulduktan ve kurtarma noktaları kaldırıldıktan sonra kaynak grubunu silebilirsiniz. Var olan bir sanal makineyi kullandıysanız kaynak grubunu ve sanal makineyi bırakmak isteyebilirsiniz.

5. Soldaki menüden **Kaynak grupları**'nı seçin.
6. Listeden kaynak grubunuzu seçin. Örnek sanal makine hızlı başlangıç komutlarını kullandıysanız kaynak grubunun adı *myResourceGroup* olacaktır.
7. **Kaynak grubunu sil**'i seçin. Onaylamak için kaynak grubunun adını girin ve **Sil**'i seçin.

    ![Kaynak grubunu Azure portalından silme](./media/quick-backup-vm-portal/delete-resource-group-from-portal.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kurtarma Hizmetleri kasası oluşturdunuz, bir sanal makine için koruma özelliklerini etkinleştirdiniz ve ilk kurtarma noktasını oluşturdunuz. Azure Backup ve Kurtarma Hizmetleri hakkında daha fazla bilgi edinmek için öğreticilere geçin.

> [!div class="nextstepaction"]
> [Birden fazla Azure sanal makinesini yedekleme](./tutorial-backup-vm-at-scale.md)
