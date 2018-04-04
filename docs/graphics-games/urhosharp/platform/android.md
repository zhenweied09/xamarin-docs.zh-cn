---
title: UrhoSharp Android 支持
description: Android 特定的安装程序并 UrhoSharp 的功能。
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 2d0eb183817a84b982149736fa24f9ec56c1753e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="urhosharp-android-support"></a>UrhoSharp Android 支持

_Android 特定安装程序和功能_

尽管 Urho 是一个可移植类库，并且允许相同的 API，用于跨各种平台的游戏的逻辑，仍需要初始化 Urho，在您平台特定的驱动程序，并在某些情况下，你将想要利用平台特定的功能.

在下面的页面中，假定`MyGame`是的一个子类`Application`类。

## <a name="architectures"></a>体系结构

**支持的体系结构**: x86、 armeabi，armeabi v7a

## <a name="create-a-project"></a>创建项目

创建 Android 项目，并添加 UrhoSharp NuGet 包。

添加数据包含你资产的**资产**目录并确保所有文件都具有**AndroidAsset**作为**生成操作**。

![项目设置](android-images/image-3.png "包含资产的资产目录中添加数据")

## <a name="configure-and-launching-urho"></a>配置和启动 Urho

添加 using 语句`Urho`和`Urho.Android`命名空间，并将这段代码用于初始化 Urho，以及启动你的应用程序。

运行一个游戏，在 MyGame 类中实现的最简单方法是调用

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

这将作为内容与游戏打开全屏活动。

## <a name="custom-embedding-of-urho"></a>自定义嵌入的 Urho

你可以或者使其 Urho 接管整个应用程序屏幕中，并且若要使用它为你的应用程序的一个组件，可以创建`SurfaceView`通过：

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

你还需要几个事件从你将活动转发到 UrhoSharp，例如：

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

必须为执行同样： `OnResume`， `OnPause`， `OnLowMemory`， `OnDestroy`，`DispatchKeyEvent`和`OnWindowFocusChanged`。

下面的示例演示将启动游戏的典型活动：

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

