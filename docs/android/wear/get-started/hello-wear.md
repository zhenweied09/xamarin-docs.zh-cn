---
title: "Hello 磨损"
description: "创建第一个 Android 磨损应用并在磨损模拟器或设备上运行它。 本演练提供有关创建处理按钮单击事件并在磨损设备上显示单击计数器的小 Android 磨损项目的分步说明。 它还说明了如何调试使用磨损仿真程序或磨损设备通过蓝牙连接到 Android 手机的应用。 它还提供有关 Android 磨损的一套调试的提示。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: bac9402df544a57cceb98e816e268eb2dcb99c72
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="hello-wear"></a>Hello 磨损

_创建第一个 Android 磨损应用并在磨损模拟器或设备上运行它。本演练提供有关创建处理按钮单击事件并在磨损设备上显示单击计数器的小 Android 磨损项目的分步说明。它还说明了如何调试使用磨损仿真程序或磨损设备通过蓝牙连接到 Android 手机的应用。它还提供有关 Android 磨损的一套调试的提示。_

![屏幕截图的磨损应用以在本教程中完成](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>第一个磨损应用

请按照下列步骤以创建第一个 Xamarin.Android 磨损应用：

### <a name="1-create-a-new-android-project"></a>1.创建新的 Android 项目

创建一个新**磨损 Android 应用程序**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在新项目对话框中创建新的 Android 磨损应用程序](hello-wear-images/vs/new-solution-sml.png)](hello-wear-images/vs/new-solution.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在新的解决方案对话框创建新的 Android 磨损应用程序](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png)

-----


此模板自动包括**Xamarin Android Wearable 库**NuGet （和依赖关系） 以便将可以访问特定于磨损的小组件。 如果看不到磨损模板，请查看[安装和设置](~/android/wear/get-started/installation.md)指南请仔细检查你已安装支持的 Android SDK。 

### <a name="2-choose-the-correct-target-framework"></a>2.选择正确**目标框架**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

确保**到目标最低 Android**设置为**Android 5.0 （棒糖形）**或更高版本： 

[![将目标框架设置为 Visual Studio 中的 Android 5.0](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

确保目标框架设置为**Android 5.0 （棒糖形）**或更高版本：

[ ![在设计时将目标框架设置为 Visual Studio 中的 Android 5.0 for Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png)

-----

设置目标框架的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


### <a name="3-edit-the-mainaxml-layout"></a>3.编辑**main.axml**布局

配置要包含的布局`TextView`和`Button`的示例： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4.编辑**MainActivity.cs**源

添加代码以递增计数器并将其显示时单击该按钮： 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5.安装程序的模拟器或设备

下一步是设置要部署并运行应用的模拟器或设备。 如果你尚不熟悉部署和运行的进程 Xamarin.Android 应用程序一般情况下，请参阅[Hello，Android 快速入门](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果没有如 Android 磨损 Smartwatch Android 磨损设备，你可以在模拟器上运行应用程序。 有关调试的仿真程序上的磨损应用的信息，请参阅[在模拟器上调试 Android 磨损](~/android/wear/deploy-test/debug-on-emulator.md)。

如果你没有如 Android 磨损 Smartwatch Android 磨损设备，你可以在运行应用而不是使用仿真程序在设备上。 有关在磨损设备上进行调试的详细信息，请参阅[磨损设备上调试](~/android/wear/deploy-test/debug-on-device.md)。


### <a name="6-run-the-android-wear-app"></a>6.运行 Android 磨损应用

Android 磨损设备应出现在设备下拉列表中。 请确保选择正确的 Android 磨损设备或 AVD 开始调试之前。 选择后该设备，单击播放按钮，将应用部署到仿真程序或设备。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![在 Visual Studio 设备菜单中选择磨损 AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![为 Mac 设备菜单在 Visual Studio 中选择磨损 AVD](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png)

-----

你可能会看到**只需一分钟...**在第一个消息 （或某些其他间隙屏幕）： 

![观看仿真程序将显示只需一分钟...](hello-wear-images/please-wait.png)

如果你使用的监视仿真程序，可能需要一段时间才能启动应用程序。 当使用蓝牙时，它采用更多时间来部署应用于通过 USB。 （例如，它需要大约 5 分钟将此应用程序部署到是蓝牙连接到 Nexus 5 phone LG G 监视。）

应用程序成功部署后，磨损设备的屏幕应显示类似于以下屏幕：

[![磨损应用程序的初始屏幕](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png)

点击**单击我 ！** 按钮的表面上磨损设备并与每个 tap 计数增量，请参阅：

[![带屏幕截图 3 次单击后的应用程序](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png)


## <a name="next-steps"></a>后续步骤

签出[磨损示例](https://developer.xamarin.com/samples/android/Android%20Wear/)包括 Android 磨损助理 Phone 应用使用的应用程序。

当你准备好分发应用程序，请参阅[使用打包](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相关链接

- [单击我的应用程序 （示例）](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
