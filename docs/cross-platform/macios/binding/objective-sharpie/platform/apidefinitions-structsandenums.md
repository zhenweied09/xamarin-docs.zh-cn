---
title: "ApiDefinitions 和 StructsAndEnums 文件"
ms.topic: article
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 0dedc6f574fbe2f2bf4ffaf4e70fb972670e9a8c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions 和 StructsAndEnums 文件

在目标 Sharpie 已经成功地运行，它将生成`Binding/ApiDefinitions.cs`和`Binding/StructsAndEnums.cs`文件。
这两个文件添加到 Visual Studio 中的绑定项目适用于 Mac 或直接传递到`btouch`或`bmac`工具以生成最终的绑定。

在*某些*这些生成的文件可能满足所有需要的是，但更通常开发人员将需要手动修改这些生成的文件以修复任何问题，可能不会自动处理通过工具 （例如那些已标记的情况下与[`Verify`属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md))。

一些后续步骤包括：

- **调整名称**： 有时你需要进行调整的方法和类以匹配.NET Framework 设计准则的名称。
- **方法或属性**： 有时使用目标 Sharpie 的试探法将选取转变为属性的方法。 此时，你无法确定这是否是预期的行为。
- **挂接事件**： 你无法将链接您使用委托类的类，自动为那些生成事件。
- **通知挂钩**： 不能从纯标头文件中提取通知的 API 协定，这将需要访问的 API 文档。 如果你想强类型的通知，你将需要更新结果。
- **API 内容策展**： 在此点，你可以选择提供额外的构造函数中，添加 （以允许 C# 初始化上构造语法） 的方法、 运算符重载和实现你自己的额外的定义文件上的接口。

请参阅[绑定 API](~/cross-platform/macios/binding/objective-c-libraries.md)描述以查看这些文件如何适应绑定过程中下, 图中所示：

![](apidefinitions-structsandenums-images/binding-flowchart.png "此图显示了在绑定过程")

请参阅[绑定类型引用](~/cross-platform/macios/binding/binding-types-reference.md)有关内容的详细信息，这些文件。

