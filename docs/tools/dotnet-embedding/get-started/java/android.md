---
title: Android 入门
ms.topic: article
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 713f182e3139933484d3185cc0226bf6758c23c3
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="getting-started-with-android"></a>Android 入门


除了从要求我们[入门 Java](~/tools/dotnet-embedding/get-started/java/index.md)指南还需要：

* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/)或更高版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html) with Java 1.8

作为概述，我们将：

* 创建 C# Android 库项目
* 安装.NET 嵌入通过 NuGet
* 在 Android 库程序集上运行 Embeddinator
* 使用 Android Studio 中的 Java 项目中的生成的 AAR 文件

## <a name="create-an-android-library-project"></a>创建 Android 库项目

打开 Visual Studio for Windows 或 Mac，创建一个新的 Android 类库项目，将其命名为`hello-from-csharp`，并将其保存到`~/Projects/hello-from-csharp`或`%USERPROFILE%\Projects\hello-from-csharp`。

添加名为的一个新的 Android 活动`HelloActivity.cs`后, 跟在 Android 布局`Resource/layout/hello.axml`。

添加新`TextView`到布局，并更改为愉快的内容的文本。

布局源代码应如下所示：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

在你的活动，请确保你调用`SetContentView`与新布局：
```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```
*注意： 不要忘记`[Register]`属性，请参阅下限制的详细信息*

生成项目，生成的程序集将保存在`bin/Debug/hello-from-csharp.dll`。

## <a name="installing-net-embedding-from-nuget"></a>安装 NuGet 从嵌入.NET

选择**添加 > 添加 NuGet 包...**并安装`Embeddinator-4000`从 NuGet 包管理器： ![NuGet 包管理器](android-images/visualstudionuget.png)

这将安装`Embeddinator-4000.exe`到`packages/Embeddinator-4000/tools`目录。

## <a name="run-embeddinator-4000"></a>运行 Embeddinator 4000

我们将添加生成后步骤来运行 Embeddinator 并为 Android 库项目程序集创建本机 AAR 文件。

在适用于 Mac 的 Visual Studio，请转到_项目选项 > 生成 > 自定义命令_并添加_后生成_步骤。

安装以下 commnd:
```
mono '${SolutionDir}/packages/Embeddinator-4000.0.2.0.80/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

> [!NOTE]
> 注意： 请确保使用从 NuGet 安装的版本号

如果想要执行的操作正在进行开发 C# 项目，则还可能添加自定义的命令，以清理`output`之前运行 Embeddinator 目录：
```
rm -Rf '${SolutionDir}/output/'
```

![自定义生成操作](android-images/visualstudiocustombuild.png)

Android AAR 文件将被放入`~/Projects/hello-from-csharp/output/hello_from_csharp.aar`。 _注意： 由于 Java 不支持它包名称中，将替换连字符。_

### <a name="running-net-embedding-on-windows"></a>在 Windows 上嵌入的正在运行.NET

在 Windows 上，我们将实质上是在安装程序的内容相同，但 Visual Studio 中的菜单稍有不同。 Shell 命令也是略有不同的。

转到**项目选项 > 生成事件**并输入到以下**后期生成事件命令行**框：
```
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

如以下屏幕快照：

![在 Windows 上的 Embeddinator](android-images/visualstudiowindows.png)

## <a name="use-the-generated-output-in-an-android-studio-project"></a>在 Android Studio 项目中使用生成的输出

1. 打开 Android Studio 并创建新的项目与**空活动**。
2. 右键单击你`app`模块，然后选择**新建 > 模块**。
3. 选择**导入。JAR /。AAR 包**。
4. 使用目录浏览器查找`~/Projects/hello-from-csharp/output/hello_from_csharp.aar`单击**完成**。

![导入 AAR 到 Android Studio](android-images/androidstudioimport.png)

这会将 AAR 文件复制到一个名为的新模块`hello_from_csharp`。

![Android Studio 项目](android-images/androidstudioproject.png)

若要使用新模块从你`app`，右键单击，然后选择**打开模块设置**。 上**依赖关系**选项卡上，添加新**模块依赖项**选择`:hello_from_csharp`。

![Android Studio 依赖关系](android-images/androidstudiodependencies.png)

在活动中，添加新`onResume`方法，并使用以下代码以启动我们 C# 活动：

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>程序集压缩*重要说明*

一个进一步的更改是必需的.NET 嵌入在 Android Studio 项目。

打开你的应用`build.gradle`文件并添加了以下更改：
```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```
Xamarin.Android 加载当前的.NET 程序集直接从 APK，但需要的程序集。 不会压缩。

如果你没有此安装程序，应用程序将崩溃启动，并将类似于以下打印到控制台：

```csharp
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>运行应用

在启动你的应用程序：

![从 C# 示例模拟器中运行 hello](android-images/hello-from-csharp-android.png)

请注意发生了什么情况此处：

* 我们有了一个 C# 类， `HelloActivity`，该子类 Java
* 我们有 Android 资源文件
* 在 Android Studio 中使用这些通过 Java

因此在此示例正常运行，所有以下都是最终 APK 在安装程序：

* 在启动应用程序上配置 Xamarin.Android
* 中包含的.NET 程序集 `assets/assemblies`
* `AndroidManifest.xml` 修改你的 C# 活动，等等。
* Android 资源和.NET 库中的资产
* [Android 可调用包装器](https://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/android_callable_wrappers/)任何`Java.Lang.Object`子类

如果你要查找的其他演练，请查看本视频中嵌入 Charles Petzold [FingerPaint 演示](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/)Android Studio 项目中此处：

[![适用于 Android 的 Embeddinator 4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 Java 1.8

截至编写此操作，最佳选择是使用 Android Studio 3.0 ([可从此处下载](https://developer.android.com/studio/index.html))。

若要启用你的应用程序模块中的 Java 1.8`build.gradle`文件：
```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
你还可以考虑一下我们 Android Studio 的测试项目[此处](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle)有关详细信息。

如果你想要使用 Android Studio 2.3.x 稳定，必须将启用不推荐使用上插座工具链：
```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>在 Android 上的当前限制

现在如果你子类`Java.Lang.Object`，Xamarin.Android 将生成而不是 Embeddinator Java 存根 （stub） （Android 可调用包装器）。

因此，你必须遵循相同的规则，用于向 Xamarin.Android 作为 Java 导出 C#。

因此，例如在 C# 中：
```csharp
    [Register("mono.embeddinator.android.ViewSubclass")]
    public class ViewSubclass : TextView
    {
        public ViewSubclass(Context context) : base(context) { }

        [Export("apply")]
        public void Apply(string text)
        {
            Text = text;
        }
    }
```

* `[Register]` 映射到所需的 Java 包名称被必需
* `[Export]` 需使方法对 Java 可见

我们可以使用`ViewSubclass`java 如下所示：
```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

阅读更多有关[与 Xamarin.Android 的 Java 集成](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多个程序集

嵌入到单个程序集非常简单;但是，很可能得多，你将有多个一个 C# 程序集。 多次将具有依赖项，例如，Android 支持库或 Google Play 服务的进一步使操作变得复杂的 NuGet 程序包。

这将导致一个难题，因为 Embeddinator 需要将许多类型的文件包含在最终 AAR 如：
* Android 资产
* Android 资源
* Android 本机库
* Android java 源

最有可能不希望将这些文件从 Android 支持库或 Google Play 服务包含在你 AAR，但将在 Android Studio 中使用从 Google 正式的版本。

下面是建议的方法：
* 传递 Embeddinator 你拥有的任何程序集 （具有源），并且想要调用从 Java
* 传递 Embeddinator 需要 Android 资产、 本机库或资源的任何程序集
* 添加 Android 如 Java 依赖项在 Android Studio 中支持库或 Google Play 服务

因此，可能是你的命令：
```
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```
你应该从 NuGet 中排除任何内容，除非你找出包含 Android 资产、 资源、 将需要在 Android Studio 项目等。 你还可以忽略不需要调用从 Java 和链接器的依赖关系_应_包括你的库所需的部件。

若要添加在 Android Studio 中，所需的任何 Java 依赖项你`build.gradle`文件可能如下所示：
```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>其他阅读材料

* [在 Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
* [初步 Android 研究](~/tools/dotnet-embedding/android/index.md)
* [Embeddinator 限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)


## <a name="related-links"></a>相关链接

- [天气示例 (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
