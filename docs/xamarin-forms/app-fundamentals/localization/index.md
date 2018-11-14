---
title: Xamarin.Forms 本地化
description: 可以使用内置的.NET 本地化框架构建跨平台使用 Xamarin.Forms 的多语言应用程序。 可本地化的文本和图像，并且应用程序可支持从右到左流动方向。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285568"
---
# <a name="xamarinforms-localization"></a>Xamarin.Forms 本地化

_可以使用内置的.NET 本地化框架构建跨平台使用 Xamarin.Forms 的多语言应用程序。_

## <a name="string-and-image-localizationtextmd"></a>[字符串和映像本地化](text.md)

用于本地化.NET 应用程序使用的内置机制[RESX 文件](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)中的类和`System.Resources`和`System.Globalization`命名空间。 包含已翻译的字符串的 RESX 文件嵌入在 Xamarin.Forms 程序集，以及编译器生成的类，提供强类型化访问翻译。 然后可以在代码中检索已翻译的文本。

## <a name="right-to-left-localizationright-to-leftmd"></a>[从右到左本地化](right-to-left.md)

流方向是密切关注扫描的页上的 UI 元素的方向。 从右到左本地化将对从右到左的流方向的支持添加到 Xamarin.Forms 应用程序。
