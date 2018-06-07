---
title: Xamarin.iOS 错误
description: 本文档介绍 mtouch，用于捆绑 Xamarin.iOS 应用程序的工具所生成的各种错误。 错误代码列出并给出的完整说明。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: e9332ba34f113f56859065c74c24c116a331eceb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789442"
---
# <a name="xamarinios-errors"></a>Xamarin.iOS 错误

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mtouch 错误消息

例如， 参数，环境中，缺少工具。

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a>MT0000： 意外的错误-请填写在一个 bug 报告 http://bugzilla.xamarin.com

出现意外的错误条件。 请[文件一个 bug 报告](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)利用尽可能多的信息可能，其中包括：

* 完全生成具有最大详细级别的日志，(例如`-v -v -v -v`中**其他 mtouch 参数**);
* 再现错误; 的最小测试用例和
* 所有版本信息

获取确切的版本信息的最简单方法是使用**Visual Studio for Mac**菜单上，**有关 Visual Studio for Mac**项，**显示详细信息**按钮和复制/粘贴版本信息 (你可以使用**复制信息**按钮)。

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001:-devname 提供无需任何特定于设备的操作

这是如果-devname 传递给 mtouch 在任何特定于设备的操作时，将发出一个警告 (-logdev/installdev/killdev/launchdev /-listapps) 请求。

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002： 无法分析的环境变量 *。

如果你尝试设置的无效环境密钥，会发生此错误 = 值变量对。 正确的格式为： `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003： 应用程序名称 *.exe 与 SDK 或产品程序集 (.dll) 名称冲突。

可执行程序集的名称和应用程序的名称不能与应用程序中的任何 dll 的名称匹配。 请修改可执行文件的名称。

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004： 新 refcounting 逻辑需要 SGen 太启用。

如果启用 refcounting 扩展还必须启用 SGen 垃圾回收器在项目的 iOS 生成选项 （高级选项卡） 中。

提升从 Xamarin.iOS 7.2.1 此要求，可以使用 Boehm 和 SGen 垃圾回收器启用新的 refcounting 逻辑。

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: 输出目录 * 不存在。

请创建目录。

不会再生成此错误，如果它不存在，mtouch 自动将创建的目录。

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006： 没有任何 devel 平台 *，使用-平台 = PLAT 指定 SDK。

Xamarin.iOS 找不到在错误消息中提及的位置的 SDK 目录。 请验证路径正确。

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007： 根程序集 * 不存在。

Xamarin.iOS 找不到错误消息中提及的位置处的程序集。 请验证路径正确。

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008： 你应提供一个根程序集仅，找到 # 程序集: *。

多个根程序集传递到 mtouch，虽然可只有一个根程序集。

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009： 加载程序集时错误: *。

加载根程序集引用的程序集时出错。 可能生成输出中提供详细信息。

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010： 无法分析命令行参数: *。

分析命令行自变量时出错。 请验证它们都正确。

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * 比 MonoTouch 支持生成针对较新的运行时 （*）。

因为该项目包含对不使用 Xamarin.iOS BCL 生成的类库的引用，通常将报告此警告。

仅支持.NET 2.0 中，系统可能不适用于使用.NET 4.0 SDK 的应用的相同方式 Xamarin.iOS 可能不适用于使用.NET 4.0 生成的库，它可能在 Xamarin.iOS 上使用 API 不存在。

通用的解决方案是生成在库中的作为 Xamarin.iOS 类库。 这可以通过创建新的 Xamarin.iOS 类库项目来实现，并向其中添加所有源文件。 如果你没有库的源代码，应该联系供应商并请求他们提供其库的 Xamarin.iOS 兼容版本。

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: 不完整的数据用于完成 *。

此错误不 Xamarin.iOS 的当前版本中不再报告。

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013： 分析支持需要 sgen 太启用。

SGen (-sgen) 分析必须启用 (-分析) 启用。

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: iOS * SDK 不支持面向构建应用程序 *。

这可能是在以下情况下：

*  启用 ARMv6 并安装 Xcode 4.5 或更高版本。
*  启用 ARMv7s 并安装 Xcode 4.4 或更早版本。

请验证已安装的 Xcode 的版本支持的所选的体系结构。

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015： 无效 ABI: *。 支持的 ABIs 是： i386，x86_64、 armv7，armv7 + llvm、 armv7 + llvm + thumb2、 armv7s、 armv7s + llvm、 armv7s + llvm + thumb2、 arm64 和 arm64 + llvm。

无效的 ABI 传递到 mtouch。 请指定有效的 ABI。

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: 选项 * 已弃用。

提到的 mtouch 选项已弃用，将被忽略。

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017： 应提供根程序集。

需要指定根程序集 （通常主可执行文件） 时生成应用程序。

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018： 未知的命令行参数: *。

Mtouch 无法识别的错误消息中所述的命令行自变量。

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019： 只有一个-[日志 | 安装 | 终止 | 启动] 开发人员或--[启动 | 调试] sim 选项可用。

有几个选项不能同时使用的 mtouch:

-  -logdev
-  --installdev
-  -killdev
-  -launchdev
-  -launchdebug
-  -launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020： 有效选项为\*是\*。

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021： 无法编译使用 gcc / g + + (-使用 gcc) 时使用的静态的注册机构 （编译的设备时，这是默认值）。 删除--使用 gcc 标记或使用动态注册机构 (-注册机构： 动态)。

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: 选项-不支持-启用-泛型-中的注册机构和-注册机构不兼容。

删除这两个选项`--unsupported--enable-generics-in-registrar`和`--registrar`。 从开始 Xamarin.iOS 7.2.1 默认注册机构支持泛型。

不再显示此错误 (命令行自变量`--unsupported--enable-generics-in-registrar`已从 mtouch)。

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023： 应用程序名称 *.exe 与另一个用户程序集冲突。

可执行程序集的名称和应用程序的名称不能与应用程序中的任何 dll 的名称匹配。 请修改可执行文件的名称。

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024： 找不到所需的文件 *。

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025： 未不提供任何 SDK 版本。 请添加`--sdk=X.Y`来指定哪些 iOS SDK 应该用于生成应用程序。

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026： 无法分析命令行参数 *: *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027： 的选项的\*和\*不兼容。

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028： 不能启用饼图 (-饼图) 面向 iOS 4.1 或更早版本时。 请禁用饼图 (-饼图： false) 或至少将部署目标设置为 iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (-启用 repl) 仅支持在模拟器中 (-sim)。

如果你正在生成对于模拟器，则仅支持 REPL。 这意味着，如果你通过`--enable-repl`到 mtouch，还必须传递`--sim`。

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: 可执行文件的名称 (\*) 和应用程序名称 (\*) 不相同，这可能会阻止获取正确 symbolicated 崩溃日志。

当 Xcode symbolicates （转化以下位置以函数名称和文件/行号的内存地址） 的过程可能会失败，如果可执行文件和应用程序具有不同的名称 （不含扩展名）。

若要修复此可更改项目的生成/iOS 应用程序选项或在项目的生成/输出选项中的更改程序集名称中的应用程序名称。

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031： 命令行参数-启用背景提取和-启动为背景提取需要-launchsim 过。

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: 选项 '-debugtrack 除非否则将忽略-调试还指定。

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Xamarin.iOS 项目必须引用 monotouch.dll 或 Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034： 不能包含 monotouch.dll 和 Xamarin.iOS.dll 中相同的 Xamarin.iOS 项目-\*是显式引用，而\*引用的 *。

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: 不能启动 * 模拟器 * 应用程序。 请启用在您的项目的 iOS 生成选项 （高级页） 中正确 architecture(s)。

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch.dll 为 64 位兼容。 引用 Xamarin.iOS.dll，或者不生成 64 位体系结构 （ARM64 和/或 x86_64）。

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038： 旧的注册机构 (-注册机构： oldstatic | olddynamic) 不支持引用 Xamarin.iOS.dll 时。

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039： 面向 ARMv6 应用程序不能引用 Xamarin.iOS.dll。

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040： 找不到程序集\*'，所引用的'\*。

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041： 无法引用 monotouch.dll 和 Xamarin.iOS.dll。

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042： 找不到 monotouch.dll 或 Xamarin.iOS.dll 已到引用。 将添加对 monotouch.dll 的引用。

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: Boehm 垃圾回收器当前不支持引用 Xamarin.iOS.dll 时。 选择 SGen 垃圾回收器。

SGen 垃圾回收器仅支持与统一项目中。 确保作为垃圾回收器指定 Boehm 任何其他 mtouch 标志。

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044: Xcode 6.0 或更高版本，-listsim 仅支持。

安装更高版本的 Xcode 版本。

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:-如果使用 iOS 仅支持扩展 8.0 （或更高版本） 的 SDK。

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047： 统一应用程序的最小的部署目标是 5.1.1，当前的部署目标是 *。 请在你的项目的 iOS 应用程序选项中选择一个较新的部署目标。

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049： 仅当部署目标是 8.0 或更高版本，才支持 *.framework。 * 功能可能无法正常工作。

在部署目标是指的 iOS 版本不支持指定的框架。 更新到较新的 iOS 版本，部署目标，或者从应用中删除指定的框架的使用情况。

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin.iOS * 需要 Xcode 5.0 或更高版本。 当前的 Xcode 版本 (位于 *) 是 *。

安装更高版本的 Xcode。

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052： 没有指定的命令。

Mtouch 为不指定的任何操作。

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054： 无法规范化路径 *: *

这是内部错误。 如果你看到此错误，请提交 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: Xcode 路径 * 不存在。

使用 Xcode 路径传递`--sdkroot`不存在。 请指定有效的路径。

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056： 在默认位置找不到 Xcode (/ Applications/Xcode.app)。 请安装 Xcode，或将自定义路径使用-sdkroot 传递<path>。

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057： 无法从 sdk 根到 Xcode.app 确定的路径 *。 请指定 Xcode.app 捆绑的完整路径。

使用路径传递`--sdkroot`未指定有效的 Xcode 应用。 请指定到 Xcode 应用程序的路径。

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: Xcode.app\*' 无效 (文件\*不存在)。

使用路径传递`--sdkroot`未指定有效的 Xcode 应用。 请指定到 Xcode 应用程序的路径。

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059： 系统上找不到当前所选的 Xcode: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060： 找不到当前所选的 Xcode，系统上。 xcode 选择-打印路径返回 *，但该目录不存在。

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061： 没有 Xcode.app （使用-sdkroot），指定报告的 xcode 选择-打印路径使用系统 Xcode: *

这是信息性警告，解释 Xcode 将使用，因为未指定。

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062： 未指定 （使用-sdkroot 或 xcode-选择-打印路径），改为使用默认 Xcode Xcode.app: *

这是信息性警告，解释 Xcode 将使用，因为未指定。

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063： 找不到扩展中的可执行文件 * （在其 Info.plist 中的没有 CFBundleExecutable 条目）

每个 Info.plist 必须具有的可执行文件 （使用 CFBundleExecutable 条目），但应在生成期间自动生成一个条目。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin.iOS 仅支持与统一项目的嵌入式的框架。

Xamarin.iOS 仅支持嵌入的框架，在使用统一的 API;请更新你的项目以使用统一的 API。

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin.iOS 仅支持嵌入的框架时部署目标是至少 8.0 (当前部署目标: * 嵌入框架: *)

在部署目标是至少 8.0 （因为 iOS 的早期版本不支持嵌入的框架） 时，Xamarin.iOS 仅支持嵌入的框架。

请更新为 8.0 或更高版本的项目的 Info.plist 中的部署目标。

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066： 无效的生成注册器程序集: *

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067： 无效的注册机构: *

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068： 的目标框架的值无效: *。

使用传递无效的目标 framework--目标框架自变量。 请指定一个有效的目标框架。

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070： 无效的目标框架: *。 有效的目标框架: *。

使用传递无效的目标 framework--目标框架自变量。 请指定一个有效的目标框架。

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071： 未知的平台: *。 这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录http://bugzilla.xamarin.com与测试用例。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072： 平台不支持扩展 *。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin.iOS * 不支持的一个部署目标 * (最小值是 *)。 请选择在你的项目的 Info.plist 中的较新的部署目标。

最小的部署目标是指定的错误消息中; 中请在项目的 Info.plist 中选择一个较新的部署目标。

如果不能更新的部署目标，请使用较旧版本的 Xamarin.iOS。

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin.iOS * 不支持的最小的部署目标 * (最大值是 *)。 请在你的项目的 Info.plist 选择较旧的部署目标，或升级到 Xamarin.iOS 的较新版本。

Xamarin.iOS 不支持将最小的部署目标设置为高于此特定版本的 Xamarin.iOS 为生成的版本更高版本。

请在项目的 Info.plist 选择较旧的最小的部署目标，或升级到 Xamarin.iOS 的较新版本。

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075： 无效的体系结构 * 的 * 项目。 有效的体系结构是: *

指定了无效的体系结构。 请验证体系结构有效。

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075： 没有指定体系结构 （使用-abi 自变量）。 一种体系结构，才能使用 * 项目。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: WatchOS 项目必须是扩展。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077： 增量生成启用与部署目标 < 8.0 (当前 *)。 不支持这样做 （生成的应用程序将不启动在 iOS 9），因此部署目标将设置为 8.0。

这是一个警告，指出部署目标已设置到此版本 8.0 以便增量生成工作正常。

部署目标是至少 8.0 （因为生成的应用程序将不启动在 iOS 9 否则） 时，才支持增量生成。

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078： 推荐的 Xcode 版本 xamarin.ios * 是 Xcode * 或更高版本。 当前的 Xcode 版本 (位于 *) 是 *。

这是一个警告，指出 Xcode 的当前版本不此版本的 Xamarin.iOS Xcode 的建议的版本。

请升级 Xcode 以确保最佳的行为。

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080： 禁用 NewRefCount、--新的 refcount:false，已弃用。

这是一个警告，指出，禁用新的请求已被忽略，引用计数 (-新的 refcount:false)。

新的引用计数功能现在是必需的所有项目，并且不因此可以再禁用。

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081： 命令行参数-下载崩溃报告还要求-下载-崩溃的报表的。

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (-启用 repl) 如果不使用链接仅支持 (-nolink)。

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: watchOS 上不支持仅 Asm bitcode。 使用任一-bitcode： 标记或-bitcode： 完整。

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084： 在模拟器中不支持 Bitcode。 为模拟器进行生成时，不要将-bitcode 的传递。

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085： 未引用 * 未找到。 将自动添加它。

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086： 目标框架 (-目标框架) 时在生成针对 TVOS 或 WatchOS，必须指定。

这通常表示 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087： 增量生成 (-fastdev) 不支持 Boehm GC。 将禁用增量生成。

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088： 协作 watchOS 应用模式必须是 GC。 请删除 mtouch--联合： false 参数。

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: 选项 '\*'无法 take 值'\*为 GC 时启用了协作的模式。

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091： 此版本的 Xamarin.iOS 需要 * SDK (随 Xcode *)。 请升级 Xcode 以获取必需的标头文件或将托管链接器行为设置为链接 Framework Sdk 仅 （若要尽量避免新的 Api）。

Xamarin.iOS 要求标头文件，从要生成你的应用程序的错误消息中指定的 SDK 版本。 若要修复此错误的建议的方法是升级 Xcode 来获取所需的 SDK，这将包括所有必需的标头文件。 如果你有多个版本的 Xcode 安装，或想要在非默认位置使用 Xcode，请确保在 IDE 的首选项中设置正确的 Xcode 位置。

潜在替代解决方案是使托管链接器。 这将删除未使用 API 包括，在大多数情况下，新的 API 从中对标头文件进行缺失 （或不完整）。 但是这不会正常如果你的项目使用了较新的 SDK 比你的 Xcode 中引入的 API 提供。

上一次 straw 解决方案是使用较旧版本的 Xamarin.iOS，另一个支持你的项目的 SDK 需要。

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093： 找不到 mlaunch。

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Aot 文件可能不会复制到目标目录 {dest}: {错误}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>不发现 MT0096: Xamarin.iOS.dll 对任何引用。

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099： 内部错误 *。 请文件与测试用例的一个 bug 报告 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 内部一致性检查失败时，将报告此错误消息。

这表示 Xamarin.iOS; 中的存在 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100： 无效的程序集生成目标: *。 请文件与测试用例的一个 bug 报告 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 内部一致性检查失败时，将报告此错误消息。

这始终是 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试用例。

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: 程序集 *-程序集生成目标自变量中指定了多次。

错误消息中提到的程序集-程序集生成目标自变量中多次指定。 请确保每个程序集仅一次提到。

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: 程序集的\*和\*具有相同的目标名称 (\*)，但使用不同的目标 (\*和 *)。

错误消息中提及的程序集具有冲突的生成目标。

例如：

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

此示例尝试创建具有相同的动态库和一个框架 (`MyBinary`)。

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103： 静态对象\*包含多个程序集 (\*)，但每个静态对象必须与一个程序集相对应。

错误消息中提及的程序集被所有编译到单个静态对象。 这不允许，每个程序集必须编译到不同的静态对象。

例如：

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

此示例尝试生成一个静态对象 (`MyBinary`) 组成两个程序集 (`Assembly1.dll`和`Assembly2.dll`)，这不允许。

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: 指定任何程序集生成目标 *。

指定程序集进行生成时目标使用`--assembly-build-target`，应用程序中的每个程序集必须具有分配了生成目标。

当错误消息中提到的程序集没有生成分配的目标程序集时，会报告此错误。

请参阅文档，`--assembly-build-target`有关进一步信息。

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106： 程序集生成目标名称\*无效： 字符\*不允许。

程序集生成目标名称必须是有效的文件名。

例如，这些值将触发此错误：

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

因为`my/path.o`不是由于目录分隔符字符是有效的文件名。

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: 程序集的\*具有不同的自定义 LLVM 优化 (\*)，这不允许在所有编译时为单个二进制。

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108： 程序集生成目标 * 与任何程序集不匹配。

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109： 程序集{0}的加载路径不同于所提供的路径不同路径 (提供路径： {1}，实际路径： {2})。

这是警告，该值由应用程序引用的程序集从不同位置加载比请求。

这可能意味着应用程序正在引用多个程序集具有相同的名称，但来自不同的位置，这可能导致意外的结果 （将使用的第一个程序集）。

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110： 具有禁用增量生成，因为此版本的 Xamarin.iOS 不在项目中包括第三方绑定库和程序编译为 bitcode 支持增量生成。

因为此版本的 Xamarin.iOS 不支持增量生成项目，包括第三方绑定库和程序编译为 bitcode （tvOS 和 watchOS 项目） 中已禁用增量生成。

您需要执行任何操作，此消息是仅用于提供信息。

有关详细信息请参阅 bug #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710)。

不不再报告此警告。

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode 具有已启用，因为此版本的 Xamarin.iOS 不支持生成 watchOS 项目而不启用 bitcode 使用 LLVM。

因为此版本的 Xamarin.iOS 不支持生成 watchOS 已自动启用 Bitcode 项目而不启用 bitcode 使用 LLVM。

您需要执行任何操作，此消息是仅用于提供信息。

有关详细信息请参阅 bug #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634)。

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112： 本机代码共享已禁用，因为 *

可以禁用代码共享的多个原因有：

* 因为容器应用的部署目标是早于 iOS 8.0 (它具有 *))。

由于本机代码共享上使用用户的框架，实现，如果其中引入了 iOS 8.0，本机代码共享要求 iOS 8.0。

* 因为容器应用程序包括 I18N 程序集 （*）。

当前不支持本机代码共享的如果容器应用程序包括 I18N 程序集。

* 因为容器应用程序具有自定义 xml 定义的托管链接器 （*）。

本机代码共享要求，不支持对自定义 xml 定义用于托管链接器的项目。

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113： 本机代码共享已禁用的扩展 * 因为 *。

* 因为 bitcode 选项与不同容器应用程序之间 (\*) 和扩展名 (\*)。

  本机代码共享要求共享代码的项目之间的匹配 bitcode 选项。

* 因为--程序集生成目标选项的不同容器应用程序之间 (\*) 和扩展名 (\*)。

  本机代码共享要求，--程序集生成目标选项相等之间共享代码的项目。

  发生此问题可以如果增量生成不是任何一个是启用还是禁用所有项目中。

* 因为 I18N 程序集是容器应用程序之间的差异 (\*) 和扩展名 (\*)。

  包括 I18N 程序集的扩展当前不支持本机代码共享。

* 因为 AOT 编译器的自变量是容器应用程序之间的差异 (\*) 和扩展名 (\*)。

  本机代码共享要求 AOT 编译器的自变量不会共享代码的项目之间存在差异。

* 因为 AOT 编译器的其他自变量是容器应用程序之间的差异 (\*) 和扩展名 (\*)。

  本机代码共享要求 AOT 编译器的自变量不会共享代码的项目之间存在差异。

  如果项目之间不同，作为 64 位浮点执行所有的 32 位浮点操作，则会发生这种情况。

* 因为 LLVM 未启用或禁用在容器应用程序 (\*) 和扩展名 (\*)。

  本机代码共享要求 LLVM 已启用或禁用的所有项目的共享代码。

* 因为托管链接器设置不同容器应用程序之间 (\*) 和扩展名 (\*)。

  本机代码共享要求托管链接器设置的所有项目的共享代码相同。

* 因为托管链接器的已跳过程序集不同容器应用程序之间 (\*) 和扩展名 (\*)。

  本机代码共享要求托管链接器设置的所有项目的共享代码相同。

* 因为扩展名具有自定义 xml 定义的托管链接器 （*）。

  本机代码共享要求，不支持对自定义 xml 定义用于托管链接器的项目。

* 因为容器应用程序不会生成有关 ABI * （在扩展构建的此 ABI）。

  本机代码共享要求容器应用程序生成为任何应用扩展为生成的所有体系结构。

  例如： 当为 ARM64 + ARMv7，生成的扩展，但容器应用程序仅生成有关 ARM64 时发生此条件。

* 因为容器应用程序生成为 ABI \*，这不是扩展的 ABI 与兼容 (\*)。

  本机代码共享要求的所有项目都生成为完全相同的 API。

  例如： 当扩展生成 ARMv7 + llvm + thumb2，但容器应用程序将仅生成 ARMv7 + llvm 时发生此条件。

* 因为容器应用程序所引用程序集\*发件人\*，而扩展引用版本的版本不同 *。

  本机代码共享要求所有共享代码的项目的所有程序集使用相同的版本。

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115： 建议引用使用代码动态符号 (-动态符号模式 = 代码) 启用 bitcode 后。

Xamarin.iOS 项目将会经常引用本机符号动态，这意味着本机链接器可能会在本机链接过程中，删除此类本机符号因为本机链接器看不到，使用这些符号。

Xamarin.iOS 将通常要求本机链接器以保留此类符号 (使用`-u symbol`链接器标志)，但当编译的 bitcode 本机链接器不接受`-u`标志。

Xamarin.iOS 已实现的替代解决方法： 我们会生成额外本机代码，即在引用这些符号，因此本机链接器将会看到，使用这些符号。 这将在编译到 bitcode 时自动完成。

如果`--dynamic-symbol-mode=linker`传递给 mtouch，解决方案将被禁用，且 Xamarin.iOS 将尝试传递此备用`-u`到本机链接器。 这很可能将导致本机链接器错误。

解决方法是删除`--dynamic-symbol-mode=linker`从项目的生成选项中的其他 mtouch 参数的自变量。

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116： 无效的体系结构: {体系结构}。 部署目标为 11 或更高版本时不支持 32 位体系结构。 请确保该项目不会生成 32 位体系结构。

iOS 11 不包含对 32 位应用程序的支持，因此不支持部署目标为 11 或更高版本的 iOS 时生成适用于 32 位应用程序。

更改项目的 iOS 生成选项中的目标体系结构为 arm64，或在项目的 Info.plist 中将部署目标更改为较早的 iOS 版本。

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117： 无法启动模拟器仅支持 64 位上的 32 位应用。

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Aot 文件找不到预期目录 {msymdir}。

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123： 可执行的程序集 * 不引用 *。

未引用找不到平台程序集 (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) 中可执行程序集。

这通常发生在其中使用平台程序集; 中的任何内容可执行项目中没有代码例如空的 Main 方法 （和任何其他代码） 会显示此错误：

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

使用平台程序集的 API 将解决此问题：

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124： 无法为 {lang} 设置的当前语言 （根据 LANG = {LANG}）: {异常}

这是一个警告，指示当前的语言，无法设置为错误消息中的语言。

当前语言将默认为系统语言。

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125:--程序集的生成的目标在模拟器中，将忽略命令行参数。

不不需要任何操作，此消息是仅用于提供信息。

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126： 具有禁用增量生成，因为增量生成不支持在模拟器中。

不不需要任何操作，此消息是仅用于提供信息。

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127： 具有禁用增量生成，因为此版本的 Xamarin.iOS 不支持包含多个一个第三方绑定库的项目中的增量生成。

增量生成已被自动禁用，因为此版本的 Xamarin.iOS 不会始终生成具有多个第三方绑定库项目正确。

不不需要任何操作，此消息是仅用于提供信息。

有关详细信息请参阅 bug #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727)。

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128： 无法触摸文件 *: *

触摸 （这样做是为了确保正确完成部分生成） 的文件时出错。

最有可能可以忽略此警告;在出现任何问题时提交 bug (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) ，它将进行调查。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx： 项目相关的错误消息

### <a name="mt10xx-installer--mtouch"></a>MT10xx： 安装程序 / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>在指定的目录，MT1001： 找不到应用程序

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002： 无法创建符号链接，复制文件

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003： 无法终止应用程序 *。 你可能必须手动终止应用程序。

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004： 无法获取安装的应用程序的列表。

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005： 无法终止应用程序\*设备上\*: *-你可能需要手动终止应用程序。

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006： 无法安装应用程序\*设备上\*: *。

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007： 无法启动应用程序\*设备上\*: *。 你仍可以通过在其上的点击手动启动该应用程序。

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>无法启动模拟器 MT1008:

如果 mtouch 无法启动模拟器，则会报告此错误。   因为已经运行的过期或不模拟器进程，这可能有时发生。

可以使用以下命令在 Unix 命令行上发布终止停滞的模拟器进程：

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009： 无法将复制程序集\*到\*: *

这是某些版本的 Xamarin.iOS 中的已知的问题。

如果你发生这种情况，请尝试以下解决方法：

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

但是，由于 Xamarin.iOS 的最新版本中已解决此问题，请提交新 bug 在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与你的完整版本信息和生成日志输出。

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010： 无法加载程序集 *: *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011： 无法添加缺少的资源文件: *

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: 未能列出设备上的应用 *: *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013： 依赖项跟踪错误： 没有要比较的文件。 在一个 bug 报告，请记录http://bugzilla.xamarin.com与测试用例。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与测试 caes。

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014： 无法重新使用的缓存的版本 *: *。

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015： 未能创建可执行文件 *: *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015： 未能创建可执行文件 *: *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016： 无法创建通知文件，因为目录已存在具有相同的名称。

删除目录`NOTICE`从项目。

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017： 未能创建通知文件: *。

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018： 你的应用程序代码签名检查失败，无法安装在设备上 *。 检查你的证书，预配配置文件，和捆绑 id。 可能你的设备不是所选的预配配置文件的一部分 (错误： 0xe8008015)。

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019： 你的应用程序具有不受你当前的预配配置文件的权利和无法安装在设备上 *。 有关详细信息，请检查 iOS 设备日志 (错误： 0xe8008016)。

可能的原因是：

* 你的应用程序使用具有当前预配配置文件不支持的权利。
  可能的解决方案：
  - 指定支持权利的不同预配配置文件应用程序的需要。
  - 删除当前的预配配置文件中不支持的权利。
* 你正在尝试部署不包括在你使用的预配配置文件中的设备。
  可能的解决方案：
  - 从模板在 Xcode 中创建新的应用程序，选择相同的配置文件，并部署到同一设备。 有时 Xcode 可以自动与新设备 （在其他情况下，Xcode 将询问你要执行的操作） 刷新预配配置文件。
  -转到 iOS 开发人员中心并更新预配配置文件的新设备，然后下载到计算机的更新的预配配置文件。

在大多数情况下，将向 iOS 设备日志输出有关失败的详细信息，从而帮助诊断问题。

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020： 无法启动应用程序\*设备上\*: *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021： 无法将复制文件\*到\*: {2}

不会复制文件。 来自复制操作的错误消息包含有关错误详细信息。

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022： 无法将复制目录\*到\*: {2}

无法复制一个目录。 来自复制操作的错误消息包含有关错误详细信息。

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023： 无法与要查找应用程序的设备 *: *

尝试查找设备上的应用程序时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024： 应用程序签名无法验证在设备上 *。 请确保预配配置文件是安装且未过期 (错误： 0xe8008017)。

设备会拒绝应用程序安装，因为无法验证签名。

请确保预配配置文件是安装并且未过期。

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025： 无法列出在设备上的崩溃报告 *。

尝试列出在设备上的崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026： 无法下载崩溃报告 * 从设备 *。

尝试从设备下载崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027： 不能使用 Xcode 7 + 启动具有 iOS 设备上的应用程序 * （Xcode 7 仅支持 ios 6 或更）。

不能使用 Xcode 7 + 启动使用 iOS 版本低于 6.0 在设备上的应用程序。

请使用较旧版本的 Xcode，或点击应用程序手动启动它。

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028： 无效的设备规范: *。 预期的 ios、 watchos' 或者 'all'。

设备规范传递使用--设备无效。 有效值为: ios，watchos' 或 'all'。

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029： 在指定的目录找不到应用程序: *

传递到-launchdev 的应用程序路径不存在。 请指定有效的应用捆绑包。

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030： 启动应用程序使用的捆绑标识符设备上已弃用。 请将完整路径传递给捆绑包来启动。

建议你先将路径传递给应用程序以启动而不是只需捆绑 id 的设备上。

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031： 无法启动应用程序\*设备上\*因为设备已被锁定。 请解锁设备，然后重试。

请解锁设备，然后重试。

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032： 此应用程序可执行文件可能太大 (* MB) 能够在设备上执行。 如果启用了 bitcode 你可能想要用于开发禁用它，只需将提交到 Apple 应用程序。

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033： 无法卸载应用程序\*从设备\*: *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035： 不能包含不同版本的 framework {name}

不能与相同的 framework 的不同版本链接在一起。

这通常发生时扩展引用不同的比 （可能通过第三方绑定程序集） 的容器应用的框架版本。

此错误后将有多个[MT1036](#MT1036)错误列出每个不同的框架的路径。

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: 从所含 Framework {name}: {路径} （与前一个错误的相关）

仅与报告此错误[MT1036](#MT1036)。 请参阅[MT1036](#MT1036)有关详细信息。

### <a name="mt11xx-debug-service"></a>MT11xx： 调试服务

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101： 无法启动应用程序

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102： 无法附加到应用程序 （以终止它）: *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103： 无法分离

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104： 发送数据包失败: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105： 意外的响应类型

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106： 无法在设备上获取的应用程序的列表： 请求的操作已超时。

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107： 应用程序启动失败: *

请检查你设备被锁定。

如果要部署企业应用或使用可用的预配配置文件，你可能必须信任开发人员 (对此进行说明<a href="http://stackoverflow.com/a/30726375/183422">此处</a>)。

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108： 找不到此 XX (YY) 设备的开发人员工具。

从 mtouch 的几个操作需要<tt>DeveloperDiskImage.dmg</tt>文件必须存在。   此文件是 Xcode 的一部分，通常位于相对于你用于在生成针对、 SDK <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>。

发生此错误可能是因为你没有 DeveloperDiskImage.dmg 已连接的设备相匹配。

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109： 应用程序无法启动，因为设备已锁定。 请解锁设备，然后重试。

请检查你设备被锁定。

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110： 应用程序启动因 iOS 安全限制而失败。 请确保开发人员是受信任。

如果要部署企业应用或使用可用的预配配置文件，你可能必须信任开发人员 (对此进行说明<a href="http://stackoverflow.com/a/30726375/183422">此处</a>)。

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111： 应用程序已成功启动，但不能等待应用程序以退出如请求，因为不能在启动使用 gdbserver 检测应用程序终止。

### <a name="mt12xx-simulator"></a>MT12xx： 模拟器

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201： 无法加载模拟器: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202： 无效的模拟器配置: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203： 无效的模拟器规范: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204： 无效的模拟器规范 *： 未指定的运行时。

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205： 无效的模拟器规范 *： 未指定的设备类型。

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206： 找不到模拟器运行时 *。

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207： 找不到的模拟器设备类型 *。

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208： 找不到模拟器运行时 *。

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209： 找不到的模拟器设备类型 *。

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210： 无效的模拟器规范： \*，未知的键\*

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211： 模拟器版本\*不支持模拟器类型\*

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212： 无法创建模拟器版本在其中键入 = * 和运行时 = *。

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213： Xcode 4 无效的模拟器规范: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214： Xcode 5 无效的模拟器规范: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215： 指定无效 SDK: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216： 找不到模拟器 UDID *。

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217： 无法加载在应用程序捆绑 *。

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218： 时的应用中找到的任何捆绑标识符 *。

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219： 找不到的模拟器 *。

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220： 找不到最新的模拟器运行时的设备 *。

这通常表示 Xcode 问题。

要尝试解决此问题的事项：

* 在 Xcode 中一次使用模拟器。
* 通过使用-sdk 的显式 SDK 版本<version>。
* 重新安装 Xcode。

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221： 找不到配对的 iPhone 模拟器 WatchOS 模拟器 *。

在启动 WatchOS 模拟器中的 WatchOS 应用，必须有配对的 iOS 模拟器以及。

观看可以与 iOS 模拟器使用 Xcode 的设备 UI 配对模拟器 (菜单窗口-> 设备)。

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301： 本机库`*`(\*) 已被忽略，因为它不匹配当前生成 architecture(s) (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302： 无法提取本机库 * 从 +。 请确保本机库已正确嵌入的托管程序集 （如果使用的绑定项目生成程序集、 本机库必须包括在项目中，以及其生成的操作必须是 ObjcBindingNativeLibrary）。

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303： 无法解压缩本机框架\*发件人\*。 请查看有关从本机 zip 命令的详细信息生成日志。

无法解压缩绑定库中的指定本机框架。

请查看有关此故障从本机 zip 命令的详细信息的 bulid 日志。

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: 嵌入的框架 * 在 * 无效： 它不包含 Info.plist。

指定的嵌入式的框架不包含 Info.plist，并因此不是有效的框架。

请确保在框架手有效。

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305： 绑定库\*包含一个用户框架 (\*)，但嵌入的用户框架需要 iOS 8.0 (当前部署目标是 *)。 请在至少 8.0 的 Info.plist 文件中设置部署目标。

指定的绑定库包含嵌入的框架中，但 Xamarin.iOS 仅支持 iOS 8.0 或更高版本中的嵌入式的框架。

请在至少 8.0 若要解决此错误的 Info.plist 文件中设置部署目标 （或不使用嵌入的框架）。

### <a name="mt14xx-crash-reports"></a>MT14xx： 崩溃报告

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400： 无法打开崩溃报表服务： AFCConnectionOpen 返回 *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401： 无法关闭崩溃报表服务： AFCConnectionClose 返回 *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402： 无法读取文件信息 *: AFCFileInfoOpen 返回 *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403： 无法读取的崩溃报告： AFCDirectoryOpen （*） 返回: *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404： 无法读取的崩溃报告： AFCFileRefOpen （*） 返回: *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405： 无法读取的崩溃报告： AFCFileRefRead （*） 返回: *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406： 无法列出崩溃报告： AFCDirectoryOpen （*） 返回: *

尝试从设备访问崩溃报告时出错。

要尝试解决此问题的事项：

* 从设备中删除应用程序，然后重试。
* 断开连接设备并将其重新连接。
* 重新启动设备。
* 重新启动 mac
* 将设备与 iTunes （这将从设备删除任何崩溃报告） 同步。

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600： 不匹配-O 动态库 （未知标头 0 x *）: *。

处理问题的动态库时出错。

请确保动态库是一个有效的匹配-O 动态库。

可以使用已验证的库格式`file`从终端命令：

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601： 不是静态库 (未知的标头 *): *。

处理问题的静态库时出错。

请确保静态库是一个有效的匹配-O 静态库。

可以使用已验证的库格式`file`从终端命令：

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602： 不匹配-O 动态库 （未知标头 0 x *）: *。

处理问题的动态库时出错。

请确保动态库是一个有效的匹配-O 动态库。

可以使用已验证的库格式`file`从终端命令：

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: 位置处的 fat 条目的格式未知 * 在 *。

处理问题 fat 存档时出错。

请确保 fat 存档有效。

可以使用验证 fat 存档格式`file`从终端命令：

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604： 类型的文件 * 不是 MachO 文件 （*）。

处理所涉及的 MachO 文件时出错。

请确保该文件是有效的匹配-O 动态库。

可以使用验证文件的格式`file`从终端命令：

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx： 链接器错误消息

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001： 无法链接程序集

此错误意味着托管链接器遇到意外的错误，例如异常，，和无法完成或保存正在处理的程序集。 例如具体错误有关的详细信息将作为一部分的生成日志

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

请务必文件这类问题的一个 bug 报告。 在大多数情况下发布正确的修补程序之前，可以提供一个解决方法。 上述信息至关重要 （以及测试用例和/或程序集 binairy） 来解决此问题。

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002： 无法解析引用: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003： 选项 * 将被忽略，因为链接被禁用

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004： 额外链接器定义文件 * 找不到。

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005： 从定义 * 无法分析。

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006： 无法加载从 mscorlib.dll: *。 请重新安装 Xamarin.iOS。

这通常表示你的 Xamarin.iOS 安装问题。 请尝试重新安装 Xamarin.iOS。

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010： 未知的 HttpMessageHandler `*`。 有效值为 HttpClientHandler （默认值）、 CFNetworkHandler 或 NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011： 未知的 TlsProvider `*`。  有效值为默认值或 appletls。

赋予的值`tls-provider=`不是有效的 TLS （传输层安全性） 提供程序。

`default`和`appletls`是唯一有效的值，并且两个表示相同的选项，则需要提供 SSL/TLS 支持使用本机 Apple TLS API。

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015： 无效 HttpMessageHandler `*` watchOS 有关。 唯一有效的值是 NSUrlSessionHandler。

这是由于该项目文件指定无效 HttpMessageHandler 而出现一个警告。

早期版本的我们的预览工具默认生成无效的值在项目文件中。

若要解决此警告，请在文本编辑器中，打开项目文件并从 XML 中删除所有 HttpMessageHandler 节点。

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016： 无效 TlsProvider`legacy`选项。 唯一有效的值`appletls`将使用。

`legacy`提供程序，这是完全托管的 SSLv3 / TLSv1 仅提供程序，通常不附带 Xamarin.iOS 不再。 使用此旧提供程序的并生成与较新的项目`appletls`一个。

若要解决此警告，请在文本编辑器中，打开项目文件并删除所有 MtouchTlsProvider ' 从 XML 节点。

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017： 无法处理 XML 描述。

这意味着在没有错误[自定义 XML 链接器配置文件](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/)提供，请查看你的文件。

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018： 程序集\*从两个不同的位置引用:\*和 *。

错误消息中提到的程序集是从多个位置加载。 请确保始终使用相同版本的程序集。

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019： 无法加载根程序集 *

无法加载根程序集。 请验证错误消息中的该路径指向现有文件，并且它是有效的.NET 程序集。

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x： 绑定优化器失败处理`...`。

尝试优化生成绑定代码出现意外的一些内容时发生。 错误消息中名为导致出现此问题的元素。 若要解决此问题，名为程序集 （或包含的类型或方法名为） 将需要提供在[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch自变量**)。

最后一位数`x`将：
* `0` 程序集名称;
* `1` 类型名称;
* `3` 方法名称;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030： 删除用户资源失败处理`...`。

尝试删除用户资源时出现意外的一些内容。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

用户资源是包含在需要提取，在生成时，若要创建应用程序捆绑包 （作为资源） 的程序集的文件。 这包括：

* `__monotouch_content_*` 和`__monotouch_pages_*`资源; 和
* 在绑定程序集; 内嵌入的本机库

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040： 默认 HttpMessageHandler setter 失败处理`...`。

尝试设置默认值时出现意外出现`HttpMessageHandler`应用程序。 请文件[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050： 代码 Remover 失败处理`...`。

尝试从与应用程序传送的 BCL 删除代码出现意外的一些内容时发生。 请文件[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Sealer 失败处理`...`。

出现意外出现尝试密封类型或方法 （最终） 时或当 devirtualizing 一些方法。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070： 处理的元数据化简器失败`...`。

尝试减少从应用程序的元数据时出现意外的一些内容。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: MarkNSObjects 失败处理`...`。

尝试将标记时出现意外出现`NSObject`子类从应用程序。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](http://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090： 内联方失败处理`...`。

尝试将代码内联到从应用程序出现意外的一些内容时发生。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](https://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100： 智能枚举转换意味着保留失败处理`...`。

尝试将从应用程序的智能枚举这些转换方法标记时出现意外的一些内容。 错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](https://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101： 无法解析引用\*，引用该方法从\*中 *。

处理错误消息中提及的方法时遇到了无效的程序集引用。

错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](https://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102： 错误处理方法\*assembly 中\*: *

尝试将标记错误消息中提及的方法时出现意外的一些内容。

错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](https://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103： 错误处理程序集\*: *

处理程序集时发生意外的错误。

错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要提供在[bug 报表](https://bugzilla.xamarin.com)与具有详细级别启用完整的生成日志一起 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104： 无法将链接程序集{0}按原样混合模式。

链接器无法处理混合模式程序集。

请参阅https://msdn.microsoft.com/library/x0w2664k.aspx为混合模式程序集的详细信息。

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: AOT 错误消息

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001： 无法不 AOT 程序集 *

这通常表示 AOT 编译器中的存在 bug。 请提交 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)与可用于重现错误的项目。

有时，很可能要解决此问题通过禁用增量生成项目的 iOS 生成选项中 (但它依然是一个 bug，因此请仍报告)。

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: AOT 限制： 方法 ' *' 必须是静态的因为它用 [MonoPInvokeCallback] 修饰。 请参阅 [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

此错误消息来自 AOT 编译器。

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003： 冲突-调试和-llvm 选项。 软调试被禁用。

启用 LLVM 后，不支持调试。 如果你需要调试应用程序，请先禁用 LLVM。

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004： 无法不 AOT 程序集 * 因为它不存在。

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005： 的依赖关系\*的程序集\*找不到。 请查看项目的引用。

这通常发生时的程序集引用另一个版本的平台程序集 （通常 mscorlib.dll 的.NET 4 版本）。

这不受支持，可能不生成或正确执行 （程序集可能从 Xamarin.iOS 版并没有的 mscorlib.dll 的.NET 4 版本使用 API）。

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006： 无法计算项目的完整的依赖项映射。 因为 Xamarin.iOS 无法正确检测到什么需要重新生成 （和新增功能不需要重新生成），这将导致生成速度要慢。 请查看以前的警告，有关详细信息。

 生成或正确执行 （程序集可能从 Xamarin.iOS 版并没有的 mscorlib.dll 的.NET 4 版本使用 API）。

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007： 调试启用 llvm 后，将不加载文件 (*.mdb) 的信息。

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: Bitcode 支持还要求 LLVM AOT 后端使用 (-llvm)

Bitcode 支持还要求 LLVM AOT 后端使用 (-llvm)。

禁用 Bitcode 支持或启用 LLVM。

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx： 代码生成错误消息

### <a name="mt40xx-main"></a>MT40xx： 主

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001： 主模板无法扩展到`*`。

生成 main.m 时出错。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002： 编译 P/Invoke 方法生成的代码失败。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

无法编译 P/Invoke 方法生成的代码。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt41xx-registrar"></a>MT41xx： 注册机构

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: 注册机构无法生成类型的签名`*`。

运行时不知道如何封送到/从目标 C.的导出 API 中发现了某个类型

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: 注册机构找到无效的类型`*`方法签名中`*`。 请改用 `*`。

这当前仅发生与一种类型： System.DateTime。 改为使用 OBJECTIVE-C 的等效项 (NSDate)。

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: 注册机构找到无效的类型`*`方法签名中`*`： 的类型实现 INativeObject，但没有采用两个构造函数 (IntPtr，bool) 自变量

发生这种情况时注册器遇到具有所述的特性的签名中的类型。 注册机构可能需要创建新类型的实例，并且在这种情况下它需要 (IntPtr，bool) 的构造函数签名的第一个自变量 (IntPtr) 指定的托管的句柄，第二个如果调用方将本机的所有权（如果此值为 false 将在对象上调用保留） 处理。

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: 注册机构不能封送类型的返回值`*`方法签名中`*`。

运行时不知道如何封送到/从目标 C.的导出 API 中发现了某个类型

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: 注册机构不能封送类型的参数`*`方法签名中`*`。

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: 注册机构不能封送结构的返回值`*`方法签名中`*`。

运行时不知道如何封送到/从目标 C.的导出 API 中发现了某个类型

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: 注册机构不能封送类型的参数`*`方法签名中`+`。

运行时不知道如何封送到/从目标 C.的导出 API 中发现了某个类型

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: 注册机构无法获取托管类型的 ObjectiveC 类型`*`。

运行时不知道如何封送到/从目标 C.的导出 API 中发现了某个类型

如果你认为 Xamarin.iOS 应支持所涉及的类型，请文件在的增强功能请求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109： 编译生成的注册机构代码失败。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

无法为注册机构进行编译时生成的代码。 生成日志将包含本机编译器，以解释为什么不编译代码的输出。

这始终是 Xamarin.iOS; 中的 bug在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)与你的项目或测试用例。

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: 注册机构不能封送类型的 out 参数`*`方法签名中`*`。

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: 注册机构无法生成类型的签名`*`方法中`*`。

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: 注册机构找到无效的类型`*`。 注册 Objective C 的泛型类型不受支持，并且可能导致随机行为和/或崩溃 (有关向后兼容的 Xamarin.iOS 的较旧版本可通过将传递忽略此错误`--unsupported--enable-generics-in-registrar`作为其他 mtouch在项目的 iOS 生成选项页中的自变量。 请参阅[developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar)有关详细信息)。

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: 注册机构找到泛型方法: '\*。\*'。 导出泛型方法不受支持，并且将导致随机行为和/或崩溃。

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114： 中的方法注册机构意外的错误\*。\*-请文件在一个 bug 报告 http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116： 无法注册程序集 *: *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: 注册机构签名不匹配方法中找到 *。*-选择器指示该方法采用 * 参数，而托管的方法具有 * 参数。

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118： 无法注册两个托管的类型 (\*和\*) 具有相同的本机名称 (*)。

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119： 无法注册选择器\*of member\*。 * 原因在不同的成员上已注册的选择器。

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: 注册机构找到未知的字段类型\*字段中\*。 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

此错误表示 Xamarin.iOS 中的存在 bug。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121： 不能使用 GCC / G + + 来编译静态注册机构中的生成的代码时使用帐户框架 （在编译期间使用的 Apple 提供的标头文件要求 Clang）。 请使用 Clang (-编译器： clang) 或动态注册机构 (-注册机构： 动态)。

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122： 不能使用 Clang 编译器中提供 *。* SDK 进行编译时非 ASCII 静态的注册机构从生成的代码键入名称 (*) 都位于应用程序。 请使用 GCC / G + + (-编译器： gcc | g + +)，动态注册机构 (-注册机构： 动态) 或更高版本的 SDK。

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123： 可变参数函数中的可变参数参数的类型 ' *' 必须是 System.IntPtr。

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124： 无效 * 上找到 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

此错误表示 Xamarin.iOS 中的存在 bug。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: 注册机构找到无效的类型\*中方法的签名\*: 接口必须指定其包装类型的协议特性。

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126： 无法注册两个托管的协议 (\*和\*) 具有相同的本机名称 (*)。

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127： 无法注册多个方法的接口方法\*(这实现\*)。

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: 注册机构找到了无效的泛型参数类型\*中方法\*。 泛型参数必须具有 NSObject 约束。

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: 注册机构找到泛型返回类型无效\*中方法\*。 泛型的返回类型必须具有 NSObject 约束。

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: 注册机构不能导出泛型类中的静态方法 (*)。

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: 注册机构无法导出泛型类中的静态属性 (\*。\*)。

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: 注册机构找到泛型返回类型无效\*加入的属性。\*。 返回类型必须具有 NSObject 约束。

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133： 无法构造类型的实例 * 从 Objective C 因为类型是泛型。 [运行时异常]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134： 你的应用程序正在使用 * 框架，不包括在 iOS SDK 将生成应用程序 (在 iOS 中引入了此框架 *，而你正在使用 iOS 生成 * SDK。)请在您的应用程序的 iOS 生成选项中选择了较新的 SDK。

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135： 的成员\*。\*具有未指定选择器导出属性。 选择器是必需的。

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: 注册机构不能封送的参数类型\*的参数\*方法中\*。 *

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: 注册机构不能封送的属性类型\*of 的属性 '\*。 *'。

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: 注册机构不能封送的属性类型\*of 的属性 '\*。 *'。 具有 [连接] 特性的属性必须具有 NSObject 属性类型 （或 NSObject 的一个子类）。

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: 注册机构签名不匹配方法中找到 *。*-选择器指示可变参数方法采用 * 参数，而托管的方法具有 * 参数。

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141： 无法注册选择器\*on member\*因为 Xamarin.iOS 隐式注册此选择器。

发生这种情况是当子类化 framework 类型，并尝试实现保留，发布或 dealloc 方法：

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

它是可以重写这些方法，如果类不是框架的第一个子类但是键入：

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

在这种情况下将重写 Xamarin.iOS `retain`，`release`和`dealloc`上`MyNSObject`类，并且没有冲突。

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142： 注册类型失败 *。

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: ObjectiveC 类 * 找不到注册，它似乎不派生自任何已知的 ObjectiveC 类 （包括 NSObject）。

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144： 无法注册的方法 * 因为它没有关联的 trampoline。 在一个 bug 报告，请记录http://bugzilla.xamarin.com。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145： 无效的枚举 *： 用 [本机] 特性的枚举必须具有长或 ulong 基础枚举类型。

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146： 类的注册机构特性的 Name 参数\*(\*) 包含无效字符:\*(\*)。

Objectice C 类的名称不能包含空格，这意味着`Register`上相应的托管类的特性不能有`Name`参数既不能包含空格。

请验证`Register`错误消息中提及的托管类上的属性不包含任何空格。

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147： 检测到使用动态注册机构时继承自 JSExport 协议的协议。 不能动态; 导出到 JavaScriptCore 协议必须使用静态的注册机构 (添加-注册机构： 静态映射到在项目的 iOS 生成选项中进行选择静态注册机构中的其他 mtouch 自变量)。

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: 注册机构找到泛型协议: *。 导出泛型协议不受支持。

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149： 无法注册方法\*。\*因为第一个参数的类型 (\*) 不匹配的类别类型 (\*)。

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150： 无法注册类型\*因为类型属性 (\*) 在其类别属性将不能继承 NSObject。

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151： 无法注册类型 *，因为没有设置其 Category 属性中的类型属性。

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152： 无法注册类型 * 作为类别因为它实现了 INativeObject 或子类 NSObject。

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153： 无法注册类型\*作为类别因为它是泛型。

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154： 无法注册的方法\*作为类别方法因为它是泛型。

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155： 无法注册的方法\*'with selector\*上的类别方法 * 因为 Objective C 已经具有此选择器的实现。

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156： 无法注册两个类别 (\*和\*) 具有相同的本机名称 (*)。

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157： 无法注册类别方法\*由于至少一个参数是必需 (其类型必须与类别类型相匹配\*)

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158： 无法注册构造函数 * 类别中 * 因为类别中的构造函数不受支持。

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159： 无法注册的方法 * 作为类别方法因为类别方法必须是静态的。

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160： 无效字符\*(\*) 选择器中找到\*for\*。

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: 注册机构找到不受支持的结构\*: 结构中的所有字段还必须都是结构 (字段\*with type{2}不是一种结构)。

注册机构找到具有不受支持的字段的结构。

一个结构，它就会遭受 Objective C 中的所有字段还必须都是结构 （而不是类）。

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: 类型 '\*(用作 * {2}) 中不可用 * * (中引入了 * *)\*请生成与较新 * SDK （通常通过使用 Xcode 的最新版本。

注册机构找到最新的 SDK 中未包括的类型。

请升级 Xcode。

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>在注册机构 （*） MT4163： 内部错误。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

此错误表示 Xamarin.iOS 中的存在 bug。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164： 无法导出的属性\*因为其选择器\*是 OBJECTIVE-C 的关键字。 请使用其他名称。

在上述属性的选择器不是有效的 OBJECTIVE-C 的标识符。

请使用有效的 OBJECTIVE-C 的标识符作为选择器。

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: 注册机构找不到类型 System.Void，在任何引用的程序集。

此错误最可能表示 Xamarin.iOS 中的存在 bug。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166： 无法注册的方法\*因为签名包含类型 (\*) 这不是引用类型。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167： 无法注册的方法\*因为签名包含泛型类型 (\*) 具有不 NSObject 子类 （*） 的泛型自变量类型。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168： 无法注册类型 ' {托管\_名称} 因为其 Objective C 名称 {导出\_名称} 是 OBJECTIVE-C 的关键字。 请使用其他名称。

所涉及的类型的 OBJECTIVE-C 的名称不是有效的 OBJECTIVE-C 的标识符。

请使用有效的 OBJECTIVE-C 的标识符。

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169： 无法生成 P/Invoke 包装器 {方法}: {message}

Xamarin.iOS 生成提及的 P/Invoke 包装函数失败。
请检查报告的错误消息中的根本原因。

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: 注册机构不能从转换 {托管 type} 为 {本机 type} 中方法 {方法} 的返回值。

请参阅错误的说明<a href="#MT4172">MT4172</a>。

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171： 成员 {成员} BindAs 属性无效： BindAs 类型 {type} 是 {type} 的属性类型不同。

请确保 BindAs 属性中的类型匹配的附加到的成员类型。

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: 注册机构不能从转换 {本机 type} 为 {托管 type} 中方法 {方法} 的参数 {参数 name}。

注册机构不支持对上述类型之间进行转换。

这是在 Xamarin.iOS 一个 bug，如果问题 API 提供的 Xamarin.iOS;请提交在 bug [ http://bugzilla.xamarin.com ] [ 1]。

如果到此运行时本机库绑定项目开发时，我们打开与将添加对新类型的组合的支持。 如果出现这种情况，请文件的增强功能请求 ([http://bugzilla.xamarin.com][2]) 与测试用例，我们将评估它。

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: GCC 和工具链错误消息

### <a name="mt51xx-compilation"></a>MT51xx： 编译

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101： 缺少 * 编译器。 请安装 Xcode 命令行工具组件

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: 未能组建文件 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: 无法将该文件编译 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104： 找不到都\*和\*编译器。 请安装 Xcode 命令行工具组件

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106： 无法编译文件 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt52xx-linking"></a>MT52xx： 链接

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201： 本机链接失败。 请查看生成日志和提供给 gcc 的用户标志: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202： 本机链接失败。 请查看生成日志。

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203： 本机链接警告: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209： 本机链接错误: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210： 本机链接失败，未定义的符号: *。 请验证已引用所有必要的框架和本机库中正确链接。

本机链接器找不到某个位置引用的符号时，将发生这种情况。 这可能会发生情况的原因包括：

* 第三方绑定需要一个框架，但绑定未指定在其`[LinkWith]`属性。 解决方案：
  - 如果你的第三方绑定中，作者或能够访问其源，修改绑定的`[LinkWith]`属性以包括它需要的 framework:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - 如果你不能修改第三方绑定，则可以手动将链接的所需的框架通过传递`-gcc_flags '-framework SystemFramework'`到`mtouch`（这可通过修改项目的 iOS 生成选项页中的其他 mtouch 自变量。 请记住，此操作必须的每个项目配置）。
* 在某些情况下托管的绑定组合的几个本机库，和所有必须包含在绑定中。 很可能有多个本机库中的每个绑定项目，因此该解决方案是将所有所需的本机库添加到绑定项目。</li>
* 托管的绑定是指不存在于本机库的本机符号。
    这通常发生在绑定已经存在了一段时间，并在该时间段，以便为特定的本机类已删除或重命名，而不更新绑定已修改的本机代码。
* 使用 P/Invoke 引用不存在的本机符号。 从 Xamarin.iOS 7.4 <a href="#MT5214">MT5214</a>这种情况下，将报告错误 （有关详细信息，请参阅 MT5214）。
* 第三方绑定 / 库生成使用 c + +，但绑定未指定在其`[LinkWith]`属性。 这通常是很容易识别，由于符号制定有重整 c + + 符号 (一个常见示例是`__ZNKSt9exception4whatEv`)。
  - 如果你的第三方绑定中，作者或能够访问其源，修改绑定的`[LinkWith]`要设置属性`IsCxx`标志：

            [LinkWith ("mylib.a", IsCxx = true)]

  - 如果您不能修改第三方绑定，或要与第三方库手动链接，你可以通过将传递设置等效标志<code>-cxx</code>到 mtouch （这可通过修改项目的 iOS 生成选项页中的其他 mtouch 自变量. 请记住，此操作必须的每个项目配置）。

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211： 本机链接失败，未定义的 OBJECTIVE-C 类： \*。 符号\*找不到中的任何库或与你的应用程序链接的框架。

本机链接器找不到某个位置引用的 OBJECTIVE-C 类时，将发生这种情况。 这可能会发生情况的原因包括： 相同[MT5210](#MT5210)和此外：

* 第三方绑定绑定 OBJECTIVE-C 的协议，但未未对其进行批注与<code>[Protocol]</code>在其 api 定义中的属性。 解决方案：
  - 添加缺少`[Protocol]`属性：

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212： 本机链接失败，重复的符号: *。

当本机链接器遇到所有本机库之间的重复的符号时，将发生这种情况。 以下此错误可能有一个或多个[MT5213](#MT5213)符号的位置的每个匹配项的错误。 此错误的可能原因：

* 同一个本机库是包含两次。
* 两个不同的本机库碰巧定义相同的符号。
* 本机库未正确生成，并且多次包含相同的符号。
  您可以通过使用以下命令从终端集确认这 （替换为 i386 x86_64/armv7/armv7s/arm64 体系结构根据你正在生成的）：

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  有几种方式来解决此问题：

  - 请求的本机库提供程序修复此错误，并提供的更新的版本。
  - 通过删除额外的对象文件 （这仅适用如果问题实际上是重复的对象文件） 自行修复

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - 要求链接器以删除未使用的代码。 Xamarin.iOS 将自动执行此操作如果满足所有以下条件：
    - 所有第三方绑定`[LinkWith]`属性已启用 SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - 不`-gcc_flags`传递给 mtouch （在项目的 iOS 生成选项的其他 mtouch 参数字段）。
    - 还有可能要求链接器直接通过添加删除未使用的代码`-gcc_flags -dead_strip`到项目的 iOS 中的其他 mtouch 参数生成选项。

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213： 重复中的符号: * （与以前的错误相关位置）

仅与报告此错误[MT5212](#MT5212)。 请参阅[MT5212](#MT5212)有关详细信息。

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214： 本机链接失败，未定义的符号: *。 引用此符号托管的成员 *。 请验证所有必需的框架已被引用和本机库链接。

当托管的代码中包含不存在的本机方法使用 P/Invoke 时，会报告此错误。 例如：

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

有几个可能的解决方案：

  -  从源代码中删除 P/Invoke 的问题。
  -  启用托管链接器 （这通过在项目的 iOS 生成选项中为"所有程序集"设置"链接器行为"） 的所有程序集。 这将有效地从应用程序删除所有 P/Invokes 不使用 （自动，而不是手动如以前的点）。 其缺点在于，这将使模拟器生成稍慢一些，并且如果正在使用反射-找不到链接器有关的详细信息，它可能会中断你的应用[此处](~/ios/deploy-test/linker.md))
  -  创建第二个本机库，其中包含缺少本机符号。 请注意，这只是一种解决方法 （如果你尝试调用这些函数，您的应用程序时将崩溃）。

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: 对引用 * 可能需要额外的 framework = XXX 或-lXXX 说明本机链接器

这是一个警告，指示检测到使用 P/Invoke 来引用该库问题，但应用程序不与之链接。

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: 本机链接失败 *。 请在一个 bug 报告，记录 http://bugzilla.xamarin.com

链接 AOT 编译器的输出时，会报告此错误。

此错误最可能表示 Xamarin.iOS 中的存在 bug。 在一个 bug 报告，请记录[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217： 本机链接可能失败，因为链接器命令行太长 (* 字符)。

本机链接失败，并且可能出现这种因为链接器命令已太长。

Xamarin.iOS 项目将会经常引用本机符号动态，这意味着本机链接器可能会在本机链接过程中，删除此类本机符号因为本机链接器看不到，使用这些符号。

Xamarin.iOS 将通常要求本机链接器中，需要使用此类符号`-u symbol`链接器标志，但如果有许多此类符号，整个命令行可能超过了最大命令行长度为指定的操作系统。

没有此类动态符号的几个可能的源：

* P/Invoke 移动到静态链接库中的方法 (其中 dll 名称是`__Internal`DllImport 特性中`[DllImport ("__Internal")]`)。
* 字段对静态链接的库中的内存位置的绑定项目引用 (`[Field]`属性)。
* Objective C 类中静态链接库 （使用增量生成时或在不使用静态的注册机构） 绑定项目引用。

可能的解决方案：

* 启用托管链接器 （如果而不是唯一的 SDK 程序集的所有程序集可能）。 动态符号，以便链接器的命令行不会超过最大值，这可能会删除足够多的源。
* 减少 P/Invokes、 字段引用和/或 OBJECTIVE-C 的类数量。
* 重写动态的符号具有较短的名称。
* 传递`-dlsym:false`作为项目的 iOS 中的其他 mtouch 参数生成选项。 使用此选项，Xamarin.iOS 在 AOT 编译的代码，将生成的本机引用，并且无需来询问链接器以保留此符号。 但是，此仅适用于设备生成，而且它将导致链接器错误，如果在静态库中不存在的函数有 P/Invoke。
* 传递`--dynamic-symbol-mode=code`作为项目的 iOS 中存在其他 mtouch 参数生成选项。 使用此选项，Xamarin.iOS 将生成其他本机代码的引用而不是要求本机链接器中，需要使用命令行自变量这些符号这些符号。 此方法的缺点是，它将某种程度上增加的可执行文件的大小。
* 通过将传递启用静态注册机构`--registrar:static`作为项目的 iOS 中的其他 mtouch 参数生成选项 （为模拟器版本，因为静态注册机构已是默认值为设备版本）。 静态注册机构将生成代码的静态，引用 OBJECTIVE-C 的类，因此无需要求本机链接器需要这样的类。
* 禁用增量生成 （对于设备生成）。 启用增量生成后，由静态注册机构生成的代码不会视为本机链接器，这意味着即 Xamarin.iOS 仍必须请求链接器以保留引用 OBJECTIVE-C 的类。 因此禁用增量生成，则将阻止这一要求。

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218： 不能忽略动态符号 {symbol} (-忽略动态符号 = {符号}) 因为它未检测到为动态符号。

命令行自变量`--ignore-dynamic-symbol=symbol`传递，但此符号不是已识别出为必须手动保留一个动态符号的符号。

有两个主要原因：

* 符号名不正确。
    * 不前面预置的符号名称以下划线。
    * Objective C 类的符号是`OBJC_CLASS_$_<classname>`。
* 符号正确，但它是一个符号，已保留 （某些生成选项的原因的动态符号改变确切列表） 的正常方式。

### <a name="mt53xx-other-tools"></a>其他工具 MT53xx:

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301： 缺少最小化工具。 请安装 Xcode 命令行工具组件

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302： 缺少 dsymutil 工具。 请安装 Xcode 命令行工具组件

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303： 未能生成调试符号 （dSYM 目录）。 请查看生成日志。

要创建的调试符号的最终.app 目录上运行 dsymutil 时出错。 请查看生成日志，请参阅 dsymutil 的输出，并查看如何修复它。

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304： 失败最小化最终二进制文件。 请查看生成日志。

运行条带工具删除从应用程序的调试信息时出错。

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305： 缺少 lipo 工具。 请安装 Xcode 命令行工具组件

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306： 无法创建 fat 库。 请查看生成日志。

在运行 lipo 工具时出错。 请查看生成日志，以查看报告 lipo 的错误。

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307： 注册可执行文件失败。 请查看生成日志。

登录应用程序时出错。 请查看生成日志，以查看报告 codesign 的错误。

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mtouch 内部工具错误消息

### <a name="mt600x-stripper"></a>MT600x: Stripper

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Cecil 的运行版本不支持最小化的程序集

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002： 无法不去除程序集`*`。

应用程序中去除从程序集 （删除 IL 代码） 的托管的代码时出错。

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [UnauthorizedAccessException 消息]

当去除调试从应用程序的符号时，安全出错。

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: MSBuild 错误消息

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001： 无法解析主机 Ip WiFi 调试器设置。

*MSBuild 任务： DetectDebugNetworkConfigurationTaskBase*

故障排除步骤：

- 尝试运行`csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'`（，应为你提供的 IP 地址而不是错误很明显）。
- 尝试运行"ping\`主机名\`"这可能会为你提供的详细信息，如： `cannot resolve MyHost.local: Unknown host`

在某些情况下，它是"本地网络"问题，并可以通过未知的主机添加对它进行寻址`127.0.0.1   MyHost.local`中`/etc/hosts`。

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002： 此计算机没有任何网络适配器。 调试或在设备上分析 wifi 时，这是必需的。

*MSBuild 任务： DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>应用扩展的 MT7003: * 不包含 Info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>应用扩展的 MT7004: * 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>应用扩展的 MT7005: * 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006： 应用扩展\*具有无效的 CFBundleIdentifier (\*)，不会开始与主应用捆绑 CFBundleIdentifier （*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007： 应用扩展\*具有 CFBundleIdentifier (\*)，以非法后缀".key"结尾。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>应用扩展的 MT7008: * 未指定 CFBundleShortVersionString。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>应用扩展的 MT7009: * 具有无效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>应用扩展的 MT7010: * 具有无效的 Info.plist: NSExtension 字典不包含 NSExtensionPointIdentifier 值。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: WatchKit 扩展 * 具有无效的 Info.plist: NSExtension 字典不包含 NSExtensionAttributes 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: WatchKit 扩展 * 不具有恰好一个 watch 应用。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: WatchKit 扩展 * 具有无效的 Info.plist: UIRequiredDeviceCapabilities 必须包含监视助理功能。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014： 监视应用程序 * 不包含 Info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015： 监视应用程序 * 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016： 监视应用程序\*具有无效的 CFBundleIdentifier (\*)，不会开始与主应用捆绑 CFBundleIdentifier （*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017： 监视应用程序\*不具有有效的 UIDeviceFamily 值。 预期的监视 (4)，但找到 '\* （*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018： 监视应用程序 * 未指定 CFBundleExecutable

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019： 监视应用程序\*具有无效的 WKCompanionAppBundleIdentifier 值 (\*)，它与主应用捆绑 CFBundleIdentifier 不匹配 (*)。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020： 监视应用程序 * 具有无效的 Info.plist: WKWatchKitApp 密钥必须存在且具有值为 'true'。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021： 监视应用程序 * 具有无效的 Info.plist: LSRequiresIPhoneOS 密钥不能存在。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022： 监视应用程序 * 不包含监视扩展。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>监视扩展的 MT7023: * 不包含 Info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>监视扩展的 MT7024: * 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>监视扩展的 MT7025: * 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026： 监视扩展\*具有无效的 CFBundleIdentifier (\*)，不会开始与主应用捆绑 CFBundleIdentifier （*）。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027： 监视扩展\*具有 CFBundleIdentifier (\*)，以非法后缀".key"结尾。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>监视扩展的 MT7028: * 具有无效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>监视扩展的 MT7029: * 具有无效的 Info.plist: NSExtensionPointIdentifier 必须是"com.apple.watchkit"。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>监视扩展的 MT7030: * 具有无效的 Info.plist: NSExtension 字典必须包含 NSExtensionAttributes。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>监视扩展的 MT7031: * 具有无效的 Info.plist: NSExtensionAttributes 字典必须包含 WKAppBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: WatchKit 扩展 * 具有无效的 Info.plist: UIRequiredDeviceCapabilities 不应包含监视助理功能。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033： 监视应用程序 * 不包含 Info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034： 监视应用程序 * 未指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035： 监视应用程序\*不具有有效的 UIDeviceFamily 值。 预期\*但找到'\* (\*)。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036： 监视应用程序 * 未指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: WatchKit 扩展 {extensionName} 具有无效的 WKAppBundleIdentifier 值 (\*)，它与监视应用程序的 CFBundleIdentifier 不匹配 (\*)。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038： 监视应用程序 * 具有无效的 Info.plist: WKCompanionAppBundleIdentifier 必须存在并且必须与主应用捆绑 CFBundleIdentifier 相匹配。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039： 监视应用程序 * 具有无效的 Info.plist: LSRequiresIPhoneOS 密钥不能存在。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040： 应用程序捆绑包 {AppBundlePath} 不包含 Info.plist。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Main Info.plist 路径并不指定 CFBundleIdentifier。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Main Info.plist 路径并不指定 CFBundleExecutable。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Main Info.plist 路径并不指定 CFBundleSupportedPlatforms。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Main Info.plist 路径并不指定 UIDeviceFamily。

*MSBuild 任务： ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045： 无法识别的格式: *。

*MSBuild 任务： PropertyListEditorTaskBase*

其中 * 可以是：

- 字符串
- array
- dict
- bool
- 实数
- 整数
- date
- 数据

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046： 添加： 条目，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047： 添加： 条目，*，包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048： 添加: * 条目已存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049： 添加： 无法添加项，* 的父级。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050： 删除： 无法删除条目，*，来自父代。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051： 删除： 条目，*，包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052： 删除： 条目，*，不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053： 导入： 条目，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054： 导入： 条目，*，包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055： 导入： 读取文件时出错: *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056： 导入： 无法添加项，* 的父级。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057： 合并： 无法将数组条目添加到 dict.

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058： 合并： 指定项必须是一个容器。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059： 合并： 条目，*，包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060： 合并： 条目，*，不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061： 合并： 读取文件时出错: *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062： 设置： 条目，*，未正确指定。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063： 设置： 条目，*，包含无效的数组索引。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064： 设置： 条目，*，不存在。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065： 未知的 PropertyList 编辑器操作: *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066： 加载时出错 *: *。

*MSBuild 任务： PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067： 错误保存 *: *。

*MSBuild 任务： PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx： 运行时错误消息

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>之间的本机 Xamarin.iOS 运行时和 monotouch.dll MT8001： 版本不匹配。 请重新安装 Xamarin.iOS。

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002： 找不到方法\*加入的 type\*。

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: 未能找到封闭式泛型方法\*on 的 type\*。

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004： 无法创建的实例 * 的本机对象 0 x * (的类型 *)，因为另一个实例已存在此本机对象 (类型的 *)。

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005： 包装类型\*缺少其本机 ObjectiveC 类\*。

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006： 未能找到为选择器\*on 的 type\*

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007： 无法获取的方法说明符选择器\*on 的 type\*，因为选择器并不对应于一种方法

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008： 的 Xamarin.iOS.dll 加载的版本编译为 * 位，而进程是 * bits。 请提交在 bug http://bugzilla.xamarin.com。

这表示在生成过程中出错。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009： 找不到用于委托的方法的转换方法的块 *。* s 参数 # *。 请提交在 bug http://bugzilla.xamarin.com。

这指示 API 未正确绑定。 如果这是通过 Xamarin 公开的 API，请在我们 bugzilla 中提交 bug ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS))，如果它是一种第三方绑定，请与供应商联系。

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010： 本机类型大小不匹配 Xamarin。[iOS |Mac].dll 和正在执行的体系结构。 Xamarin。[iOS |Mac].dll 为生成的 *-位，而当前的进程是 *-位。

这表示在生成过程中出错。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011： 找不到到块转换属性 ([DelegateProxy]) 的委托的方法的返回值 *。*。 请提交在 bug http://bugzilla.xamarin.com。

Xamarin.iOS 无法在运行时 （若要将委托转换为块） 中找到所需的方法。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012： 该方法的返回值为无效 DelegateProxyAttribute *。*: DelegateType 为 null。 请提交在 bug http://bugzilla.xamarin.com。

问题的方法的 DelegateProxy 属性无效。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013： 该方法的返回值为无效 DelegateProxyAttribute *。*: DelegateType ({2}) 指定没有处理程序字段的类型。 请提交在 bug http://bugzilla.xamarin.com。

问题的方法的 DelegateProxy 属性无效。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014： 该方法的返回值为无效 DelegateProxyAttribute *。*: DelegateType ({2}) 处理程序字段为 null。 请提交在 bug http://bugzilla.xamarin.com。

问题的方法的 DelegateProxy 属性无效。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015： 该方法的返回值为无效 DelegateProxyAttribute *。*: DelegateType ({2}) 处理程序字段不是一个委托，它是 *。 请提交在 bug http://bugzilla.xamarin.com。

问题的方法的 DelegateProxy 属性无效。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016： 无法转换进行阻止针对方法的返回值的委托 *。*，因为输入不是委托，它是 *。 请提交在 bug http://bugzilla.xamarin.com。

问题的方法的 DelegateProxy 属性无效。

这通常表示 Xamarin.iOS; 中的 bug请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018： 内部一致性错误。 在一个 bug 报告，请记录http://bugzilla.xamarin.com。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019： 找不到程序集 * 中加载的程序集。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020： 找不到与 MetadataToken 模块 * 程序集中 *。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021： 未知隐式令牌类型: *。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022： 预期的令牌引用 * 为 *，但它是 *。 在一个 bug 报告，请记录http://bugzilla.xamarin.com。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: 实例对象需要构造封闭式泛型方法打开的泛型方法: * (令牌引用: *)。 在一个 bug 报告，请记录http://bugzilla.xamarin.com。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024： 将智能枚举 {smart_type} 中找不到有效的扩展类型。 请提交在 bug https://bugzilla.xamarin.com。

这表示 Xamarin.iOS 中的存在 bug。 请提交在 bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。
