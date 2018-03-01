---
title: Vision Framework
ms.topic: article
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2016
ms.openlocfilehash: fb1b5b11ef0117a40267f805621797c3aee04810
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="vision-framework"></a>Vision Framework

Vision 框架就会将大量新图像处理功能，可对 iOS 11 中，包括：

- [矩形检测](#rectangles)
- [人脸检测](#faces)
- 机器学习图像分析 (中所述[CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- 条形码检测
- 图像对齐方式分析
- 文本检测
- 水平分割检测
- 对象检测和跟踪

![检测到具有三个拍摄照片](vision-images/found-rectangles-tiny.png) ![拍摄照片，检测到的两个外观](vision-images/xamarin-home-faces-tiny.png)

下面更详细地讨论了矩形检测和人脸检测。

<a name="rectangles" />

## <a name="rectangle-detection"></a>矩形检测

[VisionRects 示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)演示如何处理图像，并在其上绘制检测到的矩形。

### <a name="1-initialize-the-vision-request"></a>1.初始化愿景请求

在`ViewDidLoad`，创建`VNDetectRectanglesRequest`引用`HandleRectangles`将每个请求结束时调用的方法：

`MaximumObservations`还应设置属性，否则为它将默认为 1 并将返回单个结果。

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2.启动愿景处理

下面的代码启动处理请求。 在**VisionRects**示例，此代码运行后用户所选映像：

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`愿景 framework`VNDetectRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理愿景处理的结果

矩形检测完成后，框架所执行`HandleRectangles`方法，其中摘要如下所示：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4.显示结果

`OverlayRectangles`中的方法**VisionRectangles**示例有三个函数：

- 呈现源映像中，
- 绘制一个矩形，以指示每个已检测到和
- 添加使用 CoreGraphics 每个矩形的文本标签。

视图[示例的源](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)确切 CoreGraphics 方法。

![检测到具有三个拍摄照片](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5.进一步的处理

矩形检测是通常只是第一步链中的操作，如与[CoreMLVision 本例](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision)，其中矩形传递给一个 CoreML 模型来分析手写的数字。


<a name="faces" />

## <a name="face-detection"></a>人脸检测

[VisionFaces 示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)的类似的方式在工作**VisionRectangles**示例，使用不同的愿景请求类。

### <a name="1-initialize-the-vision-request"></a>1.初始化愿景请求

在`ViewDidLoad`，创建`VNDetectFaceRectanglesRequest`引用`HandleRectangles`将每个请求结束时调用的方法。

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2.启动愿景处理

下面的代码启动处理请求。 在**VisionFaces**此代码运行后用户所选映像的示例：

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`愿景 framework`VNDetectFaceRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理愿景处理的结果

人脸检测完成后，执行处理程序`HandleRectangles`方法执行错误处理，并显示检测到的表面，并在结尾调用边界`OverlayRectangles`原始图上绘制边界矩形：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4.显示结果

`OverlayRectangles`中的方法**VisionFaces**示例有三个函数：

- 呈现源映像中，
- 绘制检测到，每个字体的矩形和
- 添加使用 CoreGraphics 每个字体的文本标签。

视图[示例的源](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)确切 CoreGraphics 方法。

![拍摄照片，检测到的两个外观](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5.进一步的处理

Vision framework 包含更多功能，检测面部识别功能，如眼睛和嘴。 使用`VNDetectFaceLandmarksRequest`类型，它将返回`VNFaceObservation`结果，如步骤 3 更高版本，但与其他`VNFaceLandmark`数据。


## <a name="related-links"></a>相关链接

- [Vision 矩形 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Vision 表面 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Core 映像-筛选器、 裸机、 的愿景，和多 (WWDC) （视频） 中的高级功能](https://developer.apple.com/videos/play/wwdc2017/510/)
