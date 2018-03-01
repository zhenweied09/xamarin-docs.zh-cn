---
title: ".NET 嵌入错误"
ms.topic: article
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 90d30b92069bcd6a5c008fa8009c0392c4d26473
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="em0xxx-binding-error-messages"></a>EM0xxx： 绑定错误消息

例如 参数、 环境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->
<h3><a name="EM0000"/>EM0000： 意外的错误-请填写 https://github.com/mono/Embeddinator-4000/issues 在一个 bug 报告</h3>

出现意外的错误条件。 请[记录相应的问题](https://github.com/mono/Embeddinator-4000/issues)利用尽可能多的信息可能，其中包括：

* 完全生成具有最大详细级别的日志，
* 再现错误的最小测试用例
* 所有版本信息

获取确切的版本信息的最简单方法是使用**Xamarin Studio**菜单上，**有关 Xamarin Studio**项，**显示详细信息**按钮，然后复制/粘贴版本信息 (你可以使用**复制信息**按钮)。

<h3><a name="EM0001"/>EM0001： 无法创建输出目录 `X`</h3>

指定的目录名称`-o=DIR`不存在并且无法创建。 它可能是文件系统的名称无效。

<h3><a name="EM0002"/>EM0002： 选项`X`不支持</h3>

该工具不支持选项`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<h3><a name="EM0003"/>EM0003: 平台`X`无效。</h3>

该工具不支持该平台`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<h3><a name="EM0004"/>EM0004: 目标`X`无效。</h3>

该工具不支持目标`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<h3><a name="EM0005"/>EM0005： 编译目标`X`无效。</h3>

该工具不支持编译目标`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<h3><a name="EM0006"/>EM0006： 找不到 Xcode 位置。</h3>

该工具找不到当前所选的 Xcode 位置使用`xcode-select -p`命令。 请验证此命令成功，并返回正确的 Xcode 位置。

<h3><a name="EM0007"/>EM0007： 无法获取 {sdk} 的 sdk 版本。</h3>

该工具无法获取使用 SDK 版本`xcrun --show-sdk-version --sdk {sdk}`命令。 请验证此命令成功，并返回的 SDK 版本。

<h3><a name="EM0008"/>EM0008： 体系结构 {arch} 为 {平台} 无效。 适用于 {平台} 的有效体系结构是: {体系结构}。</h3>

错误消息中的体系结构对于目标平台无效。 请验证-abi 选项，将传递有效的体系结构。

<h3><a name="EM0009"/>EM0009: 功能`X`生成器当前未实现</h3>

这是我们想要的生成器的未来版本中修复的已知的问题。 贡献是欢迎。

<h3><a name="EM0010"/>EM0010： 无法合并框架 {simulatorFramework} 和 {deviceFramework}，因为文件 {file} 中同时存在。</h3>

该工具不能合并错误消息中提及的框架，因为它们之间没有公共文件。

这可能表示 Embeddinator-4000; 中的 bug在一个 bug 报告，请记录[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)与测试用例。

<h3><a name="EM0011"/>EM0011: 程序集`X`不存在。</h3>

该工具找不到程序集`X`指定自变量中。

<h3><a name="EM0012"/>EM0012: 程序集名称`X`不是唯一的</h3>

提供的多个程序集具有相同的内部名称，并且它将不能区分它们在运行时。

最可能的原因是程序集指定一次，在命令行自变量。 但是它是原始名称和多个副本重命名的程序集仍保持不能共存。

<h3><a name="EM0013"/>EM0013： 找不到程序集 X，以引用的 Y。</h3>

该工具找不到程序集 X，以引用的程序集 Y。 请确保所有引用的程序集位于要绑定的程序集所在的目录。

<h3><a name="EM0014"/>EM0014： 找不到 {产品} （{产品} {min_version} 是必需的）。</h3>

在系统上找不到错误消息中提及的依赖关系。

<h3><a name="EM0015"/>EM0015： 找不到 {产品} 的有效版本 (找到 {版本}，但至少 {min_version} 是必需的)。</h3>

依赖项错误中提到，在系统上，找到消息，但它是太旧。 请更新到较新版本。

<h3><a name="EM0016">EM0016： 无法创建符号链接 {file}-> {面向}： 错误 {number}</h3>

无法创建错误消息中提及的符号链接。

<h3><a name="EM0026"/>EM0026 无法分析命令行参数 'A': *</h3>

提供的命令行选项的语法`A`无法分析工具。 很可能不正确，请与文档或正确的语法的帮助。

<h3><a name="EM0099"/>EM0099： 内部错误 *。 请文件与测试用例 (https://github.com/mono/Embeddinator-4000/issues) 一个 bug 报告。</h3>

在 Embeddinator 4000 内部一致性检查失败时，将报告此错误消息。

这表示 Embeddinator-4000; 中的存在 bug在一个 bug 报告，请记录[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)与测试用例。


<!-- 1xxx: code processing -->

# <a name="em1xxx-code-processing"></a>EM1xxx： 代码处理

<h3><a name="EM1010"/>类型`T`，因此未生成`X`不支持。</h3>

这是**警告**，类型`T`将被忽略 （即执行任何操作将会生成） 因为它使用`X`，不支持的功能。

注意： 支持的功能将发展用新版本的工具。


<h3><a name="EM1011"/>类型`T`不会生成它，因为它缺少本机对应项。</h3>

这是**警告**，类型`T`将被忽略 （即执行任何操作将会生成） 因为它使用它公开的本机平台中具有没有对应项在.NET framework 中的内容。


<h3><a name="EM1011"/>类型`T`不会生成它，因为它缺少具有对应的本机封送处理代码。</h3>

这是**警告**，类型`T`将被忽略 （即执行任何操作将会生成） 因为它使用它需要额外封送处理在.NET framework 中的内容的公开。

注意： 这是这一点可能获取支持，有一些限制，该工具的未来版本中。


<h3><a name="EM1020"/>构造函数`C`由于参数类型而不生成`T`不支持。</h3>

这是**警告**，构造函数`C`将被忽略 （即执行任何操作将会生成） 因为类型的参数`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。


<h3><a name="EM1021"/>构造函数`C`具有默认值为其生成任何包装。</h3>

这是**警告**，构造函数的默认参数`C`不生成任何额外的代码。 最常见原因是现有方法已具有相同的签名。 例如 .net 中，可能能够：

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在生成此类情况下仅有两个`init`将创建选择器，这两个调入 mono，但不包装器更高版本会存在。


<h3><a name="EM1030"/>方法`M`，因此未生成返回类型`T`不支持。</h3>

这是**警告**，该方法`M`将被忽略 （即执行任何操作将会生成） 因为它是返回类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。


<h3><a name="EM1031"/>方法`M`由于参数类型而不生成`T`不支持。</h3>

这是**警告**，该方法`M`将被忽略 （即执行任何操作将会生成） 因为类型的参数`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。


<h3><a name="EM1032"/>方法`M`具有默认值为其生成任何包装。</h3>

这是**警告**的方法的默认参数`M`不生成任何额外的代码。 最常见原因是现有方法已具有相同的签名。 例如 .net 中，可能能够：

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在生成此类情况下仅有两个`increment`将创建选择器，这两个调入 mono，但不包装器更高版本会存在。


<h3><a name="EM1033"/>方法`M`由于另一种方法公开具有友好名称的运算符，则不会生成。</h3>

这是**警告**，该方法`M`由于另一种方法公开具有友好名称的运算符，则不会生成。 (https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)


<h3><a name="EM1034"/>扩展方法`M`也不能创建基元类型，因此未生成内某个类别`T`。 生成一个正常的静态方法。</h3>

这是**警告**primivite 上的扩展方法类型 (例如`System.Int32`) 找到。 在 ObjC 不可能在基元类型上创建类别。 改为生成器将生成一个正常的静态方法。



<h3><a name="EM1040"/>属性`P`由于参数类型而不生成`T`不支持。</h3>

这是**警告**，属性`P`将被忽略 （即执行任何操作将会生成） 因为公开的类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<h3><a name="EM1041"/>索引属性上`T`不会生成它，因为不支持多个索引的属性。</h3>

这是**警告**的上的索引的属性`T`将被忽略 （即执行任何操作将会生成） 因为不支持多个索引的属性。


<h3><a name="EM1050"/>字段`F`由于字段类型而不生成`T`不支持。</h3>

这是**警告**，字段`F`将被忽略 （即执行任何操作将会生成） 因为公开的类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<h3><a name="EM1051"/>元素`E`，则生成作为`F`因为其名称与重要的 objective-c 的选择器冲突。</h3>

这是**警告**，元素`E`将改为生成作为`F`因为其名称与重要的 objective-c 的选择器冲突。

上的选择器[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) objective c 中有重要意义并且必须仔细重写。

注意： 保留选择器的列表将发展用新版本的工具。


<!-- 2xxx: code generation -->

# <a name="em2xxx-code-generation"></a>EM2xxx： 代码生成


<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
