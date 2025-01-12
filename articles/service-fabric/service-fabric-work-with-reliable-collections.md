---
title: Güvenilir Koleksiyonlar ile çalışma
description: Azure Service Fabric uygulamasında güvenilir Koleksiyonlar ile çalışmaya yönelik en iyi uygulamaları öğrenin.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787884"
---
# <a name="working-with-reliable-collections"></a>Güvenilir Koleksiyonlar ile çalışma
Service Fabric, güvenilir koleksiyonlar aracılığıyla .NET geliştiricileri için kullanılabilen bir durum bilgisi olan programlama modeli sunar. Özellikle, Service Fabric güvenilir sözlük ve güvenilir sıra sınıfları sağlar. Bu sınıfları kullandığınızda, durumunuzun bölümlenmesi (ölçeklenebilirlik için), çoğaltılan (kullanılabilirlik için) ve bir bölüm içinde (ACID semantiği için) işlem gerçekleştirilmiş olur. Güvenilir bir sözlük nesnesinin tipik bir kullanımına bakalım ve gerçekten ne yaptığını görelim.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
   await Task.Delay(100);
}
```

Güvenilir sözlük nesnelerindeki tüm işlemler (geri alınamaz olan ClearAsync hariç), bir ITransaction nesnesi gerektirir. Bu nesne kendisiyle ilişkilendirilir ve tüm güvenilir sözlükte ve/veya güvenilir kuyruk nesnelerinde tek bir bölüm içinde yapmaya çalıştığınız tüm değişiklikler ile ilişkilendirilmiştir. Bölümün StateManager's CreateTransaction yöntemini çağırarak bir ITransaction nesnesi elde edersiniz.

Yukarıdaki kodda, ITransaction nesnesi güvenilir bir sözlüğün Addadsync yöntemine geçirilir. Dahili olarak, anahtar kabul eden sözlük yöntemleri, anahtarla ilişkili bir okuyucu/yazıcı kilidi alır. Yöntem anahtarın değerini değiştirirse, yöntemi anahtar üzerinde bir yazma kilidi alır ve yöntem yalnızca anahtarın değerinden okunursa, anahtar üzerinde bir okuma kilidi yapılır. Addadsync anahtarın değerini yeni, geçirilen değerine değiştirdiğinde, anahtarın yazma kilidi alınır. Bu nedenle, 2 (veya daha fazla) iş parçacığı aynı anda aynı anahtarla değer eklemeye çalışır, bir iş parçacığı yazma kilidi elde eder ve diğer iş parçacıkları da engeller. Varsayılan olarak, Yöntemler kilidi almak için 4 saniyeye kadar blok; 4 saniye sonra Yöntemler bir TimeoutException oluşturur. Yöntem aşırı yüklemeleri, tercih ediyorsanız açık bir zaman aşımı değeri geçirmenize olanak sağlar.

Genellikle, bir TimeoutException 'e yanıt vererek ve tüm işlemi yeniden denemeden (Yukarıdaki kodda gösterildiği gibi) kodu yazarsınız. Bu basit kodda yalnızca görevi çağırıyoruz. her seferinde 100 milisaniyeyi geçen gecikme. Ancak gerçekte, bunun yerine bazı üstel geri dönme gecikmesini kullanarak daha iyi kapatılabilir.

Kilit alındıktan sonra, Addadsync, ITransaction nesnesiyle ilişkili bir iç geçici sözlüğe anahtar ve değer nesnesi başvurularını ekler. Bu, size salt yazılır yazma semantiğini sağlamak için yapılır. Diğer bir deyişle, Addadsync öğesini çağırdıktan sonra, aynı ITransaction nesnesini kullanarak TryGetValueAsync 'e daha sonraki bir çağrı, işlemi henüz kaydetmemiş olsanız bile değeri döndürür.

> [!NOTE]
> TryGetValueAsync 'i yeni bir işlemle çağırmak, son kaydedilen değere bir başvuru döndürür. Bu başvuruyu doğrudan değiştirmeyin, çünkü bu başvuruyu kalıcı hale getirme ve değişiklikleri çoğaltma mekanizmasını atlar. Bir anahtarın değerini değiştirmek için tek yol güvenilir sözlük API 'Leri aracılığıyla, değerlerin salt okunurlanmasını öneririz.

Sonra, Addadsync, anahtar ve değer nesnelerinizi bayt dizileri olarak serileştirir ve bu bayt dizilerini yerel düğümdeki bir günlük dosyasına ekler. Son olarak, Addadsync, bayt dizilerini aynı anahtar/değer bilgilerine sahip olacak şekilde tüm ikincil çoğaltmalara gönderir. Anahtar/değer bilgileri bir günlük dosyasına yazılsa da, ilişkilendirildikleri işlem kaydedilene kadar bilgiler sözlüğün bir parçası olarak kabul edilmez.

Yukarıdaki kodda, Commınvosync çağrısı tüm işlem işlemlerini kaydeder. Özellikle, kaydetme bilgilerini yerel düğümdeki günlük dosyasına ekler ve ayrıca, kaydetme kaydını tüm ikincil çoğaltmalara gönderir. Çoğaltmaların bir çekirdeği (çoğunluğu) Yanıtlandıktan sonra, tüm veri değişiklikleri kalıcı olarak kabul edilir ve ITransaction nesnesi aracılığıyla yönetilen anahtarlarla ilişkili kilitler, diğer iş parçacıkları/işlemler aynı anahtarları ve değerlerini işleyebilir.

Commction eşitleme çağrılmadığından (genellikle bir özel durum nedeniyle), ITransaction nesnesi atılır. İşlenmemiş bir ITransaction nesnesini elden atılırken Service Fabric, iptal bilgilerini yerel düğümün günlük dosyasına ekler ve hiçbir şeyin ikincil çoğaltmalardan herhangi birine gönderilmesi gerekmez. Böylece, işlem aracılığıyla yönetilen anahtarlarla ilişkili kilitler serbest bırakılır.

## <a name="volatile-reliable-collections"></a>Geçici güvenilir Koleksiyonlar 
Örneğin, çoğaltılan bir önbellek gibi bazı iş yüklerinde, zaman zaman veri kaybı toleranslı olabilir. Verilerin diske kalıcılığın önlenmemesi, güvenilir sözlüklere yazarken daha iyi gecikme süreleri ve geçiş için izin verebilir. Kalıcılık olmaması için zorunluluğunu getirir, çekirdek kaybı gerçekleşirse tam veri kaybı meydana gelir. Çekirdek kaybı nadir bir oluşum olduğundan, yüksek performans, bu iş yükleri için nadiren veri kaybı olasılığını ortadan kaldırmaya devam edebilir.

Şu anda geçici destek yalnızca güvenilir sözlüklerde ve güvenilir sıralarda bulunur ve bu, ReliableConcurrentQueues değildir. Geçici koleksiyonları kullanıp kullanmayacağınızı kararlayaöğrenmek için lütfen [Uyarılar](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) listesine bakın.

Hizmetinizde geçici desteği etkinleştirmek için, ```HasPersistedState``` hizmet türü bildiriminde bayrağını ```false``` şöyle ayarlayın:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Var olan kalıcı hizmetler geçici yapılamaz ve tam tersi de geçerlidir. Bunu yapmak istiyorsanız, mevcut hizmeti silmeniz ve ardından hizmeti güncelleştirilmiş bayrağıyla dağıtmanız gerekecektir. Bu, bayrağını değiştirmek istiyorsanız, tam veri kaybı olması gerektiği anlamına gelir ```HasPersistedState``` . 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Ortak ve nasıl kaçınacak
Artık güvenilir koleksiyonların dahili olarak nasıl çalıştığını anladığınıza göre, bunların bazı yaygın kötüye kullanımlarına göz atalım. Aşağıdaki koda bakın:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Normal bir .NET sözlüğü ile çalışırken, sözlüğe bir anahtar/değer ekleyebilir ve sonra bir özelliğin değerini (LastLogin gibi) değiştirebilirsiniz. Ancak, bu kod güvenilir bir sözlükle doğru çalışmaz. Önceki tartışmayı unutmayın, Addadsync çağrısı, anahtar/değer nesnelerini bayt dizileri olarak serileştirir ve sonra dizileri yerel bir dosyaya kaydeder ve ayrıca bunları ikincil çoğaltmalara gönderir. Daha sonra bir özelliği değiştirirseniz, bu özellik yalnızca bellekteki değeri değiştirir; Yerel dosyayı veya çoğaltmalara gönderilen verileri etkilemez. İşlem kilitlenirse, bellekte bulunan özellikler oluşur. Yeni bir işlem başladığında veya başka bir çoğaltma birincil hale gelirse, eski Özellik değeri kullanılabilir değildir.

Yukarıda gösterilen hata türünü ne kadar kolay bir şekilde vurgulamıyorum. Ve yalnızca işlemin ne zaman/zaman aşağı gidecağından oluşan hata hakkında bilgi edineceksiniz. Kodu yazmanın doğru yolu, iki satırı tersine çevirmek için yeterlidir:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Yaygın bir hata gösteren başka bir örnek aşağıda verilmiştir:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Normal .NET sözlükleriyle, yukarıdaki kod ince çalışır ve ortak bir modeldir: geliştirici bir değer aramak için bir anahtar kullanır. Değer varsa, geliştirici bir özelliğin değerini değiştirir. Bununla birlikte, güvenilir Koleksiyonlar ile, bu kod zaten tartışılan ile aynı sorunu sergiler: **güvenilir bir koleksiyona verdiğiniz bir nesneyi değiştirmemelidir.**

Güvenilir bir koleksiyondaki bir değeri güncelleştirmenin doğru yolu, var olan değere bir başvuru almak ve bu başvuru ile başvurulan nesneyi sabit olarak kabul etmek için kullanılır. Ardından, özgün nesnenin tam bir kopyası olan yeni bir nesne oluşturun. Şimdi, bu yeni nesnenin durumunu değiştirebilir ve yeni nesneyi koleksiyona, yerel dosyaya eklenerek ve çoğaltmalara gönderilmek üzere, bayt dizilerine seri hale getirilebilmesi için yazabilirsiniz. Değişiklik (ler) kaydedildikten sonra, bellek içi nesneler, yerel dosya ve tüm çoğaltmalar aynı duruma sahiptir. Hepsi iyi!

Aşağıdaki kod, güvenilir bir koleksiyondaki bir değeri güncelleştirmenin doğru yolunu göstermektedir:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Programcı hatasını engellemek için değişmez veri türlerini tanımlayın
İdeal olarak, sabit olarak göz önünde bulundurmanız gereken bir nesnenin durumunu değiştirmede yanlışlıkla kod oluşturduğunuzda derleyicinin hataları raporoluşturmasını istiyoruz. Ancak, C# derleyicisinin bunu yapma yeteneği yoktur. Bu nedenle, potansiyel programcı hatalarından kaçınmak için, güvenilir koleksiyonlarla kullandığınız türleri değişmez türler olarak tanımlamanızı öneririz. Bu, özellikle de temel değer türlerini (sayılar [Int32, UInt64, vs.], DateTime, Guid, TimeSpan ve benzeri) gösteren bir anlamına gelir. Dizeyi de kullanabilirsiniz. Koleksiyon özelliklerinin serileştirilmesi ve seri durumdan kaldırılması en iyisidir. Ancak, koleksiyon özelliklerini kullanmak istiyorsanız, kullanımı önemle önerilir. NET 'in değişmez koleksiyonlar kitaplığı ([System. Collections. sabit](https://www.nuget.org/packages/System.Collections.Immutable/)). Bu kitaplık, ' den indirileceği için kullanılabilir https://nuget.org . Ayrıca, sınıflarınızı mühürleyen ve alanları mümkün olduğunca Salt okunabilir hale getirmeyi öneririz.

Aşağıdaki UserInfo türü, belirtilen önerilerden faydalanan sabit bir türün nasıl tanımlanacağını göstermektedir.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId türü Ayrıca, burada gösterildiği gibi sabit bir türdür:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Şema sürümü oluşturma (Yükseltmeler)
Dahili olarak, güvenilir Koleksiyonlar kullanarak nesnelerinizi seri hale getirme. NET ' in DataContractSerializer. Serileştirilmiş nesneler birincil çoğaltmanın yerel diskine kalıcı hale getirilir ve ayrıca ikincil çoğaltmalara iletilir. Hizmetiniz sizin için gerekli olan veri türünü (şema) değiştirmek isteyebilirsiniz. Harika bir sorun ile verilerinizin sürümü oluşturma yaklaşımı. İlk ve daha sonra, her zaman eski verileri seri durumdan çıkartabilmeniz gerekir. Bu, seri durumdan çıkarma kodunuzun sınırsız bir şekilde geriye dönük olarak uyumlu olması gerektiği anlamına gelir: hizmet kodunuzun sürüm 333, hizmet kodunuzun 5 yıl önce sürüm 1 ' i tarafından güvenilir bir koleksiyona yerleştirilmiş verilerle çalışabilebilmelidir.

Ayrıca, hizmet kodu tek seferde bir yükseltme etki alanı yükseltilir. Bu nedenle, bir yükseltme sırasında hizmet kodunuzun iki farklı sürümü aynı anda çalışır. Hizmet kodunuzun yeni sürümü yeni şemayı kullanarak, hizmet kodunuzun eski sürümleri yeni şemayı işleyemeyebilir. Mümkün olduğunda, hizmetinizin her sürümünü, bir sürüm ile ileri doğru uyumlu olacak şekilde tasarlamanız gerekir. Özellikle, bu, hizmet kodunuzun v1 açıkça işlemediği herhangi bir şema öğesini yoksaymanız gerektiği anlamına gelir. Ancak, bir sözlük anahtarını veya değerini güncelleştirirken, açıkça bilmeyen ve geri yazamayacak verileri kaydedebilmelidir.

> [!WARNING]
> Bir anahtarın şemasını değiştiremeyeceğiniz sürece, anahtarınızın karma kodu ve eşittir algoritmalarının kararlı olduğundan emin olmanız gerekir. Bu algoritmalardan birinin çalışmasını değiştirirseniz, anahtarı güvenilir sözlük içinde yeniden aramanız mümkün olmayacaktır.
> .NET dizeleri anahtar olarak kullanılabilir ancak dizenin kendisini anahtar olarak kullanın; anahtar olarak String. GetHashCode sonucunu kullanmayın.

Alternatif olarak, genellikle iki yükseltme olarak adlandırılan işlemleri yapabilirsiniz. İki aşamalı bir yükseltme sayesinde, hizmetinizi v1 'den v2 'ye yükseltirsiniz: v2 yeni şema değişikliğine nasıl uğratacağını bilen kodu içerir, ancak bu kod yürütülmez. V2 kodu v1 verilerini okuduğunda, üzerinde çalışır ve v1 verilerini yazar. Yükseltme tamamlandıktan sonra, yükseltme tüm etki alanlarında tamamlandıktan sonra, yükseltmenin tamamlandığı çalışan v2 örneklerine bir uyarı verebilirsiniz. (Bunu işaret etmenin bir yolu, bir yapılandırma yükseltmesini kullanıma sunmanız; bu, bu iki aşamalı yükseltme yapmayı sağlar.) Artık v2 örnekleri v1 verilerini okuyabilir, v2 verilerine dönüştürebilir, üzerinde işlem yapabilir ve V2 verisi olarak yazabilir. Diğer örnekler v2 verilerini okuduklarında, bunları dönüştürmeleri gerekmez, yalnızca üzerinde çalışır ve v2 verilerini yazar.

## <a name="next-steps"></a>Sonraki adımlar
İleri uyumlu veri sözleşmeleri oluşturma hakkında bilgi edinmek için bkz. [Ileri uyumlu veri sözleşmeleri](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Sürüm oluşturma veri sözleşmeleri hakkında en iyi yöntemleri öğrenmek için bkz. [veri sözleşmesi sürümü oluşturma](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Sürüm dayanıklı veri sözleşmeleri uygulama hakkında bilgi edinmek için bkz. [Sürüm dayanıklı serileştirme geri çağırmaları](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Birden çok sürümde birlikte çalışabilecek bir veri yapısını nasıl sağlayabileceğinizi öğrenmek için bkz. [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject)
