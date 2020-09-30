---
title: 使用人脸 API 感知情感识别
description: 人脸 API 将图像中的面部表情作为输入，并返回数据，这些数据包括图像中每个人脸的一组情感的置信度。 本文介绍如何使用人脸 API 来识别情感，以对应用程序进行评级 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 220b8a49f4f261568a653af16e333885edec23f8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562868"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>使用人脸 API 感知情感识别

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

人脸 API 可以执行情感检测，在基于人为普通人的可感知批注的面部表达式中检测愤怒、蔑视、厌恶、恐惧、幸福、中性、悲伤和惊喜。 但是，请务必注意，单独的面部表达式可能不一定表示用户的内部状态。

除了为面部表达式返回情感结果外，人脸 API 还可以返回检测到的人脸的边界框。

情感识别可以通过客户端库和 REST API 来执行。 本文重点介绍如何通过 REST API 执行情感识别。 有关 REST API 的详细信息，请参阅人 [脸 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

人脸 API 也可用于识别视频中人员的面部表情，并可返回其情感的摘要。 有关详细信息，请参阅 [如何实时分析视频](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

必须获取 API 密钥才能使用人脸 API。 可在 [试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)中获取。

有关人脸 API 的详细信息，请参阅 [人脸 API](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>身份验证

对人脸 API 发出的每个请求都需要一个 API 密钥，该密钥应指定为 `Ocp-Apim-Subscription-Key` 标头的值。 下面的代码示例演示如何将 API 密钥添加到请求的 `Ocp-Apim-Subscription-Key` 标头：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

未能将有效的 API 密钥传递到人脸 API 会导致401响应错误。

## <a name="perform-emotion-recognition"></a>执行情感识别

情感识别的执行方式是将包含映像的 POST 请求 `detect` 置于 `https://[location].api.cognitive.microsoft.com/face/v1.0` 中，其中 `[location]]` 是用于获取 api 密钥的区域。 可选的请求参数包括：

- `returnFaceId` –是否返回检测到的人脸的 faceIds。 默认值为 `true`。
- `returnFaceLandmarks` –是否返回检测到的人脸特征点。 默认值为 `false`。
- `returnFaceAttributes` –是否分析并返回一个或多个指定的面部特性。 支持的面部特性包括 `age` 、 `gender` 、 `headPose` 、 `smile` 、、、 `facialHair` `glasses` `emotion` `hair` `makeup` `occlusion` `accessories` `blur` `exposure` `noise` 、、、、、、和。 请注意，人脸属性分析具有额外的计算和时间成本。

图像内容必须作为 URL 或二进制数据放置在 POST 请求的正文中。

> [!NOTE]
> 支持的图像文件格式为 JPEG、PNG、GIF 和 BMP，而允许的文件大小为从1KB 到4MB。

在示例应用程序中，通过调用方法来调用情感识别过程 `DetectAsync` ：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

此方法调用指定包含图像数据的流，应返回 faceIds，特征点不应返回，并且应分析图像的情感。 它还指定以对象数组的形式返回结果 `Face` 。 然后，该 `DetectAsync` 方法调用 `detect` 执行情感识别的 REST API：

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

此方法生成请求 URI，然后通过方法将请求发送到 `detect` API `SendRequestAsync` 。

> [!NOTE]
> 你必须在人脸 API 中使用与用于获取订阅密钥相同的区域。 例如，如果从区域获取了订阅密钥 `westus` ，则面部检测终结点将为 `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` 。

### <a name="send-the-request"></a>发送请求

`SendRequestAsync`方法对人脸 API 进行 POST 请求，并以数组形式返回结果 `Face` ：

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

如果图像是通过流提供的，则方法将通过在实例中包装图像流来生成 POST 请求 `StreamContent` ，该实例提供基于流的 HTTP 内容。 或者，如果通过 URL 提供图像，则该方法将通过在实例中包装 URL 来生成 POST 请求 `StringContent` ，这将基于字符串提供 HTTP 内容。

然后将 POST 请求发送到 `detect` API。 响应被读取、反序列化并返回到调用方法。

`detect`如果请求有效，则 API 会将 HTTP 状态代码200发送 (确定) ，这表明请求已成功，并且请求的信息在响应中。 有关可能的错误响应的列表，请参阅 [面部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="process-the-response"></a>处理响应

API 响应以 JSON 格式返回。 以下 JSON 数据显示了一条典型的成功响应消息，该消息提供示例应用程序所请求的数据：

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

成功的响应消息包含按人脸矩形大小以降序顺序排列的一组人脸条目，而空响应指示未检测到人脸。 每个识别的面部都包含一系列可选的面部特性，由 `returnFaceAttributes` 方法的参数指定 `DetectAsync` 。

在示例应用程序中，JSON 响应将反序列化为对象的数组 `Face` 。 解释人脸 API 的结果时，检测到的情感应解释为得分最高的情感，因为评分规范为 sum。 因此，示例应用程序将显示已识别的情感，其中包含图像中检测到的最大人脸的最高分。 使用以下代码即可实现此目的：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

以下屏幕截图显示了示例应用程序中情感识别过程的结果：

![情感识别](emotion-recognition-images/emotion-recognition.png)

## <a name="related-links"></a>相关链接

- [人脸 API](/azure/cognitive-services/face/overview/)。
- [Todo 认知服务 (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [人脸 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)