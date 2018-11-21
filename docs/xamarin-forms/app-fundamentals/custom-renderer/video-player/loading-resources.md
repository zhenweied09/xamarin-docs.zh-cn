---
title: 正在加载应用程序资源视频
description: 本文介绍如何加载存储为应用程序资源视频播放器应用程序，使用 Xamarin.Forms 中的视频。
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171321"
---
# <a name="loading-application-resource-videos"></a>正在加载应用程序资源视频

自定义呈现器为`VideoPlayer`视图都将能够播放应用程序资源作为单个平台项目中嵌入的视频文件。 但是，当前版本的`VideoPlayer`不能访问嵌入在.NET Standard 库中的资源。

若要加载这些资源，创建的实例`ResourceVideoSource`通过设置`Path`文件名 （或文件夹和文件名） 的资源的属性。 或者，可以调用静态`VideoSource.FromResource`方法可以引用该资源。 然后，设置`ResourceVideoSource`对象传递给`Source`属性的`VideoPlayer`。

## <a name="storing-the-video-files"></a>存储的视频文件

将视频文件存储在平台项目是为每个平台不同。

### <a name="ios-video-resources"></a>iOS 视频资源

可以在 iOS 项目中，存储中的视频**资源**文件夹或子文件夹中的**资源**文件夹。 视频文件必须具有`Build Action`的`BundleResource`。 设置`Path`的属性`ResourceVideoSource`文件名，例如， **MyFile.mp4**中的文件**资源**文件夹，或**MyFolder/MyFile.mp4**，其中**MyFolder**是子文件夹的**资源**。

在中**VideoPlayerDemos**解决方案， **VideoPlayerDemos.iOS**项目包含的子文件夹**资源**名为**视频**其中包含名为的文件**iOSApiVideo.mp4**。 这是简短的视频演示如何使用 Xamarin 网站上查找适用于 iOS 的文档`AVPlayerViewController`类。

### <a name="android-video-resources"></a>Android 视频资源

在 Android 项目中，视频必须存储的子文件夹中**资源**名为**原始**。 **原始**文件夹不能包含子文件夹。 为视频文件提供`Build Action`的`AndroidResource`。 设置`Path`的属性`ResourceVideoSource`文件名，例如， **MyFile.mp4**。

**VideoPlayerDemos.Android**项目中包含的子文件夹**资源**名为**原始**，其中包含名为的文件**AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>UWP 视频资源

在通用 Windows 平台项目中，您可以存储在项目中的任何文件夹中的视频。 为此文件提供`Build Action`的`Content`。 设置`Path`的属性`ResourceVideoSource`到的文件夹和文件名，例如， **MyFolder/MyVideo.mp4**。

**VideoPlayerDemos.UWP**项目包含名为的文件夹**视频**与文件**UWPApiVideo.mp4**。

## <a name="loading-the-video-files"></a>正在加载的视频文件

每个平台呈现器类都包含中的代码及其`SetSource`加载视频文件作为资源存储方法。

### <a name="ios-resource-loading"></a>iOS 资源加载

IOS 版本的`VideoPlayerRenderer`使用`GetUrlForResource`方法的`NSBundle`用于加载资源。 完整路径必须划分文件名、 扩展和目录。 该代码使用`Path`类在.NET 中的`System.IO`将文件路径划分为这些组件的命名空间：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Android 资源加载

Android`VideoPlayerRenderer`使用的文件名和包名称来构造`Uri`对象。 包名称是应用程序的名称在这种情况下**VideoPlayerDemos.Android**，从中可以获取从静态`Context.PackageName`属性。 所产生的`Uri`对象然后传递给`SetVideoURI`方法的`VideoView`:

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>UWP 资源加载

UWP`VideoPlayerRenderer`构造`Uri`对象的路径并将其设置为`Source`属性的`MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>播放资源文件

**播放视频资源**页面**VideoPlayerDemos**解决方案使用`OnPlatform`类，以指定每个平台的视频文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

如果 iOS 资源存储在**资源**文件夹中，并且如果 UWP 资源存储在项目的根文件夹中，则可以为每个平台使用相同的文件名。 如果出现这种情况，则可以直接设置该名称`Source`属性的`VideoPlayer`。

下面是运行该页面：

[![播放视频资源](loading-resources-images/playvideoresource-small.png "播放视频资源")](loading-resources-images/playvideoresource-large.png#lightbox "播放视频资源")

现在，已了解如何[从 Web URI 加载视频](web-videos.md)和如何播放嵌入的资源。 此外，你可以[从设备的视频库加载视频](accessing-library.md)。


## <a name="related-links"></a>相关链接

- [视频播放机演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
