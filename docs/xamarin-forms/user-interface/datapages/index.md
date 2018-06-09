---
title: Xamarin.Forms DataPages
description: 本文介绍 Xamarin.Forms DataPages，也不能提供一个 API 到快速轻松地将数据源绑定到预建的视图。
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243325"
---
# <a name="xamarinforms-datapages"></a>Xamarin.Forms DataPages

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!IMPORTANT]
> DataPages 需要[Xamarin.Forms 主题](~/xamarin-forms/user-interface/themes/index.md)引用来呈现。

Xamarin.Forms DataPages 发展变化 2016年已宣布，也可作为客户尝试并提供反馈的预览。

DataPages 提供一个 API，用于快速、 轻松地将数据源绑定到预建的视图。 列表项和详细信息页将自动呈现的数据，可以使用主题自定义。

若要查看发展变化 //build 主题演示的工作原理，请查看[入门指南](get-started.md)。

[![](images/demo-sml.png "DataPages 示例应用程序")](images/demo.png#lightbox "DataPages 示例应用程序")

## <a name="introduction"></a>介绍

数据源和关联的数据页允许开发人员快速、 轻松地使用支持的数据源，并使其使用的内置 UI 基架，可以自定义主题。

向 Xamarin.Forms 应用程序添加 DataPages，应包括**Xamarin.Forms.Pages** Nuget 包。

### <a name="data-sources"></a>Data Sources

预览具有可供使用一些预构建的数据源：

* **JsonDataSource**
* **AzureDataSource** （隔开 Nuget）
* **AzureEasyTableDataSource** （隔开 Nuget）

请参阅[入门指南](get-started.md)的使用示例`JsonDataSource`。


### <a name="pages--controls"></a>页面和控件

以下页面和控件是包含在内，以允许到提供的数据源的简单绑定：

* **ListDataPage** – 请参阅[入门示例](get-started.md)。
* **DirectoryPage** – 启用分组的列表。
* **PersonDetailPage** – 单个数据项的特定对象类型 （联系人条目） 为自定义的视图。
* **DataView** – 要公开通用的方式在源中数据的视图。
* **卡片视图-** – 风格视图，其中包含图像、 标题文本和说明文本。
* **HeroImage** – 图像呈现视图。
* **ListItem** – 预生成视图的布局类似于本机 iOS 和 Android 的列表项。

请参阅[DataPages 控件参考](controls.md)有关示例。



### <a name="under-the-hood"></a>揭秘

Xamarin.Forms 数据源符合`IDataSource`接口。

Xamarin.Forms 基础结构与通过以下属性的数据源进行交互：

* `Data` – 可以显示的数据项的只读列表。
* `IsLoading` – 一个布尔值，该值指示数据是否已加载并可用于呈现。
* `[key]` – 要从中检索元素的索引器。

有两种方法`MaskKey`和`UnmaskKey`可用来隐藏 （或显示） （例如数据项目属性 防止它们呈现）。
键对应于数据项对象的命名的属性。
