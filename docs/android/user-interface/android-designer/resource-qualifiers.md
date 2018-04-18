---
title: 资源限定符和可视化效果选项
description: 本主题介绍如何定义仅在某些限定符值匹配时将使用的资源。 一个简单的示例是一个语言限定的字符串资源。 与其他定义要用于其他语言的替代资源，可以将字符串资源定义为默认情况下。 可以限定所有资源类型，包括本身的布局。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bc9eb145b6d9ed7bd441d625f533c5cbbd87fccd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="resource-qualifiers-and-visualization-options"></a>资源限定符和可视化效果选项

_本主题介绍如何定义仅在某些限定符值匹配时将使用的资源。一个简单的示例是一个语言限定的字符串资源。与其他定义要用于其他语言的替代资源，可以将字符串资源定义为默认情况下。可以限定所有资源类型，包括本身的布局。_


## <a name="custom-device-configurations"></a>自定义设备配置

Android 上会提供各种设备和屏幕分辨率。
若要帮助面向多个设备的设计用户界面，设计器附带各种生成的设备配置。 它还支持添加其他设备配置;这些配置基于*限定符*指定来区分从另一个设备配置。 有许多不同类型的限定符。 有关这些资源类型的详细信息，请参阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)。

为设备选择器底部菜单是**自定义**选项如下所示：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![设备选择器菜单](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![设备选择器菜单](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


选择**自定义**显示一个对话框，可用于浏览可用的设备配置。 当你单击**设备定义**选项卡上，显示的所有已知的设备定义列表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![AVD 管理器](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![AVD 管理器](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


在设计器中预先配置的设备不能修改。 不过，您可以单击**创建设备...**来定义自定义设备定义。 或者，你可以选择现有的定义，然后单击**克隆...**才能将其用作起始点为新的定义。
例如，选择“Nexus 5”定义并单击“克隆...”将出现以下对话框：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![克隆设备](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![克隆设备](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


在下一步的屏幕截图中，名称更改为**Nexus 5 自定义**和的设备参数被修改，以创建新的自定义设备定义。 在此示例中，**纵向**已禁用，以便设备定义为仅限横向的：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![自定义设备](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自定义设备](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


单击“克隆设备”可创建新的设备定义，该定义现显示在**设备定义**列表中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![更新的设备定义](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![更新的设备定义](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


请注意，每个用户创建设备定义显示一个绿色图标与如上所示。 当您返回到**设备**选择器菜单上，新的自定义设备定义也会出现在列表的最顶层部分 （如果看不到自定义设备中的配置此列表中，请尝试重新启动 IDE）：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![自定义设备将出现在设备列表](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自定义设备将出现在设备列表](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


选择此设备配置修改以符合前面 （在此种情况下，横向只读模式下） 创建自定义项的布局：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在使用自定义设备](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在使用自定义设备](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>资源限定符选项

**资源限定符选项**可以通过单击右侧的向下三角形图标访问**设备配置**选项：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![资源限定符选项](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![资源限定符选项](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


此对话框将显示以下的资源限定符的下拉列表菜单：

-   **语言**&ndash;显示可用的语言资源，并提供了一个选项以添加新语言/区域资源。

-   **用户界面模式**&ndash;列表显示模式 (如**汽车停靠**和**支持停靠**) 以及布局方向。

每个这些下拉列表菜单打开新的对话框中，你可以在此处选择并配置的资源限定符 （如下所述）。



### <a name="language"></a>语言

**语言**下拉菜单列出仅这些语言具有资源定义 (或**所有语言**，这是默认设置)。 但是，还有**添加语言/区域...**允许你向列表添加新语言的选项：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加语言/区域](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加语言/区域](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


当你单击**添加语言/区域...**、**选择语言**对话框将打开，以显示可用的语言和区域的下拉列表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![语言列表](resource-qualifiers-images/vs/10-languages.png "语言的列表")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![语言的列表](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


在此示例中，我们已选择**fr （法语）**的语言和**BE** （比利时） 为法语的区域语言。 请注意，**区域**字段是可选的因为许多语言可以指定不考虑特定区域。 当**语言**下拉菜单再次打开，则会显示新添加语言/区域资源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![语言和区域选择](resource-qualifiers-images/vs/11-language-region-added.png "所选择的语言和区域")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![语言和区域选择](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


请注意是否添加新语言，但在不创建新资源，为它增加了语言将不再显示在下次打开项目。



### <a name="ui-mode"></a>用户界面模式

当你单击**UI 模式下**显示下拉菜单，模式的列表，如**正常**，**汽车停靠**，**支持停靠**， **电视**，**设备**，和**监视**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![用户界面模式菜单](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

此列表下方是夜间模式**不晚上**和**夜间**后, 跟布局方向**从左到右**和**从右到左**（适用于有关信息**从左到右**和**从右到左**选项，请参见[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。
中的最后一个项**资源限定符选项**对话框是**舍入屏幕**（用于 Android 磨损） 或**不舍入屏幕**(有关轮信息和非轮屏幕，请参阅[布局](https://developer.android.com/training/wearables/ui/layouts.html))。
有关 Android UI 模式的详细信息，请参阅[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![用户界面模式菜单](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

此列表下方是夜间模式**不夜间**和**夜间**后, 跟布局方向**从左到右**和**从右到左**。 有关 Android UI 模式的详细信息，请参阅[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。
璝惠**从左到右**和**从右到左**选项，请参见[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。

### <a name="round-screen"></a>舍入屏幕

中的最后一项**资源限定符选项**对话框是**Round 屏幕**菜单。 此菜单，可以选择**舍入屏幕**（用于 Android 磨损） 或**矩形屏幕**:

[![舍入屏幕菜单](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>操作栏设置

**操作栏设置**图标位于左侧的画笔 （主题编辑器） 图标：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![操作栏设置](resource-qualifiers-images/vs/14-action-bar.png "操作栏设置")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![操作栏设置](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


此图标将打开使您能够从三个操作栏模式之一中选择对话框 popover:

-   **标准**&ndash;由任一徽标或图标和标题文本以及可选副标题。

-   **列表**&ndash;列表导航模式。 此模式而不是静态的标题文本显示在活动中的导航的列表菜单 （即，它可以向用户显示为下拉列表）。

-   **选项卡** &ndash; tab 键导航模式。 而不是静态的标题文本，此模式将显示一系列活动中的导航选项卡。



## <a name="themes"></a>主题

**主题**下拉菜单中显示所有在项目中定义的主题。 选择**多主题**使用所有可用的主题已安装的 Android SDK，从列表中打开一个对话框，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![更多的主题列表](resource-qualifiers-images/vs/15-theme-menu-sml.png "多主题列表")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![更多的主题列表](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


选择一个主题后，设计图面将更新以显示新的主题的效果。 请注意此更改将变成永久性才**确定**在单击按钮**主题**对话框。 一旦选择了一个主题，它将会包含在**主题**下拉列表菜单操作，如图所示下面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![浅色主题现已推出](resource-qualifiers-images/vs/16-light-theme.png "浅色主题现已推出")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![浅色主题现已推出](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Android 版本

Android**版本**选择器设置用于呈现在设计器的布局的 Android 版本。 选择器将显示所有兼容的版本与项目的目标 framework 版本：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Android 版本的列表](resource-qualifiers-images/vs/17-android-version.png "Android 列表版本")

可以在下的项目的设置中设置的目标框架版本**属性 > 应用程序 > 使用 Android 版本进行编译**。 目标框架版本有关的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

工具箱中的可用的小组件集由项目的目标 framework 版本决定。 这也适用于中可用的属性**属性窗口**。 可用的小组件列表是*不*中选择的值由决定**版本**工具栏的选择器。 例如，如果你设置项目的目标版本为 Android 4.4，您仍可以通过以下方式选择 Android 6.0 中工具栏版本选择器来查看项目在 Android 6.0 中的显示效果，但将不能将特定于 Android 6.0 的小组件添加&ndash; 你仍将限制为可在 Android 4.4 小组件。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Android 版本的列表](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

可以在下的项目的设置中设置的目标框架版本**项目选项 > 生成 > 常规**部分。 目标框架版本有关的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

工具箱中的可用的小组件集由项目的目标 framework 版本决定。 这也适用于中可用的属性**属性填充**。 可用的小组件列表是*不*中选择的值由决定**版本**工具栏的选择器。 例如，如果你设置项目的目标版本为 Android 4.4，您仍可以通过以下方式选择 Android 6.0 中工具栏版本选择器来查看项目在 Android 6.0 中的显示效果，但将不能将特定于 Android 6.0 的小组件添加&ndash; 你仍将限制为可在 Android 4.4 小组件。

-----
