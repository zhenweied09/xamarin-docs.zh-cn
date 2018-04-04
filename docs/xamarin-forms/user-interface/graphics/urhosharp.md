---
title: 使用 Xamarin.Forms 中 UrhoSharp
description: UrhoSharp 可以用于将 3D 图形添加到用于高级可视化效果的应用程序
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/11/2016
ms.openlocfilehash: 8421355e0630a637589cb4f08c2fec4ea9cdab24
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="using-urhosharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 UrhoSharp

## <a name="what-is-urhosharp"></a>什么是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md) Xamarin 和.NET 开发人员是一个功能强大的 3D 引擎。 [简介](~/graphics-games/urhosharp/introduction.md)关于 UrhoSharp 库，更详细说明了和[这些说明](~/graphics-games/urhosharp/using.md)介绍如何进行编程场景和操作。

UrhoSharp 可用来呈现在 Xamarin.Forms 应用程序中的图形。
这[示例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)演示如何 UrhoSharp 无法用于构造交互式 3D 图表：

![](urhosharp-images/ios-animation.gif "在 iOS 上 UrhoSharp 三维交互式图表")
![](urhosharp-images/android-animation.gif "UrhoSharp 三维在 Android 上的交互式图表")

## <a name="adding-the-urhosharp-nuget-packages"></a>添加 UrhoSharp Nuget 包

在使用之前 UrhoSharp，开发人员需要将 UrhoSharp Nuget 包添加到其解决方案。 本指南假定 Xamarin.Forms 项目与 iOS、 Android 和 PCL 项目。 所有代码将用 PCL 项目中;但 UrhoSharp Nuget 必须添加到 iOS 和 Android 的项目过。

UrhoSharp.Forms Nuget 包包含所有所需创建 UrhoSharp 对象的对象。 UrhoSharp.Forms nuget 包包含`UrhoSurface`类，该类用于托管 UrhoSharp Xamarin.Forms 中。
若要开始，右键单击 PCL**包**文件夹，然后选择**添加包...**.输入搜索词**UrhoSharp.Forms**，选择**Xamarin.Forms 的 UrhoSharp**，然后单击**添加包**。

[![](urhosharp-images/add-package-sml.png "添加包对话框")](urhosharp-images/add-package.png#lightbox "添加包对话框")

UrhoSharp.Forms NuGet 程序包将添加到项目中：

![](urhosharp-images/packages.png "包文件夹")

对于特定于平台的项目 （如 iOS 和 Android） 重复上述步骤。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>演练： 向 Xamarin.Forms 应用添加 UrhoSharp

这些步骤描述了 Xamarin.Forms UrhoSharp 示例中的代码：

1. [Creat Xamarin 窗体页](#1)
2. [添加 UrhoSurface](#2)
3. [生成 Urho 应用程序](#3)
4. [将图表类添加到 UrhoSurface](#4)
5. [与 UrhoSharp 交互](#5)

请注意此示例使用 C# 6 功能，并可能在较旧版本的 Visual Studio 不会编译。

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1.创建一个 Xamarin 窗体页

下面的代码显示了一个 Xamarin.Forms 页`UrhoPage`添加 Urho 相关的所有代码之前：

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2.添加 UrhoSurface

UrhoSharp 可以承载于`ContentPage`其他 Xamarin.Forms 控件一样。
显示以下代码段`UrhoSurface`添加到 Xamarin.Forms 页面：

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3.生成 Urho 应用程序

请参阅`Charts`Urho 3D 图形此示例中使用的实现的类。 显示基本代码大纲下面-请注意，该类实现`Urho.Application`即不同于`Xamarin.Forms.Application`中实现的类**App.cs**。

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

[UrhoSharp 文档](~/graphics-games/urhosharp/index.md)包含有关如何构建三维场景和操作的详细信息。

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4.将图表类添加到 UrhoSurface

使用`UrhoSurface.Show<T>`泛型方法，以添加到 Xamarin.Forms 页 Urho 应用程序。 以下代码段演示创建所需的其他代码`Charts`类：

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

注意：`Show<T>`方法是异步的应使用调用`await`关键字。

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5.与 UrhoSharp 交互

该示例允许图表条来选择和修改。 `Charts`类会公开`Bars`和`SelectedBar`若要启用此交互。

每个"栏"有一个选择事件处理程序之后添加`Charts`类呈现，通过循环访问公开`Bars`集合：

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

事件处理程序使用 Xamarin.Forms 的值`Slider`控件可调整给定条的高度：

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

最后，连接两个`Slider`控件，使其值更改时影响 UrhoSharp 画布。 第一个滑块将三维图表图像旋转和第二个的滑块来调整所选的栏的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

在动画[页面顶部](#)显示示例的运行。

## <a name="summary"></a>总结

此页显示如何使用 UrhoSharp 将 3D 数据可视化效果添加到 Xamarin.Forms。 读取[UrhoSharp 文档](~/graphics-games/urhosharp/index.md)有关如何生成可以使用上面所示的方法的 Xamarin.Forms 应用应用中包含的 Urho 场景的详细信息。


## <a name="related-links"></a>相关链接

- [图表示例 (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
