---
title: 图形和动画
description: Android 支持二维图形和动画提供了非常丰富、 不同的框架。 本主题介绍这些框架，并讨论如何在 Xamarin.Android 应用程序中创建自定义图形和动画供使用。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 85a7cd2ac5094a4760c5465bd099ce2e3beeae79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="graphics-and-animation"></a>图形和动画

_Android 支持二维图形和动画提供了非常丰富、 不同的框架。本主题介绍这些框架，并讨论如何在 Xamarin.Android 应用程序中创建自定义图形和动画供使用。_


## <a name="overview"></a>概述

尽管是传统意义上的有限电源的设备上运行，最高的额定移动应用程序通常具有复杂用户体验 (UX)，完成，但高质量图形和动画提供直观、 响应、 动态的外观。 在移动应用程序获取的详细信息并更复杂，用户已经开始需要越来越多地从应用程序。

幸运的是，对我们而言，现代移动平台都具有非常强大的框架，用于在保留易用性的同时创建复杂的动画和自定义图形。 这使开发人员将丰富的交互性很少的工作量。

在 Android 中的 UI API 框架大致分为两类： 图形和动画。

图形进一步细分成来执行 2D 和 3D 图形的不同方法。 3D 图形可通过多种内置框架，例如 OpenGL ES （移动特定版本的 OpenGL） 和第三方框架，例如 MonoGame （一个跨平台的工具包兼容 XNA 工具包）。 虽然 3D 图形不在本文的讨论范围内，我们将查看内置的二维绘制技术。

Android 提供两个不同 API 的创建二维图形。 一个是高级别声明性方法和其他编程的低级别 API:

-   **可绘制资源**&ndash;这些凭据用于进行自定义图形以编程方式创建，或者创建 （更常见的做法） 通过在 XML 文件中嵌入绘制的说明。 通常，可绘制资源被定义为包含说明或适用于 Android 呈现二维图形操作的 XML 文件。 

-   **画布**&ndash;这是涉及直接在基础位图上绘制的较低级别 API。 它提供对显示的内容非常细化的控制。 

除了这些二维图形技巧，Android 还提供几种不同方式创建动画：

-   **可绘制动画** &ndash; Android 还支持请逐个框架动画称为*可绘制动画*。 这是最简单的动画 API。 Android 按顺序加载并显示中 （类似卡通） 的序列可绘制的资源。

-   **查看动画** &ndash; *视图动画*是原始动画 API 的 Android 中并且在所有版本的 Android 中可用。 此 API 已有限，，因为它只适用于视图对象，并且只能对这些视图执行简单的转换。
    通常在中找到的 XML 文件中定义视图动画`/Resources/anim`文件夹。

-   **属性动画** &ndash; Android 3.0 引入了一组新的动画 API 的称为*属性动画*。 这些新的 API 引入了一个可扩展、 灵活的系统，可用来进行动画处理的任何对象的属性，而不仅仅是查看对象。 这种灵活性，动画将使共享变得更容易的代码的不同类中封装。


视图动画是更适合应用程序必须支持较旧预 Android 3.0 API 的 （API 级别 11）。 否则应用程序应使用较新属性动画 API 的为上面提到的原因。

所有这些框架都是可行选项，但是在可能的情况下，首选项应授予给属性动画，因为它是一个更灵活的 API，用于。 属性动画允许动画逻辑封装在不同的类可以使共享变得更容易的代码，并且可以简化代码维护。


## <a name="accessibility"></a>可访问性

图形和动画有助于使 Android 应用具吸引力和有趣使用;但是，务必要记住一些交互发生在通过 screenreaders，备用输入设备，或与辅助缩放。
此外，某些交互可能会出现不带音频的功能。

如果它们已被设计成含有记住可访问性，应用将在这些情况下更易于使用： 提供提示和导航帮助用户界面，并确保没有文本内容或 UI 的图形化元素的说明。

请参阅[Google 的可访问性指南](http://developer.android.com/guide/topics/ui/accessibility/)有关如何利用 Android 的可访问性 Api 的详细信息。



## <a name="2d-graphics"></a>二维图形

可绘制资源是 Android 应用程序中的常用技术。 因为其他资源，可绘制资源是声明性&ndash;它们在 XML 文件中定义。 此方法允许从资源的代码完全分离。 这可以简化开发和维护，因为它不需要更改代码以更新或更改 Android 应用程序中的图形。 但是，适用于许多简单和常见图形要求可绘制资源时，它们缺少的功能和控制的画布 API。

另一个方法，使用[画布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)对象，非常类似于其他传统的 API 框架，例如 System.Drawing 或 iOS 的核心绘图。 使用画布对象提供创建如何二维图形的最大控制。 它是适用于以下情况，其中可绘制资源将无法工作，或将很难使用。 例如，可能需要绘制自定义的滑块控件更改其外观将根据与将滑块的值相关的计算。

让我们首先看一下可绘制的资源。 它们是更简单，涵盖的最常见的自定义绘制用例。


### <a name="drawable-resources"></a>可绘制资源

在目录中的 XML 文件中定义可绘制资源`/Resources/drawable`。 与嵌入 PNG 或 JPEG 的不需要提供可绘制资源密度特定版本。
在运行时，Android 应用程序将加载这些资源，并使用这些 XML 文件中包含的说明进行操作来创建二维图形。
Android 定义多种不同类型的可绘制资源：

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash;这是一个可绘制对象的绘制一个基元的几何形状，并将应用一组有限的该形状上的图形效果。 它们是对于诸如自定义按钮，或设置背景的 TextViews 非常有用。 我们将看到举例说明如何使用`ShapeDrawable`本文后续部分中。

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash;这是一个可绘制的资源，将更改外观基于小组件/控件的状态。 例如，一个按钮可能更改具体取决于是否按下或不其外观。

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash;将堆叠几个其他 drawables 一个在一起另一个的此可绘制资源。 一个示例*LayerDrawable*以下屏幕截图所示：

    ![LayerDrawable 示例](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash;这是*LayerDrawable*但有一处不同。 A *TransitionDrawable*能够进行动画处理另一个显示基础上的一个层。

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash;它非常类似于*StateListDrawable* ，因为它将显示基于某些条件的映像。 但是，与不同*StateListDrawable*、 *LevelListDrawable*显示基于整数值的映像。 一个示例*LevelListDrawable*是显示 WiFi 信号强度。 作为 WiFi 信号变化的强度，可绘制显示将相应地更改。

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;这些 Drawables 正如其名，提供缩放和剪辑功能。 *ScaleDrawable*将缩放另一个可绘制，while *ClipDrawable*将剪辑另一个 Drawable。

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash;此可绘制将应用内边距区域的另一个可绘制资源的侧面。 它用时需要小于视图的实际边界的背景的视图。

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash;此文件是一组的说明进行操作，在 XML 中，在实际位图上执行。 Android 可以执行某些操作是拼贴、 抖动，和抗锯齿。 这很常见用途之一是磁贴在布局的背景的位图。


#### <a name="drawable-example"></a>可绘制示例

让我们看一下快速示例说明如何创建二维图形 using `ShapeDrawable`。 A`ShapeDrawable`可以定义四个基本形状之一： 矩形、 椭圆、 行和环。 还有可能将基本的效果，如渐变、 彩色和大小。 以下 XML 是`ShapeDrawable`在中可能找到*AnimationsDemo*助理项目 (文件中`Resources/drawable/shape_rounded_blue_rect.xml`)。
它定义一个具有渐变背景为紫色的矩形，圆角：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

我们可以引用以声明方式中的布局和其他 Drawable，如下面的 XML 中所示的此可绘制资源：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

此外可以以编程方式应用可绘制的资源。 下面的代码段演示如何以编程方式设置的 TextView 背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看什么这将如下所示，运行*AnimationsDemo*项目，然后从主菜单中选择的形状可绘制项。 我们应看到类似于下面的屏幕快照：

![包含一个具有渐变和圆角可绘制的自定义背景 Textview](graphics-and-animation-images/image1.png)

有关 XML 元素和语法可绘制资源的详细信息，请查阅[Google 文档](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。


### <a name="using-the-canvas-drawing-api"></a>使用画布绘图 API

Drawables 功能非常强大，但有其局限性。 某些事项是不可能或非常复杂 (例如： 通过在设备上的相机拍摄图片应用筛选器)。 它将通过使用可绘制资源应用红眼非常困难。
相反，画布 API 允许应用程序具有细致地控制要有选择地更改该图片的特定部件中的颜色。

常用于画布的一个类是[绘制](https://developer.xamarin.com/api/type/Android.Graphics.Paint/)类。 此类包含有关如何绘制的颜色和样式信息。 它用于提供内容，这种颜色和透明度。

画布 API 使用*刷的模型*绘制二维图形。
在各个连续层中将操作应用。 每个操作将覆盖基础位图某些区域。 当区域重叠以前绘制的区域时，新的绘制将部分或完全遮盖旧。 这是相同的方式，例如 System.Drawing 和 iOS 的核心图形的许多其他图形 Api 工作。

有两种方法来获取`Canvas`对象。 第一种方法涉及定义[位图](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)对象，并且然后实例化`Canvas`与它的对象。 例如，下面的代码段创建一个新的画布与基础位图：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

另一种方法来获取`Canvas`对象是通过[OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/)提供的回调方法[视图](https://developer.xamarin.com/api/type/Android.Views.View/)基类。 Android 时它决定视图需要绘制本身，并且将传入调用此方法`Canvas`要使用的视图的对象。

画布类公开的方法以编程方式提供的绘图说明进行操作。 例如：

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash;用指定的绘制填充整个画布的位图。

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash;绘制使用指定的绘制的指定几何形状。

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash;与指定的彩色画布上绘制的文本。 在位置绘制文本`x,y`。



#### <a name="drawing-with-the-canvas-api"></a>绘制带画布 API

我们来看操作中的画布 API 的示例。 下面的代码段演示如何绘制视图：

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

上面这段代码首先创建红色的绘画和绿色绘制对象。 它有一个红色，填充画布的内容，然后指示画布上以绘制的画布的宽度的 25%的绿色矩形中。 可以通过中看到此示例`AnimationsDemo`附带这篇文章的源代码的项目。 通过应用程序启动并从主菜单中选择绘制项，我们应类似于以下屏幕：

![红色绘画和绿色绘制对象与屏幕](graphics-and-animation-images/image3.png)


## <a name="animation"></a>动画

用户喜欢在其应用程序中移动的内容。 动画是改善应用程序的用户体验并使其突出显示的好办法。用户未注意到，因为它们感觉自然的是最佳的动画。 Android 为动画提供以下三个 API:

-   **查看动画**&ndash;这是原始的 API。 这些动画绑定到特定的视图，并可以对视图的内容执行简单的转换。 由于它的为了简单起见，此 API 仍可用于操作如 alpha 动画、 旋转和等。

-   **属性动画** &ndash; Android 3.0 中引入了属性动画。 它们使应用程序要进行动画处理几乎所有内容。 可以使用属性动画若要更改任何属性的任何对象，即使该对象不在屏幕上可见。

-   **可绘制动画**&ndash;这用于将应用非常简单的动画的特殊可绘制资源影响到布局。

一般情况下，属性动画，则首选的系统使用，因为它更灵活，提供更多的功能。


### <a name="view-animations"></a>视图动画

视图动画被限制到视图，并且只能对值，例如开始和结束点、 大小、 旋转和透明度执行动画。
这些类型的动画通常称为*之间动画*。 可以通过两种方式定义视图动画&ndash;以编程方式在代码或通过使用 XML 文件。 XML 文件都声明视图动画的首选的方法，因为它们是更具可读性且更易维护。

动画 XML 文件将存储在`/Resources/anim`Xamarin.Android 项目目录。 此文件必须具有以下元素之一作为根元素：

-   `alpha` &ndash; 淡出动画中。

-   `rotate` &ndash; 旋转动画。

-   `scale` &ndash; 大小调整的动画。

-   `translate` &ndash; 水平和/或垂直移动。

-   `set` &ndash; 可能包含一个或多个其他动画元素的容器。

默认情况下，将同时应用到 XML 文件中的所有动画。 若要按顺序发生的动画，将设置`android:startOffset`上面定义的元素之一上的属性。

可以通过使用影响动画变动率*插值程序*。 插值程序使得要加速重复，或 decelerated 的动画效果。 Android 框架提供现成的多个插入器类似于 （但不是限于）：

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; 这些插入器增加或减少动画变动率。

-   `BounceInterpolator` &ndash; 更改退回的末尾。

-   `LinearInterpolator` &ndash; 更改的速率为常数。


下面的 XML 演示动画文件将这些元素的某些组合在一起的一个示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

此动画将所有动画同时执行。 第一个的缩放动画将拉伸图像的水平和垂直收缩，然后映像将同时将逆时针旋转 45 度和收缩，请从屏幕中消失。

动画可以以编程方式应用于视图通过 inflating 动画，然后将它应用于视图。 Android 提供的帮助器类`Android.Views.Animations.AnimationUtils`，它会放大量动画资源，并返回的实例`Android.Views.Animations.Animation`。 此对象通过调用应用于视图`StartAnimation`并传递`Animation`对象。 下面的代码段显示了此示例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

现在，我们已基本了解工作方式视图动画，则允许将移到属性动画。


### <a name="property-animations"></a>属性动画

属性动画制作人员是 Android 3.0 中引入了一个新的 API。
它们提供一种扩展性更强的 API，可以使用任何对象上的任何属性进行动画处理。

所有属性动画都由的实例[生成器](https://developer.xamarin.com/api/type/Android.Animation.Animator/)子类。 应用程序不直接使用此类，而是使用它的子类之一：

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash;此类是整个属性动画 API 中的最重要类。 此公式计算需要更改的属性的值。 `ViewAnimator`不直接更新这些值; 相反，它会引发可以用于更新动画的对象的事件。

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash;此类是的一个子类`ValueAnimator`。 它旨在简化通过接受目标对象和属性来更新进行动画处理的对象的过程。

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash;此类负责协调动画到另一个运行在关系中的方式。 动画它们之间可能会运行同时、 按顺序，或指定的延迟。


*计算器*是动画制作人员用于动画播放期间计算新值的特殊类。 现成的 Android 提供下列计算器：

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash;计算整数属性的值。

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash;计算 float 属性的值。

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash;计算颜色属性的值。

如果正在进行动画处理的属性不是`float`，`int`或彩色，应用程序可能创建其自己的计算器，通过实现`ITypeEvaluator`接口。 （实现自定义计算器是超出了本主题的范围。）

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

有两个部分的任何动画： 计算动画的值，然后在某些对象上的属性上设置这些值。 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)仅将计算的值，但它将不起作用的对象直接。 相反，对象将更新内将动画生存期内调用的事件处理程序。 此设计允许多个属性以从一个动画的值进行更新。

获取实例`ValueAnimator`通过调用以下的工厂方法之一：

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

完成后，完成`ValueAnimator`实例都必须具有其持续时间设置，并且然后启动它。 下面的示例演示如何对从 0 到 1 的值进行动画处理 1000年毫秒的不同时间：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

本身，上面的代码段不是非常有用，但&ndash;生成器将运行，但没有更新后的值没有目标。 `Animator`类将引发更新事件时它决定有必要通知侦听器的新值。 应用程序可能提供事件处理程序以响应此事件，如下面的代码段中所示：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

现在，我们已了解`ValueAnimator`，允许详细了解`ObjectAnimator`。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)是的一个子类`ViewAnimator`结合计时引擎和值计算的`ValueAnimator`将事件处理程序所需的逻辑。 `ValueAnimator`要求应用程序显式将事件处理程序&ndash;`ObjectAnimator`将为我们会负责的此步骤。

有关 API`ObjectAnimator`非常相似的 API `ViewAnimator`，但要求你提供对象和要更新的属性的名称。 下面的示例演示使用的示例`ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

正如您可以看到从上面的代码段，`ObjectAnimator`可以减少并简化对象进行动画处理所需的代码。


### <a name="drawable-animations"></a>可绘制动画

最终动画 API，则可绘制动画 API。 可绘制动画依次加载的一系列可绘制资源一个并将其显示按顺序，类似于翻转 it 卡通。

在具有的 XML 文件中定义可绘制资源`<animation-list>`作为根元素，并一系列元素`<item>`元素定义每个帧中动画。 此 XML 文件存储在`/Resource/drawable`应用程序文件夹。 以下 XML 是可绘制动画的示例：

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

此动画将贯穿六个帧。 `android:duration`特性将其声明每个帧将显示多长时间。 下一步的代码段演示创建可绘制动画和启动它，当用户单击屏幕上的按钮的示例：

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

此时，我们已介绍动画 Android 应用程序中提供的 Api 的基础。


## <a name="summary"></a>总结

这篇文章引入大量的新概念和 API 来帮助将某些图形添加到 Android 应用程序。 首先它讨论各种二维图形 API 的并演示如何 Android 允许应用程序直接向使用画布对象屏幕绘制。 我们还了解了一些允许图形要以声明方式创建使用 XML 文件的备用技术。 然后我们发往讨论的旧和新 API 的 Android 中创建动画。



## <a name="related-links"></a>相关链接

- [动画演示 （示例）](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [动画和图形](http://developer.android.com/guide/topics/graphics/index.html)
- [使用动画将您的移动应用融入生活](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [画布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [对象生成器](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [值生成器](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
