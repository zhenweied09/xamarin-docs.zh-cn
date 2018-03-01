---
title: "材料主题"
description: "主题如何使用材料主题对 Xamarin.Android 应用程序"
ms.topic: article
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: ba73e03d6bdeea64918e0232b2188bf8e3b65084
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="material-theme"></a>材料主题

<a name="overview" />

## <a name="overview"></a>概述

*材料主题*是一种用户界面样式，它确定视图和活动开头 Android 5.0 （棒糖形） 的外观和感觉。 材料主题是内置到 Android 5.0，因此它使用由系统 UI 以及应用程序。 材料主题不在用户可以动态选择从设置菜单的系统级外观选项的意义上是"主题"。 相反，可以将材料主题看作一组的相关可用于自定义你的应用程序的外观和感觉的内置基样式。

Android 提供三个材料主题风格：

-  `Theme.Material` &ndash; 深色版本的材料主题;这是 Android 5.0 中的默认风格。

-  `Theme.Material.Light` &ndash; 材料主题的轻量版本。

-  `Theme.Material.Light.DarkActionBar` &ndash; 轻量版本的材料主题，但与深色操作栏。

此处显示的这些材料主题风格示例：

[![深色主题、 浅色主题和深色操作栏主题的示例屏幕快照](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png)

你可以从派生材料主题创建您自己的主题，重写某些或所有颜色属性。 例如，你可以创建派生自主题`Theme.Material.Light`，但优先于应用程序栏颜色，以便与您的品牌颜色。 你还可以设置单个视图; 的样式例如，你可以创建的样式[卡片视图-](~/android/user-interface/controls/card-view.md) ，更具有圆角并使用更深的背景色。

可将单个主题用于在整个应用程序，或可以为不同的屏幕 （活动） 在应用程序使用不同的主题。 在上面的屏幕截图，例如的单个应用程序使用的每个活动不同的主题演示了内置的配色方案。 单选按钮切换到不同的活动的应用程序，并因此，显示不同的主题。

因为仅在 Android 5.0 和更高版本支持材料主题，则不能使用它 （或派生自材料主题的自定义主题） 主题到你的应用程序在早期版本的 Android 上运行。 但是，你可以配置你的应用以在 Android 5.0 设备上使用材料主题和正常故障回复到更早版本的主题在较旧版本的 Android 上运行时 (请参阅[兼容性](#compatibility)本文了解详细信息的部分)。

<a name="requirements" />

## <a name="requirements"></a>惠?

以下是所需基于 Xamarin 的应用中使用新的 Android 5.0 材料主题功能：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。 

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果你是专门针对 API 级别 23 及更早版本。 JDK 1.8 位于[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

若要了解如何配置 Android 5.0 应用程序项目，请参阅[设置 Android 5.0 项目](~/android/platform/lollipop.md)。

<a name="builtinthemes" />

## <a name="using-the-built-in-themes"></a>使用内置的主题

使用材料主题的最简单方法是配置你的应用能使用内置主题没有自定义项。 如果你不想要显式配置了一个主题，你的应用程序将默认为`Theme.Material`（深色主题）。 如果你的应用程序只有一个活动，你可以在应用程序级配置主题。 如果你的应用程序具有多个活动，你可以在应用程序级配置一个主题，以便它跨所有活动，使用相同的主题，或可以将不同的主题分配给不同的活动。 以下各节说明如何在应用程序级别和活动级别配置主题。

<a name="themeanapp" />

### <a name="theming-an-application"></a>主题应用程序

若要配置使用材料主题风格的整个应用程序，将设置`android:theme`属性中的应用程序节点**AndroidManifest.xml**到以下项之一：

-  `@android:style/Theme.Material` &ndash; 深色主题。

-  `@android:style/Theme.Material.Light` &ndash; 浅色主题。

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; 与深色操作栏的浅色主题。

下面的示例将配置应用程序*MyApp*用于采用浅色主题：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，可以将应用程序设置`Theme`属性中**AssemblyInfo.cs** (或**Properties.cs**)。 例如:

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

当应用程序主题设置为`@android:style/Theme.Material.Light`，在每个活动*MyApp*将显示使用`Theme.Material.Light`。

<a name="activitytheme" />

### <a name="theming-an-activity"></a>主题活动

您将添加到将活动的主题，`Theme`将设置为`[Activity]`活动声明的上方特性并将分配`Theme`到你想要使用的材料主题风格。 将活动与在以下示例主题`Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此应用程序中的其他活动会使用默认的`Theme.Material`深色配色方案 （或，如果配置，应用程序主题设置）。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自定义主题

你可以通过创建的自定义主题样式应用程序与你的品牌增强你的品牌&rsquo;的颜色。 若要创建的自定义主题，你定义一个派生自内置的材料主题风格的新样式重写你想要更改的颜色属性。 例如，你可以定义派生自的自定义主题`Theme.Material.Light.DarkActionBar`和屏幕背景色更改为米黄色而不是空白。

材料主题公开自定义的以下布局属性：

-  `colorPrimary` &ndash; 应用栏的颜色。

-  `colorPrimaryDark` &ndash; 中的状态栏和上下文的应用程序栏的颜色这通常是深色版本`colorPrimary`。

-  `colorAccent` &ndash; 如复选框、 单选按钮和编辑文本框中的 UI 控件的颜色。

-  `windowBackground` &ndash; 屏幕背景的颜色。

-  `textColorPrimary` &ndash; 在应用栏中的 UI 文本的颜色。

-  `statusBarColor` &ndash; 状态栏的颜色。

-  `navigationBarColor` &ndash; 导航栏的颜色。

这些屏幕区域分别标记为在下图中：

[ ![属性和其相关联的屏幕区域示意图](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png)

默认情况下，`statusBarColor`设置的值为`colorPrimaryDark`。 你可以设置`statusBarColor`为纯色，或将其设置为`@android:color/transparent`若要使状态栏透明。 导航栏可以还通过透明设置`navigationBarColor`到`@android:color/transparent`。

<a name="customapptheme" />

### <a name="creating-a-custom-app-theme"></a>创建自定义应用程序主题

你可以通过创建和修改文件中的创建自定义应用程序主题**资源**应用程序项目的文件夹。 若要设置样式使用的自定义主题对应用程序，使用以下步骤：

-   创建**colors.xml**文件中**资源/值**&mdash;此文件用于定义自定义主题颜色。 例如，你可以粘贴下面的代码插入**colors.xml**来帮助你开始：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   修改此示例文件，以定义的名称和颜色资源将在您的自定义主题中使用的颜色代码。

-   创建**资源/值-v21**文件夹。 在此文件夹中，创建**styles.xml**文件：

    [ ![Styles.xml 资源/值-21.xml 文件夹中的位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png)

    请注意，**资源/值-v21**特定于 Android 5.0&ndash;较旧版本的 Android 不将读取此文件夹中的文件。

-   添加`resources`节点**styles.xml**并定义`style`替换你自定义主题的名称的节点。 例如，下面是**styles.xml**定义文件*MyCustomTheme* (派生自内置`Theme.Material.Light`主题样式):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   此时，应用程序使用*MyCustomTheme*将显示股票`Theme.Material.Light`未进行自定义的主题：

    [ ![自定义主题外观后再自定义项](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png)

-   添加到的颜色自定义**styles.xml**定义你想要更改的布局特性的颜色。 例如，若要更改的应用程序栏颜色设为`my_blue`，并更改到 UI 控件的颜色`my_purple`，添加到颜色替代**styles.xml** ，请参阅配置中的颜色资源**colors.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

与这些更改后，应用程序，它使用*MyCustomTheme*将显示在应用栏颜色`my_blue`和中的 UI 控件`my_purple`，但使用`Theme.Material.Light`无处不在其他颜色方案：

[ ![在自定义后的自定义主题外观](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png)

在此示例中， *MyCustomTheme*借用中的颜色`Theme.Material.Light`的背景颜色，状态栏，和文本颜色，但它更改到应用栏的颜色`my_blue`和设置单选按钮的颜色`my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>创建自定义视图样式

Android 5.0 还使你可以设置单个视图的样式。 在创建后**colors.xml**和**styles.xml** （如上文所述），你可以添加到视图样式**styles.xml**。
若要设置单个视图的样式，使用以下步骤：

-   编辑**Resources/values-v21/styles.xml**并添加`style`替换你自定义视图样式的名称的节点。 设置在此视图的自定义颜色属性`style`节点。 例如，若要创建自定义[卡片视图-](~/android/user-interface/controls/card-view.md)更具有圆角和使用的样式`my_blue`作为卡背景色，添加`style`节点**styles.xml** (内`resources`节点) 和配置的背景颜色和角半径：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   在布局中，设置`style`该视图，以匹配您在上一步中选择的自定义样式名称的属性。 例如:

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下面的屏幕快照提供了默认值的示例`CardView`（所示在左侧） 如相比`CardView`，已使用的自定义样式`CardView.MyBlue`主题 （在右侧显示）：

[ ![默认卡片视图-和自定义卡片视图-的示例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png)

在此示例中，自定义`CardView`会显示包含的背景色的`my_blue`和 18dp 圆角半径。

<a name="compatibility" />

## <a name="compatibility"></a>兼容性

若要设置你的应用样式，使它在 Android 5.0 上使用材料主题，但会自动恢复到较旧的 Android 版本上的向下兼容类型，使用以下步骤：

-   定义中的自定义主题**Resources/values-v21/styles.xml**派生的材料主题样式。 例如:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   定义中的自定义主题**Resources/values/styles.xml** ，派生自一个较旧的主题，但按上面所述使用相同的主题名称。 例如:

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   在**AndroidManifest.xml**，请使用自定义主题名称配置你的应用程序。 
    例如:

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   或者，你可以设置特定的活动使用您的自定义主题样式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的主题使用颜色中定义**colors.xml**文件，请务必将此文件中的放置**资源/值**(而非**资源/值-v21**)，以便这两个版本颜色定义可以访问您的自定义主题。

当在 Android 5.0 设备上运行你的应用程序时，它将使用中指定的主题定义**Resources/values-v21/styles.xml**。 当在早期的 Android 设备上运行此应用程序时，它将自动回退到中指定的主题定义**Resources/values/styles.xml**。

有关主题较旧的 Android 版本的兼容性的详细信息，请参阅[备用资源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>摘要

这篇文章引入了 Android 5.0 （棒糖形） 中包含新材料主题用户界面样式。 它所述的三个内置材料主题功能可用于设置应用样式、 它解释如何创建自定义主题于品牌你的应用，和它提供到主题的一个示例是单个的视图。 最后，本文还介绍了如何在应用中使用材料主题，同时保持与旧版本的 Android 的向下兼容性。



## <a name="related-links"></a>相关链接

- [ThemeSwitcher （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [棒糖形简介](~/android/platform/lollipop.md)
- [CardView](~/android/user-interface/controls/card-view.md)
- [备用资源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)
- [Android L 开发者预览版](http://developer.android.com/preview/index.html)
- [材料设计](http://developer.android.com/preview/material/index.html)
- [材料设计原则](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
- [维护兼容性](http://developer.android.com/preview/material/compatibility.html)
