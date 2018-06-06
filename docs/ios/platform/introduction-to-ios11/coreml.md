---
title: 在 Xamarin.iOS CoreML 简介
description: 本文档介绍 CoreML，这样在 iOS 上的机器学习。 本文档讨论了如何开始使用 CoreML 以及如何使用愿景 framework。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: b893fe5e56cc2d43a71870ffbbd20f0b8c6cfd18
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787489"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>在 Xamarin.iOS CoreML 简介

_机器学习有关 iOS 11 上的移动应用_

CoreML 使到 iOS 的机器学习 – 应用可以利用训练的机器学习模型，来执行各种类型的任务，从图像识别到解决的问题。

本简介涵盖以下方面：

- [Getting Started with CoreML](#coreml)
- [使用随愿景框架 CoreML](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Getting Started with CoreML

这些步骤介绍如何将 CoreML 添加到 iOS 项目。 请参阅[Mars 人类生存环境 Pricer 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)有关的可行的示例。

![Mars 人类生存环境价格预测值示例屏幕快照](coreml-images/marspricer-heading.png)

### <a name="1-add-the-model-to-the-project"></a>1.向项目添加模型

添加编译后的模型 (具有的目录 **.modelc**扩展) 到**资源**项目目录。 目录的内容应具有生成操作的**BundleResource**:

![该资源文件夹应包含编译后的模型](coreml-images/resources-modelc.png)

[示例](https://developer.xamarin.com/samples/monotouch/ios11/)使用模型在 Xcode 9 中编译或手动使用以下终端命令：

```csharp
xcrun coremlcompiler compile {model.mlmodel} {outputFolder}
```

> [!NOTE]
> **.model**文件_必须_编译为 **.modelc**它们可以由 CoreML 之前

### <a name="2-load-the-model"></a>2.加载模型

使用一个模型之前, 加载它使用`MLModel.FromUrl`静态方法：

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.FromUrl(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3.设置的参数

模型参数传递和切出使用的容器类，实现`IMLFeatureProvider`。

功能提供程序类的行为类似于字符串的字典和`MLFeatureValue`s，其中每个功能值可能是一个简单的字符串或数量、 数组或数据或包含图像的像素缓冲区。

单值功能提供程序的代码所示：

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

使用如下的类，可通过 CoreML 理解的方式提供输入的参数。 功能的名称 (如`myParam`中的代码示例) 必须与匹配的模型的要求。

### <a name="4-run-the-model"></a>4.运行模型

使用模型需要的功能提供程序要实例化和参数设置，然后，`GetPrediction`方法调用：

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5.提取结果

预测结果`outFeatures`也是的一个实例`IMLFeatureProvider`; 输出可以使用访问值`GetFeatureValue`替换每个输出参数的名称 (如`theResult`)，如下例所示：

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>使用随愿景框架 CoreML

CoreML 还可与愿景 framework 结合在映像，如形状识别、 对象标识和其他任务上执行操作。

下面的步骤介绍如何 CoreML 和愿景一起使用在[CoreMLVision 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)。 示例汇集[矩形识别](~/ios/platform/introduction-to-ios11/vision.md#rectangles)从的愿景 framework _MNINSTClassifier_ CoreML 模型来标识面部照片的手写的数字。

![图像识别的数字 3](coreml-images/vision3.png) ![图像识别的数字 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1.创建 Vision CoreML 模型

CoreML 模型_MNISTClassifier_加载和稍后被包装在`VNCoreMLModel`这使得该模型可用于构想任务。 此代码还创建两个愿景请求： 第一次在映像中，查找矩形，然后用于处理与 CoreML 模型矩形：

```csharp
// Load the ML model
var assetPath = NSBundle.MainBundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
var mlModel = MLModel.FromUrl(assetPath, out NSError mlErr);
var vModel = VNCoreMLModel.FromMLModel(mlModel, out NSError vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(vModel, HandleClassification);
```

类仍需要实现`HandleRectangles`和`HandleClassification`对于愿景请求，步骤 3 和 4 下面所示的方法。

### <a name="2-start-the-vision-processing"></a>2.启动愿景处理

下面的代码启动处理请求。 在**CoreMLVision**示例，此代码运行后用户所选映像：

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`愿景 framework`VNDetectRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理愿景处理的结果

矩形检测完成后，它会执行`HandleRectangles`方法，可将图像提取的第一个矩形的裁剪，将矩形映像转换为灰度，并将其传递给分类的 CoreML 模型。

`request`参数传递给此方法包含愿景请求的详细信息和使用`GetResults<VNRectangleObservation>()`方法，则返回的矩形图中找到的列表。 第一个矩形`observations[0]`是提取并传递给 CoreML 模型：

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] { ClassificationRequest }, out NSError err);
  });
}
```

`ClassificationRequest`初始化在步骤 1，以使用`HandleClassification`下一步中定义的方法。

### <a name="4-handle-the-coreml"></a>4.句柄 CoreML

`request`参数传递给此方法包含 CoreML 请求的详细信息和使用`GetResults<VNClassificationObservation>()`方法，它将返回按置信度排序的可能结果的列表 (最高置信度第一个):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```



## <a name="samples"></a>示例

有三个 CoreML 样本尝试：

* [Mars 人类生存环境价格预测值示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)具有简单的数值输入和输出。

* [设想和 CoreML 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)接受图像参数，并使用愿景 framework 来标识在图中，传递给识别单个数字 CoreML 模型的方形区域。

* 最后， [CoreML 图像识别示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)CoreML 用于标识一张照片中的功能。 默认情况下，它使用的较小者**SqueezeNet**模型 (5 MB)，但是它已写入，以便你可以下载并将合并较大**VGG16**模型 (553 MB)。 有关详细信息，请参阅[示例的自述文件](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。


## <a name="related-links"></a>相关链接

- [机器学习 (Apple)](https://developer.apple.com/machine-learning/)
- [CoreML 示例 （人类生存 Mars 环境） （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML 和愿景 （数识别） （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [CoreML 图像识别 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML 与 Azure 自定义愿景 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [简介 CoreML (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/703/)
