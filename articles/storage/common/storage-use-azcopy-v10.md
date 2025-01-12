---
title: AzCopy ile v10 arasındaki kullanarak Azure depolama 'ya veri kopyalama veya taşıma | Microsoft Docs
description: AzCopy, depolama hesaplarına veri kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale AzCopy programını indirme, depolama hesabınıza bağlama ve dosyaları aktarma aşamalarında size yardımcı olur.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 826effb152d3f069e0a7c5b8e169f7083117ed22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494504"
---
# <a name="get-started-with-azcopy"></a>AzCopy’yi kullanmaya başlama

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale AzCopy programını indirme, depolama hesabınıza bağlama ve dosyaları aktarma aşamalarında size yardımcı olur.

> [!NOTE]
> AzCopy **ile v10 arasındaki** , şu anda desteklenen AzCopy sürümüdür.
>
> AzCopy 'in önceki bir sürümünü kullanmanız gerekiyorsa, bu makalenin [AzCopy 'in önceki sürümünü kullanma](#previous-version) bölümüne bakın.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy'yi indirin

İlk olarak, AzCopy Ile v10 arasındaki çalıştırılabilir dosyasını bilgisayarınızdaki herhangi bir dizine indirin. AzCopy Ile v10 arasındaki yalnızca yürütülebilir bir dosyadır, bu nedenle yüklenecek bir şey yoktur.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Bu dosyalar bir ZIP dosyası (Windows ve Mac) veya bir tar dosyası (Linux) olarak sıkıştırılır. IK dosyasını Linux üzerinde indirip sıkıştırmasını açmak için Linux dağıtımına yönelik belgelere bakın.

> [!NOTE]
> [Azure Tablo depolama](../tables/table-storage-overview.md) hizmetinize veri kopyalamak Istiyorsanız, [azcopy sürüm 7,3](https://aka.ms/downloadazcopynet)' yi de yükleyebilirsiniz.


## <a name="run-azcopy"></a>AzCopy programını çalıştırma

Kolaylık sağlamak için AzCopy yürütülebilir dosyanızın bulunduğu dizini sistem yolunuza ekleyebilirsiniz. Bu şekilde, `azcopy` sisteminizdeki herhangi bir dizinden yazabilirsiniz.

Yolunuza AzCopy dizinini eklememayı seçerseniz, dizinleri AzCopy yürütülebilir dosyanızın konumuyla değiştirmeniz ve `azcopy` `.\azcopy` Windows PowerShell komut istemleri ' nde yazmanız gerekir.

Komutların listesini görmek için yazın `azcopy -h` ve ENTER tuşuna basın.

Belirli bir komut hakkında bilgi edinmek için, yalnızca komutun adını ekleyin (örneğin: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Satır içi yardım](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Her komut ve komut parametresine ilişkin ayrıntılı başvuru belgelerini bulmak için bkz. [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Azure depolama hesabınızın sahibi olarak, verilere erişim için otomatik olarak izinler atanmamıştır. AzCopy ile anlamlı bir işlem yapabilmeniz için, depolama hizmetine yetkilendirme kimlik bilgilerini nasıl sağlayacağınıza karar vermeniz gerekir. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy yetkilendir

Yetkilendirme kimlik bilgilerini Azure Active Directory (AD) kullanarak veya paylaşılan erişim Imzası (SAS) belirteci kullanarak sağlayabilirsiniz.

Bu tabloyu kılavuz olarak kullan:

| Depolama türü | Şu anda desteklenen yetkilendirme yöntemi |
|--|--|
|**Blob depolama** | Azure AD ve SAS |
|**BLOB depolama (hiyerarşik ad alanı)** | Azure AD ve SAS |
|**Dosya depolama** | Yalnızca SAS |

#### <a name="option-1-use-azure-active-directory"></a>Seçenek 1: Azure Active Directory kullanın

Bu seçenek yalnızca BLOB depolama için kullanılabilir. Azure Active Directory kullanarak, her komuta bir SAS belirteci eklemek yerine kimlik bilgilerini bir kez sağlayabilirsiniz.  

> [!NOTE]
> Geçerli sürümde, blob 'ları depolama hesapları arasında kopyalamayı planlıyorsanız, her kaynak URL 'ye bir SAS belirteci eklemeniz gerekir. SAS belirtecini yalnızca hedef URL 'den atlayabilirsiniz. Örnekler için bkz. [depolama hesapları arasında blobları kopyalama](#transfer-data).

Azure AD 'yi kullanarak erişim yetkisi vermek için bkz. [AzCopy ve Azure Active Directory (Azure AD) ile bloblara erişim yetkisi verme](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>2. seçenek: SAS belirteci kullanma

AzCopy komutlarınız içinde kullanılan her kaynak veya hedef URL 'ye bir SAS belirteci ekleyebilirsiniz.

Bu örnek komut, verileri bir yerel dizinden bir blob kapsayıcısına özyinelemeli olarak kopyalar. Kapsayıcı URL 'sinin sonuna kurgusal bir SAS belirteci eklenir.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS belirteçleri ve nasıl elde edileceği hakkında daha fazla bilgi edinmek için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](./storage-sas-overview.md).

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Veri aktarma

Kimliğinizi yetkilendirdikten veya bir SAS belirteci elde ettikten sonra, veri aktarmaya başlayabilirsiniz.

> [!NOTE]
> Depolama hesabının [Güvenli aktarım gerekli](storage-require-secure-transfer.md) ayarı, bir depolama hesabı bağlantısının aktarım katmanı GÜVENLIĞI (TLS) ile güvenli olup olmadığını belirler. Bu ayar varsayılan olarak etkindir.   

Örnek komutları bulmak için Bu makalelerden birine bakın.

| Hizmet | Makale |
|--------|-----------|
|Azure Blob Depolama |[Azure Blob depolama alanına dosya yükleme](storage-use-azcopy-blobs-upload.md)<br><br>[Blob 'ları Azure Blob depolamadan indirin](storage-use-azcopy-blobs-download.md)<br><br>[Blob 'ları Azure depolama hesapları arasında kopyalama](storage-use-azcopy-blobs-copy.md)<br><br>[Azure Blob depolama ile eşitlemeyi](storage-use-azcopy-blobs-synchronize.md)|
|Azure Dosyaları |[AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)|
|Amazon S3|[Amazon S3 'ten Azure Storage 'a veri kopyalama](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Google Cloud Storage 'daki verileri Azure depolama 'ya kopyalama (Önizleme)](storage-use-azcopy-google-cloud.md)|
|Azure Stack depolama|[AzCopy ve Azure Stack Storage ile veri aktarımı](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Betikte kullanma

#### <a name="obtain-a-static-download-link"></a>Statik indirme bağlantısı alma

Zaman içinde, AzCopy [indirme bağlantısı](#download-and-install-azcopy) , AzCopy 'in yeni sürümlerini işaret edecektir. Betiğinizin AzCopy 'i İndirmeleri durumunda AzCopy 'in daha yeni bir sürümü betiğinizin bağımlı olduğu özellikleri değiştirirse betik çalışmayı durdurabilir.

Bu sorunlardan kaçınmak için, AzCopy 'in geçerli sürümüne bir statik (değişmeyen) bağlantı alın. Bu şekilde, komut dosyası her çalıştığında AzCopy 'in aynı tam sürümünü indirir.

Bağlantıyı almak için şu komutu çalıştırın:

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux için `--strip-components=1` `tar` komutta, sürüm adını içeren en üst düzey klasörü kaldırır ve bunun yerine ikili dosya doğrudan geçerli klasöre ayıklar. Bu, betiğin `azcopy` yalnızca URL 'yi güncelleştirerek yeni bir sürümüyle güncelleştirilmesini sağlar `wget` .

URL Bu komutun çıktısında görüntülenir. Komut dosyası daha sonra bu URL 'YI kullanarak AzCopy 'i indirebilir.

| İşletim sistemi  | Komut |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>SAS belirteçlerinde kaçış özel karakterleri

Uzantısı olan toplu iş dosyalarında `.cmd` `%` SAS belirteçlerinde görünen karakterleri atlamanız gerekir. Bunu, `%` SAS belirteç dizesindeki mevcut karakterlerin yanına ek bir karakter ekleyerek yapabilirsiniz `%` .

#### <a name="run-scripts-by-using-jenkins"></a>Jenkins kullanarak betikleri çalıştırma

Komut dosyalarını çalıştırmak için [Jenkins](https://jenkins.io/) kullanmayı planlıyorsanız, aşağıdaki komutu betiğin başına yerleştirdiğinizden emin olun.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Azure Depolama Gezgini ' de kullanın

[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) , tüm veri aktarımı işlemlerini gerçekleştirmek Için AzCopy kullanır. AzCopy 'in performans avantajlarından yararlanmak istiyorsanız [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanabilirsiniz, ancak dosyalarınızda etkileşim kurmak için komut satırı yerine bir grafik kullanıcı arabirimi kullanmayı tercih edersiniz.

Depolama Gezgini, işlemleri gerçekleştirmek için hesap anahtarınızı kullanır, bu nedenle Depolama Gezgini oturum açtıktan sonra, ek yetkilendirme kimlik bilgileri sağlamanız gerekmez.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Yapılandırma, iyileştirme ve çözme

Bkz. [AzCopy 'ı yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Önceki bir sürümü kullan

AzCopy 'in önceki sürümünü kullanmanız gerekirse, aşağıdaki bağlantılardan birine bakın:

- [Windows üzerinde AzCopy (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux üzerinde AzCopy (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Sonraki adımlar

Sorularınız, sorunlarınız veya genel geri bildiriminiz varsa bunları [GitHub](https://github.com/Azure/azure-storage-azcopy) sayfasına gönderin.
