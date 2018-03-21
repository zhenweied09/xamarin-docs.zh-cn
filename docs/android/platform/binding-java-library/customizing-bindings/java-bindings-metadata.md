---
title: "Java 绑定元数据"
description: "在 Xamarin.Android 的 C# 代码是提取的低级别的详细信息，指定在 Java 本机接口 (JNI) 的一种机制的绑定通过调用 Java 库。 Xamarin.Android 提供工具生成这些绑定。 此工具允许开发人员控件如何通过使用元数据，允许如修改命名空间以及成员重命名过程创建一个绑定。 本文档讨论元数据的工作原理、 总结了属性的元数据支持，并说明如何通过修改此元数据来纠正绑定问题。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 70f17b6bc8dc991534cdf4dd065c813aa0e27e96
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2018
---
# <a name="java-bindings-metadata"></a>Java 绑定元数据

_在 Xamarin.Android 的 C# 代码是提取的低级别的详细信息，指定在 Java 本机接口 (JNI) 的一种机制的绑定通过调用 Java 库。Xamarin.Android 提供工具生成这些绑定。此工具允许开发人员控件如何通过使用元数据，允许如修改命名空间以及成员重命名过程创建一个绑定。本文档讨论元数据的工作原理、 总结了属性的元数据支持，并说明如何通过修改此元数据来纠正绑定问题。_


## <a name="overview"></a>概述

Xamarin.Android **Java 绑定库**尝试自动执行必要的绑定有时称为工具的帮助的现有 Android 库的工作大部分_绑定生成器_。 Xamarin.Android 当绑定 Java 库时，将检查的 Java 类并生成的所有包、 类型和成员的列表用于绑定。 此列表 Api 存储在一个 XML 文件，可以找到在**\{项目 directory}\obj\Release\api.xml**为**版本**生成和**\{项目directory}\obj\Debug\api.xml**为**调试**生成。

![Api.xml 文件 obj/Debug 文件夹中的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

绑定生成器将会使用**api.xml**文件作为一项准则用于生成必要的 C# 包装类。 此 XML 文件的内容是一种变体 Google _Android Open Source Project_格式。
下面的代码段是内容的一个示例**api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

在此示例中， **api.xml**声明中的类`android`包名为`Manifest`扩展`java.lang.Object`。

在许多情况下，人工协助是以 Java API 认为详细"等.NET"或更正禁止编译将绑定程序集的问题必需的。 例如，可能有必要将 Java 包名称更改为.NET 命名空间、 重命名类，或更改方法的返回类型。

这些更改不通过修改**api.xml**直接。
相反，更改都记录在特殊均由 Java 绑定库模板提供的 XML 文件。 在编译 Xamarin.Android 绑定程序集时，绑定生成器创建的绑定程序集时将受这些映射文件

可能在中找到这些 XML 映射文件**转换**项目文件夹中：

-   **MetaData.xml** &ndash;允许更改不会对最终的 API，如更改所生成的绑定的命名空间。 

-   **EnumFields.xml** &ndash;包含 Java 之间的映射`int`常量和 C# `enums` 。 

-   **EnumMethods.xml** &ndash;允许更改方法参数和返回类型从 Java`int`向 C# 常量`enums`。 

**MetaData.xml**文件是最导入这些文件，因为它允许对等绑定的通用更改：

-   因此，它们遵循.NET 约定，重命名命名空间、 类、 方法或字段。 

-   删除命名空间、 类、 方法或不需要的字段。 

-   将类移到不同的命名空间。 

-   添加其他支持类，以使绑定的设计遵循.NET framework 模式。 

允许将移以讨论**Metadata.xml**中更多详细信息。


## <a name="metadataxml-transform-file"></a>Metadata.xml 转换文件

因为我们已经学习了，该文件**Metadata.xml**绑定生成器用于影响创建绑定程序集。
元数据格式使用[XPath](https://www.w3.org/TR/xpath/)语法和几乎等同于*GAPI 元数据*中所述[GAPI 元数据](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南。 此实现是几乎 XPath 1.0 的完整实现，并因此支持在标准 1.0 中的项。 此文件是强大的基于 XPath 机制来更改、 添加、 隐藏或移动 API 文件中的任何元素或属性。 所有元数据规范中的规则元素包括用于标识规则是要应用的节点的路径属性。 按以下顺序应用规则：

* **添加节点**&ndash;将一个子节点追加到 path 特性指定的节点。
* **attr** &ndash;设置路径属性指定的元素的属性的值。
* **删除节点**&ndash;中删除节点匹配指定的 XPath。

以下是一种**Metadata.xml**文件：

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

下面列出的一些更常使用的 XPath 元素 Java api 的：

-   `interface` &ndash; 用于查找 Java 接口。 例如`/interface[@name='AuthListener']`。

-   `class` &ndash; 用于查找一个类。 例如`/class[@name='MapView']`。

-   `method` &ndash; 用于查找上一个 Java 类或接口的方法。 例如`/class[@name='MapView']/method[@name='setTitleSource']`。

-   `parameter` &ndash; 标识一种方法的参数。 例如 `/parameter[@name='p0']`



### <a name="adding-types"></a>添加类型

`add-node`元素将告诉 Xamarin.Android 绑定项目以添加到新的包装类**api.xml**。 例如，下面的代码段将直接绑定生成器来创建一个类具有一个构造函数和单个字段：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>删除类型

它可指示 Xamarin.Android 绑定生成器将忽略 Java 类型并不将其绑定。 这可通过添加`remove-node`XML 元素**metadata.xml**文件：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重命名的成员

重命名成员不能通过直接编辑**api.xml**文件，因为 Xamarin.Android 要求原始 Java 本机接口 (JNI) 名称。 因此，`//class/@name`无法更改属性; 如果是，绑定将不起作用。

我们想要重命名类型，这种的情况，请考虑`android.Manifest`。
若要实现此目的，我们可能会尝试直接编辑**api.xml**和重命名类如下所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

这将导致绑定生成器创建的以下 C# 代码包装器类：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

请注意，包装器类已重命名为`NewName`，而原始的 Java 类型仍为`Manifest`。 不再可以访问的任何方法的 Xamarin.Android 绑定类`android.Manifest`; 包装类绑定到不存在 Java 类型。

若要正确更改包装类型 （或方法） 的托管的名称，是必需设置`managedName`特性在此示例中所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>重命名`EventArg`包装类

如果 Xamarin.Android 绑定生成器标识`onXXX`setter 方法_侦听器类型_，C# 事件和`EventArgs`将生成子类以支持.NET flavoured API 基于 Java 的侦听器模式。 作为示例，请考虑下面的 Java 类和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android 将删除前缀`on`从 setter 方法并改为使用`2DSignNextManuever`为的名称的基础`EventArgs`子类。 子类将命名如下：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

这不是合法的 C# 类名称。 若要更正此问题，绑定作者必须使用`argsType`属性，并提供 C# 的有效名称`EventArgs`子类：
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>受支持的属性

以下各节描述了一些转换 Java Api 的属性。

### <a name="argstype"></a>argsType

此特性置于 setter 方法来命名`EventArg`会生成，以支持 Java 侦听器的子类。 这下面部分中的详细信息中所述[重命名 EventArg 包装类](#Renaming_EventArg_Wrapper_Classes)本指南中的更高版本上。

### <a name="eventname"></a>eventName

指定事件的名称。 如果为空，但它会抑制事件生成。
这部分标题中的更多详细信息中所述[重命名 EventArg 包装类](#Renaming_EventArg_Wrapper_Classes)。

### <a name="managedname"></a>managedName

这用于更改的包、 类、 方法或参数的名称。 例如，若要更改的 Java 类名称`MyClass`到`NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一个示例演示了重命名方法的 XPath 表达式`java.lang.object.toString`到`Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` 用于更改方法的返回类型。 在某些情况下绑定生成器不正确地将推断 Java 方法，这将导致编译时错误的返回类型。 在此情况下一个可能的解决方案是更改该方法的返回类型。

例如，绑定生成器认为，Java 方法`de.neom.neoreadersdk.resolution.compareTo()`应返回`int`，这将导致错误消息**错误 CS0535: DE。Neom.Neoreadersdk.Resolution 不实现接口成员 Java.Lang.IComparable.CompareTo(Java.Lang.Object)**。 下面的代码段演示如何更改生成 C# 中的方法的返回类型`int`到`Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

更改方法的返回类型。 这不会更改返回的属性 （作为有所变化，返回属性可以导致 JNI 签名不兼容发生更改）。 在下面的示例中的返回类型的`append`方法从更改`SpannableStringBuilder`到`IAppendable`（回想一下，C# 不支持协变返回类型）：

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>模糊处理

对 Java 库进行模糊处理的工具可能会影响 Xamarin.Android 绑定生成器，并且能够生成 C# 包装类。 经过模糊处理的类的特征包括: * class 名称中包含 **$** ，即**$.class** * 类名称完全泄露小写字符，即**a.class**

此代码段演示如何生成一个"未经过模糊处理"的 C# 类型：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

此属性可以用于更改托管的属性的名称。

专用的化形式使用`propertyName`涉及到其中一个 Java 类具有只有一个字段的 getter 方法的情况。 在此情况下绑定生成器可能需要创建一个只写属性，不建议将使用.NET 中的内容。 下面的代码段演示如何通过设置"删除"的.NET 属性`propertyName`为空字符串：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

请注意仍由绑定生成器将创建的 setter 和 getter 方法。

### <a name="sender"></a>sender

指定的一种方法的参数应为`sender`方法映射到事件的参数。 该值可以为`true`或`false`。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可见性

此属性用于更改的类、 方法或属性的可见性。 例如，可能有必要将提升`protected`Java 方法，以便相应 C# 包装是`public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml 和 EnumMethods.xml

在其中 Android 库使用整数常量表示传递给属性或方法的库的状态的情况下。 在许多情况下，最好将这些整数常量绑定到 C# 中的枚举。 若要简化此映射，使用**EnumFields.xml**和**EnumMethods.xml**绑定项目中的文件。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>定义使用 EnumFields.xml 枚举

**EnumFields.xml**文件包含 Java 之间的映射`int`常量和 C# `enums`。 让我们正在为一组创建的 C# 枚举下例`int`常量： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

此处我们执行了的 Java 类`SKRealReachSettings`和定义调用的 C# 枚举`SKMeasurementUnit`命名空间中`Skobbler.Ngx.Map.RealReach`。 `field`条目定义的 Java 常数的名称 (示例`UNIT_SECOND`)，枚举条目的名称 (示例`Second`)，并且这两个实体所表示的整数值 (示例`0`)。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>定义使用 EnumMethods.xml 的 Getter/Setter 方法

**EnumMethods.xml**文件允许从 Java 更改方法参数和返回类型`int`向 C# 常量`enums`。 换而言之，它将映射的读取和写入的 C# 枚举 (中定义**EnumFields.xml**文件) 与 Java`int`常量`get`和`set`方法。

给定`SKRealReachSettings`上面以下定义枚举**EnumMethods.xml**文件会定义 getter/setter 此枚举：

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一个`method`行映射的返回值的 Java`getMeasurementUnit`方法`SKMeasurementUnit`枚举。 第二个`method`行映射的第一个参数`setMeasurementUnit`到相同的枚举。

所有这些更改后，你可以使用以下代码在 Xamarin.Android 中设置`MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>总结

本文讨论了如何 Xamarin.Android 使用元数据转换中的 API 定义*Google* *AOSP 格式*。 涵盖还可能有的更改后使用*Metadata.xml*它会检查重命名成员时遇到的限制，它提供的受支持的 XML 属性，描述应在何时使用每个属性的列表。



## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [GAPI 元数据](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
