---
title: ActionBar
ms.topic: article
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 64a5ac7e0c448205da66f9790a506ca34a944140
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>概述

使用时`TabActivity`，用于创建选项卡图标的代码不起作用针对 Android 4.0 框架运行时。 尽管它功能上操作中的 Android 2.3 之前, 的版本与`TabActivity`类本身 4.0 中已弃用。 创建选项卡式的界面的新方法引入了使用操作栏中，接下来，我们将讨论。


## <a name="action-bar-tabs"></a>操作栏选项卡

操作栏包括以 Android 4.0 添加选项卡式的接口的支持。
以下屏幕截图显示此类接口的示例。

[![在仿真程序; 中运行的应用的屏幕快照显示两个选项卡](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要在操作栏中创建选项卡，我们首先需要设置其`NavigationMode`属性以支持选项卡。 在 Android 4 中，`ActionBar`属性是可在活动类，我们可以用来设置上`NavigationMode`如下所示：

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成此操作后，我们可以通过调用创建选项卡`NewTab`操作栏上的方法。 与此选项卡上实例中，我们就可以调用`SetText`和`SetIcon`方法，以便设置选项卡上的标签文本和图标; 下面显示的代码中的顺序进行这些调用：

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

但是，我们可以添加该选项卡之前，我们需要处理`TabSelected`事件。 在此处理程序，我们可以创建选项卡上的内容。操作栏选项卡用于处理*片段*，表示活动中的用户界面的一部分的类。 对于此示例中，片段的视图包含单个`TextView`，其中我们放大量中我们`Fragment`子类如下：

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);
       
        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

事件自变量传入`TabSelected`事件属于类型`TabEventArgs`，其中包括`FragmentTransaction`我们可以使用添加片段，如下所示的属性：

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最后，我们可以将标签添加到操作栏通过调用`AddTab`方法，此代码中所示：

```csharp
this.ActionBar.AddTab (tab);
```

有关完整示例，请参阅*HelloTabsICS*本文档的示例代码中的项目。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`类启用要发生的操作栏从共享操作。 它负责使用的应用程序可以处理共享意向，并且从操作栏更高版本与其保留轻松访问的以前用过应用程序历史记录的列表中创建的操作视图。 这允许应用程序通过在 Android 保持一致的用户体验共享数据。


### <a name="image-sharing-example"></a>图像共享示例

例如，下面是与菜单项操作栏，要共享映像文件的屏幕快照 (摘自[ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)示例)。 当用户点击动作栏上的菜单项时，ShareActionProvider 加载的应用程序能够处理与关联为打算`ShareActionProvider`。 在此示例中，消息处理应用程序具有已以前用于，因此它显示在操作栏上。

[![消息传递操作栏中的应用程序图标的屏幕截图](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


当用户单击操作栏中的项上时，消息包含共享的映像启动应用时，如下所示：

[![显示 monkey 图像的消息传递应用的屏幕快照](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>指定的操作提供程序类

若要使用`ShareActionProvider`，将其设置`android:actionProviderClass`属性中的 XML 操作栏的菜单的菜单项，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Inflating 菜单

若要放大量菜单上，我们重写`OnCreateOptionsMenu`中活动子类。 一旦我们有了对菜单上的引用，我们可以获取`ShareActionProvider`从`ActionProvider`属性菜单项，然后使用 SetShareIntent 方法以设置`ShareActionProvider`的意图，如下所示：

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       
           
    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>创建意图

`ShareActionProvider`将使用的意图，传递给`SetShareIntent`在上面的代码，以启动适当的活动的方法。 在这种情况下，我们创建试图通过使用下面的代码发送图像：

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

上面的代码示例中的图像是包括在作为对资产进行应用程序，并复制到可公开访问的位置创建活动时，这样，它可供其他应用程序，例如，消息传送应用程序。 本文附带的示例代码包含此示例中，说明其用法的完整源代码。



## <a name="related-links"></a>相关链接

- [Hello 选项卡 ICS （示例）](https://developer.xamarin.com/samples/HelloTabsICS/)
- [ShareActionProvider 演示 （示例）](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
