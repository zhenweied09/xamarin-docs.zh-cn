---
title: .NET 嵌入 Objective c 的最佳做法
description: 本文档介绍使用.NET 嵌入目标 C.的各种最佳做法 它讨论公开的托管代码的子集、 公开 chunkier API、 命名和的详细信息。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: b4b0df6f1c7c1d5931c0c18a1508747a7c570bea
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793488"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>.NET 嵌入 Objective c 的最佳做法

这是草稿和可能不同步与的功能目前支持工具。 我们希望本文档将单独发展并最终根据最终的工具，即我们将建议从长远来看最佳方法-无法立即解决方法。

本文档的大部分内容也适用于其他支持的语言。 但是所有提供的示例是用 C# 和目标。

## <a name="exposing-a-subset-of-the-managed-code"></a>公开的托管代码的子集

生成本机库/框架包含 Objective C 代码调用托管 Api 公开的每个。 你外围的多个 API （公开） 然后更大本机_粘附_该库将成为。

它可能是一个创建不同，较小的程序集，以公开仅向本机的开发人员的必需的 Api 的好办法。 该外观还允许你更好地控制可见性，命名错误...检查生成的代码。

## <a name="exposing-a-chunkier-api"></a>公开 chunkier API

没有向转换从本机支付的价格为托管 （和后）。 在这种情况下，它是更好的做法公开_而不是琐碎块式_Api 的本机的开发人员，例如

**聊天式**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**块式**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

由于的转换数较小的性能将会更好。 它还需要更少的代码生成，因此这将生成较小的本机库以及。

## <a name="naming"></a>命名

命名事项是两个最难问题在计算机科学中其他人正在缓存失效和关闭-通过-1 错误之一。 希望.NET 嵌入可以屏蔽你从以外的所有命名。

### <a name="types"></a>类型

Objective C 不支持命名空间。 一般情况下，其类型以作为前缀 2 (for Apple) 或 3 （作为第三方） 字符前缀，如`UIView`UIKit 的视图，这表示框架。

.NET 类型的跳过命名空间不是可能会重复，或使人眼花缭乱，名称。 这样现有的.NET 类型很长例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

将像使用：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

但是你可以重新公开为的类型：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

使其更多 Objective C 友好若要使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

更好的.NET 名称可能不适合于 OBJECTIVE-C 的 API。

Objective C 中的命名约定是不同于.NET （而不是 pascal 大小写，更详细 camel 大小写）。
请阅读[针对 Cocoa 编码准则](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

OBJECTIVE-C 的开发人员的角度来看，具有的方法从`Get`前缀意味着您已经不拥有该实例，即[获取规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名规则.NET GC 世界; 具有不匹配具有的.NET 方法`Create`前缀将在.NET 中的相同行为。 但是，对于 OBJECTIVE-C 的开发人员而言，这通常意味着你拥有的返回的实例，即[创建规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>异常

是在.NET 中使用广泛报告错误的例外情况非常常见的。 但是，它们是慢速和不很完全相同目标。 只要有可能应从 OBJECTIVE-C 的开发人员来隐藏它们。

例如，.NET`Try`模式将可以更轻松地 Objective C 代码中使用：

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

与

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>内的异常 `init*`

.NET 中的构造函数必须成功并返回 (_希望_) 有效实例或引发异常。

Objective C 的允许与此相反，`init*`返回`nil`无法创建实例时。 这是在许多 Apple 的框架中使用的常见，但不是一般模式。 在某些其他情况下`assert`可以发生这种情况 （和终止当前进程）。

生成器应遵循相同`return nil`模式生成`init*`方法。 如果引发托管的异常，则它将打印 (使用`NSLog`) 和`nil`将返回到调用方。

## <a name="operators"></a>运算符

Objective C 不允许运算符重载一样 C#，所以这些转换为类选择器。

["友好的"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)命名的方法生成优先运算符重载何时找到，并会产生一个更易于使用 API。

将覆盖的运算符的类`==`和/或`!=`应重写标准的 Equals （对象） 方法。
