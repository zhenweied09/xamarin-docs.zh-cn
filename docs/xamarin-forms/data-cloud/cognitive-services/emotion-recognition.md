---
title: 表情识别使用面临的 API
description: 表面 API 在映像中作为输入，采用面部表达式并返回数据包含在映像中的每个表面情感一组之间的置信度级别。 此文章介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dc04cb077b894b255eb496b2cb2983626573897
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049761"
---
# <a name="emotion-recognition-using-the-face-api"></a>表情识别使用面临的 API

_表面 API 在映像中作为输入，采用面部表达式并返回数据包含在映像中的每个表面情感一组之间的置信度级别。此文章介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

表面 API 可以执行表情检测，以检测愤怒、 contempt、 厌恶，不必担心、 幸福、 非特定、 sadness 和意外情况发生，面部表情。 这些情感普遍和多个区域性传送通过相同的基本面部表情。 返回面部表情表情结果，以及表面 API 还可以返回检测到的表面的边界框。 请注意，必须获取 API 密钥用于表面 API。 这可以在获取[重认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

通过客户端库，并通过 REST API，可以执行表情识别。 本文重点介绍在执行通过 REST API 的表情识别。 有关 REST API 的详细信息，请参阅[表面 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

表面 API 还可以用来识别在视频中，人员的面部表达式，并且可以返回其情感的摘要。 有关详细信息，请参阅[如何实时分析视频](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

有关表面 API 的详细信息，请参阅[表面 API](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>身份验证

每个请求都会面临 api 需要 API 密钥应指定的值为`Ocp-Apim-Subscription-Key`标头。 下面的代码示例演示如何添加到 API 密钥`Ocp-Apim-Subscription-Key`请求标头：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

未能将有效的 API 密钥传递给表面 API 将导致 401 响应错误。

## <a name="performing-emotion-recognition"></a>执行表情识别

通过使包含图像的 POST 请求执行表情识别`detect`API 在`https://[location].api.cognitive.microsoft.com/face/v1.0`，其中`[location]]`是用于获取你的 API 密钥的区域。 可选的请求参数包括：

- `returnFaceId` – 是否返回 faceIds 的检测到的平面。 默认值为 `true`。
- `returnFaceLandmarks` – 是否返回表面界标的检测到的平面。 默认值为 `false`。
- `returnFaceAttributes` – 是否分析，并返回一个或多个指定面临着属性。 支持的表面属性包括`age`， `gender`， `headPose`， `smile`， `facialHair`， `glasses`， `emotion`， `hair`， `makeup`， `occlusion`， `accessories`， `blur`， `exposure`，和`noise`。 请注意，表面属性分析计算和时间会增加成本。

必须为 URL 或二进制数据的 POST 请求的正文中放置图像内容。

> [!NOTE]
> 支持的图像文件格式为 JPEG、 PNG、 GIF、 和 BMP，和允许的文件大小为 1 KB 到 4 MB。

在示例应用程序，通过调用调用表情识别过程`DetectAsync`方法：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

此方法调用指定包含的图像数据，应返回 faceIds，，不应返回表面界标，并且应分析的映像表情的流。 它还指定，将数组的形式返回结果`Face`对象。 反过来，`DetectAsync`方法调用`detect`执行表情识别的 REST API:

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

此方法生成请求 URI，然后将请求发送到`detect`API 通过`SendRequestAsync`方法。

> [!NOTE]
> 在为你用于获取你的订阅密钥你面临 API 调用中，必须使用同一区域。 例如，如果你获得你的订阅密钥从`westus`区域，表面检测终结点将是`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向表面 API 发出的 POST 请求，并将结果作为返回`Face`数组：

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

如果通过流提供的映像，则该方法通过包装中的图像流来生成 POST 请求`StreamContent`实例，它提供基于流的 HTTP 内容。 或者，如果通过 URL 提供的映像，则该方法将生成 POST 请求通过包装中的 URL 来`StringContent`实例，它提供基于字符串的 HTTP 内容。

然后将 POST 请求发送到`detect`API。 读取响应，将其反序列化，并返回到调用方法。

`detect` API 将在响应中，提供该请求是有效的表示请求成功，请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅[表面 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="processing-the-response"></a>处理响应

以 JSON 格式返回 API 响应。 以下 JSON 数据显示了一个典型的成功响应消息，提供所请求的示例应用程序数据：

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

成功的响应消息包含的按降序排序，一个空响应指示检测到没有表面表面矩形大小排名的表面项数组。 每个识别表面包含一系列由指定的可选字体特性的`returnFaceAttributes`参数`DetectAsync`方法。

在示例应用程序，则 JSON 响应进行反序列化到的数组`Face`对象。 解释结果从表面 API，当检测到的表情应被视为具有最高的得分，表情如评分被规范化到其中一个的总和。 因此，示例应用程序将显示与面临的最大检测到的最高分数识别的表情图像中。 这是替换为以下代码来实现的：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下面的屏幕截图显示了示例应用程序中的表情识别过程的结果：

![](emotion-recognition-images/emotion-recognition.png "表情识别")

## <a name="summary"></a>总结

本文介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。 表面 API 在映像中作为输入，采用面部表达式并返回包括置信度在映像中的每个表面情感一组之间的数据。

## <a name="related-links"></a>相关链接

- [不会遇到 API](/azure/cognitive-services/face/overview/)。
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [表面 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
