---
title: WPF 平台安装程序
description: 现在具有 WPF 平台的预览支持 Xamarin.Forms
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 416e33f131c6e1ef144608f98964fd8372f454f8
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33799342"
---
# <a name="wpf-platform-setup"></a>WPF 平台安装程序

![预览](~/media/shared/preview.png)

Xamarin.Forms 现在具有预览支持的 Windows Presentation Foundation (WPF)。 本文演示如何将 WPF 项目添加到 Xamarin.Forms 解决方案。

启动、 在 Visual Studio 2017 中, 创建新的 Xamarin.Forms 解决方案或使用现有 Xamarin.Forms 解决方案，例如之前, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)。 仅可针对 Windows 中的 Xamarin.Forms 解决方案的 WPF 应用中。

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>将 WPF 项目添加到包含 Xamarin.University 的 Xamarin.Forms 应用程序

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF 支持，通过[Xamarin 大学](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>添加一个 WPF 应用

按照这些说明来添加的 WPF 应用程序将在 Windows 7、 8 和 10 台式机上运行：

1. 在 Visual Studio 2017，右键单击解决方案名称上**解决方案资源管理器**选择**添加 > 新建项目...**.

2. 在**新项目**窗口中的，在左侧选择**Visual C#** 和**Windows 经典桌面**。 在项目类型列表中，选择**WPF 应用程序 (.NET Framework)**。 

3. 键入与项目的名称**WPF**扩展，例如， **BoxViewClock.WPF**。 单击**浏览**按钮，选择**BoxViewClock**文件夹，然后按**选择文件夹**。 这会将 WPF 项目放在解决方案中其他项目所在的目录中。

    ![添加新的 WPF 项目](wpf-images/add-new-project.png "添加新的 WPF 项目")

    按确定以创建项目。

4. 在**解决方案资源管理器**，右键单击新**BoxViewClock.WPF**项目，然后选择**管理 NuGet 包**。 选择**浏览**选项卡上，单击**包括预发行版**复选框，并搜索**Xamarin.Forms**。

    ![选择的 NuGet 包](wpf-images/select-nuget-package.png "选择的 NuGet 包")

    选择包并单击**安装**按钮。

5. 现在搜索**Xamarin.Forms.Platform.WPF**包并将一个以及安装。 请确保包是从 Microsoft ！

6. 右键单击解决方案名称**解决方案资源管理器**和选择**管理解决方案的 NuGet 包**。 选择**更新**选项卡和**Xamarin.Forms**包。 选择所有项目，然后将它们更新为相同的 Xamarin.Forms 版本：

    ![更新 NuGet 包](wpf-images/update-nuget-package.png "更新 NuGet 包") 

7. 在 WPF 项目中，右键单击**引用**。 在**引用管理器**对话框中，选择**项目**左侧，并检查与相邻的复选框**BoxViewClock**项目：

    ![引用该共享的项目](wpf-images/reference-shared-project.png "引用该共享的项目")

8. 编辑**MainWindow.xaml** WPF 项目文件。 在`Window`标记中，添加的 XML 命名空间声明**Xamarin.Forms.Platform.WPF**程序集和命名空间：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    现在更改`Window`到标记`wpf:FormsApplicationPage`。 更改`Title`将设置为应用程序，例如，名称**BoxViewClock**。 已完成的 XAML 文件应如下所示：

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. 编辑**MainWindow.xaml.cs** WPF 项目文件。 添加两个新`using`指令：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    更改类的基类`MainWindow`从`Window`到`FormsApplicationPage`。 以下`InitializeComponent`调用，添加以下两个语句：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    除注释外的和未使用`using`指令、 完整**MainWindows.xaml.cs**文件应如下所示：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

10. 右键单击中的 WPF 项目**解决方案资源管理器**和选择**设为启动项目**。 按 f5 键以使用 Visual Studio 调试器运行程序，在 Windows 桌面上：

    ![WPF 字数时钟](wpf-images/wpf-boxviewclock.png "WPF 字数时钟" )

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

你可以确定 Xamarin.Forms 应用程序运行从代码或 XAML 哪些平台。 这可以在 WPF 上运行时更改程序特征。 在代码中，比较的值`Device.RuntimePlatform`与`Device.WPF`常量 （它等于字符串"WPF"）。 如果没有匹配项，在 WPF 上运行应用程序。

在 XAML 中，你可以使用`OnPlatform`标记选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="window-size"></a>窗口大小

你可以调整在 WPF 窗口的初始大小**MainWindow.xaml**文件：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>问题

这是预览版，因此你应不是全部是生产就绪状态。 并非所有的 Xamarin.Forms NuGet 包已准备好进行 WPF 中，和某些功能可能无法完全正常。

