---
title: ListView 交互性
description: 通过实现选择、 轻扫到 delete 和请求刷新来为你 ListView 添加交互性。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/01/2018
ms.openlocfilehash: 5fe821e7e5254da8febbbde518b9fd42526bf262
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848117"
---
# <a name="listview-interactivity"></a>ListView 交互性

ListView 支持与它可以通过以下方式提供的数据进行交互：

- [**选择和分流**](#selectiontaps) &ndash;响应分流和项的选择/取消选择。 启用或禁用行选择 （默认情况下启用）。
- [**上下文操作**](#Context_Actions) &ndash;公开功能每个项，例如，--删除轻扫。
- [**请求刷新**](#Pull_to_Refresh) &ndash;实现用户希望从本机的体验的请求刷新惯用语法。

<a name="selectiontaps" />

## <a name="selection--taps"></a>选择和 Tap
`ListView` 支持选择项的一次。 默认情况下，选择处于启用状态。 当用户点击一项时，触发两个事件：`ItemTapped`和`ItemSelected`。 请注意，两次点击相同项将不会触发多个`ItemSelected`事件，但将激发多个`ItemTapped`事件。 另请注意，`ItemSelected`如果取消选择某个项，则将调用。

请注意，`ItemSelected`则时选择将取消选择项，并且在选中时调用。 这意味着你将需要检查 null`SelectedItem`中你`ItemSelected`事件处理程序后，才能使用：

```csharp
void OnSelection (object sender, SelectedItemChangedEventArgs e)
{
  if (e.SelectedItem == null) {
    return; //ItemSelected is called on deselection, which results in SelectedItem being set to null
  }
  DisplayAlert ("Item Selected", e.SelectedItem.ToString (), "Ok");
  //((ListView)sender).SelectedItem = null; //uncomment line if you want to disable the visual selection state.
}
```

### <a name="disabling-selection"></a>禁用所选内容

如果你想要禁用所选内容，处理`ItemSelected`事件并设置`SelectedItem`为 null 的属性：

```csharp
SelectionDemoList.ItemSelected += (sender, e) => {
    ((ListView)sender).SelectedItem = null;
};
```

选择启用：

![](interactivity-images/selection-default.png "与所选内容启用的 ListView")

<a name="Context_Actions" />

## <a name="context-actions"></a>上下文操作
通常情况下，用户会想要在中的项上执行操作`ListView`。 例如，考虑在邮件应用中的电子邮件的列表。 在 iOS 上你可以往下轻扫以删除一条消息::

![](interactivity-images/context-default.png "使用上下文的操作的 ListView")

可以在 C# 和 XAML 实现上下文操作。 下面你将找到特定参考线对于两者来说，但首先让我们为看一看一些密钥实现详细信息。

使用创建上下文操作`MenuItem`s。 为由 MenuItem 本身，而不 ListView 引发点击的菜单项的事件。 这是从 tap 事件对于单元格，其中 ListView 引发事件而不是该单元格的处理方式不同。 ListView 引发事件，因为其事件处理程序是给定密钥的信息，如其中选择或点击项。

默认情况下，MenuItem 具有无法知道其所属的单元格。 `CommandParameter` 可在上找到`MenuItem`存储对象，如后面的 MenuItem ViewCell 的对象。 `CommandParameter` 可以在 XAML 和 C# 设置。

### <a name="c"></a>C#  

在任何情况下，可实现上下文操作`Cell`子类 （只要它未被使用作为组标头） 通过创建`MenuItem`s 并将它们添加到`ContextActions`单元格的集合。 可以使用以下属性可以配置为上下文操作：

* **文本**&ndash;将显示在菜单项的字符串。
* **单击**&ndash;单击项时的事件。
* **IsDestructive** &ndash; （可选） 为 true 时呈现的项是以不同方式在 iOS 上。

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

`MenuItem`s 也可以创建 XAML 集合中以声明方式。 下面的 XAML 演示自定义单元格通过实现的两个上下文操作：

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

在代码隐藏文件中，确保`Clicked`方法来实现：

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
> `NavigationPageRenderer` Android 具有可重写`UpdateMenuItemIcon`方法，可以用来从自定义加载图标`Drawable`。 此替代使能够上以图标形式使用 SVG 图像`MenuItem`在 Android 上的实例。

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>拉取到刷新
用户希望向下拉动数据的列表将刷新该列表。 `ListView` 支持此现成的-可用。 若要启用请求刷新功能，请设置`IsPullToRefreshEnabled`为 true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

提取为用户刷新请求：

![](interactivity-images/refresh-start.png "ListView 下拉以刷新正在进行中")

为用户刷新请求已发布的请求。 这是你在更新列表时用户看到的内容： ![ ] (interactivity-images/refresh-in-progress.png "ListView 拉入到完成刷新")

ListView 公开可用于对请求刷新事件作出响应的几个事件。

-  `RefreshCommand`将调用和`Refreshing`调用的事件。 `IsRefreshing` 将设置为`true`。
-  你应执行刷新列表视图中，在命令或事件中的内容所需的任何代码。
-  在刷新时已完成，请调用`EndRefresh`或设置`IsRefreshing`到`false`告诉完毕后列表视图。

`CanExecute`属性，则考虑，后者提供控制是否应启用请求刷新命令。



## <a name="related-links"></a>相关链接

- [ListView 交互性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
