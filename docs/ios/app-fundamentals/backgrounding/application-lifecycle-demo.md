---
title: "应用程序生命周期演示"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 56310bb538d9abf850c40ebfb0b0bf551fbb104c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="application-lifecycle-demo"></a>应用程序生命周期演示

在本部分中，我们会将检查应用程序来演示四个应用程序状态，以及的角色`AppDelegate`通知的状态获取更改时应用程序中的方法。 每当应用程序更改状态时，应用程序将打印到控制台的更新：

 [ ![](application-lifecycle-demo-images/image3.png "示例应用程序")](application-lifecycle-demo-images/image3.png)

 [ ![](application-lifecycle-demo-images/image4.png "每当应用程序更改状态时，应用程序将打印到控制台的更新")](application-lifecycle-demo-images/image4.png)

## <a name="walkthrough"></a>演练


  1. 打开_生命周期_项目中_LifecycleDemo_解决方案。
  1. 打开`AppDelegate`类。 请注意，我们已于生命周期方法，以便让我们知道当应用程序已更改状态添加日志记录：

            ```chsarp
                public override void OnActivated(UIApplication application)
                {
                    Console.WriteLine("OnActivated called, App is active.");
                }
                public override void WillEnterForeground(UIApplication application)
                {
                    Console.WriteLine("App will enter foreground");
                }
                public override void OnResignActivation(UIApplication application)
                {
                    Console.WriteLine("OnResignActivation called, App moving to inactive state.");
                }
                public override void DidEnterBackground(UIApplication application)
                {
                    Console.WriteLine("App entering background state.");
                }
                // not guaranteed that this will run
                public override void WillTerminate(UIApplication application)
                {
                    Console.WriteLine("App is terminating.");
                }
            ```

  1. 启动应用程序在模拟器中或在设备上。 `OnActivated` 将在应用启动时调用。 应用程序现已在_Active_状态。
  1. 点击的模拟器或设备以使到后台应用程序上的主页按钮。 `OnResignActivation` 和`DidEnterBackground`将作为应用转换的调用`Active`到`Inactive`并放入`Backgrounded`状态。 因为我们不能给我们的应用程序要在后台执行任何代码，应用程序被视为_Suspended_在内存中。
  1. 导航回到此应用程序，以使其重新置于前台。 `WillEnterForeground` 和`OnActivated`将同时调用：

        ![](application-lifecycle-demo-images/image4.png "打印到控制台的状态更改")

    请注意，我们添加以下代码行到通知我们该应用程序具有后台从输入前台我们视图控制器：

        ```csharp
            UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
                    label.Text = "Welcome back!";
                });
        ```

1. 按**主页**按钮以将应用程序到背景。 然后，双击**主页**按钮以打开应用程序切换器：
    
    ![](application-lifecycle-demo-images/app-switcher-.png "应用程序切换器")
  
1. 在应用程序切换器，找到应用程序并将其删除向上轻扫：
    
    ![](application-lifecycle-demo-images/app-switcher-swipe-.png "若要删除正在运行的应用向上轻扫") 
    
iOS 将终止应用程序。 请记住，`WillTerminate`不会调用它，因为我们不终止的应用程序是_Suspended_在后台。

现在，我们了解 iOS 应用程序状态和转换，我们将看看可用于 backgrounding 在 iOS 中的不同选项。



## <a name="related-links"></a>相关链接

- [LifecycleDemo(Part2) (sample)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
