---
title: "设计器的基础知识"
description: "本主题介绍设计器功能，说明如何启动设计器、 描述设计图面上，并详细介绍如何使用属性窗格中编辑小组件属性。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b2ed48ae9df7e950525fdc0cb97181ebe5a44dfb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="designer-basics"></a>设计器的基础知识

_本主题介绍设计器功能，说明如何启动设计器、 描述设计图面上，并详细介绍如何使用属性窗格中编辑小组件属性。_

<a name="Launching_the_Designer" />

## <a name="launching-the-designer"></a>启动设计器

当创建一种布局，也可以通过双击现有.axml 文件时，设计器将自动启动。 例如，双击**main.axml**中**资源 > 布局**文件夹将加载的设计器，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Visual Studio 中的设计器屏幕](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![适用于 Mac 的 Visual Studio 中的设计器屏幕](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

同样，通过右键单击添加新的布局**布局**文件夹中的**解决方案资源管理器**并选择**添加 > 新建项 … > Android 布局**:

[![添加新项对话框](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

同样，通过右键单击添加新的布局**布局**文件夹中的**解决方案 Pad**并选择**添加 > 新文件 > Android > 布局**:

[![添加新文件对话框](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png)

-----

这将创建一个新的.axml 文件并将其加载到设计图面。


<a name="Designer_Features" />

## <a name="designer-features"></a>设计器的功能

设计器由组成支持其各种功能的多个部分组成，如下面的屏幕截图中所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![设计器窗格的图示](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![设计器窗格的图示](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png)

-----

编辑设计器的布局时，使用以下功能来创建和调整你的设计：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   **设计图面**&ndash;向您提供的可编辑表示形式的布局将在设备上的显示方式将加快 visual 构造的用户界面。

-   **工具栏**&ndash;显示的选择器列表：**设备**，**版本**，**主题**，布局配置和操作栏设置。 工具栏还包括用于启动主题编辑器和用于启用材料设计网格的图标。

-   **工具箱**&ndash;提供的小组件和可以拖放到设计图面上的布局的列表。

-   **属性窗口**&ndash;列出了用于查看和编辑所选的小组件的属性。

-   **文档大纲**&ndash;显示构成布局的小组件的树。 你可以单击以使其在设计器中选择的树中的项。 此外，如果单击树中的项将项的属性加载到属性窗口。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   **设计图面**&ndash;向您提供的可编辑表示形式的布局将在设备上的显示方式将加快 visual 构造的用户界面。

-   **工具栏**&ndash;显示的选择器列表：**设备**，**版本**，**主题**，布局配置和操作栏设置。 工具栏还包括用于启动主题编辑器和用于启用材料设计网格的图标。

-   **工具箱**&ndash;提供的小组件和可以拖放到设计图面上的布局的列表。

-   **属性填充**&ndash;列出了用于查看和编辑所选的小组件的属性。

-   **文档大纲**&ndash;显示构成布局的小组件的树。 你可以单击以使其在设计器中选择的树中的项。 此外，如果单击树中的项将项的属性加载到属性填充。

-----


<a name="Toolbar" />

## <a name="toolbar"></a>Toolbar

工具栏 （位于设计图面上方） 提供配置选择器和工具菜单：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![关系图的设计器工具栏](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![关系图的设计器工具栏](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png)

-----


工具栏提供了对以下功能的访问：

-   **备用布局选择器**&ndash;允许你选择从不同的布局版本。

-   **设备选择器**&ndash;定义一组与屏幕尺寸、 分辨率和键盘可用性等特定设备关联的限定符。 你还可以添加和删除新设备。

-   **Android 版本选择器**&ndash;布局所面向的 Android 版本。 设计器将呈现根据所选的 Android 版本的布局。

-   **主题选择器**&ndash;选择布局的用户界面主题。

-   **配置选择器**&ndash;选择该设备的配置，如*纵向*或*横向*。

-   **资源限定符选项**&ndash;打开一个对话框，提供用于选择的下拉列表菜单*语言*， *UI 模式下*，*夜间模式*，和*舍入屏幕*选项。

-   **操作栏设置**&ndash;配置布局的操作栏设置。

-   **主题编辑器**&ndash;打开*主题编辑器*，从而使你可以自定义的所选主题的元素。

-   **材料设计网格**&ndash;启用或禁用*材料设计网格*。 材料设计网格旁边的下拉列表菜单项打开一个对话框，允许你自定义网格。

这些主题中详细介绍了其中每这些功能：

[资源限定符和可视化效果选项](~/android/user-interface/android-designer/resource-qualifiers.md)相关详细的信息提供**设备选择器**， **Android 版本选择器**，**主题选择器**，**配置选择器**，**资源限定选项**，和**操作栏设置**。

[替代的布局视图](~/android/user-interface/android-designer/alternative-layout-views.md)说明如何使用**的替代项布局选择器**。

[材料设计功能](~/android/user-interface/android-designer/material-design-features.md)提供的全面概述**主题编辑器**和**材料设计网格**。


<a name="Design_Surface" />

## <a name="design-surface"></a>设计图面

设计器使你拖放到设计图面上的工具箱中的小组件。 当与设计器中的小组件交互 （通过添加新小组件或重新定位现有的） 时，将显示垂直和水平线，以将标记为可用的插入点。 在下面的示例中，新`Button`构件正拖动到设计图面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在设计图面上的插入行示例](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在设计图面上的插入行示例](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png)

-----

此外，可以复制小组件： 你可以使用复制和粘贴复制的小组件中，也可以拖动和删除现有的小组件时按<kbd>Ctrl</kbd>密钥。

<a name="Context_Menu_Commands" />

### <a name="context-menu-commands"></a>上下文菜单命令

在设计图面中，在文档大纲可用，上下文菜单。 此菜单显示可用于所选的小组件和其容器，使其更轻松地在容器 （这并不总是容易设计图面上） 上执行操作的命令。 下面是在上下文菜单的示例：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![示例上下文菜单右键单击设计图面时](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png)

在此示例中，右键单击`TextView`打开上下文菜单，提供多个选项：

-   **LinearLayout** &ndash;打开用于编辑的子菜单`LinearLayout`的父级`TextView`。

-   **删除**，**复制**，和**剪切**&ndash;操作适用于右键单击`TextView`。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![示例上下文菜单右键单击设计图面时](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png)

在此示例中，右键单击`TextView`打开上下文菜单，提供多个选项：

-   **RelativeLayout** &ndash;打开用于编辑的子菜单`RelativeLayout`的父级`TextView`。

-   **LinearLayout** &ndash;打开用于编辑的子菜单`LinearLayout`的父级`TextView`。

-   **删除**，**复制**，和**剪切**&ndash;操作适用于右键单击`TextView`。

-----

在此示例中，右键单击`TextView`打开上下文菜单，提供多个选项：

-   **LinearLayout** &ndash;打开用于编辑的子菜单`LinearLayout`的父级`TextView`。

-   **删除**，**复制**，和**剪切**&ndash;操作适用于右键单击`TextView`。


<a name="Zoom_Controls" />

### <a name="zoom-controls"></a>缩放控件

设计图面支持缩放通过几个控件，如所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![关系图的设计图面缩放控件](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![关系图的设计图面缩放控件](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png)

-----

这些控件使其更轻松地查看特定区域的设计器中的用户界面：

-   **突出显示容器**&ndash;突出显示在设计图面上的容器，以便更轻松地找到时放大和缩小。

-   **正常大小**&ndash;呈现布局像素的-像素，以便你可以看到如何布局着眼于所选设备的分辨率。

-   **适应窗口**&ndash;设置缩放级别，以使整个布局设计图面上可见。

-   **放大**&ndash;以增量方式放大每次单击，放大布局。

-   **缩小**&ndash;将缩小以增量方式每次单击，从而显示在设计图面上较小的布局。

请注意选的缩放设置不会影响在运行时应用程序的用户界面。

<a name="property_pad" />

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="property-pad"></a>属性填充

设计器支持通过小组件属性的编辑**属性填充**。 根据该小组件将在设计器图面上选择的属性填充更改中列出的属性。 当`Button`选择在前面的示例，该属性`Button`显示小组件：

[![属性填充的屏幕截图](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png)

-----

<a name="Property_Pad_Sections" />

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="properties-window"></a>属性窗口

设计器支持通过小组件属性的编辑**属性窗口**。 根据该小组件将设计图面选择属性窗口更改中列出的属性。
当`Button`选择在前面的示例，该属性`Button`显示小组件：

![属性窗口的屏幕截图](designer-basics-images/vs/08-property-pad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="property-pad-sections"></a>属性填充节

属性填充分为几个部分，将类似的属性组合在一起&ndash;这样会更容易，若要查找感兴趣的属性：

-   **小组件**&ndash;主要属性的小组件中，如`id`， `visibility`， `text`，等等。用于管理小组件的内容的属性通常放在此处。

-   **样式**&ndash;属性更改的可视外观的小组件中，例如`font`， `text color`， `background`，等等。

-   **布局**&ndash;设置的位置和大小的小组件的属性。

-   **滚动**&ndash;滚动属性。

-   **行为**&ndash;设置小组件的行为方式的标志。

-----


<a name="Default_Values" />

### <a name="default-values"></a>默认值

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

大多数小组件的属性将为空白中**属性**窗口因为从所选的 Android 主题继承其值。
**属性**窗口将仅显示为所选的小组件显式设置的值; 它将不会显示从主题继承的值。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

大多数小组件的属性将为空白中**属性填充**因为从所选的 Android 主题继承其值。 **属性填充**将仅显示为所选的小组件显式设置的值; 它将不会显示从主题继承的值。

-----

<a name="Referencing_resources" />

### <a name="referencing-resources"></a>引用的资源

某些属性可以引用的资源的布局以外的文件中定义了**.axml**文件。 此类型的最常见情形是`string`和`drawable`资源。 但是，引用可以还可用于其他资源，如`Boolean`值和维度。
当属性支持资源引用，浏览图标 (一个省略号， &hellip;) 条目旁边的文本属性所示。
此按钮将打开一个资源选择器，单击时。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，下面的屏幕截图显示可用的资源时单击的文本字段右侧的省略号`Button`中的小组件**属性**窗口：

[![示例资源具有两个资源列出的屏幕快照](designer-basics-images/vs/09-resources-sml.png)](designer-basics-images/vs/09-resources.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，下面的屏幕截图显示可用的资源时单击的文本字段右侧的省略号`Button`中的小组件**属性填充**:

[![示例资源具有两个资源列出的屏幕快照](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png)

-----

下一个示例演示的资源选择器`Src`属性`ImageView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![列出了 ImageView 的图标资源的资源选择器](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![列出了 ImageView 的图标资源的资源选择器](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png)

-----


<a name="Boolean_Property_References" />

### <a name="boolean-property-references"></a>布尔值属性引用

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

*布尔*从属性窗口中的下拉列表菜单通常选择属性。 你可以选择`true`或`false`值，也可以通过单击选择的属性引用**选择资源...**.如还直接输入值`true`或`false`。

![设置布尔属性的示例](designer-basics-images/vs/11-boolean.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

*布尔*属性通常显示为处于属性填充的复选框。 当`Boolean`属性支持资源引用、 在属性旁边出现一个小的复选框。 选中的复选框意味着`true`和空框意味着`false`。 如还直接输入值`true`或`false`。 鼠标悬停输入将显示一个小文本字段图标。 如果你想要手动输入此值，可以单击它。

[![设置布尔属性的示例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png)

<a name="Grouped_Properties" />

## <a name="grouped-properties"></a>分组的属性

一些小组件具有组合在一起的多值属性 (如`Padding`，例如)。 中列出了这些属性值**属性填充**单一、 可扩展的行中。 其中一些属性可以进行编辑直接在分组的行中，如`Padding`属性如下所示：

[![Padding 属性的示例设置](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png)

-----

<a name="Editing_Properties_Inline" />

## <a name="editing-properties-inline"></a>编辑属性内联

Android 设计器支持直接编辑的设计图面上的某些属性 （以便您无需搜索属性列表中的这些属性）。 可以直接编辑的属性包括文本、 边距和大小。

<a name="Text" />

### <a name="text"></a>Text

一些小组件的文本属性 (如`Button`和`TextView`)，可以直接在设计图面上编辑。 双击某个小组件将将它置于编辑模式，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![你好字符串的文本资源](designer-basics-images/vs/12-text-resource.png "文本资源")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![你好字符串的文本资源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png)

-----

可以输入一个新的文本值，或者您可以输入新的资源字符串。 在下面的示例中，`@string/hello`资源被替换为文本， `CLICK THIS BUTTON`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Shift + Enter 以自动为新的资源链接文本](designer-basics-images/vs/13-shift-enter-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Shift + Enter 以自动为新的资源链接文本](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png)

-----

此更改存储在小组件的`text`属性; 它不会修改分配给值`@string/hello`资源。
当你键入新的文本字符串中时，你可以按<kbd>Shift</kbd> +
<kbd>Enter</kbd>将输入的文本自动链接到新的资源。

<a name="Margin" />

### <a name="margin"></a>边距

当你选择某个小组件时，设计器将显示允许你以交互方式更改的大小或小组件的边距的句柄。 单击该小组件处于选定状态时在边距编辑模式和大小编辑模式之间切换。

第一次单击小组件时，将显示边距句柄。 如果将鼠标移到一个句柄，设计器会显示该句柄将更改的属性 (如下所示为`layout_marginLeft`属性):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在设计器中处理显示边距的屏幕截图](designer-basics-images/vs/15-margin-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在设计器中处理显示边距的屏幕截图](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png)

-----

如果已设置边距，虚线显示，指示边距所占据的空间：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![标记按钮周围的空间的虚线的示例](designer-basics-images/vs/16-margins-set.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![标记按钮周围的空间的虚线的示例](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png)

-----


<a name="Size" />

### <a name="size"></a>大小

如前所述，可以通过单击某个小组件，它已处于选定状态时切换到大小编辑模式。 单击要设置到的指定维数的大小的三角句柄`wrap_content`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![换行内容和重设大小句柄](designer-basics-images/vs/17-wrap-content.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![换行内容和重设大小句柄](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png)

-----

单击**包装内容**句柄收缩该小组件，因此，它是不大于包装括起来的内容所需该维度中。 在此示例中，按钮文本的下一步的屏幕截图中所示的水平收缩。

当大小值设置为**内容包装**，设计器会显示指向到的大小发生更改的相反方向的三角句柄`match_parent`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![匹配父句柄](designer-basics-images/vs/18-match-parent.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![匹配父句柄](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png)

-----

单击**匹配父**句柄还原该维度中的大小，以便它与父小组件相同。

此外，您可以拖动的循环重设大小句柄 （如上面的屏幕截图中所示） 以调整大小小组件设为任意`dp`值。 当你执行此操作，同时**内容包装**和**匹配父**句柄仅供该维度：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![循环的调整大小图柄](designer-basics-images/vs/19-resize-dp.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![循环的调整大小图柄](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png)

-----

并非所有容器都允许编辑`Size`的小组件。 例如，请注意，在下面的屏幕截图，与`LinearLayout`选择，调整大小图柄不会显示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![没有重设大小句柄](designer-basics-images/vs/20-no-resize-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![没有重设大小句柄](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png)

-----


<a name="Outline_View" />

## <a name="document-outline"></a>文档大纲

**文档大纲**显示布局小组件层次结构。
在以下示例中，包含`LinearLayout`选择小组件：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![文档大纲](designer-basics-images/vs/21-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文档大纲](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png)

-----

轮廓的所选的小组件 (在这种情况下， `LinearLayout`) 也会突出显示在设计图面上。 在文档大纲中所选的小组件保持与设计图面上其副本同步。 这可用于选择查看组，并不总是容易设计图面上。

文档大纲支持复制和粘贴，或者可以使用拖放。 拖放支持从文档大纲到设计图面和文档大纲设计图面。 此外，右键单击文档大纲中的项将显示该项目 （显示相同的上下文菜单右键单击设计图面上的同一小组件时，将显示） 的上下文菜单。
