---
title: 在中使用 Azure Cosmos DB 文档数据库 Xamarin.Forms
description: 本文介绍如何使用 Azure Cosmos DB .NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序中。
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ef6ccbaac73946ac19f4f5fe194f395234226b5e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562842"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-no-locxamarinforms"></a>在中使用 Azure Cosmos DB 文档数据库 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Azure Cosmos DB 文档数据库是一种 NoSQL 数据库，可提供对 JSON 文档的低延迟访问，为需要无缝缩放和全局复制的应用程序提供快速、高度可用、可缩放的数据库服务。本文介绍如何使用 Azure Cosmos DB .NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序中。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB 视频**

可以使用 Azure 订阅预配 Azure Cosmos DB 文档数据库帐户。 每个数据库帐户可以有零个或多个数据库。 Azure Cosmos DB 中的文档数据库是文档集合和用户的逻辑容器。

Azure Cosmos DB 文档数据库可包含零个或多个文档集合。 每个文档集合都可以具有不同的性能级别，从而为经常访问的集合指定更多的吞吐量，并减少不常访问的集合的吞吐量。

每个文档集合包含零个或多个 JSON 文档。 集合中的文档是无架构的，因此不需要共享相同的结构或字段。 将文档添加到文档集合时，Cosmos DB 会自动为它们编制索引，并使它们可供查询。

出于开发目的，还可以通过模拟器使用文档数据库。 使用模拟器，可以在本地开发和测试应用程序，而无需创建 Azure 订阅，也不会产生任何费用。 有关模拟器的详细信息，请参阅 [在本地开发 Azure Cosmos DB 模拟器](/azure/cosmos-db/local-emulator/)。

本文和随附的示例应用程序演示了一个待办事项列表应用程序，其中的任务存储在 Azure Cosmos DB 的文档数据库中。 有关示例应用程序的详细信息，请参阅 [了解示例](~/xamarin-forms/data-cloud/web-services/introduction.md)。

有关 Azure Cosmos DB 的详细信息，请参阅 [Azure Cosmos DB 文档](/azure/cosmos-db/)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>设置

将 Azure Cosmos DB 文档数据库集成到应用程序的过程 Xamarin.Forms 如下所示：

1. 创建 Cosmos DB 帐户。 有关详细信息，请参阅 [创建 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account)。
1. 将 [Azure Cosmos DB .NET Standard 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) NuGet 包添加到解决方案中的平台项目 Xamarin.Forms 。
1. 向 `using` `Microsoft.Azure.Documents` `Microsoft.Azure.Documents.Client` `Microsoft.Azure.Documents.Linq` 将访问 Cosmos DB 帐户的类添加、和命名空间的指令。

执行这些步骤后，可以使用 Azure Cosmos DB .NET Standard 客户端库来针对文档数据库配置和执行请求。

> [!NOTE]
> Azure Cosmos DB .NET Standard 客户端库只能安装到平台项目中，而不能安装到可移植类库 (PCL) 项目。 因此，示例应用程序是 (SAP) 的共享访问项目，以避免代码重复。 但是， `DependencyService` 可以在 PCL 项目中使用类来调用特定于平台的项目中包含的 .NET Standard 客户端库代码 Azure Cosmos DB。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帐户

`DocumentClient`类型封装用于访问 Azure Cosmos DB 帐户的终结点、凭据和连接策略，并用于针对帐户配置和执行请求。 下面的代码示例演示如何创建此类的实例：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

必须为构造函数提供 Cosmos DB Uri 和主密钥 `DocumentClient` 。 可从 Azure 门户获取这些。 有关详细信息，请参阅 [连接到 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect)。

### <a name="creating-a-database"></a>创建数据库

文档数据库是文档集合和用户的逻辑容器，可以在 Azure 门户中创建，也可以使用方法以编程方式创建 `DocumentClient.CreateDatabaseIfNotExistsAsync` ：

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

`CreateDatabaseIfNotExistsAsync`方法将对象指定 `Database` 为参数， `Database` 对象将数据库名称指定为其 `Id` 属性。 `CreateDatabaseIfNotExistsAsync`如果数据库不存在，则该方法将创建数据库，如果数据库已存在，则返回该数据库。 但是，示例应用程序将忽略方法返回的任何数据 `CreateDatabaseIfNotExistsAsync` 。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法返回一个 `Task<ResourceResponse<Database>>` 对象，可以检查响应的状态代码以确定数据库是否已创建，或者是否返回了现有的数据库。

### <a name="creating-a-document-collection"></a>创建文档集合

文档集合是 JSON 文档的容器，可以在 Azure 门户中创建，也可以使用方法以编程方式创建 `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` ：

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

此 `CreateDocumentCollectionIfNotExistsAsync` 方法需要两个强制参数–指定为的数据库名称 `Uri` 和 `DocumentCollection` 对象。 `DocumentCollection`对象表示一个文档集合，其名称是使用属性指定的 `Id` 。 `CreateDocumentCollectionIfNotExistsAsync`如果文档集不存在，则该方法将创建它，如果文档集合已存在，则返回该集合。 但是，示例应用程序将忽略方法返回的任何数据 `CreateDocumentCollectionIfNotExistsAsync` 。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法返回一个 `Task<ResourceResponse<DocumentCollection>>` 对象，可以检查响应的状态代码，以确定文档集合是否已创建，或者是否返回了现有文档集。

此外，该 `CreateDocumentCollectionIfNotExistsAsync` 方法还可以指定一个 `RequestOptions` 对象，该对象封装可为颁发给 Cosmos DB 帐户的请求指定的选项。 `RequestOptions.OfferThroughput`属性用于定义文档集合的性能级别，在示例应用程序中，设置为每秒400请求单位数。 此值应增加或减少，具体取决于是否经常访问集合。

> [!IMPORTANT]
> 请注意，该 `CreateDocumentCollectionIfNotExistsAsync` 方法将创建具有保留吞吐量的新集合，这会产生定价影响。

### <a name="retrieving-document-collection-documents"></a>检索文档集合文档

可以通过创建和执行文档查询来检索文档集合的内容。 使用方法创建文档查询 `DocumentClient.CreateDocumentQuery` ：

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

此查询以异步方式检索指定集合中的所有文档，并将文档放入 `List<TodoItem>` 集合中以供显示。

`CreateDocumentQuery<T>`方法指定一个 `Uri` 参数，该参数表示应为文档查询的集合。 在此示例中， `collectionLink` 变量是一个类级字段，它指定 `Uri` 表示要从中检索文档的文档集合的。

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法创建一个同步执行的查询，并返回一个 `IQueryable<T>` 对象。 但是， `AsDocumentQuery` 方法将对象转换 `IQueryable<T>` 为 `IDocumentQuery<T>` 可异步执行的对象。 异步查询是通过方法执行的 `IDocumentQuery<T>.ExecuteNextAsync` ，该方法用于从文档数据库检索下一页结果， `IDocumentQuery<T>.HasMoreResults` 属性指示是否存在要从查询返回的其他结果。

文档可以通过在查询中包含子句来筛选服务器端 `Where` ，这会将筛选谓词应用于针对文档集合的查询：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查询从其属性等于的集合中检索所有文档 `Done` `false` 。

### <a name="inserting-a-document-into-a-document-collection"></a>将文档插入文档集合

文档是用户定义的 JSON 内容，可以使用方法插入到文档集合中 `DocumentClient.CreateDocumentAsync` ：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定一个 `Uri` 参数，该参数表示文档应插入到的集合，以及一个 `object` 表示要插入的文档的参数。

### <a name="replacing-a-document-in-a-document-collection"></a>替换文档集合中的文档

文档可以在文档集合中替换为 `DocumentClient.ReplaceDocumentAsync` 以下方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法指定一个 `Uri` 参数，该参数表示应替换的集合中的文档，以及一个 `object` 表示更新后的文档数据的参数。

### <a name="deleting-a-document-from-a-document-collection"></a>从文档集合中删除文档

可以使用方法从文档集合中删除文档 `DocumentClient.DeleteDocumentAsync` ：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法指定一个 `Uri` 参数，该参数表示集合中应删除的文档。

### <a name="deleting-a-document-collection"></a>删除文档集合

可以使用方法从数据库中删除文档集合 `DocumentClient.DeleteDocumentCollectionAsync` ：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法指定一个 `Uri` 参数，该参数表示要删除的文档集合。 请注意，调用此方法还将删除存储在集合中的文档。

### <a name="deleting-a-database"></a>删除数据库

可以使用方法从 Cosmos DB 数据库帐户中删除数据库 `DocumentClient.DeleteDatabaesAsync` ：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法指定一个 `Uri` 参数，该参数表示要删除的数据库。 请注意，调用此方法还将删除存储在数据库中的文档集，以及存储在文档集合中的文档。

## <a name="summary"></a>总结

本文介绍了如何使用 Azure Cosmos DB .NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序中。 Azure Cosmos DB 文档数据库是一种 NoSQL 数据库，可提供对 JSON 文档的低延迟访问，为需要无缝缩放和全局复制的应用程序提供快速、高度可用、可缩放的数据库服务。

## <a name="related-links"></a>相关链接

- [Todo Azure Cosmos DB (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Azure Cosmos DB 文档](/azure/cosmos-db/)
- [Azure Cosmos DB .NET Standard 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)