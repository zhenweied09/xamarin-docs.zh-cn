---
title: 目标 Sharpie 发布历史记录
description: 本文档介绍目标 Sharpie，用于自动创建的 C# 绑定到 Objective C 代码的工具的版本历史记录。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: a173df769068a3834dfdc314675254af1f3594ed
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781726"
---
# <a name="objective-sharpie-release-history"></a>目标 Sharpie 发布历史记录

## <a name="34-october-11-2017"></a>3.4 (2017 年 10 月 11 日)

[下载 v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支持的 Xcode 9: iOS 11，macOS 10.13，tvOS 11 中，和 watchOS 4
* 现在应修复 SIMD 和 tgmath 问题
* 已完全删除遥测

## <a name="33-august-3-2016"></a>3.3 (2016 年 8 月 3 日)

[下载 v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 10 和 watchOS 3 Xcode 8 Beta 4、 iOS 10、 macOS 10.12、 tvOS 的支持。
* 更新为最新 Clang master 生成 (2016年-08-02)
* [保留遥测提交选项](https://twitter.com/Symbiatch/status/760373403878559744)从`sharpie pod bind`到`sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 (2016 年 6 月 14 日)

[下载 v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 10 和 watchOS 3 Xcode 8 Beta 1、 iOS 10、 macOS 10.12、 tvOS 的支持。

## <a name="31-may-31-2016"></a>3.1 （2016 年 5 月 31）

[下载版本 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* 对 CocoaPods 1.0 的支持
* 通过第一个构建本机改进 CocoaPods 绑定可靠性`.framework`，然后将该绑定
* 复制 CocoaPods`.framework`和绑定定义流入`Binding`目录以简化与 Xamarin.iOS 和 Xamarin.Mac 绑定项目的集成

## <a name="30-october-5-2015"></a>3.0 （于 2015 年 10 月 5 日）

[下载 v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 对包括轻型泛型和可为 null，因为在 Xcode 7 中引入了新 Objective C 语言功能的支持
* 适用于最新的 iOS 和 Mac Sdk 的支持。
* 生成和分析支持的 Xcode 项目 ！ 现在，你可以将完整的 Xcode 项目传递到目标 Sharpie 以及它将做了最大努力找出最右侧的操作 (例如`sharpie bind Project.xcodeproj -sdk ios`)。
* [CocoaPods](https://cocoapods.org)支持 ！ 现在可以配置、 生成和直接从目标 Sharpie 绑定 CocoaPods (例如`sharpie pod init ios AFNetworking && sharpie pod bind`)。
* 如果框架支持它们，从而导致最正确分析一个框架，由于框架的结构定义的当绑定框架时，将启用 Clang 模块其`module.modulemap`。
* 对于生成 framework 产品的 Xcode 项目，如 Xcode 项目中的非 framework 目标可能仍有不能为自动解决歧义问题分析而不是中间产品目标该产品。

## <a name="216-march-17-2015"></a>2.1.6 （于 2015 2015年 3 月 17日日)

[下载 v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 固定的二元运算符的表达式绑定： 表达式的左侧被错误地与其右侧 (例如`1 << 0`被错误地绑定为`0 << 1`)。 感谢到 Adam Kemp 注意到这 ！
* 修复的问题`NSInteger`和`NSUInteger`绑定为`int`和`uint`而不是`nint`和`nuint`上 i386;`-DNS_BUILD_32_LIKE_64`现在已传递给要分析的 Clang `objc/NSObjCRuntime.h` i386 上按预期工作。
* Mac OS X Sdk 的默认体系结构 (例如`-sdk macosx10.10`) 是现在 x86_64 而不是 i386，因此`-arch`除非需要重写默认值，则可以省略。

## <a name="210-march-15-2015"></a>2.1.0 （于 2015 2015年 3 月 15日日)

[下载 v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc #27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849)： 确保`using ObjCRuntime;`时生成`ArgumentSemantic`使用。
* [bxc #27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850)： 确保`using System.Runtime.InteropServices;`时生成`DllImport`使用。
* [bxc #27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852)： 默认`DllImport`加载符号从`__Internal`。
* [bxc #27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848)： 跳过前向声明 Objective C 容器声明。
* [bxc #27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846)： 绑定协议与作为具体接口的单一限定的类型 (`id<Foo>`作为`Foo`而不是`Foundation.NSObject<Foo>`)。
* [bxc #28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037)： 绑定`UInt32`， `UInt64`，和`Int64`作为文字`Int32`删除`u`和/或`uL`后缀时值可以安全地适合于`Int32`。
* [bxc #28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038)： 修复枚举的名称映射，在原始的本地名称开头`k`前缀。
* `sizeof` 其自变量类型不映射到 C# 基元类型的 C 表达式将 Clang 中进行计算，并可以绑定为整数文字，以避免生成无效 C#。
* 修复其类型是一个块的属性的 OBJECTIVE-C 的语法 （在上面绑定声明的注释中出现 Objective C 代码）。
* 绑定发生衰变类型为其原始类型 (`int[]`衰减到`int*`语义在分析期间 Clang 中，但将其绑定与原为编写`int[]`相反)。

很大程度到 Dave Dunkin 用于报告的许多此点版本中修复的 bug，谢谢 ！

## <a name="200-march-9-2015"></a>2.0.0: 2015 年 3 月 9 日

[下载 2.0.0 版](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目标 Sharpie 2.0 是功能的改进的基于 Clang 的驱动程序和分析器和新的基于 NRefactory 绑定引擎的主要发行版。 为提供这些改进的组件_很多_更好的绑定，特别是围绕类型绑定。 许多其他进行了改进的内部目标 Sharpie 其会生成在未来版本中的许多用户可见的功能。

目标 Sharpie 2.0 基于 Clang 3.6.1。

### <a name="type-binding-improvements"></a>类型绑定改进

* 现在支持 OBJECTIVE-C 的块。 这包括匿名/内联块和块通过名为`typedef`。 匿名块将可绑定为`System.Action`或`System.Func`委托，而将为强名称程序集绑定命名的块`delegate`类型。

* 没有的改进命名启发式对前面紧挨着的匿名枚举算法`typedef`如解析为 builtin 整型`long`或`int`。

* C 指针现在绑定与 C#`unsafe`而不是指针`System.IntPtr`。 这会导致更高时你可能想要打开指针参数转换的绑定中的清晰度`out`或`ref`参数。 不能始终推导出的参数是否应是`out`或`ref`，因此指针保留在绑定，以便更轻松审核。

* 上面的指针绑定到一个例外的情况指向 OBJECTIVE-C 的对象的 2 级别遇到作为参数。 在这些情况下，约定是主导和参数将绑定为`out`(例如`NSError **error`→ `out NSError error`)。

### <a name="verify-attribute"></a>验证特性。

你通常会发现生成的目标 Sharpie 绑定现在将使用批注`[Verify]`属性。 这些属性指示你应_验证_目标 Sharpie 通过比较与原始 C/Objective C 声明 （这将提供一条注释，该绑定的声明的上方） 绑定未正确操作。

验证_建议_所有绑定的声明，但很可能是_必需_声明与为`[Verify]`属性。 这是因为在许多情况下，没有足够的元数据的原始本机源代码中推断如何最好地生成绑定。 你可能需要引用文档或标头文件做出最佳的绑定决策内代码注释。

请参阅[验证属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)更多详细信息的文档。

### <a name="other-notable-improvements"></a>其他值得注意的改进

* `using` 现在，基于绑定的类型生成语句。 例如，如果`NSURL`被绑定，`using Foundation;`将以及生成语句。

* `struct` 和`union`声明将现在被绑定，使用`[FieldOffset]`墩联合的。

* 具有常量表达式初始值设定项的枚举值现在能正确地绑定;完整表达式转换为 C#。

* 现在绑定可变参数方法和块。

* 框架现在支持通过`-framework`选项。 在查看文档[绑定本机框架](http://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks)有关详细信息。

* OBJECTIVE-C 的源的代码将自动检测现在，这应不再需要传递`-ObjC`或`-xobjective-c`以手动 Clang。

* Clang 模块使用情况 (`@import`) 现已自动检测，这应不再需要传递`-fmodules`到 Clang 手动为使用 Clang 中的新的模块支持的库。

* Xamarin 统一 API 现在是默认的绑定目标;使用`-classic`选项可面向 32 位唯一经典 API。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修复

* 修复`instancetype`绑定时使用在 Objective C 的类别
* 完全名称 OBJECTIVE-C 的类别
* 前缀 OBJECTIVE-C 的协议与`I`(例如`INSCopying`而不是`NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 2014 年 12 月 21 日

[下载 v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

次要 bug 修补程序。

## <a name="111-december-15-2014"></a>1.1.1: 2014 年 12 月 15 日

[下载 v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 时的内部使用和在 2013 年 4 月以下目标 Sharpie 的初始预览版的 Xamarin 的开发 1.5 年后的第一个主版本。 此版本是通常被视为稳定状态并且可用于各种本机库，如果突出显示新的 Clang 后端的第一个。

