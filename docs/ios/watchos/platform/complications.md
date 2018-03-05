---
title: "协调的并发数据"
description: "watchOS 允许开发人员编写自定义监视表面复杂因素"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/03/2017
ms.openlocfilehash: a13de7fbb4b6e1f9fa2853ce599f3a038a5e4040
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="complications"></a>协调的并发数据

_watchOS 允许开发人员编写自定义监视表面复杂因素_

此页介绍了不同类型的复杂性可用，以及如何向 watchOS 3 应用程序中添加一个问题。

请注意，每个 watchOS 应用程序只能有一个复杂性。

首先阅读[Apple 的文档](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)以确定你的应用程序是否适用于一个问题。 有 5`CLKComplicationFamily`类型的显示可供选择：

[ ![](complications-images/all-complications-sml.png "可用的 5 CLKComplicationFamily 类型： 循环小、 模块化小、 模块化大型、 实用小、 最有效大型")](complications-images/all-complications.png)

只是一种样式，或者所有五个，具体取决于要显示的数据，可以实现应用。
你还可以支持时程一样在用户打开数字王冠过去和/或将来的某个时间提供值。

<a name="adding" />

## <a name="adding-a-complication"></a>添加一个问题

### <a name="configuration"></a>配置

可以在创建期间，添加到监视应用程序或手动添加到现有解决方案复杂性。

### <a name="add-new-project"></a>添加新项目...

**添加新项目...**向导包括一个复选框，将自动创建了复杂性控制器类和配置**Info.plist**文件：

![](complications-images/file-new-project-sml.png "包括复杂性复选框")

### <a name="existing-projects"></a>现有项目

若要向现有项目添加一个问题：

1. 创建一个新**ComplicationController.cs**类文件和实现`CLKComplicationDataSource`。
2. 配置应用程序的**Info.plist**公开的复杂性和支持的复杂性系列的标识。

下面更详细地描述了这些步骤。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 类

下面的 C# 模板包括最小所需的方法，用于实现`CLKComplicationDataSource`。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

请按照[编写一个问题](#writing)说明将代码添加到此类。

### <a name="infoplist"></a>Info.plist

监视扩展**Info.plist**文件应指定的名称`CLKComplicationDataSource`并且您要支持的复杂性系列：

[ ![](complications-images/complications-config-sml.png "复杂性系列类型")](complications-images/complications-config.png)

**数据源类**条目列表将显示类名该子类`CLKComplicationDataSource`子类，其中包含复杂性逻辑。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

单个类，重写中的方法中实现所有复杂功能`CLKComplicationDataSource`抽象类 (该类实现`ICLKComplicationDataSource`接口)。

### <a name="required-methods"></a>所需的方法

你必须实现的复杂性，若要运行的以下方法：

- `GetPlaceholderTemplate` -返回静态显示在配置期间或在应用程序无法提供一个值时使用。
- `GetCurrentTimelineEntry` -复杂性运行时计算正确显示。
- `GetSupportedTimeTravelDirections` -返回从选项`CLKComplicationTimeTravelDirections`如`None`， `Forward`， `Backward`，或`Forward | Backward`。

### <a name="privacy"></a>隐私

显示个人数据的复杂性

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果此方法返回`HideOnLockScreen`出现的错误将显示一个图标或应用程序名称 （和没有任何数据），然后当监视处于锁定状态。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate` 返回操作系统在下一步查询的应用一次更新后的复杂性显示数据。

你还可以通过 iOS 应用程序强制更新。

### <a name="supporting-time-travel"></a>支持时程

时间旅行支持是否可选的并且由控制`GetSupportedTimeTravelDirections`方法。 如果它返回`Forward`， `Backward`，或`Forward | Backward`则必须实现以下方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>编写一个问题

从简单的数据的复杂性范围显示复杂的映像包和时程支持的数据呈现。 下面的代码演示如何生成一个简单的、 单模板问题。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>代码示例

此示例仅支持`UtilitarianLarge`模板，因此只能选择支持该类型的问题的特定监视表面上。 当*选择*复杂性在监视，它将显示**我复杂性**以及何时*运行*其显示文本**分钟_小时_**  （与时间的部分）。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>复杂性模板

对于每个复杂性样式有可用的大量不同的模板。
**环**模板，可以显示一个进度样式环解决复杂性，可以用于以图形方式显示进度或某个其他值。

[Apple 的 CLKComplicationTemplate 文档](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>循环小

这些模板类名称为前缀与`CLKComplicationTemplateCircularSmall`:

- **RingImage** -显示一幅图像，与在其周围采用进度环。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **SimpleImage** -只显示单个的小图像。
- **SimpleText** -只显示一个小的代码段的文本。
- **StackImage** -显示的映像和文本，相互行
- **StackText** -显示的文本的两个行。

### <a name="modular-small"></a>模块化小

这些模板类名称为前缀与`CLKComplicationTemplateModularSmall`:

- **ColumnsText** -显示小网格的文本值 （2 行和 2 列）。
- **RingImage** -显示一幅图像，与在其周围采用进度环。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **SimpleImage** -只显示单个的小图像。
- **SimpleText** -只显示一个小的代码段的文本。
- **StackImage** -显示的映像和文本，相互行
- **StackText** -显示的文本的两个行。

### <a name="modular-large"></a>大型模块化

这些模板类名称为前缀与`CLKComplicationTemplateModularLarge`:

- **列**-显示包含 2 列，有选择性地包括每个行左侧的映像的 3 行的网格。
- **StandardBody** -显示粗体标头字符串，使用纯文本的两个行。 标头 （可选） 可以在左侧显示图像。
- **表**-显示的粗体标头的字符串，其下的文本的 2 x 2 网格。 标头 （可选） 可以在左侧显示图像。
- **TallBody** -显示粗体标头字符串，使用更大的下方的文本的字体单个行。

### <a name="utilitarian-small"></a>最有效的小

这些模板类名称为前缀与`CLKComplicationTemplateUtilitarianSmall`:

- **平面**-在单独的行 （文本应短） 中显示的映像和一些文本。
- **RingImage** -显示一幅图像，与在其周围采用进度环。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **正方形**-显示的正方形映像 （40px 或 44px 正方形 38 mm 或 42 mm Apple Watch 分别为）。

### <a name="utilitarian-large"></a>实用大型

没有为此复杂性样式只有一个模板： `CLKComplicationTemplateUtilitarianLargeFlat`。
它显示单个映像和某些文本，所有在单独的行。



## <a name="related-links"></a>相关链接

- [Apple 的文档](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 视频](https://developer.apple.com/videos/play/wwdc2015-209/)
