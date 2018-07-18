---
title: 初始屏幕
description: Android 应用程序需要向一些时间才能启动，尤其是应用程序第一次启动时在设备上。 初始屏幕可能会显示开始向上向用户或以指示品牌进度。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/14/2018
ms.openlocfilehash: 6200a04bb4d82174d36a48beab7c63709ac39187
ms.sourcegitcommit: c5bb1045b2f4607dafe3101ad1ea6ade23e44342
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34153192"
---
# <a name="splash-screen"></a>初始屏幕

_Android 应用程序需要向一些时间才能启动，尤其是应用程序第一次启动时在设备上。初始屏幕可能会显示开始向上向用户或以指示品牌进度。_


## <a name="overview"></a>概述

Android 应用程序需要花费一些时间来启动，特别是在首次运行该应用程序在设备上 (有时这称为_冷启动_)。 可能会显示初始屏幕启动进度给用户，或者根本不显示品牌信息，以便标识，并将提升应用程序。

本指南讨论在 Android 应用程序中实现的初始屏幕的一项技术。 其中包括以下步骤：

1.  创建初始屏幕的可绘制资源。

2.  定义将显示可绘制的资源的新主题。

3.  将新活动添加到的应用程序将用作由在上一步中创建的主题定义初始屏幕。

[![示例 Xamarin 徽标初始屏幕跟应用程序屏幕](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>要求

本指南假定该应用程序面向 Android API 级别 15 (Android 4.0.3) 或更高版本。 应用程序还必须具有**Xamarin.Android.Support.v4**和**Xamarin.Android.Support.v7.AppCompat** NuGet 包添加到项目。

在中可能找到的所有代码和在本指南中的 XML[初始屏幕](https://developer.xamarin.com/samples/monodroid/SplashScreen)用于本指南的示例项目。


## <a name="implementing-a-splash-screen"></a>实现的初始屏幕

呈现和显示初始屏幕的最快方法是创建一个自定义主题，并将其应用到活动显示初始屏幕。 当呈现活动时，它将加载主题和适用于活动的背景 （主题引用） 的可绘制资源。 此方法无需创建布局文件。

作为活动显示经过品牌打造实现初始屏幕可绘制，执行任何初始化，并启动任何任务。 一旦应用程序具有引导，初始屏幕活动启动主活动，并从应用程序后堆栈删除本身。


### <a name="creating-a-drawable-for-the-splash-screen"></a>创建可绘制的初始屏幕

初始屏幕将显示在初始屏幕活动后台可绘制 XML。 它是使用图像的位图化图像 （例如 JPG 或 PNG） 以显示所必需的。

在本指南中，我们将使用[层列表](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)到中心再在应用程序初始屏幕图像。 下面的代码段演示了`drawable`资源使用`layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

这`layer-list`将中心初始屏幕图像**splash.png**指定背景上`@color/splash_background`资源。
将此文件中的放置**可资源/绘制**文件夹 (例如， **Resources/drawable/splash_screen.xml**)。

创建初始屏幕可绘制后下, 一步是创建初始屏幕的主题。


### <a name="implementing-a-theme"></a>实现一个主题

若要创建初始屏幕活动的自定义主题，编辑 （或添加） 文件**values/styles.xml**并创建新`style`的初始屏幕元素。 示例**values/style.xml**文件与如下所示`style`名为**MyTheme.Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme.Splash**是非常 spartan&ndash;声明窗口背景，显式中的标题栏删除从窗口中，并声明它是全屏幕。 如果你想要创建模拟你的应用程序的 UI 之前活动放大的第一个布局, 的初始屏幕，则可以使用`windowContentOverlay`而非`windowBackground`样式定义中。 在这种情况下，还必须修改**splash_screen.xml**可绘制，使其显示你的 UI 的仿真。


### <a name="create-a-splash-activity"></a>创建初始活动

现在，我们需要使用 Android 以启动我们初始图像并执行的任何启动任务的新活动。 下面的代码演示了完整的初始屏幕实现：

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` 显式使用在中创建上一节中，重写应用程序的默认主题的主题。
无需加载的布局`OnCreate`如主题声明可绘制为的背景。

务必要设置`NoHistory=true`属性，以便从后的堆栈中移除活动。 若要防止后退按钮取消启动过程，你也可以替代`OnBackPressed`并使之不执行任何操作：

```csharp
public override void OnBackPressed() { }
```

以异步方式在执行启动工作`OnResume`。 这是必需的以便启动工作不会减慢或延迟的启动屏幕的外观。 完成工作后，`SplashActivity`将启动`MainActivity`和用户可能会开始与应用交互。

此新`SplashActivity`通过设置设置为应用程序的启动器活动`MainLauncher`属性设为`true`。 因为`SplashActivity`现在启动器活动中，你必须编辑`MainActivity.cs`，并删除`MainLauncher`属性从`MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>横向模式

在前面的步骤中实现的初始屏幕将正确显示，在纵向与横向模式下。 但是，在某些情况下是所需的纵向和横向模式的单独的初始屏幕 （例如，如果初始图像是全屏幕）。

若要添加横向模式的初始屏幕，请使用以下步骤：

1. 在**可资源/绘制**文件夹中，添加你想要使用的初始屏幕图像的布局版本。 在此示例中， **splash_logo_land.png**是在上面的示例 （它使用白色信而不是蓝色） 中使用的徽标的横向版本。

2. 在**可资源/绘制**文件夹中，创建的布局版本`layer-list`可绘制的前面定义 (例如， **splash_screen_land.xml**)。 在此文件中，设置初始屏幕图像的布局版本的位图路径。 在下面的示例中， **splash_screen_land.xml**使用**splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3.  创建**上关于领土资源/值**文件夹，如果它尚不存在。

4.  将文件添加**colors.xml**和**style.xml**到**值上关于领土**(这些可复制并修改现有**values/colors.xml**和**values/style.xml**文件)。

5.  修改**值-上关于领土/style.xml** ，使其使用的可绘制的横向版本`windowBackground`。 在此示例中， **splash_screen_land.xml**使用：

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6.  修改**值-上关于领土/colors.xml**配置你想要使用的初始屏幕的布局版的颜色。 在此示例中，初始背景色更改为蓝色横向模式：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7.  生成并再次运行该应用。 旋转设备以横向模式时仍显示初始屏幕。 初始屏幕更改为横向版本：

    [![为横向模式的初始屏幕的旋转](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


请注意，使用横向模式初始屏幕不始终提供无缝体验。 默认情况下，Android 启动纵向模式下应用，并转换它以横向模式，即使该设备已在横向模式中。 结果是，如果设备处于横向模式时，启动应用程序，该设备将简要显示纵向初始屏幕，然后进行动画处理到横向初始屏幕纵向从旋转。 遗憾的是，发生此初始纵向横向转换，即使`ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape`初始活动的标志中指定。 若要解决此限制的最佳方式是在纵向与横向模式下正确创建单个初始屏幕图像呈现。


## <a name="summary"></a>总结

本指南讨论一种方法在 Xamarin.Android 应用程序中; 实现的初始屏幕也就是说，将自定义主题应用于启动活动。


## <a name="related-links"></a>相关链接

- [初始屏幕 （示例）](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [层列表 Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ 材料设计模式的启动屏幕](https://www.google.com/design/spec/patterns/launch-screens.html)
