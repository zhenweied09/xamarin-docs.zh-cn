---
title: 设置了选取器的 ItemsSource 属性
description: 选取器视图是用于从数据的列表中选择文本项的控件。 此文章介绍了如何通过设置 ItemsSource 属性填充数据选取器以及如何响应用户的项选择。
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: bf3940bc1bc0318bad4d785388f9dc9292af80ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="setting-a-pickers-itemssource-property"></a>设置了选取器的 ItemsSource 属性

_选取器视图是用于从数据的列表中选择文本项的控件。此文章介绍了如何通过设置 ItemsSource 属性填充数据选取器以及如何响应用户的项选择。_

已增强，Xamarin.Forms 2.3.4 [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)视图中添加的功能，以用数据填充该通过设置其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性，并从检索选定的项[`SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性。 此外，通过设置来更改所选的项的文本的颜色[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.TextColor/)属性[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)。

## <a name="populating-a-picker-with-data"></a>填充数据选取器

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以通过设置用数据填充其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性`IList`集合。 集合中的每个项必须是的或从派生类型`object`。 可以初始化在 XAML 中添加项`ItemsSource`从项的数组的属性：

```xaml
<Picker x:Name="picker" Title="Select a monkey">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> 请注意，`x:Array`元素需要`Type`，该值指示数组中的项的类型的属性。

等效的 C# 代码所示：

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>响应项选择

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)支持选择项的一次。 当用户选择某一项， [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件激发[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性更新为一个整数，表示在列表中，所选的项的索引和[`SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性更新为`object`表示选定的项。 [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性是一个从零开始的数字，指示用户选择的项。 如果未不选定任何项，这是这种情况时[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)首次创建和初始化，`SelectedIndex`将为-1。

> [!NOTE]
> 项中的选择行为[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以上具有平台特定的 iOS 自定义。 有关详细信息，请参阅[控制选取器项选择](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)。

下面的代码示例演示如何检索[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性值从[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)在 XAML 中：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

等效的 C# 代码所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，可以是事件处理程序时执行[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件将触发：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

此方法获取[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性值和使用的值来检索的选定的项[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)集合。 这在功能上等效于检索的选定的项[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性。 请注意，在每个项`ItemsSource`集合是类型的`object`，，因此必须强制转换为`string`进行显示。

> [!NOTE]
> A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可对其进行初始化，以通过设置显示的特定项[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)或[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性。 但是，这些属性必须设置初始化后[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)集合。

## <a name="populating-a-picker-with-data-using-data-binding"></a>填充与使用数据绑定的数据选取器

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以还用数据填充，通过使用数据绑定将绑定其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性`IList`集合。 在 XAML 情况下，这实现与[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)标记扩展：

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

等效的 C# 代码所示：

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性数据绑定到`Monkeys`连接的视图模型，它返回属性`IList<Monkey>`集合。 下面的代码示例演示`Monkey`类，该类包含四个属性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

绑定到的对象的列表时[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)必须告诉要显示的每个对象的属性。 这可通过设置[ `ItemDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemDisplayBinding/)到所需的属性从每个对象的属性。 在上面的代码示例`Picker`设置以显示每个`Monkey.Name`属性值。

### <a name="responding-to-item-selection"></a>响应项选择

数据绑定可以用于将对象设置为[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性值更改时：

```xaml
<Picker Title="Select a monkey"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

等效的 C# 代码所示：

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)属性数据绑定到`SelectedMonkey`连接的视图模型，这是类型的属性`Monkey`。 因此，当用户选择中的项时，才[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)、`SelectedMonkey`属性将设置到所选`Monkey`对象。 `SelectedMonkey`通过用户界面中显示对象数据[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)视图：

![](populating-itemssource-images/monkeys.png "选取器项选择")

> [!NOTE]
> 请注意， [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)和[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性都支持双向绑定，默认情况下。

## <a name="summary"></a>总结

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)视图是用于从数据的列表中选择文本项的控件。 本文介绍了如何填充`Picker`通过设置数据[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性，以及如何响应用户的项选择。 此方法时，Xamarin.Forms 2.3.4 中引入，是与之进行交互的建议的方法`Picker`。


## <a name="related-links"></a>相关链接

- [选取器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey 应用程序 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [可绑定选取器 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [选取器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
