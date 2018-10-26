---
title: 从图片库中选取照片
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类从手机的图片库中选取照片。
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 00308a6c7883d4ac6ce41592d4a0e18f9fb28d52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113308"
---
# <a name="picking-a-photo-from-the-picture-library"></a>从图片库中选取照片

本文将指导完成创建一个允许用户从电话的图片库中选取照片应用程序。 因为 Xamarin.Forms 不包括此功能，它是使用所需[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)访问每个平台上的本机 Api。  本文将介绍以下步骤使用`DependencyService`此任务：

- **[创建接口](#Creating_the_Interface)** &ndash;了解如何共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash;了解如何在适用于 iOS 的本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash;了解如何适用于 Android 的本机代码中实现的接口。
- **[通用 Windows 平台实现](#UWP_Implementation)** &ndash;了解如何为通用 Windows 平台 (UWP) 中的本机代码实现的接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`来调入本机实现从共享代码。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，创建一个接口，表示所需的功能的共享代码中。 对于照片选择应用程序，只是一种方法是必需的。 在中定义的这[ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) .NET 标准库的示例代码中的接口：

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync`方法定义为异步，因为该方法必须返回快速，但它不能返回`Stream`直到用户有浏览图片库，并选择了一个所选照片的对象。

在使用特定于平台的代码的所有平台实现此接口。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

IOS 实现`IPicturePicker`接口使用[ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/)中所述[**从库中选择一张照片**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)方案并[示例代码](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)。

IOS 实现包含在[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) iOS 项目中的示例代码的类。 若要使此类对可见`DependencyService`管理器中，必须使用标识的类 [`assembly`] 类型的属性`Dependency`，类必须是公共的且显式实现`IPicturePicker`接口：

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

`GetImageStreamAsync`方法创建`UIImagePickerController`和它初始化，以选择照片库中的映像。 两个事件处理程序所需： 一个用于当用户选择一张照片以及另一个用于当用户取消的照片库显示。 `PresentModalViewController`然后向用户显示照片库。

在此情况下，`GetImageStreamAsync`方法必须返回`Task<Stream>`对象正在调用它的代码。 仅当用户完成与照片库进行交互并调用其中一个事件处理程序时，此任务已完成。 为此，类情况下[ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx)类非常重要。 此类提供`Task`要从返回的正确泛型类型的对象`GetImageStreamAsync`方法和类可以稍后向发出信号完成此任务。

`FinishedPickingMedia`时用户所选图片调用事件处理程序。 但是，该处理程序提供了`UIImage`对象和`Task`必须返回.NET`Stream`对象。 这是在两个步骤：`UIImage`对象必须先转换为存储在内存中的 JPEG 文件`NSData`对象，然后`NSData`对象转换为.NET`Stream`对象。 调用`SetResult`方法`TaskCompletionSource`对象，从而完成任务`Stream`对象：

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

IOS 应用程序需要访问照片库从用户的权限。 以下内容添加至`dict`Info.plist 文件的部分：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

Android 实现使用中所述的技术[**选择一个映像**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)方案并[示例代码](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)。 但是，当用户从图片库选择图像时调用的方法是`OnActivityResult`派生的类中重写`Activity`。 出于此原因，普通[ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) Android 项目中的类具有补充，使用字段、 属性和的重写`OnActivityResult`方法：

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

`OnActivityResult`重写指示选定的图片文件与 Android`Uri`对象，但是可以转换为.NET`Stream`对象通过调用`OpenInputStream`方法`ContentResolver`从获取的对象活动的`ContentResolver`属性。

Android 实现与 iOS 实现一样使用`TaskCompletionSource`任务完成时发出信号通知。 这`TaskCompletionSource`对象定义中的公共属性作为`MainActivity`类。 这允许将属性中引用[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) Android 项目中的类。 这是与类`GetImageStreamAsync`方法：

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

此方法访问`MainActivity`类有多种用途： 为`Instance`属性，对于`PickImageId`字段中，为`TaskCompletionSource`属性，并调用`StartActivityForResult`。 此方法由定义`FormsAppCompatActivity`类，该类是类的基类的`MainActivity`。

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>UWP 实现

与 iOS 和 Android 的实现中，不同于通用 Windows 平台的照片选取器实现不需要`TaskCompletionSource`类。 [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs)类使用[ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)类，以获取对照片库的访问。 因为`PickSingleFileAsync`方法`FileOpenPicker`本身是异步的`GetImageStreamAsync`方法只需使用`await`使用该方法 （和其他异步方法），并返回`Stream`对象：


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

现在，已针对每个平台实现该接口，.NET Standard 库中的应用程序可以充分利用它。

[ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs)类创建`Button`选取照片：

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

`Clicked`处理程序使用`DependencyService`类来调用`GetImageStreamAsync`。 这会导致在平台项目中的调用。 如果该方法返回`Stream`对象，则处理程序将创建`Image`元素以使用该图片`TapGestureRecognizer`，并替换`StackLayout`的页面上`Image`:

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

点击`Image`元素将页返回到正常。


## <a name="related-links"></a>相关链接

- [从库 (iOS) 中选择一张照片](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [选择一个映像 (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService （示例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
