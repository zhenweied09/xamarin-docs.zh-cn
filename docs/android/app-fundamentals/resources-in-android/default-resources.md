---
title: "默认资源"
ms.topic: article
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3d9c747cdf8e43f33b9310ac1156550066b400eb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="default-resources"></a>默认资源

默认资源是项，并不特定于任何特定设备或窗体因素，并因此就是默认的选择由 Android OS 如果没有更具体找不到资源。 在这种情况下，它们是资源的要创建最常见类型。 它们组织到子目录的**资源**目录根据它们的资源类型：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![默认资源文件](default-resources-images/01-resource-files-vs.png)

在上图中，该项目包含可绘制资源、 布局和值 （包含简单值的 XML 文件） 的默认值。

下面提供的资源类型的完整列表：

-  **生成器**&ndash;描述属性动画的 XML 文件。
   属性动画 API 级别 11 (Android 3.0) 中引入，并提供了对某个对象的属性的动画效果。 属性动画是对象的一种在来描述在任何类型上的动画的更灵活、 更强大的方法。

-  **anim** &ndash;描述 XML 文件*之间*动画。 之间动画是文本的一系列的动画说明映像或增长大小的视图对象或示例中，旋转的内容进行转换。 之间动画被限制为仅查看的对象。

-  **颜色**&ndash;描述颜色的状态列表的 XML 文件。 若要了解颜色状态列表，请考虑如按钮 UI 小组件。
   这可能会增加具有不同的状态，如按下还是禁用状态，并按钮可能处于状态每次更改颜色。 列表用表示状态列表。

-  **可绘制**&ndash;可绘制资源是用于图形可编译到应用程序，然后访问 API 调用或引用的其他 XML 资源的常规概念。
   Drawables 的一些示例是位图文件 （.png、.gif、.jpg）、 特殊可调整大小的位图称为[包含 9 个修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，列出了状态，在 XML 等中定义的泛型形状。
 
-  **布局**&ndash;描述用户界面布局，如活动或列表中的行的 XML 文件。

-  **菜单** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 有关菜单的示例，请参阅[弹出菜单演示](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[标准控件](https://developer.xamarin.com/samples/mobile/StandardControls/)示例。

-  **原始**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件编译为二进制格式中的 Android 应用程序。

-  **值**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不会定义单个资源，而改为可以定义多个资源。 例如一个 XML 文件可以容纳字符串值的列表，而另一个 XML 文件可以容纳颜色值的列表。

-  **xml** &ndash;在函数类似于.NET 配置文件的 XML 文件。 这些都是由应用程序可以在运行时中读取的任意 XML


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![默认资源文件](default-resources-images/01-resource-files-xs.png)

在上图中，该项目包含可绘制资源、 布局和值 （包含简单值的 XML 文件） 的默认值。

下面提供的资源类型的完整列表：

-  **生成器**&ndash;描述属性动画的 XML 文件。
   属性动画 API 级别 11 (Android 3.0) 中引入，并提供了对某个对象的属性的动画效果。 属性动画是对象的一种在来描述在任何类型上的动画的更灵活、 更强大的方法。

-  **anim** &ndash;描述 XML 文件*之间*动画。 之间动画是文本的一系列的动画说明映像或增长大小的视图对象或示例中，旋转的内容进行转换。 之间动画被限制为仅查看的对象。

-  **颜色**&ndash;描述颜色的状态列表的 XML 文件。 若要了解颜色状态列表，请考虑如按钮 UI 小组件。
   这可能会增加具有不同的状态，如按下还是禁用状态，并按钮可能处于状态每次更改颜色。 列表用表示状态列表。

-  **字体**&ndash;从 API 级别 26 年开始，可在 Android 应用程序中作为资源嵌入字体。 支持库 26 将向后移植到 API 级别 14 的字体。 嵌入字体允许应用程序

-  **mipmap** &ndash;可绘制资源是用于图形可编译到应用程序，然后访问 API 调用或引用的其他 XML 资源的常规概念。
   Drawables 的一些示例是位图文件 （.png、.gif、.jpg）、 特殊可调整大小的位图称为[包含 9 个修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，列出了状态，在 XML 等中定义的泛型形状。

-  **布局**&ndash;描述用户界面布局，如活动或列表中的行的 XML 文件。

-  **菜单** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 有关菜单的示例，请参阅[弹出菜单演示](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[标准控件](https://developer.xamarin.com/samples/mobile/StandardControls/)示例。

-  **原始**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件编译为二进制格式中的 Android 应用程序。

-  **值**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不会定义单个资源，而改为可以定义多个资源。 例如一个 XML 文件可以容纳字符串值的列表，而另一个 XML 文件可以容纳颜色值的列表。

-  **xml** &ndash;在函数类似于.NET 配置文件的 XML 文件。 这些都是由应用程序可以在运行时中读取的任意 XML

-----
