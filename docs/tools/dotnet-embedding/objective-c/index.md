---
title: Objective C 的支持
description: 本文档提供对在.NET 嵌入的 Objective C 的支持的说明。 它讨论自动引用计数、 NSString、 协议、 NSObject 协议、 异常和的详细信息。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 95604133293f0fb2fe9b651fd7cb6b18f3994c84
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793846"
---
# <a name="objective-c-support"></a>Objective C 的支持

## <a name="specific-features"></a>特定功能

Objective C 的生成具有数据类型值得注意的几个特殊功能。

### <a name="automatic-reference-counting"></a>自动引用计数

使用的自动引用计数 (ARC) 是**必需**调用生成的绑定。 使用基于.NET 嵌入的库项目都必须使用编译`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支持

公开的 Api`System.String`类型转换为`NSString`。 这使内存管理比时处理更容易`char*`。

### <a name="protocols-support"></a>协议支持

托管的接口转换为 OBJECTIVE-C 的协议，其中所有成员都是`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 协议支持

默认情况下，假定的默认哈希和相等性.NET 和 Objective C 运行时是可互换，因为它们共享相似的语义。

当托管的类型的重写`Equals(Object)`或`GetHashCode`，它通常意味着默认 (.NET) 行为未足够; 这意味着默认 OBJECTIVE-C 的行为是可能足够任一。

在这种情况下，生成器的替代[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法和[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)中定义的属性[`NSObject`协议](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 这样，要 Objective C 代码中以透明方式使用的自定义托管的实现。

### <a name="exceptions-support"></a>异常支持

传递`--nativeexception`的自变量作为`objcgen`会将托管的异常转换为 Objective C 异常可以捕获和处理。 

### <a name="comparison"></a>比较

托管类型实现`IComparable`(或其泛型版本`IComparable<T>`) 将生成返回的 OBJECTIVE-C 友好方法`NSComparisonResult`并接受`nil`自变量。 这使得生成的 API OBJECTIVE-C 的开发人员更友好。 例如：

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>类别

托管的扩展方法将转换为类别。 例如，以下扩展方法在`Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

将创建与此类似的 OBJECTIVE-C 的类别：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

当单个托管的类型扩展几种类型时，将生成多个 OBJECTIVE-C 的类别。

### <a name="subscripting"></a>下标

托管索引的属性转换为对象下标。 例如：

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

将创建类似于 OBJECTIVE-C 的：

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

这可以通过 OBJECTIVE-C 的下标语法来使用：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

根据你的索引器的类型，在适当的位置，则将生成索引或键控的下标。

这[文章](http://nshipster.com/object-subscripting/)是下标的极佳介绍。

## <a name="main-differences-with-net"></a>使用.NET 的主要差异

### <a name="constructors-vs-initializers"></a>构造函数 vs 初始值设定项

在目标-C 中，你可以调用任意初始值设定项的任何父类继承链中的原型除非它被标记为不可 (`NS_UNAVAILABLE`)。

在 C# 中必须显式声明一个类，这意味着不会继承构造函数内的构造函数成员。

公开到 OBJECTIVE-C 的 C# API 的右表示`NS_UNAVAILABLE`添加到中不存在子类从父类任何初始值设定项。

C# API:

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

Objective C 中加以表示 API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在这里，`initWithId:`已标记为不可用。

### <a name="operator"></a>运算符

Objective C 不支持运算符重载一样 C#，因此运算符转换为类选择器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

更改为

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

但是，某些.NET 语言不支持运算符重载，因此通常以便也包括["友好的"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)名为除了运算符重载的方法。

如果运算符版本和"友好的"版本找到，将生成只有友好的版本，因为它们不会生成相同的 OBJECTIVE-C 的名称。

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

会成为：

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>相等运算符

在常规运算符`==`在 C# 中会被视为常规运算符如上所示。

但是，如果"友好的"等号运算符找到，则这两个运算符`==`和运算符`!=`将在生成中跳过。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

从[ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc)文档：

> `NSDate` 对象将封装在时间，独立于任何特定 calendrical 系统和时区中的单个点。 Date 对象是不可变，表示相对于绝对引用日期的固定时间间隔 (00: 00:00 UTC 上 2001 年 1 月 1)。

由于`NSDate`引用日期，它之间的所有转换和`DateTime`UTC，必须完成。

#### <a name="datetime-to-nsdate"></a>从 DateTime 到 NSDate

从转换时`DateTime`到`NSDate`、`Kind`属性`DateTime`考虑：

|类型|结果|
|---|---|
|`Utc`|使用提供执行的转换`DateTime`对象原样。|
|`Local`|调用`ToUniversalTime()`在提供`DateTime`对象用于转换。|
|`Unspecified`|提供`DateTime`对象被假定为 UTC，因此相同的行为时`Kind`是`Utc`。|

转换使用以下公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式中： 

- `NSDateReferenceDateTicks` 根据进行计算`NSDate`2001 年 1 月 1 引用的 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 上定义 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要创建`NSDate`对象，`TimeInterval`用于`NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)选择器。

#### <a name="nsdate-to-datetime"></a>NSDate 为 DateTime

从转换`NSDate`到`DateTime`使用以下公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式中： 

- `NSDateReferenceDateTicks` 根据进行计算`NSDate`2001 年 1 月 1 引用的 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 上定义 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

后计算`DateTimeTicks`、 `DateTime` [构造函数](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)调用时，设置其`kind`到`DateTimeKind.Utc`。

> [!NOTE]
> `NSDate` 可以是`nil`，但`DateTime`是在.NET 中，后者的定义不能为一个结构`null`。 如果你向提供`nil` `NSDate`，它将转换为默认值`DateTime`值，该值映射到`DateTime.MinValue`。

`NSDate` 支持更高版本的最大值和低于最小值`DateTime`。 从转换时`NSDate`到`DateTime`，这些更高版本和较低的值更改为`DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue)或[MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)分别。
