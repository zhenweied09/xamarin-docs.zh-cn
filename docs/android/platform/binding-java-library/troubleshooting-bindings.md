---
title: 故障排除的绑定
description: 本文总结了生成绑定，以及可能的原因和建议的方法可以解决它们时可能发生的多种常见错误。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: da6286eed091114c117c723f462bbb8cac77034b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771091"
---
# <a name="troubleshooting-bindings"></a>故障排除的绑定

_本文总结了生成绑定，以及可能的原因和建议的方法可以解决它们时可能发生的多种常见错误。_


## <a name="overview"></a>概述

绑定 Android 库 ( **.aar**或 **.jar**) 文件很少是简单的次重要; 它通常需要额外的工作来减少从 Java 和.NET 之间的差异导致的问题。
这些问题将阻止 Xamarin.Android 绑定 Android 库，并显示为生成日志中的错误消息。 本指南将提供用于解决问题的一些提示，列出了一些较为常见的问题/情景，并提供成功绑定 Android 库的可能解决方案。

当绑定时的现有 Android 库，所以务必请记住以下几点：

- **库的外部依赖关系** &ndash; Android 库所需的任何 Java 依赖项必须包含在与的 Xamarin.Android 项目**ReferenceJar**或**EmbeddedReferenceJar**。

- **Android 库是针对 Android API 级别**&ndash;它不是"降级"Android API 级别; 确保 Xamarin.Android 绑定项目所面向的相同的 API，级别 （或更高） 为 Android 库。

- **用于打包 Android 库的 Android jdk 版本**&ndash;绑定错误可能会出现，如果使用不同版本的 JDK 比在使用一个由 Xamarin.Android 生成 Android 库。 如果可能，请重新编译使用由您的 Xamarin.Android 的安装的 jdk 的相同版本的 Android 库。

解决问题的绑定 Xamarin.Android 库的第一步是启用[诊断 MSBuild 输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
启用诊断的输出后, 重新生成 Xamarin.Android 绑定项目并检查生成日志，以找到有关问题的原因的线索。

它可以证明反编译 Android 库并检查的类型和 Xamarin.Android 尝试绑定的方法很有帮助。 这一点在本指南中的更高版本上的更多详细信息。


## <a name="decompiling-an-android-library"></a>反编译 Android 库

检查的类和的 Java 类的方法可以提供有价值的信息将会帮助你在绑定的库。
[博士 GUI](http://jd.benow.ca/)是一种图形工具，可显示从 Java 源代码**类**包含在一个 JAR 文件。 它可以作为独立应用程序或插件为于 IntelliJ 或运行 Eclipse。

反编译 Android 库打开 **。JAR** Java 反编译程序文件。 如果库 **。AAR**文件中，有必要将文件解压缩**classes.jar**该存档文件中。 以下是使用博士 GUI 分析的示例屏幕快照[高手](http://square.github.io/picasso/)JAR:

![使用 Java 反编译程序分析高手 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

一旦你有反编译 Android 库，检查的源代码。 通常情况下，查找：

- **具有模糊处理的特征的类**&ndash;的经过模糊处理的类特性包括：

    - Class 名称中包含**$**，即 **$.class**
    - 类名称完全泄露小写字符，即**a.class**      

- **`import` 语句未引用库**&ndash;标识未引用的库，并将这些依赖项添加到的项目的 Xamarin.Android 绑定**生成操作**的**ReferenceJar**或**EmbedddedReferenceJar**。

> [!NOTE]
> 反编译 Java 库可能禁止或受合法限制的约束根据当地的法律或 Java 库已发布在其下的许可证。 如有必要，请在尝试反编译 Java 库和检查源代码之前登记合法 professional 的服务。


## <a name="inspect-apixml"></a>检查 API。XML

作为生成的绑定项目的一部分，Xamarin.Android 将生成 XML 文件名**obj/Debug/api.xml**:

![在 obj / 调试下的生成的 api.xml](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此文件提供的所有 Java Api 列表 Xamarin.Android 正在绑定。 此文件的内容可以帮助确定缺少的任何类型或方法，重复的绑定。 尽管检查此文件是单调乏味并且耗时，但它可以提供在内容可能会导致任何绑定问题的线索。 例如， **api.xml**属性返回不适合的类型，或有两个类型该共享相同的托管名称可能会显示。


## <a name="known-issues"></a>已知问题

本部分将列出的一些常见的错误消息或症状，我尝试绑定 Android 库时发生。


### <a name="problem-java-version-mismatch"></a>问题： Java 版本不匹配

有时不会生成类型或者因为您正在使用或者较新的或较旧版本的 Java 相比库用编译，可能发生意外的故障。 重新编译你的 Xamarin.Android 项目使用的 jdk 的相同版本的 Android 库。


### <a name="problem-at-least-one-java-library-is-required"></a>至少一个 Java 库是必需的问题：

你将收到错误"至少一个 Java 库是必需的"即使。已添加 JAR。

#### <a name="possible-causes"></a>可能的原因：

请确保生成操作设置为`EmbeddedJar`。 因为有多个生成操作。JAR 文件 (如`InputJar`， `EmbeddedJar`，`ReferenceJar`和`EmbeddedReferenceJar`)，绑定生成器无法自动猜测默认情况下使用哪一个。 有关生成操作的详细信息，请参阅[生成操作](~/android/platform/binding-java-library/index.md)。


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>问题： 绑定工具无法加载。JAR 库

绑定库生成器无法加载。JAR 库。

#### <a name="possible-causes"></a>可能的原因

一些。使用代码 （通过如 Proguard 的工具） 的模糊处理的 JAR 库无法加载的 Java 工具。 由于我们的工具都会使用 Java 反射和工程库 ASM 字节代码，这些依赖的工具可以拒绝的经过模糊处理的库，虽然可能将传递 Android 运行时工具。 此解决方法是以手动绑定而不是使用绑定生成器这些库。



### <a name="problem-missing-c-types-in-generated-output"></a>问题： 缺少 C# 中生成的输出类型。

绑定 **.dll**生成但未命中某些 Java 类型，或生成的 C# 源代码不会生成由于一个错误，指出有缺少的类型。

#### <a name="possible-causes"></a>可能的原因：

此错误可能有多种原因，如下所示：

-   要绑定的库可能引用第二个 Java 库。 如果绑定的库的公共 API 使用从第二个库的类型，则必须引用第二个库的托管的绑定。

-   很可能库而插入由于 Java 反射，类似于上面，导致意外的元数据加载库加载错误的原因。 Xamarin.Android 的工具当前不能解决此问题。 在这种情况下，必须手动绑定库。

-   无法加载程序集时它应具有的.NET 4.0 运行时出现 bug。 已在.NET 4.5 运行时中修复此问题。

-   Java 允许从非公共类，派生公共类，但在.NET 中，这不支持。 由于绑定生成器不会生成绑定非公共类，派生类，如这些不能正确生成。 若要解决此问题，请删除这些派生的类使用中的删除节点的元数据条目**Metadata.xml**，或修复公开的非公共类的元数据。 尽管后一种解决方案将创建绑定，以便将生成 C# 源代码，但不应使用的非公共类。

    例如：

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   对 Java 库进行模糊处理的工具可能会影响 Xamarin.Android 绑定生成器，并且能够生成 C# 包装类。 下面的代码段演示如何更新**Metadata.xml**以 unobfuscate 类名称：

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>问题： 生成的 C# 源不会生成由于参数类型不匹配

生成的 C# 源代码不会生成。 重写方法的参数类型不匹配。

#### <a name="possible-causes"></a>可能的原因：

Xamarin.Android 包括各种 Java 字段映射到在 C# 的绑定中的枚举。 这些可能会导致生成的绑定类型不兼容问题。 若要解决此问题，需要修改以使用枚举从绑定生成器创建的方法签名。 有关详细信息，请参阅[更正枚举](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>包中的问题： NoClassDefFoundError

`java.lang.NoClassDefFoundError` 在打包步骤中引发。

#### <a name="possible-causes"></a>可能的原因：

此错误最可能的原因是必需的 Java 库需要添加到应用程序项目 (**.csproj**)。 .JAR 文件不自动解决。 针对目标设备或仿真程序中不存在的用户程序集始终不生成 Java 库绑定 (如 Google 地图**maps.jar**)。 这是与库的不用于 Android 库项目支持，这种情况。JAR 嵌入库 dll 中。 例如： [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>问题： 重复的自定义 EventArgs 类型

由于重复的自定义 EventArgs 类型，生成失败。 发生如下错误：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

这是由于来自多个共享具有相同名称的方法的接口"侦听器"类型的事件类型之间的某些冲突。 例如，如果有两个 Java 接口中下面的示例所示，生成器将创建`DismissScreenEventArgs`两个`MediationBannerListener`和`MediationInterstitialListener`，这会导致错误中。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

这是设计使然，以便避免事件自变量类型上的时间较长名称的不同而不同。 若要避免这些冲突，某些元数据转换是必需的。 编辑[ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml)并添加`argsType`接口中的任何一个 （或接口方法） 的属性：

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>问题： 类未实现接口方法

该值指示，生成的类未实现所需的生成的类实现的接口方法生成一条错误消息。 但是，查看生成的代码，你可以看到，实现方法。

下面是错误的一个示例：

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>可能的原因：

这是协变返回类型与绑定 Java 方法会出现一个问题。 在此示例中，该方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`需要返回`Java.Lang.Object`。 但是，该方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`具有返回类型的`HttpURLConnection`。 有两种方法，若要解决此问题：

-   添加的分部类声明`HttpURLConnectionRequestAdapter`和显式实现`IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   从生成的 C# 代码中删除协方差。 这涉及到添加到以下转换**Transforms\Metadata.xml**这将导致生成的 C# 的代码以具有返回类型的`Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>问题： 名称内部的类上的冲突 / 属性

继承的对象的冲突可见性。

在 Java 中，它不具有必需的派生的类具有为其父级相同的可见性。 Java 将只是，为你解决问题。 在 C# 中，具有可以明确，因此你需要确保层次结构中的所有类都具有相应可见性。 下面的示例演示如何将 Java 包名称，由`com.evernote.android.job`到`Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>问题： A **.so**绑定所需的库是未加载

某些绑定项目还取决于中功能 **.so**库。 很可能不会自动加载 Xamarin.Android **.so**库。 Xamarin.Android 的已包装的 Java 代码执行时，将无法使 JNI 调用和错误消息_java.lang.UnsatisfiedLinkError： 找不到的本机方法：_ 会在 logcat 出应用程序中显示。

此解决方法是手动加载 **.so**库通过调用`Java.Lang.JavaSystem.LoadLibrary`。 例如假设 Xamarin.Android 项目已共享库**libpocketsphinx_jni.so**具有生成操作的绑定项目中包含**EmbeddedNativeLibrary**，则以下（在使用共享的库之前执行） 的代码段将加载 **.so**库：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>总结

在本文中，我们将列出与 Java 绑定相关联的常见故障排除问题，并且说明了如何解决这些问题。


## <a name="related-links"></a>相关链接

- [库项目](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [启用诊断输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin for Android 开发人员](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
