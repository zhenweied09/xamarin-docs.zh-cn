---
title: "联系人和 ContactsUI"
description: "本文介绍如何使用新的联系人和联系人 UI 在 Xamarin.iOS 应用程序中的框架。 这些框架替换现有的通讯簿和 iOS 的早期版本中使用的地址簿 UI。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: cbabaf36ba8bfaa74ec17b0af0cd97e29f72d270
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="contacts-and-contactsui"></a>联系人和 ContactsUI

_本文介绍如何使用新的联系人和联系人 UI 在 Xamarin.iOS 应用程序中的框架。这些框架替换现有的通讯簿和 iOS 的早期版本中使用的地址簿 UI。_

IOS 9 的引入，与 Apple 已发布两个新的框架，`Contacts`和`ContactsUI`，该替换的现有通讯簿，且地址簿 UI 框架使用的 iOS 8 及更早版本。

两个新框架包含以下功能：

- [**联系人**](#contacts) -提供对用户的联系人列表数据访问。
    因为大多数应用程序只需要只读访问权限，此框架经过优化的线程安全的只读访问。

- [**ContactsUI** ](#contactsui) -提供 Xamarin.iOS UI 元素，若要显示，编辑、 选择和创建在 iOS 设备上的联系人。

[ ![](contacts-images/add01.png "IOS 设备上一个示例联系表")](contacts-images/add01.png)

> [!IMPORTANT]
> **注意：**现有`AddressBook`和`AddressBookUI`框架使用 ios 8 （和之前） iOS 9 中已弃用，并且应替换为与新`Contacts`和`ContactsUI`尽快针对任何现有 Xamarin.iOS 的框架应用程序。 针对新的框架，应编写新应用。




在以下部分中，我们将看看这些新框架以及如何在 Xamarin.iOS 应用程序中实现它们。

<a name="contacts" />

## <a name="the-contacts-framework"></a>联系人 Framework

联系人 Framework 提供了 Xamarin.iOS 访问用户的联系人信息。 因为大多数应用程序只需要只读访问权限，此框架经过优化的线程安全的只读访问。

### <a name="contact-objects"></a>联系人对象

`CNContact`类提供对联系人的属性，如名称、 地址或电话号码的线程安全的只读访问。 `CNContact` 与运行方式类似`NSDictionary`和包含多个，只读集合的属性 （如地址或电话号码）：

[ ![](contacts-images/contactobjects.png "联系人对象概述")](contacts-images/contactobjects.png)

对于可以有多个值 （如电子邮件地址或电话号码） 的任何属性，它们将表示为一个数组`NSLabeledValue`对象。 `NSLabeledValue` 是只读组的标签组成一个线程安全元组和值标签其中向用户 （例如家庭或工作的电子邮件） 定义的值。 联系人 framework 提供的预定义的标签选择 (通过`CNLabelKey`和`CNLabelPhoneNumberKey`静态类) 可以在应用中使用或已定义为你的需求的自定义标签的选项。

对于需要调整现有联系人的值 （或创建新的） 任何 Xamarin.iOS 应用，使用`NSMutableContact`版本的类和子类 (如`CNMutablePostalAddress`)。

例如，以下代码将创建新的联系人，并将其添加到联系人的用户的集合：

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

如果在 iOS 9 设备上运行此代码，则新联系人将添加到用户的集合。 例如:

[ ![](contacts-images/add01.png "新的联系人添加到用户的集合")](contacts-images/add01.png)

### <a name="contact-formatting-and-localization"></a>联系人的格式设置和本地化

联系人 framework 包含几个对象和方法，可帮助你设置的格式和本地化向用户显示的内容。 例如，下面的代码将正确地格式化联系人名称和邮寄地址显示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

你将在应用程序的 UI 中显示的属性标签，联系人 framework 具有本地化以及这些字符串的方法。 同样，这基于 iOS 设备运行该应用程序的当前区域设置。 例如:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOption.Nickname));
Console.WriteLine(CNLabeledValue.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>提取现有联系人

通过使用的实例`CNContactStore`类，你可以从用户的联系人数据库中提取的联系信息。 `CNContactStore`包含所有所需的提取或更新联系人和组从数据库的方法。 因为这些方法是同步的但还是建议你在后台线程，以防止阻塞 UI 上运行它们。

通过使用谓词 (从生成`CNContact`类)，你可以筛选在从数据库提取联系人时返回的结果。 若要提取仅包含字符串的联系人`Appleseed`，使用以下代码：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> **注意：**泛型和联系人 framework 不支持复合谓词。

例如，若要限制为仅提取**GivenName**和**FamilyName**的联系人的属性，可以使用下面的代码：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最后，若要查找数据库，并返回结果，请使用下面的代码：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果我们在中创建运行示例后运行此代码**联系人对象**上面部分中，它将返回我们刚刚创建的"John Appleseed"联系人。

### <a name="contact-access-privacy"></a>联系人访问隐私

由于最终用户可以授予或拒绝对他们的联系信息基于每个应用程序的访问，因此您使调用第一次`CNContactStore`，将显示一个对话框，要求他们以允许你应用程序访问权限。

权限请求将仅介绍一次、 第一次在应用正在运行，且后续运行或调用`CNContactStore`将使用用户选择在该时间的权限。

应设计您的应用程序，使它正常处理用户拒绝对其联系人数据库的访问。

#### <a name="fetching-partial-contacts"></a>提取部分联系人

A_部分联系_是仅将某些可用的属性已从联系存储提取的联系人。 如果你尝试访问不以前获取的属性，则将导致异常。

可以轻松地对其进行检查以查看给定的联系人是否通过使用具有所需的属性`IsKeyAvailable`或`AreKeysAvailable`方法`CNContact`实例。 例如:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> **注意：** `GetUnifiedContact`和`GetUnifiedContacts`方法`CNContactStore`类_仅_返回部分与限制为请求提供的提取键的属性。

### <a name="unified-contacts"></a>统一的联系人

用户可能有不同的 （如 Facebook 或 Google 邮件 iCloud) 其联系人数据库中的人员的联系信息源。 在 iOS 和 OS X 应用，此联系信息将自动可链接在一起并向作为单个用户显示_统一联系_:

[ ![](contacts-images/unified01.png "统一的联系人概述")](contacts-images/unified01.png)

此统一联系是临时内存中都有它自己的唯一标识符 （这应该用于根据需要重新提取联系人） 的链接联系信息视图。 默认情况下，联系人 framework 将返回尽可能统一联系人。

### <a name="creating-and-updating-contacts"></a>创建和更新联系人

正如我们看到在[联系对象](#Contact_Objects)上面部分中，你使用`CNContactStore`和的一个实例`CNMutableContact`若要创建新的联系人然后写入到用户的联系数据库使用`CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

A`CNSaveRequest`还可用于缓存到单个操作的多个联系人和组更改和批处理到这些修改`CNContactStore`。

若要更新从提取操作获取非可变联系人，你必须首先请求可变副本，然后修改并保存回联系人的存储库。 例如:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>联系人的更改通知

每当修改联系人时，请与应用商店文章`CNContactStoreDidChangeNotification`到默认通知中心。 如果已缓存或当前正在显示任何联系人，你将需要刷新这些对象从联系人应用商店 (`CNContactStore`)。

### <a name="containers-and-groups"></a>容器和组

用户的联系人可以存在或本地用户的设备上作为从一个或多个服务器帐户 （如 Facebook 或 Google） 同步到设备的联系人。 联系人的每个池都有自己_容器_和给定的联系人只能存在一个容器中。

[ ![](contacts-images/containers01.png "容器和组概述")](contacts-images/containers01.png)

某些容器允许安排到一个或多个联系人_组_或_子组_。 此行为是依赖于给定容器的后备存储。 例如，iCloud 具有只有一个容器，但可以具有多个组 （但没有子组）。 Microsoft Exchange 另一方面，不支持组，但可以有多个容器 （一个用于每个 Exchange 文件夹）。

[ ![](contacts-images/containers02.png "在容器和组重叠")](contacts-images/containers02.png)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>ContactsUI Framework

其中你的应用程序不需要提供自定义 UI 的情况下，你可以使用 ContactsUI framework 提供 UI 元素，以显示、 编辑、 选择和在你的 Xamarin.iOS 应用程序中创建联系人。

通过使用 Apple 的内置控件不仅减少你必须创建要在你的 Xamarin.iOS 应用程序，支持联系人的代码量，但向应用程序的用户提供一致的接口。

### <a name="the-contact-picker-view-controller"></a>联系人选取器视图控制器

联系人选取器视图控制器 (`CNContactPickerViewController`) 管理标准的联系人选取器视图，用户可以从用户的联系人数据库中选择联系人或联系人属性。 用户可以选择一个或多个联系人 （基于其使用情况），并联系选取器视图控制器不会提示之前显示选取器的权限。

在调用之前`CNContactPickerViewController`类，你定义用户可以选择，并定义谓词，用于控制的显示和联系人属性的选择哪些属性。

使用继承自的类的实例`CNContactPickerDelegate`对与选取器的用户交互作出响应。 例如:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

若要允许用户选择从其数据库中的联系人的电子邮件地址，可以使用下面的代码：

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>联系人的视图控制器

请与视图控制器 (`CNContactViewController`) 类提供了要呈现给最终用户的标准的联系人视图的控制器。 联系人视图可以显示新的新建、 未知或现有联系人，并且必须指定类型，然后通过调用正确的静态构造函数显示的视图 (`FromNewContact`， `FromUnknownContact`， `FromContact`)。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>摘要

本文已了解使用 Xamarin.iOS 应用程序中的联系人和联系人 UI 框架的详细的信息。 首先，它涉及不同类型的联系人 framework 提供的对象以及如何使用它们来创建新模板或访问现有的联系人。 它还检查，请与联系 UI 框架，选择现有联系人并显示联系信息。


## <a name="related-links"></a>相关链接

- [QuickContacts 示例](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [IOS 9 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-DontLinkElementID_14))
- [联系人 Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/Contacts/Reference/Contacts_Framework/index.html#//apple_ref/doc/uid/TP40015328)
- [ContactsUI Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/ContactsUI/Reference/ContactsUI_Framework/index.html#//apple_ref/doc/uid/TP40016207)
