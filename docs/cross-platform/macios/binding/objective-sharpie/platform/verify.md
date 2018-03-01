---
title: "验证属性"
ms.topic: article
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cda523cd9d762c3a3c1570e2abd0acb8a264d5dd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="verify-attributes"></a>验证属性


你通常会发现，将使用批注绑定生成的目标 Sharpie`[Verify]`属性。 这些属性指示你应_验证_目标 Sharpie 通过比较与原始 C/Objective C 声明 （这将提供一条注释，该绑定的声明的上方） 绑定未正确操作。

验证建议用于_所有_绑定声明，但很可能是_必需_声明与为`[Verify]`属性。 这是因为在许多情况下，没有足够的元数据的原始本机源代码中推断如何最好地生成绑定。 你可能需要引用文档或标头文件做出最佳的绑定决策内代码注释。

验证绑定适用后更正或具有固定它正确无误，_删除_`[Verify]`从绑定的属性。

> [!IMPORTANT]
> `[Verify]` 属性有意导致 C# 编译错误，以便强制来验证绑定。 应删除`[Verify]`属性时你已查看 （和可能更正） 的代码。

## <a name="verify-hints-reference"></a>验证提示引用

提供给特性的提示参数可以是交叉引用与下面的文档。 对于任何生成的文档`[Verify]`绑定完成后，将在控制台上也提供属性。

<table>
  <thead>
  <tr>
    <th>验证提示</th>
    <th>描述</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td>InferredFromPreceedingTypedef</td>
    <td>此声明的名称已由从通用约定推断立即前面<code>typedef</code>原始本机源代码中。 验证推断的名称正确，因为此约定是不明确。</td>
  </tr>
  <tr>
    <td>ConstantsInterfaceAssociation</td>
    <td>没有可靠一点方法来确定哪个 Objective C 接口与外部变量声明可能是关联。 这些实例绑定为<code>[Field]</code>到附近-通过具体接口以生成更直观的 API，可能消除常量部分接口中的属性完全接口。</td>
  </tr>
  <tr>
    <td>MethodToProperty</td>
    <td>Objective C 方法被绑定为由于如不采用任何参数和返回值 （非 void 返回） 的约定的 C# 属性。 通常类似这样的方法应绑定为属性，做法是，不用去涉猎 API，但有时会的误报和绑定实际应是一种方法。</td>
  </tr>
  <tr>
    <td>StronglyTypedNSArray</td>
    <td>一个本机<code>NSArray*</code>被绑定为<code>NSObject[]</code>。 它可以更强类型化数组基于期望通过 API 文档 （例如标头文件中的注释） 设置的绑定中或通过检查通过测试的数组内容。 例如，NSArray * 包含仅 NSNumber * instancescan 可绑定为<code>NSNumber[]</code>而不是<code>NSObject[]</code>。</td>
  </tr>
  </tbody>
</table>

你可以快速接收有关提示使用文档`sharpie verify-docs`工具，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

