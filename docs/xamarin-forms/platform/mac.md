---
title: Mac 平台安装程序
description: 现在具有 Mac 平台的预览支持 Xamarin.Forms
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 0985fb533969a331ceee62f490bae600de9f189c
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="mac-platform-setup"></a>Mac 平台安装程序

![预览](~/media/shared/preview.png)

在开始之前，创建 （或使用现有） Xamarin.Forms 项目。
你仅可以添加为 mac。 使用 Visual Studio 的 Mac 应用程序

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**通过将 macOS 项目添加到 Xamarin.Forms， [Xamarin 大学](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>添加 Mac 应用程序

请按照这些说明中添加的 Mac 应用程序将运行在 macOS 上 Sierra 和 Mac OS X El Capitan 操作：

1. 在适用于 Mac 的 Visual Studio，右键单击现有的 Xamarin.Forms 解决方案，然后选择**添加 > 添加新项目...**

2. 在**新项目**窗口选择**Mac > 应用程序 > Cocoa 应用**按**下一步**。

3. 类型**应用名称**（和 （可选） 选择停靠项目的不同名称），然后按**下一步**。

4. 查看配置和按**创建**。 这些步骤如下所示中：

  ![显示如何添加 Cocoa 应用的动画的说明](mac-images/add-macos-proj.gif)

5. 在 Mac 项目中，右键单击**包 > 添加包...** 添加[Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet。 你还应更新到此版本的其他项目。

6. 在 Mac 项目中，右键单击**引用**并添加对 Xamarin.Forms 项目 （共享项目或标准.NET 类库项目） 的引用。

  ![添加对 Xamarin.Forms 共享的代码项目的引用](mac-images/references-sml.png)

7. 更新**Main.cs**初始化`AppDelegate`:

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. 更新`AppDelegate`若要初始化 Xamarin.Forms，创建一个窗口，并加载 Xamarin.Forms 应用程序 (请记住要设置相应的`Title`)。 _如果你有需要初始化的其他依赖项，此处执行该操作以及。_

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. 双击**Main.storyboard**在 Xcode 中进行编辑。 选择**窗口**和_取消选中_**是初始控制器**复选框 （这是因为上面的代码创建一个窗口）：

  [![取消选中在 Xcode 中的是初始控制器复选框](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

  你可以编辑情节提要以删除不需要的项目中的菜单系统。

10. 最后，添加任何本地资源 （如。 图像文件） 所需指定现有平台项目中。

11. Mac 项目现在应该在 macOS 上运行你 Xamarin.Forms 代码 ！

## <a name="next-steps"></a>后续步骤

### <a name="styling"></a>“样式”

与对最新更改`OnPlatform`现在可以针对任意数量的平台。 这包括 macOS。

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

请注意还可能在如下的平台上双击： `<On Platform="iOS, macOS" ...>`。

### <a name="window-size-and-position"></a>窗口大小和位置

你可以调整的初始大小和位置中的窗口`AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>已知问题

这是预览版，因此你应不是全部是生产就绪状态。 以下是将 macOS 添加到你的项目时可能会遇到的一些事项：

### <a name="not-all-nugets-are-ready-for-macos"></a>并非所有 NuGets 都已准备好进行 macOS

包必须为目标"xamarinmac20"来处理 macOS 项目中。 你可能会发现，你使用的库的某些尚不支持 macOS。

在这种情况下，你将需要将请求发送到项目的维护，以将其添加。 直到它们具有支持，你可能需要查看有关其他信息。

### <a name="missing-xamarinforms-features"></a>缺少 Xamarin.Forms 功能

并非所有 Xamarin.Forms 功能都都已完成在此预览版中;下面是功能的一些尚未实现的列表：

* 页脚
* 映像 – 方面
* ListView – ScrollTo，UnevenRows 支持，刷新，SeparatorColor，SeparatorVisibility
* MasterDetailPage – BackgroundColor
* 导航 – InsertPageBefore
* OpenGLRenderer
* 选取器 – Bindable/可观测对象实现
* TabbedPage – BarBackgroundColor，BarTextColor
* TableView – UnevenRows
* ViewCell – IsEnabled，ForceUpdateSize
* WebView – 大多数 WebNavigationEvents


## <a name="related-links"></a>相关链接

- [Xamarin.Mac](~/mac/index.yml)
