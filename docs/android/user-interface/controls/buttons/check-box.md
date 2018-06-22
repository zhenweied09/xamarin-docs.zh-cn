---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 85c505d03e7a763b24fb176b6a94c0fe43009b79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765748"
---
# <a name="checkbox"></a>CheckBox

在本部分中，你将创建一个用于选择项，复选框使用[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)小组件。 按下的复选框时的 toast 消息将指示该复选框的当前状态。

打开**Resources/layout/Main.axml**文件并添加[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)元素 (内[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

若要在状态更改时执行某些操作，请将下面的代码添加到末尾的[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)方法：

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

这将捕获[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)元素从布局中，然后处理 Click 事件，定义单击复选框时进行的操作。 单击时， [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)属性调用以检查新状态的复选框。 如果已选中它，则[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)显示消息"选定"，否则为它将显示"未选择"。 [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)处理它自己的状态更改，因此你只需要查询的当前状态。

运行它。

**提示：** 如果你需要自行更改的状态 (例如，当加载已保存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference)，使用[ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)属性 setter 或[ `Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)方法。

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).
