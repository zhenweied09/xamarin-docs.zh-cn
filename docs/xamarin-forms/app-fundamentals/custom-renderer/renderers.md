---
title: "呈现器基类，这些类和本机控件"
description: "Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。 本文列出的呈现器和实现每个 Xamarin.Forms 页、 布局、 视图和单元格的本机控件类。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 95518d9b23db68cc972549c730deeea968512444
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>呈现器基类，这些类和本机控件

_Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。本文列出的呈现器和实现每个 Xamarin.Forms 页、 布局、 视图和单元格的本机控件类。_

除`MapRenderer`类，可以在以下命名空间中找到特定于平台的呈现器：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Windows Phone 8** – Xamarin.Forms.Platform.WinPhone
- **WinRT** – Xamarin.Forms.Platform.WinRT
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

`MapRenderer`以下命名空间中找不到类：

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Windows Phone 8** – Xamarin.Forms.Maps.WP8
- **WinRT** – Xamarin.Forms.Maps.WinRT
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>页数

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[页](~/xamarin-forms/user-interface/controls/pages.md)类型：

<table>
 <thead>
   <tr>
     <td><strong>Page</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (AppCompat)</strong></td>
     <td><strong>Windows Phone 8 < / g</td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md">PageRenderer</a></td>
     <td>UIViewController</td>
     <td>分组</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a></td>
     <td><p>PhoneMasterDetailRenderer (iOS – Phone)</p><p>TabletMasterDetailPageRenderer (iOS – 平板电脑)</p><p>MasterDetailRenderer (Android)</p><p>MasterDetailPageRenderer (Android AppCompat)</p><p>MasterDetailRenderer (Windows Phone)</p><p>MasterDetailPageRenderer (WinRT)</p></td>
     <td><p>UIViewController (Phone)</p><p>UISplitViewController （平板电脑）</p></td>
     <td>DrawerLayout (v4)</td>
     <td>DrawerLayout (v4)</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement （自定义控件）</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a></td>
     <td><p>NavigationRenderer （iOS 和 Android）</p><p>NavigationPageRenderer (Android AppCompat)</p><p>NavigationPageRenderer （Windows Phone 8 和 WinRT）</p></td>
     <td>UIToolbar</td>
     <td>分组</td>
     <td>分组</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement （自定义控件）</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a></td>
     <td><p>TabbedRenderer （iOS 和 Android）</p><p>TabbedPageRenderer (Android AppCompat)</p><p>TabbedPageRenderer （Windows Phone 8 和 WinRT）</p></td>
     <td>UIView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement (Pivot)</td>
     <td>FrameworkElement (Pivot)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a></td>
     <td>PageRenderer</td>
     <td>UIViewController</td>
     <td>分组</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage<a/></td>
     <td>CarouselPageRenderer</td>
     <td>UIScrollView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement （全景）</td>
     <td>FrameworkElement (FlipView)</td>
   </tr>
 </tbody>
</table>

## <a name="layouts"></a>布局

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[布局](~/xamarin-forms/user-interface/controls/layouts.md)类型：

<table>
 <thead>
   <tr>
     <td><strong>布局</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">框架</a></td>
     <td>FrameRenderer</td>
     <td>UIView</td>
     <td>分组</td>
     <td>Border</td>
     <td>Border</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a></td>
     <td>ScrollViewRenderer</td>
     <td>UIScrollView</td>
     <td>ScrollView</td>
     <td>ScrollViewer</td>
     <td>ScrollViewer</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">网格</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>视图</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
 </tbody>
</table>

## <a name="views"></a>视图

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[视图](~/xamarin-forms/user-interface/controls/views.md)类型：

<table>
 <thead>
   <tr>
     <td><strong>视图</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (AppCompat)</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a></td>
     <td>ActivityIndicatorRenderer</td>
     <td>UIActivityIndicator</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a></td>
     <td><p>BoxRenderer （iOS 和 Android）</p><p>BoxViewRenderer (Windows Phone 和 WinRT)</p></td>
     <td>UIView</td>
     <td>分组</td>
     <td></td>
     <td>矩形</td>
     <td>矩形</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a></td>
     <td>ButtonRenderer</td>
     <td>UIButton</td>
     <td>Button</td>
     <td>AppCompatButton</td>
     <td>Button</td>
     <td>Button</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/">CarouselView</a></td>
     <td>CarouselViewRenderer</td>
     <td>UIScrollView</td>
     <td>RecyclerView</td>
     <td></td>
     <td>FlipView</td>
     <td>FlipView</td>
   </tr>   
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a></td>
     <td>DatePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>DatePicker</td>
     <td>DatePicker</td>
   </tr>
   <tr>
     <td>编辑器<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/"></a></td>
     <td>EditorRenderer</td>
     <td>UITextView</td>
     <td>EditText</td>
     <td></td>
     <td>文本框</td>
     <td>文本框</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entry</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/entry.md">EntryRenderer</a></td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>PhoneTextBox/PasswordBox</td>
     <td>文本框</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Image</a></td>
     <td>ImageRenderer</td>
     <td>UIImageView</td>
     <td>ImageView</td>
     <td></td>
     <td>图像</td>
     <td>图像</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">标签</a></td>
     <td>LabelRenderer</td>
     <td>UILabel</td>
     <td>TextView</td>
     <td></td>
     <td>TextBlock</td>
     <td>TextBlock</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/listview.md">ListViewRenderer</a></td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>LongListSelector</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/">映射</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md">MapRenderer</a></td>
     <td>MKMapView</td>
     <td>MapView</td>
     <td></td>
     <td>映射</td>
     <td>MapControl</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Picker</a></td>
     <td>PickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td>EditText</td>
     <td>FrameworkElement</td>
     <td>组合框</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">进度栏</a></td>
     <td>ProgressBarRenderer</td>
     <td>UIProgressView</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a></td>
     <td>SearchBarRenderer</td>
     <td>UISearchBar</td>
     <td>程序标签</td>
     <td></td>
     <td>PhoneTextBox</td>
     <td><p>搜索 (WinRT)</p><p>AutoSuggestBox (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">滑块</a></td>
     <td>SliderRenderer</td>
     <td>UISlider</td>
     <td>拖动条</td>
     <td></td>
     <td>Slider</td>
     <td>Slider</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Stepper</a></td>
     <td>StepperRenderer</td>
     <td>UIStepper</td>
     <td>LinearLayout</td>
     <td></td>
     <td>带有 StackPanel 边框和两个按钮</td>
     <td><p>UserControl (WinRT)</p><p>控件 (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">交换机</a></td>
     <td>SwitchRenderer</td>
     <td>UISwitch</td>
     <td>开关</td>
     <td>SwitchCompat</td>
     <td>带有 ToggleSwitchButton 边框</td>
     <td>ToggleSwitch</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a></td>
     <td>TableViewRenderer</td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>包含文本块和列表框中的网格</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a></td>
     <td>TimePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>TimePicker</td>
     <td>TimePicker</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a></td>
     <td>WebViewRenderer</td>
     <td>UIWebView</td>
     <td>WebView</td>
     <td></td>
     <td>Web 浏览器</td>
     <td>WebView</td>
   </tr>
 </tbody>
</table>

## <a name="cells"></a>单元格

下表列出的呈现器和本机控件类的实现每个 Xamarin.Forms[单元格](~/xamarin-forms/user-interface/controls/cells.md)类型：

<table>
 <thead>
   <tr>
     <td><strong>单元格</strong></td>
     <td><strong>Renderer</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a></td>
   <td>EntryCellRenderer</td>
   <td>与 UITextField UITableViewCell</td>
   <td>使用 TextView 和 EditText LinearLayout</td>
   <td>与包含一个 TextBlock 和 PhoneTextBox 网格的数据模板</td>
   <td>与文本框的数据模板</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a></td>
   <td>SwitchCellRenderer</td>
   <td>与 UISwitch UITableViewCell</td>
   <td>开关</td>
   <td>与 ToggleSwitch DataTemplate</td>
   <td>与包含的 TextBlock 和 ToggleSwitch 网格的数据模板</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a></td>
   <td>TextCellRenderer</td>
   <td>UITableViewCell</td>
   <td>使用两个 TextViews LinearLayout</td>
   <td>与包含具有两个 Textblock StackPanel 按钮 DataTemplate</td>
   <td>数据模板包含两个 Textblock StackPanel 与</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a></td>
   <td>ImageCellRenderer</td>
   <td>与 UIImage UITableViewCell</td>
   <td>使用两个 TextViews 和 ImageView LinearLayout</td>
   <td>与按钮包含一个具有一个映像，并包含两个 Textblock StackPanel 的网格的数据模板</td>
   <td>与包含的映像和两个 Textblock 网格的数据模板</td>  
   </td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/">ViewCell</a></td>
   <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md">ViewCellRenderer</a></td>
   <td>UITableViewCell</td>
   <td>视图</td>
   <td>与 ContentPresenter DataTemplate</td>
   <td>与 ContentPresenter DataTemplate</td>  
   </td>
 </tr>
 </tbody>
</table>

## <a name="summary"></a>摘要

本文已列出的呈现器和实现每个 Xamarin.Forms 页、 布局、 视图和单元格的本机控件类。 Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。



## <a name="related-links"></a>相关链接

- [自定义呈现器 （Xamarin 大学视频）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
