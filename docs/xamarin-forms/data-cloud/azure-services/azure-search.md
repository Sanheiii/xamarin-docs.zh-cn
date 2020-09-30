---
title: 在 Azure 搜索中搜索数据 Xamarin.Forms
description: 本文演示如何使用 Microsoft Azure 搜索库将 Azure 搜索集成到 Xamarin.Forms 应用程序中。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 14d26c1360c1c1b7997598ef1263e3dd62e3c013
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561776"
---
# <a name="search-data-with-azure-search-and-no-locxamarinforms"></a>在 Azure 搜索中搜索数据 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure 搜索是一种云服务，可为上载的数据提供索引和查询功能。这消除了传统上与在应用程序中实现搜索功能相关的基础结构要求和搜索算法复杂性。本文演示如何使用 Microsoft Azure 搜索库将 Azure 搜索集成到 Xamarin.Forms 应用程序中。_

## <a name="overview"></a>概述

数据作为索引和文档存储在 Azure 搜索中。 *索引*是 Azure 搜索服务可搜索的数据存储，在概念上类似于数据库表。 *文档*是索引中可搜索数据的单个单元，在概念上类似于数据库行。 将文档上载到 Azure 搜索并向 Azure 搜索提交搜索查询时，会向搜索服务中的特定索引发出请求。

对 Azure 搜索发出的每个请求都必须包含服务的名称和 API 密钥。 有两种类型的 API 密钥：

- *管理密钥* 授予所有操作的完全权限。 这包括管理服务、创建和删除索引以及数据源。
- *查询密钥* 授予对索引和文档的只读访问权限，并且应由发出搜索请求的应用程序使用。

最常见的 Azure 搜索请求是执行查询。 可提交两种类型的查询：

- *搜索*查询可搜索索引中所有可搜索字段中的一个或多个项。 搜索查询是使用简化的语法或 Lucene 查询语法生成的。 有关详细信息，请参阅 azure [搜索中的简单查询语法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)和 [azure 搜索中的 Lucene 查询语法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- *筛选器*查询将计算索引中所有可筛选字段上的布尔表达式。 使用 OData 筛选器语言的子集生成筛选器查询。 有关详细信息，请参阅 [Azure 搜索的 OData 表达式语法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

搜索查询和筛选器查询可以单独使用，也可以一起使用。 当一起使用时，筛选器查询首先应用于整个索引，然后在筛选器查询的结果上执行搜索查询。

Azure 搜索还支持根据搜索输入检索建议。 有关详细信息，请参阅 [建议查询](#suggestion-queries)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>设置

将 Azure 搜索集成到应用程序的过程如下所示 Xamarin.Forms ：

1. 创建 Azure 搜索服务。 有关详细信息，请参阅 [使用 Azure 门户创建 Azure 搜索服务](/azure/search/search-create-service-portal/)。
1. 从解决方案可移植类库中删除作为目标框架的 Silverlight Xamarin.Forms (PCL) 。 这可以通过将 PCL 配置文件更改为支持跨平台开发的任何配置文件来实现，但不支持 Silverlight，如配置文件151或配置文件92。
1. 将 [Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search) NuGet 包添加到解决方案中的 PCL 项目 Xamarin.Forms 。

执行这些步骤后，可以使用 Microsoft 搜索库 API 来管理搜索索引和数据源、上传和管理文档并执行查询。

## <a name="creating-the-azure-search-index"></a>创建 Azure 搜索索引

必须定义一个映射到要搜索的数据结构的索引架构。 可以在 Azure 门户中完成此操作，也可以使用类以编程方式完成此操作 `SearchServiceClient` 。 此类管理与 Azure 搜索的连接，可用于创建索引。 下面的代码示例演示如何创建此类的实例：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient`构造函数重载采用搜索服务名称和 `SearchCredentials` 对象作为参数，并将 `SearchCredentials` 对象包装到 Azure 搜索服务的*管理密钥*。 需要使用 *admin 密钥* 来创建索引。

> [!NOTE]
> `SearchServiceClient`应在应用程序中使用单个实例，以避免打开到 Azure 搜索的过多连接。

索引由 `Index` 对象定义，如下面的代码示例所示：

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

`Index.Name`应将属性设置为索引的名称，并且 `Index.Fields` 应将属性设置为对象的数组 `Field` 。 每个 `Field` 实例指定一个名称、一个类型和任何属性，这些属性指定字段的使用方式。 这些属性包括：

- `IsKey` -指示此字段是否为索引的键。 必须将一个类型为的索引中的一个字段 `DataType.String` 指定为键字段。
- `IsFacetable` -指示是否可以在此字段上执行分面导航。 默认值为 `false`。
- `IsFilterable` -指示是否可以在筛选器查询中使用该字段。 默认值为 `false`。
- `IsRetrievable` -指示是否可以在搜索结果中检索字段。 默认值为 `true`。
- `IsSearchable` –指示全文搜索中是否包含字段。 默认值为 `false`。
- `IsSortable` -指示是否可在表达式中使用该字段 `OrderBy` 。 默认值为 `false`。

> [!NOTE]
> 在部署后更改索引需要重新生成和重新加载数据。

`Index`对象可以选择指定一个 `Suggesters` 属性，该属性定义索引中用于支持自动完成或搜索建议查询的字段。 `Suggesters`应将属性设置为对象的数组 `Suggester` ，这些对象定义用于生成搜索建议结果的字段。

创建对象后 `Index` ，将通过对实例调用来创建索引 `Indexes.Create` `SearchServiceClient` 。

> [!NOTE]
> 从必须保持响应状态的应用程序创建索引时，请使用 `Indexes.CreateAsync` 方法。

有关详细信息，请参阅 [使用 .NET SDK 创建 Azure 搜索索引](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>删除 Azure 搜索索引

可以通过对实例调用来删除索引 `Indexes.Delete` `SearchServiceClient` ：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>将数据上传到 Azure 搜索索引

定义索引后，可以使用以下两种模型之一将数据上传到其中：

- **拉取模型** –数据定期从 Azure Cosmos DB、Azure SQL 数据库、Azure Blob 存储或 Azure 虚拟机中托管 SQL Server 进行引入。
- **推送模型** –以编程方式将数据发送到索引。 这是本文中采用的模型。

`SearchIndexClient`若要将数据导入到索引中，必须创建一个实例。 这可以通过调用方法来完成 `SearchServiceClient.Indexes.GetClient` ，如下面的代码示例所示：

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

要导入到索引中的数据打包为一个 `IndexBatch` 对象，该对象封装对象的集合 `IndexAction` 。 每个 `IndexAction` 实例都包含一个文档和一个属性，该属性告知 Azure 搜索要对文档执行的操作。 在上面的代码示例中， `IndexAction.Upload` 指定了操作，这会导致文档在索引中插入（如果是新的），或者替换为（如果已存在）。 `IndexBatch`然后，通过对对象调用方法，将对象发送到索引 `Documents.Index` `SearchIndexClient` 。 有关其他索引操作的详细信息，请参阅 [确定要使用的索引操作](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)。

> [!NOTE]
> 只有1000个文档才能包含在单个索引请求中。

请注意，在上面的代码示例中， `monkeyList` 集合是从对象集合创建为匿名对象的 `Monkey` 。 这将为字段创建数据 `id` ，并将 Pascal 大小写属性名称的映射解析 `Monkey` 为 camel 大小写搜索索引字段名称。 此外，也可以通过将属性添加到类来完成此映射 `[SerializePropertyNamesAsCamelCase]` `Monkey` 。

有关详细信息，请参阅 [使用 .NET SDK 将数据上传到 Azure 搜索](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查询 Azure 搜索索引

`SearchIndexClient`若要查询索引，必须创建实例。 当应用程序执行查询时，建议遵循最低权限原则并 `SearchIndexClient` 直接创建，并将 *查询密钥* 作为参数传递。 这可确保用户对索引和文档具有只读访问权限。 下面的代码示例演示了这种方法：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient`构造函数重载采用搜索服务名称、索引名称和 `SearchCredentials` 对象作为参数，并将 `SearchCredentials` 对象包装在 Azure 搜索服务的*查询密钥*。

### <a name="search-queries"></a>搜索查询

可以通过对实例调用方法来查询索引 `Documents.SearchAsync` `SearchIndexClient` ，如以下代码示例所示：

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SearchAsync`方法采用搜索文本参数，并 `SearchParameters` 使用可用于进一步优化查询的可选对象。 搜索查询指定为搜索文本参数，而通过设置参数的属性可以指定筛选查询 `Filter` `SearchParameters` 。 下面的代码示例演示了这两种查询类型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此筛选器查询应用于整个索引，并从结果中删除文档，该 `location` 字段不等于中国且不等于越南。 筛选后，将对筛选器查询的结果执行搜索查询。

> [!NOTE]
> 若要筛选但不搜索，请传递 `*` 作为搜索文本参数。

`SearchAsync`方法返回一个 `DocumentSearchResult` 包含查询结果的对象。 枚举此对象，并将每个 `Document` 对象都创建为 `Monkey` 对象并将其添加到 `Monkeys` `ObservableCollection` 以显示。 以下屏幕截图显示了从 Azure 搜索返回的搜索查询结果：

![搜索结果](azure-search-images/search.png)

有关搜索和筛选的详细信息，请参阅 [使用 .NET SDK 查询 Azure 搜索索引](/azure/search/search-query-dotnet/)。

### <a name="suggestion-queries"></a>建议查询

Azure 搜索允许根据搜索查询请求建议，方法是对 `Documents.SuggestAsync` 实例调用方法 `SearchIndexClient` 。 下面的代码示例演示了这一点：

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SuggestAsync`方法采用搜索文本参数、建议器的名称以使用在索引) 中定义 (，并使用 `SuggestParameters` 可用于进一步优化查询的可选对象。 此 `SuggestParameters` 实例设置以下属性：

- `UseFuzzyMatching` –当设置为时 `true` ，Azure 搜索将查找建议，即使搜索文本中存在替换或缺失字符也是如此。
- `HighlightPreTag` –附加到建议命中的标记。
- `HighlightPostTag` –附加到建议点击的标记。
- `MinimumCoverage` –表示索引的百分比，该百分比必须包含在建议查询中才能报告成功。 默认值为80。
- `Top` –要检索的建议数。 它必须是介于1和100之间的整数，默认值为5。

总体效果是，将返回索引中的前10个结果，并突出显示突出显示内容，结果将包含包含拼写类似的搜索词的文档。

`SuggestAsync`方法返回一个 `DocumentSuggestResult` 包含查询结果的对象。 枚举此对象，并将每个 `Document` 对象都创建为 `Monkey` 对象并将其添加到 `Monkeys` `ObservableCollection` 以显示。 以下屏幕截图显示了从 Azure 搜索返回的建议结果：

![建议结果](azure-search-images/suggest.png)

请注意，在示例应用程序中， `SuggestAsync` 仅当用户完成输入搜索词时才会调用方法。 不过，它还可用于通过对每个按键执行来支持自动完成的搜索查询。

## <a name="summary"></a>总结

本文演示了如何使用 Microsoft Azure 搜索库将 Azure 搜索集成到 Xamarin.Forms 应用程序中。 Azure 搜索是一种云服务，可为上载的数据提供索引和查询功能。 这消除了传统上与在应用程序中实现搜索功能相关的基础结构要求和搜索算法复杂性。

## <a name="related-links"></a>相关链接

- [Azure Search (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Azure 搜索文档](/azure/search/)
- [Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search/)