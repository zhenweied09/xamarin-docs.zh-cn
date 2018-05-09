---
title: 创建服务
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/03/2018
ms.openlocfilehash: 00785ad161f5f05fd70b059bb0a3f1c8d6c31f97
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="creating-a-service"></a>创建服务

Xamarin.Android 服务必须遵循 Android 服务的两个侵犯的隔离的规则：

* 它们必须扩展[ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/)。
* 它们必须使用修饰[ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)。

Android 服务的另一个要求是必须注册它们**AndroidManifest.xml**和指定唯一的名称。 Xamarin.Android 将自动注册服务清单中在生成时必需的 XML 特性。

此代码片段是在满足以下两个要求的 Xamarin.Android 中创建服务的最简单的示例：  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在编译时，Xamarin.Android 将注册该服务通过将注入到下面的 XML 元素**AndroidManifest.xml** （注意 Xamarin.Android 生成服务的随机名称）：

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

可以通过其他 Android 应用程序与共享服务_导出_它。 这通过设置实现`Exported`属性`ServiceAttribute`。 导出服务时`ServiceAttribute.Name`还应将属性设置以提供服务的有意义的公共名称。 此代码段演示了如何导出和服务：

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml**此服务元素然后将如下所示：

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服务具有其自己的生命周期有创建服务时调用的回调方法。 完全调用的方法取决于服务的类型。 启动的服务必须实现比绑定的服务，不同的生命周期方法，而混合服务必须实现启动的服务和绑定的服务的回调方法。 这些方法是的所有成员`Service`类; 服务的启动方式将确定哪些生命周期方法将调用。 将更高版本中更详细地讨论这些生命周期方法。

默认情况下，服务将在与 Android 应用程序相同的进程中启动。 可以通过设置其自己的进程中启动服务`ServiceAttribute.IsolatedProcess`属性为 true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一步是了解如何启动服务，然后继续研究如何实现三个不同类型的服务。

> [!NOTE]
> 服务在运行在 UI 线程，因此如果任何工作要执行这会阻止 UI，该服务必须使用线程来执行工作。

## <a name="starting-a-service"></a>启动服务

在 Android 中启动服务的最基本方法是以调度`Intent`其中包含可帮助确定应启动的服务的元数据。 有两种不同样式的意向可用来启动服务：

-   **显式意向** &ndash; _显式意向_将确定完全什么服务应该用于完成某个给定的操作。 可以将显式意向看作具有特定地址; 字母Android 将路由到显式标识的服务的意图。 此代码片段是使用显式意向启动一个称为服务的一个示例`DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **隐式意向**&ndash;这种类型的意向松散标识的用户想要执行，但若要完成该操作的确切服务未知的操作。 可以将隐式意向想象为字母是解决"收件人 Whom It 可能问题..."中。
    Android 将检查的意图，内容，并确定是否存在现有服务与匹配意图。

    _意向的筛选器_用来帮助查找与已注册的服务的隐式意图。 意向的筛选器是添加到一个 XML 元素**AndroidManifest.xml**其中包含必要元数据来帮助查找具有为隐式打算的服务。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果 Android 有多个可能为隐式打算匹配，它可能会要求用户选择要处理操作的组件：

![消除歧义对话框的屏幕截图](images/creating-a-service-01.png "消除歧义对话框的屏幕截图")

> [!IMPORTANT]
> 从 Android 5.0 （AP 级别 21） 开始为隐式打算无法用于启动服务。

如果可能，应用程序应使用显式意向来启动服务。 隐式意图不要求提供要启动的特定服务&ndash;这是一个设备上安装某些服务的请求，用于处理该请求。 此不明确的请求可能会导致错误的服务处理请求或不必要地启动 （这会增加在设备上的资源压力） 的其他应用。

意图被调度如何依赖于服务的类型，并且将在特定于每种服务类型的指南的稍后部分详细讨论。


### <a name="creating-an-intent-filter-for-implicit-intents"></a>为隐式意向创建意向的筛选器

若要将服务与隐式意向相关联，Android 应用程序必须提供一些元数据以标识该服务的功能。 此元数据由_意向的筛选器_。 意向的筛选器包含一些信息，如操作或类型的数据，必须存在于试图启动服务。 在 Xamarin.Android，意向的筛选器注册中**AndroidManifest.xml**的修饰具有的服务[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 例如，下面的代码将添加具有一个关联的操作的意向筛选器`com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

这会导致中所包含的条目**AndroidManifest.xml**文件&ndash;与方式类似于下面的示例应用程序一起打包的条目：

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

使用开 Xamarin.Android 服务的基础知识，让我们检查的更多详细信息中的服务不同的子类型。


## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
