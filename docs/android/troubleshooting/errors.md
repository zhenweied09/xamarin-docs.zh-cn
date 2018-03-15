---
title: "Xamarin.Android 错误矩阵"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: d113e7747f656eda2bedb88574f4b2027616add8
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android 错误矩阵

## <a name="errors-reference"></a>错误参考

通过 Xamarin 还原时，本文档提供的各种错误代码的一些信息。

|类别|描述|
|--- |--- |
|XA0xxx|mandroid 错误|
|XA1xxx|文件复制 / 符号链接 （相关项目） 错误|
|XA2xxx|链接器错误|
|XA3xxx|AOT 错误|
|XA4xxx|代码生成错误|
|XA5xxx|GCC 和工具链错误|
|XA6xxx|mandroid 内部工具错误|
|XA7xxx|保留|
|XA8xxx|保留|
|XA9xxx|许可错误|


## <a name="error-codes"></a>错误代码

### <a name="xa0xxx-errors"></a>XA0xxx 错误

|错误代码|描述|
|--- |--- |
|XA0000|意外的错误-请填写[bug 报表](http://bugzilla.xamarin.com)。|
|XA0001|-devname 提供不需要任何特定于设备的操作。|
|XA0002|无法分析的环境变量"{0}"。|
|XA0003|使用 SDK 或产品程序集 (.dll) 名称的应用程序名称 {0}.exe 冲突。|
|XA0004|新 refcounting 逻辑需要 sgen 太启用。|
|XA0005|输出目录"{0}"不存在。|
|XA0006|在"{0}"没有任何 devel 的平台，请使用-平台 = PLAT 指定 SDK|
|XA0007|根程序集"{0}"不存在。|
|XA0008|应提供一个根只有程序集。|
|XA0009|加载程序集时出错: {0}。|
|XA0010|无法分析命令行参数: {0}。|
|XA0011|{0} 已针对较新的运行时 ({1}) 构建的比 MonoTouch 支持。|
|XA0012|不完整的数据用于完成"{0}"。|
|XA0013|分析支持需要 sgen 太启用。|
|XA0014|iOS {0} 不支持面向 ARMv6 构建应用程序。|
|XA0020|无法确定 mandroid 路径。|
|XA0100|EmbeddedNativeLibrary"{0}"在 Android 应用程序项目中无效。 请改为使用 AndroidNativeLibrary。|

### <a name="xa1xxx-errors"></a>XA1xxx 错误

|错误代码|描述|
|--- |--- |
|XA1001|在指定的目录中找不到应用程序。|
|XA1002|无法创建符号链接，复制文件。|
|XA1003|无法终止应用程序"{0}"。 你可能必须手动终止应用程序。|
|XA1004|无法获取安装的应用程序的列表。|
|XA1005|无法取消设备 {1} 上的应用程序"{0}": {2}。 你可能必须手动终止应用程序。|
|XA1006|无法在设备 {1} 上安装应用程序"{0}": {2}。|
|XA1007|无法启动设备 {1} 上的应用程序"{0}": {2}。 你仍可以通过在其上的点击手动启动该应用程序。|
|XA1008|无法启动模拟器: {0}。|
|XA1009|无法将程序集"{0}"复制到 {1}: {2}。|
|XA1010|无法加载的程序集"{0}": {1}。|
|XA1011|无法添加缺少的资源文件:"{0}"。|
|XA1101|无法启动应用程序。|
|XA1102|无法附加到应用程序 （以终止它）: {0}。|
|XA1103|无法分离。|
|XA1104|发送数据包失败: {0}。|
|XA1105|意外的响应类型。|
|XA1106|无法在设备上获取的应用程序的列表： 请求的操作已超时。|
|XA1107|应用程序启动失败。|
|XA1201|无法加载模拟器: {0}。|
|XA1301|本机库"{0}"({1}) 已被忽略，因为它与当前生成 architecture(s) ({2}) 不匹配。|

### <a name="xa2xxx-errors"></a>XA2xxx 错误

|错误代码|描述|
|--- |--- |
|XA2001|无法链接程序集。|
|XA2002|无法解析引用: {0}。|
|XA2003|选项"{0}"将被忽略，因为链接被禁用。|
|XA2004|找不到额外链接器定义文件"{0}"。|
|XA2005|无法分析从"{0}"的定义。|
|XA2006|无法解析对元数据项"{0}"（在 {1} 中定义） 从 {2} 的引用。|

### <a name="xa3xxx-errors"></a>XA3xxx 错误

这些是 AOT 错误。

|错误代码|描述|
|--- |--- |
|XA3001|无法不 AOT 的程序集"{0}"。|
|XA3002|AOT 限制： 方法"{0}"必须是静态，因为它用 [MonoPInvokeCallback] 修饰。|
|XA3003|冲突-调试和-llvm 选项。 软调试被禁用。|


### <a name="xa4xxx-errors"></a>XA4xxx 错误

这些是代码生成错误。

|错误代码|描述|
|--- |--- |
|XA4001|主模板中找不到"{0}"expansed。|
|XA4101|注册机构无法生成类型"{0}"的签名。|
|XA4102|注册机构 {2} 方法签名中找到无效的类型"{0}"。 请改用 {1}。|
|XA4103|注册机构找到无效的类型"{0}"中方法的签名 {2}: 类型实现 INativeObject，但没有采用两个构造函数 (IntPtr，bool) 自变量。|
|XA4104|注册机构不能封送方法 {1} 的签名中的类型"{0}"的返回值。|
|XA4105|注册机构不能封送类型"{0}"中方法 {1} 的签名的参数。|
|XA4106|注册机构不能封送结构"{0}"方法 {1} 的签名中的返回值。|
|XA4107|注册机构不能封送类型"{0}"中方法 {1} 的签名的参数。|
|XA4108|注册机构无法获取托管类型"{0}"的 ObjectiveC 类型。|
|XA4109|编译生成的注册机构代码失败。 请文件[bug 报表](http://bugzilla.xamarin.com)。|
|XA4110|注册机构不能封送处理类型"{0}"的 out 参数在方法 {1} 的签名中。|
|XA4111|注册机构无法生成方法 {1} 中的类型"{0}"的签名。|
|XA4200|只能为 'claas 类型生成 ACW 的。|
|XA4201|无法确定类型 {0} JNI 名称。|
|XA4203|指定的类型名称必须是完全限定的。|
|XA4204|无法解析接口类型"{0}"。 是否缺少程序集引用?|
|XA4205|[ExportField] 只能使用 0 参数的方法。|
|XA4206|不能在泛型类型上使用 [导出]。|
|XA4207|[ExportField] 不能在泛型类型上。|
|XA4208|[Java.Interop.ExportFieldAttribute] 不能返回 void 的方法上使用。|
|XA4209|无法为类的创建 JavaTypeInfo： 由于 {1} {0}。|
|XA4210|你需要使用 ExportAttribute 或 ExportFieldAttribute 时添加对 Mono.Android.Export.dll 的引用。|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion "{0}"小于 $(TargetFrameworkVersion) {1}。 使用 API-\ {1 \} ACW 编译。|


### <a name="xa5xxx-errors"></a>XA5xxx 错误

|错误代码|描述|
|--- |--- |
|XA5101|缺少"{0}"编译器。 请安装 Android NDK。|
|XA5102|从程序集转换为本机代码失败。 请文件[bug 报表](http://bugzilla.xamarin.com)。|
|XA5103|编译的文件"{0}"失败。 请文件[bug 报表](http://bugzilla.xamarin.com)。|
|XA5201|本机链接失败。 请查看提供给 gcc 用户标志： {0}|
|XA5202|本机链接失败。 请查看生成日志。|
|XA5303|本机链接警告: {0}。|
|XA5300|Android SDK 找不到或未完全安装。|
|XA5301|缺少最小化工具。 请安装 Xcode 命令行工具组件。|
|XA5302|缺少 dsymutil 工具。 请安装 Xcode 命令行工具组件。|
|XA5203|未能生成调试符号 （dSYM 目录）。 请查看生成日志。|
|XA5204|最小化最终二进制文件失败。 请查看生成日志。|
|XA5205|缺少 aapt 工具。 请安装 Android SDK 生成工具包。|
|XA5206|{0}. Android 资源目录 {1} 不存在。|
|XA5207|{0}. Java 库文件 {1} 不存在。|
|XA5208|下载失败。 请下载 {0} 并将其放到 {1} 目录。|
|XA5209|解压缩失败。 请下载 {0} 并将它解压缩到 {1} 目录。|
|XA5210|{0}. 本机库文件 {1} 不存在。|

### <a name="xa6xxx-errors"></a>XA6xxx 错误

|错误代码|描述|
|--- |--- |
|XA6001|运行 Cecil 版本不支持最小化的程序集。|
|XA6002|无法不去除程序集"{0}"。|
|XA6003|UnauthorizedAccessException 消息。|

### <a name="xa9xxx-errors"></a>XA9xxx 错误

这些错误代码是授权和激活错误。


#### <a name="xa9000"></a>XA9000

 **原因：**许可证已过期

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|警告|警告|警告|警告|警告|


#### <a name="xa9001"></a>XA9001

 **原因：**试用版已过期

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|警告|警告|警告|警告|警告|



#### <a name="xa9002"></a>XA9002

 **原因：** AndroidJavaSource

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|

 **原因：** AndroidJavaLibrary

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|

 **如果绑定程序集具有嵌入.jar，这是捕获在包时，不生成时间。**

 **原因：** AndroidNativeLibrary

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|


#### <a name="xa9003"></a>XA9003

 **原因：** System.Runtime.Serialization

 **检查期间：**包

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **原因：** System.ServiceModel.Web

 **检查期间：**包

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **原因：** Mono.Data.Tds

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **这是由 System.Data.dll，这允许引用**

 **原因：** Mono.Android.Export

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|



#### <a name="xa9004"></a>XA9004

 **原因：** -分析

 **检查期间：**包

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|



#### <a name="xa9005"></a>XA9005

 **原因：**大小限制 (32 kb)。

 **检查期间：**包

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|-|-|-|



#### <a name="xa9006"></a>XA9006

 **原因：** System.Data.SqlClient 命名空间。

 **检查期间：**包

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|


#### <a name="xa9008"></a>XA9008

 **原因：**从命令行生成。

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|


#### <a name="xa9009"></a>XA9009

 **原因：**缺少序列号。

 **检查期间：**从而

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9010"></a>XA9010

 **原因：**无效的 ProductId。

 **检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|

等效于 XA9018。



#### <a name="xa9011"></a>XA9011

 **原因：**未能更新许可证文件 （到新的文件格式）。

 **检查期间：**激活

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|

#### <a name="xa9012"></a>XA9012

 **原因：**没有 internet

 **检查期间：**激活

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9013"></a>XA9013

 **原因：**未知的错误

 **检查期间：**激活

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9014"></a>XA9014

 **原因：**无效激活代码

 **检查期间：**激活

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9017"></a>XA9017

 **原因：**激活服务器不会返回有效的许可证。

 **检查期间：**激活

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9018"></a>XA9018

**原因：**许可证无效

**检查期间：**生成

|入门|Indie|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|-|-|错误|-|-|

