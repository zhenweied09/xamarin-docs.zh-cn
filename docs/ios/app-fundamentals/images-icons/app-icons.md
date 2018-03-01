---
title: "应用程序图标"
description: "此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，要用作应用程序图标。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: 6de57e9523ff336c2e06e39903280db9c9ab95fa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="application-icons"></a>应用程序图标

_此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，要用作应用程序图标。_

将详细介绍以下主题：

* [应用程序、 Spotlight 和设置图标](#icon-types)-不同类型的所需的 iOS 应用程序的图标。
* [管理与资产目录图标](#managing)-管理应用程序图标，使用资产目录。
* [iTunes 图稿](#itunes)-提供传送你的应用程序的即席方法需要的 iTunes 图片。

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>应用程序、 Spotlight 和设置图标

在相同的方式的 Xamarin.iOS 应用程序可使用图像资产为 UI 控件和为文档图标，可以用于提供应用程序图标图像资产。 从 iPad 以下屏幕截图演示在 iOS 中的图标的三个使用：

- **应用程序图标**-每个 iOS 应用程序必须定义应用程序图标。 用户将点击此图标在 iOS 主页屏幕中以启动应用程序。 此外，此图标使用 Game Center 有可能的话。 示例: 

    [ ![](app-icons-images/000.png "应用程序图标")](app-icons-images/000-full.png)
- **聚光灯图标**-每当用户在 Spotlight 搜索时，输入应用的名称将显示此图标。 示例: 

    [ ![](app-icons-images/000a.png "Spotlight 图标")](app-icons-images/000a-full.png)
- **设置图标**-如果用户输入**设置**应用在其 iOS 设备，此图标上的将显示在末尾**设置**应用程序的列表。 示例: 

    [ ![](app-icons-images/000b.png "设置图标")](app-icons-images/000b-full.png)

以下图像资产大小和分辨率将需要支持所有面向 iOS 5 通过 iOS 9 （或更高版本） Xamarin.iOS 应用程序所需的图标类型：

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPhone</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 & 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td align="center" bgcolor="#F9F9F9"><b>iOS 9 & 10<b><br/><i>(iPhone 6 & 7 Plus)</i></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>图标类型</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>3x</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">应用程序图标</td>
        <td align="center">57x57</td>
        <td align="center">114x114</td>
        <td align="center" style="color:#BBBBBB;">60x60<sup>(1)</sup></td>
        <td align="center">120x120</td>
        <td align="center">180x180</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Spotlight</td>
        <td align="center">29x29</td>
        <td align="center">58x58</td>
        <td align="center" style="color:#BBBBBB;">40x40<sup>(2)</sup></td>
        <td align="center">80x80</td>
        <td align="center">120x120</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">设置</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(4)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(4)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center">87x87</td>
    </tr>
</table>

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPad</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 & 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td colspan="1" align="center" bgcolor="#F9F9F9"><b>iOS&nbsp;9 & 10</b></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>图标类型</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>2x<br/>iPad&nbsp;Pro</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">应用程序图标</td>
        <td align="center">72x72</td>
        <td align="center">144x144</td>
        <td align="center">76x76</td>
        <td align="center">152x152</td>
        <td align="center">167x167<sup>(6)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Spotlight</td>
        <td align="center">50x50</td>
        <td align="center">100x100</td>
        <td align="center">40x40</td>
        <td align="center">80x80</td>
        <td align="center" style="color:#BBBBBB;">120x120<sup>(5)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">设置</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(5)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(5)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(5)</sup></td>
    </tr>
</table>

1. _针对 Mac 和 Xcode 这两个 Visual Studio 不再支持设置适用于 iOS 7 的 1 x 图像。_
2. _使用资产目录时，不支持设置 iOS 7 的 1 x 映像。_
3. _iOS 7 和 8 使用相同的图像大小作为 iOS 5 和 6。_
4. _为 Spotlight 图标中使用的相同的映像和大小。_
5. _使用作为 iPhone 的相同的大小图标。_
6. _仅支持资产目录映像集。_

有关图标的详细信息，请参阅 Apple 的[图标和图像的大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)文档。

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>管理与资产目录的图标

图标，一种特殊`AppIcons`映像组可以添加到`Assets.xcassets`应用的项目文件中。 支持所有的解决方法所需的映像的所有版本都将都纳入_xcasset_和组合在一起。 适用于 Mac 的 Visual Studio 中的特殊编辑器允许开发人员可以包括以图形方式设置这些映像。

若要使用资产目录，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 向下滚动到**应用图标**部分。
3. 从**源**下拉列表中，确保**AppIcons**选择： 

    ![](app-icons-images/migrate01.png "确保选择 AppIcons")
4. 从**解决方案资源管理器**，双击`Assets.xcassets`文件以打开进行编辑： 

    ![](app-icons-images/asset01.png "在解决方案资源管理器 Assets.xcassets 文件")
5. 选择`AppIcons`从要显示的资产列表`Icon Editor`: 

    ![](app-icons-images/asset02.png "AppIcons 编辑器")
6. 单击给定的图标类型和选择所需的类型/大小的图像文件或从文件夹在图像中拖动并放置在所需大小上。
7. 单击**打开**按钮在项目中包含图像，并将其设置 xcasset 中。
8. 重复上述步骤，所需的所有映像的。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击**Info.plist**文件中**解决方案资源管理器**:

    ![](app-icons-images/icon01w.png "选择 Info.plist")
2. 单击**可见资产**选项卡上，然后单击**使用资产目录**按钮下**应用图标**: 

    ![](app-icons-images/icon02w.png "选择可见资产选项卡")
4. 从**解决方案资源管理器**，展开**资产目录**文件夹： 

    ![](app-icons-images/image009.png "展开资产目录文件夹")
5. 双击**媒体**文件以在编辑器中打开它： 

    ![](app-icons-images/image010.png "在编辑器中打开的媒体文件")
6. 下**属性资源管理器**开发人员可以选择的不同类型和所需的图标大小。
7. 给定的图标类型单击并选择所需的类型/大小的图像文件。
8. 单击**打开**按钮在项目中包含图像，并将其设置 xcasset 中。
9. 重复上述步骤，所需的所有映像的。

-----

这是包括和管理将用于为应用提供应用程序、 Spotlight 和设置图标的图像资产的首选的方法。

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>从 Info.plist 迁移到资产目录

现有的 Xamarin.iOS 应用程序使用`Info.plist`文件来管理它的图标，强烈建议，开发人员将其转换为使用`AppIcons`内的图像资产`Assets.xcassets`。

请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 向下滚动到**应用图标**部分。
3. 从**源**下拉列表中，选择**迁移到资产目录**: 

    ![](app-icons-images/migrate02.png "选择将迁移到资产目录")
4. 在中定义的任何现有图标`Info.plist`文件将迁移到`AppIcons`设置图像添加到`Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "在 Assets.xcassets 中设置 AppIcons 映像")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 在 iPhone 上单击图标部分： 

    ![](app-icons-images/image007.png "Rhe iPhone 图标编辑器")
3. 向下滚动到**图标**部分。
4. 从**资产目录**下拉列表中，选择**使用资产目录**。
5. 在中定义的任何现有图标`Info.plist`文件将迁移到`Images`集添加到`Assets.xcassets`。
6. 保存对 `Info.plist` 文件所做的更改。

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>iTunes 图稿

如果使用交付应用程序 （不管是为企业用户或在真实设备上的测试） 的即席方法，开发人员还需要包括 512 x 512 和将用于表示在 iTunes 应用的 1024 x 1024 映像。

若要指定 iTunes 图稿，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 滚动到**iTunes 图稿**编辑器部分中： 

    ![](app-icons-images/itunes01.png "滚动到 iTunes 编辑器图稿部分")
3. 任何缺少的图像，请单击编辑器中的缩略图上，从打开的文件对话框中选择所需的 iTunes 作品的图像文件，单击**确定**按钮。
4. 重复此步骤的所有前需要已指定图像的应用。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。

2. 单击**可见资产**卡，展开**iTunes 图稿**: 

    ![](app-icons-images/itunes01w.png "编辑 iTunes Visual Studio 中的图片")
4. 任何缺少的图像，请单击编辑器中的缩略图上，从打开的文件对话框中选择所需的 iTunes 作品的图像文件，单击**打开**按钮。
5. 重复此步骤的所有前需要已指定图像的应用。

-----

## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和映像创建指南](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
