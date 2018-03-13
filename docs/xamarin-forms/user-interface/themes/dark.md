---
title: "深色主题"
ms.topic: article
ms.prod: xamarin
ms.assetid: 43A3798D-6F05-4734-AF5E-97235B46D9B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 7314074424933451ab3cc3ebbf63d0b6f56d9c30
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="dark-theme"></a>深色主题

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!NOTE]
> 主题需要 Xamarin.Forms 2.3 预览版本。 检查[的故障排除技巧](~/xamarin-forms/user-interface/themes/index.md)如果出现错误。

若要使用深色主题：

## <a name="1-add-nuget-packages"></a>1.添加 Nuget 包

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Dark

## <a name="2-add-to-the-resource-dictionary"></a>2.将添加到资源字典

在**App.xaml**文件添加新的自定义`xmlns`主题，然后确保应用程序的资源字典与合并主题的资源。
XAML 文件的示例所示：

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:dark="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Dark">
    <Application.Resources>
        <ResourceDictionary MergedWith="dark:DarkThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3.加载主题类

遵循此[故障排除步骤](~/xamarin-forms/user-interface/themes/index.md)和在 iOS 和 Android 应用程序项目中添加所需的代码。

## <a name="4-use-styleclass"></a>4.使用 StyleClass

下面是按钮和标签深色主题，以及生成它们的标记中的示例。

[![](dark-images/dark-theme-sml.png "按钮和标签深色主题中")](dark-images/dark-theme.png#lightbox "按钮和标签深色主题中")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />

    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

[的内置类的完整列表](~/xamarin-forms/user-interface/themes/index.md)显示哪些样式提供的某些常见的控件。

