---
title: Objective C 的支持
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 74d62121e9c99061c118f3ab85c27328ca950b9d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="objective-c-support"></a>Objective C 的支持

## <a name="specific-features"></a>特定功能

ObjC 生成具有数据类型值得注意几个、 特殊功能。

### <a name="automatic-reference-counting"></a>自动引用计数

使用的自动引用计数 (ARC) 是**必需**调用生成的绑定。 使用基于 embeddinator 的库项目都必须使用编译`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支持

公开的 Api`System.String`类型转换为`NSString`。 这样，内存管理更容易比处理`char*`。

### <a name="protocols-support"></a>协议支持

托管的接口转换为 ObjC 协议，其中所有成员都是`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 协议支持

默认情况下我们假定的默认哈希处理和相等性.net 和 ObjC 运行时是正常且可互换它们共享非常相似的语义。

当托管的类型的重写`Equals(Object)`或`GetHashCode`则这通常意味着的情况下 (.NET) 行为不是最佳的一个。 我们可以假定默认 OBJECTIVE-C 的行为将不是。

在这种情况下生成器的替代[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法和[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)中定义的属性[`NSObject`协议](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 这样，要 ObjC 代码中以透明方式使用的自定义托管的实现。

### <a name="comparison"></a>比较

托管类型实现`IComparable`或者它是泛型版本`IComparable<T>`将生成返回的 ObjC 友好方法`NSComparisonResult`并接受`nil`自变量。 这样生成的 API 更友好 ObjC 开发人员例如

```csharp
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>类别

托管的扩展方法将转换为类别。 例如以下扩展方法在`Collection`:

```csharp
    public static class SomeExtensions {

        public static int CountNonNull (this Collection collection) { ... }

        public static int CountNull (this Collection collection) { ... }
    }
```

将创建与此类似的 OBJECTIVE-C 的类别：

```csharp
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;
@end
```

当单个托管类型扩展几种类型，则生成多个 OBJECTIVE-C 的类别。

### <a name="subscripting"></a>下标

托管索引的属性转换为对象下标。 例如：

```csharp
    public bool this[int index] {
        get { return c[index]; }
        set { c[index] = value; }
    }
```

将创建类似于 OBJECTIVE-C 的：

```csharp
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

这可以通过 OBJECTIVE-C 的下标语法来使用：

```csharp
    if ([intCollection [0] isEqual:@42])
        intCollection[0] = @13;
```

根据你的索引器的类型，在适当的位置，则将生成索引或键控的下标。

这[文章](http://nshipster.com/object-subscripting/)是下标的极佳介绍。

## <a name="main-differences-with-net"></a>使用.NET 的主要差异

### <a name="constructors-vs-initializers"></a>构造函数 vs 初始值设定项

在目标-C 中，你可以调用任意初始值设定项的任何父类继承链中的原型除非它被标记为不可用 (NS_UNAVAILABLE)。

在 C# 中必须显式声明的类中的构造函数成员，这意味着不会继承构造函数。

若要公开的 C# API 对 Objective C 的正确表示，我们将添加`NS_UNAVAILABLE`到中不存在子类从父类任何初始值设定项。

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

```objectivec
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在这里我们可以看到`initWithId:`已标记为不可用。

### <a name="operator"></a>运算符

ObjC 不支持运算符重载一样 C#，因此运算符转换为类选择器：

```csharp
    public static AllOperators operator + (AllOperators c1, AllOperators c2)
    {
        return new AllOperators (c1.Value + c2.Value);
    }
```

更改为

```csharp
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

但是，某些.NET 语言不支持运算符重载，因此通常以便也包括["友好的"](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)名为除了运算符重载的方法。

如果运算符版本和"友好的"版本找到，将生成只有友好的版本，因为它们不会生成相同的 objective-c 的名称。

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

```csharp
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>相等运算符

在常规运算符 = = 中作为一个常规运算符设为上述处理 C#。

但是，如果"友好的"等号运算符找到，则这两个运算符 = = 和运算符 ！ = 将在生成中跳过。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

从[NSDate 的](https://developer.apple.com/reference/foundation/nsdate?language=objc)文档：

> NSDate 对象将封装在时间，独立于任何特定 calendrical 系统和时区中的单个点。 Date 对象是不可变，表示相对于绝对引用日期的固定时间间隔 (00: 00:00 UTC 上 2001 年 1 月 1)。

由于`NSDate`引用日期，它之间的所有转换和`DateTime`UTC，必须完成。

#### <a name="datetime-to-nsdate"></a>从 DateTime 到 NSDate

从转换时`DateTime`到`NSDate`的日期时间`Kind`属性考虑在内。

|类型|结果                                                                                            |
|---|---|
|Utc|使用提供执行的转换`DateTime`对象原样。|
|本地|调用`ToUniversalTime()`在提供`DateTime`对象用于转换。|
|未指定|提供`DateTime`对象被假定为 UTC，因此相同的行为类型 = = Utc。|

转换是通过使用以下公式：

> [!NOTE]
> **TimeInterval** = DateTimeObjectTicks - NSDateReferenceDateTicks[dt] / [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx)

一旦我们有 TimeInterval，我们使用 NSDate 的[dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)选择器来创建它。

#### <a name="nsdate-to-datetime"></a>NSDate 为 DateTime

从 NSDate 转到 DateTime 我们假定我们获得 NSDate 即引用日期的实例是**2001 年 1 月 1 上的 00:00:00 UTC**并使用以下公式：

> [!NOTE]
> **DateTimeTicks** = NSDateTimeIntervalSinceReferenceDate * [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx) + NSDateReferenceDateTicks[dt]

一旦我们计算**DateTimeTicks**我们使用以下的 datetime 数据类型[构造函数](https://msdn.microsoft.com/en-us/library/w0d47c9c(v=vs.110).aspx)设置其`kind`到`DateTimeKind.Utc`。

有必须要注意的一些注意事项，可以为 NSDate `nil` DateTime 是.NET 中的一个结构，但它不能是定义`null`。 如果你向提供`nil`NSDate 我们会将其转换为默认日期时间值映射到`DateTime.MinValue`。

MinValue 和 MaxValue 也是不同的 NSDate 可以支持比 DateTime 的更高和较低的边界，因此，每当您授予更大或更低的值时我们会将其设置为 DateTime 的[MaxValue](https://msdn.microsoft.com/en-us/library/system.datetime.maxvalue(v=vs.110).aspx)或[MinValue](https://msdn.microsoft.com/en-us/library/system.datetime.minvalue(v=vs.110).aspx)分别。

**dt**: NSDate 引用日期**2001 年 1 月 1 上的 00:00:00 UTC** => `new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;`
