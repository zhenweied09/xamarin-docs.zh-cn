---
title: Xamarin.Essentials：故障排除
description: 本文档介绍如何解决使用 Xamarin.Essentials 库进行开发时遇到的问题。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3440b572514c02c8c2240dc99b09aecfde06fcb9
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898740"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials：故障排除

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>错误：检测到 Xamarin.Android.Support.Compat 版本冲突

更新 NuGet 包（或添加新包）时，如果其中某 Xamarin.Forms 项目使用 Xamarin.Essentials，则可能出现以下错误：

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

问题在于两个 NuGet 的依赖项不匹配。 通过手动添加同时支持两者的特定版本的依赖项（在本例中为 Xamarin.Android.Support.Compat）可解决此问题。

为此，请手动添加冲突源 NuGet，并使用“版本”列表选择特定版本。 目前，Xamarin.Android.Support.Compat 和 Xamarin.Android.Support.Core.Util NuGet 的版本 27.0.2.1 可解决此错误。

有关详细信息，请参阅[此博客文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)以及有关如何解决此问题的视频。

如果遇到任何问题或发现 bug，请在 [Xamarin.Essentials GitHub 存储库](http://github.com/xamarin/Essentials)上报告。
