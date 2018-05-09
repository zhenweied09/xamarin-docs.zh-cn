---
title: Xamarin.Forms 本地化
description: 内置的.NET 本地化框架可以用于构建跨平台使用 Xamarin.Forms 的多语言应用程序。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 29624eeccc8542b3296774f6b77480b664bee478
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="localization"></a>本地化

_内置的.NET 本地化框架可以用于构建跨平台使用 Xamarin.Forms 的多语言应用程序。_

## <a name="string-and-image-localizationtextmd"></a>[字符串和映像本地化](text.md)

本地化.NET 应用程序使用的内置机制[RESX 文件](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)和中的类`System.Resources`和`System.Globalization`命名空间。 包含已翻译的字符串的 RESX 文件都嵌入到 Xamarin.Forms 程序集，以及一个编译器生成的类，提供对翻译强类型访问。 然后可以在代码中检索已翻译的文本。

## <a name="right-to-left-localizationright-to-leftmd"></a>[从右到左本地化](right-to-left.md)

流方向是人眼扫描的页上的 UI 元素的方向。 从右到左本地化将从右到左排列方向对支持添加到 Xamarin.Forms 应用程序。
