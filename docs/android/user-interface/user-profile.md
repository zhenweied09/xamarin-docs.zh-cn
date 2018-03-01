---
title: "用户配置文件"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2017
ms.openlocfilehash: 53ac30abea05095583fcac5ddc315f93ce7024f2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="user-profile"></a>用户配置文件

Android 已支持的枚举联系人`ContactsContract`自 API 级别 5 提供程序。 例如，向列表联系人非常简单，只使用`ContactContracts.Contacts`类，如下面的代码中所示：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
           
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id,
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);
           
if (cursor.MoveToFirst ()) {
    do {
        Console.WriteLine ("Contact ID: {0}, Contact Name: {1}",
            cursor.GetString (cursor.GetColumnIndex (projection [0])),
            cursor.GetString (cursor.GetColumnIndex (projection [1])));
                   
    } while (cursor.MoveToNext());
}
```

Android 4 (API 级别 14 中)，新`ContactsContact.Profile`类是可通过 ContactsContract 提供程序。 `ContactsContact.Profile`允许访问个人的配置文件的设备，其中包括如设备所有者的姓名和电话号码的联系人数据的所有者。

<a name="Required_Permissions" />

## <a name="required-permissions"></a>所需权限

若要读取和写入联系人数据，应用程序必须请求`Read_Contacts`和`Write_Contacts`权限，分别。 此外，若要阅读和编辑用户配置文件，应用程序必须请求`Read_Profile`和`Write_Profile`权限。

<a name="Updating_Profile_Data" />

## <a name="updating-profile-data"></a>正在更新配置文件数据

设置这些权限后，应用程序可以使用正常的 Android 技术与用户配置文件的数据进行交互。 例如，若要更新的配置文件的显示名称，我们将调用`ContentResolver.Update`与`Uri`通过检索`ContactsContract.Profile.ContentRawContactsUri`属性，如下所示：

```csharp
var values = new ContentValues ();
          
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName,
    "John Doe");
           
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri,
    values, null, null);
```

<a name="Reading_Profile_Data" />

## <a name="reading-profile-data"></a>读取配置文件数据

发出到查询`ContactsContact.Profile.ContentUri`读回配置文件数据。 例如，下面的代码将读取用户配置文件的显示名称：

```csharp
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);

if (cursor.MoveToFirst ()) {
    Console.WriteLine(
        cursor.GetString (cursor.GetColumnIndex (projection [0])));
}
```

<a name="Navigating_to_the_People_App" />

## <a name="navigating-to-the-people-app"></a>导航到人员应用

最后，导航到新的人员应用程序附带了 Android 4 的用户配置文件，只需创建与为打算`ActionView`操作和`ContactsContract.Profile.ContentUri`，并将其传递到`StartActivity`方法如下：

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

在运行上面的代码，如下面的屏幕截图中所示都对用户配置文件，将加载人员应用：

[![显示的 John Doe 用户配置文件的屏幕截图的人员应用](user-profile-images/15-people-app.png)](user-profile-images/15-people-app.png)

使用用户配置文件现在是类似于与 Android 中的其他数据进行交互，并且提供的额外级别的设备的个性化。



## <a name="related-links"></a>相关链接

- [ContactsProviderDemo (sample)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
