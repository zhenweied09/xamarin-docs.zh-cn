---
title: 检查设备方向
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类来访问设备方向从共享代码。
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 620404a217b2e8a31192ae6613dcec023ac366cd
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995636"
---
# <a name="checking-device-orientation"></a>检查设备方向

本文将指导你使用[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)检查设备方向从每个平台上使用本机 Api 的共享代码。 本演练基于现有`DeviceOrientation`阿里 Özgür 的插件。 请参阅[GitHub 存储库](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)有关详细信息。

- **[创建接口](#Creating_the_Interface)** &ndash;了解如何在接口中共享代码创建。
- **[iOS 实现](#iOS_Implementation)** &ndash;了解如何在适用于 iOS 的本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash;了解如何适用于 Android 的本机代码中实现的接口。
- **[UWP 实现](#WindowsImplementation)** &ndash;了解如何为通用 Windows 平台 (UWP) 中的本机代码实现的接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`来调入本机实现从共享代码。

应用程序使用`DependencyService`将具有以下结构：

![](device-orientation-images/orientation-diagram.png "DependencyService 应用程序结构")

> [!NOTE]
> 可以检测是否在设备处于纵向或横向方向中的共享代码，如下所示在 [设备 Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation)。 在本文中所述的方法使用本机功能来获取有关方向，包括设备在正面朝下的详细信息。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，表达你打算实现的功能的共享代码中创建的接口。 对于此示例，该接口包含一个方法：

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

针对此接口在共享代码中编写代码将允许 Xamarin.Forms 应用程序访问每个平台上的设备方向 Api。

> [!NOTE]
> 实现接口的类必须具有无参数的构造函数，以使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

必须在每个特定于平台的应用程序项目中实现该接口。 请注意，类具有无参数构造函数，以便`DependencyService`可以创建新实例：

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

最后，添加以下`[assembly]`包括任何所需属性该类上方 （和任何已定义的命名空间之外），`using`语句：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

此属性的实现作为注册类`IDeviceOrientation`接口，这意味着`DependencyService.Get<IDeviceOrientation>`可用于共享代码中创建它的一个实例。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

下面的代码实现`IDeviceOrientation`在 Android 上：

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

添加以下`[assembly]`包括任何所需属性该类上方 （和任何已定义的命名空间之外），`using`语句：

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

此属性的实现作为注册类`IDeviceOrientaiton`接口，这意味着`DependencyService.Get<IDeviceOrientation>`可在共享的代码可以创建它的一个实例。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台实现

下面的代码实现`IDeviceOrientation`通用 Windows 平台上的接口：

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

添加`[assembly]`包括任何所需属性该类上方 （和任何已定义的命名空间之外），`using`语句：

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

此属性的实现作为注册类`DeviceOrientationImplementation`接口，这意味着`DependencyService.Get<IDeviceOrientation>`可在共享的代码可以创建它的一个实例。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

现在，我们可以编写并测试访问的共享的代码`IDeviceOrientation`接口。 此简单的页面包括一个按钮，更新其自己基于设备方向的文本。 它使用`DependencyService`若要获取的实例`IDeviceOrientation`接口&ndash;此实例将在运行时是具有完全访问权限的本机 SDK 的特定于平台的实现：

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

在 iOS、 Android 或 Windows 平台上运行此应用程序并按下按钮会导致在更新设备的方向的按钮的文本。

![](device-orientation-images/orientation.png "设备方向示例")


## <a name="related-links"></a>相关链接

- [使用 DependencyService （示例）](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService （示例）](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
