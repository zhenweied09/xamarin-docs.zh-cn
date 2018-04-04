---
title: 工具栏兼容性
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: c3418a007ded30175049e83d515f59d5134deee0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="toolbar-compatibility"></a>工具栏兼容性


## <a name="overview"></a>概述

本部分说明如何使用`Toolbar`上的 Android 版本早于 Android 5.0 棒糖形。 如果你的应用程序不支持早于 Android 5.0 的 Android 版本，则可以跳过本节。 

因为`Toolbar`是一部分的 Android v7 支持库，它可在运行 Android 2.1 （API 级别 7） 设备上和更高版本。 但是， [Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)必须安装 NuGet 和代码修改，使其使用`Toolbar`此库中提供的实现。 本部分介绍如何安装此 NuGet 和修改**ToolbarFun**应用从[添加第二个工具栏](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md)以便它早于棒糖形 5.0 版本的 Android 上运行。

若要修改的应用程序以使用工具栏的 AppCompat 版本： 

1.  设置应用程序的最小值和目标 Android 版本。

2.  安装 AppCompat NuGet 包。

3.  而不是内置的 Android 主题使用 AppCompat 主题。

4.  修改`MainActivity`，以便它子类`AppCompatActivity`而非`Activity`。 

下列部分中的详细阐述每个步骤。



## <a name="set-the-minimum-and-target-android-version"></a>设置最小值和目标 Android 版本

必须设置应用程序的目标框架，为 API 级别 21 或更高版本或应用程序将不会正确部署。 如果出现错误，如**属性 tileModeX 包 android 中的任何资源标识符找到**出现时将应用部署，这是因为未将目标框架设置为**Android 5.0 (API 级别 21-棒糖形)**或更高版本。 

目标框架设置为 API 级别 21 级别或更高版本，将最低 Android 版本，此应用程序以支持 Android API 级别的项目设置。 有关设置 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。 在`ToolbarFun`示例中，最低 Android 版本设置为 KitKat (API 级别 4.4)。 


## <a name="install-the-appcompat-nuget-package"></a>安装 AppCompat NuGet 包

接下来，添加[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)到项目的包。 在 Visual Studio 中，右键单击**引用**和选择**管理 NuGet 包...**.单击**浏览**并搜索**Android 支持库 v7 AppCompat**。 选择**Xamarin.Android.Support.v7.AppCompat**单击**安装**: 

[![在管理 NuGet 包中选择的屏幕截图的 V7 Appcompat 包](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

安装此 NuGet 时，多个其他 NuGet 包还会安装如果尚不存在 (如**Xamarin.Android.Support.Animated.Vector.Drawable**， **Xamarin.Android.Support.v4**，和**Xamarin.Android.Support.Vector.Drawable**)。 有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 


## <a name="use-an-appcompat-theme-and-toolbar"></a>使用 AppCompat 主题和工具栏

AppCompat 库附带了几个`Theme.AppCompat`可在任何版本的 Android 受 AppCompat 库的主题。 `ToolbarFun`示例应用程序主题派生自`Theme.Material.Light.DarkActionBar`，不可用早 Android 版本比棒糖形。 因此，`ToolbarFun`必须进行适配化以便为此主题中，使用对应的 AppCompat `Theme.AppCompat.Light.DarkActionBar`。 此外，因为`Toolbar`是在版本的 Android 上不可用早于棒糖形，我们必须使用 AppCompat 版本`Toolbar`。 因此，必须使用布局`android.support.v7.widget.Toolbar`而不是`Toolbar`。 


### <a name="update-layouts"></a>更新布局

编辑**Resources/layout/Main.axml**和替换`Toolbar`使用以下 XML 元素： 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

编辑**Resources/layout/toolbar.xml**并将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

请注意，`?attr`值不再带有前缀`android:`(回想一下，`?`表示法引用当前主题中的资源)。 如果`?android:attr`仍使用 Android 此处，从当前正在运行的平台中，而不是从 AppCompat 库引用属性值。 由于本示例使用`actionBarSize`AppCompat 库中，定义`android:`删除前缀。 同样，`@android:style`更改为`@style`以便`android:theme`属性已设置为一个主题中的 AppCompat 库&ndash;`ThemeOverlay.AppCompat.Dark.ActionBar`此处使用主题而非`ThemeOverlay.Material.Dark.ActionBar`。 


### <a name="update-the-style"></a>更新样式

编辑**Resources/values/styles.xml**并将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

项名称和在此示例中的父主题不再带有前缀`android:`因为我们正在使用 AppCompat 库。 此外，父主题更改为的 AppCompat 版本`Light.DarkActionBar`。 



### <a name="update-menus"></a>更新菜单

若要支持早期版本的 Android，AppCompat 库使用镜像的属性的自定义特性`android:`命名空间。 但是，某些属性 (如`showAsAction`属性中使用`<menu>`标记) 中的 Android framework 在较旧的设备上不存在&ndash; `showAsAction` Android API 11 中引入了，但在 Android API 7 中不可用。 为此，必须使用自定义的命名空间前缀的所有属性定义的支持库。 在菜单资源文件，一个命名空间中调用`local`为前缀定义`showAsAction`属性。 

编辑**Resources/menu/top_menus.xml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

`local`命名空间添加行：

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

`showAsAction`属性开头为这`local:`命名空间而非 `android:` 

```csharp
local:showAsAction="ifRoom"
```

同样，编辑**Resources/menu/edit_menus.xml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此命名空间交换机如何提供对支持`showAsAction`Android API 级别 11 之前的版本上的属性？ 自定义特性`showAsAction`和所有及其可能的值包含在应用程序已安装 AppCompat NuGet。 


## <a name="subclass-appcompatactivity"></a>子类 AppCompatActivity

在转换中的最后一步是修改`MainActivity`以便它的一个子类`AppCompactActivity`。 编辑**MainActivity.cs**并添加以下`using`语句： 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

这会将声明`Toolbar`要 AppCompat 新版`Toolbar`。 接下来，更改的类定义`MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

若要设置为 AppCompat 版本操作栏`Toolbar`，替换调用`SetActionBar`与`SetSupportActionBar`。 在此示例中，标题也会更改，则指示的 AppCompat 版本`Toolbar`正在使用：

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

最后，将最低 Android 级别更改为要支持 （例如，API 19） 的预棒糖形值。 

构建该应用并在预棒糖形设备或 Android 仿真程序上运行它。 以下屏幕截图显示的 AppCompat 版本**ToolbarFun** Nexus 4 个正在运行 KitKat (API 19) 上： 

[![KitKat 设备上运行的应用的完整屏幕截图，显示这两个工具栏](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

当使用 AppCompat 库时，主题也不必切换基于 Android 版本&ndash;AppCompat 库使它能够提供跨所有支持的 Android 版本的一致的用户体验。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
