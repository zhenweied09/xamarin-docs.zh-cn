---
title: "iOS 扩展"
description: "在 iOS 8 中引入了扩展的小组件提供的 iOS 在标准上下文中，如在通知中心中，当用户请求自定义键盘，或者当它们照片时编辑。 所有扩展与容器应用程序一起安装，并从特定的扩展点，在主机应用程序中激活。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f6e80b21c76089c0f3f7ac655584b7e18400307e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="ios-extensions"></a>iOS 扩展

_在 iOS 8 中引入了扩展的小组件提供的 iOS 在标准上下文中，如在通知中心中，当用户请求自定义键盘，或者当它们照片时编辑。所有扩展与容器应用程序一起安装，并从特定的扩展点，在主机应用程序中激活。_

扩展，因为在 iOS 8 中引入了一些专用`UIViewControllers`，提供的标准的上下文内的 iOS 如在**通知中心**，如用户请求执行的自定义键盘类型专用化输入或其他上下文，如编辑的照片，扩展可以提供特殊效果筛选器。

所有扩展安装结合容器应用程序 （使用 64 位统一 Api 编写两个元素） 以及从特定的扩展点，在主机应用程序中激活。 并且，因为它们将用作为对现有系统函数的补充，它们必须是精益，和可靠的高性能。 

本文介绍了以下主题：

- [扩展点](#Extension-Points)-列出可用扩展点的类型和可以为每个点创建的扩展的类型。
- [限制](#Limitations)的扩展有大量的限制，其中一些是通用于所有类型的而其他类型的扩展可能有特定的限制，其使用情况。
- [分发、 安装和运行扩展](#Distributing-Installing-and-Running-Extensions)-扩展从分布在容器的应用，而后者，已提交并通过应用商店分发内。 此时，安装与应用一起分发的扩展插件，但用户必须显式启用每个扩展。 不同类型的扩展启用不同的方式。
- [扩展生命周期](#Extension-Lifecycle)-的扩展的`UIViewController`将实例化并开始正常视图控制器生命周期。 但是，与不同的是正常的应用程序，扩展都是加载、 执行，，然后终止重复。
- [创建扩展](#Creating-an-Extension)-在开发时扩展，你的解决方案将包含至少两个项目： 在容器应用程序和一个项目的每个扩展容器提供。
- [演练](#Walkthrough)-介绍创建简单**今天**小组件提供了其用户界面使用情节提要的扩展或在代码中，安装扩展，并在 iOS 模拟器中对其进行测试。
- [与主机应用程序通信](#Communicating-with-the-Host-App)-简要讨论与扩展主机应用程序通信。
- [与父应用程序通信](#Communicating-with-the-Parent-App)-简要讨论与扩展中的父应用程序通信。
- [预防措施和注意事项](#Precautions-and-Considerations)-某些知道的预防措施和应考虑设计和实现扩展时考虑的事项的列表。
 

<a name="Extension-Points" />

## <a name="extension-points"></a>扩展点

有多种类型的 8 （和更高版本） 可以在 iOS 中创建的扩展：

<table>
<colgroup>
<col />
<col />
<col />
</colgroup>

<thead>
<tr>
    <th >类型</th>
    <th >描述</th>
    <th >扩展点</th>
    <th >主机应用程序</th>
</tr>
</thead>

<tbody>
<tr>
    <td >操作</td>
    <td >专用的编辑器或特定媒体类型的查看器</td>
    <td ><code>com.apple.ui-services</code></td>
    <td >任意</td>
</tr>
<tr>
    <td >文档提供程序</td>
    <td >允许应用程序以使用远程文档存储区</td>
    <td ><code>com.apple.fileprovider-ui</code></td>
    <td >使用应用<a href="https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/">UIDocumentPickerViewController</a></td>
</tr>
<tr>
    <td >键盘</td>
    <td >备用键盘</td>
    <td ><code>com.apple.keyboard-service</code></td>
    <td >任意</td>
</tr>
<tr>
    <td >照片编辑</td>
    <td >照片操作和编辑</td>
    <td ><code>com.apple.photo-editing</code></td>
    <td >Photos.app 编辑器</td>
</tr>
<tr>
    <td >共享</td>
    <td >共享数据，与社交网络，消息传送服务，等等。</td>
    <td ><code>com.apple.share-services</code></td>
    <td >任意</td>
</tr>
<tr>
    <td >今天</td>
    <td >显示在今天屏幕或通知中心上的"小组件"</td>
    <td ><code>com.apple.widget-extensions</code></td>
    <td >今天和通知中心</td>
</tr>
</tbody>
</table>

[其他扩展点](~/ios/platform/introduction-to-ios10/index.md#app-extensions)iOS 10 中已添加。

<a name="Limitations" />

## <a name="limitations"></a>限制

扩展有大量的限制，其中有一些通用于所有类型 （对于实例，没有任何类型的扩展可访问的摄像头或麦克风） 虽然其他类型的扩展可能具有其使用情况 （例如，自定义键盘上的特定限制不能使用为类似于密码的安全数据输入字段）。 

通用的限制是：

- [运行状况工具包](~/ios/platform/healthkit.md)和[事件工具包 UI](~/ios/platform/eventkit.md)框架不可用
- 扩展不能使用[扩展后台模式](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- （但它们可以访问现有的媒体文件），扩展将不能访问设备的照相机或麦克风
- 扩展无法接收无线删除数据 （尽管它们可以通过传输通过无线删除数据）
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/)和[UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/)不可用; 扩展必须使用[UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- 多个成员[UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/)都不可用： [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/)， `UIApplication.OpenURL`，`UIApplication.BeginIgnoringInteractionEvents`和 `UIApplication.EndIgnoringInteractionEvents`
- iOS 增强当今扩展了 16 MB 内存使用情况限制。
- 默认情况下键盘扩展没有网络访问权限。 这会影响调试在设备上 （限制不在模拟器中强制执行），因为 Xamarin.iOS 要求网络访问以进行调试的正常工作。 它是以请求网络访问权限可能通过设置`Requests Open Access`中项目的 Info.plist 到值`Yes`。 请参阅 Apple 的[自定义键盘指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)键盘扩展限制有关的详细信息。

有关各个限制，请参阅 Apple 的[应用程序扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

<a name="Distributing-Installing-and-Running-Extensions" />

## <a name="distributing-installing-and-running-extensions"></a>分发、 安装和运行扩展

在容器的应用，而后者，已提交并通过应用商店分发内，从分发扩展。 此时，安装与应用一起分发的扩展插件，但用户必须显式启用每个扩展。 在不同的方式; 中启用了不同类型的扩展某些需要用户导航到**设置**应用并使其从那里。 虽然其他人处于启用在使用，例如发送照片时启用共享的扩展点。 

应用程序在其中扩展使用 （其中，则在用户遇到扩展点） 被称为**主机应用程序**，因为它是承载扩展，它执行时的应用。 安装扩展的应用程序已**容器应用**，因为它是包含扩展，安装时的应用。  

通常情况下，容器应用程序介绍扩展，并指导用户完成启用它的过程。

<a name="Extension-Lifecycle" />

## <a name="extension-lifecycle"></a>扩展生命周期

扩展可以很简单，只需为单个[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/)或更复杂的扩展提供的 UI 的多个屏幕。 当用户遇到_扩展点_(例如，当共享映像)，它们将有机会选择从为该扩展点注册的扩展。 

如果他们选择你的应用程序之一的扩展，其`UIViewController`将实例化并开始正常视图控制器生命周期。 但是，与常规的应用，这样做是挂起，但通常不终止用户完成与其进行交互时，不同扩展都是加载、 执行，，然后终止重复。

扩展可以与通过其承载应用程序进行通信[NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)对象。 某些扩展有接收使用的结果的异步回调的操作。 将在后台线程上执行这些回调和扩展必须考虑这一点;例如，通过使用[NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/)如果他们想要更新的用户界面。 请参阅[与主机应用程序通信](#Communicating-with-the-Host-App)下面部分以了解更多详细信息。

默认情况下，扩展和其容器应用无法通信，尽管一起安装。 在某些情况下，容器应用程序是实质上是一个空"装运"的容器安装扩展后，其目的提供服务。 但是，如果规定的情况下，容器应用程序和扩展插件可能会共享资源从常见的区域。 此外，**今天扩展**可能会请求其容器应用以打开 URL。 演示了此行为[发展倒计时小组件](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo)。

<a name="Creating-an-Extension" />

## <a name="creating-an-extension"></a>创建扩展

扩展 （和其容器应用程序） 必须是 64 位二进制文件和使用 Xamarin.iOS 构建[统一 Api](http://developer.xamarin.com/guides/cross-platform/macios/unified)。 在开发时扩展，你的解决方案将包含至少两个项目： 在容器应用程序和一个项目的每个扩展容器提供。 

<a name="Container-App-Project-Requirements" />

### <a name="container-app-project-requirements"></a>容器应用程序项目要求

用于安装扩展的容器应用程序具有以下要求：

- 它必须维护向扩展项目的引用。   
- 它必须是一个完整的应用 （必须能够启动并成功运行），即使它不执行任何操作超过提供安装扩展的方法。 
- 它必须具有一个捆绑标识符，它是扩展的捆绑标识符的基础项目 （请参阅下面有关详细信息的部分）。

<a name="Extension-Project-Requirements" />

### <a name="extension-project-requirements"></a>扩展项目要求

此外，扩展的项目具有以下要求：

- 它必须具有开头其容器应用的捆绑标识符的捆绑标识符。 例如，如果容器应用程序具有的捆绑标识符`com.myCompany.ContainerApp`，扩展的标识符可能`com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- 它必须定义键`NSExtensionPointIdentifier`，使用适当的值 (如`com.apple.widget-extension`为**今天**通知中心小组件)，请在其`Info.plist`文件。
- 它还必须定义*任一*`NSExtensionMainStoryboard`密钥或`NSPrincipalClass`中的键其`Info.plist`文件具有适当的值：
    - 使用`NSExtensionMainStoryboard`键以指定的名称为扩展提供的主 UI 情节提要 (减`.storyboard`)。 例如，`Main`为`Main.storyboard`文件。
    - 使用`NSPrincipalClass`键以指定启动扩展时将初始化的类。 值必须与匹配**注册**值你`UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

特定类型的扩展可能具有附加要求。 例如，**今天**或**通知中心**扩展的主体类必须实现[INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/)。

> [!IMPORTANT]
> **注意：**如果启动你的项目使用一个 Visual Studio 提供的适用于 Mac 的扩展模板时，将提供并为你自动通过模板来满足大多数 （如果不是所有） 这些要求。

<a name="Walkthrough" />

## <a name="walkthrough"></a>演练 

在下面的演练，你将创建一个示例**今天**计算的日期和年份中剩余的天数的小组件：

[ ![](extensions-images/carpediemscreenshot-sm.png "计算的日期和年份中剩余天数示例今天小组件")](extensions-images/carpediemscreenshot.png)

<a name="Creating-the-Solution" />

### <a name="creating-the-solution"></a>创建解决方案

若要创建所需的解决方案，请执行以下操作：

1. 首先，创建新的 iOS，**单视图应用**项目，然后单击**下一步**按钮： 

    [ ![](extensions-images/today01.png "首先，创建新的 iOS、 单个视图应用程序项目并单击下一步按钮")](extensions-images/today01.png)
2. 调用项目`TodayContainer`单击**下一步**按钮： 

    [ ![](extensions-images/today02.png "调用项目 TodayContainer 并单击下一步按钮")](extensions-images/today02.png)
3. 验证**项目名称**和**SolutionName**单击**创建**按钮以创建解决方案： 

    [ ![](extensions-images/today03.png "验证项目名称和解决方案名称，单击创建按钮以创建解决方案")](extensions-images/today03.png)
4. 接下来，在**解决方案资源管理器**，右击该解决方案，并添加一个新**iOS 扩展**项目从**今天扩展**模板： 

    [ ![](extensions-images/today04.png "接下来，在解决方案资源管理器，右击该解决方案并从今天扩展模板添加新的 iOS 扩展项目")](extensions-images/today04.png)
5. 调用项目`DaysRemaining`单击**下一步**按钮： 

    [ ![](extensions-images/today05.png "调用项目 DaysRemaining 并单击下一步按钮")](extensions-images/today05.png)
6. 查看项目，然后单击**创建**按钮以创建它： 

    [ ![](extensions-images/today06.png "查看项目，然后单击创建按钮来创建它")](extensions-images/today06.png)

生成的解决方案现在应具有两个项目，如下所示：

[ ![](extensions-images/today07.png "生成的解决方案现在应有两个项目，如下所示")](extensions-images/today07.png)

<a name="Creating-the-Extension-User-Interface" />

### <a name="creating-the-extension-user-interface"></a>创建扩展的用户界面

接下来，你将需要设计的界面你**今天**小组件。 这既可以使用情节提要或通过在代码中创建 UI。 在详细信息，将下面介绍这两种方法。

<a name="Using-Storyboards" />

#### <a name="using-storyboards"></a>使用情节提要

若要生成使用情节提要 UI，请执行以下操作：

1. 在**解决方案资源管理器**，双击扩展项目的`Main.storyboard`文件以打开进行编辑： 

    [ ![](extensions-images/today08.png "双击要打开进行编辑的扩展项目 Main.storyboard 文件")](extensions-images/today08.png)
2. 选择模板自动添加到 UI 的标签，并为其提供**名称**`TodayMessage`中**小组件**选项卡**属性资源管理器**: 

    [ ![](extensions-images/today09.png "选择模板自动添加到 UI 的标签，并为其提供属性资源管理器的小组件选项卡中名称 TodayMessage")](extensions-images/today09.png)
3. 将所做的更改保存到情节提要。

<a name="Using-Code" />

#### <a name="using-code"></a>使用代码

若要生成代码中的用户界面，请执行以下操作： 

1. 在**解决方案资源管理器**，选择**DaysRemaining**项目中，添加一个新类，称之为`CodeBasedViewController`: 

    [ ![](extensions-images/code01.png "Aelect DaysRemaining 项目中，添加一个新类并调用它 CodeBasedViewController")](extensions-images/code01.png)
2. 同样，**解决方案资源管理器**，双击扩展的`Info.plist`文件以打开进行编辑： 

    [ ![](extensions-images/code02.png "双击扩展的 Info.plist 文件，以便打开进行编辑")](extensions-images/code02.png)
3. 选择**源视图**（从屏幕底部） 并打开`NSExtension`节点： 

    [ ![](extensions-images/code03.png "选择从屏幕底部的源视图，然后打开 NSExtension 节点")](extensions-images/code03.png)
4. 删除`NSExtensionMainStoryboard`密钥，然后添加`NSPrincipalClass`值`CodeBasedViewController`: 

    [ ![](extensions-images/code04.png "删除 NSExtensionMainStoryboard 密钥并添加值 CodeBasedViewController NSPrincipalClass")](extensions-images/code04.png)
5. 保存更改。

接下来，编辑`CodeBasedViewController.cs`文件并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewContoller")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

请注意，`[Register("CodeBasedViewContoller")]`为指定的值匹配`NSPrincipalClass`上面。

<a name="Coding-the-Extension" />

### <a name="coding-the-extension"></a>编码扩展

使用创建用户界面，打开`TodayViewController.cs`或`CodeBasedViewController.cs`文件 （基于用于创建用户界面上面的方法）、 更改**ViewDidLoad**方法并使其如下所示：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

如果使用的代码基于用户接口方法，将`// Insert code to power extension here...`与从上面的新代码的注释。 之后调用基实现 （和插入的代码基于版本的标签），这段代码执行简单计算，以获取按年和剩余多少天的星期数。 然后它标签中显示该消息 (`TodayMessage`) 在 UI 设计中创建。

请注意此过程为编写一个应用程序的正常过程的方式类似。 扩展的`UIViewController`具有在应用程序，视图控制器相同的生命周期，只不过扩展不具有后台模式，并且不会挂起完成用户使用它们。 相反，扩展不重复初始化，并根据需要取消分配。

<a name="Creating-the-Container-App-User-Interface" />

### <a name="creating-the-container-app-user-interface"></a>创建容器应用程序用户界面

本演练中，容器应用程序只需用作一种方法用来提供并安装扩展，并不提供任何功能。 编辑 TodayContainer`Main.storyboard`文件并添加一些文本定义的扩展函数和如何安装它：

[ ![](extensions-images/today10.png "编辑 TodayContainers Main.storyboard 文件并添加一些定义扩展函数和如何安装它的文本")](extensions-images/today10.png)

将所做的更改保存到情节提要。

<a name="Testing-the-Extension" />

### <a name="testing-the-extension"></a>测试扩展

若要在 iOS 模拟器中测试你的扩展，运行**TodayContainer**应用。 容器的主视图将显示：

[ ![](extensions-images/run01.png "将显示的容器的主视图")](extensions-images/run01.png)

接下来，命中**主页**在模拟器中，向下轻扫以打开屏幕的顶部的按钮**通知中心**，选择**今天**选项卡，单击**编辑**按钮：

[ ![](extensions-images/run02.png "命中在模拟器中，从要打开通知中心，选择今天选项卡，单击编辑按钮的屏幕的顶部向下轻扫主页按钮")](extensions-images/run02.png)

添加**DaysRemaining**扩展**今天**查看，然后单击**完成**按钮：

[ ![](extensions-images/run03.png "将 DaysRemaining 扩展添加到今日视图，单击完成按钮")](extensions-images/run03.png)

新的小组件将添加到**今天**将显示视图和结果：

[ ![](extensions-images/run04.png "新的小组件将添加到今日视图，并将显示结果")](extensions-images/run04.png)

<a name="Communicating-with-the-Host-App" />

## <a name="communicating-with-the-host-app"></a>与主机应用程序通信

示例今天上面创建的扩展不与其主机应用程序通信 (**今天**屏幕)。 如果那样，它将使用[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)属性`TodayViewController`或`CodeBasedViewController`类。 

对于将从其主机应用接收数据的扩展，数据是以数组的形式[NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/)对象存储在[InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/)属性[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)的扩展的`UIViewController`。

其他扩展名，例如照片编辑扩展可能区分用户完成或取消使用情况。 这将回到此主机应用程序通过发出信号[CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/)和[CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/)方法[ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/)属性。

有关详细信息，请参阅 Apple 的[应用程序扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

<a name="Communicating-with-the-Parent-App" />

## <a name="communicating-with-the-parent-app"></a>与父应用程序通信

应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

- [Apple Watch 设置](~/ios/watchos/app-fundamentals/settings.md)。
- [共享 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共享文件](~/ios/watchos/app-fundamentals/parent-app.md#files)。

有关详细信息，请参阅[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一部分我们**使用功能**文档。

<a name="MobileCoreServices" />

## <a name="mobilecoreservices"></a>MobileCoreServices

在使用扩展，使用统一类型标识符 （实用程序） 来创建和操作应用程序、 其他应用程序和/或服务之间交换的数据。

`MobileCoreServices.UTType`静态类定义的以下帮助器属性，与 Apple 的`kUTType...`定义：

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

请参阅下面的示例：

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

有关详细信息，请参阅[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一部分我们**使用功能**文档。


<a name="Precautions-and-Considerations" />

## <a name="precautions-and-considerations"></a>预防措施和注意事项

扩展有明显更少内存提供给他们不是执行应用。 他们预计要执行快速和与指向用户和它们承载在应用程序的最小入侵。 但是，扩展还应提供到消费应用程序与的经过品牌打造的用户界面，可允许用户确定扩展的开发人员或它们所属的容器应用程序的独特、 有用函数。

提供这些紧密的要求，您应该仅部署已全面测试和优化性能和内存消耗的扩展。 

<a name="Summary" />

## <a name="summary"></a>摘要

本文档已覆盖扩展，它们是什么，扩展点以及由 iOS 规定扩展上的已知的限制的类型。 它讨论创建、 分发、 安装和运行扩展和扩展生命周期。 它提供的演练创建一个简单的**今天**小组件显示两种方式创建小组件的 UI 使用情节提要或代码。 它介绍了如何在 iOS 模拟器中测试扩展。 最后，它简要讨论与主机应用程序和几个预防措施和开发扩展时应考虑的事项通信。 


## <a name="related-links"></a>相关链接

- [ContainerApp （示例）](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [创建在 Xamarin.iOS 的扩展 （视频）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
