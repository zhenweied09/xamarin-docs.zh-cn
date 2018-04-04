---
title: 表情识别使用面临的 API
description: 表面 API 在映像中作为输入，采用面部表达式并返回数据包含在映像中的每个表面情感一组之间的置信度级别。 此文章介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 49e53425dbaf3aadd74d02ab030929e3311c7c8c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>表情识别使用面临的 API

_表面 API 在映像中作为输入，采用面部表达式并返回数据包含在映像中的每个表面情感一组之间的置信度级别。此文章介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

表面 API 可以执行表情检测，以检测愤怒、 contempt、 厌恶，不必担心、 幸福、 非特定、 sadness 和意外情况发生，面部表情。 这些情感普遍和多个区域性传送通过相同的基本面部表情。 返回面部表情表情结果，以及表面 API 还可以返回检测到的表面的边界框。 请注意，必须获取 API 密钥用于表面 API。 这可以在获取[重认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

通过客户端库，并通过 REST API，可以执行表情识别。 本文重点介绍在执行通过表情识别[Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)会从 NuGet 下载的客户端库。

表面 API 还可以用来识别在视频中，人员的面部表达式，并且可以返回其情感的摘要。 有关详细信息，请参阅[如何实时分析视频](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

有关表面 API 的详细信息，请参阅[表面 API](/azure/cognitive-services/face/overview/)。

## <a name="performing-emotion-recognition"></a>执行表情识别

表情识别被通过将图像流上载到表面 API。 图像文件大小不应为大于 4 MB，并受支持的文件的格式为 JPEG、 PNG、 GIF、 和 BMP。

下面的代码示例显示的表情识别过程：

```csharp
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

var faceServiceClient = new FaceServiceClient(Constants.FaceApiKey, Constants.FaceEndpoint);
// e.g. var faceServiceClient = new FaceServiceClient("a3dbe2ed6a5a9231bb66f9a964d64a12", "https://westus.api.cognitive.microsoft.com/face/v1.0/detect");

var faceAttributes = new FaceAttributeType[] { FaceAttributeType.Emotion };
using (var photoStream = photo.GetStream())
{
    Face[] faces = await faceServiceClient.DetectAsync(photoStream, true, false, faceAttributes);
    if (faces.Any())
    {
        // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
        emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
    }
    // Store emotion as app rating
    ...
}
```

`FaceServiceClient`必须创建实例来执行表情识别，与表面 API 密钥和终结点正在作为参数传递给`FaceServiceClient`构造函数。

> [!NOTE]
> 在为你用于获取你的订阅密钥你面临 API 调用中，必须使用同一区域。 例如，如果你获得你的订阅密钥从`westus`区域，表面检测终结点将是`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

`DetectAsync`方法, 调用时`FaceServiceClient`实例时，将面临 api 中，一个图像上载作为`Stream`。 API 密钥要提交到表面 API 时调用此操作。 未能提交有效的 API 密钥将导致在`Microsoft.ProjectOxford.Face.FaceAPIException`引发的异常消息，指示已提交 API 密钥无效。

`DetectAsync`方法将返回`Face`数组，前提是已识别了表面。 每个返回的字体包含一个矩形，以指示其位置，与一系列由指定的可选表面属性结合使用`faceAttributes`参数`DetectAsync`方法。 如果检测到没有表面，空`Face`将返回的数组。

解释结果从表面 API，当检测到的表情应被视为具有最高的得分，表情如评分被规范化到其中一个的总和。 因此，示例应用程序将显示与面临的最大检测到的最高分数识别的表情在图中，如以下屏幕截图中所示：

![](emotion-recognition-images/emotion-recognition.png "表情识别")

## <a name="summary"></a>总结

本文介绍了如何使用表面 API 来识别表情，进行评级 Xamarin.Forms 应用程序。 表面 API 在映像中作为输入，采用面部表达式并返回包括置信度在映像中的每个表面情感一组之间的数据。

## <a name="related-links"></a>相关链接

- [不会遇到 API](/azure/cognitive-services/face/overview/)。
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)
