---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 1267491f2d9b7519f76651df059722420fa8e1eb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="radiobutton"></a>RadioButton

在本部分中，你将创建两个互相排斥单选按钮 （启用一个禁用另），使用[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)和[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)小组件。 按下任意单选按钮时，将显示的 toast 消息。


打开**Resources/layout/Main.axml**文件并添加两个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s，嵌套在[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (内[ `LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

很重要， [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s 组合在一起[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)元素，以便可以一次选择不超过一个。 由 Android 系统自动处理此逻辑。 当一个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)内选择一个组，其他所有类型是自动取消选择。

若要执行某些操作时每个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)是选择，我们需要编写事件处理程序：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，在传递的发件人被强制转换到单选按钮。
则[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)消息显示所选的单选按钮的文本。

现在，在底部[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)方法，添加以下：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

这将捕获的每个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s 从布局，并将每个新创建的事件 handlerto 添加。

运行该应用程序。

**提示：**如果你需要自行更改的状态 (例如，当加载已保存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用[ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)属性 setter 或[ `Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)方法。

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/). 
