---
title: 在 Azure 存储中存储和访问数据 Xamarin.Forms
description: Azure 存储是一种可缩放的云存储解决方案，可用于存储非结构化和结构化数据。 本文介绍如何使用 Xamarin.Forms 在 Azure 存储中存储文本和二进制数据，以及如何访问数据。
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cba4c670e9e092eef92f7b37eefc750782c94367
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563830"
---
# <a name="store-and-access-data-in-azure-storage-from-no-locxamarinforms"></a>在 Azure 存储中存储和访问数据 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Azure 存储是一种可缩放的云存储解决方案，可用于存储非结构化和结构化数据。本文演示如何使用 Xamarin.Forms 在 Azure 存储中存储文本和二进制数据，以及如何访问数据。_

Azure 存储提供以下四种存储服务：

- Blob 存储。 Blob 可以是文本或二进制数据，如备份、虚拟机、媒体文件或文档。
- 表存储是一个 NoSQL 键-特性存储。
- 队列存储是用于工作流处理和云服务之间的通信的消息传递服务。
- 文件存储使用 SMB 协议提供共享存储。

有两种类型的存储帐户：

- 常规用途的存储帐户通过单一帐户提供对 Azure 存储服务的访问权限。
- Blob 存储帐户是用于存储 blob 的专用存储帐户。 如果只需要存储 blob 数据，则建议使用此帐户类型。

本文和随附的示例应用程序演示了如何将图像和文本文件上传到 blob 存储，以及如何下载它们。 此外，它还演示了如何从 blob 存储中检索文件列表和删除文件。

有关 Azure 存储的详细信息，请参阅 [存储简介](https://azure.microsoft.com/documentation/articles/storage-introduction/)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

## <a name="introduction-to-blob-storage"></a>Blob 存储简介

Blob 存储由三个组件组成，如下图所示：

![Blob 存储概念](azure-storage-images/blob-storage.png)

对 Azure 存储的所有访问都是通过存储帐户进行的。 一个存储帐户可以包含无限数量的容器，一个容器可以存储无限数量的 blob，直至达到存储帐户的容量限制。

Blob 是任意类型和大小的文件。 Azure 存储支持三种不同的 blob 类型：

- 块 blob 经过优化，可用于流式传输和存储云对象，并且是用于存储备份、媒体文件、文档等的一个不错选择。块 blob 的大小最大可为195Gb。
- 追加 blob 类似于块 blob，但针对追加操作（例如日志记录）进行了优化。 追加 blob 最大可为195Gb。
- 页 blob 针对频繁的读取/写入操作进行了优化，通常用于存储虚拟机及其磁盘。 页 blob 最大可为 1 Tb。

> [!NOTE]
> 请注意，blob 存储帐户支持块 blob 和追加 blob，而不支持页 blob。

将 blob 上传到 Azure 存储，并以字节流的形式从 Azure 存储空间下载。 因此，在上传之前必须将文件转换为字节流，并在下载后转换回其原始表示形式。

存储在 Azure 存储中的每个对象都有唯一的 URL 地址。 存储帐户名称构成该地址的子域，子域和域名的组合构成存储帐户的 *终结点* 。 例如，如果你的存储帐户名为 *mystorageaccount*，则存储帐户的默认 blob 终结点为 `https://mystorageaccount.blob.core.windows.net` 。

用于访问存储帐户中某个对象的 URL 是通过将存储帐户中对象的位置附加到终结点而构建的。 例如，blob 地址的格式为 `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` 。

## <a name="setup"></a>设置

将 Azure 存储帐户集成到应用程序的过程 Xamarin.Forms 如下所示：

1. 创建存储帐户。 有关详细信息，请参阅[创建存储帐户](/azure/storage/common/storage-account-create#create-a-storage-account)。
1. 将 [Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/) 添加到 Xamarin.Forms 应用程序。
1. 配置存储连接字符串。 有关详细信息，请参阅 [连接到 Azure 存储](#connecting-to-azure-storage)。
1. 将 `using` `Microsoft.WindowsAzure.Storage` 和命名空间的指令添加 `Microsoft.WindowsAzure.Storage.Blob` 到将访问 Azure 存储的类。

## <a name="connecting-to-azure-storage"></a>连接到 Azure 存储

针对存储帐户资源发出的每个请求都必须经过身份验证。 虽然可以将 blob 配置为支持匿名身份验证，但应用程序可使用两种主要方法来向存储帐户进行身份验证：

- 共享密钥。 此方法使用 Azure 存储帐户名称和帐户密钥访问存储服务。 创建存储帐户时，将为其分配两个可用于共享密钥身份验证的私钥。
- 共享访问签名。 这是一个标记，可将其追加到 URL，该 URL 允许在有效的时间段内对存储资源进行委托访问。

可以指定连接字符串，其中包括从应用程序访问 Azure 存储资源所需的身份验证信息。 此外，可以将连接字符串配置为从 Visual Studio 连接到 Azure 存储模拟器。

> [!NOTE]
> Azure 存储支持在连接字符串中使用 HTTP 和 HTTPS。 但建议使用 HTTPS。

### <a name="connecting-to-the-azure-storage-emulator"></a>连接到 Azure 存储模拟器

Azure 存储模拟器提供了一个模拟 Azure blob、队列和表服务以进行开发的本地环境。

应使用以下连接字符串连接到 Azure 存储模拟器：

```csharp
UseDevelopmentStorage=true
```

有关 Azure 存储模拟器的详细信息，请参阅 [使用 azure 存储模拟器进行开发和测试](/azure/storage/common/storage-use-emulator)。

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>使用共享密钥连接到 Azure 存储

应使用以下连接字符串格式通过共享密钥连接到 Azure 存储：

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` 应该替换为您的存储帐户的名称，并且 `myAccountKey` 应替换为您的两个帐户访问密钥之一。

> [!NOTE]
> 使用共享密钥身份验证时，你的帐户名称和帐户密钥将分发给使用你的应用程序的每个用户，这将提供对存储帐户的完全读/写访问权限。 因此，请使用共享密钥身份验证仅用于测试目的，而永远不会将密钥分发给其他用户。

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>使用共享访问签名连接到 Azure 存储

应使用以下连接字符串格式通过 SAS 连接到 Azure 存储：

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` 应该替换为你的 blob 终结点的 URL，并 `mySharedAccessSignature` 应替换为你的 SAS。 SAS 提供协议、服务终结点和用于访问资源的凭据。

> [!NOTE]
> 对于生产应用程序，建议使用 SAS 身份验证。 但是，在生产应用程序中，应根据需要从后端服务检索 SAS，而不是将其与应用程序捆绑在一起。

有关共享访问签名的详细信息，请参阅 [使用共享访问签名 (SAS) ](/azure/storage/common/storage-sas-overview)。

## <a name="creating-a-container"></a>创建容器

`GetContainer`方法用于检索对命名容器的引用，然后可以使用该容器从容器检索 blob 或将 blob 添加到容器中。 下面的代码示例说明 `GetContainer` 方法：

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse`方法分析连接字符串并返回 `CloudStorageAccount` 表示存储帐户的实例。 `CloudBlobClient`用于检索容器和 blob 的实例随后由 `CreateCloudBlobClient` 方法创建。 方法在将 `GetContainerReference` 指定的容器 `CloudBlobContainer` 返回到调用方法之前，将该容器作为实例检索。 在此示例中，容器名称是 `ContainerType` 转换为小写字符串的枚举值。

> [!NOTE]
> 容器名称必须是小写，且必须以字母或数字开头。 此外，它们只能包含字母、数字和短划线字符，长度必须介于3到63个字符之间。

`GetContainer`调用方法，如下所示：

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer`如果容器尚不存在，则可以使用该实例来创建它：

```csharp
await container.CreateIfNotExistsAsync();
```

默认情况下，新创建的容器是专用的。 这意味着，必须指定存储访问密钥才能从容器检索 blob。 有关在容器中公开 blob 的详细信息，请参阅 [创建容器](/azure/storage/blobs/storage-quickstart-blobs-dotnet#create-a-container)。

## <a name="uploading-data-to-a-container"></a>将数据上传到容器

`UploadFileAsync`方法用于将字节数据流上传到 blob 存储，如以下代码示例所示：

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

检索容器引用后，如果容器尚不存在，则该方法将创建该容器。 然后，将创建一个新的 `Guid` 来充当唯一的 blob 名称，并以实例的形式检索 blob 块引用 `CloudBlockBlob` 。 然后，使用方法将数据流上传到 blob `UploadFromStreamAsync` ，该方法会创建 blob （如果该 blob 尚不存在），或者覆盖该 blob （如果该 blob 存在）。

必须先将文件转换为字节流，然后才能使用此方法将文件上载到 blob 存储。 下面的代码示例演示了这一点：

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text`数据将转换为字节数组，并将其包装为传递给方法的流 `UploadFileAsync` 。

## <a name="downloading-data-from-a-container"></a>从容器下载数据

`GetFileAsync`方法用于从 Azure 存储下载 blob 数据，如以下代码示例所示：

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

检索容器引用后，方法将检索存储数据的 blob 引用。 如果 blob 存在，则方法将检索其属性 `FetchAttributesAsync` 。 创建了正确大小的字节数组，并将 blob 作为返回到调用方法的字节数组下载。

下载 blob 字节数据后，必须将其转换为其原始表示形式。 下面的代码示例演示了这一点：

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

在将字节 `GetFileAsync` 转换回 UTF8 编码的字符串之前，通过方法从 Azure 存储中检索字节数组。

## <a name="listing-data-in-a-container"></a>列出容器中的数据

`GetFilesListAsync`方法用于检索容器中存储的 blob 列表，如下面的代码示例所示：

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

检索容器引用后，方法使用容器的 `ListBlobsSegmentedAsync` 方法来检索对容器中的 blob 的引用。 如果 `ListBlobsSegmentedAsync` 实例不是，则枚举方法返回的结果 `BlobContinuationToken` `null` 。 `IListBlobItem` `CloudBlockBlob` 在将 `Name` 值添加到集合之前，每个 blob 都将从返回的转换为，以便访问 blob 的属性 `allBlobsList` 。 一旦 `BlobContinuationToken` 实例为 `null` ，则返回最后一个 blob 名称，并且执行将退出该循环。

## <a name="deleting-data-from-a-container"></a>删除容器中的数据

`DeleteFileAsync`方法用于从容器中删除 blob，如以下代码示例所示：

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

检索容器引用后，方法将检索指定 blob 的 blob 引用。 然后，将用方法删除该 blob `DeleteIfExistsAsync` 。

## <a name="related-links"></a>相关链接

- [Azure 存储 (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [存储简介](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [如何通过 Xamarin 使用 Blob 存储](/azure/storage/blobs/storage-quickstart-blobs-xamarin)
- [使用共享访问签名 (SAS)](/azure/storage/common/storage-sas-overview)
- [Windows Azure 存储 (NuGet) ](https://www.nuget.org/packages/WindowsAzure.Storage/)