---
title: "使用用户默认值"
description: "本文介绍如何使用 NSUserDefault Xamarin iOS 应用程序或扩展中保存默认设置。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: c4b2a103821bb18da4878cd37335faa899e910be
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-user-defaults"></a>使用用户默认值

_本文介绍如何使用 NSUserDefault Xamarin iOS 应用程序或扩展中保存默认设置。_


`NSUserDefaults`类提供一种 iOS 应用和扩展，以编程方式与整个系统的默认系统交互。 通过使用默认系统，用户可以配置应用的行为或样式以满足他们的首选项 （基于应用程序的设计）。 例如，若要提供皇室度量值的度量值的 vs 中的数据或选择一个给定的用户界面主题。

当使用应用组、`NSUserDefaults`还提供给定组中的应用程序 （或扩展） 之间进行通信的方法。

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>有关用户默认值

正如所述的上方，用户默认设置 (`NSUserDefaults`) 可以添加到应用程序 （或扩展），用于提供最终用户可以修改它来调整其外观或操作的运行时的应用的可配置选项。

你的应用程序第一次执行时，`NSUserDefaults`从应用程序的用户默认数据库中读取的键和值并将它们缓存到内存中以避免打开和读取每个时间的数据库的值是必需的。 

> [!IMPORTANT]
> **请注意**: Apple 不再建议开发人员调用`Synchronize`直接同步与数据库的内存中缓存的方法。 相反，它将自动调用应按定期间隔，以使内存中缓存与用户的默认值数据库同步。

`NSUserDefaults`类包含几种便利方法读取和如编写的常见数据类型的首选项值： 字符串、 整数、 浮点、 布尔值和 Url。 可以使用对其他类型的数据将存档`NSData`，然后读取或写入用户默认数据库。 有关详细信息，请参阅 Apple 的[首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>访问共享使用 NSUserDefaults 实例 

为当前用户的设备的情况下，共享的用户默认实例提供对用户的默认设置的访问。 如果不存在共享默认值对象，它创建第一次访问和使用以下信息初始化：

- `NSArgumentDomain`包含从当前应用程序分析的默认值。
- 应用的捆绑标识符域。
- `NSGlobalDomain`包含由所有应用共享的默认值。
- 为每个用户单独的域的首选语言。
- `NSRegistationDomain`与一组可通过应用程序以确保搜索始终成功修改的临时默认设置。

若要访问共享的用户默认实例，请使用下面的代码：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>访问应用程序组使用 NSUserDefaults 实例

如前所述，通过使用应用组、`NSUserDefaults`可用于进行应用程序 （或扩展） 之间的通信给定组中。 首先，你将需要确保，应用程序组和所需的应用程序 Id 是否已正确配置中**证书、 标识符和配置文件**节[iOS 开发人员中心](https://developer.apple.com/devcenter/ios/)和已安装在开发环境中。

接下来，你的应用程序和/或扩展的项目将需要为一个有效的应用程序 Id，上面创建的`Entitlements.plist`文件启用和指定的应用程序组，它获取的包含在应用捆绑包。

与就地此 all，可以使用下面的代码访问的共享的应用程序组用户的默认值：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

其中`group.com.xamarin.todaysharing`中创建应用程序组**证书、 标识符和配置文件**你想要访问。 有关详细信息，请参阅[应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>读取默认值

在访问所需的用户默认数据库之后，你可以阅读值中使用键/值对和多个基于正在读取的数据类型的简便方法的默认值：

- `ArrayForKey` -返回的数组`NSObjects`针对给定的密钥值。
- `BoolForKey` -返回给定键的布尔值。
- `DataForKey` -返回`NSData`给定键的对象。
- `DictionaryForKey` -返回`NSDictionary`给定键。
- `DoubleForKey` -返回给定键的双精度值。
- `FloatForKey` -返回给定键的 float 值。
- `IntForKey` -返回给定键的整数值。
- `StringArrayForKey` -返回的数组`String`从给定的密钥值的对象。
- `StringForKey` -返回给定键的字符串值。
- `URLForKey` -返回`NSUrl`给定键的值。

例如，下面的代码将读取一个布尔值，从用户默认设置：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>编写默认值

就像读取上面值，在访问所需的用户默认数据库之后, 你可以编写值使用键/值对和多个基于正在写入的数据类型的简便方法的默认值：

- `SetBool` -将给定的布尔值写入给定的键。
- `SetDouble` -将给定的双精度值写入给定的键。
- `SetFloat` -将给定的浮点值写入给定的键。
- `SetString` -将给定的字符串值写入给定的键。
- `SetURL` -将给定的 URL (`NSUrl`) 到给定的键的值。

例如，下面的代码会将布尔值写入到用户的默认设置：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> **注意：**您的应用程序第一次执行时，`NSUserDefaults`从应用程序的用户默认数据库中读取的键和值并将它们缓存到内存中以避免打开和读取每个时间的数据库的值是必需的。



<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖`NSUserDefaults`类以及如何使用它来提供一组的最终用户可用于配置你的 Xamarin.iOS 应用程序的选项。 此外，它涵盖应用组用于扩展和其父应用程序之间或组中的应用之间进行通信。


## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
