---
title: 适用于 iPad 的多任务
description: iOS 9 支持两个应用程序运行在同一时间、 通过使用幻灯片或拆分视图。 它还支持视频播放图片中图片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 553fd6e45df897037d6ad90ef9211eef0bf27aae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="multitasking-for-ipad"></a>适用于 iPad 的多任务

_iOS 9 支持两个应用程序运行在同一时间、 通过使用幻灯片或拆分视图。它还支持视频播放图片中图片。_

![](multitasking-images/about02-sml.png "拆分屏幕示例") ![ ](multitasking-images/about03-sml.png "图片中图示例")

iOS 9 增加了对在特定 iPad 硬件上同时运行两个应用程序的多任务支持。 适用于 iPad 的多任务支持通过以下功能：

- [**幻灯片通过**](#Slide-Over) -允许用户暂时滑出面板 （不管是基于语言方向屏幕的右或向左一端） 涵盖大约 25%的当前运行的主应用程序中运行的第二个 iOS 应用程序。 滑动通过仅上 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 提供了。
- [**拆分视图**](#Split-View) -支持的 iPad 的硬件上 (iPad 无线 2，iPad 迷你 4 和 iPad Pro 仅)，用户可以选取第二个应用程序并使用当前正在运行的应用在拆分屏幕模式下运行它的并排显示。 用户可以控制主屏幕每个应用所占的百分比。
- [**在图中的图片**](#Picture-in-Picture) -用于播放视频的内容，现在视频可以播放该浮动通过其他应用在 iOS 设备上当前运行的可移动和可调整大小窗口中的应用程序。 用户可以完全控制的大小和位置的此窗口。 在图中的图片都仅提供 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4。

有大量要时，应考虑的事项[应用程序中支持多任务](#Supporting-Multitasking-in-your-App)，包括：

- [屏幕的大小和方向](#Screen-Size-Considerations)
- [自定义硬件键盘快捷方式](#Custom-Hardware-Keyboard-Shortcuts)
- [资源管理](#Resource-Management-Considerations)

作为应用程序开发人员还可以[选择退出多任务](#Opting-Out-of-Multitasking)，包括[禁用 PIP 视频播放](#Disabling-PIP-Video-Playback)。

本文将介绍确保你的 Xamarin.iOS 应用程序在多任务的环境中正常运行，或如何选择退出多任务，如果这不是一个好适合您的应用程序所需的步骤。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**适用于 iPad，多任务通过[Xamarin 大学](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>多任务快速入门

若要支持**滑动通过**或**拆分视图**您的应用程序必须执行以下操作：

 - 是针对 iOS 9 （或更高版本） 构建的。
 - 用于其启动屏幕的情节提要 （和图像资产没有）。
 - 使用情节提要具有自动布局和大小类的其 UI。
 - 支持所有 4 的 iOS 设备方向 （纵向、 倒置纵向、 横向朝左和横向朝右）。

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>有关适用于 iPad 的多任务

iOS 9 提供通过引入的 iPad 上的新多任务功能_滑动通过_，_拆分视图_(iPad 无线 2，iPad 迷你 4 和 iPad Pro 仅) 和_图片中的图片_。 下列部分中，我们将花进一步查看这些功能。

<a name="Slide-Over" />

### <a name="slide-over"></a>通过幻灯片

滑动通过功能允许用户选取第二个应用程序并将其显示在小的滑动面板中，以提供快速的交互。 滑动通过面板是临时的并将关闭时用户将返回到再次使用主应用程序。

[![](multitasking-images/about01.png "滑动通过面板")](multitasking-images/about01.png#lightbox)

要记住的要点是用户决定将运行的两个应用，通过并行和开发人员可以不控制此过程。 因此，有你将需要要做，以确保你的 Xamarin.iOS 应用程序在滑动通过面板正确运行的几项：

- **使用自动布局和大小类**-现在，你的 Xamarin.iOS 应用程序可以运行幻灯片外侧面板，因为可以不再依赖于设备、 其屏幕的大小或其方向设布局为你的 UI。 若要确保你的应用程序正确缩放的接口，你将需要使用自动布局和大小类。 有关详细信息，请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
- **将资源有效地使用**-现在要共享您的应用程序与另一个正在运行的应用的系统，因为它是关键应用程序有效地使用系统资源。 当内存变为稀疏时，系统将自动终止的应用程序占用了大多数的内存。 请参阅 Apple 的[iOS 应用程序的能量效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)有关详细信息。

滑动通过仅上 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 提供了。 若要了解有关准备滑动通过您的应用程序的详细信息，请参阅 Apple 的[在 iPad 上采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文档。

<a name="Split-View" />

### <a name="split-view"></a>拆分视图

在支持的 iPad 硬件 (iPad 无线 2，iPad 迷你 4 和 iPad Pro 仅) 上，用户可以选取第二个应用程序，然后运行并排显示在拆分屏幕模式下当前正在运行的应用。 用户可以控制主屏幕每个应用程序通过拖动所占的百分比屏幕上分隔符。

[![](multitasking-images/about02.png "拆分视图")](multitasking-images/about02.png#lightbox)

和一样滑动通过用户决定哪些两个应用程序将运行的并行同样，开发人员可以不控制此过程。 因此，拆分视图在 Xamarin.iOS 应用程序上放置类似的要求：

- **使用自动布局和大小类**-因为你的 Xamarin.iOS 应用程序现在可以在用户的指定大小在拆分屏幕模式下运行，可以不再依赖于设备、 其屏幕的大小或其方向设布局为你的 UI。 若要确保你的应用程序正确缩放的接口，你将需要使用自动布局和大小类。 有关详细信息，请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
- **将资源有效地使用**-现在要共享您的应用程序与另一个正在运行的应用的系统，因为它是关键应用程序有效地使用系统资源。 当内存变为稀疏时，系统将自动终止的应用程序占用了大多数的内存。 请参阅 Apple 的[iOS 应用程序的能量效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)有关详细信息。

若要了解有关为拆分视图准备你的应用程序的详细信息，请参阅 Apple 的[在 iPad 上采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文档。

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>在图中的图片

图片功能中的新图片 (也称为_PIP_) 后，用户可以观看视频中用户可以在其他正在运行的应用上面的屏幕任意位置放置一个很小浮动窗口。

[![](multitasking-images/about03.png "示例图片浮动窗口中的图片")](multitasking-images/about03.png#lightbox)

与一样滑动通过和拆分视图中，用户也具有完全控制监视在图片模式下图中的视频。 如果你的应用的主要功能是若要观看视频，它将需要若要行为正确在 PIP 模式下进行一些修改。 否则，不需要进行任何更改以支持 PIP。

应用程序显示在用户的请求 PIP 视频，你将需要使用任一_AVKit_或_AV Foundation Api_。 媒体播放器框架已在 iOS 9 中已过时，不支持 PIP。

在图中的图片都仅提供 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4。 有关详细信息，请参阅我们[PictureInPicture 示例应用程序](https://developer.xamarin.com/samples/ios/iOS9/)和 Apple 的[在图片快速启动中的图片](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14)文档。

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>在你的应用程序中支持多任务

对于任何现有的 Xamarin.iOS 应用程序，支持多任务是透明的任务，只要你的应用程序已遵循 Apple 的设计指南和适用于 iOS 8 的最佳实践。 这意味着，应用程序应使用情节提要与自动布局和大小类对于其用户界面布局 (请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)有关详细信息)。

对于这些应用程序，不需要支持多任务，从而内良好行为很少或没有更改。 如果您的应用程序 UI 中，已使用其他方法，例如直接定位并调整大小中 C# 代码或如果它依赖于特定设备的屏幕大小或方向的 UI 元素创建的它将需要重大修改以正确的支持 iOS 9 多任务。

若要在任何新的 Xamarin.iOS 应用程序上支持 iOS 9 多任务，再次对所有应用程序的用户界面布局与自动布局和大小类一起使用情节提要，并实现下列部分中的说明进行操作。

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>屏幕的大小和方向注意事项

在 iOS 9 之前，您可以设计将应用与特定设备的屏幕大小和方向。 因为在滑出面板中或在拆分视图模式下，现在可以运行应用程序，它可以找到本身中任意一种紧凑或普通的水平大小类上运行 iPad，而不考虑设备的物理方向或屏幕大小。

[![](multitasking-images/sizeclasses01.png "屏幕的大小和方向注意事项")](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上的全屏应用具有常规的水平和垂直大小类。 所有 iPhone 但 iPhone 6 Plus 和 iPhone 6s Plus，位于采用任何方向的两个方向中有压缩大小类。 iPhone 6 Plus 和 iPhone 6s Plus 在横向模式中具有一个正则的水平大小类和 Compact 的垂直大小类 （非常类似于 iPad 迷你）。

在支持滑动通过和拆分视图的 iPad，你可以结束，以下组合：

| **方向** | **主应用程序** | **辅助应用程序** |
|--- |--- |--- |
| **Portrait** |75%的屏幕<br />Compact 水平<br />正则垂直|25%的屏幕<br />Compact 水平<br />正则垂直|
| **横向** |75%的屏幕<br />正则水平<br />正则垂直|25%的屏幕<br />Compact 水平<br />正则垂直|
| **横向** |50%的屏幕<br />Compact 水平<br />正则垂直|50%的屏幕<br />Compact 水平<br />正则垂直|

在示例中[MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)的应用程序，如果它在以横向模式 iPad 上运行全屏幕，它将提供的列表和详细信息视图在同一时间：

[![](multitasking-images/sizeclasses03.png "列表和显示在同一时间的详细信息视图")](multitasking-images/sizeclasses03.png#lightbox)

如果在滑动通过面板中运行相同的应用程序，它作为 Compact 的水平大小类布局，并只显示列表：

[![](multitasking-images/sizeclasses04.png "仅当设备是水平时，出现在列表")](multitasking-images/sizeclasses04.png#lightbox)

若要确保你的应用程序行为正确在这些情况下，你应采用以及大小类的特征集合，并符合`IUIContentContainer`和`IUITraitEnvironment`接口。 请参阅 Apple 的[UITraitCollection 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202)和我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南以获取详细信息。

此外，不再可以依赖于设备屏幕边界，以定义应用程序的可见区域，你将需要改为使用您的应用程序窗口边界。 由于窗口边界完全受控制的用户，你无法以编程方式对其进行调整或防止用户更改这些边界。

最后，你的应用程序必须使用情节提要文件存在而不是使用一组其启动屏幕**.png**图像文件，并支持所有四个接口方向 （纵向、 倒置纵向、 横向左右横向）若要考虑对运行在滑动通过面板中或在拆分视图模式下进行。

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>自定义硬件键盘快捷方式

在 iOS 9 iPad 上运行，Apple 有扩展的支持的硬件键盘。 iPad 始终具有包括基本外部键盘支持通过蓝牙和某些键盘制造商创建键盘包含硬连线的特定于 iOS 的密钥。

现在，使用 iOS 9，应用程序可以创建自己的自定义键盘快捷键。 此外，某些基本的键盘快捷方式与一样可用**命令 C** （副本），**命令 X** （剪切）、**命令 V** （粘贴） 和**命令 Shift H** （主），而无需应用程序正在对其进行专门编写的响应。

**命令选项卡**允许用户从键盘，非常类似 Mac OS 的应用之间快速切换，应用程序切换器将显示：

[![](multitasking-images/keyboard01.png "应用程序切换器")](multitasking-images/keyboard01.png#lightbox)

如果 iOS 9 应用程序包括键盘快捷方式，用户可以按住**命令**，**选项**或**控件**密钥以在弹出窗口中显示：

[![](multitasking-images/keyboard02.png "键盘快捷方式弹出窗口")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定义自定义键盘快捷键

如果我们将添加下面的代码到一个视图或视图控制器我们的应用程序，该视图或控制器可见时，将可自定义键盘快捷方式：

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

首先，我们重写`CanBecomeFirstResponder`属性，并返回`true`使视图或视图控制器可以接收键盘输入。 

接下来，我们重写`KeyCommands`属性并创建新`UIKeyCommand`为**命令 N**击键。 当激活键击时，我们调用`NewEntry`方法 (我们公开为 iOS 9 使用`Export`命令) 来执行请求的操作。

如果我们在 iPad 上运行此应用程序的附加硬件键盘和用户类型了**命令 N**，新的项将添加到列表。 如果用户按住**命令**密钥，快捷键的列表将显示：

[![](multitasking-images/keyboard03.png "键盘快捷方式弹出窗口")](multitasking-images/keyboard03.png#lightbox)

此示例，请参阅[MultiTask 应用](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)有关示例实现。

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>资源管理注意事项

即使对于应用程序已在使用 iOS 8 的设计指南和最佳实践，有效的资源管理可能仍是问题。 在 iOS 9 中，应用程序不再需要内存、 CPU 或其他系统资源的独占的使用。

因此，您必须调整你的 Xamarin.iOS 应用程序，以有效地使用系统资源或它面向在内存不足情况下终止。 这同样适用的应用的选择退出多任务，自第二个应用程序可能仍在运行滑动通过面板中或图片图片窗口需要额外的资源或导致刷新速率低于每秒 60 帧。

请考虑以下的用户操作和及其含义：

- **在滑动通过面板中输入文本**-即使你的应用程序没有文本输入，在系统键盘，现在可以通过其 UI 显示。 因此，应用程序可能需要对键盘显示通知 （如显示和隐藏键盘） 做出响应。
- **在滑动通过面板上运行第二个应用**-新的应用程序现在在前台运行，并避免与现有的应用，如内存和 CPU 周期的系统资源争用。
- **播放视频 PIP 窗口中的**-不只可以此窗口包括您的应用程序的接口的一部分，但启动视频应用仍在后台运行并消耗 CPU 和内存资源。

若要确保你的应用使用资源高效，应执行以下操作：

- **配置文件应用程序与检测**-检查是否有内存泄漏，产品说明具有明显的 CPU 使用率和其中应用程序可能会阻止主线程的区域。
- **响应状态转换方法**-在你**AppDelegate.cs**文件替代和响应状态更改，例如，输入或返回从后台应用程序的方法。 释放任何不是必需的资产，例如图像、 数据或视图和视图控制器。
- **测试与内存密集型应用程序并行**-运行使用滑出和在物理 iOS 硬件上的拆分视图与内存密集型应用程序如地图 （在附属视图模式下） 对应用程序和测试这两个应用程序保持响应状态和不崩溃。

请参阅 Apple 的[iOS 应用程序的能量效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)有关资源管理的详细信息。

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>选择退出多任务

虽然 Apple 提供的建议所有 iOS 9 应用都支持多任务，可能存在极特殊原因需要应用程序不太如游戏或需要全屏正常工作的相机应用。

对于你参加任一滑出将在面板中或在拆分视图模式下运行的 Xamarin.iOS 应用程序，编辑项目的**Info.plist**文件，然后检查**需要全屏**:

[![](multitasking-images/fullscreen01.png "选择退出多任务")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 时选择退出多任务可以防止你的应用程序在滑出或拆分视图中正在运行，它不会阻止另一个应用程序显示以及你的应用程序从运行在滑出或图片视频中的图片。

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>禁用 PIP 视频播放

在大多数情况下，你的应用程序应允许用户以用于播放它图片浮动窗口中的图片中显示任何视频内容。 但是，可能有情况下，这可能不所需的如游戏剪切的场景视频。

若要选择退出 PIP 视频播放，请执行以下操作应用程序中：

 - 如果你使用`AVPlayerViewController`若要显示视频，请设置`AllowsPictureInPicturePlayback`属性`false`。
 - 如果你使用`AVPlayerLayer`若要显示视频，请不要实例化`AVPictureInPictureController`。
 - 如果你使用`WKWebView`若要显示视频，请设置`AllowsPictureInPictureMediaPlayback`属性`false`。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了为确保 Xamarin.iOS 应用程序将运行并在 iOS 9 的新多任务能力 iPad 中的正确行为所需的步骤。 此外，则覆盖选择加入扩展的应用程序，它不是适合的多任务。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [执行多任务 （示例）](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [统一的情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [博客文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
