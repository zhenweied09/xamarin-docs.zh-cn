---
title: 呈现器基类和本机控件
description: 每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。 本文列出了呈现器和实现每个 Xamarin.Forms 页面、 布局、 视图和单元格的本机控件类。
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 01610581a0fd72401cb6daa4d5c8c2cb99fe6a2f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995103"
---
# <a name="renderer-base-classes-and-native-controls"></a>呈现器基类和本机控件

_每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。本文列出了呈现器和实现每个 Xamarin.Forms 页面、 布局、 视图和单元格的本机控件类。_

除`MapRenderer`类，可以在以下命名空间中找到特定于平台的呈现器：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer`类可在以下命名空间：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>页数

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[页](~/xamarin-forms/user-interface/controls/pages.md)类型：

|页|呈现器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|视图分组||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – 电话)、 TabletMasterDetailPageRenderer (iOS – 平板电脑)、 MasterDetailRenderer (Android)、 MasterDetailPageRenderer (Android AppCompat)、 MasterDetailPageRenderer (UWP)|UIViewController (Phone) UISplitViewController （平板电脑）|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement （自定义控件）|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer （iOS 和 Android）、 NavigationPageRenderer (Android AppCompat)、 NavigationPageRenderer (UWP)|UIToolbar|视图分组|视图分组|FrameworkElement （自定义控件）|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer （iOS 和 Android）、 TabbedPageRenderer (Android AppCompat)、 TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement （转动）|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|视图分组||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>布局

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[布局](~/xamarin-forms/user-interface/controls/layouts.md)类型：

|布局|呈现器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|视图|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|视图|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|视图分组|Border|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|视图|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|视图|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|视图|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|视图|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|视图|FrameworkElement|

## <a name="views"></a>视图

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[视图](~/xamarin-forms/user-interface/controls/views.md)类型：

|视图|呈现器|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer （iOS 和 Android）、 BoxViewRenderer (UWP)|UIView|视图分组||矩形|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Button|AppCompatButton|Button|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||文本框|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||文本框|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||图像|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|组合框|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|程序标签||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|也||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||控件|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|开关|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|Web 视图||Web 视图|

## <a name="cells"></a>单元格

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[单元格](~/xamarin-forms/user-interface/controls/cells.md)类型：

|单元格|呈现器|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|使用 UITextField UITableViewCell|TextView 和 EditText LinearLayout|带有文本框的 DataTemplate|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|使用 UISwitch UITableViewCell|开关|使用网格包含 TextBlock 和 ToggleSwitch 的 DataTemplate|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|使用两个 TextViews LinearLayout|使用包含两个 Textblock StackPanel 的 DataTemplate|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|使用 UIImage UITableViewCell|使用两个 TextViews 和 ImageView LinearLayout|使用网格包含图像和两个 Textblock 的 DataTemplate|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|视图|用 ContentPresenter 的 DataTemplate|

## <a name="summary"></a>总结

本文已列出的呈现器和本机控件类的实现每个 Xamarin.Forms 页面、 布局、 视图和单元格。 每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。

## <a name="related-links"></a>相关链接

- [自定义呈现器 （Xamarin University 视频）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
