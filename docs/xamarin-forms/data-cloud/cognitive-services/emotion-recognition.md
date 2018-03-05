---
title: "表情识别使用表情 API"
description: "表情 API 在映像中作为输入，采用面部表达式，并在图像中的每个表面情感一组返回置信度级别。 此文章介绍了如何使用表情 API 来识别表情，进行评级 Xamarin.Forms 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 159bd1b23eb7505c5d5629570a34d54e0525567e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="emotion-recognition-using-the-emotion-api"></a>表情识别使用表情 API

_表情 API 在映像中作为输入，采用面部表达式，并在图像中的每个表面情感一组返回置信度级别。此文章介绍了如何使用表情 API 来识别表情，进行评级 Xamarin.Forms 应用程序。_

![](~/media/shared/preview.png "此 API 已当前预发行版")

> [!NOTE]
> 表情 API 仍处于预览状态。 那里可能重大更改对最终发布之前 API。

## <a name="overview"></a>概述

表情 API 可以愤怒、 contempt、 厌恶、 不必担心、 幸福、 非特定于、 sadness 和意外情况发生，检测面部识别表达式中。 这些情感普遍和多个区域性传送通过相同的基本面部表情。 返回面部表情表情结果，以及表情 API 还返回检测到使用表面 API 的表面的边界框。 如果用户已调用表面 API，它们可以作为可选输入提交的表面矩形。 请注意，必须获取 API 密钥用于表情 API。 这可以在获取[入门免费](https://www.microsoft.com/cognitive-services/sign-up)microsoft.com 上。

通过客户端库，并通过 REST API，可以执行表情识别。 本文重点介绍在执行通过表情识别[Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)会从 NuGet 下载的客户端库。

表情 API 还可以用来识别在视频中，人员的面部表达式，并且返回其情感的摘要。 有关详细信息，请参阅[在视频中表情](https://www.microsoft.com/cognitive-services/emotion-api/documentation#emotion-in-video)microsoft.com 上。

有关表情 API 的详细信息，请参阅[表情 API 文档](https://www.microsoft.com/cognitive-services/emotion-api/documentation)microsoft.com 上。

## <a name="performing-emotion-recognition"></a>执行表情识别

表情识别被通过将图像流上载到表情 API。 图像文件大小不应为大于 4 MB，并受支持的文件的格式为 JPEG、 PNG、 GIF、 和 BMP。 中的映像，可检测的字体大小范围是 36 x 36 到 4096 x 4096 像素。 不会检测到在此范围之外的任何表面。

下面的代码示例显示的表情识别过程：

```csharp
using Microsoft.ProjectOxford.Emotion;
using Microsoft.ProjectOxford.Emotion.Contract;

var emotionClient = new EmotionServiceClient(Constants.EmotionApiKey);

using (var photoStream = photo.GetStream())
{
  Emotion[] emotionResult = await emotionClient.RecognizeAsync(photoStream);
  if (emotionResult.Any())
  {
    // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
    emotionResultLabel.Text = emotionResult.FirstOrDefault().Scores.ToRankedList().FirstOrDefault().Key;
  }
  // Store emotion as app rating
  ...
}
```

`EmotionServiceClient`必须创建实例来使用作为参数传递的表情 API 密钥执行表情识别`EmotionServiceClient`构造函数。

`RecognizeAsync`方法, 调用时`EmotionServiceClient`实例时，将表情 api 中，一个图像上载作为`Stream`。 API 密钥要提交到表情 API 时调用此操作。 未能提交有效的 API 密钥将导致在`Microsoft.ProjectOxford.Common.ClientException`引发的异常消息，指示已提交 API 密钥无效。

`RecognizeAsync`方法将返回`Emotion`数组，前提是已识别了表面。 每个映像，可以检测到的表面最大数目为 64，并按由表面矩形大小以降序顺序排列立方体表面。 如果检测到没有表面，空`Emotion`将返回的数组。

解释结果从表情 API，当检测到的表情应被视为具有最高的得分，表情如评分被规范化到其中一个的总和。 因此，示例应用程序将显示与面临的最大检测到的最高分数识别的表情在图中，如以下屏幕截图中所示：

![](emotion-recognition-images/emotion-recognition.png "表情识别")

## <a name="summary"></a>摘要

本文介绍了如何使用表情 API 来识别表情，进行评级 Xamarin.Forms 应用程序。 表情 API 在映像中作为输入，采用面部表达式，并在图像中的每个表面情感一组返回置信度。


## <a name="related-links"></a>相关链接

- [表情 API 文档](https://www.microsoft.com/cognitive-services/emotion-api/documentation)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)
- [表情 API](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)
