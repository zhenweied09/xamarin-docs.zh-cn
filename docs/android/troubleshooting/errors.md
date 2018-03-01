---
title: "Xamarin.Android 错误矩阵"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c3aab44fcb0522b762aaa101de0aeba08016b641
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android 错误矩阵

## <a name="errors-reference"></a>错误参考

通过 Xamarin 还原时，本文档提供的各种错误代码的一些信息。

<table>
    <thead>
        <tr>
            <td>类别</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr><td>XA0xxx</td><td><code>mandroid</code> 错误</td></tr>
        <tr><td>XA1xxx</td><td>文件复制 / 符号链接 （相关项目） 错误</td></tr>
        <tr><td>XA2xxx</td><td>链接器错误</td></tr>
        <tr><td>XA3xxx</td><td>AOT 错误</td></tr>
        <tr><td>XA4xxx</td><td>代码生成错误</td></tr>
        <tr><td>XA5xxx</td><td>GCC 和工具链错误</td></tr>
        <tr><td>XA6xxx</td><td><code>mandroid</code> 内部工具错误</td></tr>
        <tr><td>XA7xxx</td><td>保留</td></tr>
        <tr><td>XA8xxx</td><td>保留</td></tr>
        <tr><td>XA9xxx</td><td>许可错误</td></tr>
    </tbody>
</table>

## <a name="error-codes"></a>错误代码

### <a name="xa0xxx-errors"></a>XA0xxx 错误

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA0000</td><td>意外的错误-请填写在一个 bug 报告<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA0001</td><td>-devname 提供不需要任何特定于设备的操作</td></tr>
        <tr><td>XA0002</td><td>无法分析的环境变量"{0}"。</td></tr>
        <tr><td>XA0003</td><td>使用 SDK 或产品程序集 (.dll) 名称的应用程序名称 {0}.exe 冲突。</td></tr>
        <tr><td>XA0004</td><td>新 refcounting 逻辑需要 sgen 太启用。</td></tr>
        <tr><td>XA0005</td><td>输出目录"{0}"不存在</td></tr>
        <tr><td>XA0006</td><td>在"{0}"没有任何 devel 的平台，请使用-平台 = PLAT 指定 SDK</td></tr>
        <tr><td>XA0007</td><td>根程序集"{0}"不存在</td></tr>
        <tr><td>XA0008</td><td>应提供一个根只有程序集</td></tr>
        <tr><td>XA0009</td><td>加载程序集时出错： {0}</td></tr>
        <tr><td>XA0010</td><td>无法分析命令行参数： {0}</td></tr>
        <tr><td>XA0011</td><td>{0} 已针对较新的运行时 ({1}) 构建的比 MonoTouch 支持</td></tr>
        <tr><td>XA0012</td><td>不完整的数据用于完成`{0}`。</td></tr>
        <tr><td>XA0013</td><td>分析支持需要 sgen 太启用</td></tr>
        <tr><td>XA0014</td><td>iOS {0} 不支持面向 ARMv6 构建应用程序</td></tr>
        <tr><td>XA0020</td><td>无法确定 mandroid 路径。</td></tr>
        <tr><td>XA0100</td><td>EmbeddedNativeLibrary"{0}"在 Android 应用程序项目中无效。 请改为使用 AndroidNativeLibrary。</td></tr>
    </tbody>
</table>

### <a name="xa1xxx-errors"></a>XA1xxx 错误

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA1001</td><td>找不到应用程序在指定的目录</td></tr>
        <tr><td>XA1002</td><td>无法创建符号链接，复制文件</td></tr>
        <tr><td>XA1003</td><td>无法终止应用程序"{0}"。 你可能必须手动终止应用程序。</td></tr>
        <tr><td>XA1004</td><td>无法获取安装的应用程序的列表。</td></tr>
        <tr><td>XA1005</td><td>无法取消设备 {1} 上的应用程序"{0}": {2}。 你可能必须手动终止应用程序。</td></tr>
        <tr><td>XA1006</td><td>无法在设备 {1} 上安装应用程序"{0}": {2}。</td></tr>
        <tr><td>XA1007</td><td>无法启动设备 {1} 上的应用程序"{0}": {2}。 你仍可以通过在其上的点击手动启动该应用程序。</td></tr>
        <tr><td>XA1008</td><td>无法启动模拟器： {0}</td></tr>
        <tr><td>XA1009</td><td>无法将程序集"{0}"复制到 {1}: {2}</td></tr>
        <tr><td>XA1010</td><td>无法加载的程序集"{0}": {1}</td></tr>
        <tr><td>XA1011</td><td>无法添加缺少的资源文件:"{0}"</td></tr>
        <tr><td>XA1101</td><td>无法启动应用程序</td></tr>
        <tr><td>XA1102</td><td>无法附加到应用程序 （以终止它）： {0}</td></tr>
        <tr><td>XA1103</td><td>无法分离</td></tr>
        <tr><td>XA1104</td><td>发送数据包失败： {0}</td></tr>
        <tr><td>XA1105</td><td>意外的响应类型</td></tr>
        <tr><td>XA1106</td><td>无法在设备上获取的应用程序的列表： 请求的操作已超时。</td></tr>
        <tr><td>XA1107</td><td>应用程序启动失败</td></tr>
        <tr><td>XA1201</td><td>无法加载模拟器： {0}</td></tr>
        <tr><td>XA1301</td><td>本机库`{0}`({1}) 已被忽略，因为它与当前生成 architecture(s) ({2}) 不匹配</td></tr>
    </tbody>
</table>

### <a name="xa2xxx-errors"></a>XA2xxx 错误

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA2001</td><td>无法链接程序集</td></tr>
        <tr><td>XA2002</td><td>无法解析引用： {0}</td></tr>
        <tr><td>XA2003</td><td>选项"{0}"将被忽略，因为链接被禁用</td></tr>
        <tr><td>XA2004</td><td>找不到额外链接器定义文件"{0}"。</td></tr>
        <tr><td>XA2005</td><td>无法分析从"{0}"的定义。</td></tr>
        <tr><td>XA2006</td><td>无法解析对元数据项"{0}"（在 {1} 中定义） 从 {2} 的引用。</td></tr>
    </tbody>
</table>

### <a name="xa3xxx-errors"></a>XA3xxx 错误

这些是 AOT 错误。

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA3001</td><td>无法不 AOT 的程序集"{0}"</td></tr>
        <tr><td>XA3002</td><td>AOT 限制： 方法"{0}"必须是静态，因为它用 [MonoPInvokeCallback] 修饰。</td></tr>
        <tr><td>XA3003</td><td>冲突-调试和-llvm 选项。 软调试被禁用。</td></tr>
    </tbody>
</table>

### <a name="xa4xxx-errors"></a>XA4xxx 错误

这些是代码生成错误。

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA4001</td><td>主模板中找不到 expansed `{0}`。</td></tr>
        <tr><td>XA4101</td><td>注册机构无法生成类型的签名`{0}`。</td></tr>
        <tr><td>XA4102</td><td>注册机构找到无效的类型`{0}`方法签名中`{2}`。 请改用 `{1}`。</td></tr>
        <tr><td>XA4103</td><td>注册机构找到无效的类型`{0}`方法签名中`{2}`： 的类型实现 INativeObject，但没有采用两个构造函数 (IntPtr，bool) 自变量</td></tr>
        <tr><td>XA4104</td><td>注册机构不能封送类型的返回值`{0}`方法签名中`{1}`。</td></tr>
        <tr><td>XA4105</td><td>注册机构不能封送类型的参数`{0}`方法签名中`{1}`。</td></tr>
        <tr><td>XA4106</td><td>注册机构不能封送结构的返回值`{0}`方法签名中`{1}`。</td></tr>
        <tr><td>XA4107</td><td>注册机构不能封送类型的参数`{0}`方法签名中`{1}`。</td></tr>
        <tr><td>XA4108</td><td>注册机构无法获取托管类型的 ObjectiveC 类型`{0}`。</td></tr>
        <tr><td>XA4109</td><td>编译生成的注册机构代码失败。 在一个 bug 报告，请记录<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA4110</td><td>注册机构不能封送类型的 out 参数`{0}`方法签名中`{1}`。</td></tr>
        <tr><td>XA4111</td><td>注册机构无法生成类型的签名`{0}' in method `{1}。</td></tr>
        <tr><td>XA4200</td><td>只能为 'claas 类型生成 ACW 的。</td></tr>
        <tr><td>XA4201</td><td>无法确定类型 {0} JNI 名称。</td></tr>
        <tr><td>XA4203</td><td>指定的类型名称必须是完全限定的。</td></tr>
        <tr><td>XA4204</td><td>无法解析接口类型"{0}"。 是否缺少程序集引用?</td></tr>
        <tr><td>XA4205</td><td>[ExportField] 只能使用 0 参数的方法。</td></tr>
        <tr><td>XA4206</td><td>不能在泛型类型上使用 [导出]。</td></tr>
        <tr><td>XA4207</td><td>[ExportField] 不能在泛型类型上。</td></tr>
        <tr><td>XA4208</td><td>[Java.Interop.ExportFieldAttribute] 不能返回 void 的方法上使用。</td></tr>
        <tr><td>XA4209</td><td>无法为类的创建 JavaTypeInfo： 由于 {1} {0}</td></tr>
        <tr><td>XA4210</td><td>你需要使用 ExportAttribute 或 ExportFieldAttribute 时添加对 Mono.Android.Export.dll 的引用。</td></tr>
        <tr><td>XA4211</td><td>AndroidManifest.xml //uses-sdk/@android:targetSdkVersion "{0}"小于 $(TargetFrameworkVersion) {1}。 使用 API-\ {1 \} ACW 编译。</td></tr>
    </tbody>
</table>

### <a name="xa5xxx-errors"></a>XA5xxx 错误

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA5101</td><td>缺少"{0}"编译器。 请安装 Android NDK。</td></tr>
        <tr><td>XA5102</td><td>从程序集转换为本机代码失败。 在一个 bug 报告，请记录<a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5103</td><td>编译的文件"{0}"失败。 在一个 bug 报告，请记录<a href="http://bugzilla.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5201</td><td>本机链接失败。 请查看提供给 gcc 用户标志： {0}</td></tr>
        <tr><td>XA5202</td><td>本机链接失败。 请查看生成日志。</td></tr>
        <tr><td>XA5303</td><td>本机链接警告： {0}</td></tr>
        <tr><td>XA5300</td><td>Android SDK 找不到或未完全安装。</td></tr>
        <tr><td>XA5301</td><td>缺少最小化工具。 请安装 Xcode 命令行工具组件</td></tr>
        <tr><td>XA5302</td><td>缺少 dsymutil 工具。 请安装 Xcode 命令行工具组件</td></tr>
        <tr><td>XA5203</td><td>未能生成调试符号 （dSYM 目录）。 请查看生成日志。</td></tr>
        <tr><td>XA5204</td><td>最小化最终二进制文件失败。 请查看生成日志。</td></tr>
        <tr><td>XA5205</td><td>缺少 aapt 工具。 请安装 Android SDK 生成工具包。</td></tr>
        <tr><td>XA5206</td><td>{0}. Android 资源目录 {1} 不存在。</td></tr>
        <tr><td>XA5207</td><td>{0}. Java 库文件 {1} 不存在。</td></tr>
        <tr><td>XA5208</td><td>下载失败。 请下载 {0} 并将其放到 {1} 目录。</td></tr>
        <tr><td>XA5209</td><td>解压缩失败。 请下载 {0} 并将它解压缩到 {1} 目录。</td></tr>
        <tr><td>XA5210</td><td>{0}. 本机库文件 {1} 不存在。</td></tr>
    </tbody>
</table>

### <a name="xa6xxx-errors"></a>XA6xxx 错误

<table>
    <thead>
        <tr><td>错误代码</td><td>描述</td></tr>
    </thead>
    <tbody>
        <tr><td>XA6001</td><td>运行 Cecil 版本不支持最小化的程序集</td></tr>
        <tr><td>XA6002</td><td>无法对程序集不去除`{0}`。</td></tr>
        <tr><td>XA6003</td><td>UnauthorizedAccessException message]</td></tr>
    </tbody>
</table>

### <a name="xa9xxx-errors"></a>XA9xxx 错误

此错误代码是授权和激活错误。

 <a name="xa9000" />


#### <a name="xa9000"></a>XA9000

 **原因：**许可证已过期

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
        </tr>
    </tbody>
</table>

 <a name="XA9001" />


#### <a name="xa9001"></a>XA9001

 **原因：**试用版已过期

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
            <td>警告</td>
        </tr>
    </tbody>
</table>

 <a name="XA9002" />


#### <a name="xa9002"></a>XA9002

 **原因：** AndroidJavaSource

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 **原因：** AndroidJavaLibrary

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 **如果绑定程序集具有嵌入.jar，这是捕获在包时，不生成时间。**

 **原因：** AndroidNativeLibrary

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9003" />


#### <a name="xa9003"></a>XA9003

 **原因：** System.Runtime.Serialization

 **检查期间：**包

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 **原因：** System.ServiceModel.Web

 **检查期间：**包

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 **原因：** Mono.Data.Tds

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 **这是由 System.Data.dll，这允许引用**

 **原因：** Mono.Android.Export

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9004" />


#### <a name="xa9004"></a>XA9004

 **原因：** -分析

 **检查期间：**包

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9005" />


#### <a name="xa9005"></a>XA9005

 **原因：**大小限制 (32 kb)

 **检查期间：**包

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>确定</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>

 <a name="XA9006" />


#### <a name="xa9006"></a>XA9006

 **原因：** System.Data.SqlClient 命名空间

 **检查期间：**包

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9008" />


#### <a name="xa9008"></a>XA9008

 **原因：**从命令行生成

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>确定</td>
            <td>确定</td>
            <td>确定</td>
        </tr>
    </tbody>
</table>

 <a name="XA9009" />


#### <a name="xa9009"></a>XA9009

 **原因：**缺少序列号

 **检查期间：**从而

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

 <a name="XA9010" />


#### <a name="xa9010"></a>XA9010

 **原因：**无效 ProductId

 **检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

等效于 XA9018

 <a name="XA9011" />


#### <a name="xa9011"></a>XA9011

 **原因：**未能更新 （为新的文件格式） 的许可证文件

 **检查期间：**激活

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

 <a name="XA9012" />


#### <a name="xa9012"></a>XA9012

 **原因：**没有 internet

 **检查期间：**激活

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

 <a name="XA9013" />


#### <a name="xa9013"></a>XA9013

 **原因：**未知的错误

 **检查期间：**激活

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

 <a name="XA9014" />


#### <a name="xa9014"></a>XA9014

 **原因：**无效激活代码

 **检查期间：**激活

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

 <a name="XA9017" />


#### <a name="xa9017"></a>XA9017

 **原因：**激活服务器不会返回有效的许可证

 **检查期间：**激活

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
            <td>错误</td>
        </tr>
    </tbody>
</table>

<a name="XA9018" />

#### <a name="xa9018"></a>XA9018

**原因：**许可证无效

**检查期间：**生成

<table>
    <thead>
        <tr>
            <th>入门</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>业务</th>
            <th>企业</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-</td>
            <td>-</td>
            <td>错误</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
