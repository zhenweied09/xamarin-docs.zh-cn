---
title: "在 XAML 中的传递自变量"
description: "本文演示如何使用可用于将参数传递到非默认构造函数，以调用工厂方法，并指定泛型自变量的类型的 XAML 属性。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2016
ms.openlocfilehash: a30dd9b33466ac6907322f8c6b586c012452a44f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="passing-arguments-in-xaml"></a>在 XAML 中的传递自变量

_本文演示如何使用可用于将参数传递到非默认构造函数，以调用工厂方法，并指定泛型自变量的类型的 XAML 属性。_

## <a name="overview"></a>概述

通常是使用构造函数需要参数，或通过调用静态创建方法的对象的实例化所必需的。 这可以通过在 XAML 中使用`x:Arguments`和`x:FactoryMethod`属性：

- `x:Arguments`属性用于指定非默认的构造函数，或为工厂方法的对象声明的构造函数自变量。 有关详细信息，请参阅[传递构造函数自变量](#constructor_arguments)。
- `x:FactoryMethod`属性用于指定用于初始化的对象的工厂方法。 有关详细信息，请参阅[调用工厂方法](#factory_methods)。

此外，`x:TypeArguments`属性可以用于指定泛型类型的构造函数的泛型类型自变量。 有关详细信息，请参阅[指定泛型类型自变量](#generic_type_arguments)。

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>传递构造函数自变量

可以将自变量传递给非默认构造函数使用`x:Arguments`属性。 必须在 XML 元素表示的自变量类型分隔每个构造函数自变量。 Xamarin.Forms 基本类型支持以下元素：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

下面的代码示例演示如何使用`x:Arguments`具有三个属性[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)构造函数：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

中的元素数`x:Arguments`标记，以及这些元素的类型必须与一种[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)构造函数。 `Color` [构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)使用单个参数需要灰度值从 0 （黑色） 设置为 1 （白色）。 `Color` [构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)带有三个参数需要范围从 0 到 1 的红色、 绿色和蓝色值。 `Color` [构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)带四个参数将作为第四个参数添加 alpha 通道。

以下屏幕截图显示每个调用的结果[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)用指定的参数值的构造函数：

![](passing-arguments-images/passing-arguments.png "使用 x： 参数指定的 BoxView.Color")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>调用工厂方法

可以在 XAML 中通过指定方法的调用工厂方法命名为使用`x:FactoryMethod`特性，并且使用其自变量`x:Arguments`属性。 工厂方法是`public static`返回对象或值类型与类或结构，它定义的方法相同的方法。

[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)结构定义了多个工厂方法和下面的代码示例演示调用三人：

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                      
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

中的元素数`x:Arguments`标记，以及这些元素的类型必须匹配调用工厂方法的自变量。 [ `FromRgba` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/)工厂方法需要四个[ `Int32` ](https://developer.xamarin.com/api/type/System.Int32/)参数，这表示红、 绿、 蓝方和 alpha 值，分别范围从 0 到 255 之间。 [ `FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)工厂方法需要四个[ `Double` ](https://developer.xamarin.com/api/type/System.Double/)参数，这表示色调、 饱和度、 亮度和 alpha 值，分别范围从 0 到 1。 [ `FromHex` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/)工厂方法需要[ `String` ](https://developer.xamarin.com/api/type/System.String/)表示十六进制 (A) 代表 RGB 颜色。

以下屏幕截图显示每个调用的结果[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)工厂方法与指定的参数值：

![](passing-arguments-images/factory-methods.png "X:factorymethod 和 x： 参数指定的 BoxView.Color")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>指定泛型类型参数

可以使用指定泛型类型参数的泛型类型的构造函数`x:TypeArguments`特性，如下面的代码示例所示：

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="WinPhone, Windows" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)类是一个泛型类，必须要实例化的`x:TypeArguments`匹配的目标类型属性。 在[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)类， [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)属性可以接受单个`string`值或以逗号分隔的多个`string`值。 在此示例中， [ `StackLayout.Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)属性设置为特定于平台的[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)。

## <a name="summary"></a>摘要

这篇文章演示了使用的 XAML 特性，可以用于将自变量传递给非默认构造函数，以调用工厂方法，并指定泛型自变量的类型。


## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [传递构造函数自变量 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [调用工厂方法 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
