---
title: 使用转换器 API 的文本转换
description: Microsoft 转换器 API 可用来翻译语音和通过 REST API 的文本。 此文章介绍了如何使用 Microsoft 转换器文本 API 转换从一种语言到另一个在 Xamarin.Forms 应用程序中的文本。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 5c1001335fb030f9a91ec72456042316864ccf5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776659"
---
# <a name="text-translation-using-the-translator-api"></a>使用转换器 API 的文本转换

_Microsoft 转换器 API 可用来翻译语音和通过 REST API 的文本。此文章介绍了如何使用 Microsoft 转换器文本 API 转换从一种语言到另一个在 Xamarin.Forms 应用程序中的文本。_

## <a name="overview"></a>概述

转换器 API 具有两个组件：

- 文本转换 REST API 将从一种语言的文本转换的另一种语言的文本。 API 会自动检测已将其转换之前发送的文本的语言。
- 语音转换成另一种语言的文本，理赔从一种语言的语音 REST API。 API 还集成进行沟通返回已翻译的文本的文本到语音转换功能。

本文重点介绍在转换到另一个使用转换器文本 API 从一种语言的文本。

若要使用转换器文本 API，必须获取 API 密钥。 这可以在获取[如何注册 Microsoft 转换器文本 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)。

有关 Microsoft 转换器文本 API 的详细信息，请参阅[转换器文本 API 文档](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>身份验证

每个请求都会对转换器文本 API 需要一个 JSON Web 令牌 (JWT) 访问令牌，可以从在认知服务令牌服务获取`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以通过 POST 请求的令牌的服务，获得令牌指定`Ocp-Apim-Subscription-Key`标头，其中包含作为其值的 API 密钥。

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

必须在每个转换器文本 API 中指定的访问令牌作为调用`Authorization`使用字符串作为前缀的标头`Bearer`，下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

认知服务令牌服务有关的详细信息，请参阅[身份验证令牌 API](http://docs.microsofttranslator.com/oauth-token.html)。

## <a name="performing-text-translation"></a>执行文本转换

文本转换可以通过发出 GET 请求到`translate`API 在`https://api.microsofttranslator.com/v2/http.svc/translate`。 在示例应用程序，`TranslateTextAsync`方法调用文本转换过程：

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

`TranslateTextAsync`方法生成请求 URI，并从令牌的服务中检索访问令牌。 然后，文本转换请求将发送到`translate`API，后者将返回 XML 响应包含结果。 解析 XML 响应，并转换结果返回到调用方法以显示。

有关文本转换 REST Api 的详细信息，请参阅[Microsoft 转换器文本 API](http://docs.microsofttranslator.com/text-translate.html)。

### <a name="configuring-text-translation"></a>配置文本转换

通过指定 HTTP 查询参数可以将文本转换过程：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

此方法设置要转换的文本和要转换到的文本的语言。 有关支持 Microsoft Translator 的语言的列表，请参阅[Microsoft 转换器文本 API 中支持的语言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果应用程序需要哪种语言的文本是在中， `Detect` API 可以调用以进行检测的文本字符串的语言。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向文本转换 REST API 发出 GET 请求，并返回响应：

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

此方法可通过添加到的访问令牌生成 GET 请求`Authorization`标头，使用字符串作为前缀`Bearer`。 然后将 GET 请求发送到`translate`API，在使用请求 URL 指定的文本要转换，以及要转换到的文本的语言。 然后将对响应进行读取，并将其返回到调用方法。

`translate` API 将在响应中，提供该请求是有效的表示请求成功，请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 有关可能的错误响应的列表，请参阅在响应消息[获取转换](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate)。

### <a name="processing-the-response"></a>处理响应

API 响应以 XML 格式返回。 下面的 XML 数据显示了典型成功的响应消息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

在示例应用程序，XML 响应解析为`XDocument`实例，而且正在返回到调用方法以显示以下屏幕截图中所示的 XML 根值：

![](text-translation-images/text-translation.png "为德语的文本转换")

## <a name="summary"></a>总结

本文介绍了如何使用 Microsoft 转换器文本 API 将从一种语言的文本转换的 Xamarin.Forms 应用程序中的另一种语言的文本。 除了转换文本时，Microsoft 转换器 API 可以还成另一种语言的文本，理赔从一种语言的语音。

## <a name="related-links"></a>相关链接

- [转换器文本 API 文档](/azure/cognitive-services/translator/)。
- [使用 rest 样式 Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft Translator 文本 API](http://docs.microsofttranslator.com/text-translate.html)。
