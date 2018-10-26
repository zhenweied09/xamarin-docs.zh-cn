---
title: 添加轻扫手势识别器
description: 本文介绍如何识别上一个视图发生轻扫手势。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130948"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>添加轻扫手势识别器

_轻扫手势手指在水平或垂直方向，在屏幕上移动，并且通常用于初始化通过内容导航时发生。在本文中的代码示例摘自[轻扫手势](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)示例。_

若要使[ `View` ](xref:Xamarin.Forms.View)识别轻扫手势，创建[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)实例，设置[ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction)属性设置为[ `SwipeDirection`](xref:Xamarin.Forms.SwipeDirection)枚举值 (`Left`， `Right`， `Up`，或`Down`)，根据需要设置[ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)属性、 句柄[ `Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件，并添加到新的笔势识别器[ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)在视图上的集合。 下面的代码示例演示`SwipeGestureRecognizer`附加到[ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

下面是等效的C#代码：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)类还包括[ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)属性，可根据需要设置为`uint`值，该值表示为而必须实现的最小轻扫距离轻扫，若要在设备无关的单位中识别。 此属性的默认值为 100，也就是说，任何扫的少于 100 个与设备无关单位将被忽略。

## <a name="recognizing-the-swipe-direction"></a>识别轻扫方向

在上面的示例[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)属性设置为单个值从[ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection)枚举。 但是，还有可能要将此属性设置为从多个值`SwipeDirection`枚举，以便[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件激发以响应在多个方向轻扫。 但是，约束是单个[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)只能识别扫发生在同一个轴上。 因此，可以通过设置识别发生在水平轴的扫`Direction`属性设置为`Left`和`Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同样，可以通过设置识别垂直轴发生的扫[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)属性设置为`Up`和`Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者， [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)为每个轻扫可以创建方向识别扫中每个方向：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

下面是等效的C#代码：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> 在上述示例中，同一个事件处理程序响应[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件触发。 但是，每个[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)实例，如果需要，也可以使用不同的事件处理程序。

## <a name="responding-to-the-swipe"></a>轻扫响应

事件处理程序[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件在下面的示例所示：

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

[ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs)可以检查以确定方向轻扫，使用响应所需轻扫的自定义逻辑。 可以从获取方向轻扫[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)属性的事件自变量，都将设置为的值之一[ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection)枚举。 此外，事件参数还具有[ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter)将设置为的值的属性[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)属性，如果定义。

## <a name="using-commands"></a>使用命令

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)类还包括[ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command)并[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)属性。 使用模型-视图-视图模型 (MVVM) 模式的应用程序中通常使用这些属性。 `Command`属性定义`ICommand`轻扫手势识别时, 要调用与`CommandParameter`属性定义的对象传递给`ICommand.`下面的代码示例演示如何将绑定`Command`属性设置为`ICommand`其实例设置为网页的视图模型中定义[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

是等效的 XAML 代码：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` 是类型的属性`ICommand`设置为网页的视图模型实例中定义[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 轻扫手势识别时，`Execute`方法的`SwipeCommand`将执行对象。 参数`Execute`method 的值是[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)属性。 有关命令的详细信息，请参阅[命令界面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>正在创建轻扫容器

`SwipeContainer`类，下面的代码示例中所示，是将一个通用轻扫识别类封装[ `View` ](xref:Xamarin.Forms.View)执行轻扫手势识别：

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

`SwipeContainer`类创建[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)对象的所有四个轻扫方向，并将附加`Swipe`事件处理程序。 这些事件处理程序调用`Swipe`事件定义的`SwipeContainer`。

以下 XAML 代码示例所示`SwipeContainer`类包装[ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

下面的代码示例演示如何`SwipeContainer`包装[ `BoxView` ](xref:Xamarin.Forms.BoxView)在C#页：

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

当[ `BoxView` ](xref:Xamarin.Forms.BoxView)接收轻扫手势[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)中的事件[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)激发。 这将由`SwipeContainer`类，该类会触发其自己`Swipe`事件。 这`Swipe`页面上处理事件。 [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs)之后进行检查以确定方向轻扫，使用响应所需轻扫的自定义逻辑。

## <a name="related-links"></a>相关链接

- [轻扫手势 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
