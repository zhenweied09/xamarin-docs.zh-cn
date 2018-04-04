---
title: Getting Started with DataPages
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: d06c3f95f55edb4870279bfc7d5326be4af4b3bc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-datapages"></a>Getting Started with DataPages

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!IMPORTANT]
> DataPages 需要[Xamarin.Forms 主题](~/xamarin-forms/user-interface/themes/index.md)引用来呈现。


若要开始构建使用 DataPages 预览版简单的数据驱动器页上，按照以下步骤。 在预览中的硬编码样式 （"事件"） 生成此演示使用了仅适用于特定的 JSON 格式，在代码中。

[![](get-started-images/demo-sml.png "DataPages 示例应用程序")](get-started-images/demo.png#lightbox "DataPages 示例应用程序")

## <a name="1-add-nuget-packages"></a>1.添加 NuGet 包

将这些 Nuget 包添加到你的 Xamarin.Forms PCL 和应用程序项目：

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* 主题实现 （如 Nuget Xamarin.Forms.Themes.Light)

## <a name="2-add-theme-reference"></a>2.添加主题引用

在**App.xaml**文件中，添加自定义`xmlns:mytheme`主题，并确保主题合并到应用程序的资源字典：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

**重要说明：**还应遵循的步骤[加载主题程序集 （下文）](#loadtheme)通过将某些样板文件代码添加到 iOS`AppDelegate`和 Android `MainActivity`。 这将在将来的预览版得到改进。


## <a name="3-add-a-xaml-page"></a>3.添加 XAML 页

向 Xamarin.Forms 应用程序中，添加一个新的 XAML 页面和*将基类更改*从`ContentPage`到`Xamarin.Forms.Pages.ListDataPage`。 这必须在 C# 和 XAML 中进行：

**C# 文件**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**XAML 文件**

除了更改到的根元素`<p:ListDataPage>`的自定义命名空间`xmlns:p`还必须添加：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**应用程序子类**

更改`App`类构造函数，以便`MainPage`设置为`NavigationPage`包含新`SessionDataPage`。 在导航页*必须*使用。

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3.添加数据源

删除`Content`元素并将其替换`p:ListDataPage.DataSource`来填充数据的页。 在远程 Json 下面的示例是从 URL 加载数据文件。

**注意：**预览*需要*`StyleClass`特性来为数据源提供呈现提示。 `StyleClass="Events"`在预览中预定义和包含样式的布局是指*硬编码*以匹配正在使用的 JSON 数据源。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**JSON 数据**

从 JSON 数据的一个示例[演示源](http://demo3143189.mockable.io/sessions)如下所示：

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4.运行 ！

上述步骤应导致工作数据页：

[![](get-started-images/demo-sml.png "DataPages 示例应用程序")](get-started-images/demo.png#lightbox "DataPages 示例应用程序")

这样做的原因的预建的样式**"事件"**浅色主题 Nuget 包中存在并且具有与数据源 （如匹配的样式定义 "标题"、"映像"、"演示器"）。

"事件"`StyleClass`旨在显示`ListDataPage`具有自定义控件`CardView`控制，它是在中定义的 Xamarin.Forms.Pages。 `CardView`控件具有三个属性： `ImageSource`， `Text`，和`Detail`。 主题是硬编码将绑定数据源的三个字段 （来自 JSON 文件中） 以显示这些属性。

## <a name="5-customize"></a>5.自定义

可以通过指定的模板和使用数据源绑定中重写继承的样式。 下面的 XAML 声明每个行中使用新的自定义模板`ListItemControl`和`{p:DataSourceBinding}`语法它包括在**Xamarin.Forms.Pages** Nuget:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

通过提供`DataTemplate`此代码可替代`StyleClass`，而是使用的默认布局`ListItemControl`。

[![](get-started-images/custom-sml.png "DataPages 示例应用程序")](get-started-images/custom.png#lightbox "DataPages 示例应用程序")

开发人员喜欢到使用 XAML 的 C# 可以创建数据源绑定太 (请记住包括`using Xamarin.Forms.Pages;`语句):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


它是要从头开始创建主题的稍有多个工作 (请参阅[主题指南](~/xamarin-forms/user-interface/themes/index.md)) 但将来的预览版本将使此更轻松地执行。


## <a name="troubleshooting"></a>疑难解答

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>无法加载文件或程序集 Xamarin.Forms.Theme.Light 或其依赖项之一

在预览版本中，主题可能不能在运行时加载。 添加下面显示的代码到相关项目要修复此错误。

**iOS**

在**AppDelegate.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**MainActivity.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>相关链接

- [DataPagesDemo 示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
