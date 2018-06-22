---
title: 添加第二个工具栏
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f2255ab5ac7e153266093877a1c52bfc08927a09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766632"
---
# <a name="adding-a-second-toolbar"></a>添加第二个工具栏


## <a name="overview"></a>概述 

`Toolbar`可以执行多个替换操作栏&ndash;它可以在活动内使用多个时间，它可以是可自定义放置任意位置在屏幕上，并且可以将它配置为跨仅屏幕的部分的宽度。 下面的示例说明如何创建第二个`Toolbar`并将其放在屏幕的底部。 这`Toolbar`实现**复制**，**剪切**，和**粘贴**菜单项。 


## <a name="define-the-second-toolbar"></a>定义第二个工具栏 

编辑布局文件**main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

此 XML 将添加第二个`Toolbar`到了一个空的屏幕底部`ImageView`填充屏幕中间。 此高度`Toolbar`设置为操作栏的高度： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

此的背景色`Toolbar`设置为将下一步定义以强调文字颜色：

```xml
android:background="?android:attr/colorAccent
```

请注意此`Toolbar`基于不同的主题 (**ThemeOverlay.Material.Dark.ActionBar**) 比使用`Toolbar`中创建[替换操作栏](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;它未绑定到活动的窗口装饰或使用在第一个主题`Toolbar`。

编辑**Resources/values/styles.xml**并将以下强调文字颜色添加到样式定义： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

这样，底部工具栏上深色琥珀色的颜色。 生成并运行此应用将在屏幕的底部显示一个空白的第二个工具栏： 

[![在屏幕底部的黄色第二个工具栏使用应用程序的屏幕截图](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>添加编辑菜单项 

本部分介绍如何将编辑菜单项添加到底部`Toolbar`。 

若要将菜单项添加到辅助`Toolbar`: 

1.  添加菜单图标添加到`mipmap-`文件夹中的应用程序项目 （如果需要）。

2.  通过添加的额外的菜单资源文件中定义的菜单项的内容**资源/菜单**。 

3.  在活动的`OnCreate`方法，查找`Toolbar`(通过调用`FindViewById`) 和放大量`Toolbar`的菜单。

4.  实现中的单击处理程序`OnCreate`以使新的菜单项。 

下列各节演示此过程的详细信息：**剪切**，**复制**，和**粘贴**菜单项添加到底部`Toolbar`。 



### <a name="define-the-edit-menu-resource"></a>定义编辑菜单资源

在**资源/菜单**子目录，创建新的 XML 文件调用**edit_menus.xml**并将内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此 XML 创建**剪切**，**复制**，和**粘贴**菜单项 (使用已添加到的图标`mipmap-`中的文件夹[替换操作栏](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>放大量菜单

在结束`OnCreate`中的方法**MainActivity.cs**，添加以下代码行： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

此代码查找`edit_toolbar`中定义的视图**main.axml**，将其标题设置为**编辑**，并将其菜单项 (中定义**edit_menus.xml**)。 它定义一个菜单单击显示 toast 以指示点击的编辑图标的处理程序。 

生成并运行应用。 当应用运行时，则会出现的文本和前面添加的图标如下所示： 

[![关系图的底部工具栏与剪切、 复制和粘贴图标](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

点击**剪切**菜单图标会导致显示以下 toast: 

[![屏幕快照的 Toast，该值指示点击的剪切菜单图标](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

点击任一工具栏上的菜单项显示生成 toast: 

[![有关屏幕截图的 Toast 保存，复制并粘贴所点击的菜单项](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>上移按钮 

大多数 Android 应用程序依赖于**回**应用导航按钮; 按**回**按钮将用户带到之前的屏幕。
但是，你可能还想要提供**向上**轻松让用户能够导航"最多"到应用程序的主屏幕的按钮。 当用户选择**向上**按钮，用户将上移到应用层次结构中较高级别的&ndash;，即应用弹出用户返回在后退堆栈而不是砰声回到以前访问的多个活动活动。 

若要启用**向上**使用的第二个活动中的按钮`Toolbar`作为其操作栏中，调用`SetDisplayHomeAsUpEnabled`和`SetHomeButtonEnabled`中第二个活动的方法`OnCreate`方法：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[支持 v7 工具栏](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)代码示例演示如何**向上**操作中的按钮。 此示例 （它使用下文所述的 AppCompat 库） 实现使用工具栏上的第二个活动**向上**回上一个活动的分层导航按钮。 在此示例中，`DetailActivity`主页按钮使**向上**通过进行以下按钮`SupportActionBar`方法调用： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

当用户导航从`MainActivity`到`DetailActivity`、`DetailActivity`显示**向上**按钮 （左箭头） 的屏幕截图中所示：

[![屏幕快照的工具栏中向上按钮左箭头的示例](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

点击此**向上**按钮将导致应用程序返回到`MainActivity`。 在包含多个级别的层次结构的更复杂的应用程序，点击此按钮将返回用户应用程序中的下一步最高级别，而不是上一屏幕。 



## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
