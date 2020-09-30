---
title: 使用必应拼写检查 API 进行拼写检查
description: 必应拼写检查对文本执行上下文拼写检查，并为拼写错误的单词提供内联建议。 本文介绍如何使用必应拼写检查 REST API 更正应用程序中的拼写错误 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 719bc923a53d6a2ce9250def48a99893cadac32d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555302"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>使用必应拼写检查 API 进行拼写检查

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_必应拼写检查对文本执行上下文拼写检查，并为拼写错误的单词提供内联建议。本文介绍如何使用必应拼写检查 REST API 更正应用程序中的拼写错误 Xamarin.Forms 。_

## <a name="overview"></a>概述

必应拼写检查 REST API 具有两种运行模式，并且在向 API 发出请求时必须指定模式：

- `Spell` 更正短文本 (最多9个单词) 不更改任何大小写。
- `Proof` 更正长文本，提供大小写更正和基本标点，并取消主动更正。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

必须获取 API 密钥才能使用必应拼写检查 API。 可在 [试用认知服务](https://azure.microsoft.com/try/cognitive-services/)

有关必应拼写检查 API 支持的语言的列表，请参阅支持的 [语言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 有关必应拼写检查 API 的详细信息，请参阅 [必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>身份验证

对必应拼写检查 API 发出的每个请求都需要一个 API 密钥，该密钥应指定为 `Ocp-Apim-Subscription-Key` 标头的值。 下面的代码示例演示如何将 API 密钥添加到请求的 `Ocp-Apim-Subscription-Key` 标头：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

未能将有效的 API 密钥传递到必应拼写检查 API 将导致401响应错误。

## <a name="performing-spell-checking"></a>执行拼写检查

可以通过对中的 API 发出 GET 或 POST 请求来实现拼写检查 `SpellCheck` `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` 。 发出 GET 请求时，要拼写检查的文本将作为查询参数发送。 发出 POST 请求时，将在请求正文中发送要拼写检查的文本。 由于查询参数字符串长度限制，GET 请求限制为拼写检查1500个字符。 因此，通常应进行 POST 请求，除非正在对短字符串进行拼写检查。

在示例应用程序中， `SpellCheckTextAsync` 方法调用拼写检查过程：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法生成请求 URI，然后将请求发送到 `SpellCheck` API，该 API 返回包含结果的 JSON 响应。 JSON 响应将反序列化，并将结果返回给要显示的调用方法。

### <a name="configuring-spell-checking"></a>配置拼写检查

可以通过指定 HTTP 查询参数来配置拼写检查过程：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

此方法将文本设置为拼写检查，并将拼写检查模式设置为。

有关必应拼写检查 REST API 的详细信息，请参阅 [拼写检查 API v7 参考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>正在发送请求

`SendRequestAsync`方法对必应拼写检查进行 GET 请求 REST API 并返回响应：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法将 GET 请求发送到 `SpellCheck` API，并将请求 URL 指定为要转换的文本和拼写检查模式。 然后，将读取响应并将其返回给调用方法。

`SpellCheck`如果请求有效，则 API 会将 HTTP 状态代码200发送 (确定) ，这表明请求已成功，并且请求的信息在响应中。 有关响应对象的列表，请参阅 [response 对象](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

### <a name="processing-the-response"></a>处理响应

API 响应以 JSON 格式返回。 以下 JSON 数据显示拼写错误的文本的响应消息 `Go shappin tommorow` ：

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

`flaggedTokens`数组包含文本中被标记为不正确或语法不正确的单词的数组。 如果未找到拼写或语法错误，则数组将为空。 数组中的标记为：

- `offset` –从文本字符串的开头到标记的单词的从零开始的偏移量。
- `token` –文本字符串中拼写错误或语法错误的词。
- `type` –导致单词被标记的错误的类型。 有两个可能的值 `RepeatedToken` `UnknownToken` ：和。
- `suggestions` –将更正拼写或语法错误的单词的数组。 数组由 `suggestion` 和组成 `score` ，这表示建议的更正正确的置信度级别。

在示例应用程序中，JSON 响应将反序列化为 `SpellCheckResult` 实例，并将结果返回给要显示的调用方法。 下面的代码示例演示如何 `SpellCheckResult` 处理实例以便显示：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此代码循环访问 `FlaggedTokens` 集合，并在第一个建议中替换源文本中任何拼写错误或语法不正确的单词。 以下屏幕截图显示拼写检查前后的内容：

![拼写检查之前](spell-check-images/before-spell-check.png)

![之后拼写检查](spell-check-images/after-spell-check.png)

> [!NOTE]
> 上面的示例使用 `Replace` 简单，但在大量文本中，它可以替换错误的标记。 API 提供了一个 `offset` 值，应在生产应用中使用该值来识别源文本中用于执行更新的正确位置。

## <a name="summary"></a>总结

本文介绍了如何使用必应拼写检查 REST API 更正应用程序中的拼写错误 Xamarin.Forms 。 必应拼写检查对文本执行上下文拼写检查，并为拼写错误的单词提供内联建议。

## <a name="related-links"></a>相关链接

- [必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)
- [使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 认知服务 (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [必应拼写检查 API v7 参考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)