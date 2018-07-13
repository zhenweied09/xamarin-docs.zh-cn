---
title: 实现视图
description: 此文章介绍了如何创建 Xamarin.Forms 自定义控件用于显示来自设备的摄像机的预览视频流的自定义呈现器。
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994697"
---
# <a name="implementing-a-view"></a>实现视图

_Xamarin.Forms 自定义用户界面控件应派生视图类，用于放置布局和屏幕上的控件。本文演示如何创建 Xamarin.Forms 自定义控件用于显示来自设备的摄像机的预览视频流的自定义呈现器。_

每个 Xamarin.Forms 视图都会随附的呈现器为每个平台创建本机控件的实例。 当[ `View` ](xref:Xamarin.Forms.View) Xamarin.Forms 应用程序在 iOS 中，呈现`ViewRenderer`类实例化时，这反过来实例化本机`UIView`控件。 在 Android 平台上`ViewRenderer`类实例化本机`View`控件。 在通用 Windows 平台 (UWP)，`ViewRenderer`类实例化本机`FrameworkElement`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了之间的关系[ `View` ](xref:Xamarin.Forms.View)和相应的本机控件实现它：

![](view-images/view-classes.png "视图类和其实现的本机类之间的关系")

呈现过程可用于通过创建自定义呈现器为实现特定于平台的自定义[ `View` ](xref:Xamarin.Forms.View)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Control)Xamarin.Forms 自定义控件。
1. [使用](#Consuming_the_Custom_Control)Xamarin.Forms 中的自定义控件。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的控件的自定义呈现器。

每个项将现在讨论反过来，若要实现`CameraPreview`显示来自设备的摄像机的预览视频流的呈现器。 点击视频流将停止并启动它。

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>创建自定义控件

可以通过子类化创建一个自定义控件[ `View` ](xref:Xamarin.Forms.View)类，如下面的代码示例中所示：

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

`CameraPreview`自定义控件的可移植类库 (PCL) 项目中创建和定义用于控制的 API。 自定义控件公开`Camera`用于控制是否应从前面或背面摄像头设备上的显示视频流的属性。 如果指定的值不是`Camera`属性创建控件时，它默认为指定背面摄像头。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自定义控件

`CameraPreview`自定义控件可以在 XAML 中引用 PCL 项目中通过声明其位置的命名空间和自定义控件元素上使用的命名空间前缀。 下面的代码示例演示如何将`CameraPreview`自定义控件可供 XAML 页：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配的自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

下面的代码示例演示如何将`CameraPreview`自定义控件可供 C# 页：

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

实例`CameraPreview`自定义控件用于显示来自设备的摄像机的预览视频流。 除了可以选择指定的值`Camera`属性，自定义控件将执行自定义呈现器中。

自定义呈现器现在可以添加到每个应用程序项目来创建特定于平台的相机预览控件。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建一个子类`ViewRenderer<T1,T2>`呈现自定义控件的类。 第一个类型参数应为自定义控件呈现器，在这种情况下是`CameraPreview`。 第二个类型参数应将实现自定义控件的本机控件。
1. 重写`OnElementChanged`呈现其进行自定义的自定义控件和写入逻辑的方法。 创建相应的 Xamarin.Forms 控件时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定它将用于呈现 Xamarin.Forms 自定义控件属性。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，它是可选提供每个平台项目中的自定义呈现器。 如果未注册的自定义呈现器，则将使用默认的呈现器的控件的基类。 但是，自定义呈现器呈现时所需的每个平台项目中[视图](xref:Xamarin.Forms.View)元素。

下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](view-images/solution-structure.png "CameraPreview 自定义呈现器项目职责")

`CameraPreview`自定义控件呈现的特定于平台的呈现器类，它们都派生自`ViewRenderer`为每个平台的类。 这会导致每个`CameraPreview`自定义控件呈现与特定于平台的控件，如以下屏幕截图中所示：

![](view-images/screenshots.png "每个平台上 CameraPreview")

`ViewRenderer`类公开`OnElementChanged`创建 Xamarin.Forms 自定义控件来呈现相应的本机控件时调用的方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*附加到，和 Xamarin.Forms 元素的呈现器*是*附加到分别。 在示例应用程序，`OldElement`属性将为`null`并`NewElement`属性将包含对引用`CameraPreview`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行的本机控件实例化和自定义的位置。 `SetNativeControl`应使用方法来实例化本机控件，此方法还会将分配到的控件引用`Control`属性。 此外，通过获取对所呈现的 Xamarin.Forms 控件的引用`Element`属性。

在某些情况下，`OnElementChanged`可以多次调用方法。 因此，若要防止内存泄漏，必须格外小心实例化新的本机控件时。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。 仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应配置该控件并订阅事件处理程序。 同样，任何已订阅的事件处理程序只应呈现器附加到的元素发生更改时取消订阅。 采用此方法将有助于创建不会遭受内存泄漏的高性能自定义呈现器。

每个自定义呈现器类用修饰`ExportRenderer`与 Xamarin.Forms 结合注册呈现器的属性。 该属性采用两个参数 – 正在呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到的属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 ios 设备上创建自定义呈现器

下面的代码示例显示了为 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

前提`Control`属性是`null`，则`SetNativeControl`调用方法来实例化一个新`UICameraPreview`控件并将分配给它的引用`Control`属性。 `UICameraPreview`控件是使用一个特定于平台的自定义控件`AVCapture`Api 以提供从相机预览流。 它公开`Tapped`处理的事件，`OnCameraPreviewTapped`方法来停止和启动视频预览点击它时。 `Tapped`自定义呈现器附加到新 Xamarin.Forms 元素，并且已取消订阅仅元素呈现器附加到更改时，对订阅的事件。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

前提`Control`属性是`null`，则`SetNativeControl`调用方法来实例化一个新`CameraPreview`控制并为其分配一个引用`Control`属性。 `CameraPreview`控件是使用一个特定于平台的自定义控件`Camera`API 以提供从相机预览流。 `CameraPreview`然后配置控制，前提是自定义呈现器附加到新 Xamarin.Forms 元素。 此配置涉及到创建一个新的本机`Camera`对象，以便访问特定的硬件照相机，并注册事件处理程序来处理`Click`事件。 反过来此处理程序将停止和启动视频预览，点击它时。 `Click`如果 Xamarin.Forms 元素呈现器附加到更改从订阅的事件，将取消。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

下面的代码示例显示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

前提`Control`属性是`null`，一个新`CaptureElement`实例化并`SetupCamera`调用方法时，使用`MediaCapture`API 以提供从相机预览流。 `SetNativeControl`然后，调用方法的引用分配`CaptureElement`实例向`Control`属性。 `CaptureElement`控件公开`Tapped`处理的事件，`OnCameraPreviewTapped`方法来停止和启动视频预览点击它时。 `Tapped`自定义呈现器附加到新 Xamarin.Forms 元素，并且已取消订阅仅元素呈现器附加到更改时，对订阅的事件。

> [!NOTE]
> 若要停止和释放的对象，它提供对 UWP 应用程序中相机的访问至关重要。 如果不这样做可能会影响其他应用程序尝试访问设备的照相机。 有关详细信息，请参阅[显示摄像头预览](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>总结

本文演示了如何创建 Xamarin.Forms 自定义控件用于显示来自设备的摄像机的预览视频流的自定义呈现器。 Xamarin.Forms 自定义用户界面控件应派生自[ `View` ](xref:Xamarin.Forms.View)类，该类用于将布局和屏幕上的控件。


## <a name="related-links"></a>相关链接

- [CustomRendererView （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
