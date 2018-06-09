---
title: 配置管理
description: 本章介绍 eShopOnContainers 移动应用程序如何实现配置管理来提供应用程序设置和用户设置。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6cd9771760bc2932345fec24887842ce1c47376
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243946"
---
# <a name="configuration-management"></a>配置管理

设置允许分离数据，用于配置从代码中，应用程序中的行为允许更改而不重新生成应用程序的行为。 有两种类型的设置： 应用程序设置和用户设置。

应用设置是应用程序创建和管理的数据。 它可以包括如固定的 web 服务终结点、 API 密钥和运行时状态数据。 应用程序设置绑定到的应用程序存在并且仅对该应用程序有意义。

用户设置均影响应用的行为，并且不需要频繁重新调整的应用程序的可自定义设置。 例如，应用程序可能允许用户指定在何处检索数据，以及如何在屏幕上显示。

Xamarin.Forms 包括一个持久的字典，其中可以用于存储设置数据。 可以使用访问此字典[ `Application.Current.Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/)属性，以及将被放入它的任何数据时，保存应用程序将进入睡眠状态，并且应用程序恢复或重新启动时还原。 此外， [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)类还具有[ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)允许应用具有保存在需要时它设置的方法。 有关此字典的详细信息，请参阅[属性字典](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary)。

使用 Xamarin.Forms 持久字典来存储数据一个缺点是，它不是轻松地绑定到数据。 因此，eShopOnContainers 移动应用程序使用 Xam.Plugins.Settings 库，可从[NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)。 此库提供了一种一致的、 类型安全、 跨平台的方法，用于保存和检索应用程序和用户设置时使用提供的每个平台的本机设置管理。 此外，很方便地使用数据绑定来访问由库公开的设置数据。

> [!NOTE]
> 虽然 Xam.Plugin.Settings 库可以存储应用程序和用户设置，但将提高这两者之间没有区别。

## <a name="creating-a-settings-class"></a>创建一个设置类

当使用 Xam.Plugins.Settings 库，单个静态类应创建的将包含所需的应用程序的应用程序和用户设置。 下面的代码示例演示 eShopOnContainers 移动应用程序中的设置类：

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

设置可以读取和写入通过`ISettings`Xam.Plugins.Settings 库提供的 API。 此库提供可用来访问 API，是 singleton `CrossSettings.Current`，和应用的设置类应公开通过此单一实例`ISettings`属性。

> [!NOTE]
> 使用适用于 Plugin.Settings 和 Plugin.Settings.Abstractions 命名空间指令应添加到需要访问 Xam.Plugins.Settings 库类型的类。

## <a name="adding-a-setting"></a>添加设置

每个设置包括密钥、 默认值和属性。 下面的代码示例演示为表示 eShopOnContainers 移动应用程序连接到的联机服务的基 URL 的用户设置的所有三个项：

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

密钥始终是一个定义的密钥名称的常量字符串，该设置的默认值由所需类型的静态只读值。 提供默认值可确保有效的值是检索取消设置的设置才可用。

`UrlBase`静态属性使用两种方法从`ISettings`API 以读取或写入的设置值。 `ISettings.GetValueOrDefault`方法用于检索特定于平台的存储设置的值。 如果设置为不定义了任何值，而被检索其默认值。 同样，`ISettings.AddOrUpdateValue`方法用于保存到特定于平台的存储设置的值。

而是定义内的默认值`Settings`类，`UrlBaseDefault`字符串获取其值从`GlobalSetting`类。 下面的代码示例演示`BaseEndpoint`属性和`UpdateEndpoint`中此类方法：

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

每次`BaseEndpoint`设置属性，`UpdateEndpoint`调用方法。 此方法将更新的属性，它们都基于一系列`UrlBase`由提供的用户设置`Settings`表示不同 eShopOnContainers 移动应用程序连接到的终结点的类。

## <a name="data-binding-to-user-settings"></a>数据绑定到用户设置

在 eShopOnContainers 移动应用中，`SettingsView`公开两个用户设置。 这些设置允许应用程序是否从 Docker 容器作为部署的微服务应检索数据或应用程序是否应检索数据从 mock 不需要 internet 连接的服务配置。 在选择从容器化微服务中检索数据，必须指定微服务的终结点基 URL。 图 7-1 显示`SettingsView`时用户已选择从容器化微服务中检索数据。

![](configuration-management-images/settings-endpoint.png "由 eShopOnContainers 移动应用程序公开的用户设置")

**图 7-1**： 公开 eShopOnContainers 移动应用程序的用户设置

数据绑定可以用于检索和设置由公开`Settings`类。 这通过视图绑定到视图模型属性，反过来进行访问中的属性上的控件实现`Settings`类，并且引发属性更改通知，如果设置值已更改。 有关如何 eShopOnContainers 移动应用程序构造视图信息建模和将其关联到视图，请参阅[自动向视图模型定位器创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

下面的代码示例演示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件从`SettingsView`，它允许用户输入容器化微服务的终结点基 URL:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

这[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件绑定到`Endpoint`属性`SettingsViewModel`类，使用双向绑定。 下面的代码示例演示终结点属性：

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

当`Endpoint`属性设置`UpdateEndpoint`方法调用，前提是所提供的值是有效，且属性更改引发通知。 下面的代码示例演示`UpdateEndpoint`方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

此方法更新`UrlBase`中的属性`Settings`类终结点基 URL 值输入用户，这会导致它能持久保存到特定于平台的存储。

当`SettingsView`导航到，`InitializeAsync`中的方法`SettingsViewModel`执行类。 下面的代码示例演示此方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法设置`Endpoint`属性的值`UrlBase`中的属性`Settings`类。 访问`UrlBase`属性会导致 Xam.Plugins.Settings 库以检索特定于平台的存储设置值。 有关如何信息`InitializeAsync`调用方法，请参阅[将参数传递期间导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

此机制可确保用户导航到 SettingsView，每当用户设置都从特定于平台的存储中检索并显示通过数据绑定。 然后，如果用户更改的设置值，数据绑定来确保它们立即保存回特定于平台的存储。

## <a name="summary"></a>总结

设置允许分离数据，用于配置从代码中，应用程序中的行为允许更改而不重新生成应用程序的行为。 应用设置是应用程序创建和管理的数据和用户设置均影响应用的行为，并且不需要频繁重新调整的应用程序的可自定义设置。

Xam.Plugins.Settings 库提供了一致，类型安全，用于保存和检索应用程序和用户设置和数据绑定的跨平台方法可以用于访问与以下库创建的设置。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
