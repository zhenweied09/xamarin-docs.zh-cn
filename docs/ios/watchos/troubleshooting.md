---
title: "watchOS 故障排除"
description: "已知的问题和解决方法的 watchOS 开发问题。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 9c9032b3206fa35d264bd69c94b7882e877b334f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="watchos-troubleshooting"></a>watchOS 故障排除

此页包含其他信息和解决方法仍在开发的功能。 某些这些解决方法仅适用于我们的预览版本。

- [已知问题](#knownissues)

- [移除图标图像的 Alpha 通道](#noalpha)

- [手动将接口控制器文件添加](#add)Xcode 接口生成器。

- [启动从命令行 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知问题

### <a name="general"></a>常规

<a name="deploy" />
<!--
* You cannot deploy to the App Store *from within Visual Studio for Mac or Visual Studio*
    in the current release. You should create an **Archive** in Visual Studio for Mac
    and then switch to Xcode to upload the archive to iTunes Connect. Visual Studio
    is not currently supported (but will be a future release). Refer to the
    [deployment guide](~/ios/watchos/deploy-test/appstore.md) for more information.
-->

- 适用于 Mac 的 Visual Studio 的早期版本不正确地显示之一**AppleCompanionSettings**图标为 88 x 88 像素; 这会导致**缺少图标错误**如果你尝试将提交到应用程序存储区。
    此图标应为 87 x 87 像素 (29 单位 **@3x**  Retina 屏幕)。 无法为 Mac-编辑在 Xcode 中的图像资产解决此问题在 Visual Studio 中，或手动编辑**Contents.json**文件 (以匹配[此示例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。

- 如果监视扩展项目**Info.plist > WKApp 捆绑 ID**不[正确设置](~/ios/watchos/get-started/project-references.md)以匹配 Watch 应用**捆绑 ID**，调试器将无法进行连接和视觉适用于 Mac 的 studio 将等待消息*"正在等待调试器连接"*。

- 中支持调试**通知**模式但无法保证其准确性。 重试有时会起作用。 确认 Watch 应用**Info.plist** `WKCompanionAppBundleIdentifier`设置为相匹配的 iOS 父/容器应用的捆绑标识符 (即。 在 iPhone 运行的一个)。

<!--
- **Can't launch application on Watch simulator.** This seems to
    be an issue with the iOS Simulator hanging when trying to
    install an app that has changed. Xcode release notes (beta 4)
    includes a similar known issue:
    If the issue persists, reset the Simulator (**iOS Simulator > Reset Content and Settings...**).
-->

- iOS 设计器不显示为快速或通知接口控制器的入口点箭头。

- 无法添加两个`WKNotificationControllers`到情节提要。
    解决方法：`notificationCategory`情节提要 XML 中的元素始终插入具有相同`id`。 若要解决此问题，你可以添加两个 （或多个） 通知控制器、 文本编辑器中打开情节提要文件和手动更改`id`元素是唯一的。

    [ ![](troubleshooting-images/duplicate-id-sml.png "打开情节提要文件位于文本编辑器，并手动更改要是唯一的 id 元素")](troubleshooting-images/duplicate-id.png)

- 你可能会看到错误"还未生成应用程序"时尝试启动应用程序。 之后将发生这种情况**清理**当启动项目设置为监视扩展项目。
    解决方法是选择**生成 > 全部重新生成**，然后重新启动应用程序。

### <a name="visual-studio"></a>Visual Studio

IOS 设计器支持监视工具包*需要*解决方案以正确配置。 如果未设置项目引用 (请参阅[如何设置引用](~/ios/watchos/get-started/project-references.md)) 然后设计图面将无法正常工作。

<!--
* New Watch Kit apps created in Visual Studio might not allow
    starting in Notifications mode.

* You cannot deploy to the App Store from Visual Studio (see [notes above](#deploy)
    and the [deployment guide](~/ios/watchos/deploy-test/appstore.md)). Use
    Visual Studio for Mac and Xcode on your Mac Build Host.
    -->

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>移除图标图像的 Alpha 通道

图标不应包含 alpha 通道 （alpha 通道定义图像的透明区域），否则应用程序与此类似的错误的应用商店提交过程将被拒绝：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

很容易删除 Mac OS X 使用 alpha 通道**预览**应用：

1. 打开中的图标图像**预览**，然后选择**文件 > 导出**。

2. 显示的对话框将包括**字母**存在 alpha 通道时的复选框。

    ![](troubleshooting-images/remove-alpha-sml.png "如果存在 alpha 通道，则，显示的对话框将包含字母复选框")

3. *Untick* **字母**复选框和**保存**到正确的位置的文件。

4. 图标图像应现在通过 Apple 的验证检查。


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手动将接口控制器文件添加

> [!IMPORTANT]
> Xamarin 的监视包支持包括设计监视情节提要的 iOS 设计器中 （在 Visual Studio for Mac 和 Visual Studio） 中，不需要下面所述的步骤。 只需为接口控制器的类名称在 Visual Studio for Mac 属性填充和 C# 代码文件将自动创建。


*如果*正在使用 Xcode 接口生成器，请按照这些步骤创建您监视的应用程序的新接口控制器并启用与 Xcode 同步，以使容器和操作均在 C# 中可用：


1. 打开 watch 应用**Interface.storyboard**中**Xcode 接口生成器**。
    
    ![](troubleshooting-images/add-6.png "在 Xcode 接口生成器中打开情节提要")

2. 将一个新`InterfaceController`到情节提要：

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. 你可以现在将控件拖到接口控制器 （如。 标签和按钮） 但不是能创建插座或操作，因为没有任何**.h**标头文件。 以下步骤将导致所需**.h**要创建标头文件。

    ![](troubleshooting-images/add-2.png "布局中的按钮")

4. 关闭情节提要并返回到 Visual Studio for mac。 创建一个新的 C# 文件**MyInterfaceController.cs** （或你喜欢的任何名称） 中**观看应用扩展**项目 （不监视应用程序本身所在情节提要）。 添加以下代码 （更新命名空间、 类名、 和的构造函数名称）：

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. 创建另一个新的 C# 文件**MyInterfaceController.designer.cs**中**观看应用扩展**项目，并添加下面的代码。 务必要更新的命名空间，类名和`Register`属性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    提示： 你可以 （可选） 使此文件的第一个文件的子节点通过用于 Mac 解决方案填充将其拖动到的其他 C# 文件在 Visual Studio。 它随后将显示如下：
    
    ![](troubleshooting-images/add-5.png "解决方案填充")

6. 选择**生成 > 所有生成**，以便 Xcode 同步将识别新的类 (通过`Register`属性)，我们使用。

7. 重新打开监视应用情节提要文件上右键单击并选择情节提要**打开 > Xcode 接口生成器**:

    ![](troubleshooting-images/add-6.png "在接口生成器中打开情节提要")

8. 选择你新接口控制器并为其提供更高版本，例如定义类名。 `MyInterfaceController`。
如果一切已运行正常，它应显示在中自动**类：**下拉列表中，您可以从那里选择它。

    ![](troubleshooting-images/add-4.png "设置自定义类")

9. 选择**助手编辑器中**查看在 Xcode （使用两个重叠圆圈图标） 中，因此，你可以看到情节提要和代码的并行安装：

    ![](troubleshooting-images/add-7.png "助手编辑器工具栏项")

    当焦点在代码窗格中，确保你正在查看**.h**标头文件，并且如果痕迹导航栏中右键单击并选择正确的文件 (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "选择 MyInterfaceController")

10. 你现在可以创建 outlet 和操作**Ctrl + 拖动**从到情节提要**.h**标头文件。

    ![](troubleshooting-images/add-9.png "创建 outlet 和操作")

    当你释放拖动时，你将会提示您选择是否创建电源插座或操作，并且选择其名称：

    ![](troubleshooting-images/add-a.png "Outlet，操作对话框")

11. 一旦情节提要更改将保存并关闭 Xcode，返回到 Visual Studio for mac。 它将检测标头文件更改并自动将代码添加到**。 designer.cs**文件：


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


你可以现在引用控件 （或执行操作） 在 C# ！


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>启动监视应用程序从命令行

> [!IMPORTANT]
> 你可以在常规应用模式下启动监视应用程序，默认情况下，以及在**速览**或**通知**使用模式[自定义执行参数](~/ios/watchos/get-started/installation.md#custommodes)Visual Studio 中用于 Mac 和Visual Studio。


命令行还可用于控制 iOS 模拟器。 用于启动监视应用程序的命令行工具是**mtouch**。

下面是完整的示例 （作为单独的一行在终端中执行）：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

你需要更新以反映你的应用程序的参数是`launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

主应用捆绑包的完整路径*的 iOS 应用程序包含的监视应用程序和扩展*。

> [!NOTE]
> *注意：*你需要提供指向*iPhone 应用程序.app 文件*，即一个，将部署到 iOS 模拟器和包含的监视扩展和 watch 应用。

示例:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>通知模式

测试应用程序的[**通知**模式](~/ios/watchos/platform/notifications.md)，将其设置`watchlaunchmode`参数`Notification`并提供包含测试通知负载的 JSON 文件的路径。

负载参数是*必需*通知模式。

例如，将这些自变量添加到 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>其他参数

其余的自变量被解释如下：

### <a name="--sdkroot"></a>--sdkroot

必须的。 指定到 Xcode （6.2 或更高版本） 的路径。

示例:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>-设备

要执行的模拟器设备。 这可以指定两种方式，使用特定设备，udid 或使用运行时和设备类型的组合。

确切的值而异的计算机，和可以使用 Apple 的查询**simctl**工具：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

示例:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**运行时和设备类型**

示例:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
