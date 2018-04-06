---
title: Xamarin.Forms 多屏显示深度解析
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: f4e6dace59c14a0e30ef4cdea2c7dd490d645057
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Xamarin.Forms 多屏显示深度解析

在 [Xamarin.Forms 多屏显示快速入门](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md)中，Phoneword 应用程序扩展到包括第二个屏幕，它可跟踪应用程序的调用历史记录。 本文对已生成的内容进行回顾，以深入了解 Xamarin.Forms 应用程序中的页面导航和数据绑定。

## <a name="navigation"></a>导航

Xamarin.Forms 提供了内置导航模型，用于管理页面堆栈的导航和用户体验。 此模型实现了 `Page` 对象的后进先出 (LIFO) 堆栈。 若要从一个页面移动到另一个页面，应用程序会将新页面推送到此堆栈上。 若要返回上一页，应用程序将从堆栈弹出当前页。

Xamarin.Forms 具有 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类，用于管理 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 对象的堆栈。 `NavigationPage` 类还会将导航栏添加到页面顶部，此页面显示标题和平台相应的“返回”<span class="uiitem"></span>按钮，通过此按钮可返回上一页。 以下代码示例演示如何围绕应用程序的第一页包装 `NavigationPage`：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

所有 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 实例都具有 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 属性，可提供修改页面堆栈的方法。 应仅当应用程序包括 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 时，才调用这些方法。 若要导航到 `CallHistoryPage`，则必须调用 [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法，如下面的代码示例中所示：

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

这将导致新的 `CallHistoryPage` 对象推送到导航堆栈上。 若要以编程方式返回原始页，`CallHistoryPage` 对象必须调用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如下面的代码示例中所示：

```csharp
await Navigation.PopAsync();
```

但是，在 Phoneword 应用程序中无需此代码，因为 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类将导航栏添加到页面顶部，此页面包括平台相应的“返回”<span class="uiitem"></span>按钮，通过此按钮可返回上一页。

## <a name="data-binding"></a>数据绑定

数据绑定用于简化 Xamarin.Forms 应用程序显示及其与数据的交互方式。 它将在用户界面和基础应用程序之间建立连接。 [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) 类包含大部分基础结构以支持数据绑定。

数据绑定可定义两个对象之间的关系。 源对象提供数据。 目标对象使用（并通常显示）源对象中的数据。 在 Phoneword 应用程序中，绑定目标是显示电话号码的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件，`PhoneNumbers` 集合是绑定源。

`PhoneNumbers` 集合在 `App` 类中进行声明和初始化，如下面的代码示例中所示：

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 实例在 `CallHistoryPage` 类中进行声明和初始化，如下面的代码示例中所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

在此示例中，[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件将显示 [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/) 属性绑定到的数据的 `IEnumerable` 集合。 数据集合可以是任何类型的对象，但默认情况下，`ListView` 将使用每个项的 `ToString` 方法来显示该项。 [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) 标记扩展用于指示 `ItemsSource` 属性将绑定到 `App` 类（可在 `local` 命名空间中找到）的静态 `PhoneNumbers` 属性。

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。 若要深入了解 XAML 标记扩展，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 负责在屏幕上显示项的集合。 单个单元格中包含 `ListView` 中的每个项。 若要深入了解如何使用 `ListView` 控件，请参阅 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="summary"></a>总结

本文介绍了 Xamarin.Forms 应用程序中的页面导航和数据绑定，并演示了如何在多屏幕跨平台应用程序中使用它们。
