---
title: iOS 9 兼容性
description: 即使你不打算直接向您的应用程序中添加 iOS 9 功能，应重新生成你的应用的 Xamarin 的最新版本。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b7cbec3f064e6000f991fb0f9ce256415f6ce5dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777546"
---
# <a name="ios-9-compatibility"></a>iOS 9 兼容性

_即使你不打算直接向您的应用程序中添加 iOS 9 功能，应重新生成你的应用的 Xamarin 的最新版本。_

> [!IMPORTANT]
> 此页上的信息适用于已在应用商店应用的客户面向 iOS 8 或更早版本，具有不已提交的用户更新为 iOS 9 兼容性。 如果你已在使用的最新版本的 Xcode 7 和 Xamarin.iOS 9-为你的应用程序开发，请访问[简介 iOS 9](~/ios/platform/introduction-to-ios9/index.md)。

如果出现第一个 iOS 9 测试版，我们使用较旧版本的显示为正在无法在 iOS 9 上启动的较旧的应用的 Xamarin 标识两个问题。

这两个问题 (作为[在我们的论坛上详细](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) 已：

- 应用创建针对 iOS 8 或更早版本无法在 32 位设备上启动 (包括使用生成的应用[统一 API](~/cross-platform/macios/unified/index.md))。
- 未指定 P/Invoke 由于而失败的完整路径。

Xamarin 安装更新到最新稳定通道版本中，然后重新生成并重新部署你的应用，解决了这两个问题。

_即使你不打算立即使用 iOS 9 功能更新应用，建议你重新构建 Xamarin 的最新版本并重新提交到应用商店_。



这将确保你的客户在升级后，你的应用程序将运行在 iOS 9。
你可以继续支持 iOS 8-使用最新版本重新生成不会影响应用程序的目标版本。

如果你在测试现有应用在 iOS 9 上的时有更多问题，读取[提高兼容性](#compat)下面一节。


### <a name="updating-with-visual-studio"></a>使用 Visual Studio 更新

建议你显式检查 Visual Studio 将更新为最新稳定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>有关组件、 Nugets 和其他库的新增功能？

你执行**不**需要等待新任何的版本组件或 Nugets 你用于解决上述两个问题。
只需通过重新生成应用程序与 Xamarin.iOS 的最新稳定版本解决这些问题。

同样，组件供应商和 Nuget 作者都是**不**需提交新的生成，只是为了解决上面提到的两个问题。 但是，如果任何组件或 Nuget 使用`UICollectionView`或负载中的视图**Xib**文件、 更新*可能*会需要用来处理下面所述的 iOS 9 兼容性问题。


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>提高你的代码中的兼容性

没有少数情况下的代码的模式*使用*iOS iOS 9 中的重大的较旧版本中的工作。 下面是可能存在的问题 （和其解决方案），可能出现在 iOS 9 上测试时：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是 null，在构造函数

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



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>从 Xib/Nib 加载视图时，UIView 故障到与代码编写者 init

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


### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息： 具有名称没有缓存映像...

你可能会遇到在系统崩溃时在日志中的以下信息：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是一个 bug Apple 的本机链接器中，这种情况发生时它们公开私有 framework （JavaScriptCore 被公开在 iOS 7，之前它是一个专用的框架），并且应用程序的部署目标的 iOS 版本时框架是私有的。 在这种情况下 Apple 的链接器将链接与专用而不是公共的版本的 framework 版本。

**修复：** 这将针对 iOS 9，但没有简单的解决方法，你可以在此期间应用自己： 只需面向更高版本 iOS 版本在你的项目 （在这种情况下，你可以尝试 iOS 7）。 其他框架可能出现类似问题，例如 WebKit framework 被公开在 iOS 8 中 （和因此面向 iOS 7 将导致此错误; 应为目标 iOS 8 以在应用中使用 WebKit）。



## <a name="related-links"></a>相关链接

- [iOS 9 兼容性版本信息](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [更新你的 Xamarin.iOS 应用程序到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
