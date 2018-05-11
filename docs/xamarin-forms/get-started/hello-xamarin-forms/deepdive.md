---
title: Xamarin.Forms 深度解析
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d97aa580-1eb9-48b3-b15b-0d7421ea7ae
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 9bb368ca72909489ca267794035a4c4d0883f109
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="xamarinforms-deep-dive"></a>Xamarin.Forms 深度解析

在 [Xamarin.Forms 快速入门](~/xamarin-forms/get-started/hello-xamarin-forms/quickstart.md)中，生成了 Phoneword 应用程序。 本文对已生成的内容进行回顾，以深入了解有关 Xamarin.Forms 应用程序工作原理的基础知识。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="introduction-to-visual-studio"></a>Visual Studio 简介

Visual Studio 是 Microsoft 提供的强大 IDE。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 本文重点介绍如何通过 Xamarin 插件使用 Visual Studio 的一些基本功能。

Visual Studio 将代码组织为解决方案和项目。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 Phoneword 应用程序包含 1 个内附 4 个项目的解决方案，如以下屏幕截图所示。

![](deepdive-images/vs/solution.png "Visual Studio 解决方案资源管理器")

这些项目如下：

- Phoneword - 此项目是 .NET Standard 库项目，其中包含所有共享代码和共享 UI。
- Phoneword.Android - 此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Phoneword.iOS - 此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。
- Phoneword.UWP - 此项目包含通用 Windows 平台特定代码，是 UWP 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

以下屏幕截图显示 Visual Studio 中 Phoneword .NET Standard 库项目的内容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard 项目内容")

项目具有包含 NuGet 和 SDK 节点的依赖项节点。 NuGet 节点包含已添加至项目的 Xamarin.Forms NuGet 包，SDK 节点包含可引用一组完整的 NuGet 包（此包用于定义 .NET Standard）的 `NETStandard.Library` 元包。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 简介

Visual Studio for Mac 是免费的开源 IDE，类似于 Visual Studio。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 有关 Visual Studio for Mac 的详细信息，请参阅 [Visual Studio for Mac 简介](/visualstudio/mac/)。

Visual Studio for Mac 遵循将代码组织为解决方案和项目的 Visual Studio 做法。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 Phoneword 应用程序包含 1 个内附 3 个项目的解决方案，如以下屏幕截图所示。

![](deepdive-images/xs/solution.png "Visual Studio for Mac 解决方案窗格")

这些项目如下：

- Phoneword - 此项目是 .NET Standard 库项目，其中包含所有共享代码和共享 UI。
- Phoneword.Droid - 此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Phoneword.iOS - 此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

以下屏幕截图显示 Visual Studio for Mac 中 Phoneword PCL 项目的内容：

![](deepdive-images/xs/pcl-project.png "Phoneword PCL 项目内容")

项目包含 3 个文件夹：

- **引用** - 包含生成和运行应用程序所需的程序集。 展开 .NET 可移植子集文件夹会显示对 [System](http://msdn.microsoft.com/library/system%28v=vs.110%29.aspx)、System.Core 和 [System.Xml](http://msdn.microsoft.com/library/system.xml%28v=vs.110%29.aspx) 等 .NET 程序集的引用。 展开“从包”文件夹会显示对 Xamarin.Forms 程序集的引用。
- **包** - 包目录存放 [NuGet](https://www.nuget.org) 包，该包可简化在应用程序中使用第三方库的流程。 右键单击文件夹并在弹出菜单中选择更新选项，可将这些包更新为最新版本。
- **属性** - 包含 .NET 程序集元数据文件 **AssemblyInfo.cs**。 最好在此文件中填写一些应用程序相关的基本信息。 有关此文件的详细信息，请参阅 MSDN 上的 [AssemblyInfo 类](http://msdn.microsoft.com/library/microsoft.visualbasic.applicationservices.assemblyinfo(v=vs.110).aspx)。

-----

项目还包括多个文件：

- **App.xaml** - `App` 类的 XAML 标记，该类定义应用程序的资源字典。
- **App.xaml.cs** - `App` 类的代码隐藏，该类负责实例化应用程序在每个平台上将显示的首页，并处理应用程序生命周期事件。
- IDialer.cs – `IDialer` 接口，该接口指定 `Dial` 方法必须由任何实现类提供。
- **MainPage.xaml** - `MainPage` 类的 XAML 标记，该类定义应用程序启动时所显示页的 UI。
- **MainPage.xaml.cs** - `MainPage` 类的代码隐藏，该类包含用户与页面交互时执行的业务逻辑。
- packages.config -（仅适用于 Visual Studio for Mac）一个 XML 文件，其中包含项目所使用的 NuGet 包的相关信息，以便跟踪所需包及其各自的版本。 Visual Studio for Mac 和 Visual Studio 均可配置为：与其他用户共享源代码时，自动还原任何缺少的 NuGet 包。 此文件的内容由 NuGet 包管理器控制，且不可手动编辑。
- **PhoneTranslator.cs** - 负责将电话文字转换为电话号码的业务逻辑，此逻辑从 **MainPage.xaml.cs** 调用。

有关 Xamarin.iOS 应用程序剖析的详细信息，请参阅 [Xamarin.iOS 应用程序剖析](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy)。 有关 Xamarin.Android 应用程序剖析的详细信息，请参阅 [Xamarin Android 应用程序剖析](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>体系结构和应用程序基础知识

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin.Forms 应用程序采用与传统跨平台应用程序相同的构建方式。 共享代码通常位于 .NET Standard 库中，平台特定应用程序将使用此共享代码。 下图概要演示了 Phoneword 应用程序的这种关系：

![](deepdive-images/vs/architecture.png "Phoneword 体系结构")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Xamarin.Forms 应用程序采用与传统跨平台应用程序相同的构建方式。 共享代码通常位于 .NET Standard 库中，平台特定应用程序将使用此共享代码。 下图概要演示了 Phoneword 应用程序的这种关系：

![](deepdive-images/xs/architecture.png "Phoneword 体系结构")

有关 PCL的详细信息，请参阅[可移植类库简介](~/cross-platform/app-fundamentals/pcl.md)。

-----

若要最大限度重用启动代码，Xamarin.Forms 应用程序需有一个名为 `App` 的单个类，该类负责实例化应用程序在每个平台上将显示的首页，如以下代码示例所示：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Phoneword
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new MainPage();
        }
        ...
    }
}
```

此代码将 `App` 类的 `MainPage` 属性设置为 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 类的一个新实例。 此外，[`XamlCompilation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/) 属性可打开 XAML 编译器，以使 XAML 直接编译为中间语言。 有关详细信息，请参阅 [XAML 编译](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>在每个平台上启动应用程序

### <a name="ios"></a>iOS

若要在 iOS 中启动 Xamarin.Forms 初始页面，Phoneword.iOS 项目应包括继承自 `FormsApplicationDelegate` 类的 `AppDelegate` 类，如以下代码示例所示：

```csharp
namespace Phoneword.iOS
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init ();
            LoadApplication (new App ());
            return base.FinishedLaunching (app, options);
        }
    }
}
```

通过调用 `Init` 方法，`FinishedLaunching` 替代初始化 Xamarin.Forms 框架。 这会导致在调用将根视图控制器设置为 `LoadApplication` 方法之前，将特定于 iOS 的 Xamarin.Forms 实现加载到应用程序。

### <a name="android"></a>Android

要在 Android 中启动 Xamarin.Forms 初始页面，Phoneword.Droid 项目应包括使用 `MainLauncher` 属性创建 `Activity` 的代码，以及继承自 `FormsApplicationActivity` 类的活动，如以下代码示例所示：

```csharp
namespace Phoneword.Droid
{
    [Activity(Label = "Phoneword",
              Icon = "@drawable/icon",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        internal static MainActivity Instance { get; private set; }

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            Instance = this;
            global::Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication(new App());
        }
    }
}
```

通过调用 `Init` 方法，`OnCreate` 替代初始化 Xamarin.Forms 框架。 这会导致在加载 Xamarin.Forms 应用程序之前，将特定于 Android 的 Xamarin.Forms 实现加载到应用程序。 此外，`MainActivity` 类将在 `Instance` 属性中存储对其自身的引用。 引用自 `PhoneDialer` 类的 `Instance` 属性被称为本地上下文。

## <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 应用程序中，可从 `App` 类调用初始化 Xamarin.Forms 框架的 `Init` 方法：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

这会将特定于 UWP 的 Xamarin.Forms 实现加载到应用程序。 可通过 `MainPage` 类启动 Xamarin.Forms 初始页面，如以下代码示例所示：

```csharp
namespace Phoneword.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Phoneword.App());
        }
    }
}
```

可通过 `LoadApplication` 方法加载 Xamarin.Forms 应用程序。

> [!NOTE]
> 通用 Windows 平台 (UWP) 应用可以使用 Xamarin.Forms 生成，但只能在 Windows 上使用 Visual Studio。

## <a name="user-interface"></a>用户界面

可使用 4 个主要控件组创建 Xamarin.Forms 应用程序的用户界面。

1. **页面** - Xamarin.Forms 页呈现跨平台移动应用程序屏幕。 Phoneword 应用程序使用 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 类显示单个屏幕。 有关页面的详细信息，请参阅 [Xamarin.Forms 页面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **布局** - Xamarin.Forms 布局是用于将视图组合到逻辑结构的容器。 Phoneword 应用程序使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 类以水平堆叠方式排列控件。 有关布局的详细信息，请参阅 [Xamarin.Forms 布局](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **视图** - Xamarin.Forms 视图是显示在用户界面上的控件，如标签、按钮和文本输入框。 Phoneword 应用程序使用 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)、[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 和 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控件。 有关视图的详细信息，请参阅 [Xamarin.Forms 视图](~/xamarin-forms/user-interface/controls/views.md)。
1. **单元格** - Xamarin.Forms 单元格是专门用于列表中的项的元素，描述列表中每个项的绘制方式。 Phoneword 应用程序不会使用任何单元格。 有关单元格的详细信息，请参阅 [Xamarin.Forms 单元格](~/xamarin-forms/user-interface/controls/cells.md)。

在运行时，每个控件都会映射到其本身的本机等效项（即呈现的内容）。

在任一平台运行 Phoneword 应用程序时，此应用会显示对应于 Xamarin.Forms 中 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 的单一屏幕。 `Page` 在 Android 中表示为一个 *ViewGroup*，在 iOS 中表示为视图控制器，在 Windows 通用平台中则表示为一个页面。 Phoneword 应用程序也会实例化表示 `MainPage` 类的 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 对象，该类的 XAML 标记如以下代码示例所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                         xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                         x:Class="Phoneword.MainPage">
        ...
        <StackLayout>
            <Label Text="Enter a Phoneword:" />
            <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
            <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
            <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
</ContentPage>
```

`MainPage` 类使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控件在屏幕上自动排列控件，而不考虑屏幕大小。 根据添加顺序，以垂直方式逐个放置每个子元素。 `StackLayout` 控件包含 1 个用于在页面上显示文本的 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控件、1 个用于接收文本用户输入的 [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 控件和 2 个用于在响应触摸事件时执行代码的 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控件。

有关 Xamarin.Forms 中 XAML 的详细信息，请参阅 [Xamarin.Forms XAML 基础知识](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="responding-to-user-interaction"></a>响应用户交互

XAML 中定义的对象可触发在隐藏文件中处理的事件。 以下代码示例演示了 `MainPage` 类的代码隐藏中的 `OnTranslate` 方法，该类在响应 [`Clicked`](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) 事件触发“转换”按钮时执行。

```csharp
void OnTranslate(object sender, EventArgs e)
{
    translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
    if (!string.IsNullOrWhiteSpace (translatedNumber)) {
        callButton.IsEnabled = true;
        callButton.Text = "Call " + translatedNumber;
    } else {
        callButton.IsEnabled = false;
        callButton.Text = "Call";
    }
}
```

`OnTranslate` 方法将 phoneword 转换为其相应的电话号码，并在响应时设置调用按钮上的属性。 XAML 类的代码隐藏文件可使用为其分配的、具有 `x:Name` 属性的名称访问 XAML 中定义的对象。 分配给此属性的值与 C# 变量的规则相同，因为该值必须以字母或下划线开头，且不包含嵌入的空格。

对 `OnTranslate` 方法的切换按钮布线会在 `MainPage` 类的 XAML 标记中出现：

```xaml
<Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

用于 Xamarin.Forms 的 Phoneword 应用程序引入了多个本文未提及的概念。 这些概念包括：

- 启用和禁用按钮。 通过更改 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 的 [`IsEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) 属性，可将其打开或关闭。 例如，以下代码示例禁用 `callButton`：

    ```csharp
    callButton.IsEnabled = false;
    ```

- 显示警报对话框。 用户按呼叫**按钮**时，Phoneword 应用程序会显示“警报”对话框，其中包含发出或取消呼叫的选项。 [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) 方法用于创建该对话框，如以下代码示例所示：

    ```csharp
    await this.DisplayAlert (
            "Dial a Number",
            "Would you like to call " + translatedNumber + "?",
            "Yes",
            "No");
    ```

- 通过 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 类访问本机功能。 Phoneword 应用程序使用 `DependencyService` 类将 `IDialer` 接口解析到特定于平台的电话拨号实现中，如以下 Phoneword 项目中的代码示例所示：

    ```csharp
    async void OnCall (object sender, EventArgs e)
    {
        ...
        var dialer = DependencyService.Get<IDialer> ();
        ...
    }
    ```

  有关 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 类的详细信息，请参阅[通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

- 通过 URL 发出电话呼叫。 Phoneword 应用程序使用 `OpenURL` 启动系统电话应用。 URL 包含 `tel:` 前缀，后跟要呼叫的电话号码，如以下 iOS 项目中的代码示例所示：

    ```csharp
    return UIApplication.SharedApplication.OpenUrl (new NSUrl ("tel:" + number));
    ```

- 调整平台布局。 使用 [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) 类，开发人员能够根据每个平台自定义应用程序布局和功能，如以下代码示例所示（此示例使用不同平台上的另一个 [`Padding`](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) 值正确显示每一页）：

    ```xaml
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms" ... >
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        ...
    </ContentPage>
    ```

  有关平台调整的详细信息，请参阅[设备类](~/xamarin-forms/platform/device.md)。

## <a name="testing-and-deployment"></a>测试和部署

Visual Studio for Mac 和 Visual Studio 均提供许多用于测试和部署应用程序的选项。 调试应用程序是应用程序开发生命周期的普遍一环，有助于诊断代码问题。 有关详细信息，请参阅[设置断点](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/set_a_breakpoint/)、[逐步执行代码](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/step_through_code/)和[日志窗口的输出信息](https://developer.xamarin.com/recipes/cross-platform/ide/debugging/output_information_to_log_window/)。

模拟器是开始部署和测试应用程序的有利位置，其提供用于测试应用程序的有用功能。 但是，用户不会在模拟器中使用最终应用程序，因此应尽早并经常在实际设备上测试应用程序。 有关 iOS 设备预配的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)。 有关 Android 设备预配的详细信息，请参阅[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="summary"></a>总结

本文介绍了使用 Xamarin.Forms 开发应用程序的基础知识。 涵盖的主题包括：Xamarin.Forms 应用程序剖析、体系结构和应用程序基础知识以及用户界面。

在本指南的下一部分将介绍如何扩展应用程序以包含多个屏幕，从而探索更高级的 Xamarin.Forms 体系结构和概念。
