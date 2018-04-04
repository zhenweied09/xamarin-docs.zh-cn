---
title: XAML 标准 （预览）
description: 如何开始浏览 Xamarin.Forms 中 XAML Standard 预览版
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 7cb2a79fffda40f09ee75a12d6287b162566741d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-standard-preview"></a>XAML 标准 （预览）

![预览](~/media/shared/preview.png)

请按照下列步骤进行实验，以使用 Xamarin.Forms 中 XAML 标准：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 下载[预览此处 NuGet 包](https://aka.ms/xf-xamlstandard-nuget)。
2. 添加**Xamarin.Forms.Alias**到 Xamarin.Forms PCL、 标准.NET 和平台项目的 NuGet 包。
3. 初始化的软件包 `Alias.Init()`
4. 添加`xmlns:a`引用 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的类型-请参阅[控件参考](controls.md)有关详细信息。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 下载[预览此处 NuGet 包](https://aka.ms/xf-xamlstandard-nuget)。
2. 添加**Xamarin.Forms.Alias**到 Xamarin.Forms PCL、 标准.NET 和平台项目的 NuGet 包。
3. 初始化的软件包 `Alias.Init()`
4. 添加`xmlns:a`引用 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的类型-请参阅[控件参考](controls.md)有关详细信息。

-----

下面的 XAML 演示了一些使用 Xamarin.Forms 中的 XAML 标准控件`ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> 需要 xmlns `a:` XAML 标准控件上的前缀是当前的预览版的限制。


## <a name="related-links"></a>相关链接

- [预览 NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [控件引用](controls.md)
