---
title: "使用 Android 清单"
ms.topic: article
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: aa2d2ce6cabe9c394b9807ca3d6328da5b4ba311
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-the-android-manifest"></a>使用 Android 清单


## <a name="overview"></a>概述

**AndroidManifest.xml**是 Android 平台，可用于描述的功能和到 Android 应用程序要求中的功能强大文件。 但是，使用它并不容易。 Xamarin.Android 有助于让你可以向你的类，然后将用于自动生成你的清单中添加自定义特性来尽量减少这一问题。 我们的目标是 99%的我们的用户应从不需要手动修改**AndroidManifest.xml**。 

**AndroidManifest.xml**作为生成过程中，并在中找到的 XML 的一部分生成**Properties/AndroidManifest.xml**合并在一起生成从自定义特性的 XML。 生成合并**AndroidManifest.xml**驻留在**obj**子目录; 例如，它驻留在**obj/Debug/android/AndroidManifest.xml**为调试版本. 合并过程很简单： 它使用在代码中的自定义特性来生成 XML 元素和*插入*到这些元素**AndroidManifest.xml**。 



## <a name="the-basics"></a>基本知识

在编译时，程序集扫描的非`abstract`派生自的类[活动](https://developer.xamarin.com/api/type/Android.App.Activity/)并且具有[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/)对其声明属性。 它然后使用这些类和属性生成清单。 例如，考虑以下代码： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

这会导致执行任何操作中正在生成**AndroidManifest.xml**。 如果你想`<activity/>`元素生成，你需要使用[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute)自定义属性： 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

此示例会导致下面的 xml 片段，要添加到**AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]`特性没有任何影响`abstract`类型;`abstract`类型将被忽略。



### <a name="activity-name"></a>活动名称

从 Xamarin.Android 5.1 开始，活动的类型名称取决于正在导出的类型的程序集限定名称的 MD5SUM。 这样，相同的完全限定名称，以提供从两个不同的程序集，无法获取打包错误。 （在 Xamarin.Android 5.1 中之前, 的活动的默认类型名称创建从小写的命名空间和类名称。） 

如果您想要覆盖此默认设置，并显式指定你的活动，使用的名称[ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)属性： 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

该示例产生下面的 xml 片段：

```xml
<activity android:name="awesome.demo.activity" />
```

*请注意*： 应使用`Name`仅出于向后兼容性原因，这种情况下重命名的属性可能会降低在运行时的类型查找。 如果你有需要基于小写的命名空间的活动的默认类型名称和类名称，请参阅的旧代码[Android 可调用包装命名](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)维护兼容性的提示和技巧。 


### <a name="activity-title-bar"></a>活动标题栏

默认情况下，Android，该应用程序的标题栏运行时。 用于此目的的值是[ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大多数情况下，此值将与您的类名称不同。 若要指定你的应用标签的标题栏上，使用[ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/)属性。
例如: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

该示例产生下面的 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>可以从应用程序选择器

默认情况下，你的活动将不会显示在 Android 的应用程序启动器屏幕中。 这是因为将可能有许多活动在你的应用程序，并且你不希望为每个的图标。 若要指定的一列应为可以从应用程序启动程序，使用[ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/)属性。 例如: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

该示例产生下面的 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>活动图标

默认情况下，你的活动指定为由系统提供的默认启动程序图标。 若要使用自定义图标，首先添加你**.png**到**可资源/绘制**，设置为其生成操作**AndroidResource**，然后使用[ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/)属性来指定要使用的图标。 例如: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

该示例产生下面的 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>权限

当将权限添加到 Android 清单 (中所述[将权限添加到 Android 清单](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/))，这些权限记录在**Properties/AndroidManifest.xml**。 例如，如果你设置`INTERNET`权限，下面的元素添加到**Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

调试版本中自动设置某些权限，以使调试变得更容易 (如`INTERNET`和`READ_EXTERNAL_STORAGE`)&ndash;配置这些设置仅在生成**obj/Debug/android/AndroidManifest.xml** ，且无法显示为在中启用**所需的权限**设置。 

例如，如果您检查生成清单文件以在**obj/Debug/android/AndroidManifest.xml**，你可能会看到以下添加的权限元素： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在版本生成的清单的版本 (在**obj/Debug/android/AndroidManifest.xml**)，这些权限是*不*自动配置。 如果你发现，切换到发行版本会导致您的应用程序丢失所做的调试版本中提供的权限，请验证你显式中设置了此权限**所需的权限**设置你的应用程序 （请参阅**生成 > Android 应用程序**Visual Studio 中用于 Mac; 请参阅**属性 > Android 清单**Visual Studio 中)。 




## <a name="advanced-features"></a>高级的功能


### <a name="intent-actions-and-features"></a>意向的操作和功能

Android 清单提供为你用于描述活动的功能的方法。 这是通过[意向](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)和[ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)自定义属性。 你可以指定哪些操作适用于你使用的活动[ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/)构造函数，并且是与相应的类别[ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/)属性。 至少一个活动必须提供 （这是活动提供构造函数中的原因）。 `[IntentFilter]` 可以是提供多次，并且每次使用会导致单独`<intent-filter/>`中的元素`<activity/>`。 例如:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

该示例产生下面的 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>应用程序元素

Android 清单还提供了有助于您声明为整个应用程序的属性。 这是通过`<application>`元素和其对应项，[应用程序](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/)自定义属性。 请注意，这些应用程序级 （程序集范围） 设置，而不是每个活动设置。 通常情况下，声明`<application>`整个应用程序的属性，然后重写这些设置 （如需要） 基于每个活动。 

例如，以下`Application`属性添加到**AssemblyInfo.cs**以指示应用程序可调试，其用户可读名称是**My App**，而且它使用`Theme.Light`为所有活动的默认主题样式： 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

此声明将导致下面的 XML 片段，在生成**obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
在此示例中，应用程序中的所有活动将都默认为`Theme.Light`样式。 如果活动的主题设置为`Theme.Dialog`，仅，活动将使用`Theme.Dialog`样式时应用程序中的所有其他活动将默认为`Theme.Light`中设置的样式`<application>`元素。 

`Application`元素不是配置的唯一办法`<application>`属性。 或者，还可以将直接插入属性`<application>`元素**Properties/AndroidManifest.xml**。 这些设置合并到最终`<application>`驻留在的元素**obj/Debug/android/AndroidManifest.xml**。 请注意的内容**Properties/AndroidManifest.xml**始终替代提供自定义特性的数据。 

有许多应用程序级特性，它们可以在配置`<application>`元素; 有关这些设置的详细信息，请参阅[公共属性](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties)部分[ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>自定义特性列表

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) ： 生成[/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) ： 生成[/清单/应用程序](http://developer.android.com/guide/topics/manifest/application-element.html)XML 片段 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) ： 生成[/清单/检测](http://developer.android.com/guide/topics/manifest/instrumentation-element.html)XML 片段 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) ： 生成[//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) ： 生成[//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) ： 生成[//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) ： 生成[//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) ： 生成[//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) ： 生成[/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) ： 生成[/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) ： 生成[/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) ： 生成[/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) ： 生成[/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) ： 生成[/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段

