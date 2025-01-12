---
title: .NET-Azure depolama ile bir blob kapsayıcısı oluşturma veya silme
description: .NET istemci kitaplığını kullanarak Azure depolama hesabınızda bir blob kapsayıcısı oluşturmayı veya silmeyi öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576456"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>.NET ile Azure depolama 'da kapsayıcı oluşturma veya silme

Azure depolama 'daki Bloblar kapsayıcılar halinde düzenlenir. Bir blobu karşıya yüklemeden önce, önce bir kapsayıcı oluşturmanız gerekir. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)ile kapsayıcıları oluşturma ve silme işlemlerinin nasıl yapılacağı gösterilmektedir.

## <a name="name-a-container"></a>Bir kapsayıcıyı adlandırın

Kapsayıcı adı, kapsayıcıyı veya bloblarını ele almak için kullanılan benzersiz URI 'nin bir parçasını oluşturan geçerli bir DNS adı olmalıdır. Bir kapsayıcıyı adlandırırken bu kuralları izleyin:

- Kapsayıcı adları 3 ila 63 karakter uzunluğunda olabilir.
- Kapsayıcı adları bir harf veya sayıyla başlamalıdır ve yalnızca küçük harf, rakam ve tire (-) karakterini içerebilir.
- Kapsayıcı adlarında iki veya daha fazla ardışık çizgi karakterine izin verilmez.

Bir kapsayıcının URI 'SI şu biçimdedir:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı oluşturmak için aşağıdaki yöntemlerden birini çağırın:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [Createblobcontainsilinebilir eşitleme](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Aynı ada sahip bir kapsayıcı zaten varsa, bu yöntemler bir özel durum oluşturur.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Oluştur](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Createıfnotexists çağırmanız](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Aynı ada sahip bir kapsayıcı zaten varsa **Create** ve **createasync** yöntemleri bir özel durum oluşturur.

**Createifnotexists** ve **Createifnotexistsasync** yöntemleri, kapsayıcının oluşturulup oluşturulmayacağını gösteren bir Boole değeri döndürür. Aynı ada sahip bir kapsayıcı zaten varsa, bu yöntemler yeni bir kapsayıcının oluşturulamadığını göstermek için **false** döndürür.

---

Kapsayıcılar depolama hesabının hemen altında oluşturulur. Bir kapsayıcıyı diğerinin altına yerleştirmek mümkün değildir.

Aşağıdaki örnek zaman uyumsuz olarak bir kapsayıcı oluşturur:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Kök kapsayıcısını oluşturma

Kök kapsayıcı, depolama hesabınız için varsayılan kapsayıcı görevi görür. Her depolama hesabının bir kök kapsayıcısı olabilir ve bu *$root* adı verilmelidir. Kök kapsayıcısının açık olarak oluşturulması veya silinmesi gerekir.

Kök kapsayıcı adını dahil etmeden kök kapsayıcısında depolanan bir blob 'a başvurabilirsiniz. Kök kapsayıcı, depolama hesabı hiyerarşisinin en üst düzeyindeki bir bloba başvurmanızı sağlar. Örneğin, kök kapsayıcıda aşağıdaki şekilde olan bir bloba başvurabilirsiniz:

`https://myaccount.blob.core.windows.net/default.html`

Aşağıdaki örnek, eş zamanlı olarak kök kapsayıcısını oluşturur:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Kapsayıcı silme

.NET 'teki bir kapsayıcıyı silmek için aşağıdaki yöntemlerden birini kullanın:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

- [Silme](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [Deleteıfexists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [Deleteıfexistsasync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Silme](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [Deleteıfexists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [Deleteıfexistsasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

**Delete** ve **DeleteAsync** yöntemleri, kapsayıcı yoksa bir özel durum oluşturur.

**Deleteifexists** ve **Deleteifvartsasync** yöntemleri kapsayıcının silinip silinmediğini gösteren bir Boole değeri döndürür. Belirtilen kapsayıcı yoksa, bu yöntemler kapsayıcının silinmediğini göstermek için **false** döndürür.

Bir kapsayıcıyı sildikten sonra, *en az* 30 saniye için aynı ada sahip bir kapsayıcı oluşturamazsınız. Aynı ada sahip bir kapsayıcı oluşturulmaya çalışılması, HTTP hata kodu 409 (çakışma) ile başarısız olur. Kapsayıcıda veya içerdiği bloblarda bulunan diğer işlemler, HTTP hata kodu 404 (bulunamadı) ile başarısız olur.

Aşağıdaki örnek, belirtilen kapsayıcıyı siler ve kapsayıcı yoksa özel durumu işler:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

Aşağıdaki örnek, belirtilen bir önek ile başlayan tüm kapsayıcıların nasıl silineceğini gösterir.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Kapsayıcı oluşturma işlemi](/rest/api/storageservices/create-container)
- [Kapsayıcıyı Silme işlemi](/rest/api/storageservices/delete-container)
