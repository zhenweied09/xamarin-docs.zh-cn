---
title: "使用联系人 ContentProvider"
ms.topic: article
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 677d672b3f00d4c3f3505ab2adf977f16fca4de5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-the-contacts-contentprovider"></a>使用联系人 ContentProvider

使用访问公开的数据的代码`ContentProvider`不需要对引用`ContentProvider`根本类。 相反，Uri 用于通过由公开的数据创建游标`ContentProvider`。 Android 使用 Uri 来搜索的应用程序公开的系统`ContentProvider`与该标识符。 Uri 是一个字符串，通常在反向 DNS 格式如`com.android.contacts/data`。

而不是使开发人员记住此字符串中，Android*联系人*提供程序公开其元数据中的`android.provider.ContactsContract`类。 此类用于确定的 Uri`ContentProvider`以及表和列可查询的名称。

某些数据类型还需要访问的特殊权限。 内置的联系人列表需要`android.permission.READ_CONTACTS`中的权限**AndroidManifest.xml**文件。

有三种方法从 Uri 创建一个游标：

1. **ManagedQuery()** &ndash;是首选的方法在 Android 2.3 (API 级别 10) 和早期版本，`ManagedQuery`光标返回并还会自动管理刷新数据和关闭游标。 此方法已弃用 Android 3.0 (API 级别 11) 中。

1. **ContentResolver.Query()** &ndash;返回非托管的游标，这意味着您必须刷新和在代码显式关闭它。

1. **CursorLoader()。LoadInBackground()** &ndash; Android 3.0 (API 级别 11) 中引入`CursorLoader`是现在使用的首选的方法`ContentProvider`。 `CursorLoader` 查询`ContentResolver`后台线程，因此不会阻止 UI。
   此类可以访问在较旧版本的 Android 使用 v4 兼容性库。


上述每种方法具有相同的一组基本输入：

-  **Uri** &ndash;的完全限定的名称`ContentProvider`。
-  **投影**&ndash;要选择光标的列规范。
-  **选择**&ndash;与 SQL 类似`WHERE`子句。
-  **SelectionArgs** &ndash;替换选定内容中的参数。
-  **SortOrder** &ndash;要作为排序依据的列。


<a name="Creating_Inputs_for_a_Query" />

## <a name="creating-inputs-for-a-query"></a>为查询创建输入

`ContactsProvider`示例代码执行针对 Android 的内置联系人提供程序的非常简单查询。 不需要知道的实际 Uri 名称或列名称的所需查询联系人的所有信息`ContentProvider`可用作常量由公开`ContactsContract`类。

无论哪种方法用于检索光标，这些相同的对象被用作参数中所示*ContactsProvider/ContactsAdapter.cs*文件：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

对于此示例， `selection`，`selectionArgs`和`sortOrder`将被忽略，将其设置为`null`。


<a name="Creating_a_Cursor_from_a_Content_Provider_Uri" />

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>创建内容提供程序 Uri 中的游标

参数对象具有创建后，它们可在以下三种方法之一：


<a name="Using_a_Managed_Query" />

### <a name="using-a-managed-query"></a>使用托管的查询

针对 Android 2.3 (API 级别 10) 的应用程序或更早版本应使用此方法：

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

此游标将由 Android 托管，因此不需要关闭它。


<a name="Using_ContentResolver" />

### <a name="using-contentresolver"></a>使用 ContentResolver

访问`ContentResolver`直接以获取对游标`ContentProvider`可以完成如下所示：

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

因此它必须在不再需要时关闭，不受管理，此光标。
确保代码是否关闭游标处于打开状态，否则将发生错误。

```csharp
cursor.Close();
```

或者，可以调用`StartManagingCursor()`和`StopManagingCursor()`管理光标。 托管的游标自动停用并重新查询活动将停止和重新启动时。


<a name="Using_CursorLoader" />

### <a name="using-cursorloader"></a>使用 CursorLoader

应用程序生成的 Android 3.0 (API 级别 11) 或更高版本应使用此方法：

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader`可确保所有游标操作都将在后台线程，并且可以智能地重复使用现有的光标在活动实例之间活动重新启动时 （例如由于配置更改） 而不是，再次重新加载数据。

此外可以使用早期的 Android 版本`CursorLoader`类通过[v4 支持库](http://developer.android.com/tools/support-library/index.html)。


<a name="Displaying_the_Cursor_Data_with_a_Custom_Adapter" />

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>显示光标数据与自定义适配器

若要显示联系人图像我们将使用自定义适配器，以便我们可以手动解决`PhotoId`对图像文件路径引用。

若要使用自定义适配器显示数据，该示例使用`CursorLoader`将检索到的本地集合中的所有联系人数据**FillContacts**方法从**ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

然后实现 BaseAdapter 的方法使用`contactList`集合。 就像它是与任何其他集合实现适配器&ndash;没有任何特殊的处理此处因为数据源自`ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

（如果存在），会显示该图像在设备上的图像文件使用的 Uri。 应用程序如下所示：

[![在 ListView; 中显示联系人的应用的屏幕快照一项左侧显示的图像](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png)

使用类似的代码模式，你的应用程序可以访问各种系统数据包括用户的照片、 视频和音乐。
某些数据类型需要特殊的权限，若要在项目的请求**AndroidManifest.xml**。


<a name="Displaying_the_Cursor_Data_with_a_SimpleCursorAdapter" />

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>显示具有 SimpleCursorAdapter 的光标数据

光标也会显示，与`SimpleCursorAdapter`(尽管仅名称将显示，但不照片)。 此代码演示如何使用`ContentProvider`与`SimpleCursorAdapter`（此代码不出现在此示例中）：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

请参阅[Listview 和适配器](~/android/user-interface/layouts/list-view/index.md)有关实现的详细信息`SimpleCursorAdapter`。


## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
