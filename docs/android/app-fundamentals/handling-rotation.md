---
title: 处理旋转
description: 本主题介绍如何处理在 Xamarin.Android 设备方向更改。 它介绍如何使用 Android 资源系统自动加载资源的特定设备方向以及如何以编程方式处理方向更改。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1cdc7928c45b99cdd8c8149b3ae9b06e790deeca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768891"
---
# <a name="handling-rotation"></a>处理旋转

_本主题介绍如何处理在 Xamarin.Android 设备方向更改。它介绍如何使用 Android 资源系统自动加载资源的特定设备方向以及如何以编程方式处理方向更改。_


## <a name="overview"></a>概述

由于移动设备轻松地旋转，内置旋转是移动操作系统中的标准功能。 是否在 XML 中或以编程方式在代码中以声明方式创建的用户界面，android 提供用于处理应用程序中的旋转复杂的框架。 当自动处理旋转的设备上的声明性的布局更改时，应用程序可以受益于与 Android 资源系统的紧密集成。 对于以编程方式布局，必须手动处理更改。 这使得更精细的控制在运行时，但代价更多的工作是开发人员。 应用程序还可以选择以选择退出活动重新启动并手动控制的方向更改。

本指南将检查以下有针对性的主题：

-   **声明性布局旋转**&ndash;如何使用 Android 资源系统生成方向感知应用程序，包括如何加载布局和 drawables 为特定的方向。

-   **以编程方式布局旋转**&ndash;如何以编程方式添加控件，以及如何手动处理方向更改。


## <a name="handling-rotation-declaratively-with-layouts"></a>处理以声明方式与布局的旋转

通过遵循命名约定的文件夹中包含文件，Android 自动加载相应的文件更改方向时。
这包括对支持：

-   *布局资源*&ndash;指定哪些布局文件放大为每个方向。

-   *可绘制资源*&ndash;指定哪些 drawables 加载为每个方向。


### <a name="layout-resources"></a>布局资源

默认情况下，Android XML (AXML) 文件包括在**资源/布局**文件夹用于呈现视图的活动。 此文件夹的资源用于横向和纵向如果没有其他布局资源专门为环境提供。 请考虑创建的默认项目模板的项目结构：

[![默认项目模板结构](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此项目创建单个**main.axml**文件中**资源/布局**文件夹。 当活动的`OnCreate`方法调用，因此它放大中定义的视图**main.axml，** 其声明一个按钮，如下面的 XML 中所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

如果将设备旋转为横向方向，活动`OnCreate`再次调用方法和相同**main.axml**文件被放大，如下面的屏幕截图中所示：

[![但在相同屏幕横向](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>方向特定布局

除了布局文件夹 (其中纵向默认设置，也可以显式命名*布局端口*通过包括名为的文件夹`layout-land`)，应用程序可以定义它而无需任何代码的环境中需要的视图更改。

假设**main.axml**文件包含以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

如果文件夹名为包含附加的布局土地**main.axml**文件添加到项目中，inflating 在横向布局现在将导致加载新添加的 Android **main.axml。** 请考虑的横向版本**main.axml**包含下面的代码文件 (为简单起见，此 XML 类似于默认纵向版本的代码，但使用中的不同字符串`TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

运行此代码和旋转的设备纵向改为横向演示新的 XML 加载，如下所示：

[![纵向和横向打印纵向模式的屏幕截图](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>可绘制资源

轮换，Android 将可绘制资源同样地布局资源。 在这种情况下，系统获取从 drawables**可资源/绘制**和**资源/可绘制土地**文件夹，分别。

例如，项目包含名为 Monkey.png 中的图像**可资源/绘制**文件夹中，可绘制引用的位置从`ImageView`如下的 XML 中：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

让我们进一步假设的不同版本的**Monkey.png**下包括**资源/可绘制土地**。 就像与布局文件，该设备时循环使用，可绘制更改给定的方向，如下所示：

[![不同版本的 Monkey.png 纵向和横向模式中所示](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>以编程方式处理旋转

有时，我们在代码中定义布局。 这可能由于各种原因，包括技术限制、 开发人员首选项，等等。当我们以编程方式添加控件时，必须手动会考虑设备方向，我们使用 XML 资源时，会自动处理应用程序。


### <a name="adding-controls-in-code"></a>在代码中添加控件

若要以编程方式添加控件，应用程序需要执行以下步骤：

-  创建布局。
-  设置布局参数。
-  创建控件。
-  设置控件布局参数。
-  将控件添加到布局。
-  设置为内容视图布局。

例如，考虑包含单个用户界面`TextView`控件添加到`RelativeLayout`，如下面的代码中所示。

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码创建的实例`RelativeLayout`类并设置其`LayoutParameters`属性。 `LayoutParams`类是 Android 的方式封装如何控件都将放置在可重用的方式。 创建布局的实例后，可以创建并向其添加控件。 控件还具有`LayoutParameters`，如`TextView`在此示例中。 后`TextView`创建后，将其添加到`RelativeLayout`和设置`RelativeLayout`中应用程序显示的内容视图结果作为`TextView`如所示：

[![纵向和横向模式中所示的递增计数器按钮](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>在代码中的检测方向

如果应用程序尝试加载不同的用户界面为每个方向时`OnCreate`称为 （这将会发生在旋转设备每次），它必须检测方向，，然后加载所需的用户界面代码。 Android 有一个名为类`WindowManager`，这可以用于确定通过当前设备旋转`WindowManager.DefaultDisplay.Rotation`属性，如下所示：

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码将设置`TextView`若要能定位的 100 像素，向左的屏幕中，自动对进行动画处理到新的布局中，当旋转为横向，如下所示：

[![视图状态保留在纵向与横向模式](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>阻止活动重新启动

除了处理中的所有内容`OnCreate`，应用程序还可以从通过设置更改方向时重新启动会阻止某个活动`ConfigurationChanges`中`ActivityAttribute`，如下所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
现在时将设备旋转，该活动不会启动。 若要手动在这种情况下处理方向更改，活动可以重写`OnConfigurationChanged`方法，并确定从方向`Configuration`传入，如下面的活动的新实现中所示的对象：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

此处`TextView's`的横向和纵向初始化布局参数。 类变量包含的参数，沿`TextView`本身，因为该活动将不会重新创建方向更改时。 该代码仍使用`surfaceOrientartion`中`OnCreate`设置的初始布局`TextView`。 在此之后，`OnConfigurationChanged`处理所有后续的布局更改。

当我们运行应用程序时，Android 将加载用户界面会发生变化，如设备旋转发生，而不重新启动该活动。


## <a name="preventing-activity-restart-for-declarative-layouts"></a>阻止针对声明性的布局的活动重新启动

如果我们在 XML 中定义布局，则还可以阻止活动重新启动引起的设备旋转。 例如，我们可以使用此方法，如果我们想要防止活动重新启动 (出于性能原因，可能是)，而且我们无需加载为不同的方向的新资源。

若要执行此操作，我们请执行的相同过程，我们使用的编程的布局。 只需设置`ConfigurationChanges`中`ActivityAttribute`，正如我们做`CodeLayoutActivity`更早版本。 需要位于运行方向更改可以再次实现中的任何代码`OnConfigurationChanged`方法。


## <a name="maintaining-state-during-orientation-changes"></a>在方向更改过程中保持状态

以声明方式或以编程方式处理旋转，是否所有的 Android 应用程序应实现的相同技术用于管理状态时更改设备方向。 管理状态非常重要，因为转动 Android 设备时，系统重新启动正在运行的活动。 Android 这样做是为了方便地加载备用资源，如布局和 drawables 专为特定的方向。 当重新启动时，活动将丢失任何暂时性状态，它可能会将存储在本地类变量。 因此，如果某个活动是依赖于状态，它必须持久保存其状态应用程序级别。 应用程序需要处理保存和还原它想要跨方向更改保留任何应用程序状态。

有关 Android 中的保留状态的详细信息，请参阅[活动的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。


## <a name="summary"></a>总结

本文介绍如何使用 Android 的内置功能来处理旋转。 首先，它还介绍了如何使用 Android 资源系统创建感知应用程序的方向。 然后，它提供如何在代码中添加控件，以及如何手动处理方向更改。



## <a name="related-links"></a>相关链接

- [旋转演示 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [处理运行时更改](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速屏幕方向更改](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
