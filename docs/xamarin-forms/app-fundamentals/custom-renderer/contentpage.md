---
title: "自定义内容页"
description: "内容页是一个显示的单个视图，且占用大部分屏幕的可见元素。 本文演示如何创建自定义呈现器内容页页中，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: d7f7e031d91cd1505ee255bbf0d25198bd9ae82a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-a-contentpage"></a>自定义内容页

_内容页是一个显示的单个视图，且占用大部分屏幕的可见元素。本文演示如何创建自定义呈现器内容页页中，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。_

Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。 当[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) Xamarin.Forms 应用程序，在 iOS 中呈现`PageRenderer`该类进行实例化，这反过来实例化一个本机`UIViewController`控件。 在 Android 平台上，`PageRenderer`类实例化`ViewGroup`控件。 在 Windows Phone 和通用 Windows 平台 (UWP) 上`PageRenderer`类实例化`FrameworkElement`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)和相应的本机控件实现它：

![](contentpage-images/contentpage-classes.png "内容页类和实现本机控件之间的关系")

呈现进程可以拍摄利用来实现特定于平台的自定义项创建的自定义呈现器[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Xamarin.Forms_Page)Xamarin.Forms 页。
1. [使用](#Consuming_the_Xamarin.Forms_Page)来自 Xamarin.Forms 的页。
1. [创建](#Creating_the_Page_Renderer_on_each_Platform)为每个平台上的页面的自定义呈现器。

每个项将现在讨论反过来，若要实现`CameraPage`，提供实时的相机源和捕获照片的能力。

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>创建 Xamarin.Forms 页

未更改[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)可以添加到共享的 Xamarin.Forms 项目中中,，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同样，代码隐藏文件[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)还应保持不变的如下面的代码示例中所示：

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

下面的代码示例演示如何在 C# 中创建页面：

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

实例`CameraPage`将用于显示实时源每个平台上的相机。 自定义控件将执行在自定义呈现器，因此没有其他的实现需要在`CameraPage`类。

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>使用 Xamarin.Forms 页

空`CameraPage`Xamarin.Forms 应用程序必须显示。 上的按钮时将发生这种情况`MainPage`点击实例，其中又执行`OnTakePhotoButtonClicked`方法，如下面的代码示例中所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此代码只需导航到`CameraPage`上的自定义呈现器将自定义每个平台上的页面的外观。

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>在每个平台上创建页呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`PageRenderer`类。
1. 重写`OnElementChanged`呈现的本机的页和写入逻辑，以自定义页面的方法。 `OnElementChanged`创建相应的 Xamarin.Forms 控件时，调用方法。
1. 添加`ExportRenderer`到页呈现器类，以指定它将可用于呈现 Xamarin.Forms 页属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> 它是可选若要提供每个平台项目中的页呈现器。 如果未注册页呈现器，则将使用页面的默认呈现器。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](contentpage-images/solution-structure.png "CameraPage 自定义呈现器项目职责")

`CameraPage`实例由特定于平台的呈现`CameraPageRenderer`类，该类的所有派生自`PageRenderer`该平台的类。 这将导致每个`CameraPage`实例呈现具有实时相机源，如以下屏幕截图中所示：

![](contentpage-images/screenshots.png "每个平台上的 CameraPage")

`PageRenderer`类会公开`OnElementChanged`方法，该 Xamarin.Forms 页创建呈现相应的本机控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序`OldElement`属性将为`null`和`NewElement`属性将包含对引用`CameraPage`实例。

重写的版本`OnElementChanged`中的方法`CameraPageRenderer`类是执行本机页自定义的位置。 可以通过获取对呈现 Xamarin.Forms 页实例的引用`Element`属性。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 页的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论的实现`CameraPageRenderer`为每个平台的自定义呈现器。

### <a name="creating-the-page-renderer-on-ios"></a>在 iOS 上创建页呈现器

下面的代码示例演示为 iOS 平台的页呈现器：

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"          ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

调用基类的`OnElementChanged`方法实例化 iOS`UIViewController`控件。 前提被呈现器不已附加到现有 Xamarin.Forms 元素，且前提是存在的页实例，呈现的自定义呈现器仅呈现实时的相机流。

然后通过一系列使用的方法自定义页面`AVCapture`Api 以提供从相机和捕获照片的实时流。

### <a name="creating-the-page-renderer-on-android"></a>创建在 Android 上的页呈现器

下面的代码示例显示了 Android 平台的页呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"           ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

调用基类的`OnElementChanged`方法实例化 Android`ViewGroup`控件，它是一组视图。 前提被呈现器不已附加到现有 Xamarin.Forms 元素，且前提是存在的页实例，呈现的自定义呈现器仅呈现实时的相机流。

然后通过调用一系列使用的方法自定义页面`Camera`API 来提供从相机和之前捕获照片的实时流`AddView`方法调用以添加实时相机流式传输到 UI `ViewGroup`。

### <a name="creating-the-page-renderer-on-windows-phone"></a>在 Windows Phone 上创建页呈现器

下面的代码示例演示 Windows Phone 平台的页呈现器：

```csharp
[assembly: ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                ...
                var container = ContainerElement as Canvas;

                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                container.Children.Add (page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}
```

调用基类的`OnElementChanged`方法实例化 Windows Phone`Canvas`页面呈现在其的控件。 前提被呈现器不已附加到现有 Xamarin.Forms 元素，且前提是存在的页实例，呈现的自定义呈现器仅呈现实时的相机流。

在 Windows Phone 平台上，可以通过访问在平台上使用本机页上的类型化的引用`ContainerElement`属性，与`Canvas`控制对类型化的引用`FrameworkElement`。 然后通过调用一系列使用的方法自定义页面`MediaCapture`API 来提供从相机和自定义的页面添加到之前捕获照片的实时流`Canvas`进行显示。

实现派生自的自定义呈现器时`PageRenderer`对 Windows 运行时，`ArrangeOverride`方法还应实现，来排列这些页控件，因为基呈现器不知道如何处理它们。 否则，结果空白页。 因此，在此示例`ArrangeOverride`方法调用`Arrange`方法`Page`实例。

> [!NOTE]
> 务必停止和释放的对象，它提供对 Windows Phone 8.1 WinRT 应用程序中的照相机的访问。 如果不这样做可能会干扰其他应用程序尝试访问设备的照相机。 有关详细信息，请参阅`CleanUpCaptureResourcesAsync`方法中的 Windows Phone 项目中的示例解决方案，和[快速入门： 使用 MediaCapture API 捕获视频](https://msdn.microsoft.com/library/windows/apps/xaml/dn642092.aspx)。

### <a name="creating-the-page-renderer-on-uwp"></a>在 UWP 上创建页呈现器

下面的代码示例演示 UWP 的页呈现器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupCamera();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

调用基类的`OnElementChanged`方法实例化`FrameworkElement`页面呈现在其的控件。 前提被呈现器不已附加到现有 Xamarin.Forms 元素，且前提是存在的页实例，呈现的自定义呈现器仅呈现实时的相机流。 然后通过调用一系列使用的方法自定义页面`MediaCapture`API 来提供从相机和自定义的页面添加到之前捕获照片的实时流`Children`显示的集合。

实现派生自的自定义呈现器时`PageRenderer`上 UWP，`ArrangeOverride`方法还应实现，来排列这些页控件，因为基呈现器不知道如何处理它们。 否则，结果空白页。 因此，在此示例`ArrangeOverride`方法调用`Arrange`方法`Page`实例。

> [!NOTE]
> 务必停止和释放的对象，它提供对相机沿 UWP 应用程序的访问。 如果不这样做可能会干扰其他应用程序尝试访问设备的照相机。 有关详细信息，请参阅[显示相机预览](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>摘要

本文演示了如何创建自定义呈现器[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)页上，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。 A`ContentPage`是一种显示单个视图，且占用大部分屏幕的可视元素。


## <a name="related-links"></a>相关链接

- [CustomRendererContentPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
