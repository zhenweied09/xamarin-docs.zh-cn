---
title: 卡片视图-
description: 卡片视图-小组件是类似于卡的视图中显示文本和图像的内容的 UI 组件。 本指南说明如何使用和自卡片视图-Xamarin.Android 应用程序中定义同时保持向后兼容早期版本的 Android。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 21e2a2e8ef04936664344cb4fb758bc2af3b4d05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771829"
---
# <a name="cardview"></a>卡片视图-

_卡片视图-小组件是类似于卡的视图中显示文本和图像的内容的 UI 组件。本指南说明如何使用和自卡片视图-Xamarin.Android 应用程序中定义同时保持向后兼容早期版本的 Android。_


## <a name="overview"></a>概述

`Cardview`小组件中，在 Android 5.0 （棒糖形） 中引入的是一个用户界面组件，类似于卡的视图中显示文本和图像的内容。 `CardView` 作为实现`FrameLayout`带有圆的角和阴影的小组件。 通常情况下，`CardView`来显示中的单个行项`ListView`或`GridView`查看组。 例如，以下屏幕截图是实现旅行预订应用程序示例`CardView`-基于在可滚动的差旅目标卡`ListView`:

![为每个旅行目标使用卡片视图-示例应用程序](card-view-images/01-cardview-example.png)

本指南介绍如何将添加`CardView`包到你的 Xamarin.Android 项目，如何添加`CardView`所需布局，以及如何自定义的外观`CardView`应用程序中。 此外，本指南提供的详细的列表`CardView`您可以更改，包括可帮助你使用的属性的属性`CardView`上的 Android 版本早于 Android 5.0 棒糖形。

<a name="requirements" />

## <a name="requirements"></a>要求

以下需要使用新的 Android 5.0 和更高版本的功能 (包括`CardView`) 基于 Xamarin 的应用中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果你是专门针对 API 级别 23 及更早版本。 JDK 1.8 位于[Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

你的应用程序还必须包括`Xamarin.Android.Support.v7.CardView`包。 若要添加`Xamarin.Android.Support.v7.CardView`适用于 Mac 的 Visual Studio 中的包：

1. 打开你的项目，右键单击**包**和选择**将程序包添加**。

2. 在**将程序包添加**对话框中，搜索**卡片视图-**。

3. 选择**Xamarin 支持库 v7 卡片视图-**，然后单击**添加包**。

若要添加`Xamarin.Android.Support.v7.CardView`Visual Studio 中的包：

1. 打开你的项目，右键单击**引用**节点 (在**解决方案资源管理器**窗格中)，然后选择**管理 NuGet 包...**.

2. 当**管理 NuGet 包**显示对话框中，输入**卡片视图-** 的搜索框中。

3. 当**Xamarin 支持库 v7 卡片视图-** 出现时，单击**安装**。

若要了解如何配置 Android 5.0 应用程序项目，请参阅[设置 Android 5.0 项目](~/android/platform/lollipop.md)。
有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


## <a name="introducing-cardview"></a>引入卡片视图-

默认值`CardView`类似于使用最小圆的角和一些阴影的白色卡。 下面的示例**main.axml**布局显示单个`CardView`包含的小组件`TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

如果你使用此 XML 替换的现有内容**main.axml**，请务必注释掉中的任何代码**MainActivity.cs** ，引用中以前的 XML 的资源。

此布局的示例创建一个默认`CardView`与单个行的文本，如下面的屏幕快照中所示：

[![屏幕快照的卡片视图-具有白色背景和文本行](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此示例中，应用样式设置为 light 材料主题 (`Theme.Material.Light`) 以便`CardView`阴影和边缘是更轻松地查看。 有关主题 Android 5.0 的应用的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。 在下一步的部分中，我们将了解如何自定义`CardView`应用程序。


## <a name="customizing-cardview"></a>自定义卡片视图-

你可以修改基本`CardView`特性以自定义的外观`CardView`应用程序中。 例如，提升`CardView`可以增加转换更大的卷影 （这样使得可以像是在 float 上面后台更高版本的卡）。 此外，可以增加圆角半径，以使多个卡舍入的角。

在下一步布局示例中，自定义`CardView`用于创建打印照片 （"快照"） 的模拟。 `ImageView`添加到`CardView`用于显示图像，和一个`TextView`下面定位`ImageView`用于显示图像的标题。
在此示例布局中，`CardView`具有以下自定义设置：

-  `cardElevation`增加到 4dp 转换更大的卷影。
-  `cardCornerRadius`增加到 5dp 以显示更圆角。

因为`CardView`提供由 Android v7 支持库，其属性不能通过`android:`命名空间。 因此，您必须定义你自己的 XML 命名空间，并使用该命名空间作为`CardView`属性前缀。 在下面的布局示例中，我们将使用此行来定义命名空间调用`cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

你可以调用此命名空间`card_view`甚至`myapp`如果你选择 （它是只能在此文件的作用域中访问）。 无论你选择要调用此命名空间，必须使用它来前缀`CardView`你想要修改的属性。 在此布局示例中，`cardview`命名空间是前缀`cardElevation`和`cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

当此布局示例用于在照片查看应用中，显示图像`CardView`具有照片快照的外观，如下面的屏幕截图中所示：

[![使用图像和标题下图中的卡片视图-](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此屏幕截图取自[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)示例应用，使用`RecyclerView`小组件显示的滚动列表`CardView`用于查看照片的映像。 有关详细信息`RecyclerView`，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

请注意，`CardView`可以在其内容区域中显示多个子视图。 例如，在上面的照片，查看应用程序示例，内容区域组成的`ListView`包含`ImageView`和`TextView`。 尽管`CardView`实例通常垂直排列，也可以水平排列它们 (请参阅[创建自定义视图样式](~/android/user-interface/material-theme.md#customview)示例屏幕快照)。


### <a name="cardview-layout-options"></a>卡片视图-布局选项

`CardView` 通过设置会影响其填充、 提升、 圆角半径和背景色的一个或多个特性，可以自定义布局：

[![关系图的卡片视图-属性](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

每个属性还可以更改动态通过调用对应的`CardView`方法 (有关详细信息`CardView`方法，请参阅[卡片视图-类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html))。
请注意 （除外背景色） 这些属性接受维度值，该值是跟单元的十进制数。 例如，`11.5dp`指定 11.5 密度无关的像素。


#### <a name="padding"></a>填充
`
CardView` 提供了五个填充特性来定位于卡片的内容。 你的布局 XML 中设置这些或可以在代码中调用类似方法：

[![关系图的卡片视图-填充属性](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填充属性，如下所示进行了说明：

-  `contentPadding` &ndash; 填充的子视图之间的内部`CardView`和的卡的所有边缘。

-  `contentPaddingBottom` &ndash; 填充的子视图之间的内部`CardView`和智能卡的底部边缘。

-  `contentPaddingLeft` &ndash; 填充的子视图之间的内部`CardView`左边缘的卡。

-  `contentPaddingRight` &ndash; 填充的子视图之间的内部`CardView`的卡的右边缘。

-  `contentPaddingTop` &ndash; 填充的子视图之间的内部`CardView`卡的上边缘。

内容填充属性是相对于边界的内容区域中，而不是位于内容区域内任何给定小组件。
例如，如果`contentPadding`在照片查看应用中，充分增加了`CardView`将裁剪图像和在卡上显示的文本。



#### <a name="elevation"></a>提升

`CardView` 提供两个提升属性来控制其提升，因此，其阴影的大小：

[![卡片视图-提升属性的关系图](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提升属性，如下所示进行了说明：

-  `cardElevation` &ndash; 提升`CardView`（表示其 Z 轴）。

-  `cardMaxElevation` &ndash; 最大值`CardView`的提升。

较大的值的`cardElevation`增加卷影大小，以使`CardView`像是在 float 上面后台更高版本。 `cardElevation`属性也确定重叠视图的绘制顺序; 也就是，则`CardView`将另一个重叠的视图设置较高提升和更高版本设置较低提升任何重叠视图下绘制。
`cardMaxElevation`设置对于你的应用程序动态更改提升时很有用&ndash;它会阻止卷影扩展过去使用此设置定义的限制。


#### <a name="corner-radius-and-background-color"></a>圆角半径和背景色

`CardView` 提供可用于控制其圆角半径和它的背景色的属性。 这两个属性可以用来更改的整体样式`CardView`:

[![周围的卡片视图-角和背景颜色属性的关系图](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

这些属性进行了说明，如下所示：

-  `cardCornerRadius` &ndash; 所有角的圆角半径`CardView`。

-  `cardBackgroundColor` &ndash; 背景色`CardView`。

在此图中，`cardCornerRadius`设置为更舍入 10dp 和`cardBackgroundColor`设置为`"#FFFFCC"`（浅黄色）。


## <a name="compatibility"></a>兼容性

你可以使用`CardView`上的 Android 版本早于 Android 5.0 棒糖形。 因为`CardView`摘自 Android v7 支持库中，您可以使用`CardView`带有 Android 2.1 （API 级别 7） 和更高版本。
但是，你必须安装`Xamarin.Android.Support.v7.CardView`包中所述[要求](#requirements)上面。

`CardView` 出现在之前棒糖形 （API 级别 21） 的设备上略有不同的行为：

-  `CardView` 使用添加其他填充的编程的卷影实现。

-  `CardView` 不会剪切与相交的权限的子视图`CardView`的圆角。

若要帮助管理这些兼容性的差异，`CardView`提供可以在你的布局中配置的多个其他属性：

-   `cardPreventCornerOverlap` &ndash; 将此属性设置为`true`在较早的 Android 版本 （API 级别 20 及更早版本） 上运行你的应用程序时添加填充。 此设置可阻止`CardView`内容从与交叉`CardView`的圆角。

-   `cardUseCompatPadding` &ndash; 将此属性设置为`true`时的在 Android 或大于 API 级别 21 版本中运行你的应用程序添加填充。 如果你想要使用`CardView`预棒糖形设备上，并将其看起来相同棒糖形上 （或更高版本），将此属性设置为`true`。 启用此属性后，`CardView`添加其他填充绘制阴影，在该预棒糖形设备上运行。 这可以帮助克服预棒糖形以编程方式隐藏实现中都起作用时，会引入填充方面的差异。

有关维护与早期版本的 Android 的兼容性的详细信息，请参阅[维护兼容性](https://developer.android.com/training/material/compatibility.html)。


## <a name="summary"></a>总结

本指南引入新`CardView`Android 5.0 （棒糖形） 中包含的小组件。 它演示了默认值`CardView`外观并且说明了如何自定义`CardView`通过更改其提升，角圆度，内容填充和背景色。 它列出`CardView`布局属性 （与引用关系图），并且说明了如何使用`CardView`早于 Android 5.0 棒糖形 Android 设备上。 有关详细信息`CardView`，请参阅[卡片视图-类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。


## <a name="related-links"></a>相关链接

- [RecyclerView （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [卡片视图-类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
