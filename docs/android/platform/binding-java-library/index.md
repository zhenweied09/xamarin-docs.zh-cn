---
title: 绑定 Java 库
description: Android 社区有许多你可能想在应用程序; 要使用的 Java 库本指南说明如何通过创建绑定库将 Java 库合并到 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 3c2ed92da07519516db94697bbeaac9f328baa22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="binding-a-java-library"></a>绑定 Java 库

_Android 社区有许多你可能想在应用程序; 要使用的 Java 库本指南说明如何通过创建绑定库将 Java 库合并到 Xamarin.Android 应用程序。_

## <a name="overview"></a>概述

大规模适用于 Android 的第三方库生态系统。 因此，它经常意义使用比创建一个新的现有 Android 库。 Xamarin.Android 提供两种方式使用这些库：

-   创建*绑定库*以便你可以调用 C# 调用通过 Java 代码自动包装用于 C# 包装器的库。

-   使用*Java 本机接口*(*JNI*) 来直接调用用 Java 库代码的调用。 JNI 是一个编程框架，使 Java 代码可以调用并由本机应用程序或库调用。

本指南说明了第一个选项： 如何创建*绑定库*到你可以将链接到你的应用程序中的程序集中包装一个或多个现有的 Java 库。 有关使用 JNI 的详细信息，请参阅[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin.Android 实现的方法是使用绑定*管理可调用包装器*(*MCW*)。 MCW 是托管的代码需要调用 Java 代码时使用的 JNI 桥梁。 托管的可调用包装器还提供支持，子类化 Java 类型和重写 Java 类型上的虚方法。 同样，只要 Android 运行时 （艺术作品） 代码希望调用托管的代码，它会通过已知作为 Android 可调用包装 (ACW) 的另一个 JNI 桥。 这[体系结构](~/android/internals/architecture.md)在下图中所示：

[![Android JNI 桥体系结构](images/architecture.png)](images/architecture.png#lightbox)

绑定库是 Java 类型包含托管可调用包装器的程序集。 例如，下面是 Java 类型， `MyClass`，我们想要在绑定库中包装：

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

我们生成的绑定库后**.jar**包含`MyClass`，我们可以对其进行实例化并对其调用方法，从 C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要创建此绑定库，你可以使用 Xamarin.Android *Java 绑定库*模板。 生成的绑定项目创建与 MCW 类是.NET 程序集**.jar**文件和对 Android 库项目中嵌入的资源。 您也可以为 Android 存档创建绑定库 (。AAR) 文件和 Eclipse Android 库项目。 通过引用结果绑定库 DLL 程序集，你可以在你的 Xamarin.Android 项目中重用现有的 Java 库。

当你在绑定库中引用的类型时，必须使用绑定库的命名空间。 通常情况下，添加`using`顶部的 C# 源代码的指令文件，它是 Java 包名称的.NET 命名空间版本。 例如，如果您所绑定的名称 Java 包**.jar**如下所示：

```csharp
com.company.package
```

然后可使以下`using`你 C# 源代码文件，以访问顶部的语句类型中绑定**.jar**文件：

```csharp
using Com.Company.Package;
```


当绑定时的现有 Android 库，所以务必请记住以下几点：

* **是否有任何库的外部依赖关系？** &ndash; 必须为 Xamarin.Android 项目中包含的 Android 库所需的任何 Java 依赖关系**ReferenceJar**或**EmbeddedReferenceJar**。 必须将任何本机程序集添加到绑定项目作为**EmbeddedNativeLibrary**。  

* **哪个版本的 Android API？ Android 库目标** &ndash; 不能"降级"Android API 级别;确保 Xamarin.Android 绑定项目所面向的相同的 API，级别 （或更高） 为 Android 库。

* **哪些版本的 JDK 用于编译库？** &ndash; 如果使用不同版本的 JDK 比在使用由 Xamarin.Android 生成 Android 库，可能出现绑定错误。 如果可能，请重新编译使用由您的 Xamarin.Android 的安装的 jdk 的相同版本的 Android 库。


## <a name="build-actions"></a>生成操作

创建绑定库，当您设置*生成操作*上**.jar**或。你将合并到绑定库项目的 AAR 文件&ndash;每个生成操作确定如何**.jar**或。将嵌入到 （或引用） AAR 文件你绑定的库。 以下列表总结了这些生成操作：

* `EmbeddedJar` &ndash; 嵌入**.jar**到生成的绑定库 DLL 作为嵌入资源。 这是最简单和大多数常用的生成操作。 如果你希望使用此选项**.jar**自动编译为字节代码并打包到绑定库。

* `InputJar` &ndash; 不会嵌入**.jar**到结果绑定库。DLL。 你绑定的库。DLL 将具有依赖关系这**.jar**在运行时。 使用此选项，如果不希望包括**.jar**绑定库 （例如，对于授权原因） 中。 如果你使用此选项，则必须确保输入**.jar**可在运行您的应用程序的设备上。

* `LibraryProjectZip` &ndash; 嵌入。插入生成的绑定库 AAR 文件。DLL。 它类似于 EmbeddedJar，只不过你可以在绑定中访问资源 （以及代码）。AAR 文件。 当你想要嵌入时使用此选项。到绑定库 AAR。

* `ReferenceJar` &ndash; 指定的引用**.jar**: 引用**.jar**是**.jar**你绑定之一**.jar**或。AAR 文件取决于。 此引用**.jar**仅用于满足编译时依赖关系。 当你使用此生成操作时，C# 绑定不创建的引用**.jar**且未嵌入在生成的绑定库。DLL。 使用此选项，当你要将绑定库参考**.jar**但尚未尚未执行。 此生成操作可用于打包多个**.jar**s （和/或。AARs) 到多个相互依赖的绑定库。

* `EmbeddedReferenceJar` &ndash; 嵌入引用**.jar**到结果绑定库。DLL。 使用此生成操作，如果你想要创建 C# 为这两个输入绑定**.jar** （或。AAR) 和所有它的引用**.jar**(s) 绑定库中。

* `EmbeddedNativeLibrary` &ndash; 嵌入本机**.so**到的绑定。 此生成操作用于**.so**文件所需的**.jar**文件绑定。 可能有必要，手动加载**.so**库，然后从 Java 库执行代码。 这如下所述。

这些生成的以下指南中的更详细地解释了操作。

此外，以下的生成操作用于帮助导入 Java API 文档，并将它们转换为 C# XML 文档：

* `JavaDocJar` 用于指向 Javadoc 存档 Jar 符合 Maven 包样式的 Java 库 (通常`FOOBAR-javadoc**.jar**`)。
* `JavaDocIndex` 用于指向`index.html`中的 API 参考文档 HTML 文件。
* `JavaSourceJar` 用于补充`JavaDocJar`，在进行第一次从源生成 JavaDoc，然后将结果视为`JavaDocIndex`，符合 Maven 的 Java 库打包样式 (通常`FOOBAR-sources**.jar**`)。

API 文档应从 Java8、 Java7 或 Java6 SDK （它们是所有其他格式），默认 doclet 或 DroidDoc 样式。

## <a name="including-a-native-library-in-a-binding"></a>绑定中包括本机库

它可能需要包括**.so**库，请在 Xamarin.Android 绑定的绑定 Java 库的一部分。 Xamarin.Android 的已包装的 Java 代码执行时，将无法使 JNI 调用和错误消息_java.lang.UnsatisfiedLinkError： 找不到的本机方法：_会在 logcat 出应用程序中显示。

此解决方法是手动加载**.so**库通过调用`Java.Lang.JavaSystem.LoadLibrary`。 例如假设 Xamarin.Android 项目已共享库**libpocketsphinx_jni.so**具有生成操作的绑定项目中包含**EmbeddedNativeLibrary**，则以下（在使用共享的库之前执行） 的代码段将加载**.so**库：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>调整到 C Java Api&eparsl;

Xamarin.Android 绑定生成器将更改某些 Java 习语和模式以与.NET 模式相对应。 下面介绍了如何将 Java 映射到 C# /.NET:

-   _Setter/Getter 方法_在 Java_属性_.NET 中。

-   _字段_在 Java_属性_.NET 中。

-   _侦听器/侦听器接口_在 Java_事件_.NET 中。 在回调接口的方法的参数将由`EventArgs`子类。

-   A_静态嵌套类_处于 Java_嵌套类_.NET 中。

-   _内部类_处于 Java_嵌套类_使用 C# 中的实例构造函数。



## <a name="binding-scenarios"></a>绑定方案

以下绑定方案指南可帮助你将 Java 库 （或库） 绑定以便合并到你的应用程序：

-   [绑定。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是用于创建用于绑定库演练**.jar**文件。

-   [绑定。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是用于创建用于绑定库演练。AAR 文件。 读取本演练中，若要了解如何将绑定 Android Studio 库。

-   [绑定 Eclipse 库项目](~/android/platform/binding-java-library/binding-a-library-project.md)是用于从 Android 库项目中创建绑定库演练。 读取本演练中，若要了解如何将绑定 Eclipse Android 库项目。

-   [自定义绑定](~/android/platform/binding-java-library/customizing-bindings/index.md)说明如何手动修改绑定以解决生成错误，并调整所获得的 API，因此很多个"C#-如"。

-   [故障排除绑定](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出常见绑定错误情况，说明可能的原因，并提供解决这些错误的建议。


## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 元数据](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用本机库](~/android/platform/native-libraries.md)
