---
title: 可以到较新的 NuGet 包更新 Xamarin.Forms 默认模板？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fc479b4b0651e3312b855673730be21c2076d833
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049829"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>可以到较新的 NuGet 包更新 Xamarin.Forms 默认模板？

本指南使用 Xamarin.Forms.NET 标准库模板作为示例，但相同的常规方法也适用于 Xamarin.Forms 共享项目模板。 使用 Xamarin.Forms 1.5.1.6471 到 2.1.0.6529 从更新的示例编写此指南但相同的步骤可以改为将其他版本设置为默认值。

1.  复制原始模板`.zip`从：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  解压缩`.zip`到临时位置。

3.  将窗体包的旧版本出现的所有更改为你想要使用的新版本。
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    示例： `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  更改主要的"name"元素[多项目模板文件](http://msdn.microsoft.com/library/ms185308.aspx)(`Xamarin.Forms.PCL.vstemplate`) 以确保其唯一性。 例如：
    > <Name>空白应用 （Xamarin.Forms 可移植）-2.1.0.6529</Name>

5.  重新压缩整个模板文件夹。 请确保要匹配的原始文件结构`.zip`文件。 `Xamarin.Forms.PCL.vstemplate`文件应在顶部`.zip`文件，不在任何文件夹内的。

6.  在你每个用户的 Visual Studio 模板文件夹中创建的"移动应用程序"子目录：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  将新的压缩向上模板文件夹复制到新的"移动应用程序"目录。

8.  下载与步骤 3 中的版本匹配的 NuGet 程序包。 例如， [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (另请参阅[ http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file))，并将其复制到相应的子文件夹的 Xamarin Visual Studio 扩展文件夹：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
