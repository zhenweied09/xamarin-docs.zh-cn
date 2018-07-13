---
title: 自定义项
description: Xamarin.Forms 条目控制允许单个行的文本进行编辑。 本文演示如何创建自定义呈现器对于项控件，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 30326b8d52f39268015bdcbee1b84b9d9e5516b9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998955"
---
# <a name="customizing-an-entry"></a>自定义项

_Xamarin.Forms 条目控制允许单个行的文本进行编辑。本文演示如何创建自定义呈现器对于项控件，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。_

每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。 当[ `Entry` ](xref:Xamarin.Forms.Entry) Xamarin.Forms 应用程序，在 iOS 中呈现控件`EntryRenderer`类实例化时，后者又在实例化本机`UITextField`控件。 在 Android 平台上`EntryRenderer`类实例化`EditText`控件。 在通用 Windows 平台 (UWP)，`EntryRenderer`类实例化`TextBox`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了之间的关系[ `Entry` ](xref:Xamarin.Forms.Entry)控制和相应的本机控件可实现它：

![](entry-images/entry-classes.png "进入控制与实现的本机控件之间的关系")

渲染过程时可以执行利用通过创建自定义呈现器为实现特定于平台的自定义[ `Entry` ](xref:Xamarin.Forms.Entry)每个平台上的控件。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Entry_Control)Xamarin.Forms 自定义控件。
1. [使用](#Consuming_the_Custom_Control)Xamarin.Forms 中的自定义控件。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的控件的自定义呈现器。

每个项将现在讨论反过来，实现[ `Entry` ](xref:Xamarin.Forms.Entry)控件的每个平台有不同的背景色。

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>创建自定义项控件

自定义[ `Entry` ](xref:Xamarin.Forms.Entry)控制可以创建通过子类化`Entry`控制，如下面的代码示例中所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry`控件中创建.NET Standard 库项目，只需[ `Entry` ](xref:Xamarin.Forms.Entry)控件。 自定义控件将执行中的自定义呈现器，因此在不需要进行任何其他实现`MyEntry`控件。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自定义控件

`MyEntry`控件可以在 XAML 中引用.NET Standard 库项目通过声明其位置的命名空间和控件元素上使用的命名空间前缀。 下面的代码示例演示如何将`MyEntry`控件可供 XAML 页：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配的自定义控件的详细信息。 一旦声明的命名空间前缀用于引用自定义控件。

下面的代码示例演示如何将`MyEntry`控件可供 C# 页：

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

此代码实例化新[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)对象，它将显示[ `Label` ](xref:Xamarin.Forms.Label)和`MyEntry`控件居中垂直和水平页上。

自定义呈现器现在可以添加到每个平台上的控件的外观进行自定义的每个应用程序项目。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建一个子类`EntryRenderer`呈现本机控件的类。
1. 重写`OnElementChanged`呈现本机控件和写入逻辑自定义控件的方法。 创建相应的 Xamarin.Forms 控件时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定它将用于呈现 Xamarin.Forms 控件属性。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 若要提供每个平台项目中的自定义呈现器可以选择它。 如果未注册的自定义呈现器，则将使用默认的呈现器的控件的基类。

下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](entry-images/solution-structure.png "MyEntry 自定义呈现器项目职责")

`MyEntry`由特定于平台的呈现控件`MyEntryRenderer`类，它们都派生自`EntryRenderer`为每个平台的类。 这会导致每个`MyEntry`控制呈现具有特定于平台的背景色，如以下屏幕截图中所示：

![](entry-images/screenshots.png "每个平台上 MyEntry 控件")

`EntryRenderer`类公开`OnElementChanged`创建 Xamarin.Forms 控件以呈现相应的本机控件时调用的方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*附加到，和 Xamarin.Forms 元素的呈现器*是*附加到分别。 在示例应用程序`OldElement`属性将为`null`并`NewElement`属性将包含对引用`MyEntry`控件。

重写的版本`OnElementChanged`中的方法`MyEntryRenderer`类是执行的本机控件自定义的位置。 可以通过访问对正在使用在平台上的本机控件的类型化的引用`Control`属性。 此外，通过获取对所呈现的 Xamarin.Forms 控件的引用`Element`属性，尽管在示例应用程序不使用它。

每个自定义呈现器类用修饰`ExportRenderer`与 Xamarin.Forms 结合注册呈现器的属性。 该属性采用两个参数 – 正在呈现的 Xamarin.Forms 控件的类型名称和自定义呈现器的类型名称。 `assembly`到的属性的前缀指定特性应用于整个程序集。

以下各节讨论的每个特定于平台的实现`MyEntryRenderer`自定义呈现器类。

### <a name="creating-the-custom-renderer-on-ios"></a>在 ios 设备上创建自定义呈现器

下面的代码示例显示了为 iOS 平台的自定义呈现器：

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

对基类的调用`OnElementChanged`方法实例化 iOS`UITextField`控件，与分配给呈现器的控件的引用`Control`属性。 然后，将背景色设置为与淡紫色`UIColor.FromRGB`方法。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

对基类的调用`OnElementChanged`方法实例化 Android`EditText`控件，与分配给呈现器的控件的引用`Control`属性。 然后，将背景色设置为与浅绿色`Control.SetBackgroundColor`方法。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

下面的代码示例显示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

对基类的调用`OnElementChanged`方法实例化`TextBox`控件，与分配给呈现器的控件的引用`Control`属性。 背景色设置为青色通过创建`SolidColorBrush`实例。

## <a name="summary"></a>总结

本文演示了如何创建自定义控件呈现器适用于 Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)控件，使开发人员能够重写默认本机呈现具有其自己特定于平台的呈现。 自定义呈现器提供自定义的 Xamarin.Forms 控件外观的强大方法。 它们可用于小型样式更改或复杂的特定于平台的布局和行为自定义。


## <a name="related-links"></a>相关链接

- [CustomRendererEntry （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
