---
title: 在 Xamarin.iOS 应用程序商店图标
description: 本文档介绍如何使用资产目录管理 Xamarin.iOS 应用程序的应用商店图标。 以前，应用商店图标进行管理 iTunes Connect 管理。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: 749dbf01af382a54fe24652706f6a605ac7b20b4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783605"
---
# <a name="app-store-icons-in-xamarinios"></a>在 Xamarin.iOS 应用程序商店图标

在 Xcode 9 之前通过 iTunes Connect 添加了所有的应用商店图标。 但是，这不再是这种情况。 应用商店图标现在必须包括作为项目捆绑包的一部分并资产目录中添加的。 将通过 Apple 拒绝应用程序不包含应用商店图标。

应用商店图标是表面的应用程序以便对用户，因此它必须记忆和也在小尺寸处显示。 易记的图标是干净、简单且具有高度辨识度的。

Apple 建议在设计应用程序图标时遵照以下准则：

- 确保图标适合应用程序。
- 创建与应用程序的设计一致的简单图标。
- 图标中避免使用文字。
- 整体思考：单个应用图标用于商店的所有区域。

App Store 中显示的应用图标必须是 1024 x 1024 像素的图像。  Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

有关详细信息，请参阅 Apple 的[iOS 人机接口指南](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>添加一个应用商店图标

应用商店图标现在应由资产目录提供。 

若要添加应用商店图标，请执行以下操作：

1. 找到**AppIcon**映像设置**Assets.xcassets**在项目文件。 
    - 所有新项目应该装有**Assets.xcassets**包含 AppIcon 映像组的文件。
    - 若要添加新的资产目录，右键单击项目并选择**添加 > 新文件 > 资产目录**。
    - 若要添加新应用程序图标映像集，右键单击该图标集区域并选择在**应用图标和启动映像 > 新应用程序图标**:
    
    ![添加新映像 set 选项](app-store-icon-images/image1.png)

2. 滚动到**应用商店**列表中的图标：

    ![应用商店图标](app-store-icon-images/image2.png)

3. 单击图标，并浏览到你 1024 x 1024 像素的图像。 保存资产目录。




## <a name="related-links"></a>相关链接

- [管理与资产目录的图标](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
