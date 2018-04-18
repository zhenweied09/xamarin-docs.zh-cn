---
title: 创建 MonoGame UWP 项目
description: MonoGame 可用来创建游戏和应用适用于通用 Windows 平台，面向多个设备使用一个基本代码和一组内容。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: ee4ee83c07cf01d1324b5f127d4f77ced0df2afe
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="creating-a-monogame-uwp-project"></a>创建 MonoGame UWP 项目

_MonoGame 可用来创建游戏和应用适用于通用 Windows 平台，面向多个设备使用一个基本代码和一组内容。_

本演练涵盖 MonoGame 通用 Windows 平台 (UWP) 项目创建和加载的内容。 UWP 应用的可运行所有 Windows 10 设备，包括桌面、 平板电脑、 Windows Phone 和 Xbox One。

本演练将创建一个空的项目，其中显示了*藏蓝色*背景 （XNA 应用程序的传统的背景色）。

## <a name="requirements"></a>要求

开发 MonoGame UWP 应用需要：

- Windows 10 操作系统
- 任何版本的 Visual Studio 2015
- Windows 10 开发人员工具
- 设置设备到开发人员模式
- [Visual Studio 的 MonoGame 3.5](http://www.monogame.net/2016/03/17/monogame-3-5/)或更高版本

有关详细信息，请参阅此[页上设置 Windows 10 UWP 开发](https://msdn.microsoft.com/en-us/windows/uwp/get-started/get-set-up)。

可以在零售 Xbox One 硬件上开发 Xbox One 游戏。 在开发 PC 和 Xbox One 上需要其他软件。 有关配置 Xbox One 游戏开发的信息，请参阅 》 上此页面[设置 Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/index)。

## <a name="creating-an-empty-template"></a>创建一个空的模板

一旦安装了所有所需的资源和 Windows 10 计算机上已启用开发人员模式，我们可以创建通过执行以下步骤使用 Visual Studio 的新 MonoGame 项目：

1. 选择**文件** > **新** > **项目...**
1. 选择**已安装** > **模板** > **Visual C#** > **MonoGame**类别： 

    ![](uwp-images/image1.png "MonoGame 类别")

1. 选择**MonoGame Windows 10 通用项目**选项： 

    ![](uwp-images/image2.png "选择 MonoGame Windows 10 通用项目选项")

1. 输入新项目的名称，然后单击**确定**。
如果单击确定后，Visual Studio 显示任何错误，请验证安装了 Windows 10 工具和设备处于开发者模式。

在 Visual Studio 完成创建模板，我们可以运行此项可查看空的项目运行：

![](uwp-images/image3.png "在 Visual Studio 完成创建模板，则运行该程序会看到运行该空项目")

角中的数字提供诊断信息。 此信息可以通过删除中的代码才能移除`App.xaml.cs`中`DEBUG`中阻止`OnLaunched`方法：


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>在一个 Xbox 上运行

UWP 项目可以显示从同一个项目，以便部署到任意 Windows 10 设备。 设置 Windows 10 的开发计算机和 Xbox One 之后, UWP 应用可以通过切换到远程计算机的目标，并访问 Xbox One 的 IP 地址部署：

![](uwp-images/remote.png "可以通过切换到远程计算机的目标，并访问 Xbox 的 IP 地址部署 UWP 应用")

在 Xbox One，白色边框表示电视机非安全区域。 有关详细信息，请参阅[安全区域部分](#Safe_Area_on_Xbox_One)。

![](uwp-images/safearea.png "在 Xbox One，白色边框所表示的电视的非安全区域")

### <a name="safe-area-on-xbox-one"></a>在 Xbox 一个安全区域

开发游戏控制台需要考虑安全区域中，这是其中应包含所有关键的视觉对象 （如 UI 或 HUD） 在屏幕的中心中的某个区域。 安全区之外的区域并非一定要在所有电视机上可见，因此放置在此区域中的视觉对象可能会部分或完全上看不到某些显示。

Xbox One 的 MonoGame 模板会考虑安全区域，并将它呈现为白色边框。 此区域也反映在运行时在游戏的`Window.ClientBounds`Visual Studio 中的监视窗口此图中所示的属性。 请注意，客户端边界的高度 1016，尽管 1920 x 1080 显示分辨率：

![](uwp-images/clientbounds.png "请注意，客户端边界的高度是 1016，尽管 1920 x 1080 显示分辨率")

## <a name="referencing-content-in-uwp-projects"></a>引用在 UWP 项目中的内容

MonoGame 项目中的内容可以直接从文件或通过引用[MonoGame 内容管道](~/graphics-games/cocossharp/content-pipeline/index.md)。 小型游戏项目可能受益于从文件加载的简单性。 更大型的项目将受益于使用内容的管道来优化内容，以减少大小和加载时间。 与 Xbox 360 上 XNA 不同`System.IO.File`类位于 Xbox 一个 UWP 应用。

加载内容使用内容的管道的详细信息，请参阅[内容管道指南](~/graphics-games/cocossharp/content-pipeline/index.md)。 

### <a name="loading-content-from-file"></a>从文件加载内容

与 iOS 和 Android，不同 UWP 项目可以引用相对于可执行文件的文件。 简单游戏可用于此技术负载内容而无需修改并生成内容的管道项目。

若要加载`Texture2D`从文件：

1. 将.png 文件添加到 UWP 项目中的内容文件夹。 将内容添加到内容文件夹是 XNA 和 MonoGame 中的约定。
1. 右键单击新添加 PNG，然后选择属性。
1. 更改**复制到输出目录**到**如果较新则复制**。
1. 将以下代码添加到你的游戏初始化方法，以加载`Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

有关详细信息使用`Texture2D`，请参阅[MonoGame 指南简介](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>总结

本指南介绍如何在加载文件时创建新的 UWP 项目和 UWP 特定注意事项。 开发人员感兴趣创建完整的 UWP 游戏可以阅读更多有关中 MonoGame [MonoGame 指南简介](~/graphics-games/monogame/introduction/index.md)。