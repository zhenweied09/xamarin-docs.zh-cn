---
title: 材料设计功能
description: 本主题介绍便于开发人员创建材料设计符合布局的设计器功能。 本部分介绍，并说明如何使用材料网格、 材料颜色调色板、 排字刻度和主题编辑器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: a764efe7f2cadd8c777f8427c0220e45eec662c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="material-design-features"></a>材料设计功能

_本主题介绍便于开发人员创建材料设计符合布局的设计器功能。本部分介绍，并说明如何使用材料网格、 材料颜色调色板、 排字刻度和主题编辑器。_


> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**发展 2016年： 每个人都可以创建美观应用与材料设计**

## <a name="overview"></a>概述

Xamarin.Android 设计器包括功能，使你更轻松地创建材料设计符合布局。 如果你不熟悉材料设计，请参阅[材料设计简介](https://www.google.com/design/spec/material-design/introduction.html)。

本指南中，我们将看一下以下的设计器功能：

-   *材料网格*&ndash;网格、 间距和线条来帮助你将按照材料设计准则的布局小组件显示在设计图面上一个覆盖区。

-   *材料调色板*&ndash;属性填充对话框，可帮助你从官方材料设计调色板中选择一种颜色。

-   *排字缩放*&ndash;你提供的材料设计-兼容设置选择的属性填充对话框`textAppearance`的文本字段的属性。

-   *主题编辑器*&ndash;小颜色资源编辑器，可通过该设置是主题子集的颜色信息。 例如，你可以预览并修改材料的颜色，如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我们已介绍这些功能的每一种，提供了如何使用它们的示例。



## <a name="material-design-grid"></a>材料设计网格

从设计器顶部的工具栏提供了材料设计网格菜单：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![材料设计网格](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![材料设计网格](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

-----

当你单击材料设计网格图标时，设计器包括以下元素在设计图面上显示一个覆盖区：

-   线条 （橙色行）

-   间距 （绿色区域）

-   网格 （蓝色行）

可以在下面的屏幕截图中看到这些元素：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![准线、 间距和网格](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![准线、 间距和网格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

其中每一项覆盖区上进行配置。 当你单击材料设计网格菜单旁边的省略号时，对话框 popover 将打开，您可以禁用/启用网格、 配置的线条，位置和设置间距。 请注意，所有值都表示在`dp`（独立于密度的像素为单位）：

![网格、 准线和间距配置](material-design-features-images/vs/03-grid-configuration.png)

若要添加新的准线，请输入较新的偏移的值在**偏移量**框中，选择一个位置 (**左**，**顶部**，**右**，或**底部**)，然后单击 + 图标以添加新准线。

同样，若要添加新的间距，输入的大小和偏移量 （以 dp) 到**大小**和**偏移量**框中，分别。 选择一个位置 (**左**，**顶部**，**右**，或**底部**)，然后单击 + 图标以添加新的间距。

更改这些配置值时，它们是保存在布局 XML 文件中，当你再次打开布局时重复使用。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

其中每一项覆盖区上进行配置。 当你单击材料设计网格菜单旁边的向下三角形时，对话框 popover 将打开，您可以禁用/启用网格、 配置的线条，位置和设置间距。 请注意，所有值都表示在`dp`（独立于密度的像素为单位）：

[![网格、 准线和间距配置](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要添加新的准线，请输入较新的偏移的值在**偏移量**框中，选择一个位置 (**左**，**顶部**，**右**，或**底部**)，然后单击 + 图标以添加新准线。

同样，若要添加新的间距，输入的大小和偏移量 （以 dp) 到**大小**和**偏移量**框中，分别。 选择一个位置 (**左**，**顶部**，**右**，或**底部**)，然后单击 + 图标以添加新的间距。

更改这些配置值时，它们是保存在布局 XML 文件中，当你再次打开布局时重复使用。


## <a name="material-design-color-palette"></a>材料设计调色板

现在接受一种颜色的每个属性面板项具有一个是附加图标，你可用于打开材料设计调色板，此屏幕截图中所示：

[![颜色图标](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

单击此图标时，将打开对话框 popover，它使您可以配置该属性从材料设计颜色调色板的颜色：

[![材料设计调色板](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

调色板顶部显示主材料设计颜色时调色板底部显示有关所选的主要颜色色调的范围。 例如，当选择**Indigo**，一套**Indigo**色调显示在对话框底部。
当你选择色调时，属性颜色更改为所选的色调。 在下面的示例中，`Background Tint`的按钮将变为*Indigo 500*:

[![选择 Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` 设置为的颜色代码*Indigo 500* (`#ff3f51b5`)，并设计器更新以反映此更改按钮的背景色：

[![后台浅色更改](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

有关材料设计颜色调色板的详细信息，请参阅材料设计[颜色调色板指南](http://www.google.com/design/spec/style/color.html#color-color-palette)。

## <a name="typographic-scale"></a>排字缩放

**文本外观**部分**属性**pad**样式**选项卡都有一个图标，你可以选择从`TextAppearance`符合材料设计的样式规范：

[![样式选项卡](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

当单击此图标时，它打开**排字缩放**对话框 popover，它显示了你可以选择从的预配置的文本样式的列表：

[![文本样式选择器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在以下示例中，单击**显示 1**更改为更大的字体的按钮的文本**显示 1**:

[![显示 1 样式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

中的文本样式**排字缩放**对话框遵循**主题**设置。 例如，如果**Light**在设计器中，可用的 text 样式镜像的列表中选择主题**Light**主题：

[![浅色主题](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

-----



## <a name="theme-editor"></a>主题编辑器

**主题编辑器**允许你自定义的主题属性子集的颜色信息。 若要打开**主题编辑器**，单击工具栏上的画笔图标：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![主题编辑器图标](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![主题编辑器图标](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

-----

尽管**主题编辑器**仅可从工具栏访问所有目标 Android 版本和 API 级别，如下所述的功能的子集有如果目标 API 级别为早于 API 21 (Android 5.0棒糖形）。

左侧面板**主题编辑器**显示构成当前所选主题颜色的列表 (在此示例中，我们将使用`Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![主题编辑器](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![主题编辑器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

-----

当你选择颜色位于左侧时，右侧面板将提供以下选项卡中，以帮助你编辑该颜色：

-   **继承**&ndash;显示所选颜色样式继承关系图，并列出解析的颜色和颜色代码分配给该主题颜色。

-   **颜色选取器**&ndash;可让你更改所选为任何任意值的颜色。

-   **材料调色板**&ndash;对话框允许您更改所选为符合材料设计的值的颜色。

-   **资源**&ndash;允许你将更改为所选颜色的其他现有颜色主题中的资源。

让我们看一下这些选项卡。 详细信息中的每一个。



### <a name="inherit-tab"></a>继承选项卡

下面的示例中所示**继承**选项卡上列出有关样式继承**后台**颜色**默认主题**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![继承选项卡](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![继承选项卡](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

-----

在此示例中，**默认主题**继承自一种使用样式`@color/background_material_dark`但将重写它与`color/material_grey_850`，它具有的颜色代码的值为`#ff303030`。
有关样式继承的详细信息，请参阅[样式和主题](http://developer.android.com/guide/topics/ui/themes.html#Inheritance)。



### <a name="color-picker"></a>颜色选取器

下面的屏幕截图演示**颜色选取器**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![颜色选取器](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![颜色选取器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

-----

在此示例中，**后台**可以为通过各种方式的任何值更改颜色：

-   直接单击一种颜色。
-   输入色调、 饱和度和亮度值。
-   在十进制中输入 RGB （红色、 绿色、 蓝色） 值。
-   设置所选颜色的 alpha （透明度）。
-   直接输入的十六进制颜色代码。

在颜色选取器中选择的颜色是*不*限制材料设计准则还是向可用颜色资源组。


### <a name="resources"></a>资源

**资源**选项卡上提供的颜色资源已存在于主题的列表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![资源](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![资源](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

-----

使用**资源**选项卡限制于此颜色列表你选择。 请记住，如果你选择的颜色资源的已分配给主题的另一部分时，两个相邻元素的 UI"运行速度可能一起"（因为它们具有相同的颜色） 和变得困难要区分的用户。



### <a name="material-palette"></a>材料调色板

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**材料调色板**选项卡上打开**材料设计调色板**。 该调色板中选择一个颜色值约束颜色选择，以便与材料设计准则一致。

[![材料调色板](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png#lightbox)

调色板顶部显示主材料设计颜色时调色板底部显示有关所选的主要颜色色调的范围。 例如，当选择**Indigo**，一套**Indigo**色调显示在对话框底部。
当你选择色调时，属性颜色更改为所选的色调。 在下面的示例中，`Background Tint`的按钮将变为*Indigo 500*:

![选择 Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` 设置为的颜色代码*Indigo 500* (`#ff3f51b5`)，并设计器更新以反映此更改的背景色：

[![更改背景色](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png#lightbox)

有关材料设计颜色调色板的详细信息，请参阅材料设计[颜色调色板指南](http://www.google.com/design/spec/style/color.html#color-color-palette)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

**材料调色板**选项卡上打开**材料设计调色板**所述[早期](#material_palette)。 该调色板中选择一个颜色值约束颜色选择，以便与材料设计准则一致。

[![材料调色板](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

-----



### <a name="creating-a-new-theme"></a>创建新主题

在下面的示例中，我们将使用材料调色板可创建一个新的自定义主题。 首先，我们将更改**后台**颜色到*蓝色 900*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![将背景更改为蓝色 900](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![将背景更改为蓝色 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

-----


当更改颜色资源时，弹出一条消息并显示消息*当前主题具有未保存的更改*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![未保存的更改警告](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![未保存的更改警告](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

-----


**后台**设计器中的颜色已更改为新的颜色选择，但此更改尚未保存。 有关如何处理这些更改，也提供两个选项：

-   **放弃更改**&ndash;将放弃新的颜色选择 （或选择），并将恢复到其原始状态的主题。

-   **创建新主题**&ndash;创建新主题，派生自当前所选主题，并使用在中所做的颜色替代**主题编辑器**。

当你单击**创建新主题**，以下情况之一，将发生这种情况，具体取决于所选主题：

-   如果设计器上的当前所选的主题不是项目主题，将出现与自定义主题 （使用为该自定义主题的父级的所选的主题） 中创建新的资源文件的选项。 将在创建后，设计器切换到自定义主题。

-   如果当前选定的主题，在一个位置中定义的项目主题将向你提供**更新主题**选项; 单击此选项当前所选的主题，而不是创建一个新更新。

-   如果当前选定的主题是在多个位置中定义的项目主题 (例如，在以不同方式-作为后缀**值**如文件夹**值 v21**)，将显示一个对话框，询问为新的位置来保存自定义的主题。

继续前面的示例中，单击**创建新主题**会导致创建新主题调用**自定义**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加的自定义主题](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加的自定义主题](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png#lightbox)

-----


由于当前所选主题是项目主题，没有任何对话框以更新所选的主题，或以指定新位置。


## <a name="summary"></a>总结

本主题所述 Xamarin.Android 设计器中可用的材料设计功能。 它介绍如何启用和配置材料设计网格、 如何使用材料设计调色板编辑颜色属性，以及如何使用排字缩放选择器来配置文本属性。 它还演示了如何使用主题编辑器来创建符合材料设计准则的新自定义主题。 有关材料设计 Xamarin.Android 支持的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。



## <a name="related-links"></a>相关链接

- [材料主题](~/android/user-interface/material-theme.md)
- [材料设计简介](https://www.google.com/design/spec/material-design/introduction.html)
