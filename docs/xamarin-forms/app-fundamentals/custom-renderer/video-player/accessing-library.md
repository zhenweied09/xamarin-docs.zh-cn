---
title: 访问设备的视频库
description: 此文章介绍了如何访问设备的视频库中使用 Xamarin.Forms 的视频播放器应用程序。
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 7e9f7ad93ae8828155847b923cb2779b3146f63e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240695"
---
# <a name="accessing-the-devices-video-library"></a>访问设备的视频库

大多数现代移动设备和台式计算机能够使用设备的摄像头的视频记录。 然后，用户创建的视频将存储为设备上的文件。 可以从映像库中检索这些文件，通过播放`VideoPlayer`就像任何其他视频的类。

## <a name="the-photo-picker-dependency-service"></a>照片选取器依赖关系服务

其中每三个平台都包含一种功能，允许用户从设备的映像库中选择的照片或视频。 播放视频设备的映像库中的第一步生成时，将调用每个平台上的映像选取器的依赖项服务。 如下所述的依赖项服务是非常类似于中定义一个[**从图片库中选取照片**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)文章，只不过视频选取器返回文件名而不是`Stream`对象。

标准.NET 类库项目定义一个名为的接口`IVideoPicker`依赖项服务：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

每三个平台都包含一个名为类`VideoPicker`实现此接口。

### <a name="the-ios-video-picker"></a>IOS 视频选取器

IOS`VideoPicker`使用 iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/)访问映像库，指定它应被限制为视频 （称为"电影"） iOS 中`MediaType`属性。 请注意，`VideoPicker`显式实现`IVideoPicker`接口。 另请注意`Dependency`标识作为一种依赖服务的此类的属性。 这些是允许 Xamarin.Forms 平台项目中查找依赖项服务的两个要求：

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Android 视频选取器

Android 实现`IVideoPicker`需要属于应用程序的活动的回调方法。 为此，`MainActivity`类定义两个属性、 字段和回调方法：

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

`OnCreate`中的方法`MainActivity`将自己的实例存储在静态`Current`属性。 这样的实现`IVideoPicker`获取`MainActivity`用于启动实例**选择视频**选择器：

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

上述元素添加到`MainActivity`对象是中的唯一代码[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)解决方案普通的应用程序代码需要更改以支持`FormsVideoLibrary`类。

### <a name="the-uwp-video-picker"></a>UWP 视频选取器

UWP 实现`IVideoPicker`界面使用 UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)。 它开始图片库中，使用文件搜索，并将文件类型限制为 MP4 和 WMV （Windows Media 视频）：

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>调用依赖服务

**播放视频库**页[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)程序演示如何使用视频选取器依赖关系服务。 XAML 文件中包含`VideoPlayer`实例和一个`Button`标记为**显示视频库**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含`Clicked`处理程序`Button`。 调用依赖服务需要调用`DependencyService.Get`若要获取的实现`IVideoPicker`平台项目中的接口。 `GetVideoFileAsync`然后在该实例上调用方法：

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

`Clicked`处理然后使用该文件名来创建`FileVideoSource`对象并将它设置为`Source`属性`VideoPlayer`。

每个`VideoPlayerRenderer`类包含中的代码其`SetSource`类型的对象的方法`FileVideoSource`。 这些如下所示：

### <a name="handling-ios-files"></a>处理 iOS 文件

IOS 版本`VideoPlayerRenderer`进程`FileVideoSource`使用静态对象`Asset.FromUrl`文件名的方法。 这创建`AVAsset`表示设备的映像库中文件的对象：

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>处理 Android 文件

在处理类型的对象时`FileVideoSource`的 Android 实现`VideoPlayerRenderer`使用`SetVideoPath`方法`VideoView`设备的映像库中指定的文件：

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>处理 UWP 文件

在处理类型的对象时`FileVideoSource`的 UWP 实现`SetSource`方法需要创建`StorageFile`对象、 打开该文件进行读取，并将传递到的流对象`SetSource`方法`MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

对于所有三个平台，视频开始播放视频之后，几乎可以立即设置源，因为该文件在设备上，不需要下载。



## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
