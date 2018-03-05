---
title: "PCL 案例研究： 如何解析为 Microsoft TPL 数据流 NuGet 包与 System.Diagnostics.Tracing 相关的问题？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: c1d8bab1af8082d74f447cd51422a7eedb7c18c4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究： 如何解析为 Microsoft TPL 数据流 NuGet 包与 System.Diagnostics.Tracing 相关的问题？

## <a name="summary"></a>摘要

Xamarin.iOS 和 Xamarin.Android 未实现 100%的作为引用它们允许每个 PCL 配置文件。 Xamarin 项目为 Visual Studio 中用于 Mac、 Visual Studio 和 NuGet 包管理器的实际方便起见，允许使用多个配置文件仅具有_不完整_实现。 例如，Xamarin.iOS 和 Xamarin.Android 都不当前包括"System.Diagnostics.Tracing"PCL 中的类型的完整实现命名空间。 此限制将尝试使用默认值时导致的错误的 3 层`portable-net45+win8+wpa81`Microsoft TPL 数据流 NuGet 包的版本。


## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>解决方法： 切换应用程序项目，以引用`portable-net45+win8+wp8+wpa81`TPL 数据流库版本

（这样可以避免错误和适用于所有最新版本的 Xamarin 所有 3 的层。）

1. 打开应用程序项目`.csproj`在文本编辑器中的文件。

2. 查找类似如下所示的行：

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 将 `portable-net45+win8+wpa81` 更改为 `portable-net45+win8+**wp8**+wpa81`：

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>说明

`portable-net45+win8+wp8+wpa81`版本的库不引用"System.Diagnostics.Tracing.dll"_根本_，因此完全可以避免的问题的所有 3 层。

### <a name="limitations"></a>限制

- `portable-net45+win8+wp8+wpa81`库版本可能不包含功能的 100%`portable-net45+win8+wpa81`版本。

- NuGet 包管理器安装`portable-net45+win8+wpa81`默认情况下，因此你必须手动调整引用 PCL NuGet 包的版本。




## <a name="details-about-the-3-layers-of-errors"></a>有关错误的 3 层的详细信息

1. `System.Diagnostics.Tracing.dll`外观程序集目前从 Xamarin.Android (非公共 Bug 34888) 的所有 Mac 版本不存在且不从所有 Xamarin.iOS 版本低于 9.0 （或低于 XamarinVS 3.11.1443 Windows 上） (中修复[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). 此问题将导致以下错误，具体取决于部署目标和链接器之一设置：

    - > Xamarin.Android.Common.targets： 错误： 加载程序集时出现异常： System.IO.FileNotFoundException： 无法加载程序集 System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a。 可能是可在 Android 配置文件的 Mono 中不存在？

    - > 无法加载文件或程序集 System.Diagnostics.Tracing 或其依赖项之一。 系统找不到指定的文件。 (System.IO.FileNotFoundException)

    - > MTOUCH： 错误 MT3001： 无法不 AOT 程序集 ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll

    - > MTOUCH： 错误 MT2002： 无法解析程序集: System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a



2. 当前["System.Diagnostics.Tracing"中的类型的 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)缺少某些方法重载 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 在构建 Xamarin 应用程序时，此问题会导致以下链接器错误之一：

    - > / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets： 错误： 错误执行任务 LinkAssemblies： 错误 XA2006： 对元数据项引用 System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[]) (在中定义 System.Threading.Tasks.Dataflow，Version = 4.5.24.0，区域性 = neutral，PublicKeyToken = b03f5f7f11d50a3a) 从 System.Threading.Tasks.Dataflow，Version = 4.5.24.0，区域性 = neutral，PublicKeyToken = b03f5f7f11d50a3a 无法解析。

    - > MTOUCH： 错误 MT2002： 无法解析"System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])"引用"System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken =b03f5f7f11d50a3a"


3. 当前["System.Diagnostics.Tracing"中的类型的 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)目前还_空_"假"实现 ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890))。 因此，在运行时使用这些方法的任何尝试可能会产生意外的结果。 为_特定_用例的 Microsoft TPL 数据流库，它看起来对的调用`WriteEvent(System.Int32,System.Object[])`所不需要的大部分库的行为，因此"层 2"的修复 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)，添加空实现） 将 likey 足以用于大多数 Microsoft TPL 数据流用例。




## <a name="questions--answers"></a>问题和解答


###<a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>"我已能够让链接使用启用<code>portable-net45+win8+wpa81</code>版本或 Xamarin.Android 的 Xamarin.iOS 的较旧版本上的库。 如何是否的工作？"

#### <a name="answer"></a>答案

它是_可能_，能获得到完成"成功"生成 （链接启用） 中的 Xamarin.iOS 的较旧版本或在 Mac 上的 Xamarin.Android 如果包括对引用`System.Diagnostics.Tracing.dll`_引用程序集_\[1\]而不是_外观程序集_ \[2]，但遗憾的是这不是"正确"解决方法。 引用程序集仅用于生成时，使用_可移植库_，像应用不特定于平台的代码。 尝试_运行_包含在引用程序集 （而不只是针对其生成） 的代码很可能产生意外的结果。 正确的解决办法是单声道团队，以添加缺少的`WriteEvent(System.Int32,System.Object[])`重载到[ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)类型 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 切换到最佳选项是现在`portable-net45+win8+wp8+wpa81`版本的 Microsoft TPL 数据流库上面的解决方法一节中所述。

(可能阅读本文后看到从堆栈溢出的相关旧的、 更短答案的任何人 (<http://stackoverflow.com/a/23591322/2561894>)，请注意，引用程序集和外观程序集之间的区别是_不_提到。)


**\[1\] "引用程序集"的位置**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] "外观程序集"的位置**

Windows: <code>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\<strong>Facades</strong>\System.Diagnostics.Tracing.dll</code>

Mac (Mono): <code>/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/<strong>Facades</strong>/System.Diagnostics.Tracing.dll</code>


###<a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>"将它帮助如果我手动添加对"System.Diagnostics.Tracing"外观程序集引用？"

尤其是解决此问题，使用以下 2 个步骤？

1. 复制`System.Diagnostics.Tracing.dll`外观程序集到应用程序项目文件夹从以下位置之一：

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. Xamarin.iOS 或 Xamarin.Android 应用程序项目中添加外观程序集的引用。

#### <a name="answer"></a>答案

否，这将不会帮助。

- 对于 Xamarin.iOS 9.0 或任何最新版本的 Windows 上的 Xamarin.Android，此解决方法是严格冗余的并且可能导致编译错误类似于"具有相同标识程序集 System.Diagnostics.Tracing 具有已导入。"。

- 针对 Xamarin.iOS 8.10 用或较低或针对 Mac 上的 Xamarin.Android，将有助于此解决方法，但_仅_"层 1"缺少程序集问题。 它将_不_解决"层 2"链接器错误，因此它不是一个完整的解决方案。


###"可以使用<a href="https://www.nuget.org/packages/System.Diagnostics.Tracing/">System.Diagnostics.Tracing NuGet 包</a>来解决问题？"

#### <a name="answer"></a>答案

否，NuGet 3.0"System.Diagnostics.Tracing"包仅包含"DNXCore50"和"netcore50"的特定于平台的实现。 它显式_省略_Xamarin.Android ("MonoAndroid") 和 Xamarin.iOS （"MonoTouch"和"xamarinios"） 的实现。 这意味着，安装包将具有_无效_为 Xamarin.Android 和 Xamarin.iOS 项目。 NuGet 包假定这两个这些平台提供其_自己_实现的类型。 此假设条件是在具有 Mono 的意义上的"正确" 实现的命名空间，而应用作下讨论点\#2 和\#3 的"详细信息的错误的 3 层"上方，实现是当前不完整。 因此，正确地执行修复将是为单声道团队解决[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)和[Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)。


## <a name="next-steps"></a>后续步骤

有关进一步的帮助，请与我们联系，或如果此问题仍然存在即使利用的上述信息，请参阅[哪些支持选项均可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要的文件的新 bug。
