---
title: "疑难解答"
ms.topic: article
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/0201
ms.openlocfilehash: c5f6e6ef61e3705920770d317e4d5f680d4c8fbe
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshooting"></a>疑难解答

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS 无法解析 System.ValueTuple

此错误是由于使用 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更低版本） 是仅与兼容**System.ValueTuple NuGet 4.3.0** （或更低版本）。

- **Visual Studio 2017 Update 2** （15.2 或更高版本） 才与兼容**System.ValueTuple NuGet 4.3.1**或更高版本。

请选择与你的 Visual Studio 2017 安装相对应的正确 System.ValueTuple NuGet。


## <a name="receiving-error-retrieving-update-information-error-message"></a>收到错误检索更新信息错误消息

当尝试更新软件和此错误消息出现时，请尝试在 IDE 中重启 IDE 和注销然后再在到你的帐户。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何使用接口生成器创建插座或操作？

使用针对 Mac 和 Visual studio 的 Xamarin 设计器中为 Visual Studio 中的 iOS 引入，Xamarin.iOS 开发人员现在可以利用的创建通过情节提要和.xibs UI。 请参阅[Hello，iOS](~/ios/get-started/hello-ios/index.md)使用设计器的详细信息的指南。

此外可以对 Apple 的引用[Outlet](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)和[操作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)在 IB 中使用的容器和操作的详细信息的指南。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding 引发 NotSupportedException

您可能正在使用未按默认添加的编码。 检查[国际化](~/ios/app-fundamentals/localization/index.md)页以了解如何添加对多个编码的支持。

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException （其他）

成员可能已删除链接器，并因此在运行时程序集中不存在。  有几个解决方案：

-  添加[[保留]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)属性成员。  这将阻止链接器中将其删除。
-  在调用时[mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) ，使用**-nolink**或**-linksdkonly**选项。 -    **-nolink**禁用所有链接。
-    **-linksdkonly**只能将链接提供 Xamarin.iOS 的程序集，如*monotouch.dll*或 xamarin.ios.dll。

请注意，以便生成可执行文件是较小; 链接程序集因此，禁用链接可能会导致更大的可执行文件不是需要的。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>如果您听到 ModelNotImplementedException

如果收到此异常，这意味着调用基。重写一个模型的类上的方法 （)。 不需要的模型 （这些是与 [型号] 属性标记的类） 类中调用基方法。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>此类不是编码符合键 XXXX 键值

如果在加载 NIB 文件时收到此错误这意味着，在托管类找不到它们的值。 这意味着您缺少如下声明：

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

上面的定义自动生成的 Visual Studio for Mac 的适用于 Mac 中向 Visual Studio 添加任何 XIB 文件`NAME_OF_YOUR_XIB_FILE.designer.xib.cs`文件。

此外，包含上面的代码的类型必须具有的一个子类[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)。  如果包含类型是命名空间内，它还应具有[[注册]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)提供没有命名空间的类型名称 （如接口生成器不支持在类型中的命名空间） 的属性：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>未知的类接口生成器文件中的 XXXX

如果在接口生成器文件中定义的类，但你不提供实际实现的示例在 C# 代码中，会生成此错误。

你需要添加一些代码如下：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException： 没有构造函数为 Foo.Bar::ctor(System.IntPtr) 找到

当代码尝试实例化接口生成器文件中引用的类的实例时，会在运行时产生此错误。 这意味着您忘记了要添加的构造函数的参数作为单个 IntPtr。

与 IntPtr 句柄构造函数用于将与对应的非托管表示形式的托管的对象绑定。

若要解决此问题，与类 Foo.Bar 添加以下代码行：

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Type {Foo} 不包含的定义`GetNativeField' and no extension method `GetNativeField 的类型无法找到 {Foo}

如果你在设计器生成的文件中获得此错误 (*。 xib.designer.cs)，则表示以下两项操作之一：

 **1） 缺少分部类或基类**

设计器生成的分部类必须具有相应部分继承的类在用户代码中的某些子类从`NSObject`，通常`UIViewController`。 确保你有这样的类将给错误的类型。

 **2） 默认命名空间更改**

使用你的项目的默认命名空间设置生成了设计器的文件。 如果您更改这些设置，或者重命名项目，生成的分部类可能不再处于用户代码的对应相同的命名空间。

在项目选项对话框中，可以找到 Namespace 设置。 在中找到的默认命名空间**常规-> Main 设置**部分。 如果它为空，则你的项目的名称用作默认值。 在找不到更高级的命名空间设置**源代码->.NET 命名策略**部分。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>针对操作的警告： 永远不会使用 Foo 的私有方法。 (CS0169)

接口生成器文件的操作被连接到小组件通过在运行时，反射，因此应此警告。

你可以使用"#pragma 警告禁用 0169年""#pragma 警告启用 0169" 围绕你的操作如果你想要禁止显示此警告只为这些方法，或如果你想要禁用它为你的整个项目 （非添加 0169 对编译器选项中的"忽略警告"字段建议）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失败，出现以下消息： 无法打开程序集 ' / path/to/yourproject.exe

如果你看到此错误消息，通常该问题将是你的项目的绝对路径包含空格。 这将在 Xamarin.iOS 的未来版本中修复，但可以通过将项目移到不含空格的文件夹来解决该问题。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sqlite3 版本太旧，-请至少升级到 v3.5.0 ！

当你执行所有以下操作时，将发生这种情况：

1.  使用 Mono.Data.Sqlite
1.  使用 Mac OS X Leopard (10.5)
1.  运行你的应用在模拟器中。


问题是 Mono 选取 OS X `libsqlite3.dylib`，不 iPhoneSimulator 的`libsqlite3.dylib`文件。 你的应用程序*将*设备，但不只是你模拟器上工作。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>将部署到设备失败，并 System.Exception: AMDeviceInstallApplication 返回 3892346901

此错误意味着证书/捆绑 id 的代码签名配置与安装在你的设备上预配配置文件不匹配。  确认你具有适当的证书在项目选项中选择-> iPhone 捆绑签名，并且项目选项中指定的正确程序包 id-> iPhone 应用程序

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>补全代码不使用 Visual Studio 中适用于 Mac

确保使用最新版本的 Visual Studio 针对 Mac 和 Xamarin.iOS

如果问题仍然存在，请[提交 bug](http://monodevelop.com/Developers#Reporting_Bugs)、 附加**~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和**组件-{TIMESTAMP}.log**日志文件。

如果所有其他方法均失败，则可以尝试删除的代码完成缓存，以便重新生成它：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

请注意，你正确键入此命令，或者也可以意外删除重要文件。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>复制文本时，适用于 Mac 的 visual Studio 崩溃

常用的 Mac 实用工具 QuickSilver、 Google 工具栏和启动栏具有 Mac 的内存损坏 Visual Studio 的剪贴板功能。 在其选项中，你可以列出适用于 Mac 的 Visual Studio，作为它们应不会影响的进程。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>适用于 Mac 的 visual Studio 错误报告所需的 Mono 2.4

如果适用于 Mac 的 Visual Studio 更新由于新的更新，并且当你尝试启动它时试它错误报告不在场的 Mono 2.4，你所要做的只是[升级 Mono 2.4 安装](http://www.go-mono.com/mono-downloads/download.html)。  

单声道 2.4.2.3_6 修复会阻止 Visual Studio for Mac 运行可靠地，有时挂起 Visual Studio for Mac 在启动或阻止从正在生成的代码完成数据库的某些重要问题。

一旦安装新的 Mono，按预期方式将启动 Visual Studio for Mac。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>在断言.../../../../mono/metadata/generic-sharing.c:704，oti 不满足条件

如果你收到以下的堆栈跟踪：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

这意味着你要链接到你的项目使用 thumb 代码编译静态库。 截至 iPhone SDK 版本 3.1 （或更高版本在撰写本文时） 链接与 Thumb 代码 （静态库） 的非 Thumb 代码 (Xamarin.iOS) 时，Apple 将引入其链接器中的 bug。你将需要与静态库可缓解此问题的非 Thumb 版本链接。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException： 尝试为 JIT 编译方法 （托管到托管的包装） Foo[]:System.Collections.Generic.ICollection'1.get_Count （）

[] 后缀指示你或类库正在访问泛型集合，如 ienumerable<>、 ICollection <> 或 IList <> 阵列上调用的方法。 解决方法是，你可以明确强制 AOT 编译器通过你自己，调用方法中包含此类方法，通过确保在触发异常的调用之前执行此代码。 在这种情况下，你可以编写：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

这将强制 AOT 编译器以囊括 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 源编辑器是执行速度极慢

有时 Visual Studio for Mac 源编辑器变得极慢，看起来之间键入字符的几秒钟的挂起。

此问题是非常罕见且极难再现-它通常无法重现同一台计算机上后重新启动 Visual Studio，for mac。 因此我们会十分感激它如果可以在适用于 Mac 的重新启动 Visual Studio 之前执行几个调试步骤并将结果发送给我们。

1.  尝试关闭编辑器选项卡上，并重新打开它。 需要少量编辑或四处移动插入符号，直到再次出现减速？
1.  禁用"无线发送同步"使用"Quartz Debug"开发人员工具 (你可以查找使用 Spotlight)，并检查是否为 normal 还原源编辑器性能。
1.  使用无线发送同步仍处于禁用状态，请尝试重复步骤 (1)。
1.  如果编辑器挂起超过几秒钟，尝试运行"killall-退出 [Visual Studio for Mac]"时它挂起的终端中。 它可能很难时间 kill 命令发生挂起编辑器，但很重要进行管理，因为该命令会强制 Mono 来到 MD 日志中，我们可以使用发现时挂起 X，线程处于什么状态写入的所有线程的堆栈跟踪。



请附加 XS 日志， **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和 * * 组件-{TIMESTAMP}.log * * (在较旧版本的 XS /MonoDevelop，只需发送**~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**)。

 **注意： 在 XS 2.2 最终已解决以上问题**

## <a name="compiled-application-is-very-large"></a>编译的应用程序是非常大

若要支持调试，调试版本，请包含附加代码。 在发布模式下生成项目是大小的一小部分。

从 Xamarin.iOS 1.3 调试开始生成包含调试 Mono （框架的每个类中每个方法） 的每个单个组件的支持。  

与 Xamarin.iOS 1.4 一起，我们将介绍用于调试的更好细化的方法，默认值将仅针对你的代码和库，提供调试的检测并不执行此操作的所有[Mono 程序集](~/cross-platform/internals/available-assemblies.md)（这仍将可能的但是必须选择性加入到调试这些程序集）。

## <a name="installation-hangs"></a>安装挂起。

如果你有 iPhone 模拟器正在运行的 Mono 和 Xamarin.iOS 安装挂起。 此问题并不局限于 Mono 或 Xamarin.iOS，这是在尝试将软件安装在 MacOS Snow Leopard，如果 iPhone 模拟器运行在安装时的任何软件的一致的问题。

请确保您退出 iPhone 模拟器，然后重试安装。

<a name="trampolines"/>

## <a name="ran-out-of-trampolines-of-type-0"></a>用尽了 trampolines 类型 0

如果你运行设备时，获取此消息，你可以创建多个类型 0 trampolines （键入特定） 通过修改您的项目选项"iPhone 生成"部分。  你想要添加设备的额外自变量生成目标：

 `-aot "ntrampolines=2048"`

默认数 trampolines 为 1024年。  请尝试增大此数字，直到有足够的应用程序。

## <a name="ran-out-of-trampolines-of-type-1"></a>用尽了 trampolines 类型 1

如果你进行大量使用递归泛型，则可能在设备上获得此消息。  通过修改您的项目选项"iPhone 生成"部分中，创建多个类型 1 trampolines （类型 RGCTX）。  你想要添加设备的额外自变量生成目标：

 `-aot "nrgctx-trampolines=2048"`

默认数 trampolines 为 1024年。  请尝试增大此数字，直到有足够的有关泛型的使用情况。

## <a name="ran-out-of-trampolines-of-type-2"></a>用尽了 trampolines 类型 2

如果你进行大量使用接口，您会在设备上收到此消息。
通过修改您的项目选项"iPhone 生成"部分中，创建多个类型 2 trampolines （类型 IMT Thunk）。  你想要添加设备的额外自变量生成目标：

 `-aot "nimt-trampolines=512"`

默认数 IMT 转换 （thunk) trampolines 为 128。  请尝试增大此数字，直到有足够的有关你的接口的使用情况。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>调试器不能使用设备进行连接

当开始调试设备配置时，你将看到调试器显示一个对话框，指示它尝试连接到应用程序。 有几个原因，调试器可能无法连接到应用程序，具体取决于模式，你要用于连接 （USB 或 WiFi）。

 **如果设备和调试器主机位于不同的网络上**，可能导致应用程序与连接到 WiFi 模式中的调试器主机的防火墙或专用网络。

 **适用于 Mac 的 visual Studio 可能不能够查询该主机的正确 IP**。 在 WiFi 模式适用于 Mac 的 Visual Studio 提供了应用程序所有 Ip，它可以找到的主机，而且应用程序尝试建立其所有查看是否它可以使用其中任何连接到 Visual Studio for mac。

 **另一台设备连接到主机上的 USB 端口。** 在少数情况下，其他设备连接到 USB 端口在主机上的具有已知以某种方式影响在 USB 模式中进行调试。

如果 WiFi 或 USB 模式不起作用，你可以轻松地尝试另： 在适用于 Mac 的 Visual Studio，打开首选项，转到首选项 / 调试器 / iPhone 调试器页中，并切换"调试 iOS 设备 wifi 而不是通过 USB"复选框。   如果都不起作用，你可以查看有关设备控制台以详细模式中的失败详细信息 (这通过添加"-v-v-v"到项目的选项中的其他 mtouch 参数)。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>错误 134: mtouch 失败，出现以下消息：

如果你尝试在 Xamarin.iOS 1.4 样式的版本上使用-nolink 生成，可能会引发此错误。 可以通过在 monodevelop 将项目配置中指定多余的自变量，来解决此错误。

添加自变量

 `-nosymbolstrip`

和问题应该会得到解决。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>分发标识不会显示在 Visual Studio 中为 Mac 项目签名选项

Visual Studio for Mac 2.2 具有导致其无法检测包含逗号的分发证书的 bug。 请更新到 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>错误"返回的 AFCFileRefWrite: 1"在上载

将应用上载到你的设备时可能会收到错误"返回的 AFCFileRefWrite: 1"。 如果您有一个零长度文件，也可能发生。

## <a name="error-mtouch-failed-with-no-output"></a>错误"mtouch 失败且无输出"

最新版本的 Xamarin.iOS 和 Visual Studio for Mac 失败时的项目名称或存储解决方案或项目的目录包含空格。
若要解决此问题：


-  请确保你的项目或存储位置的目录都不包含空格。
-  在项目"Main 设置"请确保项目名称不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>错误"你上载的二进制句柄无效。 预发行版 beta 版本的 SDK 用于生成应用程序"

此错误通常由于与之前发布 Xamarin.iOS 2.0.0 iPad 开发中启动项目，则可能已在如你 Info.plist 中的一些密钥：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

应删除此密钥对，因为 Visual Studio for Mac 它自动为你处理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>错误"预发行版 beta 版本的 SDK 用于生成应用程序"

（由 Ed Anuff 提供）

请执行这些步骤：

-  连接到 3.2 或 iTunes iPhone 生成中的 SDK 版本的更改将它上载出于以下原因拒绝它所看到使用小于 3.2 的 SDK 版本创建的 iPad 兼容应用
-  创建自定义 Info.plist 项目并将 MinimumOSVersion 显式设置为在其中 3.0。   这将覆盖所设置的 Xamarin.iOS 的 MinimumOSVersion 3.2 值。   如果不这样做，应用程序不能在 iPhone 上运行。
-  重新生成、 zip 和上载到 iTunes 连接。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未经处理的异常： System.Exception： 未能找到选择器 someSelector： 上 {type}

此异常是由以下三个操作引起的：


1.  你提供了无将相应的 [导出] 特性应用于一种方法为 Objective C 运行时选择器
1.  你已启用完全链接，并且不 [保留] 特性应用到 [导出] ed 方法。
1.  你已 [导出] 特性应用到继承的类型中的私有方法。


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>不会更新 MainWindow.xib.designer.cs 文件

在 Xamarin Studio 2.4 导致其无法组将 MainWindow.xib 文件与新项目中的 MainWindow.xib.designer 文件时 bug。 这意味着它不会更新该特定的文件的设计器代码。

此问题固定的版本的 Visual Studio for Mac 位于其内置的更新程序，因此请确保你使用较新版本。

你可以通过删除 （如果不删除） 来解决现有项目 xib 和其设计器的文件，然后将它添加回。 此重新应正确组文件。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>创建后 UIAlertView 或 UIActionSheet 消失

如果你有如下一些代码：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

"actionSheet"对象存在为该函数中的临时变量，函数将终止，该对象就可以进行垃圾回收，所以最终被垃圾回收。

若要解决此问题，你需要保持对"actionSheet"你位于方法之外，某处将实时超出你的方法的引用。

## <a name="project-always-runs-in-the-ipad-simulator"></a>项目始终运行在 iPad 模拟器

IPhone SDK 4.0 安装程序安装 2 Sdk-3.2 SDK，用于生成仅 iPad 应用和 4.0 的 SDK，用于生成 iPhone 和通用应用。 它还会安装 3.2 模拟器，这将模拟仅 iPad 和模拟 iPhone 或 iPhone 4 4.0 模拟器。 删除所有旧的 Sdk 和模拟器。

Visual Studio for Mac iPhone 项目生成选项包括在生成应用程序将使用的 SDK 版本的设置。 此设置可在**项目选项-> 生成-> iPhone 生成**。

适用于 Mac 的 Visual Studio 中的新项目使用最旧的已安装的 SDK 作为默认的 SDK 设置，并且如果不存在指定的 SDK，适用于 Mac 的 Visual Studio 将使用它可以找到生成应用程序的最近。 执行此操作，以便项目将不始终 requre 的最新 SDK。 但是，这当前会导致 3.2 SDK 正在使用-这将导致在 iPad 模拟器正在使用。

若要解决此问题通过 4.0 SDK，请转到**项目选项-> 生成-> iPhone 生成**> 并将 SDK 值更改为"4.0"，然后使用下拉框。 必须为每个配置和平台的组合，访问使用的面板顶部的下拉列表来执行此操作。

不应与"的最低操作系统版本"设置混淆的 SDK 版本。
此值不必与匹配的 SDK 版本值-它会影响你的应用程序将安装上，可以是早于 SDK，只要你使用在较旧的 OS 中，存在或防止较新的功能使用运行时 OS 版本检查使用的 Api 的操作系统的最低版本k。 应将其设置为在其测试你的应用程序的最早的操作系统版本。

另请注意，**项目-> iPhone 模拟器目标**> 菜单可用于选择运行/调试项目时，默认情况下使用模拟器。 此外，**运行-> 运行与**> 菜单可用于选择用来运行特定模拟器

## <a name="ibtool-returns-error-133"></a>ibtool 返回错误 133

这意味着你已安装 XCode 4。   在 XCode 4 中，工具 ibtool 已删除，不再可以编辑 XIB 文件使用独立工具。

如果你想要使用接口生成器，安装[XCode 系列 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792)，可从 Apple 的网站。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"无法创建 mime 类型的显示绑定： application/vnd.apple-<wbr/>接口生成器"

如果你尝试从非 iPhone 项目创建 iPhone UI，会发生此错误。 请确保启动与 iPhone/iPad 解决方案时，不能将 iPhone UI 元素添加到 iPhone/iPad 项目。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>启动故障时的内部的 iOS 模拟器执行

如果你能类似如下所示的堆栈跟踪以及模拟器深入了解有关运行时崩溃 (SIGSEGV):

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
...then 模拟器应用程序目录中可能有一个 （或多个） 过时的程序集。 由于 Apple iOS 模拟器中添加并更新文件，但永远不会删除它们，则可能存在这样的程序集。 如果发生这种情况然后最简单的解决方案是从模拟器菜单中选择"重置和内容和设置..."。   

**警告：**这将从模拟器删除所有文件、 应用程序和数据。   下一次执行应用程序，适用于 Mac 的 Visual Studio 会将其部署到模拟器，将有任何旧的、 陈旧的程序集，使在发生崩溃。

## <a name="simulator-hangs-during-application-installation"></a>在应用程序安装过程中的模拟器挂起。

这可能会在应用程序名称包含。（点） 在它们的名称。
即使它可以在 （如设备） 的许多其他情况下的工作原理，这是禁止作为 CFBundleExecutable-中的可执行文件名称。

 *"值不应包含任何扩展的名称。"- [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>错误:"自定义属性类型 0x43 不支持"时 double 单击.xib 文件

这被引起尝试打开.xib 文件时未正确设置环境变量。 这应不会发生与 Visual Studio 的一般用法为 Mac/Xamarin.iOS，且从/应用程序 — Mac 的重新打开 Visual Studio 来解决该问题。

尝试更新软件和此错误消息出现后，请电子邮件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>应用程序在模拟器上运行，但在设备上失败

此问题可以多种形式中的清单，并不始终生成一致错误。 如果应用程序包含.xib，检查以确保**生成操作**.xib 设置为**接口定义**。 这是.xibs 的默认生成操作。

若要检查的生成操作，请右键单击.xib 文件并选择**生成操作**。


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>没有数据是可用于编码 437 System.NotSupportedException:

时在你的 Xamarin.iOS 应用程序中包含第三方库，你可能会在窗体中的错误"System.NotSupportedException： 不会提供编码 437 数据"时尝试编译并运行应用。 例如，库，如`Ionic.Zip.ZipFile`，可能会引发此异常在操作过程。

这可以通过打开 Xamarin.iOS 项目中，转到的选项来解决**iOS 生成** > **国际化**和检查**西部**国际化。



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>无法从试用帐户升级到 Indie/企业

如果你最近购买 Xamarin.iOS，以前启动 Xamarin.iOS 试用版，你可能需要完成以下步骤以获取用于 Mac 或 Visual Studio，由 Visual Studio 选取此许可证更改。

-  关闭 Visual Studio for Mac/Visual Studio
-  适用于 Windows 从 ~/Library/MonoTouch Mac 上的或 %PROGRAMDATA%\MonoTouch\License\ 中删除所有文件
-  For Mac/Visual Studio 中重新打开 Visual Studio 并生成 Xamarin.iOS 项目


## <a name="receiving-activation-incomplete-error-message"></a>收到激活不完整错误消息

使用用于 Visual Studio 的 Xamarin.iOS 时，可能出现此问题。 若要解决此问题，请从发送日志到以下位置[ contact@xamarin.com ](mailto:contact@xamarin.com)。

-  日志位置： %LocalAppData%/Xamarin/Logs
