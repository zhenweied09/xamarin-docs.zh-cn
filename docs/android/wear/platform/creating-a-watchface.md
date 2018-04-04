---
title: 创建手表表盘
description: 本指南说明如何实现自定义监视表面服务的 Android 损耗。 用于构建去除数字监视表面服务后, 跟更多代码以创建模拟样式手表表盘下提供了分步说明。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c02755cc3ff5b46a5a97b6c14185794d8ad538d8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-watch-face"></a>创建手表表盘

_本指南说明如何实现自定义监视表面服务的 Android 损耗。用于构建去除数字监视表面服务后, 跟更多代码以创建模拟样式手表表盘下提供了分步说明。_

## <a name="overview"></a>概述 

在本演练中，创建一个基本的监视表面服务来演示创建自定义的 Android 带手表表盘的 essentials。 初始监视表面服务显示显示的当前时间以小时和分钟的简单数字监视： 

[![数字手表表盘](creating-a-watchface-images/01-initial-face.png "示例的初始数字手表表盘的屏幕截图")](creating-a-watchface-images/01-initial-face.png#lightbox)

开发和测试此数字手表表盘后，会将它升级到更复杂，这与三个之手的模拟手表表盘添加更多代码： 

[![模拟手表表盘](creating-a-watchface-images/02-example-watchface.png "的最终模拟手表表盘示例屏幕截图")](creating-a-watchface-images/02-example-watchface.png#lightbox)

观看表面服务捆绑在一起，但作为磨损应用程序的一部分安装。 在以下示例中，`MainActivity`包含没有什么比磨损应用模板中的代码，以便可以打包监视表面服务，并将其部署到智能监视应用程序的一部分。 实际上，此应用程序将充当纯粹获取监视表面服务加载到磨损设备 （或仿真程序） 的工具用于调试和测试。 

## <a name="requirements"></a>要求

若要实现监视表面服务，需要满足以下条件：

-   Android 5.0 （API 级别 21） 或更高版本上磨损设备或仿真程序。

-   [Xamarin Android 磨损支持库](https://www.nuget.org/packages/Xamarin.Android.Wear)必须添加到 Xamarin.Android 项目。 

尽管 Android 5.0 是最低的 API 级别实现监视表面服务，Android 5.1 或更高版本建议。 Android 磨损运行 Android 5.1 (API 22) 设备或更高版本允许磨损应用控制什么显示在屏幕上，在低功耗设备时*环境*模式。 当设备离开低功耗*环境*模式时，它是在*交互式*模式。 有关这些模式的详细信息，请参阅[保持你的应用程序可见](https://developer.android.com/training/wearables/apps/always-on.html)。 


## <a name="start-an-app-project"></a>启动应用程序项目

创建新 Android 磨损项目调用**WatchFace** (有关创建新的 Xamarin.Android 项目的详细信息，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新建项目对话框](creating-a-watchface-images/03-wear-project-vs-sml.png "选择磨损的应用程序中新项目对话框")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新建项目对话框](creating-a-watchface-images/03-wear-project-xs-sml.png "选择磨损的应用程序中新项目对话框")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


将包名称设置为`com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![包名称设置](creating-a-watchface-images/04-package-name-vs.png "将包名称设置为 com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![包名称设置](creating-a-watchface-images/04-package-name-xs.png "将包名称设置为 com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

此外，向下滚动并启用**INTERNET**和**WAKE_LOCK**权限： 

[![所需的权限](creating-a-watchface-images/05-required-permissions-vs.png "启用 INTERNET 和 WAKE_LOCK 权限")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

将最低 Android 版本设置为**Android 5.1 （API 级别 22）**。 此外，可以启用**Internet**和**WakeLock**权限：

[![所需的权限](creating-a-watchface-images/05-required-permissions-xs.png "启用 Internet 和 WakeLock 权限")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下来，下载[preview.png](creating-a-watchface-images/preview.png) &ndash;这将添加到**drawables**稍后在本演练的文件夹。


## <a name="add-the-xamarin-android-wear-package"></a>添加 Xamarin Android 磨损包

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

启动 NuGet 包管理器 (在 Visual Studio 中，右键单击**引用**中**解决方案资源管理器**和选择**管理 NuGet 包...**).项目更新为最新稳定版本**Xamarin.Android.Wear**: 

[![NuGet 包管理器添加](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "添加 Xamarin.Android.Wear 包")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下来，如果**Xamarin.Android.Support.v13**是安装，将其卸载：

[![NuGet 包管理器删除](creating-a-watchface-images/07-uninstall-v13-sml.png "删除 Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

启动 NuGet 包管理器 (在适用于 Mac 的 Visual Studio，右键单击**包**中**解决方案窗格**和选择**添加包...**).项目更新为最新稳定版本**Xamarin.Android.Wear**: 

[![NuGet 包管理器添加](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "添加 Xamarin.Android.Wear 包")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


生成并在磨损设备或仿真器上运行应用程序 (有关如何执行此操作的详细信息，请参阅[入门](~/android/wear/get-started/index.md)指南)。 你应看到以下应用程序屏幕磨损设备上：

[![应用屏幕快照](creating-a-watchface-images/08-app-screen.png "磨损设备上的应用程序屏幕")](creating-a-watchface-images/08-app-screen.png#lightbox)

此时，基本磨损应用程序没有监视表面功能因为尚未提供监视表面服务实现。 将下一步添加此服务。 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 磨损实现观看通过表面`CanvasWatchFaceService`类。 `CanvasWatchFaceService` 派生自`WatchFaceService`，其自身派生自`WallpaperService`下图中所示： 

[![继承关系图](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 继承关系图")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包括嵌套`CanvasWatchFaceService.Engine`; 它实例化`CanvasWatchFaceService.Engine`执行的绘制手表表盘的实际工作的对象。 `CanvasWatchFaceService.Engine` 派生自`WallpaperService.Engine`上图中所示。 

未显示此图是`Canvas`，`CanvasWatchFaceService`用于绘制手表表盘&ndash;这`Canvas`通过传入`OnDraw`方法如下所述。 

在以下部分中，将通过执行以下步骤创建自定义监视表面服务： 

1.  定义一个名为类`MyWatchFaceService`、 派生自`CanvasWatchFaceService`。 

2.  在`MyWatchFaceService`，创建一个名为的嵌套的类`MyWatchFaceEngine`、 派生自`CanvasWatchFaceService.Engine`。 

3.  在`MyWatchFaceService`，实现`CreateEngine`方法实例化`MyWatchFaceEngine`并将其返回。

4.  在`MyWatchFaceEngine`，实现`OnCreate`方法以创建监视表面样式并执行任何其他初始化任务。 

5.  实现`OnDraw`方法`MyWatchFaceEngine`。 每当手表表盘需要重绘就会调用此方法 (即*失效*)。 `OnDraw` 是绘制 （和重绘） 监视表面元素，如小时、 分钟和第二个之手的方法。 

6.  实现`OnTimeTick`方法`MyWatchFaceEngine`。 
    `OnTimeTick` 每个分钟 （在环境和交互模式下） 或日期/时间已更改时至少一次调用。 

有关详细信息`CanvasWatchFaceService`，请参阅 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 文档。
同样， [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html)手表表盘实际实现进行说明。


### <a name="add-the-canvaswatchfaceservice"></a>添加 CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

添加新的文件称为**MyWatchFaceService.cs** (在 Visual Studio 中，右键单击**WatchFace**中**解决方案资源管理器**，单击**添加 > 新建项...**，然后选择**类**)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

添加新的文件称为**MyWatchFaceService.cs** (在适用于 Mac 的 Visual Studio，右键单击**WatchFace**项目中，单击**添加 > 新文件...**，然后选择**空类**)。 

-----

此文件的内容替换为以下代码： 

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (派生自`CanvasWatchFaceService`) 是手表表盘的"主程序"。 `MyWatchFaceService` 实现只有一个方法， `OnCreateEngine`，其实例化并返回`MyWatchFaceEngine`对象 (`MyWatchFaceEngine`派生自`CanvasWatchFaceService.Engine`)。 实例化`MyWatchFaceEngine`对象必须返回作为`WallpaperService.Engine`。 封装`MyWatchFaceService`对象传递到构造函数。 

`MyWatchFaceEngine` 是实际监视表面实现&ndash;它包含绘制手表表盘的代码。 它还可以处理系统事件，例如屏幕更改 （环境/交互模式下，屏幕关闭，等等）。 

 
### <a name="implement-the-engine-oncreate-method"></a>实现引擎 OnCreate 方法

`OnCreate`方法初始化手表表盘。 以下字段添加到`MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

这`Paint`对象将用于在手表表盘上绘制的当前时间。 接下来，添加以下方法`MyWatchFaceEngine`: 

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` 调用很快后`MyWatchFaceEngine`已启动。 它会设置`WatchFaceStyle`（它可以控制磨损设备与用户交互的方式） 并且实例化`Paint`将用来显示时间的对象。 

调用`SetWatchFaceStyle`执行下列任务：

1.  集*扫视模式*到`PeekModeShort`，这将导致显示为小"扫视"卡上显示的通知。 

2.  将后台可见性设置为`Interruptive`，这将导致显示只是暂时如果它表示中断通知扫视卡的背景。

3.  禁用从手表表盘上进行绘制，以使自定义手表表盘可以改为显示时间的默认系统 UI 时间。

有关这些及其他监视表面样式选项的详细信息，请参阅 Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 文档。

后`SetWatchFaceStyle`完成后，`OnCreate`实例化`Paint`对象 (`hoursPaint`)，并将其颜色设置为白色，其文本大小以适合 48 像素 ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必须指定以像素为单位)。 


### <a name="implement-the-engine-ondraw-method"></a>实现引擎 OnDraw 方法

`OnDraw`方法可能是最重要`CanvasWatchFaceService.Engine`方法&ndash;它是方法，实际绘制观看表面元素，如数字和时钟表面指针。 在下面的示例中，它在手表表盘上绘制时间字符串。
添加以下方法`MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

当调用 Android `OnDraw`，它将传递`Canvas`实例和可以在其中绘制面临的界限。 在上面的代码示例中，`DateTime`用于计算的当前时间以小时和分钟 （采用 12 小时格式）。 使用在画布上绘制生成的时间字符串`Canvas.DrawText`方法。 字符串将显示 70 像素通过从左边的缘和 80 像素下从上边缘。 

有关详细信息`OnDraw`方法，请参阅 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) API 文档。


### <a name="implement-the-engine-ontimetick-method"></a>实现引擎 OnTimeTick 方法

Android 定期调用`OnTimeTick`方法来更新的手表表盘所示的时间。 它在每分钟 （在环境和交互模式下） 的至少一次或日期/时间或时区已发生更改时调用。 添加以下方法`MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

此实现的`OnTimeTick`只需调用`Invalidate`。 `Invalidate`方法计划`OnDraw`重绘手表表盘。 

有关详细信息`OnTimeTick`方法，请参阅 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 文档。

 
## <a name="register-the-canvaswatchfaceservice"></a>注册 CanvasWatchFaceService

`MyWatchFaceService` 必须在注册**AndroidManifest.xml**的关联磨损应用程序。 若要执行此操作，将添加到下面的 XML`<application>`部分： 

```xml
<service 
    android:name="watchface.MyWatchFaceService" 
    android:label="Xamarin Sample" 
    android:allowEmbedded="true" 
    android:taskAffinity="" 
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data 
        android:name="android.service.wallpaper" 
        android:resource="@xml/watch_face" />
    <meta-data 
        android:name="com.google.android.wearable.watchface.preview" 
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

此 XML 将执行以下操作：

1.  集`android.permission.BIND_WALLPAPER`权限。 凭借此权限，要更改设备上的系统墙纸监视表面服务权限。 请注意，此权限必须设置在`<service>`部分而不是在外部`<application>`部分。 

2.  定义`watch_face`资源。 此资源是一个简短的 XML 文件，声明`wallpaper`（在下一节中将创建此文件） 的资源。 

3.  声明调用的可绘制映像`preview`，将显示的监视选取器选择屏幕。 

4.  包括`intent-filter`以便 Android 知道`MyWatchFaceSevice`将会显示手表表盘。 

完成基本的代码`WatchFace`示例。 下一步是添加所需的资源。

 
## <a name="add-resource-files"></a>添加资源文件

您可以运行监视服务之前，必须添加**watch_face**资源和预览图像。 首先，创建一个新的 XML 文件在**Resources/xml/watch_face.xml**并将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

将此文件的生成操作设置为**AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![生成操作](creating-a-watchface-images/10-android-resource-vs.png "组生成到 AndroidResource 的操作")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![生成操作](creating-a-watchface-images/10-android-resource-xs.png "组生成到 AndroidResource 的操作")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此资源文件定义一个简单`wallpaper`用于手表表盘的元素。 

如果尚未这样做，下载[preview.png](creating-a-watchface-images/preview.png)。 安装在**Resources/drawable/preview.png**。 请务必将添加到此文件`WatchFace`项目。 向磨损设备上监视表面选取器中的用户显示此预览图像。 若要创建你自己的手表表盘的预览图像，您可以在运行时采取手表表盘屏幕的快照。 (有关详细信息从磨损设备获取屏幕快照，请参阅[截屏](~/android/wear/deploy-test/debug-on-device.md#screenshots))。 


## <a name="try-it"></a>尝试一下！

生成并部署到磨损设备的应用程序。 你应看到显示像以前那样磨损应用程序屏幕。 执行以下操作来启用新的手表表盘： 

1.  轻扫到右侧，直到你看到监视屏幕的背景。 

2.  接触也可在屏幕的背景上任何位置保存两秒钟。

3.  从左到右可浏览的各种监视表面的轻扫。 

4.  选择**Xamarin 示例**观看表面 （显示在右侧）： 

    [![Watchface 选取器](creating-a-watchface-images/11-watchface-picker.png "轻扫以找到 Xamarin 示例手表表盘")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  点击**Xamarin 示例**观看表面以将其选中。 

这将更改手表表盘的磨损设备使用到目前为止实现的自定义监视表面服务： 

[![数字手表表盘](creating-a-watchface-images/12-digital-watchface.png "磨损设备上运行的自定义数字监视")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

这是相对较粗糙手表表盘，因为应用程序实现是因此最小 (例如，它不包括监视表面背景并且不调用`Paint`消除锯齿方法，以改善外观)。 但是，它实现所需创建自定义手表表盘单纯的功能。 

在下一步的部分中，此手表表盘将升级到更复杂实现。 

 
## <a name="upgrading-the-watch-face"></a>升级手表表盘

本演练的其余部分中`MyWatchFaceService`升级以显示模拟样式手表表盘并且它扩展以支持更多的功能。 将添加以下功能，以创建升级的手表表盘： 

1.  表示模拟的小时、 分钟、 与第二个之手的时间。

2.  在可见性的更改作出反应。

3.  对环境的模式和交互模式之间更改做出响应。 

4.  读取基础磨损设备的属性。

5.  自动更新时区更改发生的时间。 

在实现下面的代码更改之前, 下载[drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)，将其解压缩，并将移到解压缩的.png 文件**可资源/绘制**(覆盖以前**preview.png**). 添加到新的.png 文件`WatchFace`项目。


### <a name="update-engine-features"></a>更新引擎功能

下一步是升级**MyWatchFaceService.cs**到绘制模拟手表表盘并支持新功能的实现。 内容替换**MyWatchFaceService.cs**与中的监视表面代码的模拟版本[MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (你可剪切并粘贴到现有的此源**MyWatchFaceService.cs**)。 

此版本的**MyWatchFaceService.cs**将更多代码添加到现有的方法，并包括其他的重写的方法，以便添加更多功能。 以下各节提供的源代码的指导的教程。

#### <a name="oncreate"></a>OnCreate

已更新**OnCreate**方法配置监视表面样式和前面一样，但它还包括一些附加步骤： 

1.  设置的背景图像**xamarin_background**驻留在的资源**Resources/drawable-hdpi/xamarin_background.png**。 

2.  初始化`Paint`绘制时针、 分针和第二个指针的对象。

3.  初始化`Paint`用于进行绘制的手表表盘边缘周围的小时刻度对象。 

4.  创建一个计时器调用`Invalidate`（重绘） 方法，以便第二个指针将每隔一秒重绘。 请注意，此计时器必须因为`OnTimeTick`调用`Invalidate`每分钟一次。

该示例包括只有一个**xamarin_background.png**映像; 但是，你可能想要创建你的自定义手表表盘将支持每个屏幕密度的不同的背景图像。 

#### <a name="ondraw"></a>OnDraw

已更新**OnDraw**方法绘制模拟样式手表表盘，使用以下步骤： 

1.  获取当前时间，现在在中维护`time`对象。 

2.  确定的绘图图面的和其中心的边界。

3.  绘制背景，缩放以适合该设备时绘制背景。

4.  绘制十二个*刻度*围绕表面的时钟 （对应于时钟表盘上的小时数）。 

5.  计算角度、 旋转和每个监视指针的长度。

6.  监视的表面上绘制每个手动。 请注意，是否监视在环境的模式下不会绘制第二个指针。 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

调用此方法以通知`MyWatchFaceEngine`磨损设备 （如低位的环境模式和老化保护） 的属性。 在`MyWatchFaceEngine`，此方法仅检查的低位环境的模式 （在低位环境模式下，屏幕支持较少的位用于每种颜色）。 

有关此方法的详细信息，请参阅 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 文档。


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

磨损设备进入或退出环境模式时，调用此方法。 在`MyWatchFaceEngine`实现中，在环境的模式下时手表表盘禁用抗锯齿。 

有关此方法的详细信息，请参阅 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 文档。


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

调用此方法时监视变得可见还是隐藏。 在`MyWatchFaceEngine`，此方法注册/注销的时区接收方 （如下所述） 根据可见性状态。 

有关此方法的详细信息，请参阅 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 文档。

 
### <a name="time-zone-feature"></a>时区功能

新**MyWatchFaceService.cs**还包括以更新当前时间每当时区更改 （如而旅行跨时区） 的功能。 接近结尾部分**MyWatchFaceService.cs**，区域更改的时间`BroadcastReceiver`定义用于处理时区更改目的对象： 

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

`RegisterTimezoneReceiver`和`UnregisterTimezoneReceiver`方法调用的`OnVisibilityChanged`方法。 
`UnregisterTimezoneReceiver` 时调用的手表表盘的可见性状态更改为隐藏。 当再次可见时手表表盘`RegisterTimezoneReceiver`调用 (请参阅`OnVisibilityChanged`方法)。 

引擎`RegisterTimezoneReceiver`方法声明一个处理程序，此时区接收器`Receive`事件; 此处理程序更新`time`使用这个新的时间，每当跨越时区对象： 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

创建和注册时区接收者意向的筛选器：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver`方法注销时区接收方：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>运行改进的手表表盘

生成并再次将应用部署到磨损设备。 选择监视字体选择器为之前从手表表盘。 在监视选取器中的预览将显示在左侧，并在右侧显示新的手表表盘：

[![模拟手表表盘](creating-a-watchface-images/13-analog-watchface.png "改进模拟表面中选取器和设备上")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在此屏幕截图，第二个指针移动每秒一次。 磨损设备上运行此代码时，第二个指针将监视进入环境模式消失。

 
## <a name="summary"></a>总结

在此演练中，自定义的 Android 磨损 watchface 已实现和测试。 `CanvasWatchFaceService`和`CanvasWatchFaceService.Engine`引入类，并且引擎类的基本方法已执行来创建简单的数字手表表盘。 此实现使用多个功能，以创建模拟监视表面，已更新和其他方法在实现以处理更改可见性、 环境模式和设备属性方面的差异。 最后，时区广播接收方已实现，以便监视自动更新何时跨越时区的时间。 


## <a name="related-links"></a>相关链接

- [创建监视表面](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 示例](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
