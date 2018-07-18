---
title: Xamarin.Essentials： 故障排除
description: 本文档介绍如何使用 Xamarin.Essentials 库进行开发时遇到的问题进行疑难解答。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947369"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials： 故障排除

![预发行版 NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>错误： 为 Xamarin.Android.Support.Compat 检测到的版本冲突

更新 NuGet 包时，可能出现以下错误 （或添加新的包） 与使用 Xamarin.Essentials 的 Xamarin.Forms 项目：

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

问题在于不匹配的两个 Nuget 依赖项。 这可以通过手动添加特定版本的依赖项解析 (在这种情况下**Xamarin.Android.Support.Compat**)，可以支持这两个。

若要执行此操作，添加 NuGet 的手动冲突的源，并使用**版本**列表以选择特定版本。 当前版本的 Xamarin.Android.Support.Compat 和 Xamarin.Android.Support.Core.Util NuGet 27.0.2.1 将解决此错误。

请参阅[这篇博客文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)有关详细信息和如何解决此问题的视频。

如果遇到任何问题或 bug 请报告其查找[Xamarin.Essentials GitHub 存储库](http://github.com/xamarin/Essentials)。
