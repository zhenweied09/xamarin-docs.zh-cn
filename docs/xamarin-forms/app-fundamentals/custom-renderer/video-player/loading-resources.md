---
title: "加载应用程序资源视频"
ms.topic: article
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 08d07a82651887c9d87b908acd82296a3d80e43f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="loading-application-resource-videos"></a>加载应用程序资源视频

有关自定义呈现器`VideoPlayer`视图是能够播放应用程序资源作为单个平台项目中嵌入的视频文件。 但是，当前版本的`VideoPlayer`不能访问可移植类库中嵌入的资源。

若要加载这些资源，创建的实例`ResourceVideoSource`通过设置`Path`到文件名 （或的文件夹和文件名） 的资源的属性。 或者，可以调用静态`VideoSource.FromResource`方法可以引用该资源。 然后，设置`ResourceVideoSource`对象传递给`Source`属性`VideoPlayer`。 

## <a name="storing-the-video-files"></a>存储的视频文件

在平台项目中存储的视频文件时，为将不同用于三个平台。

### <a name="ios-video-resources"></a>iOS 视频资源

在 iOS 项目中，你可以存储在视频**资源**文件夹或子文件夹**资源**文件夹。 视频文件必须具有`Build Action`的`BundleResource`。 设置`Path`属性`ResourceVideoSource`为文件名，例如， **MyFile.mp4**文件**资源**文件夹，或**MyFolder/MyFile.mp4**，其中**myfolder 文件夹**是子文件夹的**资源**。

在**VideoPlayerDemos**解决方案， **VideoPlayerDemos.iOS**项目包含的子文件夹**资源**名为**视频**包含一个名为文件**iOSApiVideo.mp4**。 这是简短的视频演示如何使用 Xamarin 网站上查找适用于 iOS 的文档`AVPlayerViewController`类。

### <a name="android-video-resources"></a>Android 的视频资源

在 Android 项目中，视频必须存储在子文件夹**资源**名为**原始**。 **原始**文件夹不能包含子文件夹。 为视频文件提供`Build Action`的`AndroidResource`。 设置`Path`属性`ResourceVideoSource`为文件名，例如， **MyFile.mp4**。 

**VideoPlayerDemos.Android**项目包含的子文件夹**资源**名为**原始**，其中包含名为的文件**AndroidApiVideo.mp4**. 

### <a name="uwp-video-resources"></a>UWP 视频资源

在通用 Windows 平台项目中，你可以存储在项目中的任何文件夹中的视频。 为此文件提供`Build Action`的`Content`。 设置`Path`属性`ResourceVideoSource`到的文件夹和文件名，例如， **MyFolder/MyVideo.mp4**。 

**VideoPlayerDemos.UWP**项目包含名为的文件夹**视频**文件**UWPApiVideo.mp4**。

## <a name="loading-the-video-files"></a>加载视频文件

每个平台呈现器类包含中的代码其`SetSource`加载视频文件作为资源存储方法。

### <a name="ios-resource-loading"></a>iOS 资源加载

IOS 版本`VideoPlayerRenderer`使用`GetUrlForResource`方法`NSBundle`用于加载资源。 完整路径必须划分为文件名、 扩展名和目录。 该代码使用`Path`中.NET 类`System.IO`对于划分到这些组件的文件路径的命名空间：

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

Android`VideoPlayerRenderer`文件名和包名称用于构造`Uri`对象。 包名称是应用程序的名称在这种情况下**VideoPlayerDemos.Android**，从中可以获取从静态`Context.PackageName`属性。 所产生的`Uri`对象随后会传递给`SetVideoURI`方法`VideoView`:

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

UWP`VideoPlayerRenderer`构造`Uri`路径的对象并将它设置为`Source`属性`MediaElement`:

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

## <a name="playing-the-resource-file"></a>播放的资源文件

**播放视频资源**页面**VideoPlayerDemos**解决方案使用`OnPlatform`类可指定用于每个平台的视频文件：

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

如果 iOS 资源存储在**资源**文件夹，并且如果 UWP 资源存储在项目的根文件夹中，可以用于三个平台使用相同的文件名。 如果是这样，则您可以将该名称直接为`Source`属性`VideoPlayer`。 

下面是三个平台上运行该页面：

[![播放视频资源](loading-resources-images/playvideoresource-small.png "播放视频资源")](loading-resources-images/playvideoresource-large.png#lightbox "播放视频资源")

现在，你已了解如何[从 Web URI 加载视频](web-videos.md)以及如何播放嵌入的资源。 此外，你可以[从设备的视频库加载视频](accessing-library.md)。


## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
