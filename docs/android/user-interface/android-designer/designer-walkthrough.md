---
title: 使用 Android 设计器
description: 本主题是 Xamarin.Android 设计器的演练。 它演示了如何创建用户界面小颜色浏览器应用程序;此用户界面是完全在设计器中创建的。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/10/2018
ms.openlocfilehash: 8d1dc410d5336d9c2505a18720cc7f734e838c39
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798627"
---
# <a name="using-the-android-designer"></a>使用 Android 设计器

_本主题是 Xamarin.Android 设计器的演练。它演示了如何创建用户界面小颜色浏览器应用程序;此用户界面是完全在设计器中创建的。_


## <a name="overview"></a>概述

使用 XML 文件或以编程方式编写代码，可以以声明方式创建 android 用户界面。 Xamarin.Android 设计器允许开发人员创建和修改声明性布局以可视方式，而无需处理手动编辑 XML 文件的内容麻烦。 设计器还提供实时的反馈，让开发人员而无需重新部署到设备或仿真程序的应用程序评估 UI 更改。 这可以极大地加快 Android UI 开发。 在本文中，我们提供的演练，演示如何使用 Xamarin.Android 设计器可以直观地创建用户界面。


## <a name="walkthrough"></a>演练

本演练的目的是使用 Android 设计器创建的示例颜色浏览器应用程序会显示的颜色、 名称和其 RGB 值列表的用户界面。 我们将了解如何将小组件添加到设计图面，以及如何直观地布局这些小组件。 之后，我们将介绍如何修改设计图面上以交互方式或通过使用设计器的小组件**属性填充**。 最后，我们将看到我们的设计时运行的应用程序的外观。

让我们进入正题！


### <a name="creating-a-new-project"></a>创建新项目

第一步是创建新的 Xamarin.Android 项目。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

启动 Visual Studio，然后单击**新项目...** 然后选择**Visual C\# > Android > Android 应用程序 (Xamarin)** 模板：

[![Android 的空白应用程序](designer-walkthrough-images/vs/01-android-app-sml.w157.png)](designer-walkthrough-images/vs/01-android-app.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

针对 Mac 和单击启动 Visual Studio**新解决方案...**.选择**Android 应用**模板，然后单击**下一步**:

[![Android 的空白应用程序](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

命名新应用程序**DesignerWalkthrough**单击**确定**。

[![名称应用程序](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

命名新应用程序**DesignerWalkthrough**。 下**目标平台**，选择**最新和最大**单击**下一步**:

[![名称应用程序](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png#lightbox)

在下一步的对话框屏幕中，单击**创建**。

-----



### <a name="adding-a-layout"></a>添加布局

让我们创建**LinearLayout** ，我们将使用以保存我们的用户界面元素。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，右键单击**资源/布局**中**解决方案资源管理器**和选择**添加 > 新建项...**.在**添加新项**对话框中，选择**Android 布局**。 命名该文件**ListItem.axml**单击**添加**:

[![新的布局](designer-walkthrough-images/vs/03-new-layout-sml.w157.png)](designer-walkthrough-images/vs/03-new-layout.w157.png#lightbox)

新**ListItem**设计器中显示布局：

[![设计器视图](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png#lightbox)

单击**源**要查看此布局的 XML 源的设计器底部的选项卡：

[![设计器的 XML](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png#lightbox)

从**视图**菜单上，单击**其他 Windows > 文档大纲**以打开**文档大纲**。 **文档大纲**演示布局当前包含单个**LinearLayout**小组件：

[![文档大纲](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在适用于 Mac 的 Visual Studio，右键单击**资源/布局**中**解决方案**填充和选择**添加 > 新文件...**.在**新文件**对话框中，选择**Android > 布局**。 命名该文件**ListItem**单击**新建**:

[![新的布局](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png#lightbox)

新**ListItem**设计器中显示布局：

[![设计器视图](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png#lightbox)

单击**源**要查看此布局的 XML 源的设计器底部的选项卡。 当你单击**文档大纲**选项卡在右侧，它显示布局当前包含单个**LinearLayout**小组件：

[![设计器的 XML](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png#lightbox)

-----



### <a name="creating-the-list-item-user-interface"></a>创建列表项的用户界面

接下来，我们将创建颜色浏览器应用的用户界面。
单击**设计器**选项卡以返回到设计图面。
在**工具箱**，向下滚动到**映像和媒体**部分和细读每个项，直到你找到*ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![找到 ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![找到 ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png#lightbox)

-----

或者，你可以输入*ImageView*到要查找的搜索栏`ImageView`小组件：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView 搜索](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![ImageView 搜索](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png#lightbox)

-----

将该`ImageView`拖到设计图面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在画布上 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在画布上 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png#lightbox)

-----

这`ImageView`将用于显示颜色样本我们颜色的浏览器应用程序。

接下来，拖动`LinearLayout (Vertical)`小组件从**工具箱**与设计器中。 请注意，蓝色边框指示添加的边界`LinearLayout`，和**文档大纲**它驻留的正下方的显示`imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![蓝色边框](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![蓝色边框](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png#lightbox)

-----

当选择`ImageView`在设计器中，蓝色边框移动来包围`ImageView`; 在**文档大纲**，所选内容移动到`imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![选择 ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![选择 ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png#lightbox)

-----

接下来，拖动`Text (Large)`小组件从**工具箱**到新添加`LinearLayout`。 请注意该设计器使用绿色突出显示，以指示新的小组件将插入的位置：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![绿色突出显示](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![绿色突出显示](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png#lightbox)

-----

接下来，添加`Text (Small)`小组件下面`Text (Large)`小组件：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加小文本小组件](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![添加小文本小组件](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png#lightbox)

-----

此时，设计器应类似于下面的屏幕快照：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![设计器布局](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![设计器布局](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png#lightbox)

-----

如果两个`textView`小组件不是内部`linearLayout1`，你可以拖到`linearLayout1`中**文档大纲**并将它们放置，所以，如上面的屏幕快照中所示出现 (下缩进`linearLayout1`)。



### <a name="arranging-the-user-interface"></a>排列的用户界面

让我们修改 UI 以显示`ImageView`在左侧，这两个`TextView`右侧的小组件堆积`ImageView`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  选择 `ImageView`。

2.  在**属性窗口**，单击**按分类顺序**排序图标和向下的滚动到**布局的分组**部分。

3.  更改`layout_width`将设置为`wrap_content`:

![设置包装内容](designer-walkthrough-images/vs/15-wrap-content.png "组换行内容")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  与`ImageView`处于选中状态，单击**属性**选项卡。

2.  正下方**属性**选项卡上，单击**布局**。

3.  向下滚动到**分组**和更改`Width`将设置为`wrap_content`:

[![设置包装内容](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png#lightbox)

-----

若要更改的另一种方法`Width`设置方法是单击右侧的小组件以切换到其宽度设置上的三角形`wrap_content`:

[![拖动以调整宽度](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png#lightbox)

再次单击此三角形返回`Width`将设置为`match_parent`。

接下来，切换到**文档大纲**并选择的根`LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![选择根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![选择根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png#lightbox)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

与根`LinearLayout`选择，返回到**属性**窗口中，单击**按字母顺序显示**排序图标，并滚动，直到找到`orientation`。 更改`orientation`将设置为`horizontal`:

![选择水平方向](designer-walkthrough-images/vs/17-horizontal-orientation.png "选择水平方向")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

与根`LinearLayout`选择，返回到**属性**选项卡，单击**小组件**。 更改`Orientation`将设置为`horizontal`:

[![选择水平方向](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png#lightbox)

-----

此时，设计器应类似于下面的屏幕快照：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![设计器布局](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![设计器布局](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png#lightbox)

-----


### <a name="modifying-the-spacing"></a>修改间距

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下来，我们将修改在用户界面提供小组件之间的更多空间的填充和边距设置。 选择`ImageView`，单击**按分类顺序**中的搜索图标**属性**窗口和向下的滚动到**布局**部分。 更改`Min Height`到`70dp`、`Min Width`到`50dp`，和`padding`到`10dp`。 这适用填充的四条边`ImageView`和垂直 elongates:

[![设置填充](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下来，我们将修改在用户界面提供小组件之间的更多空间的填充和边距设置。 选择`ImageView`单击**布局**下**属性**。 更改`Padding`到`10dp`、`Min Width`到`50dp`，和`Min Height`到`70dp`。 这适用填充的四条边`ImageView`和垂直 elongates:

[![设置填充](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

下、 左、 右，和可以通过输入值插入单独设置上边距设置`paddingBottom`， `paddingLeft`， `paddingRight`，和`paddingTop`字段，分别。
例如，设置`paddingLeft`字段`5dp`和`paddingBottom`， `paddingRight`，和`paddingTop`字段设置为`10dp`:

[![自定义填充设置](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

上、 右、 下、 和左边距设置可以单独设置通过输入值插入`Top`， `Right`， `Bottom`，和`Left`分别填充字段。 例如，设置`Left`填充到值`5dp`和`Top`， `Right`，和`Bottom`填充到值`10dp`。 请注意，`Padding`设置更改为这些值的以逗号分隔列表：

[![自定义填充设置](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下来，调整的位置`LinearLayout`小工具，它包含两个`TextView`小组件。 在**文档大纲**，选择`linearLayout1`。 在**属性**窗口中，滚动到**布局的分组**部分。 设置`layout_marginBottom`， `layout_marginLeft`， `layout_marginRight`，和`layout_marginTop`到`5dp`， `5dp`， `0dp`，和`5dp`分别：

[![设置页边距](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下来，调整的位置`LinearLayout`小工具，它包含两个`TextView`小组件。 在**文档大纲**，选择`linearLayout1`。 在**属性**窗格中，选择**布局**选项卡。向下滚动到**分组**部分，然后设置`Left`， `Top`， `Right`，和`Bottom`到距`5dp`， `5dp`， `0dp`，和`5dp`分别：

[![设置页边距](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png#lightbox)

-----



### <a name="removing-the-default-image"></a>删除的默认映像

由于我们要使用`ImageView`显示颜色 （而不是映像），让我们删除添加的模板的默认图像源。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  选择 `ImageView`。

2.  在**属性**，查找`src`字段。

3.  清除`src`设置，以便它将为空：

![清除 ImageView src 设置](designer-walkthrough-images/vs/22-clear-img-src.png "清除 ImageView src 设置")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  选择 `ImageView`。

2.  单击**小组件**下**属性**。

3.  清除`Src`设置，以便它将为空：

[![清除 ImageView src 设置](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png#lightbox)

-----


### <a name="adding-a-listview-container"></a>添加 ListView 容器

现在， **ListItem**定义布局，我们将添加`ListView`到 Main 布局。 这`ListView`将包含一份**ListItems**。
在**工具箱**，找到`ListView`小组件并将其拖到设计图面。 `ListView`设计器中将为空除外概述当选中时其边框的蓝线。 你可以查看**文档大纲**以便确认**ListView**已正确添加：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![新的 ListView](designer-walkthrough-images/vs/23-new-listview.png "新的 ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新的 ListView](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png#lightbox)

-----

默认情况下，`ListView`给定`Id`值`@+id/listView1`。
打开**小组件**下**属性**和更改`Id`到`@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![将 id 重命名为 myListView](designer-walkthrough-images/vs/24-change-id.png "myListView 重命名 id")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![将 id 重命名为 myListView](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png#lightbox)

-----

此时，我们用户界面，可供使用。



### <a name="running-the-application"></a>运行应用程序


打开**MainActivity.cs**和其代码替换为以下代码：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
        }
    }
    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

此代码使用自定义`ListView`以加载颜色信息并将此数据显示在 UI 中我们刚刚创建的适配器。 若要使此示例简短，颜色信息是硬编码在列表中，但无法修改该适配器，以便从数据源中提取颜色信息或计算在运行过程中。 有关详细信息`ListView`适配器，请参阅[Listview 和适配器](~/android/user-interface/layouts/list-view/index.md)。

生成并运行应用程序。 下面的屏幕截图是在设备上运行时如何显示该应用程序的一个示例：

[![最终屏幕快照](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)



## <a name="summary"></a>总结

在本文中，我们在遍历如何在适用于 Mac 的 Visual Studio 中使用 Xamarin.Android 设计器用于创建用户界面。 接下来，我们演示了如何在列表中创建单个项的界面。
与此同时，我们介绍了如何添加小组件以及如何直观地布局，以及如何将资源分配以及如何设置这些小组件的各种属性。 总之，我们可以说明我们在设计器中创建的接口中的示例应用程序的运行方式。
