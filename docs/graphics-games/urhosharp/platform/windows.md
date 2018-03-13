---
title: "UrhoSharp Windows 支持"
description: "Windows 特定的安装程序和适用于 UrhoSharp 的功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 0deefe0e00ec96a21317bfa2e8bf4894d5deae47
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="urhosharp-windows-support"></a>UrhoSharp Windows 支持

_Windows 特定的安装程序和功能_

尽管 Urho 是一个可移植类库，并且允许相同的 API，用于跨各种平台的游戏的逻辑，仍需要初始化 Urho，在您平台特定的驱动程序，并在某些情况下，你将想要利用平台特定的功能.

在下面的页面中，假定`MyGame`是的一个子类`Application`类。

**支持的体系结构：**仅 64 位 Windows。

你可以看到完整的示例显示如何使用在此我们[示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

# <a name="standalone-project"></a>独立项目

### <a name="creating-a-project"></a>创建项目

创建控制台项目、 引用 Urho NuGet 和确保找到资产 （这些目录包含的数据目录）。

### <a name="configuring-and-launching-urho"></a>配置和启动 Urho

若要启动你的应用程序，请执行此操作：

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```
### <a name="example"></a>示例

[完整示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>与 WPF 集成

### <a name="creating-a-project"></a>创建项目

创建 WPF 项目、 引用 Urho NuGet 和确保找到资产 （这些目录包含的数据目录）。

### <a name="configuring-and-launching-urho-from-wpf"></a>配置和启动从 WPF Urho

创建一个子类`Window`和配置你的资产如下：

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>示例

[完整示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>与 UWP 集成

### <a name="creating-a-project"></a>创建项目

创建 UWP 项目引用 Urho NuGet，然后确保找到资产 （这些目录包含的数据目录）。

### <a name="configuring-and-launching-urho-from-uwp"></a>配置和启动从 UWP Urho

创建一个子类`Window`和配置你的资产如下：

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>示例

[完整示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>与 Windows.Forms 集成

### <a name="creating-a-project"></a>创建项目

创建 Windows.Forms 项目、 引用 Urho NuGet 和确保找到资产 （这些目录包含的数据目录）。

### <a name="configuring-and-launching-urho-from-windowsforms"></a>配置和启动从 Windows.Forms Urho

从你的窗体启动 Urho，请参阅[完整的示例](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)

