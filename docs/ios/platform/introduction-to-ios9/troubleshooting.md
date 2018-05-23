---
title: 疑难解答
description: 本文提供使用 iOS 9 Xamarin.iOS 应用程序中的多个故障排除提示。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 1b335fc6b19d87a46059511baf866433691b1b4d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>疑难解答

_本文提供使用 iOS 9 Xamarin.iOS 应用程序中的多个故障排除提示。_

## <a name="there-was-a-problem-parsing-the-xml"></a>时分析 XML 出现问题

Xamarin iOS 设计器尚不支持 Xcode 7 功能。 情节提要将无法在与设计器中加载 _"时出现问题分析 XML"_ 时尝试使用新的 iOS 9 (Xcode 7) StackView 之类的设计器元素。

iOS Xcode 7 功能的设计器支持针对即将到来的周期 6 功能版本。 周期 6 的预览版本当前位于 Alpha 通道，并具有有限的新 Xcode 7 功能的支持。

适用于 Mac 的 Visual Studio 的部分的解决方法： 右键单击情节提要并选择**打开** > **Xcode 接口生成器**。

## <a name="where-are-the-ios-8-simulators"></a>在哪里？ iOS 8 模拟器

如果你已安装的 Xcode 7 （或更高版本） 会自动将所有 iOS 8 模拟器替换 iOS 9 模拟器默认情况下。 如果你仍然需要在 iOS 8 上进行测试，你可以启动 Xcode，然后下载并安装 iOS 8 模拟器。

在 Xcode 中，选择**Xcode**菜单然后**首选项...**  > **下载**:

[![](troubleshooting-images/ios8.png "iOS 8 模拟器下载")](troubleshooting-images/ios8.png#lightbox)

单击**检查和立即安装**按钮以重新安装 iOS 8 模拟器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>左/右属性错误布局约束

在 iOS 8 （和之前），情节提要中的 UI 元素可以使用这二者的融合**右** & **左**属性 (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) 和**前导** & **尾随**属性 (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) 中的布局相同。

如果在相同的情节提要运行 iOS 9 中，它将导致以下窗体中出现异常：

> 终止应用程序由于未捕获的异常 NSInvalidArgumentException，原因: * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: 约束不能成为之间前导/尾随属性及其/从右到左属性。 使用前导/尾随的同时还是两者皆否。

iOS 9 强制执行布局使用**右** & **左**_或_**前导** &  **尾随**属性但*不*两者。 若要解决此问题，更改所有的布局约束，以使用相同的属性在你的情节提要文件内设置。

有关详细信息，请参阅[iOS 9 约束错误](http://stackoverflow.com/questions/32692841/ios-9-constraint-error)堆栈溢出的讨论。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>错误 ITMS-90535： 意外的 CFBundleExecutable 密钥

切换到 iOS 9 之后, 将从应用程序使用第三方组件 （专门我们现有 Google 地图组件），编译并尝试提交新的生成到 iTunes Connect 窗体中发生错误时，在 iOS 8 （或更早版本），运行：

> 错误 ITMS-90535： 意外的 CFBundleExecutable 键。 在 Payload/app-name.app/component.bundle 捆绑包不包含的捆绑包可执行文件...

此问题通常可通过在项目中查找命名的捆绑解决然后-一样的错误消息提供的建议的编辑`Info.plist`捆绑中是通过删除`CFBundleExecutable`密钥。 `CFBundlePackageType`密钥应设置为`BNDL`以及。

进行这些更改之后, 执行干净和重新生成整个项目。 你应能够进行这些更改后将提交到 iTunes Connect 不会出现问题。

有关详细信息，请参阅此[堆栈溢出](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)讨论。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失败 (-9824) 错误

时尝试连接到 internet，直接或从 web 视图在 iOS 9 中，可能会在窗体中出现错误：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或在窗体中：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

在 iOS9，应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。 此外，ATS 需要通信使用`HTTPS`协议和高级 API 通信进行加密 TLS 版本 1.2 使用向前保密。

由于默认情况下，在生成适用于 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有连接的应用程序中启用了 ATS `NSURLConnection`，`CFURL`或`NSURLSession`都将遵循 ATS 安全要求。 如果你的连接不满足这些要求，则会失败并出现异常。

请参阅[Opting 打卡的 ATS](~/ios/app-fundamentals/ats.md)一部分我们[应用传输安全](~/ios/app-fundamentals/ats.md)指南以获取如何解决此问题的信息。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我的现有应用不在 iOS 9 上运行

请参阅我们[iOS 9 兼容性信息](~/ios/platform/introduction-to-ios9/ios9.md)有关重新生成和重新部署现有应用在 iOS 9 上运行的说明。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是在构造函数中的 Null

**原因：** 在 iOS 9`initWithFrame:`构造函数现在是必需的由于在为 iOS 9 中的行为更改[UICollectionView 的文档所述](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果注册指定的标识符的类，并且必须创建新的单元格，该单元格现在通过调用初始化其`initWithFrame:`方法。

**修复：** 添加`initWithFrame:`此类构造函数：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相关示例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>从 Xib/Nib 加载视图时，UIView 故障到与代码编写者 Init

**原因：** `initWithCoder:`构造函数是从接口生成器 Xib 文件加载视图时调用。 如果此构造函数不会导出非托管的代码无法调用它的我们托管的版本。 以前 （如。 在 iOS 8) 中`IntPtr`已调用构造函数来初始化视图。

**修复：** 创建和导出`initWithCoder:`此类构造函数：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相关的示例：[聊天](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息： 具有名称没有缓存映像...

你可能会遇到在系统崩溃时在日志中的以下信息：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是一个 bug Apple 的本机链接器中，这种情况发生时它们公开私有 framework （JavaScriptCore 被公开在 iOS 7，之前它是一个专用的框架），并且应用程序的部署目标的 iOS 版本时框架是私有的。 在这种情况下 Apple 的链接器将链接与专用而不是公共的版本的 framework 版本。

**修复：** 这将针对 iOS 9，但没有简单的解决方法，你可以在此期间应用自己： 只需面向更高版本 iOS 版本在你的项目 （在这种情况下，你可以尝试 iOS 7）。 其他框架可能出现类似问题，例如 WebKit framework 被公开在 iOS 8 中 （和因此面向 iOS 7 将导致此错误; 应为目标 iOS 8 以在应用中使用 WebKit）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企业开发人员

当尝试在实际 iOS 硬件上运行你的 Xamarin.iOS 应用程序的 iOS 9 版本，你可能会收到一条消息指出在设备上开发人员帐户不受信任。 例如：

[![](troubleshooting-images/untrusted01.png "不受信任的企业开发人员警报")](troubleshooting-images/untrusted01.png#lightbox)

若要解决此问题，请执行以下操作：

1. 在开发 mac。 上启动 Xcode （最新的 beta 版本）
2. 选择**设备**从**窗口**菜单打开设备窗口： 

    [![](troubleshooting-images/untrusted02.png "设备窗口")](troubleshooting-images/untrusted02.png#lightbox)
3. 下**设备**端面板中，选择你的设备，右键单击并选择**显示预配配置文件...**: 

    [![](troubleshooting-images/untrusted03.png "SShow 预配配置文件")](troubleshooting-images/untrusted03.png#lightbox)
4. 选择当前在该设备，然后单击每个预配配置文件**-** 按钮以将其删除： 

    [![](troubleshooting-images/untrusted04.png "删除预配配置文件")](troubleshooting-images/untrusted04.png#lightbox)
5. 从**Xcode**菜单上，选择**首选项...** 和**帐户**: 

    [![](troubleshooting-images/untrusted05.png "Xcode 帐户首选项")](troubleshooting-images/untrusted05.png#lightbox)
6. 单击**查看详细信息...** 按钮，然后单击**下载所有**按钮： 

    [![](troubleshooting-images/untrusted06.png "下载所有配置文件")](troubleshooting-images/untrusted06.png#lightbox)
7. 更新完成列表后，单击**完成**按钮，然后关闭首选项窗口。
8. 删除的 Xamarin.iOS 应用程序已尝试从 iOS 设备进行测试的现有版本。
9. 返回到 Visual Studio for Mac，执行一个干净的生成并尝试在设备上重新运行该应用程序。

你可能必须停止并重启 Visual Studio for Mac，才能看到由 Xcode 加载新预配配置文件。 您可能还需要调整**iOS 捆绑签名**Xamarin.iOS 应用程序选择新的预配配置文件的选项。

## <a name="launch-screen-issues"></a>启动屏幕问题

iOS 9 现在强制执行的启动屏幕要求，以便不再可以重用同一个启动映像以支持不同的接口的方向。 请参阅 Apple 的[UILanchImage 引用](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)有关详细信息。

或者，可以使用情节提要文件以提供您的应用程序启动屏幕而不是使用一套 **.png**图像文件。 这是现在 Apple 的首选方法，可以显示启动屏幕。 请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南以获取详细信息。

最后，你的应用程序必须为其启动屏幕使用情节提要文件，并支持所有四个接口方向 （纵向、 倒置纵向、 横向左右横向） 才会考虑让滑动通过面板中或在拆分视图模式下运行。 若要了解有关 iOS 9 的新多任务功能的详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/multitasking.md)指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 异常

在编译和运行 ios 9 现有 Xamarin.iOS 应用程序时可能会在窗体中出现错误：

> Objective C 引发异常。  名称： NSInternalInconsistencyException 原因： 应用程序窗口应能够在应用程序启动末尾的根视图控制器

这是错误是否引发因为应用窗口应能够在应用程序启动末尾根视图控制器，你现有的应用程序未使用。

有关于此问题至少两个可能的解决方法：

1. 更新应用程序以使用情节提要文件而不是`xib`文件以定义其用户界面。 本练习到布局情节提要要求相当多的时间，取决于应用程序大小和了解如何使用 iOS 设计器 （或 Xcode 的接口生成器）。 有关详细信息，请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
2. 安装程序`RootViewController`的应用程序窗口的属性中`FinishedLaunching`中的方法`AppDelegate`类以指向您的应用程序 UI 中的视图控制器。

## <a name="when-to-initialize-views-and-view-controllers"></a>何时初始化视图和视图控制器

使用 Xamarin.iOS 是可以进行内部入托管代码公开的内容，但其将中断 iOS 设计时调用的构造函数的视图或视图控制器初始化。

一般情况下你应不初始化任何内容可返回 Objective C 代码从调用构造函数因为不能确保调用时。 这还意味着没有良好的起点 (其他.ctor) 或重写 （如 Objective C 不具有任何事件） 的调用其中应进行这种初始化。



## <a name="related-links"></a>相关链接

- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新你的 Xamarin.iOS 应用程序到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
