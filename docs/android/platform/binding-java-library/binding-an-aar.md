---
title: 绑定。AAR
description: 本演练提供有关从 Android 创建 Xamarin.Android Java 绑定库的分步说明。AAR 文件。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 101fb28add97749549de9c44292a1ef99a717dde
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="binding-an-aar"></a>绑定。AAR

_本演练提供有关从 Android 创建 Xamarin.Android Java 绑定库的分步说明。AAR 文件。_


## <a name="overview"></a>概述

*Android 存档 (。AAR)*文件是 Android 库的文件格式。
一个。AAR 文件。包含以下的 ZIP 存档：

-   已编译的 Java 代码
-   资源 Id
-   资源
-   元数据 （例如，活动声明，权限）

在本指南中，我们将逐步了解创建单个绑定库的基础知识。AAR 文件。 有关 Java 库会通常绑定 （使用基本代码示例） 的概述，请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。


> [!IMPORTANT]
> 绑定项目只能包含一个。AAR 文件。 如果。在其他 AAR 依赖关系。应包含在其自己绑定项目，然后引用 AAR，则这些依赖关系。 请参阅[Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。


## <a name="walkthrough"></a>演练

我们将创建绑定库，Android 存档文件，例如，已创建在 Android Studio 中， [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)。 这。AAR 包含`TextCounter`包含元音和字符串中的辅音字母的次数计数的静态方法的类。 此外， **textanalyzer.aar**包含图像资源来帮助显示计数的结果。

我们将使用以下步骤来创建从一个绑定库。AAR 文件：

1. 创建新的 Java 绑定库项目。

2. 添加一个。AAR 文件复制到项目。 绑定项目可能只包含单个。AAR。

3. 设置为相应的生成操作。AAR 文件。

4. 选择目标框架。AAR 支持。

5. 构建绑定库。

一旦我们已创建了绑定库，我们将开发小型的 Android 应用程序提示用户输入的文本字符串，调用。AAR 方法分析文本，将检索从映像。AAR，并显示随映像一起的结果。

此示例应用程序将访问`TextCounter`类**textanalyzer.aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

此外，此示例应用程序将检索并显示在中打包的图像资源**textanalyzer.aar**:

[![Xamarin monkey 映像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此映像资源位于**res/drawable/monkey.png**中**textanalyzer.aar**。



### <a name="creating-the-bindings-library"></a>创建绑定库

在开始通过下列步骤之前, 请下载该示例[textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 存档文件：

1.  创建新的绑定库项目开头 Android 绑定库模板。 你可以使用适用于 Mac 的 Visual Studio 或 Visual Studio （以下屏幕截图显示 Visual Studio 中，但 Visual Studio for Mac 非常相似）。 将解决方案命名**AarBinding**:

    [![创建 AarBindings 项目](binding-an-aar-images/01-new-bindings-library-vs-sml.png)](binding-an-aar-images/01-new-bindings-library-vs.png#lightbox)

2.  该模板包括**Jar**文件夹，你在其中添加你。AAR(s) 到绑定库项目。 右键单击**Jar**文件夹，然后选择**添加 > 现有项**:

    [![添加现有项](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  导航到**textanalyzer.aar**前面下载文件，选择它，然后单击**添加**:

    [![添加 textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  验证**textanalyzer.aar**文件已成功添加到项目：

    [![添加了 textanalyzer.aar 文件](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  设置的生成操作**textanalyzer.aar**到`LibraryProjectZip`。 在适用于 Mac 的 Visual Studio，右键单击**textanalyzer.aar**设置生成操作。 在 Visual Studio 中，可以设置生成操作**属性**窗格中):

    [![将 textanalyzer.aar 生成操作设置为 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  打开项目属性来配置*目标框架*。 如果。AAR 使用 Android 的任何 Api，将目标框架设置为 API 级别。AAR 要求。 (有关的目标框架设置和常规中的 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。)

    为你绑定的库设置目标 API 级别。 在此示例中，我们需要任意使用最新的平台 API 级别 （API 级别 23），因为我们**textanalyzer** Android Api 上没有依赖关系：

    [![将目标级别设置为 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  构建绑定库。 绑定类库项目应能成功生成和生成的输出。在以下位置的 DLL: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>使用绑定库

若要使用这种情况。在 Xamarin.Android 应用程序的 DLL，你必须首先添加到绑定库的引用。 使用以下步骤：

1.  我们在与要简化本演练中的绑定库位于同一解决方案中创建此应用程序。 （使用应用程序绑定库也可以驻留在另一种解决方案。）创建一个新的 Xamarin.Android 应用程序： 右键单击解决方案并选择**添加新项目**。 将新项目**命名为 BindingTest**:

    [![创建新的命名为 BindingTest 项目](binding-an-aar-images/07-add-new-project-vs-sml.png)](binding-an-aar-images/07-add-new-project-vs.png#lightbox)

2.  右键单击**引用**节点**命名为 BindingTest**项目，然后选择**添加引用...**:

    [![单击添加引用](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  选择**AarBinding**前面创建的项目，然后单击**确定**:

    [![检查 AAR 绑定项目](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  打开**引用**节点**命名为 BindingTest**项目以便确认**AarBinding**引用不存在：

    [![AarBinding 下列出的引用](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


如果你想要查看绑定库项目的内容，你可以双击要打开该文件的引用**对象浏览器**。 你可以查看该映射的内容`Com.Xamarin.Textcounter`命名空间 (从 Java 映射`com.xamarin.textanalyzezr`包)，您可以查看的成员`TextCounter`类：

[![查看对象浏览器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上面的屏幕截图突出显示两个`TextAnalyzer`示例应用程序将调用的方法： `NumConsonants` (其包装基础 Java`numConsonants`方法)，和`NumVowels`(其包装基础 Java`numVowels`方法)。



### <a name="accessing-aar-types"></a>访问。AAR 类型

添加对您指向绑定库的应用程序的引用后，你可以访问中的 Java 类型。为你的 AAR 会访问 C# 类型 （得益于 C# 包装）。 C# 应用程序代码可以调用`TextAnalyzer`方法在此示例中所示：

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上面的示例中，我们正在呼叫静态方法，`TextCounter`类。 但是，你还可以实例化类并调用实例方法。 例如，如果你。AAR 包装一个名为类`Employee`具有实例方法`buildFullName`，你可以实例化`MyClass`和此处使用它所示：

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

以下步骤将代码添加到应用程序，以便它会提示用户输入文本，使用`TextCounter`分析文本，然后显示结果。

替换**命名为 BindingTest**布局 (**main.axml**) 使用以下 XML。 此布局有`EditText`文本输入和适用于启动元音标记和辅音字母计数的两个按钮：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

内容替换**MainActivity.cs**替换为以下代码。 在此示例中所示，按钮事件处理程序调用包装`TextCounter`驻留在的方法。若要显示的结果的 AAR 并使用 toast。 请注意`using`命名空间的绑定的库语句 (在这种情况下， `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

编译和运行**命名为 BindingTest**项目。 应用将启动并显示在左侧的屏幕截图 (`EditText`初始化与一些文本，但你可以点击它以更改它)。 当你点击**计数元音**，toast 显示元音数，在右侧所示：

[![从运行命名为 BindingTest 的屏幕截图](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

请尝试点击**计数的辅音字母**按钮。 此外，你可以修改的文本行并点击这些按钮再次要测试不同元音标记和辅音字母计数。



### <a name="accessing-aar-resources"></a>访问。AAR 资源

Xamarin 工具合并**R**中的数据。到你的应用的 AAR**资源**类。 因此，你可以访问。AAR 资源从你的布局 （和从代码隐藏） 中的相同方式将访问中的资源**资源**的你的项目的路径。

若要访问的图像资源，你可以使用**Resource.Drawable**命名为映像打包内。AAR。 例如，你可以引用**image.png**中。通过使用 AAR 文件`@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

你也可以访问驻留在的资源布局。AAR。 若要执行此操作，请使用**Resource.Layout**中打包的布局的名称。AAR。 例如：

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer.aar**示例包含图像文件驻留在**res/drawable/monkey.png**。 让我们访问此图像资源，并使用我们的示例应用程序：

编辑**命名为 BindingTest**布局 (**main.axml**) 并添加`ImageView`到末尾`LinearLayout`容器。 这`ImageView`显示的图像位于 **@drawable/monkey**; 此映像将加载的资源部分从**textanalyzer.aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

编译和运行**命名为 BindingTest**项目。 应用将启动并显示在左侧的屏幕截图&ndash;当点击**计数的辅音字母**，结果将显示在右侧所示：

[![命名为 BindingTest 显示辅音计数](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


祝贺你！ 你已成功绑定 Java 库。AAR ！



## <a name="summary"></a>总结

在本演练中，我们将创建用于绑定库。AAR 文件，将绑定库添加到最小化测试应用，并运行应用程序来验证我们 C# 代码可以调用驻留在的 Java 代码。AAR 文件。
此外，我们扩展应用程序访问和显示驻留在图像资源。AAR 文件。


## <a name="related-links"></a>相关链接

- [生成 Java 绑定库 （视频）](https://university.xamarin.com/classes#10090)
- [绑定 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [AarBinding （示例）](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Bug 44573 一个项目不能绑定多个.aar 文件](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
