---
title: 用于 iOS 和 macOS 的本机类型
description: 本文档介绍如何 Xamarin 的统一 API 将.NET 类型映射到 32 位和 64 位本机类型，根据需要根据编译目标体系结构。
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781099"
---
# <a name="native-types-for-ios-and-macos"></a>用于 iOS 和 macOS 的本机类型

Mac 和 iOS Api 使用的都是始终在 32 位平台上和 64 位平台上的 64 位的 32 位的特定于体系结构的数据类型。

例如，Objective C 将映射`NSInteger`数据类型设置为`int32_t`在 32 位系统上并且为`int64_t`在 64 位系统上。

若要匹配此行为，请在我们统一的 API，我们正在替换以前使用的`int`(.NET 中其定义为始终`System.Int32`) 到新的数据类型： `System.nint`。 你可以将"n"是有意义"本机"，因此，本机整数键入的平台。

使用这些新的数据类型，是为 32 位和 64 位体系结构，具体取决于你编译标志编译相同的源代码。

## <a name="new-data-types"></a>新数据类型

下表显示我们的数据类型以与匹配这一新 32/64 位领域中所做的更改：

|本机类型|32 位后备类型|64 位后备类型|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

我们选择允许 C# 代码看起来更多或更少看起来会今天的相同方式这些名称。

### <a name="implicit-and-explicit-conversions"></a>隐式转换和显式转换

新的数据类型的设计旨在使单个 C# 源文件自然地使用 32 位或 64 位存储，具体取决于主机平台和编译设置。

这需要我们设计一组的隐式和显式转换与特定于平台的数据类型到.NET 整型和浮点数据类型。

在没有数据丢失 （32 位值存储在 64 位空间） 不可能不存在时才提供隐式转换运算符。

可能发生了数据丢失时提供显式转换运算符 （64 位值存储在 32 或可能 32 个存储位置）。

 `int``uint`和`float`都是隐式转换为`nint`，`nuint`和`nfloat`根据 32 位将始终在 32 或 64 位。

 `nint``nuint`和`nfloat`都是隐式转换为`long`，`ulong`和`double`32 或 64 位值将始终适合于 64 位存储。

你必须使用显式转换从`nint`，`nuint`和`nfloat`到`int`，`uint`和`float`由于本机类型可能会持有的存储的 64 位。

你必须使用显式转换从`long`，`ulong`和`double`到`nint`，`nuint`和`nfloat`由于本机类型可能仅能容纳的存储的 32 位。

## <a name="coregraphics-types"></a>CoreGraphics 类型

用于 CoreGraphics 的点、 大小和矩形数据类型使用 32 位或 64 位具体取决于设备运行。  当我们最初绑定的 iOS 和 Mac Api 时我们将使用现有相匹配的主机平台的大小的数据结构 (数据类型中`System.Drawing`)。

在迁移到**统一**，你将需要的实例替换`System.Drawing`与其`CoreGraphics`对应的下表中所示：

|System.Drawing 中的旧类型|新的数据类型 CoreGraphics|描述|
|--- |--- |--- |
|`RectangleF`|`CGRect`|浮点的保留点矩形信息。|
|`SizeF`|`CGSize`|浮点的保留点大小信息 （宽度、 高度）|
|`PointF`|`CGPoint`|保存浮点型、 点信息 (X，Y)|

旧的数据类型使用浮动存储的数据结构中，元素而新的另一个使用`System.nfloat`。

## <a name="related-links"></a>相关链接

- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
