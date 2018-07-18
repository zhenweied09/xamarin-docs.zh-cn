---
title: 在 Android 上的回调
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3f18516643c00dc67fe533ecab00e1f415eb5c46
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922817"
---
# <a name="callbacks-on-android"></a>在 Android 上的回调

从 C# 与 Java 电话是某种程度上*危险业务*。 也就是说没有*模式*的回调从 C# 与 Java; 但是，它是不是我们想要更复杂。

我们将介绍用于执行大多数意义 for Java 的回调的三个选项：

- 抽象类
- 接口
- 虚方法

## <a name="abstract-classes"></a>抽象类

这是最简单的路由的回调，因此我将建议使用`abstract`如果你只尝试获取最简单的窗体中工作的回调。

让我们开始我们想要 Java 实现的 C# 类：

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

以下是为完成此操作的详细信息：

- `[Register]` 生成良好的包名称在 Java-您将收到没有它自动生成的包名称。
- 子类化`Java.Lang.Object`.NET 查询中嵌入的信号，以通过 Xamarin.Android 的 Java 生成器运行类。
- 空的构造函数： 你将想从 Java 代码使用。
- `(IntPtr, JniHandleOwnership)` 构造函数： 是 Xamarin.Android C# 创建的用于-等效的 Java 对象。
- `[Export]` 用信号通知 Xamarin.Android 公开 java 方法。 我们还可以更改方法名称，因为 Java world 喜欢使用大小写的方法。

下一步我们必须测试方案的 C# 方法：

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` 可能是任何类，若要测试这，只要它是`Java.Lang.Object`。

现在，在你的.NET 程序集生成 AAR 上运行.NET 嵌入。 请参阅[入门指南](~/tools/dotnet-embedding/get-started/java/android.md)有关详细信息。

后 AAR 文件导入到 Android Studio 中，让我们来编写单元测试：

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
因此我们：

- 实现`AbstractClass`在 Java 中使用匿名类型
- 确保我们实例返回`"Java"`通过 Java
- 确保我们实例返回`"Java"`从 C#
- 添加`throws Throwable`，因为 C# 构造函数当前标记有 `throws`

如果我们运行此单元测试作为的是，将会失败并出错如：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

缺少了什么下面是`Invoker`类型。 这是的一个子类`AbstractClass`，将转发到 Java C# 调用。 如果 Java 对象进入 C# world 和等效的 C# 类型是抽象的则 Xamarin.Android 自动查找带有后缀的 C# 类型`Invoker`以便在 C# 代码中使用。

Xamarin.Android 使用此`Invoker`模式以及其他用途的 Java 绑定项目。

下面是我们实现`AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

没有在这里，继续相当多的位我们：

- 添加具有后缀的类`Invoker`该子类 `AbstractClass`
- 添加`class_ref`来保存 JNI 引用的 Java 类子类我们的 C# 类
- 添加`id_gettext`保存对 Java JNI 引用`getText`方法
- 包含`(IntPtr, JniHandleOwnership)`构造函数
- 实现`ThresholdType`和`ThresholdClass`Xamarin.Android 若想了解有关详细信息的要求 `Invoker`
- `GetText` 需要查找 Java`getText`使用适当的 JNI 签名的方法，调用它
- `Dispose` 只需清除对引用 `class_ref`

在添加此类和生成新的 AAR 之后, 我们单元测试通过。 正如你所看到的回调此模式不是*理想*，但可行。

有关 Java 互操作的详细信息，请参阅惊人的[Xamarin.Android 文档](~/android/platform/java-integration/working-with-jni.md)有关此主题。

## <a name="interfaces"></a>接口

接口是大致相同抽象类，除了一个详细信息： Xamarin.Android 不会为其生成 Java。 这是因为.NET 嵌入之前, 不存在很多情况下，其中 Java 将实现的 C# 接口。

让我们假设我们有下面的 C# 接口：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` 向.NET 嵌入发出信号，这是一个 Xamarin.Android 接口，但否则做到这一点与相同`abstract`类。

因为 Xamarin.Android 不当前生成此接口的 Java 代码，请向 C# 项目中添加以下 Java:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

你可以将文件放在任何位置，但请务必设置为其生成操作`AndroidJavaSource`。 这将指示.NET 嵌入将其复制到正确的目录获取编译到 AAR 文件。

接下来，`Invoker`实现将完全相同：

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

生成后 AAR 文件，我们可以编写以下的传递单位的 Android Studio 中的测试:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>虚方法

重写`virtual`在 Java 中是可行的但是不更完美的体验。

让我们假定您有下面的 C# 类：

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

如果您遵循`abstract`类上面示例中，它将工作除外一个详细信息： _Xamarin.Android 不会查找`Invoker`_ 。

若要解决此问题，修改 C# 该类是`abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

这并不理想，但它获取了此方案发挥作用。 Xamarin.Android 将选取`VirtualClassInvoker`并且可以使用 Java`@Override`的方法上。

## <a name="callbacks-in-the-future"></a>在将来的回调

有几物联网我们无法以改进了这些方案：

1. `throws Throwable` 在 C# 构造函数固定此[PR](https://github.com/xamarin/java.interop/pull/170)。
1. 请 Java 生成器在 Xamarin.Android 中支持的接口。
    - 这无需添加生成操作的 Java 源文件`AndroidJavaSource`。
1. 使 Xamarin.Android 加载一种方法`Invoker`虚拟类。
    - 因此，无需将标记中的类我们`virtual`示例`abstract`。
1. 生成`Invoker`自动类的.NET 嵌入
    - 这将比较复杂，但可行。 Xamarin.Android 已正在执行与如下内容类似 Java 绑定项目。

大量的工作在这里，完成，但.NET 查询中嵌入这些增强功能还可能有。

## <a name="further-reading"></a>其他阅读材料

* [在 Android 上入门](~/tools/dotnet-embedding/get-started/java/android.md)
* [初步 Android 研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
