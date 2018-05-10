---
title: 统一的 API
description: 新样式的 API 可以比以往更容易地 Mac 和 iOS 以及从而可以支持具有相同的 32 和 64 位应用程序二进制间共享代码。
ms.prod: xamarin
ms.assetid: 14311617-1BC2-42CC-AF3F-9F97733EE2D0
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: bee477a8e84e8cfdd20e89b7ca51c1ac40c0ee52
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="unified-api"></a>统一的 API

_新样式的 API 可以比以往更容易地 Mac 和 iOS 以及从而可以支持具有相同的 32 和 64 位应用程序二进制间共享代码。_

可改善 Mac 和 iOS 之间共享的代码和用于让开发人员能够使用单个代码库，在适用于 32 和 64 位，尽早 2015年我们引入了一个新的 API 调用统一 API 的 Xamarin.Mac 和 Xamarin.iOS 产品中。

> [!IMPORTANT]
> **经典的配置文件弃用：** Xamarin.iOS 中添加新的平台时我们开始逐渐否决从经典的配置文件 (monotouch.dll) 的功能。 例如，已删除非 NRC （新 ref 计数） 选项。 NRC 始终已为所有统一应用程序启用 （即非 NRC 永远不会是一个选项），并且有任何已知的问题。 将来的版本将删除作为垃圾回收器使用 Boehm 的选项。 这也是永远不会对统一应用程序可用的选项。 完全删除的经典支持用于下一步回退 Xamarin.iOS 10.0 的发布计划。

## <a name="overviewoverviewmd"></a>[概述](overview.md)

介绍统一 API 背后的基本原理，并说明在详细信息与经典 API 差异。 请参阅此页后，可以了解已在统一 API 中进行的更改。

## <a name="updating-classic-api-based-apps"></a>更新经典基于 API 的应用

按照你的平台的相关说明：

- [更新现有应用](updating-apps.md)
- [更新现有 iOS 应用](updating-ios-apps.md)
- [更新现有 Mac 应用](updating-mac-apps.md)
- [更新现有 Xamarin.Forms 应用](updating-xamarin-forms-apps.md)
- [将绑定迁移到 Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[将代码更新为 Unified API 的提示](updating-tips.md)

而不考虑哪些应用程序进行迁移，请查看[这些提示](updating-tips.md)帮助你成功更新到统一的 API。

## <a name="library-split"></a>库拆分

从该点开始，我们的 Api，不会出现两种方式：

-  **经典 API:** 限制为 32 位 （仅限） 和中公开`monotouch.dll`和`XamMac.dll`程序集。
-  **统一的 API:** 支持使用单个 API 中可用的 32 和 64 位开发`Xamarin.iOS.dll`和`Xamarin.Mac.dll`程序集。

这意味着，企业开发人员 （不是针对应用商店），你可以继续使用现有的经典 Api，因为我们将保留维护它们永久，或你可以升级到新的 Api。

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Namespace 更改

若要减少的摩擦我们 Mac 和 iOS 的产品之间共享代码，我们在产品中的 Api 更改为命名空间。

我们将要删除前缀"MonoTouch"从我们的 iOS 产品和"MonoMac"从我们 Mac 产品上的数据类型。

这使得它更简单，而无需进行条件编译的 Mac 和 iOS 平台之间共享代码，并将降噪在源代码文件的顶部。

-  **经典 API:** 命名空间使用`MonoTouch.`或`MonoMac.`前缀。
-  **统一的 API:** 没有命名空间前缀

## <a name="api-changes"></a>API 更改

统一的 API 中删除不推荐使用的方法，并且有少数情况下存在拼写错误时所处的 API 名称它们绑定到原始 MonoTouch 和 MonoMac 命名空间经典 Api 中的位置。 这些实例中新的统一 Api 已经得到更正，并且将需要更新组件、 iOS 和 Mac 应用程序中。 下面是可能会遇到的最常见事件列表：

|经典 API 方法名称|统一的 API 方法名称|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

有关从经典切换到统一 API 时的更改的完整列表，请参阅我们[经典 (monotouch.dll) vs 统一 (Xamarin.iOS.dll) API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)文档。

## <a name="updating-to-unified"></a>更新到统一

中的多个旧/中断/弃用 API**经典**中均不提供**统一**API。 它可以更方便地修复`CS0616`警告再启动你 （手动或自动） 升级由于你必须`[Obsolete]`属性消息 （该警告的一部分） 以指导你找到正确的 API。

请注意，我们正在发布[*差异*](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)在经典 vs 统一之前或之后您的项目更新可以使用的 API 更改。 仍修复废弃经典将调用通常将节省了时间 （小于文档查找）。

按照这些说明[更新现有的 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)，或[Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)到统一的 API。
查看此页上的其余部分和[这些提示](~/cross-platform/macios/unified/updating-tips.md)有关迁移你的代码的其他信息。

### <a name="nuget"></a>NuGet

以前支持通过经典 API Xamarin.iOS 的 NuGet 包发布及其使用的程序集**Monotouch10**平台名字对象。

统一 API 引入了新的平台标识符兼容包的**Xamarin.iOS10**。 现有的 NuGet 包将需要更新以添加对此平台支持统一 API 针对生成的。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在同一 Xamarin.iOS 项目中包含 monotouch.dll 和 Xamarin.iOS.dll-Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 xxx，版本 = 0.0.000，区域性 = neutral，PublicKeyToken = null'"_ 后转换到统一 Api 应用程序，它通常是因为尚未更新到统一 API 的项目中采用的组件或 NuGet 包。 你将需要删除现有的组件/NuGet，更新到版本支持统一 Api 并执行一个干净的生成。

### <a name="the-road-to-64-bits"></a>为 64 位道路

有关背景支持 32 和 64 位应用程序和有关框架的信息请参阅[32 位和 64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)。

 <a name="new-data-types" />

#### <a name="new-data-types"></a>新数据类型

在差异的核心，Mac 和 iOS Api 使用的特定于体系结构的数据类型都始终在 32 位平台上和 64 位平台上的 64 位的 32 位。

例如，Objective C 将映射`NSInteger`数据类型设置为`int32_t`在 32 位系统上并且为`int64_t`在 64 位系统上。

若要匹配此行为，请在我们统一的 API，我们正在替换以前使用的`int`(.NET 中其定义为始终`System.Int32`) 到新的数据类型： `System.nint`。  你可以将"n"是有意义"本机"，因此，本机整数键入的平台。

我们引入了`nint`，`nuint`和`nfloat`在必要时还提供了数据类型建立在它们之上。

若要了解有关这些数据类型更改的详细信息，请参阅[本机类型](~/cross-platform/macios/nativetypes.md)文档。

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>如何检测 iOS 应用的体系结构

可能情况下你的应用程序需要知道它正在运行 32 位或 64 位 iOS 系统上。 下面的代码可以用于检查体系结构：

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>数组和 System.Collections.Generic

因为 C# 索引器需要一种`int`，你将需要显式强制转换`nint`值复制到`int`访问集合或数组中的元素。 例如：

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

这是预期的行为，因为从强制转换`int`到`nint`是在 64 位上有损，隐式转换不执行操作。

### <a name="converting-datetime-to-nsdate"></a>将日期时间转换为 NSDate

使用统一的 Api，隐式转换时`DateTime`到`NSDate`不再执行值。 这些值将需要显式转换从一种类型到另一个。 可以使用以下的扩展方法来自动执行此过程：

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>弃用的 Api 和拼写有误

内部 Xamarin.iOS 经典 API (monotouch.dll)`[Obsolete]`属性使用两个不同的方式：

-  **不推荐使用 iOS API:** Apple 提示你停止使用一个 API，因为它已被一个更新的版本被取代时，这是。 经典 API 是仍正常通常是必需 （如果支持 iOS 的较旧版本）。
 此类 API (和`[Obsolete]`属性) 都将包括新的 Xamarin.iOS 程序集。
-  **不正确的 API**某些 API 具有上其名称拼写错误。

我们的原始程序集 （monotouch.dll 和 XamMac.dll） 保持可用的兼容性的旧代码，但它们已从 （Xamarin.iOS.dll 和 Xamarin.Mac） 中的统一 API 程序集

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject 子类.ctor(IntPtr)

每个`NSObject`子类具有构造函数接受的`IntPtr`。 这是如何我们可以实例化的新的托管的实例，从本机 ObjC 句柄。

这是在经典`public`构造函数。 但这样做容易误用在用户代码中的此功能，例如，创建多个托管实例的单个 ObjC 实例*或*创建的托管的实例将缺少所需的托管的状态 （针对子类）。

若要避免这些类型的问题`IntPtr`构造函数现`protected`中**统一**API，用于仅针对子类化。 这将确保更正/安全 API 用于从句柄，即创建托管的实例

    var label = Runtime.GetNSObject<UILabel> (handle);

此 API 将返回现有的托管的实例 （如果它已存在） 或者将创建一个新 （如果需要）。 已在经典且统一的 API 中可用。

请注意，`.ctor(NSObjectFlag)`现还`protected`但外部子类化很少使用它。

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction 替换操作

与统一的 Api，`NSAction`已删除为支持标准.NET `Action`。 这是一个重大改进，因为`Action`是通用的.NET 类型，而`NSAction`只特定于 Xamarin.iOS。 它们都执行完全相同的操作，但它们也是独特的不兼容的类型并生成了更多代码无需编写来实现相同的结果。

例如，如果你现有的 Xamarin 应用程序包含以下代码：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
它现在可以替换的简单 lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

以前，则不编译器错误，因为`Action`不能分配给`NSAction`，但由于`UITapGestureRecognizer`现在将`Action`而不是`NSAction`统一 Api 中有效。

### <a name="custom-delegates-replaced-with-actiont"></a>替换操作的自定义委托<T>

在**统一**一些简单易学 （例如一个参数） 已替换为.net 委托`Action<T>`。 例如，

    public delegate void NSNotificationHandler (NSNotification notification);

现在可以用作`Action<NSNotification>`。 此升级代码重用，并减少代码在 Xamarin.iOS 和自己的应用程序内的重复。

### <a name="taskbool-replaced-with-taskbooleannserror"></a>任务<bool>替换为 < 布尔型、 NSError >> 的任务

在**经典**没有某些异步 Api 返回`Task<bool>`。 但一些其中是时要使用`NSError`即已签名的一部分`bool`已`true`且你必须捕获异常来获取`NSError`。

因为某些错误都很常见，并且返回值不为有用在中更改此模式**统一**返回`Task<Tuple<Boolean,NSError>>`。 这允许你检查成功和异步调用过程中无法具有发生任何错误。

### <a name="nsstring-vs-string"></a>NSString vs 字符串

在某些情况下某些常量必须从更改`string`到`NSString`，例如 `UITableViewCell`

**经典**

    public virtual string ReuseIdentifier { get; }

**统一**

    public virtual NSString ReuseIdentifier { get; }

一般情况下我们希望.NET`System.String`类型。 但是，某些本机 API Apple 准则，尽管比较常量指针 （而不是字符串本身） 和这只能处理时我们公开作为常量`NSString`。

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Objective C 协议

原始 MonoTouch 没有非最佳完全支持 ObjC 协议而有些，，已添加 API 以支持最常见的方案。 此限制不不再存在，但保留了几个 Api 是为了向后兼容，内部`monotouch.dll`和`XamMac.dll`。

已删除并在统一 Api 上清理这些限制。 大多数更改将如下所示：

**经典**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**统一**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

`I`前缀意味着**统一**公开的接口，而不是特定类型，ObjC 协议。 这将轻松地在你不希望子类化 Xamarin.iOS 提供的特定类型的情况。

它还允许将一些 API，以使其更具体和更易于使用，例如：

**经典**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**统一**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

此类 API 现在更轻松地 us，而无需与文档，并且你 IDE 代码完成将为你提供更有用的建议基于协议/接口。

#### <a name="nscoding-protocol"></a>NSCoding 协议

我们原始绑定包含的每种类型的.ctor(NSCoder)，即使它不支持`NSCoding`协议。  单个`Encode(NSCoder)`方法中已存在`NSObject`进行编码对象。
但如果该实例符合 NSCoding 协议，此方法将仅会起作用。

统一的 api 中，我们已解决这。  新程序集将仅具有`.ctor(NSCoder)`类型是否符合`NSCoding`。 这些类型现在还有`Encode(NSCoder)`方法，这符合`INSCoding`接口。

低影响： 在大多数情况下此更改不会影响应用程序如无法使用旧的、 将其删除，构造函数。

## <a name="further-tips"></a>更多提示

中列出了其他更改，需要注意的[的更新应用到统一 API 提示](~/cross-platform/macios/unified/updating-tips.md)。

## <a name="sample-code"></a>代码示例

截至 7 月 31 日前中，我们已发布到这个新的 API 的 iOS 示例的端口上`magic-types`分支在[monotouch 示例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

适用于 Mac，我们将检查中同时示例[mac 示例](https://github.com/xamarin/mac-samples)（Mavericks/Yosemite 中显示新的 Api） 的存储库，以及幻类型分支中的 32/64 位示例[mac 示例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。
-->

## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](updating-ios-apps.md)
- [更新 Mac 应用程序](updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](updating-xamarin-forms-apps.md)
- [更新绑定](update-binding.md)
- [更新提示](updating-tips.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
