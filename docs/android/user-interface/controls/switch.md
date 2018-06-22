---
title: 开关
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f4bfc3646f1ccd956ee8151468b3de20f6e1e2b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762654"
---
# <a name="switch"></a>开关

`Switch`小组件 （如下所示） 允许用户两个状态，例如在之间切换或禁用。 `Switch`默认值为 OFF。 小组件在其 ON 和 OFF 状态如下所示：

[![关闭和打开状态中的交换机小组件的屏幕快照](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>创建交换机

若要创建交换机，只需声明`Switch`XML 中的元素，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

这会创建一个基本的交换机，如下所示：

[![在关闭状态中显示交换机的演示应用程序的屏幕截图](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>更改默认值

文本，该控件将显示为 ON 和 OFF 状态以及默认值均不可配置。 例如，若要使切换默认为 ON，而不是灭/亮读取否 / 是，我们可以设置`checked`， `textOn`，和`textOff`下面的 XML 中的属性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供一个标题

`Switch`小组件还支持通过设置包括的文本标签`text`属性，如下所示：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此标记产生在运行时的以下屏幕截图：

[![使用文本水平前面交换机小组件的演示应用程序的屏幕截图](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

当`Switch`的值更改，它会发出`CheckedChange`事件。
例如，下面的代码中我们捕获此事件，并提供`Toast`一条消息的小组件基于`isChecked`值`Switch`，将作为的一部分传递给事件处理程序`CompoundButton.CheckedChangeEventArg`自变量。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相关链接

- [SwitchDemo （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/SwitchDemo/)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
