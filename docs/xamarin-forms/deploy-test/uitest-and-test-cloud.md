---
title: 使用 Xamarin.UITest 和 App Center 自动执行 Xamarin.Forms 测试
description: Xamarin UITest 组件可以与 Xamarin.Forms 一起使用，以编写可在数百个设备上的云中运行的 UI 测试。
ms.prod: xamarin
ms.assetid: b674db3d-c526-4e31-a9f4-b6d6528ce7a9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/31/2016
ms.openlocfilehash: f671f31dcc0aaf339a7c9f3d6465f4777a8f7f7c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="automate-xamarinforms-testing-with-xamarinuitest-and-app-center"></a>使用 Xamarin.UITest 和 App Center 自动执行 Xamarin.Forms 测试

_Xamarin UITest 组件可以与 Xamarin.Forms 一起使用，以编写可在数百个设备上的云中运行的 UI 测试。_

## <a name="overview"></a>概述

使用 **[App Center Test](/appcenter/test-cloud/)**，开发人员可为 iOS 和 Android 应用编写自动化用户界面测试。 经过一些细微调整，可使用 Xamarin.UITest 来测试 Xamarin.Forms 应用，包括共享相同的测试代码。 本文介绍结合使用 Xamarin.UITest 和 Xamarin.Forms 的特定使用技巧。

本指南假定用户已具备一定的 Xamarin.UITest 知识。 若要掌握 Xamarin.UITest 知识，建议遵循以下指南：

- [App Center Test 简介](/appcenter/test-cloud/)
- [UITest 简介](/appcenter/test-cloud/preparing-for-upload/uitest/)

将 UITest 项目添加到 Xamarin.Forms 解决方案后，即可采用用于 Xamarin.Android 或 Xamarin.iOS 应用程序的相同步骤为 Xamarin.Forms 应用程序编写和运行测试。

## <a name="requirements"></a>惠?

请参阅 [Xamarin.UITest](/appcenter/test-cloud/uitest/)，确认项目是否可供执行自动 UI 测试。

## <a name="adding-uitest-support-to-xamarinforms-apps"></a>将 UITest 支持添加到 Xamarin.Forms 应用

UITest 通过激活屏幕上的控件并对用户通常与应用程序交互的任何位置执行输入来自动化用户界面。 若要通过按某一按钮或在框中输入文本启用测试，测试代码需要某种可识别屏幕上控件的方法。

若要启用引用控件的 UITest 代码，每个控件均需一个唯一标识符。 在 Xamarin.Forms 中，建议使用 `AutomationId` 属性设置此标识符，如下所示：

```csharp
var b = new Button {
    Text = "Click me",
    AutomationId = "MyButton"
};
var l = new Label {
    Text = "Hello, Xamarin.Forms!",
    AutomationId = "MyLabel"
};
```

还可在 XAML 中设置 `AutomationId` 属性：

```xaml
<Button x:Name="b" AutomationId="MyButton" Text="Click me"/>
<Label x:Name="l" AutomationId="MyLabel" Text="Hello, Xamarin.Forms!" />
```

唯一 `AutomationId` 需添加到测试所需的所有控件（包括按钮、文本项和可能需要查询其值的标签）。

> [!NOTE]
> 注意，如果多次尝试设置 `Element` 的 `AutomationId` 属性，将引发 `InvalidOperationException`。

### <a name="ios-application-project"></a>iOS 应用程序项目

若要在 iOS 上运行测试，必须将 [Xamarin Test Cloud Agent NuGet 包](https://www.nuget.org/packages/Xamarin.TestCloud.Agent/)添加到项目。 完成添加后，请将以下代码复制到 `AppDelegate.FinishedLaunching` 方法：

```csharp
#if ENABLE_TEST_CLOUD
// requires Xamarin Test Cloud Agent
Xamarin.Calabash.Start();
#endif
```

Calabash 程序集使用非公开 Apple API，从而导致 App Store 拒绝应用。 但是，如果未从代码中显式引用 Calabash 程序集，则 Xamarin.iOS 链接器会从最终 IPA 中将其删除。

> [!NOTE]
>  发布版本不含 `ENABLE_TEST_CLOUD` 编译器变量，从而导致 Calabash 程序集从应用包中删除。 但是，调试版本中定义了编译器指令，可防止链接器删除程序集。

以下屏幕截图显示有关调试版本的 `ENABLE_TEST_CLOUD` 编译器变量设置：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](uitest-and-test-cloud-images/12-compiler-directive-vs.png "生成选项")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](uitest-and-test-cloud-images/11-compiler-directive-xs.png "")

-----

### <a name="android-application-project"></a>Android 应用程序项目

与 iOS 不同，Android 项目不需要任何特殊的启动代码。

## <a name="writing-uitests"></a>编写 UITest

有关编写 UITest 的信息，请参阅 [UITest 文档](/appcenter/test-cloud/preparing-for-upload/uitest/)。 以下概要步骤专门描述了如何生成 [Xamarin.Forms 演示 **UsingUITest**](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)。

### <a name="use-automationid-in-the-xamarinforms-ui"></a>使用 Xamarin.Forms UI 中的 AutomationId

编写任何 UITest 之前，必须确保 Xamarin.Forms 应用程序用户界面上的脚本可编辑。 确保用户界面中的所有控件都有 `AutomationId`，以便在测试代码中引用它们。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="adding-a-uitest-project-to-a-new-solution"></a>将 UITest 项目添加到新解决方案

使用 Visual Studio for Mac 创建新的 Xamarin.Forms 项目时，可以通过选择“Xamarin Test Cloud: 添加自动化 UI 测试项目”，将新的 UITest 项目添加到解决方案：

![](uitest-and-test-cloud-images/01-new-solution-xs.png "配置新项目")

新解决方案将自动配置为对 Xamarin.Forms 应用程序运行 Xamarin.UITest。

-----

#### <a name="referring-to-the-automationid-in-uitests"></a>在 UITest 中引用 AutomationId

编写 UITest 时，`AutomationId` 值会在每个平台上以不同方式公开：

- **iOS** 使用 `id` 字段。
- **Android** 使用 `label` 字段。

若要编写可在 iOS 和 Android 上找到 `AutomationId` 的跨平台 UITest，请使用 `Marked` 测试查询：

```csharp
app.Query(c=>c.Marked("MyButton"))
```

短格式 `app.Query("MyButton")` 同样适用。

### <a name="adding-a-uitest-project-to-an-existing-solution"></a>将 UITest 项目添加到现有解决方案

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 提供了一个模板，帮助将 Xamarin.UITest 项目添加到现有 Xamarin.Forms 解决方案：

1. 右键单击解决方案，然后选择“文件”>“新建项目”。
1. 从 **Visual C#** 模板中，选择“测试”类别。 选择“UI 测试应用”>“跨平台”模板：

    ![](uitest-and-test-cloud-images/08-new-uitest-project-vs.png "添加新项目")

    这会向解决方案添加一个包含 **NUnit**、**Xamarin.UITest** 和 **NUnitTestAdapter** NuGet 包的新项目：

    ![](uitest-and-test-cloud-images/09-new-uitest-project-xs.png "NuGet 包管理器")

    **NUnitTestAdapter** 是一个第三方测试运行程序，允许 Visual Studio 从其自身运行 NUnit 测试。

    新项目中还包含两个类。 **AppInitializer** 类包含有助于初始化和设置测试的代码。 另一个类 **Tests** 包含有助于启动 UITest 的 boilerplate 代码。

1. 将项目引用从 UITest 项目添加到 Xamarin.Android 项目：

    ![](uitest-and-test-cloud-images/10-test-apps-vs.png "项目引用管理器")

    这样做可以使 **NUnitTestAdapter** 对 Visual Studio 中的 Android 应用运行 UITest。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

可手动将新 Xamarin.UITest 项目添加到现有解决方案：

1. 首先添加新项目，方法是选择解决方案，然后单击“文件”>“添加新项目”。 在“新建项目”对话框中，选择“跨平台”>“测试”>“Xamarin Test Cloud”>“UI 测试应用”：

    ![](uitest-and-test-cloud-images/02-new-uitest-project-xs.png "选择模板")

    这将在解决方案中添加一个已包含 **NUnit** 和 **Xamarin.UITest** NuGet 包的新项目：

    ![](uitest-and-test-cloud-images/03-new-uitest-project-xs.png "Xamarin UITest NuGet 程序包")

    新项目中还包含两个类。 **AppInitializer** 类包含有助于初始化和设置测试的代码。 另一个类 **Tests** 包含有助于启动 UITest 的 boilerplate 代码。

1. 选择“视图”>“面板”>“单元测试”显示单元测试面板。 展开“UsingUITest”>“UsingUITest.UITests”>“测试应用”：

    ![](uitest-and-test-cloud-images/04-unit-test-pad-xs.png "单元测试板")

1. 右键单击“测试应用”，再单击“添加应用项目”，然后在显示的对话框中选择 iOS 和 Android 项目：

    ![](uitest-and-test-cloud-images/05-add-test-apps-xs.png "“测试应用”对话框")

    “单元测试”面板此时应有对 iOS 和 Android 项目的引用。 这可使 Visual Studio for Mac 测试运行程序在本地对这两个 Xamarin.Forms 项目执行 UITest。

#### <a name="adding-uitest-to-the-ios-app"></a>将 UITest 添加到 iOS 应用

需要先对 iOS 应用程序执行某些附加更改，Xamarin.UITest 才能正常工作：

1. 添加 **Xamarin Test Cloud Agent** NuGet 包。 右键单击“包”，选择“添加包”，搜索 **Xamarin Test Cloud Agent** 的 NuGet，然后将其添加到 Xamarin.iOS 项目：

    ![](uitest-and-test-cloud-images/07-add-test-cloud-agent-xs.png "添加 NuGet 程序包")

1. 编辑 **AppDelegate** 类的 `FinishedLaunching` 方法，以在 iOS 应用程序启动时初始化 Xamarin Test Cloud Agent，并设置视图的 `AutomationId` 属性。 `FinishedLaunching` 方法应与以下代码示例类似：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
        #if ENABLE_TEST_CLOUD
        Xamarin.Calabash.Start();
        #endif

        global::Xamarin.Forms.Forms.Init();

        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
}
```

-----

将 Xamarin.UITest 添加到 Xamarin.Forms 解决方案后，即可创建 UITest、在本地运行 UITest，并将其提交到 Xamarin Test Cloud。

## <a name="summary"></a>总结

使用一种简单机制即可轻松通过 **Xamarin.UITest** 测试 Xamarin.Forms 应用程序，以将 `AutomationId` 公开为测试自动化的唯一视图标识符。 将 UITest 项目添加到 Xamarin.Forms 解决方案后，即可采用用于 Xamarin.Android 或 Xamarin.iOS 应用程序的相同步骤为 Xamarin.Forms 应用程序编写和运行测试。

有关如何将测试提交到 App Center Test 的信息，请参阅[提交 UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)。 有关 UITest 的详细信息，请参阅 [App Center Test 文档](/appcenter/test-cloud/)。


## <a name="related-links"></a>相关链接

- [UITestSample](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [应用中心测试](/appcenter/test-cloud/)
- [Xamarin.UITest](/appcenter/test-cloud/uitest/)
- [NUnit](http://www.nunit.org)
