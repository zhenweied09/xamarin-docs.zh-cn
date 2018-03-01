---
title: "使用布局"
ms.topic: article
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 71a192343800dc11f46c645c27ab8a74209c92e6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-layout"></a>使用布局

针对 Apple Watch 设计布局[屏幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)提供独特的挑战。

## <a name="design-tips"></a>设计提示

关键之处在于： 使你的用户界面，具可读性且可用的小监视屏上，用大手指。 不属于的有关设计陷阱**iOS 模拟器**（显示非常大） 和一个**鼠标指针**（适用于小触摸目标） ！

- 使用黑色背景-它创建监视的黑色挡板更大的屏幕的效果。

- 执行不填充屏幕布局周围-挡板窗体自然 visual 填充。

- 专注于可读性。 使用字体大小和颜色谨慎以确保文本是可读。 使用内置文本样式以获得自动动态类型支持。

![](layout-images/type.png "动态类型支持的示例")

- 专注于触摸目标大小。 带有文本标签的按钮/tappable 表行应跨越整个屏幕。 Apple 指出"永远不会将放置在超过三个项并行"，并且如果你不使用图标和不文本标签。

- 使用[`Menu`控件](~/ios/watchos/user-interface/menu.md)到不太常使用的公开功能，使你的应用程序设计清晰、 简洁。


## <a name="implementation"></a>实现

观看工具包包含以下控件可帮助你构建极具吸引力的监视应用布局：

### <a name="interface-controller"></a>接口控制器

`WKInterfaceController`是所有你场景的基类。

接口控制器的设计图面表现得像垂直**组**： 可以将其他控件拖动到接口控制器以及他们将上下自动布局的一个：

![](layout-images/controller-scene.png "控件是上下自动布局的一个")

你可以设置**位置**和**大小**上每个控件来控制其外观的属性：

![](layout-images/positionsize-attributes.png "在每个控件上设置的位置和大小的属性")

当将大小设置为**相对于容器**您可以提供比例值和偏移量的调整。 此屏幕快照显示已设置为使用监视屏幕的宽度的 80%的按钮 (**0.8**):

![](layout-images/button-attributes.png "提供比例值和偏移量的调整")


### <a name="group"></a>Group

`WKInterfaceGroup` 是可以配置为堆栈简单布局容器控件垂直或水平。 它包括默认情况下，每个控件之间的间距，但你可以在修改的间距 （和内边距）**属性**检查器。

![](layout-images/group-attributes.png "修改的间距和内边距属性检查器中")

组可以自己可调整大小和相对于围绕它们控件定位和组可以嵌套创建复杂布局。

![](layout-images/group-scene.png "组可以嵌套创建复杂的布局")


### <a name="separator"></a>Separator

Separator 控件旨在帮助提供了在你的布局的可视指南。 使用分隔符 （或背景色或图像） 以帮助用户了解哪些内容相关屏幕上。

![](layout-images/separator-scene.png "分隔符用法的示例")

请注意不要使用整个屏幕的宽度的蓝色和绿色分隔符已配置了或者**固定**或**相对于容器**大小。

### <a name="content-controls"></a>内容控件

任何布局都是不完整`Label`， `Image`， `Button`， `Switch`， `Slider`， `Map`，和[其他控件](~/ios/watchos/user-interface/index.md)。
这些可以在你使用的布局中定位的**组**或上每个控件的位置和大小设置。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Apple 的布局引用](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的颜色和版式引用](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
