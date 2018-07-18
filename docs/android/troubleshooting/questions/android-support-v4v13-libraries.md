---
title: 更智能的 Xamarin Android 支持 v4 / v13 NuGet 包
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 47ae63fbd7062be97be04bfc1f1244ec63a1c5bd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763951"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更智能的 Xamarin Android 支持 v4 / v13 NuGet 包

## <a name="about-the-android-support-libraries"></a>有关 Android 支持库

Google 已创建支持库要提供给较旧版本的 Android 新功能。 支持库一般情况下，其名称，即它们与相兼容的最低 Android API 级别中提供的版本号 (例如： 支持 v4 只可用在 API 级别 4 和更高版本。 在此的详细信息[堆栈溢出讨论](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13))。 

两个支持库：`Support-v4`和`Support-v13`不能使用一起在相同的应用中，即，它们是互相排斥。 这是因为`Support-v13`实际包含的所有类型和实现`Support-v4`。 如果您尝试并同时在同一项目引用将会遇到重复的类型错误。

## <a name="problems-with-referencing"></a>与引用的问题

由于`Support-v4`已变得很常见，大量的第三方库现在依赖于它。 它们可能已选择以相反，依赖于支持 v13 但更常见依赖于_v4_由于，它将授予使用这些第三方库的任何应用的支持一直到 4 的 API 级别选项。

如果 Xamarin 第三方库引用`Xamarin.Android.Support.v4.dll`绑定到`Support-v4`，任何应用程序使用此库还必须引用`Xamarin.Android.Support.v4.dll`。 这将成为问题，当相同的应用程序还想要使用中的功能的某些`Xamarin.Android.Support.v13.dll`绑定到`Support-v13`。 如果引用这两种绑定，将会遇到重复的类型错误。

## <a name="type-forwarded-v4-binding-assembly"></a>类型转发 v4 绑定程序集

若要获取解决此问题，我们已创建了一种特殊`Xamarin.Android.Support.v4.dll`程序集不具有实现，但只需`[assembly: TypeForwardedTo (..)]`转发的所有属性`Support-v4`类型中实现到`Xamarin.Android.Support.v13.dll`程序集。

这意味着开发人员可以引用此_类型转发_中其应用程序将满足对引用的程序集`Xamarin.Android.Support.v4.dll`任何第三方库，同时仍然允许通过`Xamarin.Android.Support.v13.dll`要在应用程序中使用。

## <a name="nuget-assistance"></a>NuGet 协助

尽管开发人员可以手动添加的正确引用必要时，我们就能够使用 NuGet 若要帮助选择适当的程序集 (任一法线_v4_绑定或类型转发_v4_程序集) 时安装 NuGet 包。

因此， `Xamarin.Android.Support.v4` NuGet 包现在包含以下逻辑：

如果你的应用面向 API 级别 13 (小玩偶 3.2) 或更高版本：

*   `Xamarin.Android.Support.v13` NuGet 将自动添加作为依赖项
*   _类型转发_`Xamarin.Android.Support.v4.dll`将项目中引用

如果你的应用程序面向任何内容低于 API 级别 13，你将获得正常`Xamarin.Android.Support.v4.dll`在你的项目中引用的绑定。

## <a name="do-i-have-to-use-support-v13"></a>我是否必须使用支持 v13？

如果你的应用面向 API 级别 13 或更高版本，并且你选择使用`Xamarin Android Support-v4`NuGet 包，则`Xamarin Android Support v13`NuGet 包是必需的依赖关系。

我们认为很小 （17 kb 的不同的两个的.jar 文件） 的应用程序大小增加很值得的兼容性和更少这会导致的问题。

如果你是一定不要将使用`Support-v4`在应用程序面向 API 级别 13 或更高版本，则可以始终手动下载`.nupkg`，提取它，以及引用程序集。
