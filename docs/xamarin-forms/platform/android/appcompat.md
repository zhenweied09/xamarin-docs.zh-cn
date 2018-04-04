---
title: 添加 AppCompat 和材料设计
description: 请按照下列步骤要转换现有的 Xamarin.Forms Android 应用程序以使用 AppCompat 和材料设计
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: 8f9820b863274453cff7e4124df683fb8518a978
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="adding-appcompat-and-material-design"></a>添加 AppCompat 和材料设计

_请按照下列步骤要转换现有的 Xamarin.Forms Android 应用程序以使用 AppCompat 和材料设计_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>概述

这些说明解释了如何更新现有 Xamarin.Forms Android 应用程序使用 AppCompat 库，并在 Xamarin.Forms 应用的 Android 版本中启用材料设计。

### <a name="1-update-xamarinforms"></a>1.Update Xamarin.Forms

确保解决方案使用 Xamarin.Forms 2.0 或更高版本。 如果需要，请更新的 Xamarin.Forms Nuget 包为 2.0。

### <a name="2-check-android-version"></a>2.检查 Android 版本

确保 Android 项目的目标框架是 Android 6.0 (Marshmallow)。 检查**Android 项目 > 选项 > 生成 > 常规**选择设置，以确保正确 framework:

 ![](appcompat-images/target-android-6-sml.png "Android 常规生成配置")

### <a name="3-add-new-themes-to-support-material-design"></a>3.添加新的主题，以支持材料设计

在 Android 项目中创建以下三个文件并粘贴以下内容中。 Google 提供[样式指南](http://www.google.com/design/spec/style/color.html#color-color-palette)和[颜色调色板生成器](http://www.materialpalette.com/)以帮助您选择为指定备用的配色方案。

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

其他样式必须包括在**值 v21**文件夹运行 Android 棒糖形上和更高版本时将特定属性。

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4.Update AndroidManifest.xml

若要确保此新主题信息是在中的，使用，将主题**AndroidManifest**文件添加`android:theme="@style/MyTheme"`（按照原样，则使 XML 的其余部分）。

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5.提供工具栏和选项卡的布局

创建**Tabbar.axml**和**Toolbar.axml**文件中**资源/布局**目录和从下其内容中的粘贴：

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

已设置为选项卡的几个属性包括到选项卡的重力`fill`和模式来`fixed`。
如果你有大量的选项卡可能想要切换这到可滚动的通读 Android [TabLayout 文档](http://developer.android.com/reference/android/support/design/widget/TabLayout.html)若要了解详细信息。

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

在这些文件中，我们正在创建你的应用程序都可能不同的工具栏的特定主题。
请参阅[Hello 工具栏](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/)博客文章以了解详细信息。


### <a name="6-update-the-mainactivity"></a>6.更新 `MainActivity`

在现有 Xamarin.Forms 应用中**MainActivity.cs**类将从继承`FormsApplicationActivity`。 这必须替换`FormsAppCompatActivity`以启用新功能。

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

最后，"连接"中的步骤 5 中的新布局`OnCreate`方法，如下所示：

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
