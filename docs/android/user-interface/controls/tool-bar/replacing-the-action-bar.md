---
title: "替换操作栏"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 91d5612991c2297418cf7003c499c1a1bbfc7558
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="replacing-the-action-bar"></a>替换操作栏

<a name="overview" />

## <a name="overview"></a>概述

最常见的一个用途为`Toolbar`是替换自定义的默认操作栏`Toolbar`（创建新的 Android 项目后，它使用默认操作栏）。 因为`Toolbar`提供了将品牌的徽标、 标题、 菜单项、 导航按钮和甚至是自定义视图添加到活动的应用程序栏部分的能力 UI 中，它提供了重大升级的默认操作栏上方。

若要将与应用的默认操作栏`Toolbar`: 

1.  创建一个新的自定义主题，并修改应用程序的属性，以便它使用此新主题。 

2.  禁用`windowActionBar`属性中的自定义主题，并启用`windowNoTitle`属性。

3.  为定义的布局`Toolbar`。

4.  包括`Toolbar`中活动的布局**main.axml**布局文件。 

5.  将代码添加到活动的`OnCreate`方法来查找`Toolbar`并调用`SetActionBar`安装`ToolBar`作为操作栏。

以下各节介绍此过程的详细信息。 创建简单的应用程序和其操作栏替换为使用自定义`Toolbar`。 


<a name="start_project" />

## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**ToolbarFun** (请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)有关创建新的 Android 项目的详细信息)。 创建此项目后，将目标和最小值 Android API 级别设置为**Android 5.0 (API 级别 21-棒糖形)**。 有关设置 Android 版本级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。 当生成和运行应用程序时，它显示的默认操作栏，此屏幕截图中所示： 

[![默认操作栏的屏幕截图](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png)


<a name="custom_theme" />

## <a name="create-a-custom-theme"></a>创建自定义主题

打开**资源/值**目录和创建新的文件称为**styles.xml**。 其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

此 XML 定义新的自定义主题调用**MyTheme**基于**Theme.Material.Light.DarkActionBar**棒糖形中的主题。 `windowNoTitle`属性设置为`true`隐藏的标题栏： 

```xml
<item name="android:windowNoTitle">true</item>
```

若要显示自定义的工具栏中，默认值`ActionBar`必须禁用： 

```xml
<item name="android:windowActionBar">false</item>
```

Olive-green`colorPrimary`设置用于工具栏的背景色： 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

编辑**Properties/AndroidManifest.xml**并添加以下`android:theme`属性设为`<application>`元素，以便该应用使用`MyTheme`自定义主题： 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

有关将自定义主题应用到应用程序的详细信息，请参阅[使用自定义主题](~/android/user-interface/material-theme.md#customtheme)。 


<a name="toolbar_layout" />

## <a name="define-a-toolbar-layout"></a>定义工具栏布局

在**资源/布局**目录中，创建新的文件称为**toolbar.xml**。 其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

此 XML 定义自定义`Toolbar`替换默认操作栏。 最小高度`Toolbar`设置为它将替换操作栏的大小： 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

背景色`Toolbar`设置为定义中较早的 olive-green 颜色**styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

从开始棒糖形，`android:theme`属性可用来设置单个视图的样式。 `ThemeOverlay.Material`棒糖形中引入的主题使得可覆盖默认值`Theme.Material`主题，覆盖相关的属性，以使它们亮或暗。 在此示例中，`Toolbar`使用深色主题，以便其内容采用浅色： 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

使用此设置，以便菜单项的更深的背景色与之相反。


<a name="include_layout" />

## <a name="include-the-toolbar-layout"></a>包括工具栏布局

编辑布局文件**Resources/layout/Main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <Button
        android:id="@+id/MyButton"
        android:layout_below="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World, Click Me!" />
</RelativeLayout>
```

此布局包括`Toolbar`中定义**toolbar.xml**并使用`RelativeLayout`指定`Toolbar`要放置在 （上面按钮） 的用户界面的最顶部。 


<a name="activate_toolbar" />

## <a name="find-and-activate-the-toolbar"></a>查找并激活工具栏

编辑**MainActivity.cs**并添加以下 using 语句：

```csharp
using Android.Views;
```

此外，将以下代码行添加到末尾`OnCreate`方法：

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此代码将查找`Toolbar`和调用`SetActionBar`以便`Toolbar`将不执行默认操作栏特征。 工具栏的标题更改为**我工具栏**。 此代码示例中所示`ToolBar`可以作为一个操作栏直接引用。 编译和运行此应用程序&ndash;的自定义`Toolbar`代替默认操作栏显示： 

[![绿色配色方案的自定义工具栏的屏幕截图](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png)

请注意，`Toolbar`独立于风格`Theme.Material.Light.DarkActionBar`应用于应用程序的其余部分的主题。 


<a name="main_menus" />
 
## <a name="add-menu-items"></a>添加菜单项 

在此部分中，菜单添加到`Toolbar`。 右上角区域`ToolBar`是保留的菜单项&ndash;每个菜单项 (也称为*操作项*) 可以执行的当前活动中的操作或它可以执行代表整个应用程序的操作。 

若要添加到菜单`Toolbar`: 

1.  （如果需要），菜单图标添加到`mipmap-`文件夹中的应用程序项目。 Google 提供一组可用菜单图标上[材料图标](https://design.google.com/icons/)页。 

2.  通过添加下的新菜单资源文件中定义的菜单项的内容**资源/菜单**。 

3.  实现`OnCreateOptionsMenu`活动的方法&ndash;此方法放大菜单项。 

4.  实现`OnOptionsItemSelected`活动的方法&ndash;点击的菜单项时，此方法将执行的操作。 

下列各节演示此过程的详细信息，通过添加**编辑**和**保存**的自定义菜单项`Toolbar`。 


<a name="menu_icons" />

### <a name="install-menu-icons"></a>安装菜单图标

继续`ToolbarFun`示例应用程序，将菜单图标添加到应用程序项目。 下载[工具栏 icons.zip](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons.zip?raw=true)并将其解压缩。 所提取的内容复制*mipmap-*到项目文件夹*mipmap-*下的文件夹**ToolbarFun/资源**和在项目中包含每个添加的图标文件。

<a name="menu_resource" />

### <a name="define-a-menu-resource"></a>定义菜单资源

创建一个新**菜单**下的子目录**资源**。 在**菜单**子目录，创建新的菜单资源文件调用**top_menus.xml**并将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

此 XML 将创建三个菜单项：

-   **编辑**使用的菜单项`ic_action_content_create.png`图标 （铅笔）。 

-   A**保存**使用的菜单项`ic_action_content_save.png`图标 （磁盘）。 

-   A**首选项**没有图标的菜单项。

`showAsAction`属性**编辑**和**保存**菜单项设置为`ifRoom`&ndash;此设置会导致这些菜单项显示在`Toolbar`是否存在才会显示这些的足够空间。 **首选项**菜单项集`showAsAction`到`never`&ndash;这将导致**首选项**菜单才会显示在*溢出*菜单 （三个垂直点）。 

<a name="on_create_options_menu" />

### <a name="implement-oncreateoptionsmenu"></a>实现 OnCreateOptionsMenu

添加以下方法**MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 调用`OnCreateOptionsMenu`方法，以便应用可指定活动的菜单资源。 在此方法， **top_menus.xml**资源被放大到传入`menu`。 此代码会导致新**编辑**，**保存**，和**首选项**菜单项显示在`Toolbar`。 


<a name="on_options_item_selected" />

### <a name="implement-onoptionsitemselected"></a>实现 OnOptionsItemSelected

添加以下方法**MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

当用户点击的菜单项时，Android 调用`OnOptionsItemSelected`方法并传入所选菜单项。 在此示例中，实现只是显示 toast 以指示点击的菜单项的。 

生成并运行`ToolbarFun`若要查看在工具栏中的新菜单项。 `Toolbar`现在显示三个菜单图标中此屏幕截图中所示： 

[![保存、 的关系图的编辑，演示位置和溢出菜单项](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png)

当用户点击**编辑**菜单项，toast 显示，则指示`OnOptionsItemSelected`调用了方法： 

[![显示时编辑项点击 Toast 的屏幕截图](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png)

当用户点击溢出菜单中，**首选项**显示菜单项。 通常情况下，不太常见操作应放置在溢出菜单&ndash;此示例使用的溢出菜单**首选项**因为不频繁使用它作为**编辑**和**保存**: 

[![出现在溢出菜单的屏幕截图的首选项菜单项](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png)

有关 Android 菜单的详细信息，请参阅 Android 开发人员[菜单](https://developer.android.com/guide/topics/ui/menus.html)主题。 
 



## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
