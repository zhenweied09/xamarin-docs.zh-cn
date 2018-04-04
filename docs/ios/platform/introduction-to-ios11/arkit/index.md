---
title: ARKit 简介
description: 适用于 iOS 11 的增强的现实
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: f48cdd48e63131fe234fef1bb60b555724dd8a92
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-arkit"></a>ARKit 简介

_适用于 iOS 11 的增强的现实_

ARKit 使各种增强的现实应用程序和游戏。 此节涵盖以下主题：

- [Getting Started with ARKit](#gettingstarted)
- [使用 UrhoSharp ARKit](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Getting Started with ARKit

若要开始使用增强现实，下面的说明，指导通过简单的应用程序： 定位三维模型并让 ARKit 将模型保存在位置使用其跟踪功能。

![浮点照相机中图像的 jet 三维模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1.添加三维模型

资产应添加到与项目**SceneKitAsset**生成操作。

![在项目中的 SceneKit 资产](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2.将此视图配置

中的视图控制器`ViewDidLoad`方法，加载场景资产并设置`Scene`视图上的属性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3.（可选） 实现会话委托

尽管无需进行简单的情况下，实现会话委托可以帮助进行调试的 ARKit 会话 （和实际的应用程序，向用户提供反馈） 的状态。 创建一个简单的委托，使用以下代码：

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

分配中的委托中`ViewDidLoad`方法：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4.世界上的位置三维模型

在`ViewWillAppear`，下面的代码建立 ARKit 会话并设置三维模型的位置相对于设备的照相机的空间中：

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

每次运行或恢复后，应用程序时将相机的前面定位三维模型。 一旦位于模型，移动照相机并观看像 ARKit 总是以定位该模型。

### <a name="5-pause-the-augmented-reality-session"></a>5.暂停增强的现实会话

很好的做法暂停视图控制器不可见的 ARKit 会话 (在`ViewWillDisappear`方法：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>总结

上面的代码生成的一个简单的 ARKit 应用程序。 更复杂的示例预期托管增强的现实会话来实现的视图控制器`IARSCNViewDelegate`，并实现其他方法。

ARKit 提供大量更复杂的功能，如图面跟踪和用户交互。 请参阅[UrhoSharp 演示](urhosharp.md)有关组合 ARKit 与 UrhoSharp 跟踪示例。


## <a name="related-links"></a>相关链接

- [增强的现实 (Apple)](https://developer.apple.com/arkit/)
- [使用 UrhoSharp ARKit](urhosharp.md)
- [简单 ARKit (Jet) 示例](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit 放置对象 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [引入 ARKit-增强现实适用于 iOS (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/602/)
