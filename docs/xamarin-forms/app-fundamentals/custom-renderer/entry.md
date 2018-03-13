---
title: "自定义项"
description: "Xamarin.Forms 进入控制允许单个行要编辑的文本。 本文演示如何创建自定义呈现器对于条目控件，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0fe7aa2316abaae33da753e99a6f2eca4870d2d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-an-entry"></a>自定义项

_Xamarin.Forms 进入控制允许单个行要编辑的文本。本文演示如何创建自定义呈现器对于条目控件，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。_

Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。 当[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) Xamarin.Forms 应用程序，在 iOS 中呈现控件`EntryRenderer`实例化类，后者又在实例化一个本机`UITextField`控件。 在 Android 平台上，`EntryRenderer`类实例化`EditText`控件。 在 Windows Phone 和通用 Windows 平台 (UWP) 上`EntryRenderer`类实例化`TextBox`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件和相应的本机控件实现它：

![](entry-images/entry-classes.png "进入控制和实现本机控件之间的关系")

呈现进程可以拍摄利用来实现特定于平台的自定义项创建的自定义呈现器[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)每个平台上的控件。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Entry_Control)Xamarin.Forms 自定义控件。
1. [使用](#Consuming_the_Custom_Control)向 Xamarin.Forms 的自定义控件。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的控件的自定义呈现器。

每个项将现在讨论反过来，若要实现[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件在每个平台有不同的背景色。

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>创建自定义条目控件

自定义[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)可以通过子类化创建控件`Entry`控制，如下面的代码示例中所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry`控件在可移植类库 (PCL) 项目中创建，只需[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件。 自定义控件将执行在自定义呈现器，因此没有其他的实现需要在`MyEntry`控件。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自定义控件

`MyEntry`控件可以引用 XAML 中 PCL 项目中通过声明其位置的命名空间和控制元素上使用的命名空间前缀。 下面的代码示例演示如何`MyEntry`控件可供 XAML 页：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配自定义控件的详细信息。 一旦声明的命名空间前缀用于引用自定义控件。

下面的代码示例演示如何`MyEntry`控件可供 C# 页：

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

此代码实例化一个新[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)将显示的对象[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和`MyEntry`控件同时垂直和水平方向在页面上居中。

自定义呈现器现在可以添加到每个平台上的控件的外观进行自定义每个应用程序项目。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`EntryRenderer`呈现本机控件的类。
1. 重写`OnElementChanged`呈现自定义控件的本机控件和写入的逻辑的方法。 创建相应的 Xamarin.Forms 控件时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定，它将用于呈现 Xamarin.Forms 控件属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> 它是可选若要提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用控件的基类的默认呈现器。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](entry-images/solution-structure.png "MyEntry 自定义呈现器项目职责")

`MyEntry`时呈现控件，通过特定于平台的`MyEntryRenderer`类，该类的所有派生自`EntryRenderer`为每个平台的类。 这将导致每个`MyEntry`控制呈现使用特定于平台的背景色，如以下屏幕截图中所示：

![](entry-images/screenshots.png "每个平台上的 MyEntry 控件")

`EntryRenderer`类会公开`OnElementChanged`方法，该创建 Xamarin.Forms 控件呈现相应的本机控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序`OldElement`属性将为`null`和`NewElement`属性将包含对引用`MyEntry`控件。

重写的版本`OnElementChanged`中的方法`MyEntryRenderer`类是执行本机控件自定义的位置。 可以通过访问在平台上使用本机控件的类型化的引用`Control`属性。 此外，可以通过获取对呈现 Xamarin.Forms 控件的引用`Element`属性，不过它不用于在示例应用程序。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 控件的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论的每个特定于平台的实现`MyEntryRenderer`自定义呈现器类。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

下面的代码示例演示为 iOS 平台的自定义呈现器：

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

调用基类的`OnElementChanged`方法实例化 iOS`UITextField`具有对分配给的呈现器控件的引用控件`Control`属性。 然后，将背景色设置为与浅色紫色`UIColor.FromRGB`方法。

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

调用基类的`OnElementChanged`方法实例化 Android`EditText`具有对分配给的呈现器控件的引用控件`Control`属性。 然后，将背景色设置为与浅绿色`Control.SetBackgroundColor`方法。

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>在 Windows Phone 上创建自定义呈现器和 UWP

下面的代码示例显示了 Windows Phone 和 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.WinPhone81
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

调用基类的`OnElementChanged`方法实例化`TextBox`具有对分配给的呈现器控件的引用控件`Control`属性。 背景色设置为青色通过创建`SolidColorBrush`实例。

## <a name="summary"></a>摘要

本文演示了如何创建 Xamarin.Forms 自定义控件呈现器[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件，使开发人员能够重写默认本机呈现了其自己的特定于平台的呈现。 自定义呈现器提供强大的自定义 Xamarin.Forms 控件的外观的方法。 它们可以用于小样式更改或复杂的特定于平台的布局和行为自定义项。


## <a name="related-links"></a>相关链接

- [CustomRendererEntry （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
