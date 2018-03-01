---
title: "弹出菜单"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: 54b6809b7e27dc87be6d510e4a4b6071e4ae22e7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="popup-menu"></a>弹出菜单

`PopupMenu`类添加用于显示附加到特定的视图的弹出菜单的支持。 下图显示弹出菜单按钮，从出现突出显示就像它选择了第二个项：

 [ ![三个项的包含三个 PopopMenu 示例](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png)

Android 4 添加几个新功能`PopupMenu`，使其稍微容易使用，即：

-   **充气**&ndash;放大量方法现可直接在 PopupMenu 类上。
-   **DismissEvent** &ndash; PopupMenu 类现在具有 DismissEvent。

让我们看看这些改进。 在此示例中，我们具有单个活动包含一个按钮。 当用户单击按钮时，将显示弹出菜单，如下所示：

 [ ![在具有按钮和 3 项弹出菜单的仿真程序中运行的应用程序的示例](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png)

<a name="Creating_a_Popup_Menu" />

## <a name="creating-a-popup-menu"></a>创建弹出菜单

当我们创建的实例`PopupMenu`，我们需要其构造函数的引用进行传递到`Context`，以及菜单附加到的视图。 在这种情况下，我们创建`PopupMenu`在我们的按钮的单击事件处理程序，这名为`showPopupMenu`。
此按钮也是我们将在其中附加的视图`PopupMenu`，如下面的代码中所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

在 Android 3 中，此代码放大量 XML 资源中的菜单需要先获取对的引用`MenuInflator`，然后调用其`Inflate`与包含菜单和菜单实例放到大量的 XML 的资源 ID 的方法。 这种方法仍适用中 Android 4 和更高版本为下面的代码显示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

从 Android 4 开始但是，你可以现在调用`Inflate`实例上直接`PopupMenu`。 这使代码更简洁，如下所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

在上面的代码中，在后 inflating 菜单我们只需调用`menu.Show`若要在屏幕上显示。

<a name="Handling_Menu_Events" />

## <a name="handling-menu-events"></a>处理菜单事件

当用户选择菜单项，`MenuItemClick`将引发事件并将关闭菜单。 点击菜单之外的任意位置只需将关闭它。 在任一情况下，截至 Android 4，当消除菜单上，其`DismissEvent`将引发。 下面的代码添加两个事件处理程序`MenuItemClick`和`DismissEvent`事件：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
            menu.Show ();
};
```



## <a name="related-links"></a>相关链接

- [PopupMenuDemo （示例）](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
