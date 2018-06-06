---
title: 了解 Android API 级别
description: Xamarin.Android 具有多个 Android API 级别设置，确定与多个版本的 Android 应用程序的兼容性。 本指南说明了这些设置的含义、 如何配置它们，以及有何影响它们对你的应用程序在运行时。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: b942bb1be3441b1fb1a8bd65016914b3ecddbb26
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732315"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 级别

_Xamarin.Android 具有多个 Android API 级别设置，确定与多个版本的 Android 应用程序的兼容性。本指南说明了这些设置的含义、 如何配置它们，以及有何影响它们对你的应用程序在运行时。_


## <a name="quick-start"></a>快速入门

Xamarin.Android 公开三个 Android API 级别的项目设置：

-   [面向 Framework](#framework) &ndash;指定要在构建你的应用程序使用的框架。 此 API 级别时使用的*编译*Xamarin.Android 的时间。

-   [最低 Android 版本](#minimum)&ndash;指定你希望应用以支持的最旧的 Android 版本。 此 API 级别时使用的*运行*Android 的时间。

-   [面向 Android 版本](#target)&ndash;的你的应用程序的 Android 版本设计用于上运行的指定。 此 API 级别时使用的*运行*Android 的时间。

你可以为你的项目配置 API 级别之前，必须安装该 API 级别的 SDK 平台组件。 有关下载和安装 Android SDK 组件的详细信息，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 从开始年 8 月 2018年，Google 播放控制台将需要新应用目标 API 级别 26 (Android 8.0) 或更高版本。
现有应用程序将需要面向 API 级别 26 或更高版本中年 11 月 2018年开头。 有关详细信息，请参阅[提高应用程序的安全性和性能出现在年的 Google Play 上](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

通常情况下，所有三个 Xamarin.Android API 级别设置为相同的值。 上**应用程序**页上，设置**使用 Android 版本 （目标框架） 进行编译**到最新稳定的 API 版本 （或最小，具有所有所需的功能的 Android 版本）。
在以下屏幕截图中，目标框架设置为**Android 7.1 (API 级别 25-Nougat)**:

[![目标 Framework 版本默认为使用 Android 版本的编译](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

上**Android 清单**页上，将最低 Android 版本设置为**使用 SDK 版本进行编译使用**并将目标 Android 版本设置为相同的值 （在下面的示例的目标框架版本屏幕截图中，目标 Android 框架设置为**Android 7.1 (Nougat)**):

[![最小值和目标 Android 版本设置为目标框架版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果你想要维护向后兼容早期版本的 Android，设置**最低 Android 版本到目标**为所需应用程序以支持最早版本的 Android。 (请注意 API 级别 14，所需的最低 API 级别[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。)下面的示例配置支持从 API 级别 14 通过 API 级别 25 的 Android 版本：

[![使用 API 级别 25 编译 Nougat，最低 Android 版本设置为 API 级别 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

通常情况下，所有三个 Xamarin.Android API 级别设置为相同的值。 设置**目标框架**到最新稳定的 API 版本 （或最小，具有所有所需的功能的 Android 版本）。 若要设置**目标框架**，导航到**生成 > 常规**中**项目选项**。 在以下屏幕截图中，目标框架设置为**使用最新安装的平台 (8.0)**:

[![默认为使用最新安装平台的目标框架](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

可以下找到的最小值和目标 Android 版本设置**生成 > Android 应用程序**中**项目选项**。 将最低 Android 版本设置为**自动-使用目标框架版本**并将目标 Android 版本设置为相同的值的目标框架版本。 在以下屏幕截图中，目标 Android 框架设置为**Android 8.0 （API 级别 26）** 以匹配上面的目标框架设置：

[![在项目选项中设置的目标和 framework 级别](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果你想要维护向后兼容早期版本的 Android，更改**最低 Android 版本**为所需应用程序以支持最早版本的 Android。 请注意 API 级别 14，所需的最低 API 级别[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。
例如，下面的配置将作为 API 级别 14 尽早支持 Android 版本：

[![最小值和目标版本设置为自动的使用目标框架版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


如果你的应用程序支持多个 Android 版本，你的代码必须包含运行时检查以确保你的应用能够适应最低 Android 版本设置 (请参阅[运行时检查 Android 版本](#runtimechecks)下面有关详细信息)。 如果要使用或创建库，请参阅[API 级别和库](#libraries)下面有关最佳实践配置 API 级别库设置。



## <a name="android-versions-and-api-levels"></a>Android 版本和 API 级别

每个 Android 版本在 Android 平台的发展和新的 Android 版本发布时，分配有唯一的整数标识符，调用*API 级别*。 因此，每个 Android 版本对应于单个 Android API 级别。 由于用户安装在较旧也为最新版本的 Android 应用，因此必须设计真实的 Android 应用程序可以使用多个 Android API 级别。


### <a name="android-versions"></a>Android 版本

每个版本的 Android 将由多个名称：

-   Android 版本，如**Android 7.1**
-   一个代码名称，如_Nougat_
-   相应的 API 级别，如**API 级别 25**

Android 代码名称可能对应于多个版本和 API 级别 （如下面的列表中所示），但每个 Android 版本对应于恰好一个 API 级别。

此外，Xamarin.Android 定义*生成版本代码*，它将映射到当前已知的 Android API 级别。 下表可以帮助你 API 级别、 Android 版本、 代码名称和 Xamarin.Android 生成版本代码之间进行转换。

-   **API 27 (Android 8.1)** &ndash; _Oreo_自 2017 年 12 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.OMr1`

-   **API 26 (Android 8.0)** &ndash; _Oreo_自 2017 年 8 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.O`

-   **API 25 (Android 7.1)** &ndash; _Nougat_2016 年 12 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.NMr1`

-   **API 24 (Android 7.0)** &ndash; _Nougat_2016 年 8 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.N`

-   **API 23 (Android 6.0)** &ndash; _Marshmallow_2015 年 8 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.M`

-   **API 22 (Android 5.1)** &ndash; _棒糖形_2015 年 3 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.LollipopMr1`

-   **API 21 (Android 5.0)** &ndash; _棒糖形_2014 年 11 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Lollipop`

-   **API 20 (Android 4.4W)** &ndash; _Kitkat 监视_2014 年 6 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.KitKatWatch`

-   **API 19 (Android 4.4)** &ndash; _Kitkat_2013 年 10 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.KitKat`

-   **API 18 (Android 4.3)** &ndash; _果冻 Bean_2013 年 7 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **API 17 (Android 4.2-4.2.2)** &ndash; _果冻 Bean_2012 年 11 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **API 16 (Android 4.1-4.1.1)** &ndash; _果冻 Bean_2012 年 6 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.JellyBean`

-   **API 15 (Android 4.0.3-4.0.4)** &ndash; _冰激凌德桑威奇_2011 年 12 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **API 14 (Android 4.0 4.0.2)** &ndash; _冰激凌德桑威奇_2011 年 10 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **API 13 (Android 3.2)** &ndash; _Honeycomb_2011 年 6 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **API 12 (Android 3.1.x)** &ndash; _Honeycomb_2011 年 5 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **API 11 (Android 3.0.x)** &ndash; _Honeycomb_2011 年 2 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.HoneyComb`

-   **API 10 (Android 2.3.3-2.3.4)** &ndash; _小玩偶_2011 年 2 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **API 9 (Android 2.3-2.3.2)** &ndash; _小玩偶_2010 年 11 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.GingerBread`

-   **API 8 (Android 2.2.x)** &ndash; _Froyo_2010 年 6 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Froyo`

-   **API 7 (Android 2.1.x)** &ndash; _Eclair_2010 年 1 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.EclairMr1`

-   **API 6 (Android 2.0.1)** &ndash; _Eclair_2009 年 12 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Eclair01`

-   **API 5 (Android 2.0)** &ndash; _Eclair_2009 年 11 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Eclair`

-   **API 4 (Android 1.6)** &ndash; _圆环图_2009 年 9 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Donut`

-   **API 3 (Android 1.5)** &ndash; _Cupcake_2009 年 5 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Cupcake`

-   **API 2 (Android 1.1)** &ndash; _基_2009 年 2 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Base11`

-   **API 1 (Android 1.0)** &ndash; _基_2008 年 10 月发布。 生成版本的代码 `Android.OS.BuildVersionCodes.Base`


此列表所示，新的 Android 版本的发布频率&ndash;每年有时多个版本。 因此，可能运行你的应用的 Android 设备该领域包括的各种较旧和较新的 Android 版本。 如何保证你的应用程序将太多的不同版本的 Android 上运行一致而可靠地？ Android 的 API 级别可帮助你管理此问题。


### <a name="android-api-levels"></a>Android API 级别

每个 Android 设备运行在完全*一个*API 级别&ndash;此 API 级别保证是唯一的每个 Android 平台版本。 API 级别精确标识了你的应用程序可以调用; API 集的版本它标识作为开发人员的清单元素、 权限、 等针对该你代码的组合。 Android 的系统的 API 级别可帮助确定应用程序是否在设备上安装应用程序之前的 Android 系统映像与兼容的 Android。

当生成应用程序时，它包含以下 API 级别信息：

-   *目标*的应用生成上运行的 Android API 级别。

-   *最小*Android 设备必须运行你的应用的 Android API 级别。 

这些设置用于确保正确运行应用程序所需的功能在安装时在 Android 设备上可用。 否则，应用程序会阻止该设备上运行。 例如，如果 Android 设备的 API 级别低于最小为你的应用程序指定的 API 级别，在 Android 设备将阻止用户安装您的应用程序。


## <a name="project-api-level-settings"></a>项目 API 级别设置

以下各节说明如何使用 SDK Manager 来为你要设定为目标的 API 级别跟如何配置的详细说明准备好开发环境*目标框架*，*最小值Android 版本*，和*目标 Android 版本*Xamarin.Android 中的设置。


### <a name="android-sdk-platforms"></a>Android SDK 平台

你可在 Xamarin.Android 中选择目标或最低 API 级别之前，你必须安装到该 API 级别相对应的 Android SDK 平台版本。 目标框架、 最低 Android 版本和目标 Android 版本的可用选项的范围仅限于具有安装的范围的 Android SDK 版本。 可以使用 SDK 管理器以验证安装了所需的 Android SDK 版本，而你可以使用它将添加任何新的 API 级别，你需要为你的应用。 如果你不熟悉如何安装 API 级别，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目标 Framework

*目标框架*(也称为`compileSdkVersion`) 是您的应用程序在生成时被编译为的特定 Android 框架版本 （API 级别）。 此设置指定哪些 Api 应用*需要*时运行，但它不起 Api 个在其上实际可用向应用程序安装时使用。 因此，更改目标框架设置不会更改运行时行为。

目标框架标识针对链接你的应用程序的库版本&ndash;这将确定可以在应用中使用的 Api。 例如，如果你想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) Android 5.0 棒糖形中引入的方法，必须将目标框架设置为**API 级别 21 （棒糖形）** 或更高版本。 如果将你的项目的目标框架设置为 API 级别如**API Level 19 (KitKat)** 和尝试调用`SetCategory`方法代码中的，您将得到编译错误。

我们建议你始终编译与*最新*可用的目标框架版本。 执行此操作向你提供了有用的警告消息可能由你的代码调用任何弃用的 Api。 使用最新的目标框架版本一点尤其重要，当你使用最新的支持库版本&ndash;每个库要求你的应用能够编译在该支持库的最小的 API 级别或更高版本。 


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要访问 Visual Studio 中的目标框架设置，打开项目属性中的**解决方案资源管理器**和选择**应用程序**页：

[![应用程序项目属性页](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

目标框架设置通过在下的下拉列表菜单中选择一个 API 级别**使用 Android 版本进行编译**如上所示。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要访问 Mac 的目标框架设置 Visual Studio 中，右键单击项目名称，然后选择**选项**; 这将打开**项目选项**对话框。 在此对话框中，导航到**生成 > 常规**如下所示：

[![生成项目选项页的常规部分](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

在右侧的下拉列表菜单中选择一个 API 级别的目标框架设置**目标框架**如上所示。

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>最低 Android 版本

*最低 Android 版本*(也称为`minSdkVersion`) 是最早版本的 Android OS （即，最低 API 级别），可以安装和运行你的应用程序。 默认情况下，应用可以仅会在匹配的目标框架设置的设备上安装或更高版本;如果最低 Android 版本设置为*较低*高于目标框架设置中，你的应用程序还可以在早期版本的 Android 上运行。 例如，如果目标框架设置为**Android 7.1 (Nougat)** 和最低 Android 版本设置为**Android 4.0.3 （冰激凌德桑威奇）**，可以在 API 级别 15 从任何平台上安装你的应用程序为 API 级别 25，（含)。

虽然你的应用程序可能已成功生成，并在此系列的平台上安装，但这并不保证它已成功将*运行*所有这些平台上。 例如，如果你的应用程序安装在**Android 5.0 （棒糖形）** 和你的代码调用仅在中可用的 API **Android 7.1 (Nougat)** 和更高版本，你的应用程序将获取运行时错误和可能崩溃。 因此，请确保你的代码必须&ndash;在运行时&ndash;，它调用仅在 Android 设备上运行支持的 Api。 换而言之，你的代码必须包含显式的运行时检查来确保你的应用程序仅在不够新，无法支持它们的设备上使用较新的 Api。
[运行时检查 Android 版本](#runtimechecks)本指南中，将在后面说明如何将这些运行时检查添加到你的代码。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要访问 Visual Studio 中的最低 Android 版本设置，打开项目属性中的**解决方案资源管理器**和选择**Android 清单**页。 在下的下拉列表菜单**最低 Android 版本**可以为你的应用程序选择最低 Android 版本：

[![目标选项设置为使用 SDK 版本进行编译到的最小 Android](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果你选择**使用 SDK 版本进行编译使用**，最低 Android 版本将作为目标框架设置相同。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要访问 Mac 的目标框架设置 Visual Studio 中，右键单击项目名称，然后选择**选项**; 这将打开**项目选项**对话框。 导航到**生成 > Android 应用程序**。
使用右侧的下拉列表菜单**最低 Android 版本**，你可以为你的应用程序设置最低 Android 版本：

[![最低 Android 版本设置为自动的使用目标框架版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果你选择**自动&ndash;使用目标框架版本**，最低 Android 版本将作为目标框架设置相同。

-----


<a name="target" />

### <a name="target-android-version"></a>目标 Android 版本

*目标 Android 版本*(也称为`targetSdkVersion`) 是一个 API 的 Android 设备级别应用程序需要运行。 Android 使用此设置来确定是否启用的任何兼容性行为&ndash;这可确保你的应用程序继续按你期望的方式。 Android 使用您的应用程序的目标 Android 版本设置来找出哪些行为更改可应用于您的应用程序而不会破坏其 （这是 Android 如何提供向前兼容性）。

目标框架和目标 Android 版本，而同时将非常类似的名称，不是相同的操作。 目标框架设置目标 API 级别将信息传递给 Xamarin.Android 用于*编译时间*，而目标 Android 版本目标 API 级别将信息传递给 Android 用于*运行时间*（时应用程序已安装并在设备上运行）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要访问 Visual Studio 中的此设置，打开项目属性中的**解决方案资源管理器**和选择**Android 清单**页。 在下的下拉列表菜单**目标 Android 版本**可以为你的应用程序中选择目标 Android 版本：

[![目标 Android 版本设置为使用 SDK 版本进行编译](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

我们建议您显式设置为最新版本的用于测试你的应用的 Android 目标 Android 版本。 理想情况下，它应设置为最新的 Android SDK 版本&ndash;此选项，可以使用新 Api 之前通过的行为更改的工作。 对于大多数开发人员，我们*不这样做*建议目标 Android 版本设置成**使用 SDK 版本进行编译使用**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要访问 Mac 的目标框架设置 Visual Studio 中，右键单击项目名称，然后选择**选项**; 这将打开**项目选项**对话框。 导航到**生成 > Android 应用程序**。
使用右侧的下拉列表菜单**目标 Android 版本**，你可以为你的应用程序设置目标 Android 版本：

[![目标 Android 版本设置为自动的使用目标框架版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

我们建议您显式设置为最新版本的用于测试你的应用的 Android 目标 Android 版本。 理想情况下，它应设置为最新可用的 Android SDK 版本&ndash;此选项，可以使用新 Api 之前通过的行为更改的工作。 对于大多数开发人员，我们不建议将目标 Android 版本设置为**自动-使用目标框架版本**。

-----

一般情况下，目标 Android 版本应受最低 Android 版本和目标框架。 即：

**最低 Android 版本 < = 目标 Android 版本 < = 目标框架**

有关 SDK 级别的详细信息，请参阅 Android 开发人员[使用 sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)文档。


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>对于 Android 版本的运行时检查

发布每个新的 Android 版本时，framework API 更新以提供新或替换功能。 在少数例外情况，从早期的 Android 版本的 API 功能将会传递到较新的 Android 版本，无需进行修改。 因此，如果你的应用程序在特定的 Android API 级别上运行，它通常将能够在无需修改即可更高版本的 Android API 级别上运行。 但是，如果你还想要在早期版本的 Android 上运行你的应用程序？

如果你选择是最低 Android 版本*较低*与您的目标框架设置可能不可用向应用程序在运行时的一些 Api。 但是，你的应用程序仍然可以运行在更早版本的设备，但功能会减弱。 对于不是为最低 Android 版本设置相对应的 Android 平台上可用的每个 API，你的代码必须显式检查的值`Android.OS.Build.VERSION.SdkInt`属性来确定应用程序运行的平台的 API 级别。 如果 API 级别为*较低*要比最低 Android 版本支持的 API 调用，则你的代码必须找到一种方法才能正常工作，无需进行此 API 调用。

例如，让我们假设我们想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/)方法进行分类通知上运行时**Android 5.0 棒糖形**（和更高版本），但我们仍然希望到我们的应用程序如在早期版本的 Android 上运行**Android 4.1 果冻 Bean** (其中`SetCategory`不可用)。 本指南的开头的 Android 版本表中引用，我们看到的生成版本代码**Android 5.0 棒糖形**是`Android.OS.BuildVersionCodes.Lollipop`。 若要支持较旧版本的 Android where`SetCategory`是不可用，我们的代码可以检测在运行时 API 级别和有条件地调用`SetCategory`API 级别时才大于或等于棒糖形生成版本代码：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此示例中，我们的应用程序的目标框架设置为**Android 5.0 (API 级别 21)** 并且其最低 Android 版本设置为**Android 4.1 (API 级别 16)**。 因为`SetCategory`位于 API 级别`Android.OS.BuildVersionCodes.Lollipop`和更高版本，此代码示例将调用`SetCategory`仅当有实际可用&ndash;它将*不*尝试调用`SetCategory`时 API级别是 16、 17、 18、 19 或 20。 仅的范围内通知未排序正确 （因为它们不按类型进行分类），但仍然发布通知来警告用户，在这些较早的 Android 版本上有所减少的功能。 我们的应用仍然正常工作，但其功能会稍微降低。

一般情况下，生成版本检查可帮助你决定在运行时之间执行某项操作与旧的方法的新方法的代码。 例如：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

不存在快速而简单规则，它说明如何减少或在较旧缺少一个或多个 Api 的 Android 版本运行时修改应用的功能。 在某些情况下 (例如，在`SetCategory`上面示例中)，只需不可用时，只需省略 API 调用。 但是，在其他情况下，你可能需要为时实现替代功能`Android.OS.Build.VERSION.SdkInt`检测到小于 API 级别应用程序需要以提供其最佳体验。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>API 级别和库

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

当你创建 Xamarin.Android 库项目 （如类库或绑定库） 时，你可以配置仅的目标框架设置&ndash;最低 Android 版本和目标 Android 版本设置将不可用。 这是因为没有任何**Android 清单**页：

[![仅使用 Android 版本选项编译可用](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

当创建 Xamarin.Android 库项目时，没有任何**Android 应用程序**页可在其中配置最低 Android 版本和目标 Android 版本&ndash;最低 Android 版本和目标Android 版本设置不可用。
这是因为没有任何**生成 > Android 应用程序**页):

[![生成没有最小值和目标版本选项常规页](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

最低 Android 版本和目标 Android 版本设置将不可用因为生成的库不是一个独立应用&ndash;库可以在任何 Android 版本，具体取决于与一起打包的应用上运行。 你可以指定如何将是库*编译*，但你无法预测的平台 API 级别将在上运行库。 这一点时使用或创建库,，则应遵循以下最佳实践：

-   **使用 Android 库时**&ndash;如果你正在 Android 库应用程序中，请确保你的应用的目标框架设置到的 API 级别已设置*至少最高可达*目标库的框架设置中。

-   **创建 Android 库时**&ndash;如果要创建 Android 库为使用其他应用程序，请务必将其目标框架设置为编译所需的最低 API 级别。

这些最佳做法建议以帮助防止这种情况下尝试调用不是在运行时 （这可能导致应用崩溃） 可用的 API 库。 如果你是库开发人员，你应尽可能限制你的小型和获得广泛认可的总的 API 图面区域一部分的 API 调用的使用情况。 因此，这样做有助于确保可以更宽的范围的 Android 版本间安全地使用你的库。


## <a name="summary"></a>总结

本指南介绍如何使用 Android API 级别来管理跨不同版本的 Android 应用程序兼容性。 它提供的详细的步骤用于配置 Xamarin.Android*目标框架*，*最低 Android 版本*，和*目标 Android 版本*项目设置。 它提供有关使用 Android SDK 管理器安装 SDK 包的说明包括示例说明了如何编写代码来处理不同的 API 级别，在运行时，并且说明了如何管理时，创建或使用 Android 库的 API 级别。 它还提供了 API 级别与 Android 版本数字 （如 Android 4.4)、 Android 版本名称 （例如 Kitkat) 和 Xamarin.Android 生成版本代码的完整列表。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [选取 compileSdkVersion、 minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [API 级别是什么？](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [用、 标记和内部版本号](https://source.android.com/source/build-numbers)
