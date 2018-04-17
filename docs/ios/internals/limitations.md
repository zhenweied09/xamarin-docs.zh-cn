---
title: 限制
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/09/2018
ms.openlocfilehash: 8bd4ce464adf316517e2e1f2299006913bc68736
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="limitations"></a>限制

由于使用 Xamarin.iOS iPhone 上的应用程序编译为静态的代码，则不可以使用任何需要在运行时代码生成的功能。

这些是相比桌面 Mono Xamarin.iOS 限制：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型的支持

与传统 Mono/.NET 中，而不是由 JIT 编译器正在编译按需提前静态编译 iPhone 上的代码。

Mono 的[完整 AOT](http://www.mono-project.com/docs/advanced/aot/#full-aot)技术具有泛型方面的一些限制，因为在编译时提前确定不是每个可能的泛型实例化，将造成这些错误。 这不是正则.NET 或 Mono 运行时的问题，因为时间编译器中使用只在运行时始终编译代码。 但这会带来的静态的编译器，例如 Xamarin.iOS 一个难题。

一些开发人员遇到的常见问题包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>泛型 NSObjects 子类不能超过

Xamarin.iOS 当前具有对有限的支持创建 NSObject 类，如不支持泛型方法的泛型子类。 截至 7.2.1，使用的 NSObjects 泛型子类是可行的如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 可能的 NSObjects 泛型子类时，有几个限制。 读取[的 NSObject 泛型子类](~/ios/internals/api-design/nsobject-generics.md)文档的详细信息



### <a name="pinvokes-in-generic-types"></a>P/调用中的泛型类型

P/Invokes 泛型类中不受支持：

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>不支持可以为 Null 的类型上的 Property.SetInfo

使用反射的 Property.SetInfo 将值设置上一个可以为 Null&lt;T&gt;当前不支持。

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>作为字典键的值类型

值类型用作一个字典&lt;TKey，&gt;密钥会产生问题，默认值为字典构造函数会尝试使用 EqualityComparer&lt;TKey&gt;。默认值。 EqualityComparer&lt;TKey&gt;。默认情况下，反过来，尝试使用反射来实例化一个新的类型，这将实现 IEqualityComparer&lt;TKey&gt;接口。

这适用于引用类型 (为反射 + 创建一个新跳过类型步骤)，但对于值崩溃的类型和后你尝试在设备上使用它而是快速加深。

 **解决方法**： 手动实现[IEqualityComparer&lt;TKey&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/)接口中的新类型，并提供到该类型的实例[字典&lt;TKey，TValue&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%3CTKey,TValue%3E/) [(IEqualityComparer&lt;TKey&gt;)](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/)构造函数。


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>没有生成动态代码

由于 iPhone 的内核防止应用程序动态生成代码在 iPhone 上的 Mono 不支持任何形式的动态代码生成。 这些方法包括：

-  System.Reflection.Emit 不可用。
-  System.Runtime.Remoting 不支持。
-  不支持动态创建的类型 (没有 Type.GetType ("MyType"1"))，尽管查找现有类型 (Type.GetType ("System.String") 例如，正常工作)。 
-  在编译时，必须向运行时注册反向回调。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

System.Reflection 缺乏。 **发出**意味着没有取决于运行时代码生成的代码将起作用。 这包括的任务等：

-  动态语言运行时。
-  任何基于动态语言运行时的语言。
-  远程处理的 TransparentProxy 或任何其他会导致运行时动态生成代码的内容。 


 **重要说明：**不要混淆**Reflection.Emit**与**反射**。 Reflection.Emit 即将动态生成代码，并且具有该代码 JITed 和编译为本机代码。 由于 iPhone （没有 JIT 编译） 上的限制不是支持此。

但是，整个反射 API，包括 Type.GetType ("someClass") 列出方法，列出提取属性和值的属性，就可以正常工作。

### <a name="using-delegates-to-call-native-functions"></a>使用委托来调用本机函数

若要调用本机函数通过 C# 委托，必须使用以下属性之一修饰该委托的声明：

- [UnmanagedFunctionPointerAttribute](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute/) （首选，因为它是跨平台和与.NET 标准 1.1 + 兼容）
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

无法向其中一个属性提供将导致运行时错误，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向回调

在标准 Mono 很可能将 C# 委托实例传递给非托管代码而不是函数指针。 运行时通常会将这些函数指针转换为一个小的 thunk，用于允许回调到托管代码的非托管的代码。

在 Mono 中这些桥由实时编译器。 当使用预的时间编译器所需 iPhone 此时有两个重要的限制：

-  您必须标记所有回叫方法与[MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  方法必须是静态方法，则不支持的实例方法。 
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>没有远程处理

远程处理堆栈不在 Xamarin.iOS 上可用。


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>运行时禁用功能

已在 Mono 的 iOS 运行时中禁用以下功能：

-  探查器
-  Reflection.Emit
-  Reflection.Emit.Save 功能
-  COM 绑定
-  JIT 引擎
-  元数据验证程序 （由于没有任何 JIT）


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>.NET API 限制

.NET API 公开为不是所有内容都是可用在 iOS 中，将完整的框架的一个子集。 请参阅有关常见问题[的当前支持的程序集列表](~/cross-platform/internals/available-assemblies.md)。



具体而言，使用 Xamarin.iOS 的 API 配置文件不包括 System.Configuration，因此不能使用外部 XML 文件来配置运行时的行为。
