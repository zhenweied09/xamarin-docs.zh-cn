---
title: 创建平台视频播放器
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 316a6b9e9ce65dfb3c19d611f1b4976709546b5f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="creating-the-platform-video-players"></a>创建平台视频播放器

[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)解决方案包含所有代码，以实现 Xamarin.Forms 视频播放器。 它还包括演示如何使用视频播放器应用程序中的一系列页面。 所有`VideoPlayer`代码和其平台呈现器位于项目文件夹名为`FormsVideoLibrary`，并且也使用命名空间`FormsVideoLibrary`。 这应使轻松地将文件复制到自己的应用程序和引用类。

## <a name="the-video-player"></a>视频播放器

[ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs)类是一部分**VideoPlayerDemos**可移植类库 (PCL) 在平台之间共享。 它派生自`View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

此类的成员 (和`IVideoPlayerController`接口) 按照文章所述。

每三个平台都包含一个名为类`VideoPlayerRenderer`，其中包含特定于平台的代码来实现视频播放器。 此呈现器的主要任务是创建该平台的视频播放器。

### <a name="the-ios-player-view-controller"></a>IOS 播放器视图控制器

在 iOS 中实现视频播放器时涉及多个类。 应用程序首次创建[ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) ，然后将设置[ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/)类型的对象属性[ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/)。 播放机分配的视频源时，其他类是必需的。

如所有呈现器，iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs)包含`ExportRenderer`该属性标识`VideoPlayer`与呈现器的视图：

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

设置的平台控件的呈现器通常派生自[ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs)类，其中`View`方法是 Xamarin.Forms`View`派生 (在这种情况下， `VideoPlayer`) 和`NativeView`是 iOS`UIView`呈现器类派生。 对于此呈现器，该泛型自变量只需设置为`UIView`，很快就会看到的原因。

当呈现器根据`UIViewController`派生 （与此是），则应重写类`ViewController`属性，并返回视图控制器，在此情况下`AVPlayerViewController`。 它是的用途`_playerViewController`字段：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

主要责任`OnElementChanged`替代是检查如果`Control`属性是`null`并且，如果是这样，创建平台控件，并将其传递到`SetNativeControl`方法。 在这种情况下，该对象是仅可从`View`属性`AVPlayerViewController`。 `UIView`派生恰好是一个名为的专用类`AVPlayerView`，但因为它是私有的不能将它显式指定为第二个泛型参数`ViewRenderer`。

通常`Control`呈现器类的属性此后指`UIView`用来实现呈现器，但在这种情况下`Control`未在其他位置使用属性。

### <a name="the-android-video-view"></a>Android 视频视图

Android 呈现器`VideoPlayer`基于 Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/)类。 但是，如果`VideoView`可供本身用于播放的区域分配的在 Xamarin.Forms 应用中，视频填充视频`VideoPlayer`而无需维护正确的纵横比。 此原因 （如很快就会看到），`VideoView`进行的 Android 子`RelativeLayout`。 A`using`指令定义`ARelativeLayout`区分开来 Xamarin.Forms `RelativeLayout`，和第二个泛型参数，位于`ViewRenderer`:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

从 Xamarin.Forms 2.5 开始，Android 呈现器应包括的构造函数`Context`自变量。

`OnElementChanged`重写创建同时`VideoView`和`RelativeLayout`和设置的布局参数`VideoView`以使其内居中`RelativeLayout`。


```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

处理程序`Prepared`此方法中附加和分离中事件`Dispose`方法。 此事件激发时`VideoView`有足够的信息，以开始播放视频文件。

### <a name="the-uwp-media-element"></a>UWP 媒体元素

在通用 Windows 平台 (UWP)，最常见的视频播放器是[ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/)。 该文档的`MediaElement`指示[ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/)应改为使用时才需要支持版本的 Windows 10 版本 1607年开头。

`OnElementChanged`替代需要创建`MediaElement`、 设置几个事件处理程序，并将传递`MediaElement`对象传递给`SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

两个事件处理程序中分离`Dispose`呈现器的事件。

## <a name="showing-the-transport-controls"></a>显示传输控件

包含三个平台中的所有视频播放器支持一组默认的传输包括用于播放和暂停，以及一个栏，以指示视频中中的当前位置，并将移到新位置的按钮的控件。

`VideoPlayer`类定义一个名为属性`AreTransportControlsEnabled`并将默认值设置为`true`:


```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

尽管此属性同时具有`set`和`get`访问器，呈现器必须处理情况下，仅在设置属性。 `get`访问器只返回该属性的当前值。

属性，如`AreTransportControlsEnabled`平台呈现器两种方式处理：

- 第一次是当 Xamarin.Forms 创建`VideoPlayer`元素。 这指示在`OnElementChanged`呈现器的重写时`NewElement`属性不是`null`。 在此期间，呈现器可以设置中定义为从该属性的初始值自己平台视频播放器`VideoPlayer`。

- 如果中的属性`VideoPlayer`更高版本发生更改，则`OnElementPropertyChanged`调用中呈现器的方法。 这样的呈现器，更新基于新的属性设置的平台视频播放器。

下面是如何`AreTransportControlsEnabled`属性处理三个平台中：

### <a name="ios-playback-controls"></a>iOS 播放控件

IOS 的属性`AVPlayerViewController`控制显示的传输控制是[ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/)。 下面是如何在 iOS 中设置该属性`VideoViewRenderer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

`Element`呈现器的属性是指`VideoPlayer`类。

### <a name="the-android-media-controller"></a>Android 媒体控制器

在 Android 中，显示传输控件需要创建[ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/)对象并将其与关联`VideoView`对象。 机制所示`SetAreTransportControlsEnabled`方法：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>UWP 传输控件属性

UWP`MediaElement`定义名为的属性[ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled)，以便从设置属性`VideoPlayer`具有相同名称的属性：

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

一个属性是开始播放视频所需： 这一点至关重要`Source`属性，引用视频文件。 实现`Source`属性描述在下一步的文章中，[播放 Web 视频](web-videos.md)。


## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
