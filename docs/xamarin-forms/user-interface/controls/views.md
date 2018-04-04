---
title: Xamarin.Forms 视图
description: Xamarin.Forms 视图是跨平台移动用户界面的构建基块。
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: b690b4aed315a78d95d9e0e7444257f0fc2c4a78
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-views"></a>Xamarin.Forms 视图

_Xamarin.Forms 视图是跨平台移动用户界面的构建基块。_

视图是用户界面对象，如标签、 按钮和滑块通常称为*控件*或*小组件*其他图形的编程环境中。 Xamarin.Forms 都派生自支持的视图[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类。 它们可以划分为多个类别：

## <a name="views-for-presentation"></a>演示文稿的视图

### <a name="label"></a>Label

|     |     |
| --- | --- |
| [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 显示的单行文本字符串或多行文本块的使用常量或变量格式设置。 设置[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)为用于常量的格式设置，或组的字符串属性[ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/)属性[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)对象变量格式设置。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) / [指南](~/xamarin-forms/user-interface/text/label.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![标签示例](views-images/Label.png "标签示例")](views-images/Label-Large.png#lightbox "标签示例")<br /> [C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>图像

|     |     |
| --- | --- |
| [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 显示位图。 可以通过作为资源嵌入到常见项目或平台项目中，或使用.NET 创建的 Web 下载位图`Stream`对象。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) / [指南](~/xamarin-forms/user-interface/images.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![图像示例](views-images/Image.png "图像示例")](views-images/Image-Large.png#lightbox "图像示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>字数

|     |    |
| --- | ---|
| [`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) 显示颜色着色，由一个实心矩形[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)属性。 `BoxView` 有 40 x 40 个的默认大小请求。 有关其他的大小，将分配[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) / [指南](~/xamarin-forms/user-interface/boxview.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)， [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)， [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)， [4](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)， [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)，和[6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![字数示例](views-images/BoxView.png "字数示例")](views-images/BoxView-Large.png#lightbox "字数示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) 显示网页或 HTML 内容，基于是否[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)属性设置为[ `UriWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UrlWebViewSource/)或[ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/)对象。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) / [指南](~/xamarin-forms/user-interface/webview.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)和[2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![WebView 示例](views-images/WebView.png "WebView 示例")](views-images/WebView-Large.png#lightbox "WebView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/) 在 iOS 和 Android 的项目显示 OpenGL 图形。 没有为通用 Windows 平台支持。 IOS 和 Android 项目需要引用**OpenTK 1.0**程序集或**OpenTK**版本 1.0.0.0 的程序集。 `OpenGLView` 可以更轻松地使用在共享项目; 中如果在 PCL 或标准.NET 库中使用，然后依赖服务还需要 （如示例代码所示）。<br /><br />这是唯一的图形工具的内置于 Xamarin.Forms，但 Xamarin.Forms 应用程序也可呈现图形使用[ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md)， [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)，或[ `UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/)<br /><br /> | [![OpenGLView 示例](views-images/OpenGLView.png "OpenGLView 示例")](views-images/OpenGLView-Large.png#lightbox "OpenGLView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>映射

|     |     |
| --- | --- |
| [`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) 显示的映射。 **Xamarin.Forms.Maps**必须安装 Nuget 包。 Android 和通用 Windows 平台需要映射授权密钥。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) / [指南](~/xamarin-forms/user-interface/map.md) / [示例](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![映射示例](views-images/Map.png "映射示例")](views-images/Map-Large.png#lightbox "映射示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>启动命令的视图

### <a name="button"></a>Button

|     |     |
| --- | --- |
| [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 是一个矩形的对象，显示文本，并触发[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/)时已经按下的事件。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) | [![按钮示例](views-images/Button.png "按钮示例")](views-images/Button-Large.png#lightbox "按钮示例")<br /> [C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 显示该用户发出信号，应用程序执行的搜索的类型的文本字符串，和一个按钮 （或键盘键） 的区域。 [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.Text/)属性提供对该文本，访问和[ `SearchButtonPressed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)事件指示按下了按钮。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) | [![SearchBar 示例](views-images/SearchBar.png "SearchBar 示例")](views-images/SearchBar-Large.png#lightbox "SearchBar 示例")<br /> [C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>对于设置值的视图 

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) 允许用户选择`double`从指定的连续范围的值[ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/)和[ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) / [指南](~/xamarin-forms/user-interface/slider.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) | [![滑块示例](views-images/Slider.png "滑块示例")](views-images/Slider-Large.png#lightbox "滑块示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>分档器

|     |     |
| --- | --- |
| [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) 允许用户选择`double`来自一个使用指定的增量值的范围值[ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Minimum/)， [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Maximum/)，和[ `Increment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) | [![分档器示例](views-images/Stepper.png "分档器示例")](views-images/Stepper-Large.png#lightbox "分档器示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>开关 

|     |     |
| --- | --- |
| [`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) 采用打开/关闭开关以允许用户选择一个布尔值的形式。 [ `IsToggled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/)属性是该交换机的状态和[ `Toggled` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/)的状态更改时激发事件。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) | [![切换示例](views-images/Switch.png "切换示例")](views-images/Switch-Large.png#lightbox "切换示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) 允许用户与平台日期选取器选择一个日期。 设置与允许的日期范围[ `MinimumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/)和[ `MaximumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/)属性。 [ `Date` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/)属性是所选的日期和[ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/)该属性变化时触发事件。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) / [指南](~/xamarin-forms/user-interface/datepicker.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) | [![包含 DatePicker 示例](views-images/DatePicker.png "DatePicker 示例")](views-images/DatePicker-Large.png#lightbox "DatePicker 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) 允许用户与平台时间选取器选择的时间。 [ `Time` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/)属性是选定的时间。 应用程序可以监视中的更改`Time`通过安装的处理程序的属性[ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/)事件。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) | [![TimePicker 示例](views-images/TimePicker.png "TimePicker 示例")](views-images/TimePicker-Large.png#lightbox "TimePicker 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>编辑文本的视图

这两个类派生自[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)类，该类定义[ `Keyboard` ](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/)属性。

<a name="entry" />

### <a name="entry"></a>条目

|     |     |
| --- | --- |
| [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 允许用户输入和编辑单个文本行。 文本是可用作[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/)属性，与[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)和[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)时触发事件的文本更改或用户通过轻按 enter 键，向发出完成信号。<br /><br />使用[ `Editor` ](#editor)用于输入和编辑多行文本。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) / [指南](~/xamarin-forms/user-interface/text/entry.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![条目示例](views-images/Entry.png "条目示例")](views-images/Entry-Large.png#lightbox "条目示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>编辑器

|     |     |
| --- | --- |
| [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) 允许用户输入和编辑多行文本。 文本是可用作[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Editor.Text/)属性，与[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/)和[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/)时触发事件的文本更改或用户用信号通知完成。<br /><br />使用[ `Entry` ](#entry)输入和编辑单个文本行的视图。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) / [指南](~/xamarin-forms/user-interface/text/editor.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![条目示例](views-images/Editor.png "编辑器示例")](views-images/Editor-Large.png#lightbox "编辑器示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>视图，以指示活动

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) 使用动画显示应用程序在活动中参与而无需授予任何指示的进度。 [ `IsRunning` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)属性控制动画。<br /><br />如果已知的活动的进度，使用[ `ProgressBar` ](#progressbar)相反。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) | [![ActivityIndicator 示例](views-images/ActivityIndicator.png "ActivityIndicator 示例")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) 使用动画显示，应用程序时通过在活动的进展情况。 设置[ `Progress` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ProgressBar.Progress/)值介于 0 和 1 以指示进度的属性。<br /><br />如果不知道活动的进度，使用[ `ActivityIndicator` ](#activityindicator)相反。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) | [![ProgressBar 示例](views-images/ProgressBar.png "ProgressBar 示例")](views-images/ProgressBar-Large.png#lightbox "ProgressBar 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>显示集合的视图

### <a name="picker"></a>选取器

|     |     |
| --- | --- |
| [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) 显示所选的项，可从列表的文本字符串，并允许您选择该项，点击该视图时。 设置[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)属性列表的字符串，或[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性的对象的集合。 [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件激发时选择项。<br /><br />`Picker`仅选择此项时显示的项的列表。 使用[ `ListView` ](#listView)或[ `TableView` ](#tableView)有关一直处于页面的可滚动列表。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) / [指南](~/xamarin-forms/user-interface/picker/index.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![选取器示例](views-images/Picker.png "选取器示例")](views-images/Picker-Large.png#lightbox "选取器示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 派生自[ `ItemsView[Cell]` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) ，并显示可选择数据项的可滚动列表。 设置[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)属性的对象，并设置集合[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)属性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)描述项在哪些对象要设置格式。 [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)事件表示是否已为可进行选择[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) / [指南](~/xamarin-forms/user-interface/listview/index.md) / [示例](https://developer.xamarin.com/samples/WorkingWithListview) | [![ListView 示例](views-images/ListView.png "ListView 示例")](views-images/ListView-Large.png#lightbox "ListView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) 显示的类型的行列表[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)具有可选标题和副标题。 设置[ `Root` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/)类型的对象属性[ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)，并添加[ `TableSection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/)对象与`TableRoot`。 每个`TableSection`是一套`Cell`对象。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) / [指南](~/xamarin-forms/user-interface/tableview.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![TableView 示例](views-images/TableView.png "TableView 示例")](views-images/TableView-Large.png#lightbox "TableView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/root/Xamarin.Forms/)
