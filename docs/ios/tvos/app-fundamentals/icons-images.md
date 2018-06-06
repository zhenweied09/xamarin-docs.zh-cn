---
title: 使用 tvOS 图标和 Xamarin 的图像
description: 本文档介绍如何处理图标和中使用 Xamarin 生成的 tvOS 应用的映像。 它讨论了启动映像、 分层的映像、 应用程序图标，和的详细信息。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 59cbc53acf3ab7da12826b9d3cffb821631a0500
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788792"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>使用 tvOS 图标和 Xamarin 的图像

创建迷人视觉图标和图像是开发你的 Apple TV 应用的沉浸式用户体验的关键部分。 本指南将介绍创建和包括 Xamarin.tvOS 应用的必要图形资产所需的步骤：

- [启动映像](#Launch-Image)-时你的应用程序首次启动和完成启动后由应用程序的第一个屏幕替换显示的启动映像。
- [分层映像](#Layered-Images)-特定于 Apple TV，Apple 的新的分层映像工作，而与要创建选定项的三维效果的视差效果。 有多种到[创建分层映像](#Creating-Layered-Images)。
- [应用程序图标](#App-Icons)-图标是必需的不仅 Apple TV 主页屏幕，但对于应用商店。 因此必须提供为分层的图像。
- [排名靠前的架映像](#Top-Shelf-Image)-如果你的应用程序放置在主页屏幕的顶部行上，它将需要一个顶层架子映像，以突出显示你应用的功能。 或者，您可以提供[动态顶部架内容](#Dynamic-Top-Shelf-Content)以突出显示你的应用程序中的内容。
- [游戏中心映像](#Game-Center-Images)-如果你的应用程序是一个游戏，并使用 Game Center，都会要求提供多个其他映像。
- [设置 Xamarin.tvOS 项目图像](#Setting-Xamarin.tvOS-Project-Images)-介绍如何设置的启动映像和应用程序图标为 Xamarin.tvOS 应用程序所需的步骤。

> [!IMPORTANT]
> Apple TV 上的所有映像都位于 1 x 分辨率 (`@1x`)，你应_仅_使用此大小的图像。 包括更大，较高分辨率的图形不仅需要时间来下载和使用更多内存和存储，但它们必须在运行时将重新动态缩放，并且将对绘图的性能造成负面影响。

<a name="Launch-Image" />

## <a name="launch-image"></a>启动映像

启动映像是在 Apple TV，最初启动 Xamarin.tvOS 应用时，将显示的第一个操作，并且在这种情况下，每个 tvOS 应用必须提供启动映像。 

启动映像将快速显示，并提供你的应用程序是快速且高度可响应这样的印象。 Apple TV 将启动映像使用替换你的应用程序的第一个屏幕很快存在之后。

启动映像不是有机会广告或艺术表达式，它们存在仅仅是为了给出您的应用程序快速启动，并已准备的印象使用。

|启动映像的大小|说明|
|---|---|
|1920x1080px|仅非分层.png 文件|

Apple 使设计您的应用程序的启动映像的以下建议：

- **几乎与第一个屏幕的相同**-你的启动屏幕应接近您的应用程序尽可能的第一个屏幕。 包括不同的图形或元素可能导致令人讨厌的"闪存"在第一个屏幕出现时。
- **避免使用文本**的启动映像是静态的在这种情况下，将不会本地化在显示之前。
- **Downplay 启动**-因为 Apple TV 用户经常切换应用，你不应吸引人们关注应用程序启动进程。
- **任何广告或品牌**-你的启动映像不能用作关于屏幕或包括任何品牌，除非它是静态的应用程序的第一个屏幕的一部分。 严格禁止广告。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>设置启动映像

若要设置为你 tvOS 的项目的启动映像，请执行以下操作：

1. 在**解决方案资源管理器**，双击`Assets.xcassets`以将其打开以进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets 文件")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**，单击`LaunchImages`资产： 

    [![](icons-images-images/asset02.png "LaunchImages 资产")](icons-images-images/asset02.png#lightbox)
3. 单击**1 x Apple TV**条目和选择启动映像，或有选择地从文件系统拖动的新的映像： 

    [![](icons-images-images/asset03.png "选择启动映像")](icons-images-images/asset03.png#lightbox)
4. 保存更改。

<a name="Layered-Images" />

## <a name="layered-images"></a>分层的图像

新 Apple TV，视差产生的效果，以达到 3D 效果，以帮助使用户保持新床心理相连屏幕上的内容，在同一房间的分层映像工作。

分层的映像包含从两个 （2） 到五 （5） 单独的合并以形成完成映像的层。 除了背景层中，每个图层使用透明度以及其 Z 顺序来创建这样的深度的错觉。 当用户交互使用分层映像时，较高的 Z 排序层缩放和平重叠的要创建此效果。

[![](icons-images-images/layered01.png "分层的映像 Z 排序关系图")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 分层的映像所需的应用程序的图标和都是可选的其他[可获得焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（如顶部架映像）。 但是，Apple 提供的建议分层映像用于在你的应用程序可能会获得焦点的任何映像。




Apple 使设计分层映像以下建议：

- **使背景层不透明**-你背景层 （第 1 层）**必须**是不透明或当你尝试在 Apple TV 上使用分层映像时，将收到错误。 所有其他层可以包含多个级别的透明度以增强 3D 效果。
- **隔离前景、 中间和后台元素**-将突出显示元素 （如游戏字符为单位） 放在前台，用于辅助元素或阴影的中间。 最后，包括非特定背景为你的上层提供一个阶段。
- **保持在前台文本**-除非你想要被遮盖的更高级别的你文本，通常它应为最顶层的层上。
- **使用简单分层**-视差影响的设计目标是细微因此请将您的层保留到最小，以防止快、 不现实的效果。
- **包括安全区域**-由于较高的层可以裁剪视差效果期间，你需要先将内置于每个层的安全区域边框。 如果您将获得你内容得太近层边缘，它可以成为裁剪。 多个缩放，裁剪比较低的层，将会经历较高的层。 请参阅[大小调整图像图层](#Sizing-Image-Layers)下面一节。
- **预览通常**-应预览分层映像通常以确保所需的 3D 效果发生，并且无各个层上的内容进行裁剪。 您应预览分层映像 Apple TV 的真实硬件上以确保它们呈现为预期的方式。

只要有可能，应始终使用内置`UIKit`控件以显示你分层的映像，因为它们会自动收到视差效果时它们进入焦点。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>大小调整图像图层

务必请记住包括_安全区域_到每个层组成分层图像的边框。 因为可以缩放和剪裁视差效果期间单个层，将可以关闭裁剪图层的内容，如果它是层的边缘离得太近：

[![](icons-images-images/layered02.png "35 像素宽的边框")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>创建分层映像

tvOS 配合分层映像，采用以下格式：

- **汽车文件**-这是由 Apple 的专有资产目录格式。 不会直接创建汽车文件，它们是在编译时根据文件创建的任何 LSR，包含在应用捆绑包。
- **LSR 映像**-这是由 Apple 的专有映像格式。 使用[视差导出程序 Adobe Photoshop 插件](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[视差预览程序](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)创建和 LSR 格式预览分层图像。
- **Assets.xcassets** -从两个 （2） 到五 （5） 标准`.png`格式化将编译到辆 LSR 资产目录中所包含的图像格式在编译时的分层映像。
- **LCR 文件**-这是由 Apple 的专有文件格式。 LCR 文件旨在用作从你的内容服务器下载的其他内容。 LCR 文件应永远不会包含在应用捆绑包。 从使用 LSR 或 Photoshop 文件生成 LCR 文件`layerutil`Xcode 中包含的命令行工具。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>视差预览程序

创建 Apple[视差预览程序](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)预览并创建所需的应用程序图标和可选可获得焦点的项的分层映像。 预览程序显示窗体已完成的分层映像的每一层：

[![](icons-images-images/layered03.png "视差预览程序")](icons-images-images/layered03.png#lightbox)

时预览分层的映像，你可以使用鼠标来旋转图像和预览视差效果。 使用**+** （加） 和**-** （减号） 按钮来添加和删除层。

创建新的分层映像时，它可以是采用 LSR 格式导出并且包含在您的应用程序捆绑包。

有关创建和预览分层映像的详细信息，请参阅 Apple 的[创建视差图稿](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)部分[应用对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)。

<a name="App-Icons" />

## <a name="app-icons"></a>应用程序图标

Xamarin.tvOS 应用程序将需要不只一个应用程序图标的 Apple 电视主页屏幕，但还应用商店的图标。 应用程序图标是你的第一个更改以使极佳印象在潜在用户上，应传达一眼的应用程序的用途。

[![](icons-images-images/icon01.png "应用程序图标")](icons-images-images/icon01.png#lightbox)

每个应用必须提供一个较小和其应用程序图标的大型版本。 安装应用时，将在 Apple 电视主页屏幕上使用的小图标。 应用商店使用的大型的版本。 大的应用程序图标应模拟的小图标版本的外观和感觉。

|小图标||大图标||
|---|---|---|---|
|实际大小|400x240px|大小|1280x768px|
|安全区域大小|370x222px|||
|失去焦点的大小|300x180px|||
|已设定焦点的大小|370x222px|||

> [!IMPORTANT]
> 必须作为提供您的应用程序图标**分层映像**。 请参阅[分层映像](#Layered-Images)上面部分以了解更多详细信息。




Apple 提供用于创建应用程序图标以下建议：

- **提供一个单焦点点**– 设计您的图标与单个焦点点直接置于图标的中心。
- **使用简单后台**– 简化图标背景，以便上层突出显示。请考虑使用一种简单的颜色或细微渐变。
- **将文本量限制**– 因为时用户选择，应用程序的名称将出现在下方的图标，你只应包括文本至关重要的图标的设计时。
- **不要使用屏幕截图**-屏幕截图对于图标而言过于复杂，但不允许用户查看你一眼的应用程序的用途。
- **保持图标正方形**– tvOS 会自动应用稍为圆角的图标的掩码。 不包含这种舍入自己。
- **仔细你层分隔**– 文本应该上限，大多数层、 中间和允许你上层表现中性后台辅助项。
- **使用渐变和阴影仔细**– 渐变和阴影可以冲突视差效果与因此应谨慎使用。 简单上到下，明暗渐变样式工作效率最佳。 阴影最尖锐、 硬色调作为正常工作。
- **不同层的透明度**– 使用的各种级别的透明度在你的应用程序图标，以增加 3D 效果的较高级别上。 背景层必须是不透明，或者它将导致错误。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>设置应用程序图标

若要设置 tvOS 项目所需应用程序图标，请执行以下操作：

1. 在**解决方案资源管理器**，双击`Assets.xcassets`以将其打开以进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**，展开`App Icon & Top Shelf Image`资产： 

    [![](icons-images-images/asset04.png "展开顶部架图像资产")](icons-images-images/asset04.png#lightbox)
3. 接下来，展开`App Icon - Small`资产： 

    [![](icons-images-images/asset05.png "展开应用图标-小资产")](icons-images-images/asset05.png#lightbox)
4. 然后展开`Back`资产，然后单击`Contents`条目： 

    [![](icons-images-images/asset06.png "然后展开后资产")](icons-images-images/asset06.png#lightbox)
5. 单击**1 x Apple TV 条目**并选择图像文件。
6. 重复上述步骤为`Front`和`Middle`资产。
7. 然后重复相同的步骤来定义`App Icon - Large`资产。
4. 保存更改。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>顶层架子映像

如果用户具有 Apple 电视主页屏幕上的顶部行上放置 Xamarin.tvOS 应用程序，将显示较大的顶部架图像时由用户选择您的应用程序。 此映像应突出显示你的应用程序的功能，或者提供其内容的直接链接。

[![](icons-images-images/topshelf01.png "上架图像示例")](icons-images-images/topshelf01.png#lightbox)

也可以作为单个静态提供顶部架映像`.png`或`.lsr`文件 (请参阅[创建分层映像](#Creating-Layered-Images)) 或它可以动态创建在运行时作为单个行的焦点的项 (请参阅[动态顶层架子内容](#Dynamic-Top-Shelf-Content)下面)。

|顶层架子图像大小|说明|
|---|---|
|1920x720px|静态.png 或分层的.lsr 文件|

Apple 提供用于创建你顶部架映像以下建议：

- **使用丰富静态图像**– 如果你的应用程序不提供动态内容，其顶部架映像将非可获得焦点。 使用此映像来突出显示的应用程序或你的品牌的功能。
- **链接到应用程序内容**– 动态顶部架布局提供指向你的用户查找应用程序中最重要的内容的快速链接。 使用此区域提供快速的链接，以启动应用并立即跳转到给定的内容。
- **展示最新内容**– 可绘制到你的应用的用户将丰富顶部架内容并使它们想要使用更多。 使用此区域展示最高评分或最新内容。
- **个性化内容**– 用户位置，其最常用的或主页屏幕的顶部行中的收藏应用。 使用顶部架显示在最感兴趣的内容。
- **不允许广告**– 广告严禁要显示在顶部架中。 你可能会显示最新的单独购买该内容，但应显示没有定价的信息。

### <a name="setting-the-top-shelf-image"></a>设置顶层架子图像

若要设置你 tvOS 的项目所需的顶部架映像，请执行以下操作：

1. 在**解决方案资源管理器**，双击`Assets.xcassets`以将其打开以进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets 文件")](icons-images-images/asset01.png#lightbox)
2. 在**资产编辑器**，展开`App Icon & Top Shelf Image`资产： 

    [![](icons-images-images/asset04.png "展开顶部架图像资产")](icons-images-images/asset04.png#lightbox)
3. 单击`Top Shelf Image`资产： 

    [![](icons-images-images/asset07.png "顶部架图像资产")](icons-images-images/asset07.png#lightbox)
5. 单击**1 x Apple TV 条目**并选择图像文件。
6. 保存更改。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>动态顶层架子内容

而不是显示静态的顶部架图像，顶部架可以包含的动态行[可获得焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)或一组动态的滚动横幅。 这两个这些动态样式，可以突出显示你的应用或跳转到其最常用的功能通过提供的内容。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>分节的内容行

此动态的顶部架内容类型显示滚动，可获得焦点的项 （可选） 分解部分为单个的行。 它通常用于突出显示新的、 收藏或最近查看过应用内容。

内容会显示为单个、 水平滚动列表的内容替换为所选择的内容的当前部分下显示的标签 （当前具有焦点）。 如果用户选择某一给定的内容，将启动你的应用程序，它们应考虑直接到该内容。

将需要以下内容的大小：

||海报 (2:3)|正方形 (1:1)|HDTV (16:9)|
|---|---|---|---|
|实际大小|404x608px|608x608px|908x512px|
|安全区域大小|380x570px|570x570px|852x479px|
|失去焦点的大小|333x500px|500x500px|782x440px|
|已设定焦点的大小|380x570px|570x570px|852x479px|

Apple 提供的内容剖切行以下建议：

- **完成该行**– 应提供足够的内容跨越整个屏幕的宽度。
- **缩放混合图像**– 剖切内容的行已用于保存图像大小 （从提供上面的列表） 的组合。 如果执行但是混合图像大小，请注意，其他缩放也会应用规范化内容显示。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>滚动内嵌横幅

（可选） 你 Xamarin.tvOS 的应用程序可以在自动滚动和循环几乎填满整个屏幕的横幅的集合作为上架呈现其内容。 此样式通常用于展示丰富，新的内容，例如新的电视节目。

除了自动滚动，用户可以控制横幅，并且在使用 Siri 远程任一方向上滚动。 进行一个较小，循环笔势上使用 Siri 远程时焦点的标题是将激活该横幅视差效果。

**横幅图像 （额外宽型）**

|   |   |
|---|---|
|实际大小|1940x624px|
|安全区域大小|1740x620px|
|失去焦点的大小|1740x560px|
|已设定焦点的大小|1740x620px|

滚动内嵌横幅可以是作为提供一个静态`.png`或分层`.lsr`文件。

Apple 提供滚动内嵌标题的以下建议：

- **内容量**-应为滚动感到自然提供这三 （3） 横幅最少。 应包含不能超过八 （8） 横幅或它使导航硬最终用户。
- **内容文本**-如果你横幅要求中的文本应包括在横幅图像。 如果你正在使用分层映像，最高层应为你的文本。

请参阅 Apple 的[TVServices Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)有关将顶部架扩展添加到你的应用以提供动态顶部架内容的详细信息。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>游戏中心图像

如果您 Xamarin.tvOS 的应用程序是一个游戏，并且已包含游戏中心支持，将需要几个更多的图像资产：

- **成就图标**-不透明映像是所必需的每个都将被自动剪裁插入圆形的实现。 成就是非可获得焦点的项。
- **仪表板图稿**-一个可选图像可以是提供，将出现在游戏中心内的应用程序的仪表板的顶部。 这些映像可以非可获得焦点。
- **排行榜图稿**-您必须提供之间一 （1） 到三 （3） 16:9 纵横比图像为你的应用支持每个 leaderboard。 这些可能是静态`.png`或分层`.lsr`文件。 排行榜作品的焦点。

||成就图标|仪表板图稿|排行榜图稿|
|---|---|---|---|
|可见的大小|200x200px|923x150px|n/a|
|实际大小|320x320px|n/a|659x371px|
|安全区域大小|n/a|n/a|618x348px|
|失去焦点的大小|n/a|n/a|548x309px|
|已设定焦点的大小|n/a|n/a|618x348px|

有关使用 Game Center 的详细信息，请参阅 Apple 的[游戏中心编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>使用图像

由于 tvOS 9 的 iOS 9 子集，用于包含和在 Xamarin.iOS 应用中，显示图像的相同技术将也适用于 Xamarin.tvOS 应用。 请参阅我们[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文档以了解更多信息。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>设置 Xamarin.tvOS 项目图像

如上面所述，所有 tvOS 应用都需要[启动映像](#Launch-Image)，和[应用程序图标](#App-Icons)。 本部分介绍 Xamarin.tvOS 应用项目选择的启动映像和应用程序图标后资产目录中尚未设置它们。

请执行以下操作：

1. 在**解决方案资源管理器**，双击`Info.plist`以将其打开以进行编辑： 

    [![](icons-images-images/info01.png "Info.plist 文件")](icons-images-images/info01.png#lightbox)
2. 在**Info.Plist 编辑器**，选择的资产目录 (在上面配置[设置应用程序图标](#Setting-the-App-Icons)部分) 为**应用图标**: 

    [![](icons-images-images/info02.png "Info.Plist 编辑器")](icons-images-images/info02.png#lightbox)
3. 接下来，选择资产目录 (在上面配置[设置启动映像](#Setting-the-Launch-Image)部分) 为**启动映像**。
4. 保存更改。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖所有图像类型和大小在 Xamarin.tvOS 应用中使用。 首先，则覆盖启动映像、 分层映像、 应用程序图标，顶部架映像和游戏中心映像。 然后，则覆盖使用 Xamarin.tvOS 应用程序中的映像。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
