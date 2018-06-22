---
title: 将数据添加到了选取器的项集合
description: 选取器视图是用于从数据的列表中选择文本项的控件。 此文章介绍了如何通过将其添加到项集合填充了选取器数据以及如何响应用户的项选择。
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 63a72861895f79d2d0154297f88610ddb8bb8beb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30792649"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>将数据添加到了选取器的项集合

_选取器视图是用于从数据的列表中选择文本项的控件。此文章介绍了如何通过将其添加到项集合填充了选取器数据以及如何响应用户的项选择。_

## <a name="populating-a-picker-with-data"></a>填充数据选取器

Xamarin.Forms 2.3.4，填充的过程之前[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)数据与已将数据显示为只读的添加[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合，这属于类型`IList<string>`. 集合中的每个项的类型必须为`string`。 可以初始化在 XAML 中添加项`Items`属性的列表`x:String`项：

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

等效的 C# 代码所示：

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

除了添加数据使用`Items.Add`方法，数据还可以插入到集合使用`Items.Insert`方法。

## <a name="responding-to-item-selection"></a>响应项选择

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)支持选择项的一次。 当用户选择某一项， [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件激发和[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性更新为一个整数，表示列表中的选定项的索引。 `SelectedIndex`属性是一个从零开始的数字，指示用户选定的项的。 如果未不选定任何项，这是这种情况时`Picker`首次创建和初始化，`SelectedIndex`将为-1。

> [!NOTE]
> 项中的选择行为[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以上具有平台特定的 iOS 自定义。 有关详细信息，请参阅[控制选取器项选择](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)。

下面的代码示例演示`OnPickerSelectedIndexChanged`事件处理程序方法，即时执行[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件将触发：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

此方法获取[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性值和使用的值来检索的选定的项[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 因为每个项以`Items`集合是`string`，则可以通过显示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)而无需强制转换。

> [!NOTE]
> A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可对其进行初始化，以通过设置显示的特定项[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性。 但是，`SelectedIndex`属性必须设置初始化后[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。

## <a name="summary"></a>总结

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)视图是用于从数据的列表中选择文本项的控件。 本文介绍了如何填充`Picker`通过将其添加到的数据与[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合，以及如何响应用户的项选择。 这是使用的过程`Picker`Xamarin.Forms 2.3.4 之前。


## <a name="related-links"></a>相关链接

- [选取器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [选取器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
