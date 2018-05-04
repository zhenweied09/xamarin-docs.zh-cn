---
title: .NET 嵌入的错误
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/11/2018
ms.openlocfilehash: 677242ea12f8fd87d82f337eafd96a1743ad806a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding-errors"></a>.NET 嵌入错误

## <a name="em0xxx-binding-error-messages"></a>EM0xxx： 绑定的错误消息

例如， 参数、 环境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000： 意外的错误-请填写在一个 bug 报告 https://github.com/mono/Embeddinator-4000/issues

出现意外的错误条件。 请[记录相应的问题](https://github.com/mono/Embeddinator-4000/issues)利用尽可能多的信息可能，其中包括：

* 完全生成具有最大详细级别的日志，
* 再现错误的最小测试用例
* 所有版本信息

获取确切的版本信息的最简单方法是使用**Xamarin Studio**菜单上，**有关 Xamarin Studio**项，**显示详细信息**按钮，然后复制/粘贴版本信息 (你可以使用**复制信息**按钮)。

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001： 无法创建输出目录 `X`

指定的目录名称`-o=DIR`不存在并且无法创建。 它可能是文件系统的名称无效。

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002： 选项`X`不支持

该工具不支持选项`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: 平台`X`无效。

该工具不支持该平台`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: 目标`X`无效。

该工具不支持目标`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005： 编译目标`X`无效。

该工具不支持编译目标`X`。 很可能该工具的另一个版本支持它，或者，它不适用于在此环境中。

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006： 找不到 Xcode 位置。

该工具找不到当前所选的 Xcode 位置使用`xcode-select -p`命令。 请验证此命令成功，并返回正确的 Xcode 位置。

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007： 无法获取 {sdk} 的 sdk 版本。

该工具无法获取使用 SDK 版本`xcrun --show-sdk-version --sdk {sdk}`命令。 请验证此命令成功，并返回的 SDK 版本。

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008： 体系结构 {arch} 为 {平台} 无效。 适用于 {平台} 的有效体系结构是: {体系结构}。

错误消息中的体系结构对于目标平台无效。 请验证-abi 选项，将传递有效的体系结构。

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: 功能`X`生成器当前未实现

这是我们想要的生成器的未来版本中修复的已知的问题。 贡献是欢迎。

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010： 无法合并框架 {simulatorFramework} 和 {deviceFramework}，因为文件 {file} 中同时存在。

该工具不能合并错误消息中提及的框架，因为它们之间没有公共文件。

这可能表示.NET 嵌入; 中的 bug在一个 bug 报告，请记录[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)与测试用例。

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: 程序集`X`不存在。

该工具找不到程序集`X`指定自变量中。

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: 程序集名称`X`不是唯一的

提供的多个程序集具有相同的内部名称，并且它将不能区分它们在运行时。

最可能的原因是程序集指定一次，在命令行自变量。 但是它是原始名称和多个副本重命名的程序集仍保持不能共存。

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013： 找不到程序集 X，以引用的 Y。

该工具找不到程序集 X，以引用的程序集 Y。 请确保所有引用的程序集位于要绑定的程序集所在的目录。

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014： 找不到 {产品} （{产品} {min_version} 是必需的）。

在系统上找不到错误消息中提及的依赖关系。

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015： 找不到 {产品} 的有效版本 (找到 {版本}，但至少 {min_version} 是必需的)。

依赖项错误中提到，在系统上，找到消息，但它是太旧。 请更新到较新版本。

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016： 无法创建符号链接 {file}-> {面向}： 错误 {number}

无法创建错误消息中提及的符号链接。

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 无法分析命令行参数 'A': *

提供的命令行选项的语法`A`无法分析工具。 很可能不正确，请与文档或正确的语法的帮助。

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099： 内部错误 *。 请文件与测试用例的一个 bug 报告 (https://github.com/mono/Embeddinator-4000/issues)。

在.NET 嵌入内部一致性检查失败时，将报告此错误消息。

这表示.NET 嵌入; 中的存在 bug在一个 bug 报告，请记录[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)与测试用例。

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx： 代码处理

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010： 键入`T`，因此未生成`X`不支持。

这是**警告**，类型`T`将被忽略 （即执行任何操作将会生成） 因为它使用`X`，不支持的功能。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011： 键入`T`不会生成它，因为它缺少具有对应的本机封送处理代码。

这是**警告**，类型`T`将被忽略 （即执行任何操作将会生成） 因为它公开需要额外封送处理在.NET framework 中的内容。

注意： 这是可能获取支持，有一些限制，该工具的未来版本中的内容。

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020： 构造函数`C`由于参数类型而不生成`T`不支持。

这是**警告**，构造函数`C`将被忽略 （即执行任何操作将会生成） 因为类型的参数`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021： 构造函数`C`具有默认值为其生成任何包装。

这是**警告**，构造函数的默认参数`C`不生成任何额外的代码。 最常见原因是现有方法已具有相同的签名。 例如， .net 中，可能能够：

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在生成此类情况下仅有两个`init`将创建选择器，这两个调入 Mono，但不包装器更高版本会存在。

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030： 方法`M`，因此未生成返回类型`T`不支持。

这是**警告**，该方法`M`将被忽略 （即执行任何操作将会生成） 因为它是返回类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031： 方法`M`由于参数类型而不生成`T`不支持。

这是**警告**，该方法`M`将被忽略 （即执行任何操作将会生成） 因为类型的参数`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032： 方法`M`具有默认值为其生成任何包装。

这是**警告**的方法的默认参数`M`不生成任何额外的代码。 最常见原因是现有方法已具有相同的签名。 例如， .net 中，可能能够：

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在生成此类情况下仅有两个`increment`将创建选择器，这两个调入 Mono，但不包装器更高版本会存在。

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033： 方法`M`由于另一种方法公开具有友好名称的运算符，则不会生成。

这是**警告**，该方法`M`由于另一种方法公开具有友好名称的运算符，则不会生成。 (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034： 扩展方法`M`也不能创建基元类型，因此未生成内某个类别`T`。 生成一个正常的静态方法。

这是**警告**primivite 上的扩展方法类型 (例如`System.Int32`) 找到。 Objective C 中是不可能在基元类型上创建类别。 改为生成器将生成一个正常的静态方法。

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040： 属性`P`由于参数类型而不生成`T`不支持。

这是**警告**，属性`P`将被忽略 （即执行任何操作将会生成） 因为公开的类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041： 索引属性上`T`不会生成它，因为不支持多个索引的属性。

这是**警告**的上的索引的属性`T`将被忽略 （即执行任何操作将会生成） 因为不支持多个索引的属性。

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050： 字段`F`由于字段类型而不生成`T`不支持。

这是**警告**，字段`F`将被忽略 （即执行任何操作将会生成） 因为公开的类型`T`不支持。

应该有早期警告原因提供详细信息类型`T`不支持。

注意： 支持的功能将发展用新版本的工具。

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051： 元素`E`，则生成作为`F`因为其名称与重要的 objective-c 的选择器冲突。

这是**警告**，元素`E`将改为生成作为`F`因为其名称与重要的 objective-c 的选择器冲突。

上的选择器[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) objective c 中有重要意义并且必须仔细重写。

注意： 保留选择器的列表将发展用新版本的工具。

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052： 元素`E`则不会生成其名称与同一类上的其他元素名称冲突。

这是**警告**该元素`E`如其名称与同一类上的其他元素名称冲突，则不会生成。

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053： 面向`E`Xamarin.iOS 和 Xamarin.Mac 不支持。 仅`framework`支持，应使用时才考虑选项。

这是**警告**面向`E`被视为不受支持的 Xamarin.iOS 和 Xamarin.Mac 用例。 

静态或动态嵌入.NET 库的使用可能需要额外的工作步骤或优化，和应避免在大多数的用例。

请考虑删除你`--target`参数或传递`--target=framework`相反。

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx： 代码生成

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
