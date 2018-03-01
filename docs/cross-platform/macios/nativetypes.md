---
title: "本机类型"
ms.topic: article
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: b78ade19efed92ab3b2d8ba790f2d7334472bab4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="native-types"></a>本机类型

在差异的核心，Mac 和 iOS Api 使用的特定于体系结构的数据类型都始终在 32 位平台上和 64 位平台上的 64 位的 32 位。

例如，Objective C 将映射`NSInteger`数据类型设置为`int32_t`在 32 位系统上并且为`int64_t`在 64 位系统上。

若要匹配此行为，请在我们统一的 API，我们正在替换以前使用的`int`(.NET 中其定义为始终`System.Int32`) 到新的数据类型： `System.nint`。  你可以将"n"是有意义"本机"，因此，本机整数键入的平台。

使用这些新的数据类型相同的源代码编译为 32 位、 32 位和 64 位或 64 位，具体取决于你编译标志。

## <a name="new-data-types"></a>新数据类型

下表显示我们的数据类型以与匹配这一新 32/64 位领域中所做的更改：

<table>
        <tr>
            <th>本机类型</th>
            <th>32 位后备类型</th> 
            <th>64 位后备类型</th>
        </tr>
        <tr>
            <td><code>System.nint</code></td>
        <td><code>System.Int32</code> (<code>int</code>)</td>
        <td><code>System.Int64</code> (<code>long</code>)</td>
        </tr>
        <tr>
            <td><code>System.nuint</code></td>
        <td><code>System.UInt32</code> (<code>uint</code>)</td>
        <td><code>System.UInt64</code> (<code>ulong</code>)</td>
        </tr>
        <tr>
            <td><code>System.nfloat</code></td>
        <td><code>System.Single</code> (<code>float</code>)</td>
        <td><code>System.Double</code> (<code>double</code>)</td>
        </tr>
    </table>

我们选择允许 C# 代码看起来更多或更少看起来会今天的相同方式这些名称。

### <a name="implicit-and-explicit-conversions"></a>隐式转换和显式转换

新的数据类型的设计旨在使单个 C# 源文件自然地使用 32 位或 64 位存储，具体取决于主机 platorm 和编译设置。

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

<table>
        <tr>
            <th>System.Drawing 中的旧类型</th>
            <th>新的数据类型 CoreGraphics</th> 
            <th>描述</th>
        </tr>
        <tr>
        <td><code>RectangleF</code></td>
        <td><code>CGRect</code></td>
        <td>浮点的保留点矩形信息。  </td>
        </tr>
        <tr>
        <td><code>SizeF</code></td>
        <td><code>CGSize</code></td>
        <td>浮点的保留点大小信息 （宽度、 高度）</td>
        </tr>
        <tr>
        <td><code>PointF</code></td>
        <td><code>CGPoint</code></td>
        <td>保存浮点型、 点信息 (X，Y)</td>
        </tr>
    </table>

旧的数据类型使用浮动存储的数据结构中，元素而新的另一个使用`System.nfloat`。

## <a name="related-links"></a>相关链接

- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
