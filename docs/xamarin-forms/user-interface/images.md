---
title: "图像"
description: "可通过 Xamarin.Forms 平台之间共享映像、 它们可以是专门为每个平台中，加载或它们可以为显示下载。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: cddf3c58020b534abdc1541d967a9a7f5a4d891a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="images"></a>图像

_可通过 Xamarin.Forms 平台之间共享映像、 它们可以是专门为每个平台中，加载或它们可以为显示下载。_

映像是应用程序导航、 可用性和品牌的一个关键部分。 Xamarin.Forms 应用程序需要能够在所有平台之间共享映像，但也有可能在每个平台上显示不同的映像。

特定于平台的映像时也需要图标和初始屏幕;这些软件需要在每个平台基础上进行配置。

本文档讨论以下主题：

- [ **本地映像**](#Local_Images) -显示图像随应用程序，包括解决 iOS Retina 或 Android 高 DPI 版本的映像之类的本机解决方法。
- [ **嵌入图像**](#Embedded_Images) -显示图像嵌入作为程序集资源。
- [ **下载映像**](#Downloading_Images) -下载并显示图像。
- [ **图标和初始**](#Icons_and_splashscreens) -特定于平台的图标和启动映像。

## <a name="displaying-images"></a>显示图像

Xamarin.Forms 使用[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)视图以在页面上显示图像。 它具有两个重要属性：

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) -An [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)实例，文件、 Uri 或资源，设置要显示的图像。
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -如何调整它是否显示中 （无论到 stretch、 裁剪或 letterbox） 的边界内的图像的大小。

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) 可以使用每种类型的映像源的静态方法获取实例：

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -需要文件名或可解决每个平台的文件路径。
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -例如需要一个 Uri。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -需要嵌入到应用程序或 PCL 中，使用某个图像文件的资源标识符**生成操作： EmbeddedResource**。
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -需要提供图像数据的流。

[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/)属性确定将如何缩放图像以适合显示区域：

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -拉伸图像以完全和完全填充的显示区域。 这可能会导致正在扭曲的映像。
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -裁剪图像，以便它同时保留方面都填充的显示区域 (即。 无扭曲)。
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -建立信箱映像 （如果需要），以便整个图像的适合的显示区域中的空白区域添加到的上/下或侧，具体取决于是否映像是宽或过高。

可以从加载的映像[本地文件](#Local_Images_in_Xaml)、[嵌入资源](#embedded_images)，或[下载](#Downloading_Images)。

<a name="Local_Images" />

## <a name="local-images"></a>本地映像

可以添加到每个应用程序项目并从 Xamarin.Forms 共享代码引用图像文件。 跨所有应用程序，使用单个映像*必须在每个平台上使用相同的文件名*，并且它应该是有效的 Android 资源名称 (即。 允许小写字母、 数字、 下划线和段)。

- **iOS** -首选方法，以管理和支持映像，因为 iOS 9 是使用**资产目录映像集**，其中应包含的所有所需支持各种设备和规模因素的映像的版本应用程序。 有关详细信息，请参阅[将映像添加到的资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -放置在图像**可资源/绘制**目录**生成操作： AndroidResource**。 此外可以提供的映像的 DPI 于高和较低的版本 (在适当地命名为**资源**如子目录**可绘制 ldpi**，**可绘制 hdpi**，和**可绘制 xhdpi**)。
- **Windows Phone** -将图像放在应用程序的根目录下使用**生成操作： 内容**。
- **通用 Windows 平台 (UWP)** -将图像放在应用程序的根目录下使用**生成操作： 内容**。

> [!IMPORTANT]
> 在 iOS 9 之前, 映像通常放置在**资源**文件夹**生成操作： BundleResource**。 但是，使用 iOS 应用程序中的映像的此方法已被否决通过 Apple。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵循这些规则进行文件命名和放置允许以下 XAML，以加载并将该图像显示在所有平台上：

```xaml
<Image Source="waterfront.jpg" />
```

等效的 C# 代码如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

以下屏幕截图显示本地图像显示在每个平台上的结果：

[ ![本地 ImageSource](images-images/local-sml.png "示例应用程序显示本地图像")](images-images/local.png "示例应用程序显示本地图像")

为更大的灵活性`Device.RuntimePlatform`属性可用来选择一个不同的图像文件或路径部分或所有平台上，此代码示例中所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要跨所有平台使用相同的图像文件名名称必须是在所有平台上有效。 Android drawables 具有命名限制 – 允许小写字母、 数字、 下划线和句点 –，实现跨平台兼容性这必须遵循在所有其他平台上过。 示例 filename **waterfront.png**如下所示规则，但无效的文件名的示例包括"水位 front.png"，"WaterFront.png"，"水位-front.png"和"wåterfront.png"。

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>本机分辨率 （Retina 和高 DPI）

IOS 和 Android 平台包括针对不同的图像分辨率，其中操作系统选择适当的映像在运行时基于设备的功能的支持。 Xamarin.Forms 使用的本机平台的 Api 来加载本地图像，因此如果正确命名和位于项目文件，它会自动支持备用解决方法。

因为 iOS 9 管理映像的首选的方式是拖动到相应的资产目录映像集所需的每个分辨率的图像。 有关详细信息，请参阅[将映像添加到的资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前, retina 映像的版本无法放入**资源**文件夹的两个和第三次决策 **@2x** 或 **@3x** 上文件扩展名 （如之前的文件名的后缀 **myimage@2x.png**). 但是，使用 iOS 应用程序中的映像的此方法已被否决通过 Apple。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 备用分辨率图像应置于[专门名为目录](http://developer.android.com/guide/practices/screens_support.html)在 Android 项目中，如下面的屏幕截图中所示：

[![Android 的多个高分辨率图像位置](images-images/xs-highdpisolution-sml.png "Android 多个高分辨率图像位置")](images-images/xs-highdpisolution.png "Android 多个高分辨率图像位置")

### <a name="additional-controls-that-display-images"></a>显示图像的其他控件

某些控件具有显示一个映像，如的属性：

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -任何页上，派生自的键入`Page`具有[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)和[ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/)属性，可以为其指定的本地文件引用。 在某些情况下，例如，当[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)显示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，如果平台支持，将会显示图标。

  > [!IMPORTANT]
  > 在 iOS 上[ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)不能从映像中的资产目录映像集填充属性。 相反，使用负载的图标图像`Page.Icon`属性从**资源**的 iOS 项目中的文件夹。

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) -具有[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/)可以设置为本地文件引用的属性。
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) -具有[ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/)从本地文件、 嵌入的资源或 URI 检索可以设置为图像的属性。

<a name="embedded_images" />

## <a name="embedded-images"></a>嵌入图像

嵌入的图像还随应用程序 （如本地映像），但而不是让每个应用程序的文件结构映像中的一份映像文件嵌入程序集中作为资源。 与代码捆绑映像就尤其适合于创建组件，将映像分发的此方法。

要将图像嵌入到项目中，右键单击以添加新项并选择你想要添加的映像/s。 默认图像将具有**生成操作： 无**; 这需要将设置为**生成操作： EmbeddedResource**。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "设置生成操作： EmbeddedResource")

**生成操作**可以查看和更改在**属性**文件窗口。

在此示例中的资源 ID 是**WorkingWithImages.beach.jpg**。
IDE 生成此默认值通过串联**默认 Namespace**对于此项目以 filename，使用句点 （.） 之间的每个值。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "设置生成操作： EmbeddedResource")

**生成操作**还可以查看和更改在**属性**填充文件。
此填充显示**资源 ID**用于引用在代码中的资源。 下面的屏幕截图中**资源 ID**是**WorkingWithImages.beach.jpg**。
IDE 生成此默认值通过串联**默认 Namespace**对于此项目以 filename，使用句点 （.） 之间的每个值。
可以在中编辑此 ID**属性**填充，但对于这些示例值**WorkingWithImages.beach.jpg**将使用。

![](images-images/xs-embeddedproperties.png "EmbeddedResource 属性填充")

-----

如果你的项目中，你将嵌入的图像放置到文件夹，用句点 （.） 中的资源 id。 还分隔文件夹名称 移动**beach.jpg**映像到一个名为文件夹**MyImages**将导致的资源 ID **WorkingWithImages.MyImages.beach.jpg**

要加载的嵌入的图像的代码只是将传递**资源 ID**到[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)方法如下所示：

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg") };
```

当前没有资源标识符的隐式转换。 相反，你必须使用[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/)或`new ResourceImageSource()`以加载嵌入的图像。

以下屏幕快照显示的每个平台上显示的嵌入的图像的结果：

[ ![ResourceImageSource](images-images/resource-sml.png "示例应用程序显示的嵌入的图像")](images-images/resource.png "示例应用程序显示的嵌入的图像")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>使用 XAML

因为没有从没有内置类型转换器`string`到`ResourceImageSource`，这些类型的映像不能以本机方式加载的 XAML。 因此，编写简单的自定义 XAML 标记扩展以加载图像使用**资源 ID**在 XAML 中指定：

```csharp
[ContentProperty ("Source")]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }
   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source);

   return imageSource;
 }
}
```

若要使用此扩展添加自定义`xmlns`到 XAML 中，使用项目的正确的命名空间和程序集值。 然后可以使用此语法设置图像源： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 示例所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshooting-embedded-images"></a>故障排除的嵌入的图像

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>调试代码

因为它是有时难以理解为何不加载特定的图像资源，可以暂时将下面的调试代码添加到应用程序，以帮助确认资源正确配置。 它将输出嵌入到到给定程序集中的所有已知的资源<span class="UIItem">控制台</span>来帮助调试加载问题的资源。

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects-dont-appear"></a>在其他项目中嵌入图像不会显示

`Image.FromResource` 仅查找中的代码调用相同的程序集中对映像`FromResource`。 使用上面的调试代码可以确定哪些程序集包含特定的资源通过更改`typeof()`语句`Type`已知要处于每个程序集。

<a name="Downloading_Images" />

## <a name="downloading-images"></a>下载图像

下面的 XAML 中所示，可以显示，为自动下载图像：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

等效的 C# 代码如下所示：

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

[ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/)方法需要`Uri`对象，并返回一个新[ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)读取从`Uri`。

还有隐式转换为 URI 字符串，因此下面的示例也适用：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

以下屏幕快照显示的每个平台上显示的远程图像的结果：

[![下载 ImageSource](images-images/download-sml.png "示例应用程序显示已下载的映像")](images-images/download.png "示例应用程序显示已下载的映像")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>下载的映像缓存

A [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)还支持缓存的下载映像，通过以下属性配置：

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -是否启用缓存 (`true`默认情况下)。
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` ，它定义将本地存储映像的多长时间。

缓存默认处于启用状态，并将在 24 小时内的本地存储的映像。 若要禁用特定图像缓存，请实例化，如下所示图像源:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

若要设置特定缓存期 （例如，5 天） 实例化图像源，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

内置的缓存可轻松支持各种方案，例如每个单元格中滚动的映像，其中你可以设置 （或绑定） 映像的列表，并允许内置缓存负责在该单元格滚动返回到视图时重新加载映像。

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>图标和初始

虽然不相关[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)视图、 应用程序图标和初始也是使用 Xamarin.Forms 项目中的映像的一个重要用途。

设置图标和初始 Xamarin.Forms 应用可在每个应用程序项目。 这意味着正确生成大小为 iOS、 Android 和 UWP 映像。 这些映像应是名为，并且位于根据每个平台的要求。

## <a name="icons"></a>图标

请参阅[iOS 处理映像](~/ios/app-fundamentals/images-icons/index.md)， [Google 插图](http://developer.android.com/design/style/iconography.html)，和[磁贴和图标资产的准则](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)有关创建这些应用程序资源的详细信息。

## <a name="splashscreens"></a>初始

仅 iOS 和 UWP 应用程序需要初始屏幕 （也称为启动屏幕或默认映像）。

请参阅的文档[iOS 处理映像](~/ios/app-fundamentals/images-icons/index.md)和[初始屏幕](/windows/uwp/launch-resume/splash-screens/)Windows 开发人员中心上。

## <a name="summary"></a>摘要

Xamarin.Forms 提供了多种不同的方式，在跨平台应用程序，允许使用跨平台的相同图像或指定的特定于平台的映像中包括图像。 此外会自动缓存的已下载的映像，自动执行常见的编码方案。

应用程序图标和初始屏幕图像是设置和配置与非 Xamarin.Forms 应用程序-按照相同的指南用于特定于平台的应用。


## <a name="related-links"></a>相关链接

- [WorkingWithImages （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS 处理映像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 插图](http://developer.android.com/design/style/iconography.html)
- [磁贴和图标资产的的准则](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
