---
title: 自定义 ContentPage
description: 内容页是显示一个视图并占据屏幕的大部分的可视元素。 本文演示如何创建自定义呈现器对于 ContentPage 页中，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995737"
---
# <a name="customizing-a-contentpage"></a>自定义 ContentPage

_内容页是显示一个视图并占据屏幕的大部分的可视元素。本文演示如何创建自定义呈现器对于 ContentPage 页中，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。_

每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。 当[ `ContentPage` ](xref:Xamarin.Forms.ContentPage) Xamarin.Forms 应用程序，在 iOS 中呈现`PageRenderer`类实例化时，这反过来实例化本机`UIViewController`控件。 在 Android 平台上`PageRenderer`类实例化`ViewGroup`控件。 在通用 Windows 平台 (UWP)，`PageRenderer`类实例化`FrameworkElement`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了之间的关系[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)和相应的本机控件实现它：

![](contentpage-images/contentpage-classes.png "内容页类和实现的本机控件之间的关系")

渲染过程时可以执行利用通过创建自定义呈现器为实现特定于平台的自定义[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Xamarin.Forms_Page)Xamarin.Forms 页面。
1. [使用](#Consuming_the_Xamarin.Forms_Page)Xamarin.Forms 页。
1. [创建](#Creating_the_Page_Renderer_on_each_Platform)每个平台上的页的自定义呈现器。

每个项将现在讨论反过来，若要实现`CameraPage`，它提供了实时源的相机捕获照片的功能。

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>创建 Xamarin.Forms 页面

未更改[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)可以添加到共享的 Xamarin.Forms 项目中，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同样，有关的代码隐藏文件[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)还应保持不变的如下面的代码示例中所示：

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

实例`CameraPage`将用于显示实时相机源每个平台上。 自定义控件将执行中的自定义呈现器，因此在不需要进行任何其他实现`CameraPage`类。

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>使用 Xamarin.Forms 页面

空`CameraPage`Xamarin.Forms 应用程序必须显示。 上的按钮时将发生这种情况`MainPage`点击实例，它将依次执行`OnTakePhotoButtonClicked`方法，如下面的代码示例中所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此代码只需导航到`CameraPage`，哪些自定义呈现器将自定义每个平台上的页面的外观。

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>在每个平台上创建页的呈现器

创建自定义呈现器类的过程如下所示：

1. 创建一个子类`PageRenderer`类。
1. 重写`OnElementChanged`呈现的本机的页和写入逻辑，以自定义页面的方法。 `OnElementChanged`创建相应的 Xamarin.Forms 控件时调用方法。
1. 添加`ExportRenderer`属性到页呈现器类，以指定它将用于呈现 Xamarin.Forms 页面。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 若要提供每个平台项目中的页呈现器可以选择它。 如果未注册页的呈现器，则将使用默认的呈现器的页。

下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](contentpage-images/solution-structure.png "CameraPage 自定义呈现器项目职责")

`CameraPage`实例由特定于平台的呈现`CameraPageRenderer`类，它们都派生自`PageRenderer`该平台的类。 这会导致每个`CameraPage`实例呈现使用实时相机源，如以下屏幕截图中所示：

![](contentpage-images/screenshots.png "每个平台上 CameraPage")

`PageRenderer`类公开`OnElementChanged`创建 Xamarin.Forms 页呈现相应的本机控件时调用的方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*附加到，和 Xamarin.Forms 元素的呈现器*是*附加到分别。 在示例应用程序`OldElement`属性将为`null`并`NewElement`属性将包含对引用`CameraPage`实例。

重写的版本`OnElementChanged`中的方法`CameraPageRenderer`类是执行本机页自定义的位置。 可以通过获取对所呈现的 Xamarin.Forms 页实例的引用`Element`属性。

每个自定义呈现器类用修饰`ExportRenderer`与 Xamarin.Forms 结合注册呈现器的属性。 该属性采用两个参数 – 正在呈现的 Xamarin.Forms 页的类型名称和自定义呈现器的类型名称。 `assembly`到的属性的前缀指定特性应用于整个程序集。

以下各节讨论的实现`CameraPageRenderer`为每个平台的自定义呈现器。

### <a name="creating-the-page-renderer-on-ios"></a>在 ios 设备上创建页的呈现器

下面的代码示例显示了为 iOS 平台页的呈现器：

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
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

对基类的调用`OnElementChanged`方法实例化 iOS`UIViewController`控件。 前提被呈现器不已附加到现有的 Xamarin.Forms 元素，且前提是存在的页实例，所呈现的自定义呈现器仅呈现实时照相机流。

通过一系列的使用方法然后自定义页面`AVCapture`Api 以提供来自照相机和捕获照片的功能的实时流。

### <a name="creating-the-page-renderer-on-android"></a>创建在 Android 上的页的呈现器

下面的代码示例显示了 Android 平台的页的呈现器：

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
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

对基类的调用`OnElementChanged`方法实例化 Android`ViewGroup`控件，它是一组视图。 前提被呈现器不已附加到现有的 Xamarin.Forms 元素，且前提是存在的页实例，所呈现的自定义呈现器仅呈现实时照相机流。

然后通过调用一系列的方法使用的自定义页面`Camera`API 以提供来自照相机和之前捕获照片的功能的实时流`AddView`会调用方法来添加实时照相机流式传输到 UI `ViewGroup`。 请注意，在 Android 上它也不必重写`OnLayout`方法以执行在视图上的度量值和布局操作。 有关详细信息，请参阅[ContentPage 呈现器示例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)。

### <a name="creating-the-page-renderer-on-uwp"></a>在 UWP 上创建页的呈现器

下面的代码示例显示了适用于 UWP 的页呈现器：

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
                SetupBasedOnStateAsync();

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

对基类的调用`OnElementChanged`方法实例化`FrameworkElement`在其呈现页的控件。 前提被呈现器不已附加到现有的 Xamarin.Forms 元素，且前提是存在的页实例，所呈现的自定义呈现器仅呈现实时照相机流。 然后通过调用一系列的方法使用的自定义页面`MediaCapture`API 以提供来自照相机和自定义的页面添加到之前捕获照片的功能的实时流`Children`显示的集合。

实现派生的自定义呈现器时`PageRenderer`UWP 上,`ArrangeOverride`方法还应实现来排列页面控件，因为基呈现器不知道应如何处理它们。 否则，生成空白页。 因此，在此示例`ArrangeOverride`方法调用`Arrange`方法`Page`实例。

> [!NOTE]
> 若要停止和释放的对象，它提供对 UWP 应用程序中相机的访问至关重要。 如果不这样做可能会影响其他应用程序尝试访问设备的照相机。 有关详细信息，请参阅[显示摄像头预览](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>总结

本文演示了如何创建自定义呈现器[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)页上，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。 一个`ContentPage`是一个可显示一个视图，并占据屏幕的大部分可见元素。


## <a name="related-links"></a>相关链接

- [CustomRendererContentPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
