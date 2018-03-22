---
title: "更新现有 Xamarin.Forms 应用"
description: "请按照下列步骤以更新现有 Xamarin.Forms 应用程序使用统一的 API 并更新到版本 1.3.1"
ms.topic: article
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d2f14510e5968ebe24bd297365416fa8aa5a0c59
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="updating-existing-xamarinforms-apps"></a>更新现有 Xamarin.Forms 应用

_请按照下列步骤以更新现有 Xamarin.Forms 应用程序使用统一的 API 并更新到版本 1.3.1_

> [!IMPORTANT]
> 因为 Xamarin.Forms 1.3.1 是支持统一 API 的第一个版本，则应更新整个解决方案在迁移到统一的 iOS 应用程序在同一时间使用最新版本。 这意味着，除了更新统一支持的 iOS 项目，你还需要在编辑代码_所有_解决方案中的项目。

在两个步骤中执行更新：

1. 将 iOS 应用程序迁移到使用 Visual Studio 中迁移工具的 Mac 的生成统一的 API。

    - 使用迁移工具自动更新项目。

    - 更新 iOS 本机 Api 说明中所述[更新 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)（专门在自定义呈现器或依赖关系服务代码中）。

2. 更新到 Xamarin.Forms 1.3 版整个解决方案。

    1. 安装 Xamarin.Forms 1.3.1 NuGet 包。

    2. 更新`App`共享代码中的类。

    3. 更新`AppDelegate`的 iOS 项目中。

    4. 更新`MainActivity`Android 项目中。

    5. 更新`MainPage`Windows Phone 项目中。

## <a name="1-ios-app-unified-migration"></a>1.iOS 应用 （统一迁移）

在迁移过程中的需要升级到版本 1.3，支持统一 API 的 Xamarin.Forms。 为了使要创建的正确的程序集引用，我们首先需要更新 iOS 项目以使用统一的 API。

### <a name="migration-tool"></a>迁移工具

单击 iOS 项目，以便选择它，然后选择**项目 > 迁移到 Xamarin.iOS 统一 API...**并同意出现的警告消息。

![](updating-xamarin-forms-apps-images/beta-tool1.png "选择项目 > 将迁移到 Xamarin.iOS 统一 API...并同意出现的警告消息")

这将自动:

 - 更改项目类型以支持统一 64 位 API。
 - 更改对的 framework 引用**Xamarin.iOS** (替换旧**monotouch**引用)。
 - 更改代码以移除中的命名空间引用`MonoTouch`前缀。
 - 更新**csproj**文件以用于统一 API 正确生成目标。

**干净**和**生成**项目以确保没有要修复的其他错误。 任何进一步的操作应该不始终要求。 中的更详细地说明了这些步骤[统一 API 文档](~/cross-platform/macios/unified/updating-ios-apps.md)。

### <a name="update-native-ios-apis-if-required"></a>更新本机 iOS Api （如果需要）

如果你已添加其他 iOS 本机代码 （如自定义呈现器或依赖关系服务） 可能需要执行其他手动代码修补程序。 重新编译应用程序，并参考[更新现有 iOS 应用说明](~/cross-platform/macios/unified/updating-ios-apps.md)有关其他信息可能需要的更改。 [这些提示](~/cross-platform/macios/unified/updating-tips.md)也将帮助确定所需的更改。

## <a name="2-xamarinforms-131-update"></a>2.Xamarin.Forms 1.3.1 更新

一旦 iOS 应用程序已更新为统一 API，解决方案的其余部分将需要更新到 Xamarin.Forms 1.3.1 版。 这包括：

 - 正在更新每个项目中的 Xamarin.Forms NuGet 包。
 - 更改代码以使用新的 Xamarin.Forms `Application`， `FormsApplicationDelegate` (iOS)、 `FormsApplicationActivity` (Android)、 和`FormsApplicationPage`(Windows Phone) 类。

下面说明了这些步骤：

### <a name="21-update-nuget-in-all-projects"></a>2.1 更新所有项目中的 NuGet

更新为 1.3.1 的 Xamarin.Forms NuGet 包管理器使用的解决方案中的所有项目的预发行： PCL 中 （如果存在），iOS、 Android 和 Windows Phone。 建议你**删除并重新添加**更新为版本 1.3 的 Xamarin.Forms NuGet 包。

**注意：** Xamarin.Forms 版本 1.3.1 功能当前处于*预发行版*。 这意味着您必须选择**预发行版**选项在 NuGet 中通过 （为刻度的框在 Visual Studio for Mac） 或 Visual Studio 中的下拉列表列表以查看最新的预发行版本。

> [!IMPORTANT]
> 如果使用 Visual Studio，请确保安装最新版本的 NuGet 包管理器。 较旧版本的 NuGet Visual Studio 中将不会正确安装 Xamarin.Forms 1.3.1 的统一版本。 转到**工具 > 扩展和更新...** ，然后单击**已安装**列表来检查是否**for Visual Studio 的 NuGet 包管理器**至少为版本 2.8.5。 如果它之前，请单击**更新**下载最新版本的列表。

一旦你已经升级到 Xamarin.Forms 1.3.1 的 NuGet 包，在每个要升级到新的项目中进行以下更改`Xamarin.Forms.Application`类。

### <a name="22-portable-class-library-or-shared-project"></a>2.2 可移植类库 （或共享的项目）

更改**App.cs**文件，以便：

 - `App`类现在继承自`Application`。
 - `MainPage`属性设置为你想要显示的第一内容页。

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

我们已完全删除`GetMainPage`方法，并改为设置`MainPage`*属性*上`Application`子类。

此新`Application`基类还支持`OnStart`， `OnSleep`，和`OnResume`替代来帮助你管理你的应用程序生命周期。

`App`类然后传递到新`LoadApplication`在每个应用程序项目中，如下所述的方法：

### <a name="23-ios-app"></a>2.3 iOS 应用程序

更改**AppDelegate.cs**文件，以便：

 - 类继承自`FormsApplicationDelegate`(而不是`UIApplicationDelegate`以前)。
 - `LoadApplication` 使用的新实例调用`App`。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 android 应用程序

更改**MainActivity.cs**文件，以便：

 - 类继承自`FormsApplicationActivity`(而不是`FormsActivity`以前)。
 - `LoadApplication` 调用时使用的新实例 `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 Windows Phone 应用

我们需要更新**MainPage**的 XAML 和代码隐藏。

更改**MainPage.xaml**文件，以便：

 - 根 XAML 元素应为`winPhone:FormsApplicationPage`。
 - `xmlns:phone`属性应为*更改*到 `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

下面显示的更新的示例-只需编辑 （属性的其余部分应保持不变） 这些内容：

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

更改**MainPage.xaml.cs**文件，以便：

 - 类继承自`FormsApplicationPage`(而不是`PhoneApplicationPage`以前)。
 - `LoadApplication` 使用 Xamarin.Forms 的新实例调用`App`类。 你可能需要完全限定此引用，因为 Windows Phone 具有其自己的`App`已定义的类。

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>疑难解答

有时，你将看到类似于在更新的 Xamarin.Forms NuGet 包后错误。 NuGet 更新程序不会完全删除对从较旧版本的引用时发生你**csproj**文件。

>你\_PROJECT.csproj： 错误： 此项目引用在此计算机缺少的 NuGet 程序包。 启用 NuGet 程序包还原下载它们。  有关详细信息，请参阅http://go.microsoft.com/fwlink/?LinkID=322105。 缺少的文件是.../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets。 (你\_项目)

若要修复这些错误，请打开**csproj**文件在文本编辑器中，并查找`<Target`引用较旧版本的 Xamarin.Forms，如下面所示的元素的元素。 你应该先手动删除此整个元素**csproj**文件并保存所做的更改。

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

删除这些旧引用后，该项目应成功生成。

## <a name="considerations"></a>注意事项

将现有 Xamarin.Forms 项目从经典 API 转换为新的统一 API，如果该应用程序依赖于一个或多个组件或 NuGet 包时，应考虑以下注意事项。

### <a name="components"></a>组件数

已包括你的应用程序中的任何组件还需要更新到统一 API 或您尝试编译时，您将看到冲突。 对于任何包括的组件，使用来自支持统一 API Xamarin 组件应用商店中的新版本替换当前版本和执行干净的生成。 尚未转换由作者，任何组件将显示在组件应用商店中的唯一警告一个 32 位。

### <a name="nuget-support"></a>NuGet 支持

我们提供到 NuGet 要使用的统一 API 支持的更改，而没有新版本的 NuGet，因此我们正在评估如何获取 NuGet 能够识别新的 Api。

到那时，就像组件，你将需要切换有支持统一 Api 的版本到项目中包含任何 NuGet 包之后执行一个干净的生成。

> [!IMPORTANT]
> 如果窗体中有错误_"错误 3 不能在同一 Xamarin.iOS 项目中包含 monotouch.dll 和 Xamarin.iOS.dll-Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 xxx，版本 = 0.0.000，区域性 = neutral，PublicKeyToken = null'"_后转换到统一 Api 应用程序，它通常是因为尚未更新到统一 API 的项目中采用的组件或 NuGet 包。 你将需要删除现有的组件/NuGet，更新到版本支持统一 Api 并执行一个干净的生成。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 64 位版本的 Xamarin.iOS 应用程序

有关 Xamarin.iOS 移动应用程序已转换为统一 API，开发人员仍需要启用应用程序的选项为 64 位计算机应用程序的构建。 请参阅**启用 64 位版本的 Xamarin.iOS 应用程序**的[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#enable-64)启用 64 位的详细说明的文档生成。

## <a name="summary"></a>总结

Xamarin.Forms 应用程序现在应该更新到版本 1.3.1 和 iOS 应用程序迁移到统一 API （这在 iOS 平台上支持 64 位体系结构）。

如上所述，如果 Xamarin.Forms 应用程序包括本机代码，例如自定义呈现器或依赖关系服务然后这些可能还需要更新以使用新类型[中统一 API 引入](~/cross-platform/macios/index.md)。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
