---
title: "疑难解答指南"
ms.topic: article
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 145c8507ca5ebea6197fa8827b93f58fbc9bb078
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshooting-tips"></a>疑难解答指南


## <a name="getting-diagnostic-information"></a>获取诊断信息

Xamarin.Android 具有几位数来跟踪各种 bug 时的外观。
这些方法包括：

1.  诊断 MSBuild 输出。
2.  设备部署日志。
3.  Android 调试日志输出。


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>诊断 MSBuild 输出

诊断 MSBuild 可以包含与包生成相关的其他信息，并可能包含某些包部署信息。

若要在 Visual Studio 中启用诊断 MSBuild 输出：

1.  单击**工具 > 选项...**
2.  在左侧树视图中，选择**项目和解决方案 > 生成并运行**
3.  在右侧面板中，将 MSBuild 生成输出详细级别下拉列表设置为诊断
4.  单击“确定” 
5.  清除并重新生成包。
6.  在输出面板内可见诊断输出。


若要为 Mac/OS x： 启用 Visual Studio 中的诊断 MSBuild 输出

1.  单击**Visual Studio for Mac > 首选项...**
2.  在左侧树视图中，选择**项目 > 生成**
3.  在右侧面板中，设置日志记录详细程度下拉列表为诊断
4.  单击“确定” 
5.  重启 Visual Studio for Mac
6.  清除并重新生成包。
7.  诊断输出中是可见的错误填充 (**视图 > 填充 > 错误**)，通过单击生成输出按钮。




## <a name="device-deployment-logs"></a>设备部署日志

若要启用 Visual Studio 中的设备部署日志记录：

1.  **工具 > 选项...**>
2.  在左侧树视图中，选择**Xamarin > Android 设置**
3.  在右侧面板中，启用 [X]**扩展调试日志记录 （将 monodroid.log 写入你的桌面）**复选框。
4.  日志消息写入在你的桌面上 monodroid.log 文件。


适用于 Mac 的 visual Studio 始终会写入设备部署日志。 查找它们的略有难度;*AndroidUtils*为每个天 + 进行部署时，例如创建日志文件： **AndroidTools-2012-10-24_12-35-45.log**。

-  在 Windows 上，日志文件写入到`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
-  在 OS X 上，日志文件写入到`$HOME/Library/Logs/XamarinStudio-{VERSION}`。




## <a name="android-debug-log-output"></a>Android 调试日志输出

Android 将许多将消息写入到[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin.Android 使用 Android 系统属性来控制生成的 Android 调试日志的其他消息。 可通过设置 android 系统属性*setprop*命令内[Android 调试桥接 (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系统属性进程在启动期间，读取和之前启动应用程序或应用程序必须重新启动后更改的系统属性，因此就必须进行设置。



### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系统属性

Xamarin.Android 支持以下系统属性：

-   *debug.mono.debug*： 如果将非空字符串，这相当于`*mono-debug*`。

-   *debug.mono.env*： 管道分隔 (*|*) 的环境变量来导出应用程序在启动期间，列表*之前*mono 已初始化。 这样，设置该控件单声道日志记录的环境变量。

    - *请注意*： 因为值是*|*-分隔，该值必须包含额外级别的用引号括起来，作为\` *adb shell* \`命令将删除引号的集。

    - *请注意*: Android 系统属性值不能超过 92 个字符的长度。

    - 示例:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*： 以逗号分隔 (*，*) 应打印到 Android 调试日志的其他消息的组件列表。 默认情况下，设置执行任何操作。 组件包括：

    -   *所有*： 打印的所有消息
    -   *gc*： 打印 GC 相关的消息。
    -   *gref*： 打印弱 （全局） 参考分配和解除分配消息。
    -   *lref*： 打印本地引用分配和解除分配消息。

    *请注意*： 这些是*极*详细。 不要启用，除非你确实需要。

-   *debug.mono.trace*： 允许设置[mono-跟踪](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`设置。



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android 无法解析 System.ValueTuple

此错误是由于使用 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更低版本） 是仅与兼容**System.ValueTuple NuGet 4.3.0** （或更低版本）。

- **Visual Studio 2017 Update 2** （15.2 或更高版本） 才与兼容**System.ValueTuple NuGet 4.3.1** （或更高版本）。

请选择与你的 Visual Studio 2017 安装相对应的正确 System.ValueTuple NuGet。


## <a name="gc-messages"></a>GC 消息

可以通过将 debug.mono.log 系统属性设置为一个值，包含 gc 查看 GC 组件消息。

无论何时 GC 执行，并提供有关多少工作 GC 未的信息生成 GC 消息：

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

可以通过设置生成额外的 GC 信息，如计时信息`MONO_LOG_LEVEL`环境变量`debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

这将导致 （大量） 其他的 Mono 消息，包括这些三个后果：

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在`GC_BRIDGE`消息，`num-objects`是正在考虑的此阶段中，桥对象的数目和`num_hash_entries`是桥代码调用期间处理的对象数。

在`GC_MINOR`和`GC_MAJOR`消息，`total`时暂停世界的时间量 （执行任何线程），而`bridge`是在桥处理代码 （这涉及到 Java VM） 中花费的时间量。 这一领域*不*暂停桥处理执行一次。

 *一般情况下*的值越大`num_hash_entries`，则所用的时间，`bridge`需要集合，且更大`total`将收集所用的时间。



## <a name="global-reference-messages"></a>全局参考消息

若要启用日志记录，全局引用 loggig (GREF) *debug.mono.log*系统属性必须包含*gref*，例如：

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android 使用 Android 的全局引用时调用的 Java 实例需要提供与 Java Java 方法为提供 Java 实例和关联的托管的实例之间的映射。

遗憾的是，Android 仿真程序只允许 2000年全局引用存在一次。 硬件具有多更高的限制的 52000 全局引用。 在仿真程序中，因此，知道上运行应用程序时，较低的限制可能会产生问题*其中*实例来源可能非常有用。

 *请注意*： 全局引用计数是内部 Xamarin.Android，并且不会 （并且不能） 包括全局不再考虑其他加载到进程的本机库的引用。 使用全局引用计数为估计值。

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

有四条消息的后果：

-  全局引用创建： 这些是开头的行*+ g +* ，并将提供创建的代码路径的堆栈跟踪。
-  全局引用析构： 这些是开头的行*-g-* ，并为释放的全局引用的代码路径可能提供堆栈跟踪。 如果 GC 正在释放 gref，将不提供任何堆栈跟踪。
-  全局弱引用创建： 这些是开头的行*+ w +* 。
-  全局弱引用析构： 这些是开头的行*-w-* 。


中的全部消息*grefc*值是 Xamarin.Android 已创建，全局引用的计数时*grefwc*值是弱 Xamarin.Android 已创建的全局引用的计数。 *处理*或*obj 句柄*值为 JNI 句柄值和后面的字符 */*是句柄值的类型： */L*本地仅供参考， */G*全局引用和*/W*弱全局引用。

GC 过程的一部分，作为全局引用 （+ g +） 转换为弱全局引用 (导致 a + w + 和-g-)、 Java 端垃圾收集器将启动，然后弱的全局引用将检查以确定是否它已收集。 周围的弱引用它是否仍处于活动状态，创建了新 gref (+ g +、-w-)，否则销毁弱 ref (-w)。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>创建并由 MCW 包装 Java 实例

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>正在执行垃圾收集器...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>对象是否仍处于活动状态，作为处理 ！ = null
## <a name="wref-turned-back-into-a-gref"></a>wref 转换回 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>对象已终止，作为处理 = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 是已释放，任何新创建的 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

没有一个"有趣"的皱褶： 运行 Android 4.0 之前的目标，gref 值是否等于 Android 运行时的内存中的 Java 对象的地址。 （即，GC 是一个非活动、 保守、 收集器，并且它正在处理对这些对象的直接引用。）因此后 + g + + w +、-g-，+ g +、-w 序列，生成 gref 将具有相同的值作为原始 gref 值。 这使得 grepping 通过日志非常简单。

Android 4.0，但是，已移动的收集器，并不再为出对 Android 运行时的直接引用 VM 对象。 因此之后, + g + + w +、-g-，+ g +、-w 序列、 gref 值*将不同*。 如果对象存在多个 Gc，它会由几个 gref 值，这使得更加困难，以确定其中一个实例已从实际分配。

### <a name="querying-programmatically"></a>以编程方式查询

你可以通过查询查询 GREF 和 WREF 计数`JniRuntime`对象。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` 全局引用计数

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -弱引用计数



## <a name="offline-activation"></a>脱机激活

如果你不能激活 Xamarin.Android 在 Windows 上，或无法在 Mac OS X 上安装 Xamarin.Android 的完整版本，请参阅[脱机激活](~/android/get-started/installation/index.md)页。



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>无法从试用帐户升级到 Indie/企业

如果你最近购买 Xamarin.Android，以前启动 Xamarin.Android 试用版，你可能需要完成以下步骤以获取用于 Mac 或 Visual Studio，由 Visual Studio 选取此许可证更改。

-  关闭 Visual Studio for Mac/Visual Studio
-  删除所有文件从 ~/Library/MonoAndroid Mac 上的或 %PROGRAMDATA%\Mono Android\License\ for Windows
-  For Mac/Visual Studio 中重新打开 Visual Studio 并生成 Xamarin.Android 项目


这应让你启动并运行。 如果继续遇到问题，你可能希望尝试[脱机激活](~/android/get-started/installation/index.md)以完成激活你的工作站。



## <a name="receiving-activation-incomplete-error-message"></a>接收激活不完整的错误消息

当使用 Xamarin.Android for Visual Studio 时，可能出现此问题。 若要解决此问题，请从发送日志到以下位置 *contact@xamarin.com* 。

-  日志位置： **%localappdata%\\Xamarin\\日志**




## <a name="receiving-error-retrieving-update-information-error-message"></a>收到错误检索更新信息错误消息

有时，更新会因以下错误，则通常会出现的检查更新时：

大多数时间，只需通过日志记录无法使用 Xamarin 帐户，可以解决此错误，然后日志记录然后重新登录。

若要实现此目的，请了解下面选择你平台，并按照步骤：

在 Mac 上：
1. 打开 Visual Studio for Mac
2. 选择适用于 Mac 的 Visual Studio > 帐户...
3. 单击注销
4. 单击登录
5. 输入你的凭据
6. 检查更新

**在 PC 上使用 Visual Studo:**
1. 打开 Visual Studio
2. 选择工具 > Xamarin 帐户
3. 单击注销
4. 单击登录
5. 输入你的凭据
6. 检查更新

如果此错误消息继续出现，请发送电子邮件 **contact@xamarin.com** 。




## <a name="android-debug-logs"></a>Android 调试日志

[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)可能提供有关您会看到任何运行时错误的其他上下文。



## <a name="floating-point-performance-is-terrible"></a>浮点性能为可怕 ！

或者，"我的应用程序运行速度更快 10 倍使用发布版本使用调试生成比 ！"

Xamarin.Android 支持多个设备 ABIs: *armeabi*， *armeabi v7a*，和*x86*。 在中，可以指定设备 ABIs**项目属性 > 应用程序选项卡 > 支持的体系结构**。

调试版本中使用 Android 包也不能提供所有 ABIs，因此将使用最快的 ABI 为目标设备。

发布版本将仅包括在项目属性选项卡中选择 ABIs。可以选择多个。

*armeabi*默认 ABI，并且具有最广泛的设备支持。 *但是*，armeabi 不支持多 CPU 设备和硬件浮点、 amont 其他操作。 因此，使用 armeabi 版本运行时的应用将绑定到单核，并将使用软浮点型实现。 这两个可以参与应用程序的性能明显变慢。

如果你的应用需要不错的浮点性能 （例如游戏），则应启用*armeabi v7a* ABI。 你可能想要仅支持*armeabi v7a*运行时，尽管这意味着，仅支持较旧设备*armeabi*将无法运行你的应用。



## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

Google Android SDK for windows 提供了 2 下载。
如果你选择.exe 安装程序，它将编写告诉 Xamarin.Android 安装位置的注册表项。 如果你选择的.zip 文件，并将其解压缩自己，Xamarin.Android 不知道在何处查找 SDK。 你可以判断 Xamarin.Android 其中 SDK 是 Visual Studio 中通过转到**工具 > 选项 > Xamarin > Android 设置**:

[![在 Xamarin Android 设置中的 android SDK 位置](troubleshooting-images/01a.png)](troubleshooting-images/01a.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE 不会显示目标设备

有时，你将尝试应用程序部署到设备，但你想要部署不在选择设备对话框中显示的设备。 这可能发生 Android 调试桥去度假，决定。

若要诊断此问题，查找[adb 程序](~/android/deploy-test/debugging/android-debug-log.md)，然后运行：

```shell
adb devices
```

如果你的设备不存在，则需要重新启动 Android 调试桥服务器，以便可以找到你的设备：

```shell
adb kill-server
adb start-server
```

HTC 同步软件可能会阻止**adb 开始服务器**不能正常工作。 如果**adb 开始服务器**命令不会打印出哪个端口启动，请退出 HTC 同步软件，请尝试重新启动 adb 服务器。


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>无法运行指定的任务可执行文件"keytool"

这意味着你的路径不包含 Java SDK 中的 bin 目录所在的目录。 检查您遵循这些步骤，可从[安装](~/android/get-started/installation/index.md)指南。


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe 或 aresgen.exe 退出，代码为 1

若要帮助你调试此问题，请转到 Visual Studio 和更改 MSBuild 详细级别，为此，请选择：**工具 > 选项 > 项目**和**解决方案 > 生成**和**运行 >MSBuild 项目生成输出详细信息**并将此值设置为**正常**。

重新生成，并检查 Visual Studio 的输出窗格中，其中应包含完整的错误。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>要部署包的设备上没有足够的存储空间

在不启动从 Visual Studio 中的仿真程序时，将发生这种情况。 在开始在 Visual Studio 之外的仿真程序时，你需要传递`-partition-size 512`选项，例如

```shell
emulator -partition-size 512 -avd MonoDroid
```

确保使用正确的模拟器名称中，即[时配置模拟器使用的名称](~/android/get-started/installation/windows.md#device)。


## <a name="installfailedinvalidapk-when-installing-a-package"></a>安装\_失败\_无效\_APK 安装程序包时

Android 包名称*必须*包含句点 (*。*)。 编辑你的包名称，使其包含句点。

-   在 Visual Studio:
    -   右键单击你的项目 > 属性
    -   单击左侧的 Android 清单选项卡。
    -   更新包名称字段。
        -   如果你看到消息&ldquo;否 AndroidManifest.xml 找到。 单击此项可添加一个。&rdquo;、 单击的链接，然后更新包名称字段。
-   在 Visual Studio for Mac:
    -   右键单击你的项目 > 选项。
    -   导航到生成 / Android 应用程序部分。
    -   更改包名称字段包含 '。




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>安装\_失败\_缺少\_共享\_库安装程序包时

在此上下文中的"共享的库"，则*不*本机共享的库 (*libfoo.so*) 文件; 它而是必须单独安装在目标设备上，如 Google 地图库。

Android 包指定的共享的库所必需`<uses-library/>`元素。 如果*必需*库不存在的目标设备上 (例如`//uses-library/@android:required`是*true*，这是默认设置)，则包安装将失败并显示*安装\_失败\_缺少\_共享\_库*。

若要确定所需的共享的库，查看*生成*
**AndroidManifest.xml**文件 (例如**obj\\调试\\android\\AndroidManifest.xml**)，并查找`<uses-library/>`元素。 `<uses-library/>` 元素可在你的项目中手动添加**属性\\AndroidManifest.xml**文件和通过[UsesLibraryAttribute 自定义特性](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/)。

例如，添加的程序集引用*Mono.Android.GoogleMaps.dll*将隐式添加`<uses-library/>`Google 地图共享库。



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>安装\_失败\_更新\_不兼容时安装的包

Android 包具有三个要求：

-   必须包含。（请参阅以前的条目）
-   它们必须具有唯一的字符串包名称 (因此在 Android 应用程序名称，例如 com.android.chrome Chrome 应用中看到的反向 tld 约定)
-   在升级包，此包必须具有相同的签名密钥。

因此，设想以下场景：

1.  生成和部署你的应用作为调试应用
2.  你将更改签名的密钥，例如为用作发布应用程序 （或由于你不喜欢默认提供调试签名密钥）
3.  安装你的应用程序，而不必首先将其删除，例如调试 > 启动不调试的 Visual Studio 中


在此情况下，包安装将失败，并安装\_失败\_更新\_不兼容的错误，因为包名称未发生变化时的签名密钥未。 [Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)还包含类似的消息：

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要解决此错误，完全删除应用程序从你的设备在重新安装之前。


## <a name="installfaileduidchanged-when-installing-a-package"></a>安装\_失败\_UID\_时安装的包更改

安装 Android 包时，会分配*用户 id* (UID)。
*有时*，对于当前未知原因，已安装的应用程序通过安装时，安装将失败并`INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解决此问题，*完全卸载*Android 包，通过从 Android 目标 GUI 安装应用或使用`adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**不要使用** `adb uninstall -k`，因为这将*保留*应用程序数据，并因此保留在目标设备上的冲突 UID。



## <a name="release-apps-fail-to-launch-on-device"></a>发布应用程序无法在设备上启动

Android 调试日志输出将包含类似的消息：

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是这样，有两个可能的原因，此：

1.  .Apk 不提供目标设备支持 ABI。
    例如，.apk 仅包含 armeabi v7a 二进制文件和目标设备仅支持 armeabi。

2.  [Android bug](http://code.google.com/p/android/issues/detail?id=21670)。 如果出现这种情况，先卸载该应用、 跨手指，和重新安装该应用。

若要解决问题 (1)，请编辑项目选项/属性和[将对所需的 ABI 的支持添加到的支持 ABIs 列表](~/android/app-fundamentals/cpu-architectures.md)。 若要确定你需要添加哪些 ABI，请针对你的目标设备运行以下 adb 命令：

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

该输出将包含主 （或可选次要） ABIs。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>OutPath 属性不为项目设置&ldquo;MyApp.csproj&rdquo;

这通常意味着你具有 HP 计算机和环境变量&ldquo;平台&rdquo;如 MCD 或 HPD 设置为内容。 这与通常设置为 MSBuild 平台属性冲突&ldquo;任意 CPU&rdquo;或&ldquo;x86&rdquo;。 你将需要从计算机中删除此环境变量，MSBuild 可以正常工作：

-   控制面板 > 系统 > 高级 > 环境变量

适用于 Mac 中重新启动 Visual Studio 或 Visual Studio，然后尝试重新生成。 按预期方式，现在应工作内容。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject 不能强制转换为...

Xamarin.Android 4.x 不正确封送嵌套的泛型类型正确。 例如，考虑以下 C\#代码使用[SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


问题在于 Xamarin.Android 不正确地将嵌套的泛型类型封送。 `List<IDictionary<string, object>>`正封送到[java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/)，但`ArrayList`包含`mono.android.runtime.JavaObject`实例 (哪一个引用`Dictionary<string, object>`实例) 而不是指实现[java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/)，从而得到以下异常：

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

解决方法是使用提供[Java 集合类型](~/android/internals/api-design.md)而不是`System.Collections.Generic`类型&ldquo;内部&rdquo;类型。 封送处理实例时，这将导致相应的 Java 类型。 （下面的代码是必需以减少 gref 生存期更复杂。 它可以简化为更改通过的原始代码`s/List/JavaList/g`和`s/Dictionary/JavaDictionary/g`如果 gref 生存期不担心。)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[这将在未来版本中修复](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)。


## <a name="unexpected-nullreferenceexceptions"></a>意外的 NullReferenceExceptions

有时[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)将提到 NullReferenceExceptions 的&ldquo;不会发生，&rdquo;或来自 Mono 对于很快在应用程序发生故障之前的 Android 运行时代码：

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

或

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

这可能会在 Android 运行时决定中止该进程，可能会导致任意数量的原因，包括达到目标的 GREF 限制或执行某项操作&ldquo;错误&rdquo;与 JNI。

若要查看是否是这种情况，请检查你的过程类似于发来的邮件 Android 调试日志：

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>由于全局引用用尽而导致中止

Android 运行时的 JNI 层仅支持有限的数量的 JNI 对象引用，才能及时有效在任意给定时间。 当超过此限制时，将中断操作。

GREF (*全局引用*) 限制为 2000年引用的仿真程序中，~ 52000 引用在硬件上。

你知道你开始创建过多 GREFs，当你看到此 Android 调试日志中的消息：

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

当达到 GREF 限制时，将打印出一条消息，如下所示：

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


在上面的示例 (其中，顺便说一下，来自[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) 问题是，在创建实例的太多 Android.Graphics.Point; 请参阅[注释\#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2)有关的修补程序列表此特定的 bug。

发现哪种类型具有的实例过多的有用的解决方案是通常情况下，分配&ndash;在上面的转储中 Android.Graphics.Point&ndash;然后找到其中它们在创建源代码和处置它们中适当地 (以便其缩短 Java-object 生存期)。 这并不总是适当 (\#685215 是多线程，因此普通解决方案可以避免 Dispose 调用)，但它是要考虑的第一步。

你可以启用[GREF 日志记录](~/android/troubleshooting/index.md)若要查看何时创建 GREFs，以及多少存在。


## <a name="abort-due-to-jni-type-mismatch"></a>由于 JNI 类型不匹配而中止

如果手动汇总 JNI 代码，则有可能，类型不会匹配正确无误，例如如果你尝试调用`java.lang.Runnable.run`未实现的类型上`java.lang.Runnable`。 当发生这种情况时，将有一条消息类似于以下 Android 调试日志中：

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>动态代码支持

### <a name="dynamic-code-does-not-compile"></a>无法编译动态的代码

若要将 C\#动态应用程序或库中，你需要将 System.Core.dll、 Microsoft.CSharp.dll 和 Mono.CSharp.dll 添加到你的项目。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在发布版本 MissingMethodException 进行动态代码在运行时。

-   很可能你的应用程序项目不具有对 System.Core.dll、 Microsoft.CSharp.dll 或 Mono.CSharp.dll 的引用。 请确保引用这些程序集。

    -   请记住该动态代码始终成本。 如果你需要高效的代码，请考虑不使用动态代码。

-   在第一个预览版中，这些程序集已排除除非显式由应用程序代码使用每个程序集中的类型。 请参阅以下项作为一种解决方法： [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>在 x86 上生成 AOT + LLVM 崩溃项目设备

部署使用构建的应用时[AOT + LLVM](~/android/deploy-test/release-prep/index.md)在基于 x86 的设备，你可能会看到与下面类似的异常错误消息：

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

这是一个已知的问题中报告[56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111)。 解决方法是禁用 LLVM。
