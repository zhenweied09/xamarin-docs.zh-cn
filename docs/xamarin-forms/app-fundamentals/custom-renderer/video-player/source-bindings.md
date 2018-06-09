---
title: 绑定到播放器的视频源
description: 此文章介绍了如何将视频源绑定到视频播放器，使用 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 504E0C7E-051A-4AF2-B654-BAB4D0957928
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: b0efdc1a20f52231f15b7a08eb86962e2079c678
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240025"
---
# <a name="binding-video-sources-to-the-player"></a>绑定到播放器的视频源

当`Source`属性`VideoPlayer`视图设为新的视频文件，现有视频停止播放并开始新的播放视频。 说明了这一点通过**选择 Web 视频**页[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)示例。 该页面包括`ListView`与从引用的三个视频标题**App.xaml**文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.SelectWebVideoPage"
             Title="Select Web Video">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0" />

        <ListView Grid.Row="1"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Elephant's Dream</x:String>
                    <x:String>Big Buck Bunny</x:String>
                    <x:String>Sintel</x:String>
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

选择视频时，`ItemSelected`执行的代码隐藏文件中的事件处理程序。 该处理程序从标题中删除任何空格和撇号，并使用该值作为键来获取中定义的资源之一**App.xaml**文件。 `UriVideoSource`然后将对象设置为`Source`属性`VideoPlayer`。

```csharp
namespace VideoPlayerDemos
{
    public partial class SelectWebVideoPage : ContentPage
    {
        public SelectWebVideoPage()
        {
            InitializeComponent();
        }

        void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
        {
            if (args.SelectedItem != null)
            {
                string key = ((string)args.SelectedItem).Replace(" ", "").Replace("'", "");
                videoPlayer.Source = (UriVideoSource)Application.Current.Resources[key];
            }
        }
    }
}
```

在首次加载页时未选定任何项`ListView`，因此您必须选择一个用于开始播放视频：

[![选择 Web 视频](source-bindings-images/selectwebvideo-small.png "选择 Web 视频")](source-bindings-images/selectwebvideo-large.png#lightbox "选择 Web 视频")

`Source`属性`VideoPlayer`由可绑定的属性，这意味着，它可以是数据绑定的目标。 说明了这一点通过**将绑定到 VideoPlayer**页。 中的标记**BindToVideoPlayer.xaml**由封装的视频和相应的标题的以下类支持文件`VideoSource`对象：

```csharp
namespace VideoPlayerDemos
{
    public class VideoInfo
    {
        public string DisplayName { set; get; }

        public VideoSource VideoSource { set; get; }

        public override string ToString()
        {
            return DisplayName;
        }
    }
}
```

`ListView`中**BindToVideoPlayer.xaml**文件包含的这些数组`VideoInfo`对象，使用视频标题初始化每个与`UriVideoSource`从资源字典中的对象**App.xaml**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VideoPlayerDemos"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.BindToVideoPlayerPage"
             Title="Bind to VideoPlayer">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           Source="{Binding Source={x:Reference listView},
                                            Path=SelectedItem.VideoSource}" />

        <ListView x:Name="listView"
                  Grid.Row="1">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:VideoInfo}">
                    <local:VideoInfo DisplayName="Elephant's Dream"
                                     VideoSource="{StaticResource ElephantsDream}" />

                    <local:VideoInfo DisplayName="Big Buck Bunny"
                                     VideoSource="{StaticResource BigBuckBunny}" />

                    <local:VideoInfo DisplayName="Sintel"
                                     VideoSource="{StaticResource Sintel}" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </Grid>
</ContentPage>
```

`Source`属性`VideoPlayer`绑定到`ListView`。 `Path`绑定的指定为`SelectedItem.VideoSource`，这是包含两个属性的复合路径：`SelectedItem`是的一个属性`ListView`。 所选的项的类型是`VideoInfo`，它具有`VideoSource`属性。

第一个与**选择 Web 视频**页上，未选定任何项最初从`ListView`，因此你需要先选择视频之一，然后它将开始播放。


## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
