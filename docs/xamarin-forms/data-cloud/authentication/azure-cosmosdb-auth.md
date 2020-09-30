---
title: 使用 Azure Cosmos DB 文档数据库对用户进行身份验证并 Xamarin.Forms
description: 本文介绍如何将 access control 与 Azure Cosmos DB 分区集合结合使用，以便用户只能在应用程序中访问他们自己的文档 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2dacbc18fdbc7d9c7bd38d5ddfad2157e3a0a503
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563284"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-no-locxamarinforms"></a>使用 Azure Cosmos DB 文档数据库对用户进行身份验证并 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB 文档数据库支持分区集合，这些集合可以跨多个服务器和分区，同时支持无限制的存储和吞吐量。本文介绍如何结合使用访问控制和分区集合，使用户只能在应用程序中访问他们自己的文档 Xamarin.Forms 。_

## <a name="overview"></a>概述

创建分区集合时必须指定分区键，具有相同分区键的文档将存储在同一个分区中。 因此，将用户标识指定为分区键将导致仅存储该用户的文档的已分区集合。 这还可以确保 Azure Cosmos DB 文档数据库将随着用户和项目数量的增加而扩展。

必须向任何集合授予访问权限，并且 SQL API 访问控制模型定义了两种类型的访问构造：

- **主密钥** 启用对 Cosmos DB 帐户中所有资源的完全管理访问权限，并在创建 Cosmos DB 帐户时创建。
- **资源令牌** 捕获数据库用户与该用户对特定 Cosmos DB 资源（如集合或文档）的权限之间的关系。

公开主密钥会打开 Cosmos DB 帐户，以防止恶意或疏忽的使用。 但是，Azure Cosmos DB 资源令牌提供一种安全机制，使客户端能够根据授予的权限读取、写入和删除 Azure Cosmos DB 帐户中的特定资源。

请求、生成资源令牌并将其传递到移动应用程序的典型方法是使用资源令牌代理。 下图显示了示例应用程序如何使用资源令牌代理来管理对文档数据库数据的访问的高级概述：

![文档数据库身份验证过程](azure-cosmosdb-auth-images/documentdb-authentication.png)

资源令牌代理是托管在 Azure App Service 中的中间层 Web API 服务，它拥有 Cosmos DB 帐户的主密钥。 示例应用程序使用资源令牌代理来管理对文档数据库数据的访问，如下所示：

1. 登录时， Xamarin.Forms 应用程序会联系 Azure App Service 来启动身份验证流。
1. Azure App Service 使用 Facebook 执行 OAuth 身份验证流。 身份验证流完成后， Xamarin.Forms 应用程序会收到一个访问令牌。
1. Xamarin.Forms应用程序使用访问令牌从资源令牌代理请求资源令牌。
1. 资源令牌代理使用访问令牌从 Facebook 请求用户的标识。 然后，用户的标识用于请求 Cosmos DB 的资源令牌，该令牌用于向经过身份验证的用户的已分区集合授予读/写访问权限。
1. Xamarin.Forms应用程序使用资源令牌直接访问资源令牌所定义的权限 Cosmos DB 资源。

> [!NOTE]
> 资源令牌到期后，后续的文档数据库请求将收到401未经授权的异常。 此时， Xamarin.Forms 应用程序应该重新建立标识，并请求新的资源令牌。

有关 Cosmos DB 分区的详细信息，请参阅 [如何在 Azure Cosmos DB 中进行分区和缩放](/azure/cosmos-db/partition-data/)。 有关 Cosmos DB 访问控制的详细信息，请参阅[在 SQL API 中](/rest/api/documentdb/access-control-on-documentdb-resources/)[保护对 Cosmos DB 数据](/azure/cosmos-db/secure-access-to-data/)和访问控制的访问权限。

## <a name="setup"></a>设置

将资源令牌代理集成到应用程序的过程 Xamarin.Forms 如下所示：

1. 创建将使用访问控制的 Cosmos DB 帐户。 有关详细信息，请参阅 [Azure Cosmos DB 配置](#azure-cosmos-db-configuration)。
1. 创建用于托管资源令牌代理的 Azure App Service。 有关详细信息，请参阅 [Azure App Service 配置](#azure-app-service-configuration)。
1. 创建 Facebook 应用以执行身份验证。 有关详细信息，请参阅 [Facebook 应用配置](#facebook-app-configuration)。
1. 配置 Azure App Service 以通过 Facebook 执行轻松身份验证。 有关详细信息，请参阅 [Azure App Service 身份验证配置](#azure-app-service-authentication-configuration)。
1. 配置 Xamarin.Forms 示例应用程序，以便与 Azure App Service 和 Cosmos DB 通信。 有关详细信息，请参阅[ Xamarin.Forms 应用程序配置](#xamarinforms-application-configuration)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 配置

用于创建将使用访问控制的 Cosmos DB 帐户的过程如下所示：

1. 创建 Cosmos DB 帐户。 有关详细信息，请参阅 [创建 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帐户中，创建一个名为的新集合 `UserItems` ，并指定的分区键 `/userid` 。

### <a name="azure-app-service-configuration"></a>Azure App Service 配置

在 Azure App Service 中托管资源令牌代理的过程如下所示：

1. 在 Azure 门户中，创建一个新的应用服务 web 应用。 有关详细信息，请参阅 [在应用服务环境中创建 web 应用](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 门户中，打开 web 应用的 "应用设置" 边栏选项卡，并添加以下设置：
    - `accountUrl` –该值应为 Cosmos DB 帐户的 "密钥" 边栏选项卡中 Cosmos DB 帐户 URL。
    - `accountKey` –该值应为 Cosmos DB 帐户的 "密钥" 边栏选项卡中 ("主要" 或 "辅助") 的 Cosmos DB 主密钥。
    - `databaseId` –该值应为 Cosmos DB 数据库的名称。
    - `collectionId` –该值应为 Cosmos DB 集合的名称 (在本例中为 `UserItems`) 。
    - `hostUrl` –该值应为应用服务帐户的 "概述" 边栏选项卡中 web 应用的 URL。

    以下屏幕截图演示了此配置：

    [![应用服务 Web 应用设置](azure-cosmosdb-auth-images/azure-web-app-settings.png)](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "应用服务 Web 应用设置")

1. 将资源令牌代理解决方案发布到 Azure App Service web 应用。

### <a name="facebook-app-configuration"></a>Facebook 应用配置

创建用于执行身份验证的 Facebook 应用的过程如下所示：

1. 创建 Facebook 应用。 有关详细信息，请参阅在 Facebook 开发人员中心 [注册和配置应用](https://developers.facebook.com/docs/apps/register) 。
1. 将 Facebook 登录产品添加到应用。 有关详细信息，请参阅 Facebook 开发人员中心上的 [将 Facebook 登录名添加到应用或网站](https://developers.facebook.com/docs/facebook-login) 。
1. 按如下所示配置 Facebook 登录名：
   - 启用客户端 OAuth 登录。
   - 启用 Web OAuth 登录。
   - 将有效的 OAuth 重定向 URI 设置为应用服务 web 应用的 URI，并 `/.auth/login/facebook/callback` 追加。

  以下屏幕截图演示了此配置：

  ![Facebook 登录 OAuth 设置](azure-cosmosdb-auth-images/facebook-oauth-settings.png)

有关详细信息，请参阅 [将应用程序注册到 Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

### <a name="azure-app-service-authentication-configuration"></a>Azure App Service 身份验证配置

配置应用服务轻松身份验证的过程如下所示：

1. 在 Azure 门户中，导航到应用服务 web 应用。
1. 在 Azure 门户中，打开 "身份验证/授权" 边栏选项卡并执行以下配置：
    - 应打开应用服务身份验证。
    - 请求未经身份验证时要执行的操作应设置为 **使用 Facebook 登录**。

    以下屏幕截图演示了此配置：

    [![应用服务 Web 应用身份验证设置](azure-cosmosdb-auth-images/app-service-authentication-settings.png)](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "应用服务 Web 应用身份验证设置")

应用服务 web 应用还应配置为与 Facebook 应用通信，以启用身份验证流。 为此，可以选择 Facebook 标识提供者，并在 Facebook 开发人员中心的 Facebook 应用设置中输入 **应用程序 ID** 和 **应用程序密钥** 值。 有关详细信息，请参阅 [将 Facebook 信息添加到应用程序](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

### <a name="no-locxamarinforms-application-configuration"></a>Xamarin.Forms 应用程序配置

配置 Xamarin.Forms 示例应用程序的过程如下所示：

1. 打开 Xamarin.Forms 解决方案。
1. 打开 `Constants.cs` 并更新以下常量的值：
    - `EndpointUri` –该值应为 Cosmos DB 帐户的 "密钥" 边栏选项卡中 Cosmos DB 帐户 URL。
    - `DatabaseName` –该值应为文档数据库的名称。
    - `CollectionName` –该值应为文档数据库集合的名称 (在本例中为 `UserItems`) 。
    - `ResourceTokenBrokerUrl` –该值应为应用服务帐户的 "概述" 边栏选项卡中的资源令牌代理 web 应用的 URL。

## <a name="initiating-login"></a>启动登录

示例应用程序通过将浏览器重定向到标识提供程序 URL 来启动登录过程，如下面的示例代码所示：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

这会导致在 Azure App Service 和 Facebook 之间启动 OAuth 身份验证流，这将显示 Facebook 登录页：

![Facebook 登录](azure-cosmosdb-auth-images/login.png)

可以通过在 iOS 上按 " **取消** " 按钮或按 Android 上的 " **后退** " 按钮来取消登录，在这种情况下，用户将保持未经身份验证，并且将从屏幕中删除标识提供程序用户界面。

## <a name="obtaining-a-resource-token"></a>获取资源令牌

身份验证成功后，将 `WebRedirectAuthenticator.Completed` 触发事件。 下面的代码示例演示如何处理此事件：

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

身份验证成功的结果是访问令牌，它是可用的 `AuthenticatorCompletedEventArgs.Account` 属性。 在 GET 请求中提取并使用对资源令牌代理的 API 的访问令牌 `resourcetoken` 。

`resourcetoken`API 使用访问令牌从 Facebook 请求用户的标识，而该标识又用于请求 Cosmos DB 的资源令牌。 如果文档数据库中已存在用户的有效权限文档，则会检索该文档，并将包含资源令牌的 JSON 文档返回到 Xamarin.Forms 应用程序。 如果用户没有有效的权限文档，将在文档数据库中创建用户和权限，并从权限文档中提取资源令牌并将其返回到 Xamarin.Forms JSON 文档中的应用程序。

> [!NOTE]
> 文档数据库用户是与文档数据库相关联的资源，每个数据库可能包含零个或多个用户。 文档数据库权限是与文档数据库用户关联的资源，每个用户可能包含零个或多个权限。 权限资源提供对用户在尝试访问某个资源（如文档）时所需的安全令牌的访问权限。

如果 `resourcetoken` API 成功完成，它将在响应中发送 HTTP 状态代码 200 (确定) ，以及包含资源令牌的 JSON 文档。 以下 JSON 数据显示了一个典型的成功响应消息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

该 `WebRedirectAuthenticator.Completed` 事件处理程序从 API 读取响应 `resourcetoken` 并提取资源令牌和用户 id。然后，资源令牌作为参数传递给 `DocumentClient` 构造函数，该构造函数封装用于访问 Cosmos DB 的终结点、凭据和连接策略，并用于针对 Cosmos DB 配置和执行请求。 资源令牌随每个请求一起发送以直接访问资源，并指示授予经过身份验证的用户的已分区集合的读/写访问权限。

## <a name="retrieving-documents"></a>检索文档

通过创建包含用户的 id 作为分区键的文档查询来检索仅属于已经过身份验证的用户的文档，并在下面的代码示例中演示：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

查询以异步方式从指定的集合中检索属于已经过身份验证的用户的所有文档，并将它们放在一个 `List<TodoItem>` 集合中以供显示。

`CreateDocumentQuery<T>`方法指定一个 `Uri` 参数，该参数表示应对文档和对象进行查询的集合 `FeedOptions` 。 `FeedOptions`对象指定查询可以返回的项目数不受限制，并且用户的 id 作为分区键。 这可确保结果中只返回用户的已分区集合中的文档。

> [!NOTE]
> 请注意，由资源令牌代理创建的权限文档存储在与应用程序创建的文档相同的文档集合中 Xamarin.Forms 。 因此，文档查询包含一个 `Where` 子句，该子句将筛选谓词应用于针对文档集合的查询。 此子句可确保不会从文档集合中返回权限文档。

有关从文档集合中检索文档的详细信息，请参阅 [检索文档集合文档](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#retrieving-document-collection-documents)。

## <a name="inserting-documents"></a>插入文档

将文档插入文档集合之前， `TodoItem.UserId` 应使用作为分区键使用的值更新属性，如下面的代码示例所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

这可确保将文档插入用户的已分区集合。

有关将文档插入文档集合的详细信息，请参阅在 [文档集合中插入文档](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting-a-document-into-a-document-collection)。

## <a name="deleting-documents"></a>删除文档

删除分区集合中的文档时，必须指定分区键值，如以下代码示例所示：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

这可确保 Cosmos DB 知道要从中删除文档的分区集合。

有关从文档集合中删除文档的详细信息，请参阅 [从文档集合中删除文档](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting-a-document-from-a-document-collection)。

## <a name="summary"></a>总结

本文介绍了如何结合使用访问控制和分区集合，使用户只能在应用程序中访问他们自己的文档数据库文档 Xamarin.Forms 。 将用户标识指定为分区键可确保分区集合只能存储该用户的文档。

## <a name="related-links"></a>相关链接

- [Todo Azure Cosmos DB Auth (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [使用 Azure Cosmos DB 文档数据库](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [保护对 Azure Cosmos DB 数据的访问](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的访问控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何在 Azure Cosmos DB 中分区和缩放](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](/dotnet/api/overview/azure/cosmosdb/client)