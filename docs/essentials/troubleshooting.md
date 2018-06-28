---
title: Xamarin.Essentials： 故障排除
description: 本文档介绍如何使用 Xamarin.Essentials 库进行开发时遇到的问题进行疑难解答。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 3dba315aec2475cb334110ba7555f773f4165aa1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066729"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials： 故障排除

![预发行 NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>错误： Xamarin.Android.Support.Compat 检测到的版本冲突

在更新 NuGet 程序包时，可能会发生以下错误 （或添加新的包） 与使用 Xamarin.Essentials Xamarin.Forms 项目：

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.7.0.17-preview -> Xamarin.Android.Support.CustomTabs 27.0.2 -> Xamarin.Android.Support.Compat (= 27.0.2) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

问题在于不匹配的两个 NuGets 的依赖关系。 这可以通过手动添加特定版本的依赖项来解决 (在这种情况下**Xamarin.Android.Support.Compat**)，可以支持这两个。

若要执行此操作，添加手动，则冲突的根源 NuGet，并使用**版本**列表以选择特定版本。 当前版本 27.0.2 的 Xamarin.Android.Support.Compat NuGet 将解决此错误。

请参阅[这篇博客文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)有关详细信息和如何解决此问题的视频。

如果遇到任何问题或 bug 请报告查找[Xamarin.Essentials GitHub 存储库](http://github.com/xamarin/Essentials)。
