---
title: ListView 交互性
description: 此文章介绍了如何通过实现选择、 上下文操作和下拉刷新到 Xamarin.Forms ListView 添加交互性。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 77a48e36f33fc690306f5e590f9f4f3064fe1ddf
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202937"
---
# <a name="listview-interactivity"></a>ListView 交互性

ListView 支持与它将通过以下方法提供的数据进行交互：

- [**选择和点击**](#selectiontaps) &ndash;响应点击和项的选择/取消选择。 启用或禁用行选择 （默认情况下启用）。
- [**上下文操作**](#Context_Actions) &ndash;公开功能每个项，例如，--删除轻扫。
- [**下拉刷新**](#Pull_to_Refresh) &ndash;实现下拉刷新用语，用户都希望能从本机体验。

<a name="selectiontaps" />

## <a name="selection--taps"></a>选择和分流点

[ `ListView` ](xref:Xamarin.Forms.ListView)通过设置控制选择模式[ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode)枚举：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 指示可以将选择单个项目，而被突出显示的选定项。 这是默认值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 指示不能选择项。

当用户点击某个项时，会触发两个事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 当选择了新项时激发。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 点击某个项时触发。

> [!NOTE]
> 两次点击相同项会触发两个[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，但将只触发单个[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。

当[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可以选择[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)并[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)将不再触发事件，并且[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为所选的项的值。

当[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `None`](xref:Xamarin.Forms.ListViewSelectionMode.None)中的项[ `ListView` ](xref:Xamarin.Forms.ListView)不能选择[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)将不会触发事件，并[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性将保持`null`。 但是， [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)仍将触发事件和项目将会短暂地突出显示分流期间。

当选择项并[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性更改从[ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single)到[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None)，则[ `SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为`null`并[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件将触发与`null`项。

下面的屏幕截图演示[ `ListView` ](xref:Xamarin.Forms.ListView)与默认选择模式：

![](interactivity-images/selection-default.png "与所选内容启用的 ListView")

### <a name="disabling-selection"></a>禁用所选内容

若要禁用[ `ListView` ](xref:Xamarin.Forms.ListView)选集[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>上下文操作
通常情况下，用户将需要执行操作中的项`ListView`。 例如，请考虑在邮件应用中的电子邮件的列表。 在 iOS 上，您可以轻扫来删除一条消息::

![](interactivity-images/context-default.png "ListView 的上下文操作")

可以在 C# 和 XAML 实现上下文操作。 下面您会发现特定的指南的同时，但首先让我们看一看一些关键实现细节两个。

使用创建上下文操作`MenuItem`s。 菜单项的点击事件会引发 MenuItem 本身，而不 ListView。 这是不同于如何将点击事件处理对于 ListView 其中引发事件而不是该单元格的单元格。 因为 ListView 引发事件，其事件处理程序是给定密钥的信息，如其中选择或点击项。

默认情况下，菜单项具有无法知道它属于哪个单元格。 `CommandParameter` 可在上找到`MenuItem`来存储对象，如后面的 MenuItem ViewCell 对象。 `CommandParameter` 可以在 XAML 和 C# 中设置。

### <a name="c"></a>C#  

可以在任何实现上下文操作`Cell`子类 （只要它不被用作组标头） 通过创建`MenuItem`s 并将它们添加到`ContextActions`单元格的集合。 具有以下属性可配置的上下文操作：

* **文本**&ndash;菜单项中显示的字符串。
* **单击**&ndash;时单击项的事件。
* **IsDestructive** &ndash; （可选） 为 true 时呈现的项是以不同的方式在 iOS 上。

多个上下文操作可以添加到单元格，，但是只有一个应有`IsDestructive`设置为`true`。 下面的代码演示如何上下文操作将添加到`ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s 也可以创建 XAML 集合中以声明方式。 以下 XAML 演示自定义单元格包含两个实现的上下文操作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
               Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

在代码隐藏文件中，确保`Clicked`方法实现：

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer`适用于 Android 可重写`UpdateMenuItemIcon`方法，可以用来从自定义加载图标`Drawable`。 此重写就可以使用 SVG 图像以图标形式在`MenuItem`Android 上的实例。

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>下拉以刷新
用户都希望能拉下在列表中的数据将刷新该列表。 `ListView` 支持此--现成的。 若要启用下拉刷新功能，请设置`IsPullToRefreshEnabled`为 true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

拉取请求以刷新以用户身份：

![](interactivity-images/refresh-start.png "ListView 下拉以刷新正在进行中")

以用户身份的请求以刷新已发布的请求。 这是用户看到同时正在更新列表： ![](interactivity-images/refresh-in-progress.png "ListView 拉取到完成刷新")

ListView 公开了几个事件，可用于对请求刷新事件做出响应。

-  `RefreshCommand`将调用和`Refreshing`调用的事件。 `IsRefreshing` 将设置为`true`。
-  您应该执行刷新列表视图中，在命令或事件中的内容所需的任何代码。
-  刷新时已完成，请调用`EndRefresh`，或者设置`IsRefreshing`到`false`告诉完成列表视图。

`CanExecute`属性，则考虑，后者可提供一种方法来控制是否应启用下拉刷新命令。



## <a name="related-links"></a>相关链接

- [ListView 交互性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
