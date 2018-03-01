---
title: "使用分段控件"
description: "本文介绍如何设计和使用在 Xamarin.tvOS 应用内的分段控件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fd31413b777e1179e7f4faf6f91f91bc6c41e82b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-segmented-controls"></a>使用分段控件

_本文介绍如何设计和使用在 Xamarin.tvOS 应用内的分段控件。_


分段控件提供了一套线性元素，其中每个图标或文本，可以包含，用于向用户提供一组相关的选项。

[ ![](segmented-controls-images/segment01.png "示例段控件")](segmented-controls-images/segment01.png)

Apple 具有以下建议用于使用分段控件：

- **提供足够的空间**-注意应为要提供其他间的空间越大花费的时间[可获得焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md)的分段控件。 处于焦点 （而不是在单击），并且用户可以意外更改段实际需要选择当前段上的另一个可获得焦点的项时，将被选中一个单独的段。
- **用于拆分视图内容筛选**-分段控件不可以合理地选择筛选为内容和筛选器之间轻松导航设计拆分视图的内容。
- **七个段最大限制**-你应尝试保留的下面八个段的最大数目 (8) 作为这是更轻松地在同一房间新床、 更轻松地导航从分析。
- **使用一致的段内容大小**-所有段都具有相同的宽度，如果可能，应尝试保留中每个段的大小相同的内容。 这不仅将使段控件更视觉效果很好，但它可以更轻松地读取一眼。
- **避免混合图标和文本**-每个单独的段可以包含一个图标或文本，但不是两者。 可以混合使用图标和相同的分段控件中的文本时，这应避免使用。

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>有关段图标

Apple 提供的建议对于段图标，如搜索放大镜使用简单、 可识别的映像。 过于复杂图标很难在同一房间，识别在电视屏幕上，因此最好来限制你图标添加到简单的表示形式。

不能混合使用文本和上给定分段的图标和应避免混合图标和单个分段控件中的文本。 它应为所有图标或所有文本。

<a name="Summary" />

## <a name="segment-text"></a>段文本

Apple 使处理段文本的以下建议：

- **使用短，有意义的名词**-段标题应明确声明的用户应会发现时选择给定的分段的内容的类型。 例如： 音乐或视频。
- **使用词首字母大写大小写**-每个词段标题应大写除文章、 连词和介词小于四 （4） 个字符。
- **使用短，已设定焦点的标题**-保留标题，简短并专注于的内容需要选中段后的类型。

同样，不能混合使用文本和上给定分段的图标和应避免混合图标和单个分段控件中的文本。

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>段控件和情节提要

Xamarin.tvOS 应用中使用的段控件的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

[[ide name="xs"]]

1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**段控件**从**工具箱**并将其放在视图上： 

    [ ![](segmented-controls-images/segment02.png "段控件")](segmented-controls-images/segment02.png)
1. 在**小组件选项卡**的**属性填充**，你可以调整段控件的几个属性，如其**样式**和**状态**: 

    [ ![](segmented-controls-images/segment03.png "小组件选项卡")](segmented-controls-images/segment03.png)
1. 使用**段**字段来控制在控制器中的段的数目。
1. 选择从给定的分段**段下拉列表中**如调整其各个属性**标题**或**映像**并控制给定的分段是否**启用**或**选定**显示控件时。
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如: 

    [ ![](segmented-controls-images/segment04.png "指定一个名称")](segmented-controls-images/segment04.png)
1. 保存更改。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**段控件**从**工具箱**并将其放在视图上： 

    [ ![](segmented-controls-images/segment02-vs.png "段控件")](segmented-controls-images/segment02-vs.png)
1. 在**小组件选项卡**的**属性资源管理器**，你可以调整段控件的几个属性，如其**样式**和**状态**: 

    [ ![](segmented-controls-images/segment03-vs.png "小组件选项卡")](segmented-controls-images/segment03-vs.png)
1. 使用**段**字段来控制在控制器中的段的数目。
1. 选择从给定的分段**段下拉列表中**如调整其各个属性**标题**或**映像**并控制给定的分段是否**启用**或**选定**显示控件时。
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如: 

    [ ![](segmented-controls-images/segment04-vs.png "指定一个名称")](segmented-controls-images/segment04-vs.png)
1. 保存更改。
    
-----

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>使用分段控件

如前所述，s 分段控件提供了一套线性元素，其中每个可以包含图标或文本，并用于向用户提供一组相关的选项。

有多种不同方法，你可以使用分段控件 Xamarin.tvOS 应用程序中。

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>作为名称和事件公开

如果在接口设计器中创建段控件和名为控件和事件作为公开它可以使用下面的代码以响应段更改：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

对于上述示例中，段控件公开为`PlayerCount`名称和一个`PlayerCountChanged`事件操作。 有关使用操作和容器的详细信息，请参阅[编写使用插座和操作代码](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)一部分我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

`SelectedSegment`属性获取或设置当前所选的段为零 (0) 基于索引。 因此如果你有五 （5） 段，第一条线段的零 (0) 和最后一个索引的索引四 （4)。

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>修改段

随时可以修改数和分段控件的内容。 使用下面的代码插入一个新图标段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二个参数定义段将使用零 (0) 基于的索引插入。 如果最后一个参数是`true`插入将进行动画处理。

若要删除给定的分段，使用以下方法：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

或以下几点，以删除所有段：

```csharp
SegmentedControl.RemoveAllSegments();
```

同样，如果最后一个参数是`true`，删除将进行动画处理。 使用`NumberOfSegments`属性返回的段的当前数目。

若要获取**标题**或**图标**对于给定分段，使用以下：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

并更改**标题**或**图标**，使用以下内容：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看给定的分段是否**已启用**，使用以下内容：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

并对其**启用/禁用**给定段，使用以下：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>修改分段的控件的外观

下面的代码可用于将给定分段的背景更改为映像：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中`UIControlState`指定设置的图像作为控件的状态：

- 普通
- 突出显示
- 已禁用
- 已选定
- 已设定焦点

和`UIBarMetrics`指定要用作的度量值：

- 默认
- 压缩
- DefaultPrompt
- CompactPrompt

此外，你可以设置使用的段之间的分隔线：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中第一个`UIControlState`指定左边的分隔线，第二段的状态`UIControlState`指定向右段的状态。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖设计和使用在 Xamarin.tvOS 应用内的分段控件。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
