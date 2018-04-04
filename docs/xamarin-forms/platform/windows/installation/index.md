---
title: 安装 Windows 项目
description: 将新的 Windows 项目添加到现有 Xamarin.Forms 解决方案
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0ad8dedc2e92005473f8836c3cdd590ce4cab5ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="setup-windows-projects"></a>安装 Windows 项目

_将新的 Windows 项目添加到现有 Xamarin.Forms 解决方案_

较早的 Xamarin.Forms 项目 (或在 Mac OS 上创建的那些&nbsp;X) 将不具有这些 Windows 项目设置。

这意味着你将需要手动添加这些项目类型以生成 Windows 8.1、 Windows Phone 8.1 和 Windows 10 (UWP) 应用。

## <a name="add-a-windows-81-app"></a>添加 Windows 8.1 应用

* 如果你使用 PCL 模板，[更新该配置文件](#pcl)，然后
* [将 Windows 8.1 应用添加](~/xamarin-forms/platform/windows/installation/tablet.md)为平板电脑/桌面的窗口因素。

## <a name="add-a-windows-phone-81-app"></a>添加 Windows Phone 8.1 应用

* 如果你使用 PCL 模板，[更新该配置文件](#pcl)，然后
* [添加 Windows Phone 8.1 应用](~/xamarin-forms/platform/windows/installation/phone.md)

## <a name="add-a-universal-windows-platform-uwp-app"></a>添加通用 Windows 平台 (UWP) 应用程序

* 生成[UWP](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx)应用需要在 Windows 10 上运行的 Visual Studio 2015。
* 如果你使用 PCL 模板，[更新该配置文件](#pcl)，然后
* [添加通用 Windows 平台应用](~/xamarin-forms/platform/windows/installation/universal.md)

<a name="pcl" />

### <a name="update-your-pcl-profile"></a>更新 PCL 配置文件

如果你现有的 Xamarin.Forms 应用程序使用可移植类库 (PCL) 模板，则必须更新其配置文件。

1. **右键单击 > 属性**（你的现有设置可能不同）

  ![](images/targets.png "PCL 目标")

2. 单击**更改...**按钮

3. 确保**Windows 8**和**Windows Phone 8.1**会选择的选项 (和**Windows Phone Silveright**是*取消选择*):

  ![](images/pcl.png "PCL 目标选项")

4. 按**确定**并保存所做的更改。

这相当于**配置文件 111**如果你要在 Visual Studio 中配置你 PCL，适用于 Mac 使用下拉列表。

  ![](images/pcl-xs.png "PCL 配置文件 111")

**注意：** PCL 如果你的解决方案仍有一个 Windows Phone 8 Silverlight 项目，应设置为配置文件 259。 Windows Phone 8 Silverlight 支持已弃用，因此建议将此页面上显示的项目类型。
