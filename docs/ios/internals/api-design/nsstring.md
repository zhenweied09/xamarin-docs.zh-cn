---
title: NSString
ms.topic: article
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: b52a9e926f5ec907746d2a2dd8ee7a6a6212742f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="nsstring"></a>NSString

使用 API 公开的本机.NET 字符串类型，通过调用从 Xamarin.iOS 和 Xamarin.Mac 的设计`string`、 以 C# 中的字符串操作和其他.NET 编程语言，并将字符串公开为而不是API公开的数据类型`NSString`数据类型。


这意味着在一种特殊类型的开发人员应该不需要保留旨在用于调入 Xamarin.iOS 和 Xamarin.Mac API （统一） 的字符串 (`Foundation.NSString`)，它们可以继续使用 Mono 的`System.String`在内的各种操作的所有和每当中的 api Xamarin.iOS 或 Xamarin.Mac 需要一个字符串，我们的 API 绑定负责封送处理信息。

例如，Objective C"text"上的属性`UILabel`类型的`NSString`，如下声明：

```csharp
@property(nonatomic, copy) NSString *text
```

这被公开 Xamarin.iOS 作为：

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在后台，此属性的实现将封送到的 C# 字符串`NSString`和调用`objc_msgSend`方式 Objective C 将完全相同的方法。

第三方 OBJECTIVE-C 的 api，也不会占用几`NSString`，但改为使用一个 C 字符串 ("*char*")。 在这些情况下，您仍可以使用 C# 字符串数据类型，但必须使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)属性以通知不应作为封送该字符串的绑定生成器`NSString`，但改成作为 C 字符串。

 <a name="Exceptions_to_the_Rule" />


## <a name="exceptions-to-the-rule"></a>规则的例外情况

在 Xamarin.iOS 和 Xamarin.Mac 中，我们做了此规则的例外。 之间时，我们公开决策`string`s，并当我们使 except 和公开`NSString`s，由如果`NSString`方法无法执行而不是内容比较指针比较。


这可能会发生时 OBJECTIVE-C 的 Api 使用公共`NSString`常量作为令牌表示一些操作，而不是比较字符串的实际内容。


在这些情况下，`NSString`公开 Api，并且有极少的具有此 Api。 你将注意到 NSString 属性都公开在一些类中。 那些`NSString`属性公开 for 通知等项目。 这些情况包括属性通常类似于：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

通知是密钥用于`NSNotification`类在你想要为特定事件所广播由运行时注册。

密钥通常如下所示：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

另一个位置其中`NSString`s 都公开在 API 为用作到在 iOS 中的某些 Api 或 OS X 获取的参数的令牌已`NSDictionary`对象作为参数。 字典通常包含`NSString`密钥。 Xamarin.iOS，按照约定，名称那些静态`NSString`加上的"密钥"名称的属性。
