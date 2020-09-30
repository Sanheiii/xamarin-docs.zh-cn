---
title: 对 RESTful Web 服务进行身份验证
description: 基本身份验证仅向具有正确凭据的客户端提供对资源的访问权限。 本文介绍如何使用基本身份验证来保护对 RESTful web 服务资源的访问。
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98b59b1dc9e32b1af1be991055057b94223a4223
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563258"
---
# <a name="authenticate-a-restful-web-service"></a>对 RESTful Web 服务进行身份验证

_HTTP 支持使用多种身份验证机制来控制对资源的访问。基本身份验证仅向具有正确凭据的客户端提供对资源的访问权限。本文演示如何使用基本身份验证来保护对 RESTful web 服务资源的访问。_

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制在 internet (资源（如应用的后端服务器) 和应用）之间执行安全连接，从而防止意外泄漏敏感信息。 由于默认情况下在为 iOS 9 构建的应用中启用了 ATS，因此所有连接都将受到 ATS 的安全要求。 如果连接不满足这些要求，它们将失败并出现异常。
> 如果无法对 `HTTPS` internet 资源使用协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的 **info.plist** 文件来实现。 有关详细信息，请参阅 [应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>通过 HTTP 对用户进行身份验证

基本身份验证是 HTTP 支持的最简单的身份验证机制，并涉及将用户名和密码作为未加密 base64 编码文本发送的客户端。 此功能的工作原理如下：

- 如果 web 服务接收到受保护资源的请求，则会拒绝请求，并将 HTTP 状态代码 401 (拒绝访问) 并设置 WWW 身份验证响应标头，如下图所示：

![基本身份验证失败](rest-images/basic-authentication-fail.png)

- 如果 web 服务接收到受保护资源的请求，并且 `Authorization` 正确设置了标头，则 web 服务将使用 HTTP 状态代码200进行响应，这表示请求成功，且请求的信息在响应中。 下图显示了此方案：

![基本身份验证成功](rest-images/basic-authentication-success.png)

> [!NOTE]
> 基本身份验证仅应在 HTTPS 连接上使用。 当在 HTTP 连接上使用时， `Authorization` 如果攻击者捕获 http 流量，则可以轻松地对标头进行解码。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 请求中指定基本身份验证

使用基本身份验证的指定方式如下：

1. 将字符串 "Basic" 添加到请求的 `Authorization` 标头中。
1. 用户名和密码合并为格式为 "username： password" 的字符串，然后将其设置为 base64 编码并添加到请求的 `Authorization` 标头中。

因此，如果用户名为 "XamarinUser" 并且密码为 "XamarinPassword"，则标头将变为：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`类可以设置属性的 `Authorization` 标头值 `HttpClient.DefaultRequestHeaders.Authorization` 。 由于 `HttpClient` 实例存在于多个请求中，因此 `Authorization` 标头只需设置一次（而不是发出每个请求时），如下面的代码示例所示：

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

然后，当向 web 服务操作发出请求时，请求将使用 `Authorization` 标头进行签名，指示用户是否有权调用该操作。

> [!IMPORTANT]
> 尽管此代码将凭据存储为常量，但在已发布的应用程序中不应以不安全的格式存储它们。

## <a name="processing-the-authorization-header-server-side"></a>处理授权标头服务器端

REST 服务应用属性修饰每个操作 `[BasicAuthentication]` 。 此特性用于分析 `Authorization` 标头并确定 base64 编码的凭据是否有效，方法是将它们与 *Web.config*中存储的值进行比较。虽然这种方法适用于示例服务，但它需要为面向公众的 web 服务进行扩展。

在 IIS 使用的基本身份验证模块中，将根据用户的 Windows 凭据对用户进行身份验证。 因此，用户必须拥有服务器域上的帐户。 但是，可以将基本身份验证模型配置为允许自定义身份验证，其中用户帐户根据外部源（例如数据库）进行身份验证。 有关详细信息，请参阅 ASP.NET 网站上 [ASP.NET Web API 中的基本身份验证](https://www.asp.net/web-api/overview/security/basic-authentication) 。

> [!NOTE]
> 基本身份验证不是为管理注销而设计的。因此，用于注销的标准基本身份验证方法是结束会话。

## <a name="related-links"></a>相关链接

- [使用 RESTful web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](/dotnet/api/system.net.http.httpclient)