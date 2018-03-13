---
title: "自定义视频定位"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: e7f2ad9e94d68007b1b7d0cca212cd51515a0108
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="custom-video-positioning"></a>自定义视频定位

由每个平台实现的传输控制包括位置栏。 此栏类似于滑块或滚动条，并在其总持续时间内显示视频的当前位置。 此外，用户可以操作的位置栏将向前或向后移动到视频中的新位置。

这篇文章演示如何实现你自己的自定义位置栏。

## <a name="the-duration-property"></a>持续时间属性

信息的一个项，`VideoPlayer`需要支持自定义位置栏是视频的持续时间。 `VideoPlayer`定义只读`Duration`类型的属性`TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

如`Status`属性中所述[上一篇文章](custom-transport.md)，则此`Duration`属性是只读的。 定义专用`BindablePropertyKey`并且仅可通过引用设置`IVideoPlayerController`接口，其中包括这`Duration`属性：

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

此外会注意到的属性更改的处理程序调用一个名为方法`SetTimeToEnd`描述此文章中更高版本。

视频的持续时间是*不*可用后立即`Source`属性`VideoPlayer`设置。 部分必须下载视频文件，才能基础视频播放器可以确定其持续时间。

下面是每个平台呈现器如何获取视频的持续时间：

### <a name="video-duration-in-ios"></a>在 iOS 中的视频持续时间

在 iOS 视频的持续时间获取从`Duration`属性`AVPlayerItem`，但不是立即晚于`AVPlayerItem`创建。 可以设置的 iOS 观察者`Duration`属性，但`VideoPlayerRenderer`获取的持续时间以`UpdateStatus`方法，即每秒 10 次：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

`ConvertTime`方法将`CMTime`对象传递给`TimeSpan`值。

### <a name="video-duration-in-android"></a>在 Android 中的视频持续时间

`Duration` Android 属性`VideoView`报告以毫秒为单位的有效持续时间时`Prepared`事件`VideoView`激发。 Android`VideoPlayerRenderer`类使用该处理程序来获取`Duration`属性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>在 UWP 视频持续时间

`NaturalDuration`属性`MediaElement`是`TimeSpan`值并成为有效`MediaElement`激发`MediaOpened`事件：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>位置属性

`VideoPlayer` 此外需要`Position`属性可提高从零到`Duration`播放视频。 `VideoPlayer` 实现此属性，如`Position`UWP 中的属性`MediaElement`，这是正常的可绑定属性的公共`set`和`get`访问器：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

`get`访问器返回的视频当前位置播放它时，但`set`访问器旨在通过向前或向后移动的视频的位置的位置栏的用户的操作做出响应。

在 iOS 和 Android 中，获取当前的位置的属性仅具有`get`访问器和`Seek`方法是可用于执行此第二个任务。 如果你认为一下，单独`Seek`方法似乎是一个更明智的做法比单个`Position`属性。 单个`Position`属性具有固有的问题： 在视频播放时`Position`属性必须持续更新，以反映新的位置。 但你不希望对大多数更改`Position`属性以使视频播放器将移到视频中的新位置。 如果发生这种情况，通过查找到最后一个值以响应视频播放器会`Position`不向前移动属性和视频。

尽管的实现难度`Position`具有属性`set`和`get`访问器中，因为它是与 UWP 一致这种方法选择`MediaElement`，并且具有与数据绑定一起很大的优势： `Position`属性`VideoPlayer`可以绑定到用于同时显示的位置并要查找到新位置的滑块。 但是，一些预防措施是必需的实现这时`Position`属性以避免反馈循环。

### <a name="setting-and-getting-ios-position"></a>设置和获取 iOS 位置

在 iOS 中，`CurrentTime`属性`AVPlayerItem`对象指示播放视频的当前位置。 IOS`VideoPlayerRenderer`设置`Position`中的属性`UpdateStatus`同时它所设置的处理程序`Duration`属性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

呈现器检测到时`Position`属性设置从`VideoPlayer`中已更改`OnElementPropertyChanged`重写，并使用该新值来调用`Seek`方法`AVPlayer`对象：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

请记住，每次`Position`中的属性`VideoPlayer`设置从`OnUpdateStatus`处理程序，`Position`属性激发`PropertyChanged`事件，在中检测到`OnElementPropertyChanged`重写。 有关这些更改中的大多数`OnElementPropertyChanged`方法不执行任何操作。 否则，视频的位置中的每个更改，它将移到刚达到的相同位置 ！

若要避免这种反馈循环，`OnElementPropertyChanged`方法仅调用`Seek`时之间的差异`Position`属性和的当前位置`AVPlayer`大于 1 秒。

### <a name="setting-and-getting-android-position"></a>设置和获取 Android 位置

IOS 呈现器，Android 中一样`VideoPlayerRenderer`设置的新值`Position`中的属性`OnUpdateStatus`处理程序。 `CurrentPosition`属性`VideoView`包含单元中的毫秒的新位置：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

此外，iOS 呈现器中一样 Android 呈现器调用`SeekTo`方法`VideoView`时`Position`属性已更改，但仅限于时更改为多个为一秒从不同`CurrentPosition`值`VideoView`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>设置和获取 UWP 位置

UWP`VideoPlayerRenderer`句柄`Position`中一样的 iOS 和 Android 的呈现器，但是，由于`Position`UWP 属性`MediaElement`也`TimeSpan`值，则不必进行转换：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>计算 TimeToEnd 属性

有时视频播放器显示视频中的剩余时间。 此值匹配位置始于当视频开始，并减少到零，视频结束时的视频的持续时间。

`VideoPlayer` 包括的只读`TimeToEnd`完全在类处理的属性基于更改`Duration`和`Position`属性：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

`SetTimeToEnd`从这两者的属性更改的处理程序调用方法`Duration`和`Position`。

## <a name="a-custom-slider-for-video"></a>一个自定义的滑块，用于视频

可以编写一个位置栏中，自定义控件或使用 Xamarin.Forms`Slider`或派生自类`Slider`，如下所示`PositionSlider`类。 类定义名为的两个新属性`Duration`和`Position`类型的`TimeSpan`想要将数据绑定到中的相同名称的两个属性`VideoPlayer`。 请注意，对默认的绑定模式`Position`属性是双向：

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

属性更改处理程序`Duration`属性集`Maximum`基础属性`Slider`到`TotalSeconds`属性`TimeSpan`值。 同样的属性更改处理程序`Position`设置`Value`属性`Slider`。 这样，基础`Slider`跟踪的位置`PositionSlider`。

`PositionSlider`从基础更新`Slider`只有一个实例中： 当用户操作`Slider`以指示视频应高级或反转到新位置。 这中检测到`PropertyChanged`的构造函数中的处理程序`PositionSlider`。 处理程序检查中的更改`Value`属性，以及是否不同于`Position`属性，则`Position`属性设置从`Value`属性。

从理论上讲，内部`if`语句无法编写如下：

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

但是的 Android 实现`Slider`具有仅 1,000 离散步骤而不考虑`Minimum`和`Maximum`设置。 该视频的总长度超过 1,000 秒，然后两种不同`Position`值将对应于相同`Value`设置`Slider`，，这`if`语句会触发用户操作的假正`Slider`。 它会改为检查新的位置和现有的位置大于总持续时间的百分之一更加安全。

## <a name="using-the-positionslider"></a>使用 PositionSlider

适用于 UWP 文档[ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/)警告有关绑定到`Position`属性由于属性频繁更新。 文档建议，使用计时器查询`Position`属性。

这是一个不错的建议，但这三种`VideoPlayerRenderer`类已间接使用计时器来更新`Position`属性。 `Position`的处理程序中更改属性`UpdateStatus`事件，激发每秒仅 10 次。

因此，`Position`属性`VideoPlayer`可以绑定到`Position`属性`PositionSlider`不会出现性能问题，如中所示**自定义位置栏**页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

第一个省略号 （···） 隐藏`ActivityIndicator`; 它是与以前相同**自定义传输**页。 请注意两个`Label`元素显示`Position`和`TimeToEnd`属性。 这两个之间省略号`Label`元素隐藏这两个`Button`元素中所示**自定义传输**页面的播放、 暂停和停止。 代码隐藏逻辑也是相同**自定义传输**页。

[![自定义定位](custom-positioning-images/custompositioning-small.png "自定义定位")](custom-positioning-images/custompositioning-large.png#lightbox "自定义定位")

到此结束的讨论`VideoPlayer`。

## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
