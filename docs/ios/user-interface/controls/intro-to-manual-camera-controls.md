---
title: 在 Xamarin.iOS 手动相机控件
description: 本文档介绍如何 iOS AVFoundation framework 可与 Xamarin.iOS 若要启用手动相机控件。 手动相机控件允许用户访问控制焦点、 白色平衡，以及暴露设置。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: a0f605a38117df87a03801c3b9d86b0b7361c232
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790820"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin.iOS 手动相机控件

通过提供的手动相机控件`AVFoundation Framework`在 iOS 8 中，允许移动应用程序需要完全控制的 iOS 设备的照相机。 此级别细化的控制可以用于创建专业级别的照相机应用程序和通过采取静止图像或视频时调整相机的参数提供艺术家组合。

如果开发科学或行业应用程序，其中结果不太方便的正确性或该映像的好处，方便你更突出显示某些功能或正在执行的映像的元素，这些控件也可以很有用。

## <a name="avfoundation-capture-objects"></a>AVFoundation 捕获对象

是否采用视频或静止的 iOS 设备上使用摄像头的图像，用来捕获这些映像过程是很大程度上相同。 这是 true 的应用程序使用默认的自动相机控件或一种是充分利用新的手动相机控件：

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation 捕获对象概述")](intro-to-manual-camera-controls-images/image1.png#lightbox)

输入取自`AVCaptureDeviceInput`到`AVCaptureSession`通过`AVCaptureConnection`。 结果是任一输出视为静止图像或视频流。 整个过程由控制`AVCaptureDevice`。

## <a name="manual-controls-provided"></a>手动提供的控件

使用 iOS 8 提供的新 Api，应用程序可以控制以下相机功能：

-  **手动对焦**– 通过允许最终用户能够直接控制焦点的应用程序可以提供更好地控制执行的映像。
-  **手动曝光**– 应用程序可以通过提供手动控制透露，向用户提供更自由，使他们可以获得特定样式的外观。
-  **手动白平衡**– 白平衡用于调整在映像中的颜色-通常以使其看起来逼真。 不同的光源具有不同的颜色温度，并调整用于捕获图像的相机设置来弥补这些差异。 同样，通过允许用户控制的白色余额，用户可以进行无法自动执行的调整。


iOS 8 提供扩展和增强功能到现有 iOS Api 来提供此图像上方的细粒度控制捕获过程。

## <a name="bracketed-capture"></a>用括号括起来的捕获

括起来捕获基于来自手动相机控件上面介绍的设置，并允许在各种不同的方式进行捕获时刻，应用程序。

简言之，括起来捕获是静止图像的各种设置从图片图片时执行的突然增加。

## <a name="requirements"></a>要求

以下被需完成这篇文章中提供的步骤：

-  **Xcode 7 + 和 iOS 8 或更高版本**– Apple 的 Xcode 7 和 iOS 8 或更高版本的 Api 需要安装并配置开发人员计算机上。
-  **Visual Studio for Mac** – 应安装最新版本的适用于 Mac 的 Visual Studio，并将其配置用户设备上。
-  **iOS 8 设备**– 运行 iOS 8 的最新版本的 iOS 设备。 无法在 iOS 模拟器中测试相机功能。


## <a name="general-av-capture-setup"></a>常规 AV 捕获安装程序

录制视频 iOS 设备上的时，没有始终是必需的一些常规安装程序代码。 本部分将介绍最小设置所需记录从 iOS 设备的照相机的视频并显示在该视频中实时`UIImageView`。

### <a name="output-sample-buffer-delegate"></a>输出示例缓冲区委托

首要任务之一需要将是一个委托，以监视示例输出缓冲区，并显示从缓冲区获取内容的映像`UIImageView`中应用程序 UI。

以下例程将监视示例缓冲区，并更新 UI:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

与就地情况下，此例程`AppDelegate`可以修改，以打开 AV 捕获会话记录实时视频源。

### <a name="creating-an-av-capture-session"></a>AV 捕获会话的创建

AV 捕获会话用于控制从 iOS 设备的照相机的实时视频录制，并且需要将导入 iOS 应用程序的视频。 由于该示例`ManualCameraControl`示例应用程序中多个不同的位置使用捕获会话，因此将采用配置`AppDelegate`，供整个应用程序。

执行以下操作来修改应用程序的`AppDelegate`和添加必要的代码：


1. 双击`AppDelegate.cs`在解决方案资源管理器，以将其打开以进行编辑的文件。
1. 添加以下 using 语句到文件顶部：
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. 将以下私有变量和到的计算的属性添加`AppDelegate`类：
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. 重写已完成的方法，并将其更改为：
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. 保存对文件所做的更改。


使用此代码中的位置，手动相机控件可以轻松地实现用于试验和测试。

## <a name="manual-focus"></a>手动焦点

通过允许最终用户才能直接焦点的控件，应用程序可以提供更具艺术性控制执行的映像。

例如，专业摄影可以减轻实现，该图像的焦点[Bokeh 效果](http://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Bokeh 效果")](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，创建[焦点拉取效果](http://www.mediacollege.com/video/camera/focus/pull.html)，如：

[![](intro-to-manual-camera-controls-images/image3.png "焦点请求效果")](intro-to-manual-camera-controls-images/image3.png#lightbox)

对于科学家或医疗应用程序的编写器，该应用程序可能想要以编程方式移动小视窗的试验。 无论哪种方式的新 API 允许执行最终用户或应用程序采用控制焦点时图像。

### <a name="how-focus-works"></a>焦点的工作原理

之前讨论的控制焦点 IOS 8 应用程序中的详细信息。 让我们快速了解一下 iOS 的设备中的焦点工作原理：

[![](intro-to-manual-camera-controls-images/image4.png "IOS 的设备中的焦点工作原理")](intro-to-manual-camera-controls-images/image4.png#lightbox)

浅色进入在 iOS 设备上的相机小视窗和侧重于图像传感器。 相对于传感器焦点 （该图像将显示清晰的区域） 所在的传感器控件从小视窗距离。 距离越远重用功能区是从传感器、 距离对象似乎清晰和越接近，附近对象似乎清晰。

在 iOS 设备，小视窗移动更接近，或从 μ 传感器磁铁和 springs。 结果是的小视窗的准确位置是不可能的因为它会有变化设备到设备，但可能会受到参数，如设备的方向或的设备和 spring 年龄。

### <a name="important-focus-terms"></a>重要焦点条款

在处理具有焦点时，有了几个开发人员应熟悉的术语：

-  **字段的深度**– 最近和最远焦点对象之间的距离。 
-  **宏**-这是焦点频谱图的 near 末尾，是小视窗注意力的最近距离。
-  **无穷大**– 这是焦点频谱图的远端，是小视窗注意力的远距离。
-  **Hyperfocal 距离**– 这是帧中最远的对象所在的焦点远端在焦点频谱图中的点。 换而言之，这是最大化深度的字段的焦位置。 
-  **镜头位置**– 这是什么控制了上述所有其他条款。 这是焦点的控制器的从传感器，从而小视窗的距离。


与这些条款和记住的知识，新的手动焦点控件可以成功实现 iOS 8 应用程序中。

### <a name="existing-focus-controls"></a>现有焦点控件

iOS 7 和早期版本中，提供通过现有焦点控件`FocusMode`属性作为：

-   `AVCaptureFocusModeLocked` – 将焦点放在单个焦点的点处锁定。
-   `AVCaptureFocusModeAutoFocus` – 相机扫描通过所有焦点点镜头，直到找到尖锐焦点，然后仍保持在原位。
-   `AVCaptureFocusModeContinuousAutoFocus` – 相机 refocuses 只要它检测到的扩展的焦点条件。


现有的控件还提供一个可设置通过关注点`FocusPointOfInterest`属性，以便用户可以点击能够专注于特定区域。 应用程序还可以通过监视跟踪的小视窗移动`IsAdjustingFocus`属性。

此外，通过提供范围限制`AutoFocusRangeRestriction`属性作为：

-   `AVCaptureAutoFocusRangeRestrictionNear` – 将 autofocus 限制为附近的深度。 在如扫描 QR 代码或条形码的情况下很有用。
-   `AVCaptureAutoFocusRangeRestrictionFar` – 将 autofocus 限制为相距深度。 在认为是不相关的对象在视野 （例如，窗口框架） 中的位置的情况下很有用。


最后是`SmoothAutoFocus`降低自动焦点算法并步骤以较小增量为了避免录制视频时移动项目属性。

### <a name="new-focus-controls-in-ios-8"></a>在 iOS 8 中的新焦点控件

除了已提供 ios 7 和更高版本的功能，现可用于控制在 iOS 8 中的焦点以下功能：

-  完整手动控制锁定焦点时的可重用功能区位置。
-  键 / 值观测到的任何焦点模式中的可重用功能区位置。


以实现上述功能`AVCaptureDevice`类进行了修改，以包括只读`LensPosition`属性用于获取的相机小视窗当前位置。

若要手动控制的小视窗位置，捕获设备必须在锁定焦点模式下。 示例:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`捕获设备的方法用于调整相机功能区的位置。 可以提供可选的回调例程来更改将生效时，收到通知。 示例:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

在上面的代码所示，将必须配置锁定捕获设备，然后才能进行小视窗位置中的更改。 有效的小视窗位置值为介于 0.0 和 1.0 之间。

### <a name="manual-focus-example"></a>手动对焦示例

与常规 AV 捕获安装程序代码放情况下，`UIViewController`可以添加到应用程序的情节提要，并配置，如下所示：

[![](intro-to-manual-camera-controls-images/image5.png "可以添加到应用程序情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image5.png#lightbox)

该视图包含以下主要元素：

-  A`UIImageView`将显示视频源。
-  A `UISegmentedControl` ，将自动从更改焦点模式下，为已锁定。
-  A `UISlider` ，它会显示并更新当前的可重用功能区位置。


执行以下操作来手动焦点控件的在线向上的视图控制器：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始记录加载视图时：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 在自动模式下对照相机，滑块将移动自动相机调整焦点：

    [![](intro-to-manual-camera-controls-images/image6.png "滑块会自动将随着相机调整此示例应用中的焦点")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 点击锁定段并拖动位置滑块手动调整的可重用功能区位置：

    [![](intro-to-manual-camera-controls-images/image7.png "手动调整的小视窗位置")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止应用程序。


上面的代码说明了如何在自动模式下相机时监视的小视窗位置或使用滑块可控制在锁定模式下时的可重用功能区位置。

## <a name="manual-exposure"></a>手动曝光

公开引用相对于源洪亮图像的亮度，并确定由光的多少达到传感器，如何长时间，并通过传感器 （ISO 映射） 增益等级。 通过提供手动控制透露，应用程序可以向最终用户提供更自由，并允许他们获得特定样式的外观。

使用手动公开控件，用户可以执行中不现实亮深色和 moody 的映像：

[![](intro-to-manual-camera-controls-images/image8.png "示例显示从不现实亮暴露深色和 moody 映像")](intro-to-manual-camera-controls-images/image8.png#lightbox)

同样，这可以使用编程控制，自动为科学型应用程序或通过手动提供的应用程序用户界面的控件。 无论哪种方式，新的 iOS 8 公开 Api 提供的照相机的公开设置的细粒度控制。

### <a name="how-exposure-works"></a>公开的工作原理

之前讨论的控制 IOS 8 应用程序中的公开的详细信息。 让我们快速了解一下公开的工作原理：

[![](intro-to-manual-camera-controls-images/image9.png "公开的工作原理")](intro-to-manual-camera-controls-images/image9.png#lightbox)

结合使用来控制公开的三个基本元素为：

-  **停转速度**– 这是快门处于打开状态以让相机传感器光的时间长度。 较短者快门处于打开状态的时间更少 light 允许和明晰映像是 （较少运动模糊）。 时间越长，快门处于打开状态，但详细 light 让在和越运动发生的模糊。
-  **ISO 映射**– 这是从胶片摄影借用的字词，指的是在到 light 胶片化学品的敏感度。 在胶片低 ISO 值具有较少的粒度和更精细度色彩重现;数字传感器上的低 ISO 值具有较少亮度但小于传感器干扰。 ISO 值设置得越高，越明亮的映像但具有更大的传感器噪音。 数字传感器上的"ISO"是一种[电子提升](http://en.wikipedia.org/wiki/Gain)，不是物理的功能。 
-  **镜头小孔**– 这是可重用功能区打开的大小。 在所有的 iOS 设备上被固定的小视窗小孔，因此可以用于调整公开的仅有两个值是快门速度和 ISO。


### <a name="how-continuous-auto-exposure-works"></a>如何连续自动公开工作原理

在学习之前手动曝光的工作原理，这是一个好办法了解如何连续自动公开适用于 iOS 设备。

[![](intro-to-manual-camera-controls-images/image10.png "连续自动公开 iOS 的设备中的工作原理")](intro-to-manual-camera-controls-images/image10.png#lightbox)

首先是自动公开块，其计算理想公开的作业和持续进纸计数统计信息。它使用此信息来计算的 ISO 和快门速度，以获取也亮起场景的最佳组合。 此周期称为遍历循环。

### <a name="how-locked-exposure-works"></a>如何锁定的公开工作原理

接下来，让我们看一下如何锁定的公开适用于 iOS 设备。

[![](intro-to-manual-camera-controls-images/image11.png "如何锁定公开适用于 iOS 设备")](intro-to-manual-camera-controls-images/image11.png#lightbox)

同样，你可以尝试计算的最佳 iOS 和持续时间值自动公开块。 但是，在此模式下要从中块已断开计数统计信息引擎。

### <a name="existing-exposure-controls"></a>现有公开控件

iOS 7 和更高版本，提供以下现有公开控件通过`ExposureMode`属性：

-   `AVCaptureExposureModeLocked` – 一次示例场景，并使用这些值在整个场景。
-   `AVCaptureExposureModeContinuousAutoExposure` – 示例场景持续以确保它很好地亮起。


`ExposurePointOfInterest`可以用来点击以公开场景通过选择一个目标对象来公开，并且应用程序可以监视`AdjustingExposure`属性以查看时调整公开。

### <a name="new-exposure-controls-in-ios-8"></a>在 iOS 8 中的新公开控件

除了已提供 ios 7 和更高版本的功能，现可用于控制在 iOS 8 中的公开以下功能：

-  完全手动自定义的风险。
-  Get、 Set 和键 / 值观察 IOS 和快门速度 （持续时间）。


若要实现更高版本的功能，一个新`AVCaptureExposureModeCustom`模式已添加。 自定义的模式将相机沿时，下面的代码可以用于调整公开持续时间和 ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在自动和已锁定模式中，应用程序可以调整使用下面的代码的自动曝光例程偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小和最大设置范围取决于的设备的应用程序运行，因此它们应永远不会进行硬编码。 相反，使用以下属性来获取最小和最大值的范围：

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


在上面的代码所示，将必须配置锁定捕获设备，然后才能进行中公开的更改。

### <a name="manual-exposure-example"></a>手动曝光示例

与常规 AV 捕获安装程序代码放情况下，`UIViewController`可以添加到应用程序的情节提要，并配置，如下所示：

[![](intro-to-manual-camera-controls-images/image12.png "可以添加到应用程序情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image12.png#lightbox)

该视图包含以下主要元素：

-  A`UIImageView`将显示视频源。
-  A `UISegmentedControl` ，将自动从更改焦点模式下，为已锁定。
-  四个`UISlider`控件将显示和更新偏移量、 持续时间、 ISO 和偏差。


执行以下操作来为手动 Exposure Control 的在线向上的视图控制器：


1. 添加以下 using 语句：
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始记录加载视图时：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 在自动模式下对照相机，滑块将移动自动相机调整公开：

    [![](intro-to-manual-camera-controls-images/image13.png "滑块将会自动移动，如相机调整公开")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 点击锁定段并拖动偏差滑块手动调整自动曝光的偏差：

    [![](intro-to-manual-camera-controls-images/image14.png "手动调整自动曝光的偏差")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 点击的自定义段并拖动持续时间和 ISO 滑块来手动控制公开：

    [![](intro-to-manual-camera-controls-images/image15.png "将持续时间和 ISO 滑块拖动到手动控件公开")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止应用程序。


上面的代码已向演示如何在自动模式下，相机时监视曝光设置以及如何使用滑块来控制透露时锁定或自定义模式。

## <a name="manual-white-balance"></a>手动白色平衡

白平衡控件允许用户调整的在映像以使其看起来更加逼真 colosr 之间的平衡。 不同的光源具有不同的颜色温度，且必须调整用于捕获图像的相机设置来弥补这些差异。 同样，通过允许用户控制白色平衡它们可以进行自动例程不能以实现艺术效果的专业人员调整。

[![](intro-to-manual-camera-controls-images/image16.png "显示手动白平衡调整示例图像")](intro-to-manual-camera-controls-images/image16.png#lightbox)

例如，夏时制具有蓝强制转换，而白帜灯 incandescent 灯具有更暖、 黄色橙色浅色。 （人困惑的是，"冷"颜色具有更高版本的颜色温度比"暖"颜色。 颜色温度是物理的度量值，不感知一个）。

人类大脑是很好地补偿的颜色温度存在差异，但这是指相机无法执行操作。 照相机的工作原理是提升上相反的频谱图针对颜色差异进行调整的颜色。

新的 iOS 8 公开 API 允许应用程序控制过程并提供对摄像头的白色平衡设置细化的控制。

### <a name="how-white-balance-works"></a>如何白色平衡工作原理

之前讨论的控制 IOS 8 应用程序中的白色平衡的详细信息。 让我们快速了解一下如何白色平衡工作原理：

中的颜色感知研究[CIE 1931 RGB 颜色空间和 CIE 1931 XYZ 颜色空间](http://en.wikipedia.org/wiki/CIE_1931_color_space)是第一个数学上定义的色彩空间。 它们在中创建国际佣金上照明 (CIE) 1931年。

[![](intro-to-manual-camera-controls-images/image17.png "CIE 1931 RGB 颜色空间和 CIE 1931 XYZ 颜色空间")](intro-to-manual-camera-controls-images/image17.png#lightbox)

上面的图表显示我们的所有颜色从深蓝为亮的红色的浅绿色肉眼，可见。 关系图上的任意点可绘制具有 X 和 Y 值，上图中, 所示。

为在关系图中可见，可将人工愿景的范围之外的关系图上绘制的 X 和 Y 值，并因此不能通过相机重现这些颜色。

在上面的图表中较小的曲线称为[Planckian 轨迹](http://en.wikipedia.org/wiki/Planckian_locus)，这表示颜色温度 （以度为单位开氏），数字越大，在蓝色端 （数） 和较低数字的一侧红色 （较低）。 这些是适用于典型的照明的情况。

在混合的照明情况下，将需要白色平衡调整偏离 Planckian 轨迹，以使所需的更改。 在这些情况下，将需要进行此调整要移动到绿色或红色/洋红色一端 CIE 缩放。

iOS 设备的提升相反的颜色提升补偿的颜色强制转换。 例如，如果场景具有太多的蓝色，然后红色增益将提升补偿。 这些值校准为特定的设备中，因此它们依赖于设备的提升。

### <a name="existing-white-balance-controls"></a>现有白色平衡控件

iOS 7 和更高版本提供通过现有的下列白平衡控件`WhiteBalanceMode`属性：

-   `AVCapture WhiteBalance ModeLocked` – 示例场景一次和使用整个场景这些值。
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` – 示例场景中，连续从而确保很好地平衡。


和应用程序可以监视`AdjustingWhiteBalance`属性以查看时调整公开。

### <a name="new-white-balance-controls-in-ios-8"></a>在 iOS 8 中的新空白平衡控件

除了已提供 ios 7 和更高版本的功能，现可控制白平衡在 iOS 8 中的以下功能：

-  完全手动控制设备 RGB 获得。
-  Get、 Set 和键 / 值观察设备 RGB 获得。
-  支持使用灰色卡白平衡。
-  转换例程与设备无关的色彩空间。


以实现上述功能`AVCaptureWhiteBalanceGain`结构已添加具有以下成员：

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


最大白色平衡提升目前四 （4)，并可以可从`MaxWhiteBalanceGain`属性。 因此，合法范围是从一 (1) 到`MaxWhiteBalanceGain`(4) 当前。

`DeviceWhiteBalanceGains`属性可以用于观察的当前值。 使用`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`要调整平衡获得当相机处于锁定白色平衡模式。

#### <a name="conversion-routines"></a>转换例程

转换例程已添加到 iOS 8 来帮助进行转换，并从，设备独立的色彩空间。 若要实现的转换例程，`AVCaptureWhiteBalanceChromaticityValues`结构已添加具有以下成员：

-   `X` -为从 0 到 1 的值。
-   `Y` -为从 0 到 1 的值。


`AVCaptureWhiteBalanceTemperatureAndTintValues`结构也已添加具有以下成员：

-   `Temperature` -是浮点点以开氏度为单位的值。
-   `Tint` -从绿色或洋红色从 0 到 150 具有正值朝向绿色方向和负推进到以洋红色是的偏移量。


使用`CaptureDevice.GetTemperatureAndTintValues`和`CaptureDevice.GetDeviceWhiteBalanceGains`温度和浅色、 色度和 RGB 之间进行转换的方法获得的色彩空间。

> [!NOTE]
> 要转换的值越接近 Planckian 轨迹，转换例程是更准确。




#### <a name="gray-card-support"></a>灰色卡支持

Apple 使用术语灰色 World 来指代内置 iOS 8 的灰色卡支持。 它允许用户若要专注于物理灰色卡该类适用于至少 50%的框的中心，使用该值来调整白平衡。 灰色卡旨在实现出现非特定的空白。

这可以在应用程序中实现，通过提示用户将前面相机，物理灰色卡监视`GrayWorldDeviceWhiteBalanceGains`属性和值结算之前保持等待。

应用程序然后将锁定使白平衡获得`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`方法使用从值`GrayWorldDeviceWhiteBalanceGains`属性，将应用所做的更改。

必须配置锁定捕获设备，才能使白平衡中的更改。

### <a name="manual-white-balance-example"></a>手动白色平衡示例

与常规 AV 捕获安装程序代码放情况下，`UIViewController`可以添加到应用程序的情节提要，并配置，如下所示：

[![](intro-to-manual-camera-controls-images/image18.png "可以添加到应用程序情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image18.png#lightbox)

该视图包含以下主要元素：

-  A`UIImageView`将显示视频源。
-  A `UISegmentedControl` ，将自动从更改焦点模式下，为已锁定。
-  两个`UISlider`控件将显示和更新的温度和浅色。
-  A`UIButton`用于示例灰色卡 (灰色 World) 空间和设置空白平衡使用这些值。


执行以下操作来为手动空白余额控制的网络向上的视图控制器：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 添加以下私有方法以设置新的空白温度和浅色之间实现平衡：

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始记录加载视图时：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 保存所做的更改的代码并运行应用程序。
1. 在自动模式下对照相机，滑块将移动自动相机调整白色平衡：

    [![](intro-to-manual-camera-controls-images/image19.png "滑块将会自动移动，如相机调整白色平衡")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 点击锁定段并拖动 Temp 和浅色滑块手动调整白色平衡：

    [![](intro-to-manual-camera-controls-images/image20.png "拖动 Temp 和浅色滑块手动调整白色平衡")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 使用了已锁定段仍处于选中状态，将在前面的相机灰色物理卡，然后点击灰色卡按钮以调整白色平衡到灰色世界：

    [![](intro-to-manual-camera-controls-images/image21.png "点击灰色卡按钮以调整白色平衡到灰色 World")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止应用程序。

上面的代码说明了如何在自动模式下相机时监视白色平衡设置或者使用滑块来控制白色平衡，在锁定模式下时。

## <a name="bracketed-capture"></a>用括号括起来的捕获

括起来捕获基于来自手动相机控件上面介绍的设置，并允许在各种不同的方式进行捕获时刻，应用程序。

简言之，括起来捕获是静止图像的各种设置从图片图片时执行的突然增加。

[![](intro-to-manual-camera-controls-images/image22.png "括起来捕获的工作原理")](intro-to-manual-camera-controls-images/image22.png#lightbox)

使用 iOS 8 中的括起来捕获，应用程序可以预设的一系列手动相机控件、 发出单个命令和具有当前场景手动预设的每个返回一系列图像。

### <a name="bracketed-capture-basics"></a>用括号括起来的捕获基础知识

同样，括起来捕获是静止图像的不同设置从图片图片时执行的突然增加。 括起来捕获可用的类型包括：

-  **自动公开括号**– 所有映像都具有不同的偏移量。
-  **手动公开括号**– 所有映像都具有不同的快门速度 （持续时间） 和 ISO 量。
-  **简单迸发括号**– 一系列仍在短期内执行的图像。


### <a name="new-bracketed-capture-controls-in-ios-8"></a>括起来捕获中新的控制 iOS 8

在中实现，所有括起来捕获命令`AVCaptureStillImageOutput`类。 使用`CaptureStillImageBracket`方法以获取一系列图像使用的设置给定的数组。

已执行两个新类来处理设置：

-   `AVCaptureAutoExposureBracketedStillImageSettings` – 它具有一个属性， `ExposureTargetBias`，可用来设置自动公开括号偏差。 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – 它具有两个属性，`ExposureDuration`和`ISO`，可用来为手动曝光括号设置快门速度和 ISO。 


### <a name="bracketed-capture-controls-dos-and-donts"></a>用括号括起来的捕获控制注意事项

#### <a name="dos"></a>打造

下面是使用括起来捕获 iOS 8 中的控件时应完成的操作的列表：

-  通过调用的最坏情况捕获情况准备应用`PrepareToCaptureStillImageBracket`方法。
-  假定示例缓冲区要来自相同的共享池。
-  若要释放通过以前准备调用已分配的内存，调用`PrepareToCaptureStillImageBracket`再次并将其发送一个对象的数组。


#### <a name="donts"></a>注意事项

下面是使用括起来捕获 iOS 8 中的控件时不应执行的操作的列表：

-  不要混合中单个捕获括起来捕获设置类型。
-  没有请求多个`MaxBracketedCaptureStillImageCount`中单个捕获的映像。


### <a name="bracketed-capture-details"></a>用括号括起来的捕获详细信息

使用在 iOS 8 中的括起来捕获时，应考虑以下详细信息：

-  用括号括起来的设置临时替代`AVCaptureDevice`设置。
-  忽略 flash 和仍映像达到稳定状态设置。
-  所有映像必须都使用相同的输出格式 （jpeg、 png，等等。）
-  视频预览可能会丢弃帧。
-  与 iOS 8 兼容的所有设备都支持用括号括起来的捕获。


使用记住此信息，让我们看看使用 iOS 8 中的括起来捕获的示例。

### <a name="bracket-capture-example"></a>括号捕获示例

与常规 AV 捕获安装程序代码放情况下，`UIViewController`可以添加到应用程序的情节提要，并配置，如下所示：

[![](intro-to-manual-camera-controls-images/image23.png "可以添加到应用程序情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image23.png#lightbox)

该视图包含以下主要元素：

-  A`UIImageView`将显示视频源。
-  三个`UIImageViews`将显示捕获的结果。
-  A`UIScrollView`容纳视频源和结果视图。
-  A`UIButton`用于与一些预设设置采用括起来捕获。


执行以下操作来为括起来捕获的网络向上的视图控制器：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. 添加以下私有变量：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. 添加以下私有方法以生成所需的输出图像视图：

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. 重写`ViewDidAppear`方法并添加以下代码：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. 保存所做的更改的代码并运行应用程序。
1. 添加边框场景以及点击捕获括号按钮：

    [![](intro-to-manual-camera-controls-images/image24.png "添加边框场景以及为点击捕获括号按钮")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 轻扫从右到左若要查看执行括起来捕获三个映像：

    [![](intro-to-manual-camera-controls-images/image25.png "轻扫从右到左若要查看三个执行括起来捕获的映像")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止应用程序。


上面的代码说明了如何配置并在 iOS 8 中其执行自动泄露括起来捕获。

## <a name="summary"></a>总结

这篇文章中我们有涵盖新的手动相机控件提供的 iOS 8 的简介，并涵盖它们执行的操作以及它们如何工作的基础知识。 我们已授予手动焦点，手动公开和手动白平衡的示例。 最后，我们赋给采用括起来捕获使用前面所述的手动相机控件示例

## <a name="related-links"></a>相关链接

- [ManualCameraControls （示例）](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
