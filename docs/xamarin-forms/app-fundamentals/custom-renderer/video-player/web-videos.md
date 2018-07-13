---
title: 播放 Web 视频
description: 本文介绍如何在使用 Xamarin.Forms 的视频播放器应用程序中播放 web 视频。
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 566f056bd616c918ce274b9c7406d94fdc265ea2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994554"
---
# <a name="playing-a-web-video"></a>播放 Web 视频

`VideoPlayer`类定义`Source`属性用于指定的源视频文件，以及`AutoPlay`属性。 `AutoPlay` 其默认设置为`true`，这意味着视频应开始播放之后自动`Source`已设置：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

`Source`属性属于类型`VideoSource`，这模仿 Xamarin.Forms [ `ImageSource` ](xref:Xamarin.Forms.ImageSource)抽象类，并且其三个派生[ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource)， [`FileImageSource` ](xref:Xamarin.Forms.FileImageSource)，并[ `StreamImageSource` ](xref:Xamarin.Forms.StreamImageSource)。 没有流选项仅适用于`VideoPlayer`但是，因为 iOS 和 Android 不支持播放从流视频。

## <a name="video-sources"></a>视频源

抽象`VideoSource`类只包含三个实例化派生自的三个类的静态方法`VideoSource`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

`UriVideoSource`类用于与 URI 指定一个可下载的视频文件。 它定义类型的单个属性`string`:

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

处理类型的对象`UriVideoSource`如下所述。

`ResourceVideoSource`类用于访问作为平台应用程序还使用指定的嵌入资源存储的视频文件`string`属性：

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

处理类型的对象`ResourceVideoSource`文章中介绍了[加载应用程序资源视频](loading-resources.md)。 `VideoPlayer`类具有任何工具可以加载为.NET Standard 库中的资源存储的视频文件。

`FileVideoSource`类用于从设备的视频库访问视频文件。 单个属性的类型也是`string`:

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

处理类型的对象`FileVideoSource`文章中介绍了[访问设备的视频库](accessing-library.md)。

`VideoSource`类包括`TypeConverter`引用的属性`VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

调用此类型转换器时`Source`属性设置为在 XAML 中的字符串。 下面是`VideoSourceConverter`类：

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

`ConvertFromInvariantString`方法尝试将字符串转换为`Uri`对象。 如果成功，并且该方案不是`file:`，则该方法返回`UriVideoSource`。 否则，它将返回`ResourceVideoSource`。

## <a name="setting-the-video-source"></a>设置视频源

单个平台呈现器被实现所有其他逻辑相关的视频源。 以下部分介绍了如何平台呈现器播放视频时`Source`属性设置为`UriVideoSource`对象。

### <a name="the-ios-video-source"></a>IOS 视频源

两个部分`VideoPlayerRenderer`参与视频播放器将视频源设置。 Xamarin.Forms 首先创建类型的对象`VideoPlayer`，则`OnElementChanged`方法调用与`NewElement`自变量对象的属性设置为的`VideoPlayer`。 `OnElementChanged`方法调用`SetSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

更高版本上，当`Source`更改属性时，`OnElementPropertyChanged`方法调用与`PropertyName`"源"属性和`SetSource`再次调用。

若要播放视频文件在 iOS 中，类型的对象[ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/)首次创建封装该视频文件，并用于创建[ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)，其中然后交给`AVPlayer`对象。 下面是如何`SetSource`方法将处理`Source`属性的类型时`UriVideoSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

`AutoPlay`属性中的 iOS 视频类，具有不相似之处，因此属性检查的末尾`SetSource`方法来调用`Play`方法`AVPlayer`对象。

在某些情况下，视频继续与页之后播放`VideoPlayer`导航回主页。 若要停止视频中，`ReplaceCurrentItemWithPlayerItem`还在设置`Dispose`重写：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Android 的视频源

Android`VideoPlayerRenderer`需要在设置播放机的视频源时`VideoPlayer`是首次创建及更高版本时`Source`属性更改：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

`SetSource`方法可处理类型的对象`UriVideoSource`通过调用`SetVideoUri`上`VideoView`与 Android`Uri`从 URI 的字符串创建对象。 `Uri`类完全限定此处以便将其从.NET`Uri`类：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Android`VideoView`无相应`AutoPlay`属性，因此`Start`调用方法时如果已设置新视频。

如果存在是有区别的行为的 ios 和 Android 的呈现器`Source`属性`VideoPlayer`设置为`null`，或者如果`Uri`属性`UriVideoSource`设置为`null`或空白字符串。 如果 iOS 视频播放器当前播放的视频，并`Source`设置为`null`(或字符串是`null`或保留为空)，`ReplaceCurrentItemWithPlayerItem`使用调用`null`值。 当前的视频将被替换，并停止播放。

Android 不支持类似的工具。 如果`Source`属性设置为`null`，则`SetSource`方法只需忽略它，并将持续播放，当前的视频。

### <a name="the-uwp-video-source"></a>UWP 视频源

UWP`MediaElement`定义`AutoPlay`属性，它在与任何其他属性一样呈现器中处理：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

`SetSource`属性句柄`UriVideoSource`通过设置对象`Source`的属性`MediaElement`到.NET`Uri`值，或设置为`null`如果`Source`属性`VideoPlayer`设置为`null`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>URL 源设置

使用三个呈现器中的这些属性的实现，就可能要播放的视频 URL 的来源。 **播放 Web 视频**页面[ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md)程序由下面的 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter`类将字符串转换为`UriVideoSource`。 导航到**播放 Web 视频**页上，视频开始加载和播放下载足够数量的数据并将其缓冲时启动。 视频为大约 10 分钟的长度：

[![播放 Web 视频](web-videos-images/playwebvideo-small.png "播放 Web 视频")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 视频")

在每个三个平台上传输控件淡出如果它们不会用到，但可以还原以查看通过点击该视频。

可以阻止自动启动设置视频`AutoPlay`属性设置为`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

你将需要按**播放**按钮以开始播放视频。

类似地，可以通过设置隐含的传输控件显示`AreTransportControlsEnabled`属性设置为`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

如果这两个属性设置为`false`，然后视频不会开始播放，并将启动该服务没有办法 ！ 可能需要调用`Play`从代码隐藏文件，或创建你自己的传输控件，如本文所述[实现自定义视频传输控件](custom-transport.md)。

**App.xaml**文件包含资源的两个其他视频：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

可以为这些其他电影之一的引用，替换中的显式 URL **PlayWebVideo.xaml**文件具有`StaticResource`标记扩展，这种情况下的`VideoSourceConverter`不需要创建`UriVideoSource`对象：

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

或者，可以设置`Source`中的视频文件的属性`ListView`，如在下一篇文章中所述[绑定到播放器的视频源](source-bindings.md)。





## <a name="related-links"></a>相关链接

- [视频播放机演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
