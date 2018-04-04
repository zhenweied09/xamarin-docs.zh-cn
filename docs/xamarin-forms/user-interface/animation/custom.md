---
title: 自定义动画
description: 动画类是所有 Xamarin.Forms 动画，创建一个或多个动画对象 ViewExtensions 类中的扩展方法使用构建基块。 本文演示如何使用动画类来创建和取消动画，请同步多个动画，以及创建不由现有动画方法进行动画处理的属性进行动画处理的自定义动画。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 302aa784baad9afb703f88dcfba56b68fd3c9105
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="custom-animations"></a>自定义动画

_动画类是所有 Xamarin.Forms 动画，创建一个或多个动画对象 ViewExtensions 类中的扩展方法使用构建基块。本文演示如何使用动画类来创建和取消动画，请同步多个动画，以及创建不由现有动画方法进行动画处理的属性进行动画处理的自定义动画。_


创建时，必须指定了多个参数`Animation`对象，其中包括正在进行动画处理的属性的开始和结束值和更改的属性的值的回调。 `Animation`对象还可以维护子动画可运行和同步的集合。 有关详细信息，请参阅[子动画](#child)。

运行与创建的动画[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类，该类可能包含或不包含子动画，通过调用实现[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法。 此方法指定的持续时间的动画，并在其他项，控制是否重复动画的回调。

## <a name="creating-an-animation"></a>创建动画

在创建时[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)对象，通常情况下，最少三个参数是必需的如下面的代码示例中所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此代码定义的动画[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)实例从值 1 为 2 的值。 动画的值，该值的推导方式为 Xamarin.Forms，传递给回调指定为第一个参数，它用于更改的值`Scale`属性。

通过调用启动动画[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法，如下面的代码示例中所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

请注意， [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法不返回`Task`对象。 相反，通过回调方法提供了通知。

中指定以下参数`Commit`方法：

- 第一个参数 (*所有者*) 标识的动画的所有者。 这可以对其应用动画的可视元素或另一个可视元素，例如页。
- 第二个参数 (*名称*) 标识动画的名称。 名称结合了多个要唯一标识动画的所有者。 然后可以使用这种唯一标识，确定是否运行动画 ([`AnimationIsRunning`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AnimationIsRunning/p/Xamarin.Forms.IAnimatable/System.String/))，或取消它 ([`AbortAnimation`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/))。
- 第三个参数 (*速率*) 指示中定义的回调方法每次调用之间的毫秒数[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)构造函数
- 第四个参数 (*长度*) 指示动画，以毫秒为单位的持续时间。
- 第五个参数 (*缓动*) 定义用于动画的缓动函数。 或者，可以将缓动函数指定的自变量为[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)构造函数。 有关的缓动函数的详细信息，请参阅[缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。
- 第六个参数 (*完成*) 将在动画完成时执行的回调。 此回调采用两个参数，使用第一个参数，指示一个最终值，并且第二个自变量被`bool`设置为`true`动画已取消的情况。 或者，*完成*回调可以指定的自变量为[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)构造函数。 但是，通过单一动画，如果*完成*中都指定了回调`Animation`构造函数和`Commit`方法，仅在指定的回调`Commit`将执行方法。
- 第七个参数 (*重复*) 允许动画要重复的回调。 在动画，末尾调用并返回`true`指示应重复动画。

总体效果是创建动画可提高[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)从 1 到 2，时间超过 2 秒 （2000年毫秒），使用[ `Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/)缓动函数。 每次动画完成后，其`Scale`属性重置为 1 和动画的重复。

> [!NOTE]
> 可以通过创建构造运行单独的并发的动画`Animation`每个动画对象，然后再调用`Commit`上每个动画的方法。

<a name="child" />

### <a name="child-animations"></a>子动画

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类还支持子动画，涉及到创建`Animation`对象与哪些其他对象`Animation`添加对象。 这样一系列的动画运行并同步。 下面的代码示例演示如何创建和运行子动画：

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

或者，可以更确切地，详见下面的代码示例编写的代码示例：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在这两个代码示例中，父[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)创建对象时，到其他`Animation`然后添加对象。 前两个自变量[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)方法指定何时开始和结束子动画。 自变量值必须是 0 和 1 之间，并且表示相对期间内父动画指定的子动画将处于活动状态。 因此，在此示例`scaleUpAnimation`将处于活动状态的动画，上半年`scaleDownAnimation`将对于处于活动状态的动画后, 半和`rotateAnimation`的整个持续时间中处于活动状态。

总体效果是动画发生超过 4 秒 （4000 毫秒为单位）。 `scaleUpAnimation`进行动画处理[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)从 1 到 2，时间超过 2 秒的属性。 `scaleDownAnimation`然后进行动画处理`Scale`属性从 2 为 1，超过 2 秒。 出现这两个缩放动画，而`rotateAnimation`进行动画处理[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性从 0 到 360，超过 4 秒。 请注意，缩放动画还使用缓动函数。 [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/)缓动函数会导致[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)以获取更大前, 最初收缩和[ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)缓动函数导致`Image`以变得比完整动画末尾其实际大小更小。

有许多差异[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)对象，它使用子动画，而另一个不在：

- 使用子动画时*完成*子动画回调指示子已完成时，与*完成*回调传递给`Commit`方法指示时已完成整个动画。
- 当使用子动画，则返回`true`从*重复*回调`Commit`方法不会导致动画重复，但动画将继续运行而无需新值。
- 包括中的缓动函数时`Commit`方法，并的缓动函数则返回的值大于 1，动画将被终止。 如果的缓动函数返回小于 0 的值，其值限制为 0。 若要使用一个缓动函数，返回一个值小于 0 或大于 1，它必须指定其中一个子动画，而不是在中`Commit`方法。

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类还进行了[ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/)方法可以用于将子动画添加到父`Animation`对象。 但是，其*开始*和*完成*自变量值并不是限制为 0 到 1，但仅对应于 0 到 1 的一系列子动画的该部分将处于活动状态。 例如，如果`WithConcurrent`方法调用定义面向子动画[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性从 1 到 6，但需要满足*开始*和*完成*的值-2 和 3，*开始*-2 的值对应于`Scale`值为 1，与*完成*3 的值对应于`Scale`值为 6。 因为 0 和 1 的范围之外的值中不起作用动画，`Scale`属性将仅进行动画处理从 3 到 6。

## <a name="canceling-an-animation"></a>取消动画

应用程序可以取消通过调用动画[ `AbortAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/)扩展方法，如下面的代码示例中所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

请注意动画进行唯一标识由动画所有者和动画名称的组合。 因此，所有者和名称，指定当运行动画必须指定要取消动画。 因此，此代码示例将立即取消名为动画`SimpleAnimation`归页。

## <a name="creating-a-custom-animation"></a>创建自定义动画

到目前为止此处所示的示例已演示了同样可使用在方法实现的动画[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类。 但是，利用[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类是其有权访问用于动画的值更改时执行的回调方法。 这样，用于实现任何所需的动画的回调。 例如，下面的代码示例进行动画处理[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)属性页上通过将其设置为[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)创建的值[ `Color.FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)具有色调值范围从 0 到 1 方法：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

生成动画提供前移彩虹的颜色通过页背景图像的外观。

有关创建复杂的动画，包括贝塞尔曲线动画的更多示例，请参阅[章 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)的[具有 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="creating-a-custom-animation-extension-method"></a>创建一个自定义动画的扩展方法

中的扩展方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类进行动画处理到指定的值从其当前值的属性。 这使得难以创建，例如，`ColorTo`动画方法可以用于进行动画处理的颜色从一个值到另一个字符串，因为：

- 唯一[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)属性定义[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)类是[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)，但并非总是所需`Color`属性要进行动画处理。
- 当前值通常[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)属性是[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)，这并不实际的颜色内, 插计算中无法使用它。

此问题的解决方案是没有`ColorTo`方法面向特定[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)属性。 相反，它可以编写将传递相比内, 插的回调方法`Color`回调用方的值。 此外，该方法将执行开始和结束`Color`自变量。

`ColorTo`方法可以实现为扩展方法使用[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/)中的方法[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)类以提供其功能。 这是因为`Animate`方法可用于目标属性的类型不是`double`，如下面的代码示例所示：

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/)方法需要*转换*自变量，这是一个回调方法。 此回调的输入始终是`double`范围从 0 到 1。 因此，`ColorTo`方法定义自己的转换`Func`接受`double`范围从 0 到 1，并且返回[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)与此值相对应的值。 `Color`值计算通过进行插值来[ `R` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)， [ `G` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)， [ `B` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)，和[ `A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)提供两个值`Color`自变量。 `Color`值然后传递到应用程序的特定属性的回调方法。

此方法允许`ColorTo`方法要进行动画处理任何[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)属性，如下面的代码示例中所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此代码示例中，`ColorTo`方法进行动画处理[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)属性[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)， `BackgroundColor`属性页上，与[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)属性[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)。

## <a name="summary"></a>总结

这篇文章演示了如何使用[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类创建和取消动画、 同步多个动画，然后创建不由现有动画进行动画处理的属性进行动画处理的自定义动画方法。 `Animation`类是所有 Xamarin.Forms 动画构建基块。


## <a name="related-links"></a>相关链接

- [自定义动画 （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [动画](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)
- [AnimationExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)
