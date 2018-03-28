---
title: 使用 Microsoft 语音 API 的语音识别
description: Microsoft 语音 API 是一种基于云的 API，提供了算法来处理所说的语言。 此文章介绍了如何使用 Microsoft 语音识别 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 2230b11f9553fb779a86d7504ed5507d2e7cbaa7
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>使用 Microsoft 语音 API 的语音识别

_Microsoft 语音 API 是一种基于云的 API，提供了算法来处理所说的语言。此文章介绍了如何使用 Microsoft 语音识别 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。_

## <a name="overview"></a>概述

Microsoft 语音 API 具有两个组件：

- 转换成文本的朗读的单词语音识别 API。 可以通过 REST API、 客户端库或服务库执行语音识别。
- 文本到语音 API 将文本转换为朗读单词。 通过 REST API 执行文本到语音转换。

本文重点介绍在执行通过 REST API 的语音识别。 虽然客户端和服务库支持返回部分结果，REST API 可以仅返回单个识别结果，而无需任何部分结果。

若要使用 Microsoft 语音 API，必须获取 API 密钥。 这可以在获取[重认知服务](https://azure.microsoft.com/try/cognitive-services/)。

有关 Microsoft 语音 API 的详细信息，请参阅[Microsoft 语音 API 文档](/azure/cognitive-services/speech/home/)。

## <a name="authentication"></a>身份验证

向 Microsoft 语音 REST API 发出的每个请求需要一个 JSON Web 令牌 (JWT) 访问令牌，可以从在认知服务令牌服务获取`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以通过 POST 请求的令牌的服务，获得令牌指定`Ocp-Apim-Subscription-Key`标头，其中包含作为其值的 API 密钥。

下面的代码示例说明如何请求访问令牌从令牌的服务：

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

返回的访问令牌，它是 Base64 文本，具有 10 分钟的到期时间。 因此，示例应用程序将续订的访问令牌每隔 9 分钟。

必须在每个 Microsoft 语音 REST API 中指定的访问令牌作为调用`Authorization`使用字符串作为前缀的标头`Bearer`，下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

未能将的有效访问令牌传递给 Microsoft 语音 REST API 将导致 403 的响应错误。

## <a name="performing-speech-recognition"></a>执行语音识别

语音识别通过在 POST 请求来实现`recognition`API 在`https://speech.platform.bing.com/speech/recognition/`。 单个请求不能包含多个 10 秒的音频和总请求持续时间不能超过 14 秒。

必须 wav 格式中的请求的 POST 正文中放置的音频内容。

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
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

在为 PCM wav 数据，每个特定于平台的项目中记录音频和`RecognizeSpeechAsync`方法使用`PCLStorage`NuGet 程序包来将音频文件作为流打开。 从令牌的服务中检索语音识别请求生成 URI 和一个访问令牌。 语音识别请求发布到`recognition`API，它返回包含结果的 JSON 响应。 JSON 响应进行反序列化，返回到调用方法以显示结果。

### <a name="configuring-speech-recognition"></a>配置语音识别

可以通过指定 HTTP 查询参数配置语音识别过程：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

执行的主要配置`GenerateRequestUri`方法是将设置的音频内容的区域设置。 有关支持的区域设置的列表，请参阅[支持的语言](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/)。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向 Microsoft 语音 REST API 发出的 POST 请求，并返回响应：

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

此方法可生成由 POST 请求：

- 包装中的音频流`StreamContent`实例，它提供基于流的 HTTP 内容。
- 设置`Content-Type`到的请求标头`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 添加到的访问令牌`Authorization`标头，使用字符串作为前缀`Bearer`。

然后将 POST 请求发送到`recognition`API。 然后将对响应进行读取，并将其返回到调用方法。

`recognition` API 将在响应中，提供该请求是有效的表示请求成功，请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅[故障排除](/azure/cognitive-services/speech/troubleshooting)。

### <a name="processing-the-response"></a>处理响应

API 响应使用包含在将识别的文本返回 JSON 格式`name`标记。 下面的 JSON 数据显示了典型成功的响应消息：

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

在示例应用程序，则 JSON 响应反序列化为`SpeechResult`实例，使用结果返回到调用方法以显示，如以下屏幕截图中所示：

![](speech-recognition-images/speech-recognition.png "语音识别")

## <a name="summary"></a>总结

本文介绍了如何使用 Microsoft 语音 REST API 将音频转换为在 Xamarin.Forms 应用程序中的文本。 除了执行语音识别，Microsoft 语音 API 还可以将文本转换为朗读单词。

## <a name="related-links"></a>相关链接

- [Microsoft 语音 API 文档](/azure/cognitive-services/speech/home/)。
- [使用 rest 样式 Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
