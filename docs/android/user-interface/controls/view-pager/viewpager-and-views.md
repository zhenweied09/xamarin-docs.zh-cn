---
title: "与视图 ViewPager"
description: "ViewPager 是使您可以实现动作导航布局管理器。 动作导航允许用户轻扫，左侧和右侧逐句通过数据页。 本指南介绍如何实现使用 ViewPager 和 PagerTabStrip，为数据页中使用视图 swipeable UI （后续指南介绍如何用于页面的片段）。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d81f897fb7af39334cec4ea9f806533f09754079
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="viewpager-with-views"></a>与视图 ViewPager

_ViewPager 是使您可以实现动作导航布局管理器。动作导航允许用户轻扫，左侧和右侧逐句通过数据页。本指南介绍如何实现使用 ViewPager 和 PagerTabStrip，为数据页中使用视图 swipeable UI （后续指南介绍如何用于页面的片段）。_

<a name="overview" />
 
## <a name="overview"></a>概述

本指南是一个演练，提供分步演示如何使用`ViewPager`实现落叶和长期有效树的图像库。 在此应用中，用户刷左侧和右侧通过"树目录"以查看树图像。 在该目录的每一页的顶部，树的名称被列入`PagerTabStrip`，并且树的图像将显示在`ImageView`。 使用适配器接口`ViewPager`到基础数据模型。 此应用程序实现派生自的适配器`PagerAdapter`。 

尽管`ViewPager`-基于应用程序通常实现与`Fragment`s，有一些相对简单的用例其中的额外复杂性`Fragment`s 没有必要。 例如，在本演练中所示的基本映像库应用不需要使用`Fragment`s。 因为内容是静态的不同的映像之间来回用户仅刷，实现可以保存更简单通过使用 Android 的标准视图和布局。 


<a name="start" />

## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**TreePager** (请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)有关创建新的 Android 项目的详细信息)。 接下来，启动 NuGet 包管理器。 (有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 查找和安装**Android 支持库 v4**: 

[![选择在 NuGet 包管理器的屏幕截图的支持 v4 Nuget](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png)

这还将安装的任何其他包 reaquired **Android 支持库 v4**。


<a name="datasource" />

## <a name="add-an-example-data-source"></a>添加示例数据源

在此示例中，树目录数据源 (由表示`TreeCatalog`类) 提供`ViewPager`项内容。 
`TreeCatalog` 包含现成的树图像和适配器将用于创建的树标题集合`View`s。 `TreeCatalog`构造函数不需要任何参数：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

中的图像集合`TreeCatalog`如此组织可按照索引器访问每个图像。 例如，以下代码行检索集合中的第三个图像的图像资源 ID: 

```csharp
int imageId = treeCatalog[2].imageId;
```

因为的实现详细信息`TreeCatalog`不了解相关`ViewPager`、`TreeCatalog`代码未在此处列出。 源代码到`TreeCatalog`位于[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)。 下载此源文件 (或复制并粘贴到一个新的代码**TreeCatalog.cs**文件) 并将其添加到你的项目。 此外，下载并解压缩[图像文件](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)到你**可资源/绘制**文件夹并将其包括在项目中。 


<a name="layout" />

## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开**Resources/layout/Main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 


<a name="setup" />

## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

将 `OnCreate` 方法替换为以下代码：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

此代码执行以下任务：

1.  设置将视图从**main.axml**布局资源。

2.  检索到的引用`ViewPager`从布局。

3.  实例化一个新`TreeCatalog`作为数据源。

当生成和运行此代码时，你应看到类似于以下屏幕截图显示： 

[![显示空 ViewPager 的应用的屏幕快照](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png)

此时，`ViewPager`为空，因为它缺少适配器用于访问中的内容**TreeCatalog**。 在下一步的部分中， **PagerAdapter**将创建连接`ViewPager`到**TreeCatalog**。 


<a name="adapter" />

## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用位于之间的适配器控制器对象`ViewPager`和数据源 (请参阅中的插图[适配器](~/android/user-interface/controls/view-pager/index.md#adapter))。 若要访问此数据，`ViewPager`要求你提供自定义适配器派生自`PagerAdapter`。 此适配器填充每个`ViewPager`页中的数据源的内容。 由于此数据源是特定于应用程序，自定义适配器是了解如何访问数据的代码。 为通过页的用户刷`ViewPager`，适配器从数据源提取信息并将其加载到页`ViewPager`以显示。 

当实现`PagerAdapter`，必须重写以下：

-   **InstantiateItem** &ndash;创建页面 (`View`) 的给定位置并将其添加到`ViewPager`的视图集合。 

-   **DestroyItem** &ndash;从给定位置删除页。

-   **计数**&ndash;返回的数目 （页） 可用视图的只读属性。 

-   **IsViewFromObject** &ndash;确定某页是与特定的密钥对象相关联。 (此对象创建的`InstantiateItem`方法。)在此示例中，该密钥对象在`TreeCatalog`数据对象。

添加新的文件称为**TreePagerAdapter.cs**并将其内容替换为以下代码： 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

此代码引出不可或缺`PagerAdapter`实现。 在以下部分中，上述每种方法将被替换的工作代码。 


<a name="ctor" />

### <a name="implement-the-constructor"></a>实现构造函数

当应用程序的实例化`TreePagerAdapter`，它会提供上下文 ( `MainActivity`) 和实例化`TreeCatalog`。 将以下成员变量和构造函数添加到顶部`TreePagerAdapter`类**TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此构造函数的用途是存储上下文和`TreeCatalog`实例`TreePagerAdapter`将使用。 


<a name="count" />

### <a name="implement-count"></a>实现计数

`Count`实现是相对简单： 它返回树目录中的树数。 将 `Count` 替换为以下代码：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees`属性`TreeCatalog`在数据集中返回树 （页数） 数。


<a name="instantiateitem" />

### <a name="implement-instantiateitem"></a>实现 InstantiateItem

`InstantiateItem`方法创建的给定位置的页。 它必须还将添加到新创建的视图`ViewPager`的查看集合。 为了实现这一点，`ViewPager`将自身作为容器参数进行传递。 

将 `InstantiateItem` 方法替换为以下代码：

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

此代码执行以下任务：

1.  实例化一个新`ImageView`以树图像显示在指定位置。 应用程序的`MainActivity`是将传递给上下文`ImageView`构造函数。

2.  集`ImageView`资源`TreeCatalog`映像位于指定位置的资源 ID。

3.  将传入的容器的强制转换`View`到`ViewPager`引用。
    请注意，必须使用`JavaCast<ViewPager>()`正确执行 （这需要以便 Android 执行运行时检查类型转换） 此强制转换。

4.  添加实例化`ImageView`到`ViewPager`并返回`ImageView`给调用方。

当`ViewPager`显示在图像`position`，它会显示以下`ImageView`。 最初，`InstantiateItem`调用两次以填充包含视图的前两个页面。 当用户滚动时，它再次调用维护视图后面和前面的当前显示的项。 


<a name="destroyitem" />

### <a name="implement-destroyitem"></a>实现 DestroyItem

`DestroyItem`方法从给定的位置删除页。 在任意给定位置处的视图可在其中更改应用程序中`ViewPager`必须有某种方式来替换与新视图之前删除陈旧视图中的该位置。 在`TreeCatalog`示例中，每个位置处的视图不会更改，因此通过删除视图`DestroyItem`将只是重新添加时`InstantiateItem`为该位置调用。 (一个可以为更好地提高效率，实现要回收池`View`将重新显示在同一位置上的 s。) 

将 `DestroyItem` 方法替换为以下代码： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此代码执行以下任务：

1.  将传入的容器的强制转换`View`到`ViewPager`引用。

2.  传递的 Java 对象强制转换 (`view`) 到 C# `View` (`view as View`);

3.  删除从视图`ViewPager`。 


<a name="isviewfromobject" />

### <a name="implement-isviewfromobject"></a>实现 IsViewFromObject

为用户滑块向左和右页的内容，通过`ViewPager`调用`IsViewFromObject`以便确认子`View`位于给定位置是与该相同位置的适配器的对象关联 (因此，调用适配器的对象*对象键*)。 对于相对简单的应用程序，关联是一个标识&ndash;适配器的对象键在该实例是以前返回到的视图`ViewPager`通过`InstantiateItem`。 但是对于其他应用程序，可能是对象键，与关联的某些其他特定于适配器的类实例 （但不是与相同） 子查看`ViewPager`在该位置显示。 仅适配器知道传递的视图和对象键是相关联。 

`IsViewFromObject` 必须为实现`PagerAdapter`才能正常工作。 如果`IsViewFromObject`返回`false`为给定的位置，`ViewPager`将不会在该位置显示该视图。 在`TreePager`应用程序，该对象所返回键的`InstantiateItem`*是*页`View`的树中，因此该代码仅必须检查标识 （即，对象键和视图是相同的）。 将 `IsViewFromObject` 替换为以下代码： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

<a name="addadapter" />

## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

现在，`TreePagerAdapter`是实现，是时候将其添加到`ViewPager`。 在**MainActivity.cs**，将以下代码行添加到末尾`OnCreate`方法：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此代码实例化`TreePagerAdapter`，并传入`MainActivity`作为上下文 (`this`)。 实例化`TreeCatalog`传递到构造函数的第二个参数。 `ViewPager`的`Adapter`属性设置为实例化`TreePagerAdapter`对象; 此插头`TreePagerAdapter`到`ViewPager`。 

核心实现现已完成&ndash;生成并运行应用程序。 你应看到在左侧的下一步的屏幕截图中所示，屏幕上显示树目录的第一个的图像。 轻扫左以查看详细的树视图，然后右轻扫可树目录中向后移动： 

[![轻扫通过树映像的屏幕截图的 TreePager 应用](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png)


<a name="pagetabstrip" />

## <a name="add-a-pager-indicator"></a>添加一个页导航指示器

这最小`ViewPager`实现显示的图像树目录中，但它提供了没有指明用户所在的目录中。 下一步是添加`PagerTabStrip`。 `PagerTabStrip`告知哪一页显示，并通过显示的上一页和下一页页提示来提供导航上下文作为到用户。 `PagerTabStrip` 旨在用作的当前页指示器`ViewPager`; 它中滚动，并为每一页通过用户刷更新。 

打开**Resources/layout/Main.axml**并添加`PagerTabStrip`到布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` 和`PagerTabStrip`设计为协同工作。 当声明`PagerTabStrip`内`ViewPager`布局，`ViewPager`将自动查找`PagerTabStrip`并将其连接到适配器。 当生成和运行应用程序时，你应该会看到空`PagerTabStrip`显示在每个屏幕的顶部： 

[![空 PagerTabStrip 特写屏幕截图](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png)


<a name="title" />

### <a name="display-a-title"></a>显示标题

若要添加到每个页选项卡标题，实现`GetPageTitleFormatted`中的方法`PagerAdapter`-派生类。 `ViewPager` 调用`GetPageTitleFormatted`（如果实现） 来获得描述中的指定位置的页的标题字符串。 添加以下方法`TreePagerAdapter`类**TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此代码从树目录中的指定页 （位置） 会检索树标题字符串，将其转换为 Java `String`，并返回到`ViewPager`。 当你运行该应用使用此新方法时，每个页显示的树标题`PagerTabStrip`。 你应看到在未使用下划线屏幕顶部的树名称： 

[![包含文本填充 PagerTabStrip 选项卡页面的屏幕快照](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png)

你可以往下轻扫来回才能在目录中查看每个标题树图像。 


<a name="pagertitlestrip" />

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip Variation

`PagerTitleStrip` 非常类似于`PagerTabStrip`只不过`PagerTabStrip`添加当前选定的选项卡的下划线。你可以替换`PagerTabStrip`与`PagerTitleStrip`在上面的布局和运行应用以查看其外观与`PagerTitleStrip`: 

[![用下划线从文本中删除 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png)

请注意将转换时，删除下划线`PagerTitleStrip`。 


<a name="summary" />
 
## <a name="summary"></a>摘要

本演练提供如何生成一个基本的分步示例`ViewPager`-基于应用程序，而无需使用`Fragment`s。 它提供包含图像和标题字符串的示例数据源`ViewPager`布局，以显示的图像和`PagerAdapter`连接的子类`ViewPager`到数据源。 若要帮助用户可以浏览数据集，说明已包含用于解释如何添加`PagerTabStrip`或`PagerTitleStrip`要使映像标题显示在每一页的顶部。 


## <a name="related-links"></a>相关链接

- [TreePager （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
