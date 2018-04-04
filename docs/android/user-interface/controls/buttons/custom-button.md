---
title: 自定义按钮
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e6fc3fe4c3cb89d74188557615f58cc8e34f5991
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="custom-button"></a>自定义按钮

在本部分中，你将创建一个按钮与自定义映像而不是文本，使用[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)小组件和定义三个不同的映像以不同的按钮状态使用一个 XML 文件。 按下按钮时，将显示一条短消息。

右键单击并下载这三个图像，然后将其复制到**可资源/绘制**你的项目目录。 这些将用于不同按钮状态。

 [![正常状态的绿色 Android 图标](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![已设定焦点状态的橙色 Android 图标](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![黄色 Android 按下状态图标](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

创建中的新文件**可资源/绘制**名为目录**android_button.xml**。 插入以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

这定义单个可绘制资源，这将更改按钮的当前状态基于其映像。 第一个`<item>`定义**android_pressed.png**作为在按下按钮的图像 （它已激活）; 第二个`<item>`定义**android_focused.png**作为图像时已设定焦点的按钮 （当突出显示按钮时使用的跟踪球或方向键）;第三个`<item>`定义**android_normal.png** （时没有按下或已设定焦点） 的正常状态的图像。 此 XML 文件现在表示单个可绘制资源和当被引用时[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)有关其背景信息，显示的图像将根据更改以下三种状态。


> [!NOTE]
> 顺序`<item>`元素很重要。 这可绘制引用时，`<item>`是否遍历按顺序来确定哪一个适合于当前的按钮状态。
> "正常"映像是最后一个对话框，因为它是仅应用的时条件`android:state_pressed`和`android:state_focused`已经评估 false。

打开**Resources/layout/Main.axml**文件并添加[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)元素：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background`特性指定要使用的按钮背景的绘制资源 (其中，在保存时**Resources/drawable/android.xml**，引用了作为`@drawable/android`)。 这将替换用于在整个系统的按钮的正常背景图像。 为了使可绘制要更改其基于按钮状态的图像，图像必须应用于背景。

若要使按钮执行某按下时，请将以下代码添加在结束[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)方法：

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

这将捕获[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)从布局中，然后添加[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)时显示消息[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)单击。

现在运行应用程序。


*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).
