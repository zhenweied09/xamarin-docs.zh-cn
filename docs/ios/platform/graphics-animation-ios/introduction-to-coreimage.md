---
title: CoreImage
description: "CoreImage 是 iOS 5 以提供图像处理和实时视频增强功能引入一个新框架。 本文介绍通过 Xamarin.iOS 示例使用这些功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: da9b9230a466c70cd584a00af848ffe87dacbc5b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="coreimage"></a>CoreImage

_CoreImage 是 iOS 5 以提供图像处理和实时视频增强功能引入一个新框架。本文介绍通过 Xamarin.iOS 示例使用这些功能。_

CoreImage 是中提供了多种内置筛选器和要应用于图像和视频，包括人脸检测效果的 iOS 5 引入一个新框架。

本文档包含简单的示例：

-  面对检测。
-  对映像应用筛选器
-  列出可用的筛选器。


这些示例应帮助你开始将 CoreImage 功能合并到你的 Xamarin.iOS 应用程序。

## <a name="requirements"></a>惠?

你必须使用 Xcode 的最新版本。

## <a name="face-detection"></a>人脸检测

CoreImage 表面检测功能的作用它只是说 – 它将尝试确定中照片平面并返回它可以识别任何表面的坐标。 此信息可以用于在映像中的人员的次数计数、 （如在图像上绘制指示器 为标记人在照片），或可以将任何其他内容。

从 CoreImage\SampleCode.cs 此代码演示如何创建和使用嵌入图像上的人脸检测：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

将使用填充功能数组`CIFaceFeature`对象 （如果检测到任何对方的面部）。 没有`CIFaceFeature`每个平面。 `CIFaceFeature` 具有以下属性：

-  HasMouthPosition – 嘴是否检测到此表面。
-  HasLeftEyePosition – 左的眼睛是否检测到此表面。
-  HasRightEyePosition – 右眼是否检测到此表面。 
-  MouthPosition – 此表面嘴的坐标。
-  LeftEyePosition – 此表面左眼睛状的标志的坐标。
-  RightEyePosition – 此表面右眼睛状的标志的坐标。


所有这些属性的坐标在左下角 – 与 UIKit 使用左上角的原点不同具有其原点。 在使用坐标时`CIFaceFeature`一定要翻转它们。 CoreImage\CoreImageViewController.cs 此非常基本的自定义映像视图演示如何在图像上绘制表面指示器三角形 (请注意`FlipForBottomOrigin`方法):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

然后在 SampleCode.cs 文件中的映像和功能将分配之前在重绘映像：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

屏幕快照显示了示例输出： UITextView 中显示和绘制到使用 CoreGraphics 源映像上检测到的面部识别功能的位置。

由于的工作方式面部识别它将偶尔检测除了人工面 （如这些无实用价值黑客 ！） 的操作。

## <a name="filters"></a>筛选器

有 50 多个不同的内置筛选器，并且在框架手可扩展的以便可以实现新筛选器。

## <a name="using-filters"></a>使用筛选器

将筛选器应用于映像具有四个不同的步骤： 加载图像、 创建筛选器、 应用该筛选器和保存 （或显示） 结果。

首先，加载图像成为`CIImage`对象。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

其次，创建筛选器类，并设置其属性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，访问`OutputImage`属性并调用`CreateCGImage`方法呈现的最终结果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最后，将映像分配到视图中可以看到结果。 在实际应用程序可以将生成的图像保存到文件系统、 相册、 推文或电子邮件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

这些屏幕截图显示的结果`CISepia`和`CIHueAdjust`CoreImage.zip 所示的筛选器示例代码。

请参阅[调整协定和图像的图像配方亮度](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)有关的示例`CIColorControls`筛选器。

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>列出筛选器和它们的属性

此代码从 CoreImage\SampleCode.cs 输出的内置筛选器的完整列表和其参数。

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

[CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)描述 50 的内置筛选器和它们的属性。 使用上面的代码可以查询的筛选器类，包括参数的默认值和最大和最小的允许值 （它们无法用于在应用筛选器之前验证输入）。

在模拟器上类似如下所示的列表类别输出 – 您可以滚动浏览列表以查看所有筛选器和其参数。

 [![](introduction-to-coreimage-images/coreimage05.png "在模拟器上类似如下所示的列表类别输出")](introduction-to-coreimage-images/coreimage05.png#lightbox)

列出每个筛选器具有已公开为 Xamarin.iOS 中的类，以便您还可以查看程序集浏览器或使用自动完成在适用于 Mac 的 Visual Studio 或 Visual Studio 中的 Xamarin.iOS.CoreImage API。 

## <a name="summary"></a>摘要

本文说明了如何使用某些人脸检测和对映像应用筛选器等新 iOS 5 CoreImage framework 功能。 供你使用的框架中有多个不同的映像筛选器。

## <a name="related-links"></a>相关链接

- [Core 映像 （示例）](https://developer.xamarin.com/samples/CoreImage/)
- [调整协定和亮度的映像原因之一](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用 CoreImage 筛选器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
