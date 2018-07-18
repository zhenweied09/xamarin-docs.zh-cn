---
title: 编辑文本
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d6be8ae1587742a8c2a37b22b2da3701187dde2a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774598"
---
# <a name="edit-text"></a>编辑文本

在本部分中，你将创建用户输入，使用的文本字段[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)小组件。 一旦文本输入字段之后，"Enter"键将 toast 消息中显示文本。

打开<code>Resources\layout\main.xml</code>文件并添加[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)元素 (内[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

若要使用用户键入时，将以下代码添加到末尾的文本执行某些操作[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/)方法：

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

这将捕获[ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/)元素的布局和集[ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)处理程序，定义要该小组件具有焦点时按下某个键时进行的操作。 在这种情况下，定义方法以侦听 Enter 键 （时按下），然后弹出[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)与已输入的文本的消息。 [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)属性应始终为`true`如果事件已处理，以便事件不泛 （这将导致文本字段中返回一个回车符）。

运行该应用程序。

*此页的部分是基于创建的工作的修改和* [ *由 Android Open Source Project 共享*](http://code.google.com/policies.html) *根据中所述的条款，并用* [ *创意 Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/) *。本教程基于* [ *Android 窗体内容教程*](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*



## <a name="related-links"></a>相关链接

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
