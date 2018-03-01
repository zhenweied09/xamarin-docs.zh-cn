---
title: "Android 资源基础知识"
ms.topic: article
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: c8f6832f618c37b3593f28c8efaeb87e4df5df03
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="android-resource-basics"></a>Android 资源基础知识

几乎所有的 Android 应用程序将在它们; 具备某种形式的资源至少它们通常具有用户界面布局中的 XML 文件的形式。 当首次创建 Xamarin.Android 应用程序时，默认资源将会通过 Xamarin.Android 项目模板的安装程序：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![资源文件](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![资源文件](android-resource-basics-images/01-resource-files-xs.png)
 
-----

在资源文件夹中创建的五个文件组成的默认资源：

-  **Icon.png** &ndash;应用程序的默认图标

-  **Main.axml** &ndash;应用程序的默认用户界面布局文件。 请注意，Android 时使用**.xml**文件扩展名 Xamarin.Android 使用**.axml**文件扩展名。

-  **Strings.xml** &ndash;字符串表来帮助进行本地化的应用程序

-  **AboutResources.txt** &ndash;这不是必需的并且可以安全地删除。 它只是提供资源文件夹和文件的高级概述。

-  **Resource.designer.cs** &ndash;此文件是自动生成并由 Xamarin.Android 和保留唯一维护 ID 分配给每个资源。 这是非常类似和与 Android 应用程序用 Java 编写者必须 R.java 文件目的相同。 它将自动创建的 Xamarin.Android 工具，并将不时地重新生成。

<a name="Creating_and_Accessing_Resources" />

## <a name="creating-and-accessing-resources"></a>创建和访问资源

创建资源非常简单，将文件添加到所涉及的资源类型的目录。 下面的屏幕快照显示为德语的区域设置的字符串资源已添加到项目。 当**Strings.xml**已添加到文件，**生成操作**自动设置为**AndroidResource** Xamarin.Android 工具：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![生成 Strings.xml 设置为 AndroidResource 的操作](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![生成 Strings.xml 设置为 AndroidResource 的操作](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

这样，要正确编译并将嵌入到 APK 文件中的资源的 Xamarin.Android 工具。 如果由于某种原因**生成操作**未设置为**Android 资源**、 从 APK 中，然后将排除文件和任何尝试加载或访问的资源将导致运行时错误和应用程序将崩溃。

此外，务必请注意，虽然 Android 资源项仅支持小写的文件名，但是 Xamarin.Android 更 forgiving;它将支持大写和小写的文件名。 映像名称的约定是使用小写带下划线作为分隔符 (例如，**我\_映像\_name.png**)。 请注意，是否使用短划线或空格作为分隔符，也不能处理资源名称。

资源已添加到项目后，有两种方法在应用程序中使用它们&ndash;以编程方式 （在代码） 或从 XML 文件。

<a name="Referencing_Resources_Programmatically" />

## <a name="referencing-resources-programmatically"></a>以编程方式引用的资源

若要以编程方式访问这些文件，它们分配一个唯一的资源 id。 此资源 ID 是在一个名为的特殊类中定义的整数`Resource`，这在文件中找到**Resource.designer.cs**，并查找类似如下内容：

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

对应于资源类型的嵌套类中包含每个资源 ID。 例如，当文件**Icon.png**已添加到项目中，更新 Xamarin.Android`Resource`类，创建嵌套的类调用`Drawable`常量的内部使用名为`Icon`。
这样可以使文件**Icon.png**作为代码中引用`Resource.Drawable.Icon`。 `Resource`类不应进行手动编辑，如 Xamarin.Android 将重写对其进行了任何更改。

引用时以编程方式 （在代码中） 的资源，它们可以访问通过使用以下语法的资源类层次结构中：

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash;了提供资源和仅的包时需要其他包中的资源正在使用。

-  **ResourceType** &ndash;这是在上面所述的资源类内的嵌套的资源类型。

-  **资源名称**&ndash;这是第 （不含扩展名） 的资源的文件名或某个 XML 元素中的资源使用 android: name 属性的值。

<a name="Referencing_Resources_from_XML" />

## <a name="referencing-resources-from-xml"></a>从 XML 中引用的资源

XML 文件中的资源通过以下特殊语法：

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash;了提供资源和仅的包时需要其他包中的资源正在使用。

-  **ResourceType** &ndash;这是在资源类内的嵌套的资源类型。

-  **资源名称**&ndash;它是资源的文件名 (*而无需*文件类型扩展名) 的值或`android:name`的资源在一个 XML 元素的属性。

例如布局文件，内容**main.axml**，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

此示例具有[ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview)需要可绘制资源名称为**标志**。 `ImageView`具有其`src`属性设置为 **@drawable/flag** 。 当活动开始时，Android 看在目录内**资源/Drawable**的名为的文件**flag.png** (文件扩展名可能是另一个图像格式，如**flag.jpg**)加载此文件，并将其显示在`ImageView`。
当运行此应用程序时，它将看起来如下图所示：

![本地化的 ImageView](android-resource-basics-images/03-localized-screenshot.png)

