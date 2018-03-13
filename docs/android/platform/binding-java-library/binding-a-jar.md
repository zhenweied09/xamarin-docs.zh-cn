---
title: "绑定。JAR"
description: "本演练提供有关从 Android 创建 Xamarin.Android Java 绑定库的分步说明。JAR 文件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bbbf3fb09edb802f1315977fb14ecfe154b2572f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="binding-a-jar"></a>绑定。JAR

_本演练提供有关从 Android 创建 Xamarin.Android Java 绑定库的分步说明。JAR 文件。_

## <a name="overview"></a>概述

Android 社区提供了许多你可能需要在你的应用程序中使用的 Java 库。 这些 Java 库通常打包到中。JAR （Java 存档） 格式，但你可以将打包。JAR 在*Java 绑定库*，以便其功能可供 Xamarin.Android 应用程序。 Java 绑定库的目的是使中的 Api。JAR 文件可供通过自动生成的代码包装的 C# 代码。

Xamarin 工具可以生成绑定库，从一个或多个输入。JAR 文件。 绑定库 (。程序集 DLL） 包含以下项目： 

-   原始内容。JAR 文件。

-   托管可调用包装 (MCW)，这是 C# 类型相应 Java 类型内该换行。JAR 文件。

生成的 MCW 代码使用 JNI （Java 本机接口） 将你的 API 调用为基础。JAR 文件。 可以为任何创建绑定库。最初的目标，才能用于 Android （请注意 Xamarin 工具中当前不支持非 Android Java 库的绑定） 的 JAR 文件。 您还可以选择不包括的内容的情况下生成绑定库。JAR 文件，以便 DLL 具有依赖关系。JAR 在运行时。

在本指南中，我们将逐步了解创建单个绑定库的基础知识。JAR 文件。 我们将使用的所有内容的位置右示例加以说明&ndash;，即没有自定义项或调试的绑定在要求的位置。 
[创建绑定使用元数据](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供更高级的方案，其中绑定进程不是完全自动和手动干预的某些量所需的一个示例。 有关 Java 库会通常绑定 （使用基本代码示例） 的概述，请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。 

 
## <a name="walkthrough"></a>演练

在下面的演练中，我们将创建一个绑定库，[高手](http://square.github.io/picasso/)，常用 Android。提供映像加载和缓存功能的 JAR。 我们将使用以下步骤来将绑定**高手 2.x.x.jar** Xamarin.Android 项目中创建新的.NET 程序集，我们可以使用： 

1. 创建新的 Java 绑定库项目。

2. 添加。JAR 文件复制到项目。

3. 设置为相应的生成操作。JAR 文件。

4. 选择目标框架。JAR 支持。

5. 构建绑定库。

一旦我们已创建了绑定库，我们将开发小型的 Android 应用来演示我们能够调用绑定库中的 Api。 在此示例中，我们想要访问的方法**高手 2.x.x.jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>创建绑定库

在之前开始使用下面的步骤，请下载[高手 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，创建一个新的绑定类库项目。 在 Visual Studio for Mac 或 Visual Studio 中，创建一个新的解决方案，然后选择*Android 绑定库*模板。 （本演练中的屏幕截图使用 Visual Studio 中，但适用于 Mac 的 Visual Studio 是非常类似。）将解决方案命名**JarBinding**: 

[![创建 JarBinding 库项目](binding-a-jar-images/01-new-bindings-library-sml.png)](binding-a-jar-images/01-new-bindings-library.png#lightbox)

该模板包括**Jar**文件夹，你在其中添加你。JAR(s) 到绑定库项目。 右键单击**Jar**文件夹，然后选择**添加 > 现有项**: 

[![添加现有项](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

导航到**高手 2.x.x.jar**前面下载文件，选择它，然后单击**添加**: 

[![选择 jar 文件，然后单击添加](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

验证**高手 2.x.x.jar**文件已成功添加到项目： 

[![Jar 添加到项目](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

当你创建一个 Java 绑定的类库项目时，你必须指定是否。JAR 是嵌入到绑定库中或单独打包。 若要做到这一点，你指定以下项之一*生成操作*: 

-   **EmbeddedJar** &ndash; 。JAR 将嵌入到绑定库中。

-   **InputJar** &ndash; 。JAR 将保留单独绑定库中。

通常情况下，使用**EmbeddedJar**生成操作，以便。JAR 自动打包到绑定库。 这是最简单的选项&ndash;字节中的 Java 代码。JAR 转换为 Dex 字节码，然后 （以及管理可调用包装） 嵌入到你 APK。 如果你想要保留。JAR 分开绑定库，你可以使用**InputJar**选项; 但是，你必须确保。JAR 文件是在运行您的应用程序的设备上可用。 

生成操作设置为**EmbeddedJar**: 

[![选择 EmbeddedJar 生成操作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下来，打开项目属性来配置*目标框架*。 如果。JAR 使用 Android 的任何 Api，将目标框架设置为 API 级别。JAR 要求。 通常情况下，开发人员的。JAR 文件将指示的 API 级别 （或级别） 的。JAR 可与兼容。 (有关的目标框架设置和常规中的 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。)

将目标 API 级别设置为绑定库 （在此示例中，我们将使用 API 级别 19）： 

[![目标 API 级别设置为 API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


最后，生成绑定库。 尽管可能会显示某些警告消息，绑定类库项目应能成功生成和生成的输出。在以下位置的 DLL: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>使用绑定库

若要使用这种情况。DLL 的 Xamarin.Android 应用程序，执行以下操作：

1.  添加到绑定库的引用。

2.  调入。JAR 通过托管的可调用包装器。 

在以下步骤中，我们将创建一个使用绑定库下载并显示的图像的最小应用`ImageView`;"举重"由驻留在的代码。JAR 文件。 

首先，创建一个新的 Xamarin.Android 应用程序使用绑定库。 右键单击解决方案并选择**添加新项目**; 将新项目**命名为 BindingTest**。 我们正在与绑定库位于同一解决方案中创建此应用程序，为了简化本演练中;但是，使用应用程序绑定库无法，相反，驻留在另一种解决方案： 

[![添加新命名为 BindingTest 项目](binding-a-jar-images/07-add-new-project-sml.png)](binding-a-jar-images/07-add-new-project.png#lightbox)

右键单击**引用**节点**命名为 BindingTest**项目，然后选择**添加引用...**:

[![右添加引用](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

检查**JarBinding**前面创建的项目，然后单击**确定**:

[![选择 JarBinding 项目](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

打开**引用**节点**命名为 BindingTest**项目，然后确认**JarBinding**引用不存在： 

[![在引用下显示 JarBinding](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

修改**命名为 BindingTest**布局 (**main.axml**)，使其具有单个`ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

添加以下`using`语句**MainActivity.cs** &ndash;这样便能轻松访问的方法基于 Java 的`Picasso`驻留在绑定库的类：

```csharp
using Com.Squareup.Picasso;
```

修改`OnCreate`方法，以便它使用`Picasso`类从 URL 加载图像并将其显示在`ImageView`: 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

编译和运行**命名为 BindingTest**项目。 应用程序将启动，并小段延迟后 （具体取决于网络条件），它应下载并显示类似于以下屏幕截图的映像：

[![屏幕快照的命名为 BindingTest 运行](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

祝贺你！ 你已成功绑定 Java 库。JAR 并在 Xamarin.Android 应用程序中使用它。
 
 
## <a name="summary"></a>摘要

在本演练中，我们将创建第三方绑定库。JAR 文件，向最小化测试应用程序，添加绑定库，然后运行应用程序来验证我们 C# 代码可以调用驻留在的 Java 代码。JAR 文件。 



## <a name="related-links"></a>相关链接

- [生成 Java 绑定库 （视频）](https://university.xamarin.com/classes#10090)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
