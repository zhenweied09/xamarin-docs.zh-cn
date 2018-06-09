---
title: 应用程序索引和深层链接
description: 本文演示如何使用应用程序索引和深层链接使 Xamarin.Forms 应用程序内容可供搜索 iOS 和 Android 设备上。
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 9cc5177a585af1569385840ab8c370993984ca2b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242487"
---
# <a name="application-indexing-and-deep-linking"></a>应用程序索引和深层链接

_应用程序索引允许将否则忘记后几个使用能够通过在搜索结果中显示相关的应用程序。深层链接允许应用程序响应通过导航到从深层链接引用的页面通常包含应用程序数据的搜索结果。本文演示如何使用应用程序索引和深层链接使 Xamarin.Forms 应用程序内容可供搜索 iOS 和 Android 设备上。_

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**深层链接 Xamarin.Forms 和 Azure，通过[Xamarin 大学](https://university.xamarin.com/)**


Xamarin.Forms 应用程序索引和深层链接提供一个用于 API 为应用程序索引用户浏览应用程序的发布元数据。 在 Spotlight 搜索，在 Google 搜索中，或在 web 搜索，则可以为搜索创建索引的内容。 点击搜索结果包含深层链接将激发的事件可以由应用程序，并且通常用于导航到从深层链接引用的页。

示例应用程序演示其中的数据存储在本地 SQLite 数据库中，一个 Todo 列表应用程序，如以下屏幕截图中所示：

![](deep-linking-images/screenshots.png "TodoList 应用程序")

每个`TodoItem`索引而创建的用户实例。 然后可以使用特定于平台的搜索以查找应用程序中的索引的数据。 当用户点击应用程序的搜索结果项上时，启动应用程序，`TodoItemPage`导航到，和`TodoItem`引用从深层链接将显示。

有关使用 SQLite 数据库的详细信息，请参阅[处理本地数据库](~/xamarin-forms/app-fundamentals/databases.md)。

> [!NOTE]
> Xamarin.Forms 应用程序索引和深层链接功能仅适用于 iOS 和 Android 平台，而且需要 iOS 9 和 API 23 分别。

## <a name="setup"></a>安装

以下部分提供用于在 iOS 和 Android 平台上使用此功能的所有其他安装说明操作。

### <a name="ios"></a>iOS

在 iOS 平台上，没有使用此功能所需的任何其他设置。

### <a name="android"></a>Android

在 Android 平台上，有多个要使用应用程序索引和深层链接功能，必须满足的先决条件：

1. 你的应用程序的版本必须是实时 Google Play 上。
1. 必须针对在 Google 开发人员控制台应用程序注册助理网站。 应用程序与网站相关联后，可以是 Url 索引为该网站和应用程序，然后在搜索结果中提供服务的该工作。 有关详细信息，请参阅[应用索引在 Google 搜索](https://support.google.com/googleplay/android-developer/answer/6041489)Google 网站上。
1. 你的应用程序必须支持 HTTP URL 意向上`MainActivity`类，它告知应用程序的索引的 URL 数据方案的哪些类型的应用程序可以响应。 有关详细信息，请参阅[配置意向筛选器](~/android/platform/app-linking.md#configure-intent-filter)。

一旦满足这些先决条件，需要以下其他安装程序才能使用 Xamarin.Forms 应用程序索引和 Android 平台上的深层链接：

1. 安装[Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/)到 Android 应用程序项目的 NuGet 包。
1. 在`MainActivity.cs`文件中，导入`Xamarin.Forms.Platform.Android.AppLinks`命名空间。
1. 在`MainActivity.OnCreate`重写时，请添加以下行的下方代码`Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

有关详细信息，请参阅[深层链接内容替换 Xamarin.Forms URL 定位](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)Xamarin 博客上。

## <a name="indexing-a-page"></a>索引页

索引页和将其公开到 Google 和 Spotlight 搜索的过程如下所示：

1. 创建[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)包含索引页，以及将其返回到页中，当用户在搜索结果中选择索引的内容的深层链接所需的元数据。
1. 注册[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例编制索引的搜索。

下面的代码示例演示如何创建[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例：

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例包含多个属性的值所需索引页和创建深层链接。 [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Title/)， [ `Description` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Description/)，和[ `Thumbnail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Thumbnail/)属性用于标识创建索引的内容，当它出现在搜索结果。 [ `IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)属性设置为`true`以指示当前正在查看创建索引的内容。 [ `AppLinkUri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.AppLinkUri/)属性是`Uri`包含返回到当前页并显示当前所需的信息`TodoItem`。 下面的示例演示一个示例`Uri`示例应用程序：

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

这`Uri`包含启动所需的所有信息`deeplinking`应用程序中，导航到`DeepLinking.TodoItemPage`，并显示`TodoItem`具有`ID`的`ec38ebd1-811e-4809-8a55-0d028fce7819`。

## <a name="registering-content-for-indexing"></a>注册为进行索引的内容

一次[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)创建实例，必须将它注册为索引显示在搜索结果中。 这实现的[ `RegisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.RegisterLink/p/Xamarin.Forms.IAppLinkEntry/)方法，如下面的代码示例中所示：

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

这将添加[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例与应用程序的[ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/)集合。

> [!NOTE]
> `RegisterLink`方法还可以用于更新编制索引的页的内容。

一次[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例已注册为索引，它可以出现在搜索结果。 以下屏幕截图显示创建索引在 iOS 平台上的搜索结果中显示的内容：

![](deep-linking-images/ios-search.png "在 iOS 上的搜索结果中创建索引的内容")

## <a name="de-registering-indexed-content"></a>取消注册索引内容

[ `DeregisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.DeregisterLink/p/Xamarin.Forms.IAppLinkEntry/)方法用于创建索引的内容删除搜索结果中，如下面的代码示例中所示：

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

这将删除[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)从应用程序的实例[ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/)集合。

> [!NOTE]
> 在 Android 上不能从搜索结果中删除索引的内容。

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>响应的深层链接

当创建索引的内容显示在搜索结果中，选择的用户，`App`类应用程序将收到的请求处理`Uri`包含在创建索引的内容。 中可处理此请求[ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/)重写，如下面的代码示例中所示：

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

[ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/)方法检查接收`Uri`适用于应用程序，然后才分析`Uri`到页后，可以导航到并使用参数来传递到页。 页后，可以导航是创建的实例和`TodoItem`表示参数检索的页面。 [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)页后，可以导航到然后设置为`TodoItem`。 这可确保当`TodoItemPage`显示[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)方法，它将演示`TodoItem`其`ID`的深层链接中包含。

## <a name="making-content-available-for-search-indexing"></a>使内容可供搜索索引

深层链接表示页面显示时，每次[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)属性可以设置为`true`。 这使得在 iOS 和 Android [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)搜索索引，然后在仅限 iOS 可用的实例，它还使`AppLinkEntry`实例可用于提交。 Handoff 有关的详细信息，请参阅[简介 Handoff](~/ios/platform/handoff.md)。

下面的代码示例演示了如何设置[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)属性`true`中[ `Page.OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)重写：

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

同样，通过深层链接所代表的页导航离开从[ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/)属性可以设置为`false`。 在 iOS 和 Android，这将停止[ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)实例正被播发搜索索引，然后在 iOS 唯一，it 还停止广告`AppLinkEntry`Handoff 的实例。 这可以实现在[ `Page.OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/)重写，如下面的代码示例中所示：

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>切换到提供数据

在 iOS 上的索引页时，可以存储特定于应用程序的数据。 这通过将数据添加到实现[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)集合，这是`Dictionary<string, string>`在 Handoff 中存储使用的键 / 值对的。 Handoff 是用户启动其设备之一上的活动，并在其设备的另一台继续该活动 （如由用户的 iCloud 帐户标识） 一种方式。 下面的代码演示存储特定于应用程序的键 / 值对的示例：

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

值存储在[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)集合将存储在索引页上的元数据，并且在用户点击搜索结果，其中包含深层链接 （或使用时 Handoff 在另一台查看内容时将无法还原登录的设备）。

此外，可以指定以下键的值：

- `contentType` – `string` ，指定创建索引的内容的统一类型标识符。 建议的约定，以使用此值是页面，其中包含创建索引的内容的类型名称。
- `associatedWebPage` –`string`表示 web 页后，可以访问或如果创建索引的内容也可以查看在 web 上，如果应用程序支持 Safari 的深层链接。
- `shouldAddToPublicIndex` –`string`任一`true`或`false`控制是否将创建索引的内容添加到 Apple 的公有云索引，然后向尚未在其 iOS 设备安装应用程序的用户。 但是，只是因为内容已经设置为公共的索引，它并不意味着，它将自动添加到 Apple 的公有云索引。 有关详细信息，请参阅[公共搜索索引](~/ios/platform/search/nsuseractivity.md)。 请注意，此键应设置为`false`添加到个人数据时[ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/)集合。

> [!NOTE]
> `KeyValues`集合不在 Android 平台上使用。

Handoff 有关的详细信息，请参阅[简介 Handoff](~/ios/platform/handoff.md)。

## <a name="summary"></a>总结

本文演示如何使用应用程序索引以及深层链接使 Xamarin.Forms 应用程序内容可供搜索 iOS 和 Android 设备上。 应用程序索引允许应用程序保持关联由在几个使用后将否则有关忘记的搜索结果中显示。


## <a name="related-links"></a>相关链接

- [深层链接 （示例）](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [iOS 搜索 Api](~/ios/platform/search/index.md)
- [在 Android 6.0 中的应用程序链接](~/android/platform/app-linking.md)
- [AppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)
- [IAppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinkEntry/)
- [IAppLinks](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinks/)
