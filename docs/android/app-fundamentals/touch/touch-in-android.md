---
title: "在 Android 中按"
ms.topic: article
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 83997018f4e08567a9150bf1e21374a98c8ddb4e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="touch-in-android"></a>在 Android 中按

Android 创建包含有关与屏幕的用户的物理交互的数据的对象得多 iOS，之类&ndash;`Android.View.MotionEvent`对象。 此对象保存数据，例如执行什么操作，其中触摸花费放置，多少压力已应用，等等。A`MotionEvent`对象将分解为以下值的移动到：

-  描述的类型的动作，如初始触摸，跨屏幕或触摸结束移动触摸操作代码。

-  一组描述的位置的轴值`MotionEvent`和其他移动属性，如触摸正在进行，当触摸发生，以及使用多少压力。
   轴值可能不同，具体取决于设备，因此前面的列表不描述所有轴值。


`MotionEvent`对象将传递给应用程序中适当的方法。 有三种方法 Xamarin.Android 应用程序可通过响应触摸事件：

-  *分配到一个事件处理程序`View.Touch`*  -`Android.Views.View`类具有`EventHandler<View.TouchEventArgs>`哪些应用程序可以分配到一个处理程序。 这是典型的.NET 行为。

-  *实现`View.IOnTouchListener`*  -此接口的实例可以分配给使用该视图的视图对象。 `SetOnListener` 方法。这在功能上等效于将分配到一个事件处理程序`View.Touch`事件。 如果有许多不同的视图可能需要它们时接触一些常用或共享逻辑，它将创建一个类并实现此方法比要将分配每个视图自己的事件处理程序更有效率。

-  *重写`View.OnTouchEvent`*  -Android 子类中的所有视图`Android.Views.View`。 当视图接触时，将调用 Android`OnTouchEvent`并将其传递`MotionEvent`对象作为参数。


> [!NOTE]
> **注意：**并非所有的 Android 设备支持触摸屏。 

Google Play 将以下标记添加到清单文件到仅显示你的应用是启用了触摸那些设备将导致：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>笔势

笔势是上的触摸屏手的形状。 笔势可以到其中，一个或多个笔画每一条笔画创建联系人与屏幕不同点的点的序列组成。 Android 可以支持的笔势，从屏幕到涉及多点触控的复杂手势简单 fling 的许多不同的类型。

Android 提供`Android.Gestures`专门用于管理和响应手势的命名空间。 在所有的笔势的核心是一个名为的特殊类`Android.Gestures.GestureDetector`。 顾名思义，此类将侦听的手势和基于事件`MotionEvents`由操作系统提供。

若要实现笔势检测器，活动必须实例化`GestureDetector`类并提供的一个实例`IOnGestureListener`，如下面的代码段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活动还必须实现 OnTouchEvent 并将 MotionEvent 传递给笔势检测器。 下面的代码段显示了此示例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

实例时`GestureDetector`标识笔势感兴趣，它将通知的活动或应用程序通过引发事件或通过提供的回调`GestureDetector.IOnGestureListener`。
此接口可提供各种笔势的六个方法：

-  *OnDown* -当点击发生，但不是会被释放时调用。

-  *OnFling* -fling 时发生，并提供在触发了事件的开始和结束触摸设备上的数据时调用。

-  *OnLongPress* -长按发生时调用。

-  *OnScroll*的滚动事件发生时调用。

-  *OnShowPress* -调用 OnDown 发生后和移动或释放事件尚未执行。

-  *OnSingleTapUp* -单个点击发生时调用。


在许多情况下应用程序仅可能感兴趣的手势子集。 在这种情况下，应用程序应扩展类 GestureDetector.SimpleOnGestureListener 和重写方法对应的事件，它们感兴趣。

## <a name="custom-gestures"></a>自定义特定动作

手势是用户能够与应用程序进行交互的好方法。 到目前为止，我们已了解的 Api 的简单手势，便已足够，但有可能证实有点繁重更复杂的笔势。 为了帮助实现更复杂的手势，Android 提供 API 的变得轻松一些自定义特定动作与关联的负担 Android.Gestures 命名空间中的另一的组。

### <a name="creating-custom-gestures"></a>创建自定义特定动作

自 Android 1.6，Android SDK 附带了仿真程序调用手势生成器上预安装的应用程序。 此应用程序允许开发人员创建可以嵌入在应用程序的预定义的笔势。 下面的屏幕快照显示手势生成器的示例：

[![屏幕快照的手势生成器使用示例笔势](touch-in-android-images/image11.png)](touch-in-android-images/image11.png)

Google Play，可以找到调用笔势工具此应用程序的改进的版本。 笔势工具非常类似手势生成器，只不过它允许你创建它们后测试手势。 此下一步的屏幕截图显示手势生成器：

[![屏幕快照的笔势工具使用示例笔势](touch-in-android-images/image12.png)](touch-in-android-images/image12.png)

笔势工具少量的可用于创建自定义特定动作，因为它允许创建它们后要测试笔势，并可轻松地通过 Google Play。

笔势工具允许你创建笔势，请在屏幕上绘制，并指定一个名称。 创建笔势后它们将保存在你的设备的 SD 卡上的二进制文件。 此文件需要从设备中，检索，然后与文件夹 /Resources/raw 中的应用程序一起打包。 可以从仿真程序使用 Android 调试桥检索此文件。 下面的示例演示将从 Galaxy Nexus 文件复制到应用程序的资源目录：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦你已检索到它必须与你在目录 /Resources 内应用程序/原始一起打包的文件。 使用此手势文件的最简单方法是将文件加载到 GestureLibrary，如下面的代码段中所示：

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自定义特定动作

若要识别在活动中的自定义操作，它必须添加到其布局的 Android.Gesture.GestureOverlay 对象。 下面的代码段演示如何以编程方式向活动中添加 GestureOverlayView:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

以下 XML 代码段演示如何以声明方式添加 GestureOverlayView:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`具有多个将在绘制笔势的过程中引发的事件。 最感兴趣的事件是`GesturePeformed`。 当用户完成绘制其笔势，引发此事件。

活动时引发此事件时，会要求`GestureLibrary`尝试进行匹配笔势工具创建与某一笔势用户笔势。 `GestureLibrary` 将返回的预测对象的列表。

每个预测对象保留的分数和名称的某一在笔势`GestureLibrary`。 越高的评分，更有可能预测中名为的笔势匹配绘制用户笔势。
通常情况下，分数低于 1.0 被视为较差的匹配项。

下面的代码演示匹配笔势的一个示例：

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

此操作完成后，你应会如何在 Xamarin.Android 应用程序中使用触控和手势的了解。 让我们现在将上移至演练，并查看所有工作示例应用程序中的概念。



## <a name="related-links"></a>相关链接

- [Android Touch 启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch 最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
