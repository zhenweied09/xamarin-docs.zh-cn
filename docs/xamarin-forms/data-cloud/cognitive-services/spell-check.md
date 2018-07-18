---
title: 拼写检查使用必应拼写检查 API
description: 必应拼写检查执行上下文的拼写检查的文本，提供有关拼写错误的单词的内联建议。 此文章介绍了如何使用必应拼写检查 REST API 来更正 Xamarin.Forms 应用程序中的拼写错误。
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 259be743a706c9316e2e275ff305a0fe5ad97906
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049904"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>拼写检查使用必应拼写检查 API

_必应拼写检查执行上下文的拼写检查的文本，提供有关拼写错误的单词的内联建议。此文章介绍了如何使用必应拼写检查 REST API 来更正 Xamarin.Forms 应用程序中的拼写错误。_

## <a name="overview"></a>概述

必应拼写检查 REST API 具有两种操作模式，并对 API 发出请求时，必须指定一种模式：

- `Spell` 无任何大小写更改更正短文本 （最多 9 个字）。
- `Proof` 更正长文本，提供的大小写更正和基本标点，并取消主动更正。

若要使用必应拼写检查 API，必须获取 API 密钥。 这可以在获取[重认知服务](https://azure.microsoft.com/try/cognitive-services/)

必应拼写检查 API 支持的语言的列表，请参阅[支持的语言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 有关必应拼写检查 API 的详细信息，请参阅[必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>身份验证

每个请求都会到必应拼写检查 API 需要 API 密钥应指定的值为`Ocp-Apim-Subscription-Key`标头。 下面的代码示例演示如何添加到 API 密钥`Ocp-Apim-Subscription-Key`请求标头：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

未能将有效的 API 密钥传递给必应拼写检查 API 将导致 401 响应错误。

## <a name="performing-spell-checking"></a>执行拼写检查

拼写检查功能可以通过在 GET 或 POST 请求`SpellCheck`API 在`https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`。 在发出 GET 请求时，要进行拼写检查的文本作为查询参数发送。 在发出 POST 请求，请求正文中发送的文本来进行拼写检查。 GET 请求仅限于拼写检查 1500年个字符，因为查询参数字符串长度限制。 因此，仅除非短字符串正在拼写检查时，通常应进行 POST 请求。

在示例应用程序，`SpellCheckTextAsync`方法调用拼写检查过程：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法生成请求 URI，并随后发送到请求`SpellCheck`API，它返回包含结果的 JSON 响应。 JSON 响应进行反序列化，返回到调用方法以显示结果。

### <a name="configuring-spell-checking"></a>配置拼写检查

可以通过指定 HTTP 查询参数配置拼写检查过程：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

此方法设置拼写检查，且拼写检查模式的文本。

有关必应拼写检查 REST API 的详细信息，请参阅[拼写检查 API v7 参考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>发送请求

`SendRequestAsync`方法向必应拼写检查 REST API 发出 GET 请求，并返回响应：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

此方法可发送 GET 请求到`SpellCheck`API，使用请求 URL 指定的文本要转换和拼写检查模式。 然后将对响应进行读取，并将其返回到调用方法。

`SpellCheck` API 将在响应中，提供该请求是有效的表示请求成功，请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。 响应对象的列表，请参阅[响应对象](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

### <a name="processing-the-response"></a>处理响应

以 JSON 格式返回 API 响应。 以下 JSON 数据显示拼写错误的文本的响应消息`Go shappin tommorow`:

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

`flaggedTokens`数组包含的文本中已标记为正在拼写不正确或其语法不正确的单词数组。 如果找不到任何拼写或语法错误，则数组将不可用。 在该数组中的标记是：

- `offset` – 从零开始的偏移量从文本字符串的开头已标记的单词。
- `token` – 拼写不正确或语法不正确的文本字符串中的单词。
- `type` – 导致将标记的单词的错误的类型。 有两个可能值 –`RepeatedToken`和`UnknownToken`。
- `suggestions` – 将更正拼写或语法错误的单词的数组。 数组组成`suggestion`和`score`，这指示的建议的更正正确无误的置信度级别。

在示例应用程序，则 JSON 响应反序列化为`SpellCheckResult`实例，而且要返回到调用方法以显示的结果。 下面的代码示例演示如何`SpellCheckResult`实例处理用于显示：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此代码循环访问`FlaggedTokens`集合和替换任何拼写错误或与第一个建议的源文本中的语法不正确单词。 以下屏幕截图显示之前和之后的拼写检查：

![](spell-check-images/before-spell-check.png "拼写检查之前")

![](spell-check-images/after-spell-check.png "拼写检查后")

## <a name="summary"></a>总结

本文介绍了如何使用必应拼写检查 REST API 更正 Xamarin.Forms 应用程序中的拼写错误。 必应拼写检查执行上下文的拼写检查的文本，提供有关拼写错误的单词的内联建议。

## <a name="related-links"></a>相关链接

- [必应拼写检查文档](/azure/cognitive-services/bing-spell-check/)
- [使用 rest 样式 Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [必应拼写检查 API v7 参考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
