---
title: 演练-在 Android 中使用触摸
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: 625ba800ce498f80c0344c67e26bd79360de4002
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050554"
---
# <a name="walkthrough---using-touch-in-android"></a>演练-在 Android 中使用触摸

让我们了解如何以从上一节中运行的应用程序使用的概念。 我们将创建具有四个活动的应用程序。 第一个活动将菜单或切换面板以便将启动要演示各种 Api 的其他活动。 以下屏幕截图显示主活动：

[![示例屏幕截图 Touch 我按钮](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一个活动，Touch 示例中，将显示如何使用针对接触视图的事件处理程序。 笔势识别器活动将演示如何创建子类`Android.View.Views`和处理事件，以及演示如何处理捏合手势。 第三个和最后一个活动，**自定义笔势**，将展示了如何使用自定义特定动作。 若要使操作更轻松地遵循和吸收，我们将分解本演练为节，并将重点放在其中一个活动的每个部分。

## <a name="touch-sample-activity"></a>Touch 示例活动

-   打开项目**TouchWalkthrough\_启动**。 **MainActivity**是所有组转&ndash;都由我们要在活动中实现的触摸行为。 如果你运行应用程序并单击**Touch 示例**，应启动的以下活动：

    [![与 Touch 开始显示的活动的屏幕截图](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   现在，我们已确认活动开始，打开文件**TouchActivity.cs**和添加的处理程序`Touch`事件`ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   接下来，添加以下方法**TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

请注意，在上面的代码中，我们将`Move`和`Down`视为相同的操作。 这是因为用户可能不提升其手指的即使`ImageView`、 它可能会移动或由用户所施加的压力可能更改。 这些类型的更改将生成`Move`操作。

每次用户收尾工作`ImageView`、`Touch`将引发事件和我们的处理程序将显示消息**Touch 开始**在屏幕上，如下面的屏幕截图中所示：

[![与 Touch 开始的活动的屏幕截图](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要接触用户`ImageView`， **Touch 开始**将显示在`TextView`。 当用户不再处理`ImageView`，消息**Touch 结束**将显示在`TextView`，如以下屏幕截图中所示：

[![与 Touch 结束的活动的屏幕截图](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>笔势识别器活动

现在让我们实现笔势识别器活动。 此活动将演示如何将在屏幕的视图，并说明实现捏合缩放一种方法。

-   将新活动添加到应用程序调用`GestureRecognizer`。
    编辑此活动的代码，使它类似于下面的代码：

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   添加新的 Android 查看到项目中，并将其命名`GestureRecognizerView`。 与此类添加以下变量：

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   添加到以下构造函数`GestureRecognizerView`。 此构造函数将添加`ImageView`到我们的活动。 此时代码仍将不会编译&ndash;我们需要创建类`MyScaleListener`，将大小调整帮助`ImageView`时用户 pinches 它：

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   若要在我们活动上绘制图像，我们需要重写`OnDraw`视图类，如下面的代码段中所示的方法。 此代码将移动`ImageView`到指定的位置`_posX`和`_posY`也一样调整图像的大小的缩放因子根据：

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   接下来我们需要更新实例变量`_scaleFactor`作为用户 pinches `ImageView`。 我们将添加一个名为类`MyScaleListener`。 此类将侦听将由 Android 在用户 pinches 时引发的缩放事件`ImageView`。
    添加到以下的内部类`GestureRecognizerView`。 此类是`ScaleGesture.SimpleOnScaleGestureListener`。 此类是一个方便的类侦听器可以子类化，当你感兴趣的手势子集：

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _iconview.Invalidate();
            return true;
        }
    }
    ```

-   我们需要重写中的下一步方法`GestureRecognizerView`是`OnTouchEvent`。 下面的代码列出了此方法的完整实现。 没有大量的代码在这里，现在，我们需要一些时间，并查看当前在此处上进行操作。 此方法执行的第一件事是缩放图标，如有必要&ndash;这通过调用处理`_scaleDetector.OnTouchEvent`。 接下来，我们尝试找出哪些操作调用此方法：

    - 如果用户接触与屏幕，我们记录的 X 和 Y 位置和接触屏幕的第一个指针的 ID。

    - 如果用户移动其触摸屏幕上，然后我们也找出用户移动鼠标指针的距离。

    - 如果用户具有提升不在屏幕他手指，我们将停止跟踪手势。

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   现在运行应用程序，并启动笔势识别器活动。
    在启动时的屏幕应类似下面的屏幕截图：

    [![笔势识别器使用 Android 的图标启动屏幕](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   现在 touch 图标，并将其拖动在屏幕。 请尝试捏合缩放手势。 在某一时刻屏幕可能类似于下面的屏幕快照：

    [![在屏幕的手势移动图标](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此时，你应授予自己 pat 背面： 您只需具有在 Android 应用程序中实现捏合缩放 ！ 快速休息，并允许将上移至在本演练中的第三个和最后一个活动&ndash;使用自定义特定动作。

## <a name="custom-gesture-activity"></a>自定义笔势活动

在本演练中的最后一个屏幕将使用自定义特定动作。

出于本演练的目的，手势库已使用手势工具创建和添加到项目文件中**资源/原始/手势**。 与的开内务处理此位，允许在本演练中的最后一个活动上获取。

-   添加一个名为的布局文件**自定义\_笔势\_layout.axml**到具有以下内容的项目。 该项目已包含所有映像**资源**文件夹：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   接下来将新活动添加到项目并将其命名`CustomGestureRecognizerActivity.cs`。 添加到类中，在以下两行代码中显示为两个实例变量：

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   编辑`OnCreate`方法的活动中，以便它类似于下面的代码。 让我们看一分钟解释此代码中发生的时间。 我们首先要做是实例化`GestureOverlayView`然后设置为活动的根视图。
    我们还将分配到一个事件处理程序`GesturePerformed`事件`GestureOverlayView`。 接下来我们可放大量更早版本，已创建的布局文件并将其添加为子视图的`GestureOverlayView`。 最后一步是初始化该变量`_gestureLibrary`和从应用程序资源加载手势文件。 如果出于某种原因，无法加载的手势文件，没有太多可以执行此活动，因此很关闭：

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   我们需要执行实现方法最后一件事情`GestureOverlayViewOnGesturePerformed`下面的代码段中所示。 当`GestureOverlayView`检测到笔势，它返回调用此方法。 我们尝试获取第一件事`IList<Prediction>`对象通过调用匹配笔势`_gestureLibrary.Recognize()`。 我们使用 LINQ 位获取`Prediction`具有最高的得分的笔势。

    如果没有匹配笔势使用高等级足够分数，则事件处理程序退出时不执行任何操作。 否则为，我们将检查预测的名称并更改要显示的图像基于笔势的名称：

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   运行应用程序和自定义笔势识别器活动启动。 其外观应类似于下面的屏幕快照：

    [![屏幕截图检查我映像](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    现在，在屏幕上，绘制一个复选标记和显示位图应如下所示，在下一步的屏幕截图所示：

    [![识别绘制复选标记，复选标记](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    最后，在屏幕上绘制 scribble。 复选框应将更改回其原始映像，这些屏幕截图所示：

    [![显示在屏幕上，原始图像的 scribble](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

你已经了解如何将触控和手势中使用 Xamarin.Android 的 Android 应用程序进行集成。


## <a name="related-links"></a>相关链接

- [Android Touch 启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch 最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
