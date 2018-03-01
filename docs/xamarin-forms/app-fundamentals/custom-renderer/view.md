---
title: "实现视图"
description: "Xamarin.Forms 自定义用户界面控件应派生自视图类，用于放置布局和在屏幕上的控件。 本文演示如何创建自定义呈现器的 Xamarin.Forms 自定义控件，用于显示来自设备的照相机的预览视频流。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: d5f9f86447886e2cea46a6317d05506cdbed90bb
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="implementing-a-view"></a>实现视图

_Xamarin.Forms 自定义用户界面控件应派生自视图类，用于放置布局和在屏幕上的控件。本文演示如何创建自定义呈现器的 Xamarin.Forms 自定义控件，用于显示来自设备的照相机的预览视频流。_

Xamarin.Forms 中的每个视图已随附的呈现器针对每个平台创建的本机控件的实例。 当[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) Xamarin.Forms 应用程序在 iOS 中，呈现`ViewRenderer`该类进行实例化，这反过来实例化一个本机`UIView`控件。 在 Android 平台上，`ViewRenderer`类实例化一个本机`View`控件。 在 Windows Phone 和通用 Windows 平台 (UWP) 上`ViewRenderer`类实例化一个本机`FrameworkElement`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)和相应的本机控件实现它：

![](view-images/view-classes.png "视图类和其实现的本机类之间的关系")

呈现进程可以用于通过创建自定义呈现器实现特定于平台的自定义项[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Control)Xamarin.Forms 自定义控件。
1. [使用](#Consuming_the_Custom_Control)向 Xamarin.Forms 的自定义控件。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)每个平台上的控件的自定义呈现器。

每个项将现在讨论反过来，若要实现`CameraPreview`显示来自设备的照相机的预览视频流的呈现器。 点击视频流将会停止并启动它。

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>创建自定义控件

可以通过子类化创建一个自定义控件[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类，如下面的代码示例中所示：

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

`CameraPreview`自定义控件在可移植类库 (PCL) 项目中创建并定义控件 API。 自定义控件可公开`Camera`用于控制是否应从前端或后端设备上的照相机显示视频流的属性。 如果未指定值`Camera`属性创建控件时，它默认为指定前后的照相机。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自定义控件

`CameraPreview`自定义控件可以引用 XAML 中 PCL 项目中通过声明其位置的命名空间和自定义控件元素上使用的命名空间前缀。 下面的代码示例演示如何`CameraPreview`自定义控件可供 XAML 页：

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

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

下面的代码示例演示如何`CameraPreview`自定义控件可供 C# 页：

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

实例`CameraPreview`自定义控件用于显示设备的照相机的预览视频流。 除了可以选择指定的值外`Camera`属性，自定义控件将执行自定义呈现器中。

自定义呈现器现在可以添加到每个应用程序项目来创建特定于平台的相机预览控件。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`ViewRenderer<T1,T2>`呈现自定义控件的类。 第一个类型参数应为自定义控件呈现器，在这种情况下是`CameraPreview`。 第二个类型参数应将实现自定义控件的本机控件。
1. 重写`OnElementChanged`呈现它进行自定义的自定义控件和写入的逻辑的方法。 创建相应的 Xamarin.Forms 控件时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定，它将用于呈现 Xamarin.Forms 自定义控件属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> **请注意**： 对于大多数 Xamarin.Forms 元素，它是可选的提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用控件的基类的默认呈现器。 但是，自定义呈现器需要每个平台项目中呈现时[视图](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)元素。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](view-images/solution-structure.png "CameraPreview 自定义呈现器项目职责")

`CameraPreview`自定义控件呈现特定于平台的呈现器类，该类的所有派生自通过`ViewRenderer`为每个平台的类。 这将导致每个`CameraPreview`自定义控件呈现与特定于平台的控件，如以下屏幕截图中所示：

![](view-images/screenshots.png "每个平台上的 CameraPreview")

`ViewRenderer`类会公开`OnElementChanged`方法，该创建 Xamarin.Forms 自定义控件呈现相应的本机控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序，`OldElement`属性将为`null`和`NewElement`属性将包含对引用`CameraPreview`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行的本机控件实例化和自定义项的位置。 `SetNativeControl`方法应用于实例化本机控件，并且此方法还将分配到的控件引用`Control`属性。 此外，可以通过获取对呈现 Xamarin.Forms 控件的引用`Element`属性。

在某些情况下，`OnElementChanged`可以多次调用方法。 因此，若要防止内存泄漏，必须格外小心时实例化一个新的本机控件。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

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

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。 仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应配置该控件并订阅事件处理程序。 同样，任何已订阅的事件处理程序只应从呈现器附加到的元素的更改时取消订阅。 采用此方法将有助于创建不会受到内存泄漏的高性能的自定义呈现器。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

下面的代码示例演示为 iOS 平台的自定义呈现器：

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

假设`Control`属性是`null`、`SetNativeControl`调用方法来实例化一个新`UICameraPreview`控件并将分配给它的引用`Control`属性。 `UICameraPreview`控件是使用一个特定于平台的自定义控件`AVCapture`Api 提供相机的预览流。 它公开`Tapped`处理的事件，`OnCameraPreviewTapped`方法来停止和启动视频预览时它点击。 `Tapped`自定义呈现器是附加到一个新的 Xamarin.Forms 元素，且取消订阅只有元素呈现器附加到更改时订阅事件。

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

假设`Control`属性是`null`、`SetNativeControl`调用方法来实例化一个新`CameraPreview`控制并分配给它的引用`Control`属性。 `CameraPreview`控件是使用一个特定于平台的自定义控件`Camera`API 提供相机的预览流。 `CameraPreview`控件然后配置，前提是自定义呈现器附加到新 Xamarin.Forms 元素。 此配置涉及到创建一个新的本机`Camera`对象，以便访问特定硬件相机、 并注册事件处理程序处理`Click`事件。 反过来此处理程序将停止和启动视频预览版，当它点击。 `Click`如果 Xamarin.Forms 元素呈现器附加到更改从订阅的事件，将取消。

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>在 Windows Phone 上创建自定义呈现器和 UWP

下面的代码示例显示了 Windows Phone 和 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        MediaCapture mediaCapture;
        CaptureElement captureElement;
        CameraOptions cameraOptions;
        Application app;
        bool isPreviewing = false;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                ...
                captureElement = new CaptureElement ();
                captureElement.Stretch = Stretch.UniformToFill;

                InitializeAsync ();
                SetNativeControl (captureElement);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped (object sender, TappedRoutedEventArgs e)
        {
            if (isPreviewing) {
                await StopPreviewAsync ();
            } else {
                await StartPreviewAsync ();
            }
        }
        ...
    }
}
```

假设`Control`属性是`null`，新`CaptureElement`实例化和`InitializeAsync`调用方法时，它使用`MediaCapture`API 提供相机的预览流。 `SetNativeControl`然后调用方法来分配对引用`CaptureElement`到实例`Control`属性。 `CaptureElement`控件公开`Tapped`处理的事件，`OnCameraPreviewTapped`方法来停止和启动视频预览时它点击。 `Tapped`自定义呈现器是附加到一个新的 Xamarin.Forms 元素，且取消订阅只有元素呈现器附加到更改时订阅事件。

> [!NOTE]
> **请注意**： 必须停止并处理提供对相机沿一个 Windows Phone 或 UWP 应用程序的访问的对象。 如果不这样做可能会干扰其他应用程序尝试访问设备的照相机。 有关详细信息，请参阅和[快速入门： 使用 MediaCapture API 捕获视频](https://msdn.microsoft.com/library/windows/apps/xaml/dn642092.aspx)对于 Windows 运行时应用程序，和[显示相机预览](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)UWP 应用程序。

## <a name="summary"></a>摘要

本文演示了如何创建自定义呈现器的 Xamarin.Forms 自定义控件，用于显示来自设备的照相机的预览视频流。 Xamarin.Forms 自定义用户界面控件应派生自[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类，该类用于放置布局和在屏幕上的控件。


## <a name="related-links"></a>相关链接

- [CustomRendererView （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
