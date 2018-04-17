---
title: 'GTK # 平台安装程序'
description: '现在具有 GTK # 平台的预览支持 Xamarin.Forms'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: a601e74cc274fd57bb2be9af3562b3a7290d7047
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="gtk-platform-setup"></a>GTK # 平台安装程序

![预览](~/media/shared/preview.png)

Xamarin.Forms 现在具有 GTK # 应用程序的预览支持。 GTK # 是允许使用 Mono 和.NET 的完全本机 GNONE 图形应用的开发 GTK + 工具包和各种 GNOME 库链接起来一个图形用户界面工具包。 本文演示如何将 GTK # 项目添加到 Xamarin.Forms 解决方案。

启动、 创建新的 Xamarin.Forms 解决方案，或使用现有 Xamarin.Forms 解决方案，例如之前, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)。

> [!NOTE]
> 虽然本文将着重将 GTK # 应用程序添加到在 VS2017 和 Visual Studio 中的 Xamarin.Forms 解决方案，适用于 Mac，它还可以执行在[MonoDevelop](http://www.monodevelop.com/)适用于 Linux。

## <a name="adding-a-gtk-app"></a>添加 GTK # 应用程序

用于 macOS 的 GTK # 和 Linux 安装的一部分[Mono](http://www.mono-project.com/download/stable/)。 用于.NET 的 GTK # 可以安装在 Windows 与[GTK # Installer](http://www.mono-project.com/download/stable/#download-win)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

按照这些说明来添加 GTK # 应用将在 Windows 桌面上上运行：

1. 在 Visual Studio 2017，右键单击解决方案名称上**解决方案资源管理器**选择**添加 > 新建项目...**.

2. 在**新项目**窗口中的，在左侧选择**Visual C#**和**Windows 经典桌面**。 在项目类型列表中，选择**类库 (.NET Framework)**，并确保**Framework**下拉列表设置为.NET Framework 4.7 最少。

3. 键入与项目的名称**GTK**扩展，例如**GameOfLife.GTK**。 单击**浏览**按钮，选择包含其他平台的文件夹的项目，然后按**选择文件夹**。 这会将 GTK 项目放在解决方案中其他项目所在的目录中。

    ![添加新的 GTK 项目](gtk-images/win/add-new-project.png "添加新的 GTK 项目")

    按**确定**按钮以创建该项目。

4. 在**解决方案资源管理器**，右键单击新的 GTK 项目并选择**管理 NuGet 包**。 选择**浏览**选项卡上，单击**包括预发行版**复选框，并搜索**Xamarin.Forms** 3.0 或更高版本。

    ![选择的 Xamarin.Forms NuGet 包](gtk-images/win/select-forms-nuget-package.png "选择的 Xamarin.Forms NuGet 包")

    选择包，然后单击**安装**按钮。

5. 现在搜索**Xamarin.Forms.Platform.GTK** 3.0 包或更高版本。

    ![选择 Xamarin.Forms.Platform.GTK NuGet 包](gtk-images/win/select-forms-platform-nuget-package.png "选择 Xamarin.Forms.Platform.GTK NuGet 包")

    选择包，然后单击**安装**按钮。

6. 在**解决方案资源管理器**，右键单击解决方案名称，然后选择**管理解决方案的 NuGet 包**。 选择**更新**选项卡和**Xamarin.Forms**包。 选择所有项目，然后将它们更新为与 GTK 项目使用相同的 Xamarin.Forms 版本。

7. 在**解决方案资源管理器**，右键单击**引用**GTK 项目中。 在**引用管理器**对话框中，选择**项目**左侧，并检查.NET 标准、 PCL 中，或共享项目旁边的复选框：

    ![引用该共享的项目](gtk-images/win/reference-shared-project.png "引用该共享的项目")

8. 在**引用管理器**对话框中，按**浏览**按钮，然后浏览到**C:\Program Files (x86)\GtkSharp\2.12\lib**文件夹，然后选择**atk sharp.dll**， **gdk sharp.dll**， **glade sharp.dll**， **glib sharp.dll**， **gtk dotnet.dll**， **gtk sharp.dll**文件。

    ![引用 GTK # 库](gtk-images/win/reference-gtk-libraries.png "引用 GTK # 库")

    按**确定**按钮以添加引用。

9. 在 GTK 项目中，重命名**Class1.cs**到**Program.cs**。

10. 在 GTK 项目中，编辑**Program.cs**文件，以便它类似于下面的代码：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 与 Xamarin.Forms、 创建应用程序窗口中，并运行的应用程序。

11. 在**解决方案资源管理器**，右键单击 GTK 项目并选择**属性**。

12. 在**属性**窗口中，选择**应用程序**选项卡并更改**输出类型**下拉到**Windows 应用程序**。

    ![将项目输出类型更改](gtk-images/win/change-project-output-type.png "更改项目输出类型")

13. 在**解决方案资源管理器**，右键单击 WPF 项目并选择**设为启动项目**。 按 f5 键以使用 Visual Studio 调试器运行程序，在 Windows 桌面上：

    ![生命周期的 GTK # 游戏](gtk-images/win/gtk-gameoflife.png "GTK # 游戏生命周期")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

按照这些说明来添加 GTK # 应用将在 Mac 桌面上运行：

1. 在 Visual Studio for Mac 中，右键单击 Xamarin.Forms 解决方案，然后选择**添加 > 添加新项目...**.

2. 在**新项目**窗口选择**其他 >.NET > Gtk # 2.0 项目**按**下一步**。

3. 键入与项目的名称**GTK**扩展，例如**GameOfLife.GTK**，按**创建**。

4. 在**解决方案 Pad**，右键单击**包 > 添加包...** GTK 的项目，并添加 Xamarin.Forms 3.0 预发行 NuGet 包或更高版本。

    ![选择的 Xamarin.Forms NuGet 包](gtk-images/mac/select-forms-nuget-package.png "选择的 Xamarin.Forms NuGet 包")

5. 在**解决方案 Pad**，右键单击**包 > 添加包...** GTK 的项目，并添加 Xamarin.Forms.Platform.GTK 3.0 预发行 NuGet 包或更高版本。

    ![选择 Xamarin.Forms.Platform.GTK NuGet 包](gtk-images/mac/select-forms-platform-nuget-package.png "选择 Xamarin.Forms.Platform.GTK NuGet 包")

6. 更新其他平台项目所使用的 GTK 项目使用相同的 Xamarin.Forms 版本。

7. 在**解决方案 Pad**，右键单击**引用 > 编辑引用...** GTK 的项目，并添加对 Xamarin.Forms 项目 （.NET 标准、 PCL 中或共享项目） 的引用。

    ![引用该共享的项目](gtk-images/mac/reference-shared-project.png "引用该共享的项目")

8. 编辑**Program.cs** GTK 项目以便其类似于下面的代码文件：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 与 Xamarin.Forms、 创建应用程序窗口中，并运行的应用程序。

9. 在**解决方案 Pad**，右键单击 GTK 项目并选择**设为启动项目**。

10. 在 Visual Studio for Mac 工具栏中，按**启动**按钮 （类似于播放按钮的三角形按钮） 以启动应用程序。

    ![生命周期的 GTK # 游戏](gtk-images/mac/gtk-gameoflife.png "GTK # 游戏生命周期")

-----

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

你可以确定哪些 Xamarin.Forms 应用程序运行从 XAML 或代码的平台。 这可以在 GTK # 上运行时更改程序特征。 在代码中，比较的值`Device.RuntimePlatform`与`Device.GTK`常量 （它等于字符串"GTK"）。 如果没有匹配项，在 GTK # 上运行应用程序。

在 XAML 中，你可以使用`OnPlatform`标记选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>应用程序图标

你可以在启动时设置的应用程序图标：

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>主题

有各种主题可用于 GTK # 中，并且它们可通过 Xamarin.Forms 应用：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>本机窗体

本机窗体允许 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页将使用的本机项目，包括 GTK # 项目。 这可以通过创建的实例来实现[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页，并将其转换为本机 GTK # 类型使用`CreateContainer`扩展方法：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

有关本机窗体的详细信息，请参阅[本机窗体](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>问题

这是预览版，因此你应不是全部是生产就绪状态。 有关当前的实现状态，请参阅[状态](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)，和有关当前的已知问题，请参阅[挂起和已知问题](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。
