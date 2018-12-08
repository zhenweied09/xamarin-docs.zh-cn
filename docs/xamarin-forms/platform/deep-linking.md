---
title: 应用程序索引和深层链接
description: 本文介绍如何使用应用程序的索引和深层链接可供搜索 iOS 和 Android 设备上的 Xamarin.Forms 应用程序内容。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: c4e634ce51080ad38b093e1355767c73c72e837a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059621"
---
# <a name="application-indexing-and-deep-linking"></a>应用程序索引和深层链接

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)

_应用程序索引，否则将几个使用通过出现在搜索结果中了解相关信息后忘记的应用程序。深层链接允许应用程序响应通过导航到从深层链接引用的页面通常包含应用程序数据的搜索结果。本文介绍如何使用应用程序的索引和深层链接可供搜索 iOS 和 Android 设备上的 Xamarin.Forms 应用程序内容。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**深度通过链接使用 Xamarin.Forms 和 Azure， [Xamarin 学院课程](https://university.xamarin.com/)**


Xamarin.Forms 应用程序索引和深层链接提供一个用于 API 的应用程序索引在用户浏览应用程序通过发布元数据。 使用 Spotlight 搜索，Google 搜索或 web 搜索，则可以为搜索创建索引的内容。 点击搜索结果，其中包含深层链接就会触发事件的应用程序，可以处理和通常用于导航到从深层链接引用的页。

示例应用程序演示了其中的数据存储在本地 SQLite 数据库中，Todo 列表应用程序，如以下屏幕截图中所示：

![](deep-linking-images/screenshots.png "TodoList 应用程序")

每个`TodoItem`索引由用户创建的实例。 然后可以使用特定于平台的搜索来查找该应用程序中的索引的数据。 当用户点击该应用程序的搜索结果项上时，启动该应用程序时，`TodoItemPage`导航到，和`TodoItem`引用从深层链接显示。

有关使用 SQLite 数据库的详细信息，请参阅[Xamarin.Forms 本地数据库](~/xamarin-forms/app-fundamentals/databases.md)。

> [!NOTE]
> Xamarin.Forms 应用程序索引和深度链接功能仅适用于 iOS 和 Android 平台，iOS 9 和 API 23 至少分别需要。

## <a name="setup"></a>安装

以下部分提供在 iOS 和 Android 平台上使用此功能的任何其他安装说明。

### <a name="ios"></a>iOS

在 iOS 平台上，确保你的 iOS 平台项目设置**Entitlements.plist**文件作为对捆绑进行签名的自定义权利文件。

若要使用 iOS 通用链接：

1. 使用添加到应用中，关联域授权`applinks`密钥，包括所有域将支持您的应用程序。
1. 将 Apple 应用站点关联文件添加到你的网站。
1. 添加`applinks`键到 Apple 应用站点关联文件。

有关详细信息，请参阅[允许应用程序和网站链接到你内容](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content)developer.apple.com 上。

### <a name="android"></a>Android

在 Android 平台上有多个要使用应用程序索引和深度链接功能，必须满足的先决条件：

1. 你的应用程序的版本必须是实时 Google Play 上。
1. 必须针对 Google 的开发人员控制台中的应用程序注册的配套网站。 应用程序与网站相关联后，可以是 Url 编制索引的网站和应用程序，然后在搜索结果中提供服务的工作。 有关详细信息，请参阅[应用对 Google 搜索的索引编制](https://support.google.com/googleplay/android-developer/answer/6041489)Google 的网站上。
1. 你的应用程序必须支持上的 HTTP URL 意向`MainActivity`类，它告知应用程序的索引编制哪些类型的数据的 URL 方案的应用程序可以响应。 有关详细信息，请参阅[配置的意向筛选器](~/android/platform/app-linking.md#configure-intent-filter)。

一旦满足这些先决条件，需要以下其他安装程序才能使用 Xamarin.Forms 应用程序索引和 Android 平台上的深层链接：

1. 安装[Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/)到 Android 应用程序项目的 NuGet 包。
1. 在中**MainActivity.cs**文件中，添加一个声明，使用`Xamarin.Forms.Platform.Android.AppLinks`命名空间。
1. 在中**MainActivity.cs**文件中，添加一个声明，使用`Firebase`命名空间。
1. 在 web 浏览器中，创建一个新项目通过[Firebase 控制台](https://console.firebase.google.com/)。
1. 在 Firebase 控制台中，将 Firebase 添加到 Android 应用，并输入所需的数据。
1. 下载得到**google-services.json**文件。
1. 添加**google-services.json**文件到 Android 项目的根目录，然后设置其**生成操作**到**GoogleServicesJson**。
1. 在中`MainActivity.OnCreate`重写中，添加以下下面的代码行`Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

当**google-services.json**添加到项目 (并*GoogleServicesJson** 生成操作设置)，生成过程中提取客户端 ID 和 API 密钥，然后添加到这些凭据生成清单文件。

有关详细信息，请参阅[深层链接内容与 Xamarin.Forms URL 导航](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)Xamarin 博客上。

## <a name="indexing-a-page"></a>索引页

索引页，并将其公开到 Google 和 Spotlight 搜索的过程如下所示：

1. 创建[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)包含索引页面中，并将其返回到页中，当用户在搜索结果中选择创建索引的内容的深层链接所需的元数据。
1. 注册[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)实例要搜索的索引。

下面的代码示例演示如何创建[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)实例：

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)实例包含多个属性的值和所需索引页创建的深层链接。 [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title)， [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description)，以及[ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail)属性用于标识创建索引的内容时出现在搜索结果中。 [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)属性设置为`true`以指示当前正在查看创建索引的内容。 [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri)属性是`Uri`包含返回到当前页，并显示当前所需的信息`TodoItem`。 下面的示例演示一个示例`Uri`的示例应用程序：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

这`Uri`包含启动所需的所有信息`deeplinking`应用程序中，导航到`DeepLinking.TodoItemPage`，并显示`TodoItem`具有`ID`为 2。

## <a name="registering-content-for-indexing"></a>注册为进行索引的内容

一次[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)创建实例，必须将它注册为索引才会显示在搜索结果中。 这通过实现[ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry))方法，如下面的代码示例中所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

这将添加[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)应用程序的实例[ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks)集合。

> [!NOTE]
> `RegisterLink`方法还可以用于更新已编制索引的页面的内容。

一次[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)实例具有已注册为编制索引，它可以出现在搜索结果中。 下面的屏幕截图显示了出现在 iOS 平台上的搜索结果中创建索引的内容：

![](deep-linking-images/ios-search.png "在 iOS 上的搜索结果中创建索引的内容")

## <a name="de-registering-indexed-content"></a>取消注册编制索引的内容

[ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry))方法用于从搜索结果中，删除创建索引的内容如下面的代码示例中所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

此操作将删除[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)从应用程序的实例[ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks)集合。

> [!NOTE]
> 在 Android 上不能从搜索结果中移除创建索引的内容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>响应的深层链接

当创建索引的内容将出现在搜索结果和用户，选择`App`类的应用程序将收到要处理的请求`Uri`包含在创建索引的内容。 可以在处理此请求[ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri))重写，如下面的代码示例中所示：

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

[ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri))方法会检查收到`Uri`适用于应用程序，分析之前`Uri`到页后，可以导航到并使用参数来传递到页。 要进行创建后，导航到的页的实例和`TodoItem`表示参数检索到的页。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)要导航到的页的设置为`TodoItem`。 这可确保，当`TodoItemPage`情况下将显示[ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))方法，它将显示`TodoItem`其`ID`深层链接中包含。

## <a name="making-content-available-for-search-indexing"></a>使内容可供搜索索引

显示通过深层链接所代表的页，则每次[ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)属性设置为`true`。 IOS 和 Android 上，这使得[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)为搜索编制索引，并仅在 iOS 上可用的实例，它还使`AppLinkEntry`实例可用于切换。 有关切换的详细信息，请参阅[简介切换](~/ios/platform/handoff.md)。

下面的代码示例演示了如何设置[ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)属性设置为`true`中[ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写：

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

同样，通过深层链接所代表的页导航离开， [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive)属性设置为`false`。 在 iOS 和 Android 上，这会停止[ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry)实例正被播发的搜索索引，并在 iOS 唯一，it 也停止公布自己`AppLinkEntry`移交的实例。 可以在完成此[ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)重写，如下面的代码示例中所示：

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>切换到提供数据

在 iOS 上，索引页时，可以存储特定于应用程序的数据。 这通过将数据添加到实现[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)回收，这是`Dictionary<string, string>`中切换存储使用的键 / 值对。 切换是为用户启动一个其设备上的一个活动并在其设备的另一台继续该活动 （如由用户的 iCloud 帐户标识） 的方法。 下面的代码显示了存储特定于应用程序的键 / 值对的示例：

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

中存储的值[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)集合将存储在索引页上，为元数据，并且将在用户点击搜索结果，其中包含深层链接 （或切换用于在另一台查看内容时还原登录的设备）。

此外，可以指定以下项的值：

- `contentType` – `string` ，指定创建索引的内容的统一类型标识符。 推荐的约定要用于此值是页的包含创建索引的内容的类型名称。
- `associatedWebPage` – `string` ，表示要访问如果创建索引的内容也可以查看在 web 上，或该应用程序支持 Safari 的深层链接的网页。
- `shouldAddToPublicIndex` –`string`的任一`true`或`false`，它控制是否将创建索引的内容添加到 Apple 的公有云的索引，然后向尚未在其 iOS 设备安装应用程序用户提供。 但是，只是因为内容已设置为公共编制索引，它并不意味着，它将自动添加到 Apple 的公有云索引。 有关详细信息，请参阅[公共搜索索引](~/ios/platform/search/nsuseractivity.md)。 请注意，应将此项设置为`false`添加到个人数据时[ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues)集合。

> [!NOTE]
> `KeyValues`集合不 Android 平台上使用。

有关切换的详细信息，请参阅[简介切换](~/ios/platform/handoff.md)。

## <a name="summary"></a>总结

本文介绍如何使用应用程序的索引和深层链接可供搜索 iOS 和 Android 设备上的 Xamarin.Forms 应用程序内容。 应用程序索引允许应用程序通过出现在搜索结果，否则会在几个使用后忘记有关了解相关信息。

## <a name="related-links"></a>相关链接

- [深层链接 （示例）](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [iOS 搜索 Api](~/ios/platform/search/index.md)
- [在 Android 6.0 中将应用链接](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
