---
title: "替代的版式视图"
description: "本主题说明如何布局可以进行版本管理使用的资源限定符。 例如，可以有仅使用该设备时在横向模式中的布局的版本和仅适用于纵向模式的布局版本。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: c2df60a79ea3b5a0ff226cfaade0440db13fd5ea
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="alternative-layout-views"></a>替代的版式视图

_本主题说明如何布局可以进行版本管理使用的资源限定符。例如，可以有仅使用该设备时在横向模式中的布局的版本和仅适用于纵向模式的布局版本。_


## <a name="creating-alternative-layouts"></a>创建其他布局

当你单击**替代布局视图**图标 (左侧**设备**)，预览窗格将打开，以列出在你的项目中可用的备用布局。 如果不有任何其他布局**默认**显示视图： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![备用布局视图窗格中](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "备用布局视图窗格中")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![备用布局视图窗格中](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

当单击绿色加号旁边**新版本**、**创建布局变体**对话框随即打开，以便你可以选择此布局变体中的资源限定符： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![创建布局变体](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "创建布局变体")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![创建布局变体](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


在下面的示例中的资源限定符**屏幕方向**设置为**横向**，和**屏幕的大小**更改为**大**. 这将创建名为的新布局版本**大型上关于领土**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![大型上关于领土变体](alternative-layout-views-images/vs/03-large-land-sml.png "大上关于领土变体")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![大型上关于领土变体](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


请注意，在左侧的预览窗格中显示的资源限定符选项的效果。 单击**添加**创建替代的布局并切换到该布局的设计器。 **的替代项布局视图**预览窗格将显示哪种布局加载到小右指针通过设计器，如下面的屏幕截图中所示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![加载的布局指示器](alternative-layout-views-images/vs/04-new-layout-sml.png "加载的布局指示器")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![加载的布局指示器](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>编辑其他布局

通常，当你创建其他布局时，很需要进行适用于所有分叉版本布局的一项更改。 例如，你可能想要将按钮文本更改为黄色所有为布局中。 如果你有大量的布局，维护您很快就简洁、 易出错如果你需要将传播到所有版本的单个更改。 

若要简化维护的多个布局版本，该设计器提供**多编辑**跨一个或多个布局传播所做的更改的模式。 当存在，多个布局时**多编辑**显示图标： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![多编辑图标](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多编辑图标")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![多编辑图标](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


当你单击**多编辑**图标，显示线指示两种布局，会链接 （如下所示）; 即，当为一个布局进行更改，则所做的更改传播到任何链接的布局。 您可以通过单击下面的屏幕截图所示的带圆圈的图标中取消链接所有布局： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![取消链接所有布局](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消都链接所有布局")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![取消链接所有布局](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


如果你有两个以上的布局，你可以有选择地切换到每个布局预览版来确定哪些布局链接在一起的左侧的编辑按钮。 例如，如果你想要进行传播的单个更改到的第一个和最后一个的三种布局，您将首先取消链接中间布局如下所示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![取消链接中间布局](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消链接中间布局")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![取消链接中间布局](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

在此示例中，进行更改为**默认**或**长**布局将传播到其他布局而不适用于**大型上关于领土**布局。 



### <a name="multi-edit-example"></a>多编辑示例 

一般情况下，当为一个布局进行更改，该相同的更改将传播到所有其他链接的布局。 例如，添加一个新`TextView`小组件设为**默认**布局和更改其文本字符串以`Portrait`将导致相同的更改必须做出的所有链接的布局。 下面是其外观**默认**布局： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "添加 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

`TextView`也将添加到**大型上关于领土**布局查看，因为它链接到**默认**布局： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![横向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "横向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![横向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

但如果你想要进行本地到只有一种布局更改 （即，您不希望更改传播到任何其他布局）？ 若要执行此操作，必须取消链接你想要更改之前对其进行修改，如下所述的布局。 



### <a name="making-local-changes"></a>进行本地更改 

假设我们想要能够添加这两种布局`TextView`，但我们还想要更改中的文本字符串**大型上关于领土**布局`Landscape`而非`Portrait`。 如果我们进行到此更改**大型上关于领土**时链接这两种布局，更改将传播回**默认**布局。 因此，我们必须先取消链接两个布局之前我们进行的更改。 当我们修改中的文本**大型上关于领土**到`Landscape`，设计器将标记与一个红色的帧，以指示更改是本地的此更改**大型上关于领土**布局和为*不*传播回**默认**布局： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![本地更改](alternative-layout-views-images/vs/10-local-change-sml.png "本地更改")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![本地更改](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

当你单击**默认**布局，以查看它，`TextView`仍将文本字符串设置为`Portrait`。 



## <a name="handling-conflicts"></a>处理冲突 

如果你决定要更改中的文本的颜色**默认**为绿色的布局，你将看到警告图标显示在链接的布局。 单击该布局将打开以显示冲突的布局。 红框突出显示引起冲突的小组件，并显示以下消息：*最近的更改具有此备用布局导致冲突*。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![冲突更改](alternative-layout-views-images/vs/11-conflicting-change-sml.png "冲突更改")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![冲突的更改](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

A*冲突框*显示右侧的小组件以解释冲突： 

[![冲突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

冲突框中显示的已更改的属性的列表，并列出它们的值。 单击**忽略冲突**属性更改仅适用于此小组件。 单击**应用**适用于此小组件以及有关对应项小组件中链接的属性更改**默认**布局。 如果应用所有属性更改，将自动丢弃冲突。 


### <a name="view-group-conflicts"></a>查看组冲突 

属性更改不冲突的唯一源。 插入或删除小组件时，可以检测到冲突。 例如，当**大型上关于领土**布局从取消链接**默认**布局，与`TextView`中**大型上关于领土**布局拖放上面`Button`，设计器将标记移动的小组件，以指示冲突：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![查看组冲突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "查看组冲突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![视图组冲突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

但是，没有标记上没有`Button`。 尽管的位置`Button`已更改，`Button`显示特定于任何应用的更改**大型上关于领土**配置。 

如果`CheckBox`添加到**默认**布局，生成另一个冲突，并通过显示一个警告图标**大型上关于领土**布局： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![复选框冲突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "复选框冲突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![复选框冲突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

单击**大型上关于领土**布局显示冲突。 显示以下消息：*最近的更改具有此备用布局导致冲突*。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Alt 布局冲突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Alt 布局冲突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Alt 布局冲突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

此外，冲突框中显示以下消息：

[![冲突消息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

添加`CheckBox`导致冲突，因为**大型上关于领土**布局有更改`LinearLayout`包含它。 但是，在这种情况下冲突框中显示为刚插入到该小组件时，才**默认**布局 ( `CheckBox`)。

如果你单击**忽略冲突**，设计器将解决冲突，允许冲突该框中拖到布局小组件未显示该小组件 (在这种情况下，**大型上关于领土**布局): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![解析组冲突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "解析组冲突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![解析组冲突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

与前面的示例中所示`Button`、`CheckBox`没有红色更改标记，因为仅`LinearLayout`具有中应用的更改**大型上关于领土**布局。



### <a name="conflict-persistence"></a>冲突持久性

冲突文件中保留布局作为 XML 注释，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，当一个项目是关闭并重新打开，所有冲突仍在那里&ndash;甚至是那些已忽略。

