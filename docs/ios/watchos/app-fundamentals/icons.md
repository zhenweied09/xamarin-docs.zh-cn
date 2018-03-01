---
title: "使用图标"
ms.topic: article
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 38a2e8b8cd5932bf96c1e0032a6f47627c3ea592
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-icons"></a>使用图标

Apple Watch 解决方案需要图标的两个的集：

* 将出现在 iPhone 的 iOS 应用图标。
* 在监视菜单上圆圈，圆圈中和在通知屏幕将呈现的 Apple Watch 图标。 监视应用程序图标也将出现在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 应用程序。

## <a name="apple-watch-icons"></a>Apple Watch 图标

<table align="center" border="1" cellpadding="1" cellspacing="1">
    <tr>
      <td valign="top">
        <b>iOS 应用程序图标</b>
      </td>
      <td valign="top">
显示在 iPhone 上，启动的父应用程序 </td>
      <td>
        <img src="icons-images/icon-ios.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top" rowspan="3">
        <b>监视应用程序图标</b>
      </td>
      <td valign="top">
Apple Watch 主屏幕上显示 </td>
      <td>
        <img src="icons-images/icon-home.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
将出现在监视通知 </td>
      <td>
        <img src="icons-images/notification-icon.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
将出现在<a href="~/ios/watchos/app-fundamentals/settings.md">iOS Apple Watch 应用</a>
      </td>
      <td>
        <a href="icons-images/watch-app.png">
          <img src="icons-images/watch-app-sml.png" class="tableimg">
        </a>
      </td>
    </tr>
    <tbody>
</table>



## <a name="configuring-your-solution"></a>配置你的解决方案

若要确保您的 iOS 应用和监视应用程序都显示正确的名称和图标，请按照这些说明每个项目：

### <a name="ios-app"></a>iOS App

请参阅[iOS 应用程序图标指导](~/ios/app-fundamentals/images-icons/app-icons.md)以确保正确配置 iOS 应用程序的图标。

#### <a name="infoplist"></a>Info.plist

你监视的应用程序旁边显示的字符串[Apple Watch 设置应用程序](~/ios/watchos/app-fundamentals/settings.md)中配置**iOS 应用的 info.plist 文件**。

确认你**Info.plist**具有`CFBundleName`键和值 (注意： 这是不同于`CFBundleDisplayName`，你可以同时具有):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 应用

一次你[父应用](~/ios/watchos/app-fundamentals/parent-app.md)已经配置了其图标，你需要将应用程序图标资产目录添加到 watch 应用。

1. 右键单击监视应用程序项目并选择**文件 > 添加 > 新文件 … > iOS > 资产目录**将资产目录添加到项目。

 ![](icons-images/newasset.png "将资产目录添加到项目")

2. 双击**AppIcons.appiconset/Contents.json**文件

  ![](icons-images/xcassets-iconset-sml.png "AppIcons 内容")

3. 添加所有 watchOS 映像，此屏幕截图中所示：

  [ ![](icons-images/appicons-sml.png "在此屏幕截图所示，将添加所有 watchOS 映像")](icons-images/appicons.png)

  请参阅[Apple 的图标准则](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)为所需的大小 （维度也会显示在屏幕）。 请记住，这些图标将自动剪辑元素以在一个圆周中呈现。

  图标列表应如下所示：

  ![](icons-images/xcassets-complete-sml.png "解决方案资源管理器中的图标列表")

4. 若要确保资产目录包含应用程序中，添加以下键和值添加到**Watch 应用的 Info.plist**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcons.appiconset</string>
```

你可以验证通过检查的图标是否配置正确[Apple Watch 设置应用程序](~/ios/watchos/app-fundamentals/settings.md)在 iPhone 模拟器中，或生成[通知](~/ios/watchos/platform/notifications.md)并确认图标显示在通知上屏幕。

> [!NOTE]
> **请注意**： 图标不能具有 alpha 通道 （应用程序将被拒绝应用商店提交过程存在 alpha 通道时）。 你可以检查是否存在 alpha 通道，并将其删除[在 Mac OS X 上使用预览应用](~/ios/watchos/troubleshooting.md#noalpha)。


## <a name="related-links"></a>相关链接

- [Apple 的图标和图像指导](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)
