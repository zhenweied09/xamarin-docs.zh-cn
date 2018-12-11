---
title: Xamarin.Forms 快速入门
description: 本文说明如何使用 Xamarin.Forms 创建一个简单的跨平台便笺应用程序，供你输入便笺内容并将其保存到设备存储。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/21/2018
ms.openlocfilehash: 880fa527d91098cf8c5f8c46cd9c5cce9ec9a489
ms.sourcegitcommit: 744c0a50420bb091fca8b92a84c20e61c741cf9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742952"
---
# <a name="xamarinforms-quickstart"></a>Xamarin.Forms 快速入门

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

本快速入门教程详细介绍如何使用 Xamarin.Forms 创建一个简单的跨平台便笺应用程序，供你输入便笺内容并将其保存到设备存储。 最终的应用程序如下所示：

[![](quickstart-experimental-images/screenshots-sml.png "便笺应用程序")](quickstart-experimental-images/screenshots.png#lightbox "Notes Application")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Visual Studio 入门

1. 在“开始”屏幕中，启动 Visual Studio。 这会打开起始页：

    ![](quickstart-experimental-images/vs/start-page.png "Visual Studio")

2. 在 Visual Studio 中，单击“创建新项目...”以创建新项目：

    ![](quickstart-experimental-images/vs/new-solution.png "新建项目")

3. 在“新建项目”对话框中，单击“跨平台”，选择“移动应用(Xamarin.Forms)”模板，将“名称”设为“便笺”，为项目选择合适的位置，然后单击“确定”按钮：

    ![](quickstart-experimental-images/vs/new-project.png "跨平台项目模板")

    > [!IMPORTANT]
    > 本快速入门中的 C# 和 XAML 片段要求将解决方案命名为“便笺”。 使用不同的名称会导致：将本快速入门中的代码复制到解决方案中时出现生成错误。

4. 在“新的跨平台应用”对话框中，单击“空白应用”，选择“.NET Standard”作为代码共享策略，然后单击“确定”按钮：

    ![](quickstart-experimental-images/vs/new-app.png "新的跨平台应用")

5. 在“解决方案资源管理器”的“便笺”项目中，双击“MainPage.xaml”将其打开：

    ![](quickstart-experimental-images/vs/open-mainpage-xaml.png "打开的 MainPage.xaml")

6. 在“MainPage.xaml”中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    该代码以声明的方式定义页面的用户界面，它包含一个用于显示文本的 [`Label`、](xref:Xamarin.Forms.Label)一个用于文本输入的 [`Editor`](xref:Xamarin.Forms.Editor) 以及两个指示应用程序保存或删除文件的 [`Button`](xref:Xamarin.Forms.Button) 实例。 这两个 `Button` 实例水平放置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Label`、`Editor` 和 `Grid` 垂直放置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。

    按 **Ctrl+S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

7. 在“解决方案资源管理器”的“便笺”项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开：

    ![](quickstart-experimental-images/vs/open-mainpage-codebehind.png "打开的 MainPage.xaml.cs")

8. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    此代码定义一个 `_fileName` 字段，该字段引用名为 `notes.txt` 的文件，而该文件将便笺数据存储在应用程序的本地应用程序数据文件夹中。 如果有文件，则在执行页构造函数时读取文件并将其显示在 [`Editor`](xref:Xamarin.Forms.Editor) 中。 按“保存”[`Button`](xref:Xamarin.Forms.Button)时执行 `OnSaveButtonClicked` 事件处理程序，将 `Editor` 的内容保存到文件中。 按“删除”`Button` 时执行 `OnDeleteButtonClicked` 事件处理程序，删除该文件（前提是它存在）并删除 `Editor` 中的任何文本。

    按 **Ctrl+S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

### <a name="building-the-quickstart"></a>生成快速入门

1. 在 Visual Studio 中，选择“生成”>“生成解决方案”菜单项（或按 F6）。 将生成解决方案，Visual Studio 状态栏中将显示一条成功消息：

      ![](quickstart-experimental-images/vs/build-succeeded.png "生成已成功")

    如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成解决方案。

2. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 Android Emulator 内的应用程序：

    ![](quickstart-experimental-images/vs/android-start.png "Visual Studio Android 工具栏")

    ![](quickstart-experimental-images/vs/notes-android.png "Android Emulator 中的便笺")

    输入便笺内容，然后按“保存”按钮。

    > [!NOTE]
    > 以下步骤仅供在拥有符合 Xamarin.Forms 开发系统要求的[配对 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)时执行。

3. 在 Visual Studio 工具栏中，右键单击“Notes.iOS”项目，然后选择“设为启动项目”。

      ![](quickstart-experimental-images/vs/set-as-startup-project-ios.png "将 iOS 设为启动项目")

4. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 [iOS 远程模拟器](~/tools/ios-simulator/index.md)内的应用程序：

    ![](quickstart-experimental-images/vs/ios-start.png "Visual Studio iOS 工具栏")

    ![](quickstart-experimental-images/vs/notes-ios.png "iOS 模拟器中的便笺")

    输入便笺内容，然后按“保存”按钮。

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Visual Studio for Mac 入门

1. 启动 Visual Studio for Mac，然后在起始页上单击“新建项目...”创建新项目：

    ![](quickstart-experimental-images/vsmac/new-project.png "新建解决方案")

2. 在“为新项目选择一个模板”对话框中，单击“多平台”>“应用”，选择“空白窗体应用”模板，然后单击“下一步”按钮：

    ![](quickstart-experimental-images/vsmac/choose-template.png "选择模板")

3. 在“配置空白窗体应用”对话框中，将新应用命名为“便笺”，确保选中“使用 .NET Standard”单选按钮，然后单击“下一步”按钮：    

    ![](quickstart-experimental-images/vsmac/configure-app.png "配置 Forms 应用程序")

4. 在“配置新空白窗体应用”对话框中，将“解决方案”和“项目”名称保留设置为“便笺”，为项目选择合适的位置，然后单击“创建”按钮创建项目：

    ![](quickstart-experimental-images/vsmac/configure-project.png "配置 Forms 项目")

    > [!IMPORTANT]
    > 本快速入门中的 C# 和 XAML 片段要求将解决方案和项目都命名为“便笺”。 使用不同的名称会导致：将本快速入门中的代码复制到项目中时出现生成错误。

5. 在“Solution Pad”的“便笺”项目中，双击“MainPage.xaml”将其打开：

    ![](quickstart-experimental-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. 在“MainPage.xaml”中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    该代码以声明的方式定义页面的用户界面，它包含一个用于显示文本的 [`Label`、](xref:Xamarin.Forms.Label)一个用于文本输入的 [`Editor`](xref:Xamarin.Forms.Editor) 以及两个指示应用程序保存或删除文件的 [`Button`](xref:Xamarin.Forms.Button) 实例。 这两个 `Button` 实例水平放置在 [`Grid`](xref:Xamarin.Forms.Grid) 中，而 `Label`、`Editor` 和 `Grid` 垂直放置在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml** 所做的更改，然后关闭文件。

7. 在“Solution Pad”的“便笺”项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开：

    ![](quickstart-experimental-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. 在“MainPage.xaml.cs”中，删除所有模板代码并将其替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    此代码定义一个 `_fileName` 字段，该字段引用名为 `notes.txt` 的文件，而该文件将便笺数据存储在应用程序的本地应用程序数据文件夹中。 如果有文件，则在执行页构造函数时读取文件并将其显示在 [`Editor`](xref:Xamarin.Forms.Editor) 中。 按“保存”[`Button`](xref:Xamarin.Forms.Button)时执行 `OnSaveButtonClicked` 事件处理程序，将 `Editor` 的内容保存到文件中。 按“删除”`Button` 时执行 `OnDeleteButtonClicked` 事件处理程序，删除该文件（前提是它存在）并删除 `Editor` 中的任何文本。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **MainPage.xaml.cs** 所做的更改，然后关闭文件。

### <a name="building-the-quickstart"></a>生成快速入门

1. 在 Visual Studio for Mac 中，选择“生成”>“生成所有”菜单项，或按 **&#8984; + B**。 项目将生成，并在 Visual Studio for Mac 工具栏中显示一条成功消息。

      ![](quickstart-experimental-images/vsmac/build-successful.png "生成成功")

    如果发生错误，请重复前面的步骤并更正任何错误，直到成功生成项目。

2. 在“Solution Pad”中选择“Notes.iOS”项目，右键单击并选择“设为启动项目”：

      ![](quickstart-experimental-images/vsmac/set-startup-project-ios.png "将 iOS 设为启动项目")

3. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器内的应用程序：

      ![](quickstart-experimental-images/vsmac/start.png "Visual Studio for Mac 工具栏")

      ![](quickstart-experimental-images/vsmac/notes-ios.png "iOS 模拟器中的便笺")

    输入便笺内容，然后按“保存”按钮。

4. 在“Solution Pad”中选择“Notes.Droid”项目，右键单击并选择“设为启动项目”：

      ![](quickstart-experimental-images/vsmac/set-startup-project-android.png "将 Android 设为启动项目")

5. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 Android Emulator 内的应用程序：

      ![](quickstart-experimental-images/vsmac/notes-android.png "Android Emulator 中的便笺")

    输入便笺内容，然后按“保存”按钮。

::: zone-end

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
