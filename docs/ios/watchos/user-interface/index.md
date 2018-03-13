---
title: "watchOS 用户界面"
ms.topic: article
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: 0717b8484c6094bb1d9589c44df37745d9e21900
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="watchos-user-interface"></a>watchOS 用户界面

[ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)示例演示各种 watchOS 控件。 应用程序的情节提要如下所示 （单击来缩放）：

[![](images/storyboard-sml.png "示例 watchOS 布局")](images/storyboard.png#lightbox)

所有控件的编程名称的前面带`WKInterface`（如。 `WKInterfaceLabel`, `WKInterfaceButton`).


<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
        <strong>控件</strong>
      </th>
      <th>
        <strong>描述</strong>
      </th>
      <th>
        <strong>屏幕快照</strong>
      </th>
    </thead>
    <tbody>
    <tr>
      <td valign="top">
Label </td>
      <td valign="top">
使用<code>SetText</code>和其他属性，以控制标签控件中文本的外观。 <code>NSAttributedString</code> 此外支持。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/label.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Button </td>
      <td valign="top">
创建并在情节提要中设置属性。 <kbd>Ctrl + 拖动</kbd>添加<code>Action</code>以实现单击时的处理。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/button.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
开关 </td>
      <td valign="top">
使用<code>SetOn</code>控制交换机状态。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/switch.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Slider </td>
      <td valign="top">
可能会出现许多不同的样式。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/slider.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
图像 </td>
      <td valign="top">
使用<code>myImage.SetImage("MyWatchImage")</code>以加载图像上监视，或<code>WKInterfaceDevice.CurrentDevice.AddCachedImage</code>缓存它们为在手表上重复使用。
        <br />
        <a href="~/ios/watchos/user-interface/image.md">图像控件文档</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/image.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Separator </td>
      <td valign="top">
使用分隔符来帮助创建具吸引力监视 Ui。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/separator.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
映射 </td>
      <td valign="top">
地图图像静态显示受监视，但你可以控制其外观，包括添加 pin 的许多方面。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/map.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Movie & InlineMove </td>
      <td valign="top">
电影可以保持打开状态在他们自己或内联 <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/movie.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
Group </td>
      <td valign="top">
使用组来帮助创建具吸引力监视 Ui。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/group.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
表 </td>
      <td valign="top">
在 iOS 上的表的简化的版本。
实现<code>DidSelectRow</code>以响应用户选择 （或使用 segue）。
        <br />
        <a href="~/ios/watchos/user-interface/table.md">表控件文档</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TableDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/table.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
设备 </td>
      <td valign="top">
        <code>WKInterfaceDevice.CurrentDevice</code> 包括属性，如<code>ScreenBounds</code>， <code>ScreenScale</code>，和<code>PreferredContentSizeCategory</code>。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/device.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
        <a href="~/ios/watchos/user-interface/menu.md">菜单</a>
      </td>
      <td valign="top">
在情节提要中定义的强制按菜单并在代码中实现的每个按钮的操作。
        <br />
        <a href="~/ios/watchos/user-interface/menu.md">菜单控件 (强制 Touch) 文档</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/controller.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
文本输入 </td>
      <td valign="top">
使用<code>PresentTextInputController</code>和<code>WKTextInputMode</code>枚举。
        <br />
        <a href="~/ios/watchos/user-interface/text-input.md">文本输入文档</a>
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/textinput.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
数字王冠 </td>
      <td valign="top">
数字王冠可以用于驱动器选取器，或它的旋转可以在代码中进行跟踪。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/digital-crown.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top">
笔势 </td>
      <td valign="top">
有四种类型的笔势识别可以添加到一个场景： Tap、 轻扫、 平移和 LongPress。
        <br />
        <a href="https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs">目录代码</a>
      </td>
      <td>
        <img src="Images/gestures.png" class="tableimg">
      </td>
    </tr>
    </tbody>
</table>



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [监视工具包 API 参考](https://developer.xamarin.com/api/namespace/WatchKit/)
