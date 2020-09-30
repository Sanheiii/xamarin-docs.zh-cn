---
title: 使用转换器 API 进行文本翻译
description: Microsoft Translator API 可用于通过 REST API 翻译语音和文本。 本文介绍如何使用 Microsoft 文本翻译 API 在应用程序中将文本从一种语言翻译成另一种语言 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2b26e80267be9af6bf300b2ffc82e43fe717f59c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563115"
---
# <a name="text-translation-using-the-translator-api"></a>使用转换器 API 进行文本翻译

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Translator API 可用于通过 REST API 翻译语音和文本。本文介绍如何使用 Microsoft 文本翻译 API 在应用程序中将文本从一种语言翻译成另一种语言 Xamarin.Forms 。_

## <a name="overview"></a>概述

转换器 API 有两个组件：

- 文本翻译 REST API 将一种语言的文本翻译为另一种语言的文本。 API 会自动检测在转换前发送的文本的语言。
- 一种语音翻译 REST API 将语音从一种语言转换为另一种语言的文本。 此 API 还集成了文本转语音功能，可以将翻译的文本再次转换成语音。

本文重点介绍如何使用文本翻译 API 将文本从一种语言翻译成另一种语言。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

必须获取 API 密钥才能使用文本翻译 API。 此操作可在 [如何注册 Microsoft 文本翻译 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)中获得。

有关 Microsoft 文本翻译 API 的详细信息，请参阅 [文本翻译 API 文档](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>身份验证

对文本翻译 API 发出的每个请求都需要一个 JSON Web 令牌 (JWT) 访问令牌，该令牌可从认知服务令牌服务获取 `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` 。 可以通过向令牌服务发出 POST 请求来获得令牌，并指定 `Ocp-Apim-Subscription-Key` 包含 API 密钥作为其值的标头。

下面的代码示例演示如何从令牌服务请求访问令牌：

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

返回的访问令牌为 Base64 文本，其到期时间为10分钟。 因此，示例应用程序每9分钟续订访问令牌一次。

必须在每个文本翻译 API 调用中将访问令牌指定为以 `Authorization` 字符串为前缀的标头 `Bearer` ，如以下代码示例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

有关认知服务令牌服务的详细信息，请参阅 [身份验证](/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="performing-text-translation"></a>正在执行文本转换

可以通过对中的 API 发出 GET 请求来实现文本转换 `translate` `https://api.microsofttranslator.com/v2/http.svc/translate` 。 在示例应用程序中， `TranslateTextAsync` 方法调用文本转换过程：

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

`TranslateTextAsync`方法生成请求 URI，并从令牌服务检索访问令牌。 然后，将文本翻译请求发送到 `translate` API，该 API 返回包含结果的 XML 响应。 分析 XML 响应，并将转换结果返回给调用方法以进行显示。

有关文本翻译 REST Api 的详细信息，请参阅 [文本翻译 API](/azure/cognitive-services/translator/reference/v3-0-reference)。

### <a name="configuring-text-translation"></a>配置文本翻译

可以通过指定 HTTP 查询参数来配置文本转换过程：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

此方法设置要转换的文本，以及要将文本转换为的语言。 有关 Microsoft Translator 支持的语言的列表，请参阅 [microsoft 文本翻译 API 中支持的语言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果应用程序需要知道文本所处的语言，则 `Detect` 可以调用 API 来检测文本字符串的语言。

### <a name="sending-the-request"></a>正在发送请求

`SendRequestAsync`方法对文本翻译发出 GET 请求 REST API 并返回响应：

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法通过将访问令牌添加到 `Authorization` 标头（以字符串为前缀）来生成 GET 请求 `Bearer` 。 然后，会将 GET 请求发送到 `translate` API，并将请求 URL 指定为要转换的文本，并将文本转换为所用的语言。 然后，将读取响应并将其返回给调用方法。

`translate`如果请求有效，则 API 会将 HTTP 状态代码200发送 (确定) ，这表明请求已成功，并且请求的信息在响应中。 有关可能的错误响应的列表，请参阅 [GET 翻译](/azure/cognitive-services/translator/reference/v3-0-translate)中的响应消息。

### <a name="processing-the-response"></a>处理响应

API 响应以 XML 格式返回。 以下 XML 数据显示了一个典型的成功响应消息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

在示例应用程序中，XML 响应分析为 `XDocument` 实例，xml 根值返回到用于显示的调用方法，如以下屏幕截图所示：

![文本转换为德语](text-translation-images/text-translation.png)

## <a name="summary"></a>总结

本文介绍了如何使用 Microsoft 文本翻译 API 将文本从一种语言转换为应用程序中的另一种语言的文本 Xamarin.Forms 。 除了翻译文本以外，Microsoft Translator API 还可以将一种语言的语音转录为其他语言的文本。

## <a name="related-links"></a>相关链接

- [文本翻译 API 文档](/azure/cognitive-services/translator/)
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务 (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [文本翻译 API](/azure/cognitive-services/translator/reference/v3-0-reference)