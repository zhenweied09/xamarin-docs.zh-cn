---
title: 自定义视频传输控件
description: 本文介绍如何在使用 Xamarin.Forms 的视频播放器应用程序中实现自定义传输控件。
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 84870de28ffd30b2d29fb5d8fbea815e1fd0d9c4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996433"
---
# <a name="custom-video-transport-controls"></a>自定义视频传输控件

视频播放器的传输控件包括执行功能的按钮**播放**，**暂停**，并**停止**。 这些按钮通常带有熟悉图标而不是文本，并**播放**并**暂停**函数通常组合成一个按钮。

默认情况下，`VideoPlayer`显示传输控件支持的每个平台。 当您将设置`AreTransportControlsEnabled`属性设置为`false`，这些控件被抑制。 然后，你可以控制`VideoPlayer`以编程方式或提供你自己的传输控件。

## <a name="the-play-pause-and-stop-methods"></a>播放、 暂停和停止方法

`VideoPlayer`类定义了三个方法名为`Play`， `Pause`，和`Stop`实现的触发事件：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

这些事件的事件处理程序设置的`VideoPlayerRenderer`类中每个平台，如下所示：

### <a name="ios-transport-implementations"></a>iOS 传输实现

IOS 版本的`VideoPlayerRenderer`使用`OnElementChanged`方法以设置的这三个事件处理程序时`NewElement`属性不是`null`并分离事件处理程序时`OldElement`不是`null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

事件处理程序通过调用的方法实现`AVPlayer`对象。 没有任何`Stop`方法`AVPlayer`，因此它模拟通过暂停视频并将位置移到开头。

### <a name="android-transport-implementations"></a>Android 传输实现

Android 实现是类似于 iOS 实现。 三个函数的处理程序时设置`NewElement`不是`null`并且分离出何时`OldElement`不是`null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

三个函数调用方法定义的`VideoView`。

### <a name="uwp-transport-implementations"></a>UWP 传输实现

三个传输函数的 UWP 实现是非常类似于 iOS 和 Android 的实现：

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
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>视频播放器状态

实现**播放**，**暂停**，并**停止**函数还不足以支持传输控件。 通常**播放**并**暂停**使用同一按钮来更改其外观来指示视频播放或暂停当前是实现命令。 此外，仅当尚未加载视频时，甚至不应启用按钮。

这些要求意味着，视频播放器需要提供当前状态，指示如果它是播放或暂停，或者如果尚未准备好播放视频。 (三个平台还支持属性，指示是否视频可以暂停，也可以移动到新位置，但这些属性是适用于流式处理视频而不是视频文件，因此它们不支持在`VideoPlayer`此处所述。)

**VideoPlayerDemos**项目包括`VideoStatus`包含三个成员的枚举：

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

`VideoPlayer`类定义了名为的仅限实际的可绑定属性`Status`类型的`VideoStatus`。 此属性被定义为只读的因为它仅应设置为从平台呈现器：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

通常情况下，只读的可绑定的属性将具有私有`set`访问器上的`Status`属性以使其能够从类中设置。 有关`View`支持的呈现器，但是，派生类的属性必须设置从类外部的但只能由平台呈现器。

出于此原因，另一个属性定义的名称`IVideoPlayerController.Status`。 这是一个显式接口实现，并可生成`IVideoPlayerController`接口的`VideoPlayer`类实现：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

它类似于如何[ `WebView` ](xref:Xamarin.Forms.WebView)控件使用[ `IWebViewController` ](xref:Xamarin.Forms.IWebViewController)要实现的接口`CanGoBack`和`CanGoForward`属性。 (请参阅的源代码[ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs)和其呈现器的详细信息。)

这样就可以为类的外部`VideoPlayer`若要设置`Status`通过引用属性`IVideoPlayerController`接口。 （稍后您将看到代码。）该属性可以设置从其他类，但不太可能会无意中设置。 最重要的是，`Status`属性不能通过数据绑定设置。

若要帮助在此数字保持呈现器`Status`属性已更新，`VideoPlayer`类定义`UpdateStatus`事件时触发每隔 10 秒：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>IOS 状态设置

IOS`VideoPlayerRenderer`设置的处理程序`UpdateStatus`事件 (并分离该处理程序时的基础`VideoPlayer`元素不存在)，并使用该处理程序来设置`Status`属性：

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
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

两个属性`AVPlayer`必须访问： [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/)类型的属性`AVPlayerStatus`并[ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/)类型的属性`AVPlayerTimeControlStatus`。 请注意，`Element`属性 (即`VideoPlayer`) 必须强制转换为`IVideoPlayerController`若要设置`Status`属性。

### <a name="the-android-status-setting"></a>Android 状态设置

[ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) Android 属性`VideoView`是一个布尔值，仅指示视频是否播放或暂停。 若要确定是否`VideoView`都不 play 也不能暂停视频还，`Prepared`事件的`VideoView`必须处理。 这些两个处理程序中设置`OnElementChanged`方法，并分离期间`Dispose`重写：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`UpdateStatus`处理程序使用`isPrepared`字段 (在中设置`Prepared`处理程序) 和`IsPlaying`属性来设置`Status`属性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>UWP 状态设置

UWP`VideoPlayerRenderer`利用`UpdateStatus`事件，但它不需要它用于设置`Status`属性。 `MediaElement`定义[ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged)允许呈现器的事件时通知[ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState)属性已更改。 在分离属性`Dispose`重写：

```csharp
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
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`CurrentState`属性属于类型[ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)，并轻松地将映射到`VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>播放、 暂停和停止按钮

使用 Unicode 字符的符号**播放**，**暂停**，并**停止**图像会出现问题。 [Miscellaneous 技术](https://unicode-table.com/en/blocks/miscellaneous-technical/)部分 Unicode 标准定义了三个看似适合于此用途的符号字符。 这些是：

- 0x23F5 （黑色中等右指三角形） 或&#x23F5;有关**播放**
- 0x23F8 （双竖线） 或&#x23F8;有关**暂停**
- 0x23F9 （黑色方框） 或&#x23F9;有关**停止**

而不考虑如何在浏览器中显示这些符号 （以及不同的浏览器处理这些不同的方式），它们不一致地显示通过 Xamarin.Forms 支持的平台上。 IOS 和 UWP 设备上**暂停**并**停止**字符有图形的外观，与蓝色 3D 背景和白色的前景色。 这不是在 Android 上，符号是只是蓝色的情况。 但是，对于 0x23F5 codepoint**播放**不具有相同的外观，UWP，和它甚至不支持在 iOS 和 Android。

为此，0x23F5 码位不能用于**播放**。 是一个不错的替代品：

- 0x25B6 （黑色右指三角形） 或&#x25B6;有关**播放**

这支持的所有三个平台，只不过它是普通的黑色三角形不像的三维效果**暂停**并**停止**。 一种可能性是遵循 0x25B6 码位，变体的代码：

- 0x25B6 跟 0xFE0F （变量 16） 或&#x25B6;&#xFE0F;有关**播放**

这是如下所示的标记中的用途。 在 iOS 上，这样**播放**符号作为相同的三维外观**暂停**并**停止**按钮，但将该变量不在 Android 和 UWP 上起作用。

**自定义传输**页上设置**AreTransportControlsEnabled**属性设置为**false**并包括`ActivityIndicator`加载视频时, 显示和两个按钮。 `DataTrigger` 对象用于启用和禁用`ActivityIndicator`和按钮，并将第一个按钮之间切换**播放**并**暂停**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

一文中详细描述了数据触发器[数据触发器](~/xamarin-forms/app-fundamentals/triggers.md#data)。

代码隐藏文件具有按钮处理程序`Clicked`事件：

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

因为`AutoPlay`设置为`false`中**CustomTransport.xaml**文件中，你将需要按**播放**按钮时它将成为能够开始视频。 按钮定义，因此上面讨论的 Unicode 字符都伴随其文本等效项。 播放视频时，按钮在每个平台上具有一致的外观：

[![自定义传输播放](custom-transport-images/customtransportplaying-small.png "自定义传输播放")](custom-transport-images/customtransportplaying-large.png#lightbox "自定义传输播放")

但在 Android 和 UWP，**播放**暂停视频时，按钮看起来很不同：

[![自定义传输暂停](custom-transport-images/customtransportpaused-small.png "自定义传输暂停")](custom-transport-images/customtransportpaused-large.png#lightbox "自定义传输已暂停")

在生产应用程序中，您可能需要使用您自己的位图图像的按钮来实现 visual 一致性。


## <a name="related-links"></a>相关链接

- [视频播放机演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
