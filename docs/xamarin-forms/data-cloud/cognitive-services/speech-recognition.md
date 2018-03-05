---
title: "语音识别使用必应语音 API"
description: "必应语音 API 是一种基于云的 API，提供了算法来处理所说的语言。 此文章介绍了如何使用必应语音识别 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 186ea6277ec7bd4ceb52855186e6fd88344b1b86
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="speech-recognition-using-the-bing-speech-api"></a>语音识别使用必应语音 API

_必应语音 API 是一种基于云的 API，提供了算法来处理所说的语言。此文章介绍了如何使用必应语音识别 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。_

![](~/media/shared/preview.png "此 API 已当前预发行版")

> [!NOTE]
> 必应语音 API 仍处于预览状态。 那里可能重大更改对最终发布之前 API。

## <a name="overview"></a>概述

必应语音 API 具有两个组件：

- 转换成文本的朗读的单词语音识别 API。 可以通过 REST API、 客户端库或服务库执行语音识别。
- 文本到语音 API 将文本转换为朗读单词。 通过 REST API 执行文本到语音转换。

本文重点介绍在执行通过 REST API 的语音识别。 虽然客户端和服务库支持返回部分结果，REST API 可以仅返回单个识别结果，而无需任何部分结果。

若要使用必应语音识别 API，必须获取 API 密钥。 这可以在获取[入门免费](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview)microsoft.com 上。

有关 Bing 语音 API 的详细信息，请参阅[Bing 语音文档](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)microsoft.com 上。

## <a name="authentication"></a>身份验证

每个请求都会对 Bing 语音识别 REST API 需要一个 JSON Web 令牌 (JWT) 访问令牌，可以从在认知服务令牌服务获取`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以通过 POST 请求的令牌的服务，获得令牌指定`Ocp-Apim-Subscription-Key`标头，其中包含作为其值的 API 密钥。

下面的代码示例说明如何请求访问令牌从令牌的服务：

```csharp
async Task<string> FetchTokenAsync(string fetchUri, string apiKey)
{
  using (var client = new HttpClient())
  {
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";

    var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
  }
}
```

返回的访问令牌，它是 Base64 文本，具有 10 分钟的到期时间。 因此，示例应用程序将续订的访问令牌每隔 9 分钟。

必须在每个必应语音识别 REST API 中指定的访问令牌作为调用`Authorization`使用字符串作为前缀的标头`Bearer`，下面的代码示例中所示：

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

未能将的有效访问令牌传递给必应语音识别 REST API 将导致 403 的响应错误。

## <a name="performing-speech-recognition"></a>执行语音识别

语音识别通过在 POST 请求来实现`recognize`API 在`https://speech.platform.bing.com/recognize`。 单个请求不能包含多个 10 秒的音频和总请求持续时间不能超过 14 秒。

必须 wav 格式中的请求的 POST 正文中放置的音频内容。 有关支持的编解码器的信息，请参阅[支持的编解码器](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-codecs)microsoft.com 上。

在示例应用程序，`RecognizeSpeechAsync`方法调用语音识别过程：

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResults = JsonConvert.DeserializeObject<SpeechResults>(response);

    fileStream.Dispose();
    return speechResults.results.FirstOrDefault();
}
```

在为 PCM wav 数据，每个特定于平台的项目中记录音频和`RecognizeSpeechAsync`方法使用`PCLStorage`NuGet 程序包来将音频文件作为流打开。 从令牌的服务中检索语音识别请求生成 URI 和一个访问令牌。 语音识别请求发布到`recognize`API，它返回包含结果的 JSON 响应。 JSON 响应进行反序列化，返回到调用方法以显示结果。

### <a name="configuring-speech-recognition"></a>配置语音识别

可以通过指定 HTTP 查询参数配置语音识别过程。 没有强制和可选参数，显示的强制参数，必须设置为以下方法：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    string requestUri = speechEndpoint;
    requestUri += @"?scenarios=ulm";                                    // websearch is the other option
    requestUri += @"&appid=D4D52672-91D7-4C74-8AD8-42B1D98141A5";       // You must use this ID
    requestUri += @"&locale=en-US";                                     // Other languages supported
    requestUri += string.Format("&device.os={0}", operatingSystem);     // Open field
    requestUri += @"&version=3.0";                                      // Required value
    requestUri += @"&format=json";                                      // Required value
    requestUri += @"&instanceid=fe34a4de-7927-4e24-be60-f0629ce1d808";  // GUID for device making the request
    requestUri += @"&requestid=" + Guid.NewGuid().ToString();           // GUID for the request
    return requestUri;
}
```

执行的主要配置`GenerateRequestUri`方法是将设置的音频内容的区域设置。 有关支持的区域设置的列表，请参阅[支持区域设置](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-locales)microsoft.com 上。

有关强制性参数的可能值的详细信息，请参阅[所需参数](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#required-parameters)microsoft.com 上。有关可选参数的信息，请参阅[可选参数](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)microsoft.com 上。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向 Bing 语音识别 REST API 发出的 POST 请求，并返回响应：

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);

    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
        var response = await httpClient.PostAsync(url, content);

        return await response.Content.ReadAsStringAsync();
    }
}
```

此方法可生成由 POST 请求：

- 包装中的音频流`StreamContent`实例，它提供基于流的 HTTP 内容。
- 设置`Content-Type`到的请求标头`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 添加到的访问令牌`Authorization`标头，使用字符串作为前缀`Bearer`。

然后将 POST 请求发送到`recognize`API。 然后将对响应进行读取，并将其返回到调用方法。

`recognize` API 将在响应中，提供该请求是有效的表示请求成功，请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅[错误响应](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#error-responses)microsoft.com 上。

### <a name="processing-the-response"></a>处理响应

API 响应使用包含在将识别的文本返回 JSON 格式`name`标记。 下面的 JSON 数据显示了典型成功的响应消息：

```csharp
{
  "version": "3.0",
  "header": {
    "status": "success",
    "scenario": "ulm",
    "name": "go shopping tomorrow",
    "lexical": "go shopping tomorrow",
    "properties": {
      "requestid": "e06c059d-fa37-4bb1-843f-4914350279a8",
      "HIGHCONF": "1"
    }
  },
  "results": [
    {
      "scenario": "ulm",
      "name": "go shopping tomorrow",
      "lexical": "go shopping tomorrow",
      "confidence": "0.9493451",
      "properties": {
        "HIGHCONF": "1"
      }
    }
  ]
}
```

在示例应用程序，则 JSON 响应反序列化为`SpeechResult`实例，使用结果返回到调用方法以显示，如以下屏幕截图中所示：

![](speech-recognition-images/speech-recognition.png "语音识别")

每个 JSON 标记的值的信息，请参阅[语音识别响应](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#speech-recognition-responses)microsoft.com 上。

## <a name="summary"></a>摘要

本文介绍了如何使用必应语音识别 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。 除了执行语音识别，必应语音 API 还可以将文本转换为朗读单词。



## <a name="related-links"></a>相关链接

- [必应语音文档](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)
- [使用 rest 样式 Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [必应语音识别 REST API](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)
