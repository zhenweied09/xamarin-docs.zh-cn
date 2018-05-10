---
title: 手动为 Xamarin 创建 NuGet 包
description: 此页包含可帮助生成面向 Xamarin 平台的 NuGet 包的一些提示。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 510c27cb54b91c837ca6f6b7a93f944f13097f0c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手动为 Xamarin 创建 NuGet 包

_此页包含可帮助生成面向 Xamarin 平台的 NuGet 包的一些提示。_

> [!NOTE]
> Xamarin Studio 6.2 （和 Visual Studio for Mac） 包括以下功能_自动_从 PCL、.NET 标准或共享项目中生成 NuGet 包。 请参阅[代码共享的多平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)更多详细信息的指南。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 包 Xamarin 配置文件

NuGet 网站[支持多个.NET Framework 版本和配置文件](https://docs.nuget.org/create/enforced-package-conventions)讨论如何支持不同的 Microsoft 框架和配置文件，但不包括使用 Xamarin 的目标框架名称。

目前正在使用的主要 Xamarin 目标框架：

* **MonoAndroid** -Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS[统一 API](~/cross-platform/macios/unified/index.md) （支持 64 位）
* **Xamarin.Mac** -Xamarin.Mac 的移动配置文件，这等效于 Xamarin.iOS 和 Xamarin.Android API 图面。

还有针对较旧的 iOS[经典 API](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS 经典 API

A **.nuspec**针对所有这些文件将如下所示：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述将忽略任何可移植类库。

大多数 **.nuspec**文件指定的目标框架的版本号，但它是可选的如果您的程序集适用于该目标框架的所有版本。 因此，如果你的目标已**lib\MonoAndroid**这就意味着它的任何版本的 Xamarin.Android 工作。

你可以指定一组不带小数点的数字的版本或可以指定它使用位小数。 小数点 NuGet 将只需采用每个数量而无需将其转换为一个版本通过插入。 之间的每一位。

在上述"MonoAndroid10"意味着"Android 1.0"。 这仅表示项目的[目标框架](~/android/app-fundamentals/android-api-levels.md)需要 MonoAndroid 1.0 或更高版本。 在指定的版本`<TargetFrameworkVersion>`项目文件中的元素。

若要阐明：

- **MonoAndroid403**匹配 Android 4.0.3 和更高版本 （即 API 级别 15）
- **Xamarin.iOS10**匹配 Xamarin.iOS 1.0 和更高版本
- **Xamarin.iOS1.0**还将匹配 Xamarin.iOS 1.0 和更高版本


## <a name="pcl-nugets-with-platform-dependencies"></a>PCL NuGets 包括平台依赖项

PCL 配置文件有限的哪些.NET framework Api 可以访问它们，并且它们可以肯定是无法访问特定于平台的代码。 这些第三方链接讨论了不同的方法，用于创建使用 PCL 和本机 Api 来提供适用于 Xamarin 和其他平台的兼容性的 NuGet 程序包：

- [如何为你进行可移植库工作](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [诱饵 PCL 技巧](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [创建 NuGet PCL 配合 Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

此外部[PCL 配置文件列表的 NuGet 目标名称](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)也是一个有用的参考。

## <a name="examples"></a>示例

您可以参考一些开放源代码示例：

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) – 编写使用 System.Net.Http，对应用程序，但删除此库和它将会明显更快 (视图[源](https://github.com/paulcbetts/ModernHttpClient))。
- [**展开**](https://www.nuget.org/packages/Splat/) – 库以使跨平台的操作应 (视图[源](https://github.com/paulcbetts/Splat))。
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -用于呈现矢量图形于.NET 的跨平台库 (视图[源](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec))。


## <a name="related-links"></a>相关链接

- [Nugetizer 3000 自动 Nuget 创建](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [在更新 NuGets 适用于 iOS 64 位](http://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [在你的项目包括 NuGet](/visualstudio/mac/nuget-walkthrough/index.md)
