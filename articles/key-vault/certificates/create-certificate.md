---
title: Sertifika oluşturma yöntemleri
description: Azure Key Vault bir Key Vault sertifikası oluşturmak veya içeri aktarmak için farklı seçenekler hakkında bilgi edinin. Key Vault sertifikası oluşturmanın birkaç yolu vardır.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a9545c040809331a5556b11f6cc7536931e2d421
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93289568"
---
# <a name="certificate-creation-methods"></a>Sertifika oluşturma yöntemleri

 Bir Key Vault (KV) sertifikası oluşturulmuş veya bir anahtar kasasında içeri aktarılabilir. Bir KV sertifikası oluşturulduğunda özel anahtar, anahtar kasasında oluşturulur ve sertifika sahibine hiçbir zaman gösterilmez. Aşağıda Key Vault bir sertifika oluşturmanın yolları verilmiştir:  

-   **Otomatik olarak imzalanan sertifika oluştur:** Bu, bir ortak özel anahtar çifti oluşturur ve sertifikayla ilişkilendirir. Sertifika kendi anahtarı tarafından imzalanacak.  

-    **El ile yeni bir sertifika oluşturun:** Bu, bir ortak özel anahtar çifti oluşturur ve bir X. 509.952 sertifika imzalama isteği oluşturur. İmzalama isteği, kayıt yetkiliniz veya sertifika yetkiliniz tarafından imzalanabilir. İmzalı x509 sertifikası, Key Vault KV sertifikasını tamamlayabilmeniz için bekleyen anahtar çiftiyle birleştirilebilir. Bu yöntem daha fazla adım gerektirse de, özel anahtar ' de oluşturulduğundan ve Key Vault kısıtlandığından daha fazla güvenlik sağlar. Bu, aşağıdaki diyagramda açıklanmaktadır.  

![Kendi sertifika yetkilinizle bir sertifika oluşturun](../media/certificate-authority-1.png)  

Aşağıdaki açıklamalar, önceki diyagramdaki yeşil bir şekilde açıklanan adımlara karşılık gelir.

1. Yukarıdaki diyagramda, uygulamanız anahtar kasanızda bir anahtar oluşturarak dahili olarak başlayan bir sertifika oluşturmaktadır.
2. Key Vault, uygulamanıza bir sertifika Imzalama Isteği (CSR) döndürür
3. Uygulamanız CSR'yi seçtiğiniz CA'ya geçirir.
4. Seçtiğiniz CA 'nız bir x509 sertifikası ile yanıt veriyor.
5. Uygulamanız, CA 'nızdan x509 sertifikasının birleşmesi ile yeni sertifika oluşturmayı tamamlar.

-   **Bilinen bir veren sağlayıcısına sahip bir sertifika oluşturun:** Bu yöntem, veren nesne oluşturmanın tek seferlik bir görevini yapmanızı gerektirir. Anahtar kasasında bir veren nesnesi oluşturulduktan sonra, adı KV sertifikası ilkesinde bu ada başvurulabilir. Bu tür bir KV sertifikası oluşturma isteği kasada bir anahtar çifti oluşturur ve bir x509 sertifikası almak için başvurulan veren nesnesindeki bilgileri kullanarak veren sağlayıcı hizmeti ile iletişim kurar. X509 sertifikası, Issuer hizmetinden alınır ve KV sertifikası oluşturma işleminin tamamlanabilmesi için anahtar çiftiyle birleştirilir.  

![Key Vault iş ortağı sertifika yetkilisi ile sertifika oluşturma](../media/certificate-authority-2.png)  

Aşağıdaki açıklamalar, önceki diyagramdaki yeşil bir şekilde açıklanan adımlara karşılık gelir.

1. Yukarıdaki diyagramda, uygulamanız anahtar kasanızda bir anahtar oluşturarak dahili olarak başlayan bir sertifika oluşturmaktadır.
2. Key Vault CA 'ya bir TLS/SSL sertifika Isteği gönderir.
3. Uygulamanız bir döngü ve bekleme sürecinde sertifikanın tamamlanıp tamamlanmadığını öğrenmek için Anahtar Kasanızı sorgular. Anahtar Kasası x509 sertifikasıyla CA yanıtını aldığında sertifika oluşturma işlemi tamamlanır.
4. CA, TLS/SSL X. 509.440 sertifikasıyla Key Vault TLS/SSL sertifikası Isteğine yanıt verir.
5. Yeni sertifika oluşturma, CA için TLS/SSL X. 509.440 sertifikasının birleşmesi ile tamamlanır.

## <a name="asynchronous-process"></a>Zaman uyumsuz işlem
KV sertifika oluşturma zaman uyumsuz bir işlemdir. Bu işlem bir KV sertifika isteği oluşturur ve 202 (kabul edildi) http durum kodunu döndürür. İsteğin durumu, bu işlem tarafından oluşturulan bekleyen nesne yoklanarak izlenebilir. Bekleyen nesnenin tam URI 'SI konum üst bilgisinde döndürülür.  

KV sertifikası oluşturma isteği tamamlandığında, bekleyen nesnenin durumu "tamamlandı" olarak değişir ve KV sertifikasının yeni bir sürümü oluşturulur. Bu, geçerli sürüm olacak.  

## <a name="first-creation"></a>İlk oluşturma
 Bir KV sertifikası ilk kez oluşturulduğunda, sertifikayla aynı ada sahip bir adreslenebilir anahtar ve gizli dizi da oluşturulur. Ad zaten kullanımda ise, işlem 409 (çakışma) http durum koduyla başarısız olur.
Adreslenebilir anahtar ve gizli anahtarı, KV sertifika özniteliklerinden özniteliklerini alır. Bu şekilde oluşturulan adreslenebilir anahtar ve gizli dizi, yaşam süresi Key Vault tarafından yönetilen yönetilen anahtarlar ve parolalar olarak işaretlenir. Yönetilen Anahtarlar ve gizlilikler salt okunurdur. Note: bir KV sertifikasının süresi dolarsa veya devre dışıysa, ilgili anahtar ve gizli dizi çalışamaz hale gelir.  

 Bu, bir KV sertifikası oluşturmaya yönelik ilk işlem ise bir ilke gereklidir.  İlke kaynağını değiştirmek için art arda oluşturma işlemlerinde bir ilke de sağlanabilir. Bir ilke sağlanmazsa, hizmet üzerindeki ilke kaynağı KV sertifikasının sonraki bir sürümünü oluşturmak için kullanılır. Bir sonraki sürüm oluşturma isteği, geçerli KV sertifikası ve karşılık gelen adreslenebilir anahtar ve gizli dizi gibi bir işlem devam ederken, değişmeden kaldığını unutmayın.  

## <a name="self-issued-certificate"></a>Kendi kendine verilen sertifika
 Kendi kendine verilen bir sertifika oluşturmak için, sertifika ilkesinde aşağıdaki kod parçacığında gösterildiği gibi verenin adını sertifika ilkesinde "Self" olarak ayarlayın.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Verenin adı belirtilmemişse, verenin adı "bilinmiyor" olarak ayarlanır. Veren "Unknown" olduğunda, sertifika sahibinin kendi tercih etenden bir x509 sertifikasını el ile alması ve sonra da sertifika oluşturmayı tamamlaması için ortak x509 sertifikasını Anahtar Kasası sertifikası bekleyen nesnesiyle birleştirmesi gerekir.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>İş ortağı CA sağlayıcıları
Sertifika oluşturma el ile veya "kendi" veren kullanılarak tamamlanabilir. Ayrıca, sertifika oluşturmayı basitleştirmek için belirli veren sağlayıcılarla iş ortakları Key Vault. Aşağıdaki sertifika türleri, bu iş ortağı veren sağlayıcılarıyla Anahtar Kasası için sıralanmış olabilir.  

|Sağlayıcı|Sertifika türü|Yapılandırma kurulumu  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault DigiCert ile OV veya EV SSL sertifikaları sunar| [Tümleştirme Kılavuzu](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault GlobalSign ile OV veya EV SSL sertifikaları sunar| [Tümleştirme Kılavuzu](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Sertifika veren, Azure Key Vault (KV) ile bir Certificateıssuer kaynağı olarak temsil edilen bir varlıktır. Bir KV sertifikasının kaynağı hakkında bilgi sağlamak için kullanılır; verenin adı, sağlayıcı, kimlik bilgileri ve diğer yönetim ayrıntıları.

Bir sipariş veren sağlayıcıya yerleştirildiğinde, sertifika türüne bağlı olarak x509 sertifika uzantılarını ve sertifika geçerlilik süresini kabul edebilir veya geçersiz kılabilir.  

 Yetkilendirme: sertifikalar/oluşturma izni gerektirir.

## <a name="see-also"></a>Ayrıca Bkz.

 - [Sertifika oluşturmayı izleme ve yönetme](create-certificate-scenarios.md)