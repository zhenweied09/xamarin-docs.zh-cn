---
title: "我的 Android Resource.designer.cs 文件不会更新"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: b169bcc64af15de3d87bfb7f8059b4251f1a3ad9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>我的 Android Resource.designer.cs 文件不会更新

> [!NOTE]
> **注意：** Xamarin Studio 5.1.4 及更高版本中已解决此问题。 但是，如果 Visual Studio for Mac 发生问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。

在 Xamarin.Studio 5.1 bug 以前通过部分或完全删除.csproj 文件中的 xml 代码损坏.csproj 文件。 这将导致重要组成部分 Android 生成系统 （如更新 Android Resource.designer.cs） 失败。 从稳定 5.1.4 开始发布在年 7 月 15 日，则此 bug 已修复;但在许多情况下的项目文件如下所示手动修复。


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>两种可能的方法来修复项目文件

### <a name="either"></a>二者之一：

1) 创建一个全新的 Xamarin.Android 应用程序项目，将设置所有项目属性以匹配旧项目，然后添加你的资源、 源文件等的所有回项目。

### <a name="or"></a>或

2) 备份原始项目的.csproj 文件，然后在文本编辑器中打开它，并将从完全生成的.csproj 文件重新添加进来缺少的元素。

### <a name="if-this-does-not-solve-the-problem"></a>如果这未解决的问题

后对这些元素的试验，你可能注意到，后重新添加元素和重新生成项目，将更新 Resource.designer.cs 文件，但然后你可能仍需要关闭并重新打开解决方案以获取代码自动补全来识别Resource.designer.cs 中包含的新类型。 
