---
title: 使用片段 ViewPager
description: ViewPager 是使您可以实现动作导航布局管理器。 动作导航允许用户轻扫，左侧和右侧逐句通过数据页。 本指南说明如何实现使用 ViewPager，用作数据页片段 swipeable UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 83b9123b24b39e2d7bda392d05c6595f5fc3af0d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="viewpager-with-fragments"></a>使用片段 ViewPager

_ViewPager 是使您可以实现动作导航布局管理器。动作导航允许用户轻扫，左侧和右侧逐句通过数据页。本指南说明如何实现使用 ViewPager，用作数据页片段 swipeable UI。_

 
## <a name="overview"></a>概述

`ViewPager` 通常使用与片段结合使用，以便更轻松地管理在每一页的生命周期`ViewPager`。 在此演练中，`ViewPager`用于创建应用程序调用**FlashCardPager**数学问题的一系列显示在 flash 卡上。 每个 flash 卡实现为片段中。 用户刷左侧和右侧通过 flash 卡，并点击以显示其答案对数学问题。 此应用程序创建`Fragment`对每个适配器派生自的 flash 卡及实现的实例`FragmentPagerAdapter`。 在[Viewpager 和视图](~/android/user-interface/controls/view-pager/viewpager-and-views.md)，在中完成大部分工作`MainActivity`生命周期方法。 在**FlashCardPager**，将由完成大部分工作`Fragment`在其生命周期方法之一。 

本指南不涵盖的片段的基础知识&ndash;如果你尚不熟悉在 Xamarin.Android 的片段，请参阅[片段](~/android/platform/fragments/index.md)来帮助你开始使用片段。 



## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**FlashCardPager**。 接下来，启动 NuGet 包管理器 (有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 查找和安装**Xamarin.Android.Support.v4**包中所述[Viewpager 和视图](~/android/user-interface/controls/view-pager/viewpager-and-views.md)。 



## <a name="add-an-example-data-source"></a>添加示例数据源

在**FlashCardPager**，数据源是一副牌由组成 flash`FlashCardDeck`类; 此数据源提供`ViewPager`项内容。 `FlashCardDeck` 包含现成数学问题和答案的集合。 `FlashCardDeck`构造函数不需要任何参数： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

集合中的 flash 卡`FlashCardDeck`如此组织可按照索引器访问每个 flash 卡。 例如，以下代码行检索卡片组中的第四个 flash 卡问题： 

```csharp
string problem = flashCardDeck[3].Problem;
```

此代码行检索以前的问题的相应答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

因为的实现详细信息`FlashCardDeck`不了解相关`ViewPager`、`FlashCardDeck`代码未在此处列出。
源代码到`FlashCardDeck`位于[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)。
下载此源文件 (或复制并粘贴到一个新的代码**FlashCardDeck.cs**文件) 并将其添加到你的项目。



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
```

此 XML 定义`ViewPager`占用整个屏幕。 请注意，你必须使用完全限定名称**android.support.v4.view.ViewPager**因为`ViewPager`打包在支持库中。 `ViewPager` 则仅可从[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); 不可用 Android SDK 中。


## <a name="set-up-viewpager"></a>设置 ViewPager

编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

更改`MainActivity`类声明，使它派生自`AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 派生自`FragmentActivity`(而非`Activity`) 因为`FragmentActivity`知道如何管理的片段的支持。 将 `OnCreate` 方法替换为以下代码： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此代码执行以下任务：

1.  设置将视图从**main.axml**布局资源。

2.  检索到的引用`ViewPager`从布局。

3.  实例化一个新`FlashCardDeck`作为数据源。

当生成和运行此代码时，你应看到类似于以下屏幕截图显示： 

[![使用空 ViewPager 的屏幕截图的 FlashCardPager 应用程序](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此时，`ViewPager`填充因为它缺少的段的使用是空`ViewPager`，并且它用于从中的数据创建这些片段缺乏适配器**FlashCardDeck**。 

在以下部分中，`FlashCardFragment`是创建要实现的每个 flash 卡的功能和`FragmentPagerAdapter`将创建连接`ViewPager`到片段中的数据创建`FlashCardDeck`。 



## <a name="create-the-fragment"></a>创建片段

每个 flash 卡将由调用的 UI 片段`FlashCardFragment`。 `FlashCardFragment`视图将显示包含具有单个 flash 卡的信息。 每个实例`FlashCardFragment`是否由托管`ViewPager`。 
`FlashCardFragment`视图将包含的`TextView`显示 flash 卡问题文本。 此视图将实现的事件处理程序使用`Toast`以在用户点击 flash 卡问题时显示答案。 



### <a name="create-the-flashcardfragment-layout"></a>创建 FlashCardFragment 布局

之前`FlashCardFragment`可以是实现，就必须定义其布局。 此布局，则单个片段的片段容器布局。 添加到新的 Android 布局**资源/布局**调用**flashcard_layout.axml**。 打开**Resources/layout/flashcard_layout.axml**并将其内容替换为以下代码： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

此布局定义单个 flash 卡片段;每个片段组成`TextView`显示数学问题使用大型 (100sp) 字体。 此文本垂直和水平居中，flash 卡上。 



### <a name="create-the-initial-flashcardfragment-class"></a>创建初始 FlashCardFragment 类

添加新的文件称为**FlashCardFragment.cs**并将其内容替换为以下代码：

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

此代码引出不可或缺`Fragment`将用于显示 flash 卡的定义。 请注意，`FlashCardFragment`派生自支持库版`Fragment`中定义`Android.Support.V4.App.Fragment`。 构造函数为空，以便`newInstance`工厂方法用于创建一个新`FlashCardFragment`而不是一个构造函数。 

`OnCreateView`生命周期方法将创建并配置`TextView`。 它放大的片段的布局`TextView`并返回膨胀`TextView`给调用方。 `LayoutInflater` 和`ViewGroup`传递给`OnCreateView`，以便它可以放大量布局。 `savedInstanceState`捆绑包含数据的`OnCreateView`用于重新创建`TextView`从已保存状态。 

通过调用显式放大片段的视图`inflater.Inflate`。 `container`自变量是视图的父级和`false`标志指示 inflater 以避免将膨胀的视图添加到视图的父 (将添加时`ViewPager`调用的适配器的`GetItem`更高版本在此方法演练）。 



### <a name="add-state-code-to-flashcardfragment"></a>将状态代码添加到 FlashCardFragment

片段具有类似活动`Bundle`它用来保存和检索其状态。 在**FlashCardPager**，则此`Bundle`用于保存问题和答案关联 flash 卡的文本。 在**FlashCardFragment.cs**，添加以下`Bundle`到顶部的密钥`FlashCardFragment`类定义： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改`newInstance`工厂方法，以便它创建`Bundle`对象并使用上面的密钥存储传递的问题和回答片段中的文本，它实例化后： 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

修改片段生命周期方法`OnCreateView`从传入的捆绑检索此信息以及加载到的问题文本`TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

`answer`不在这里，使用变量，但它将在事件处理程序代码添加到此文件时更高版本使用。 


## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用位于之间的适配器控制器对象`ViewPager`和数据源 (请参阅的插图中 ViewPager[适配器](~/android/user-interface/controls/view-pager/index.md#adapter)文章)。 若要访问此数据，`ViewPager`要求你提供自定义适配器派生自`PagerAdapter`。 由于此示例使用片段，它使用`FragmentPagerAdapter` &ndash; `FragmentPagerAdapter`派生自`PagerAdapter`。 
`FragmentPagerAdapter` 表示为每个页`Fragment`，就会永久保留在的片段管理器，只要用户可以返回到页面。 为通过页的用户刷`ViewPager`、`FragmentPagerAdapter`从数据源提取信息并使用它创建`Fragment`s 针对`ViewPager`以显示。 

当实现`FragmentPagerAdapter`，必须重写以下：

-   **计数**&ndash;返回的数目 （页） 可用视图的只读属性。

-   **GetItem** &ndash;返回片段后，若要显示有关指定页。

添加新的文件称为**FlashCardDeckAdapter.cs**并将其内容替换为以下代码：

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

此代码引出不可或缺`FragmentPagerAdapter`实现。 在以下部分中，上述每种方法将被替换的工作代码。 构造函数的用途是将传递到的片段管理器`FlashCardDeckAdapter`的基类构造函数。 



### <a name="implement-the-adapter-constructor"></a>实现适配器构造函数

当应用程序的实例化`FlashCardDeckAdapter`，它会提供对片段管理器和实例化的引用`FlashCardDeck`。 将以下成员变量添加到顶部`FlashCardDeckAdapter`类**FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

添加以下代码的行`FlashCardDeckAdapter`构造函数： 

```csharp
this.flashCardDeck = flashCards;
```

此行代码将存储`FlashCardDeck`实例`FlashCardDeckAdapter`将使用。 



### <a name="implement-count"></a>实现计数

`Count`实现是相对简单： 它在 flash 卡片组中返回的 flash 卡数量。 将 `Count` 替换为以下代码： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards`属性`FlashCardDeck`在数据集中返回 flash 卡 （的片段数） 的数目。 



### <a name="implement-getitem"></a>实现 GetItem

`GetItem`方法返回与给定的位置相关联的片段。 当`GetItem`为调用的位置在 flash 卡片组中，它将返回`FlashCardFragment`配置为在该位置显示 flash 卡问题。 将 `GetItem` 方法替换为以下代码： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此代码执行以下任务：

1.  查找数学问题字符串`FlashCardDeck`指定位置的卡片组。 

2.  查找中的答案字符串`FlashCardDeck`指定位置的卡片组。 

3.  调用`FlashCardFragment`工厂方法`newInstance`，并传入 flash 卡问题和答案字符串。 

4.  创建并返回新 flash 卡`Fragment`，其中包含该位置的问题和答案文本。 

当`ViewPager`呈现`Fragment`在`position`，它显示`TextBox`包含驻留在的数学问题字符串`position`在 flash 卡片组。 



## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

现在，`FlashCardDeckAdapter`是实现，是时候将其添加到`ViewPager`。 在**MainActivity.cs**，将以下代码行添加到末尾`OnCreate`方法：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此代码实例化`FlashCardDeckAdapter`，并传入`SupportFragmentManager`第一个参数中。 ( `SupportFragmentManager` FragmentActivity 属性用于获取对引用`FragmentManager`&ndash;有关详细信息`FragmentManager`，请参阅[管理片段](~/android/platform/fragments/managing-fragments.md)。) 

核心实现现已完成&ndash;生成并运行应用程序。
你应看到在左侧的下一步的屏幕截图中所示，屏幕上显示的第一个图像的 flash 卡片组。 轻扫有待，请参阅详细 flash 卡，然后右轻扫可向后移动到 flash 卡片组：

[![而无需页导航指示器的 FlashCardPager 应用的示例屏幕快照](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>添加一个页导航指示器

这最小`ViewPager`实现显示每个 flash 卡在卡片组，但它提供了没有指明用户所在的卡片组内。 下一步是添加`PagerTabStrip`。 `PagerTabStrip`通知的用户有关的问题数将显示，并通过显示上一页和下一页 flash 卡的提示来提供导航上下文。 

打开**Resources/layout/Main.axml**并添加`PagerTabStrip`到布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
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

当生成和运行应用程序时，你应该会看到空`PagerTabStrip`显示在每个 flash 卡的顶部： 

[![不包含文本的 PagerTabStrip 特写](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>显示标题

若要添加到每个页选项卡标题，实现`GetPageTitleFormatted`适配器中的方法。 `ViewPager` 调用`GetPageTitleFormatted`（如果实现） 来获得描述中的指定位置的页的标题字符串。 添加以下方法`FlashCardDeckAdapter`类**FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

此代码将转换为问题数的 flash 卡片组中的位置。 生成的字符串转换为 Java`String`返回到`ViewPager`。 当你运行该应用使用此新方法时，每个页将显示中的问题号`PagerTabStrip`: 

[![屏幕快照的 FlashCardPager 与每个页面顶部显示的问题数量](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

你可以反复刷以查看显示在每个 flash 卡的顶部 flash 卡片组中的问题数量。 



## <a name="handle-user-input"></a>处理用户输入

**FlashCardPager**提供了一系列中的片段基于 flash 卡`ViewPager`，但它还没有一种方法，以显示每个问题的提示问题答案。 在此部分中，事件处理程序添加到`FlashCardFragment`用户点击 flash 卡问题文本上时显示答案。 

打开**FlashCardFragment.cs**并将下面的代码添加到末尾`OnCreateView`方法视图返回给调用方之前： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

这`Click`事件处理程序中将显示当用户点击 Toast 显示答案`TextBox`。 `answer`时传递给捆绑从中读取状态信息的更早版本初始化变量`OnCreateView`。 生成并运行应用，然后点击上每个 flash 卡以查看问题的回答的问题文本： 

[![屏幕快照的 FlashCardPager 应用 Toast 时分流数学问题](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager**本演练使用`MainActivity`派生自`FragmentActivity`，但也可以派生`MainActivity`从`AppCompatActivity`（其中还提供对支持管理片段）。 若要查看`AppCompatActivity`示例中，请参阅[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/)示例库中。 



## <a name="summary"></a>总结

本演练提供如何生成一个基本的分步示例`ViewPager`-基于应用程序使用`Fragment`s。 它提供的示例数据源包含 flash 卡问题和答案，`ViewPager`布局，以显示 flash 卡中，和一个`FragmentPagerAdapter`连接的子类`ViewPager`到数据源。 若要帮助用户可以浏览 flash 卡，说明已包含用于解释如何添加`PagerTabStrip`显示每个页顶部的问题数。 最后，添加事件处理代码是为了在用户点击对 flash 卡问题时显示答案。 



## <a name="related-links"></a>相关链接

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
