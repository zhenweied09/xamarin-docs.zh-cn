---
title: iOS 安全和隐私功能
description: 本文介绍如何与 iOS 以及它们如何影响 Xamarin.iOS 应用程序的安全和隐私的工作。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2ce6dadf2236c326fcbc2021509795182329010e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="ios-security-and-privacy-features"></a>iOS 安全和隐私功能

_本文介绍如何与 iOS 以及它们如何影响 Xamarin.iOS 应用程序的安全和隐私的工作。_

Apple 已对安全和隐私 iOS 10 （和更高版本） 中存在多项的增强，可帮助开发人员提高其应用程序的安全性，并确保最终用户的隐私。 本文将介绍在 Xamarin.iOS 应用程序中实现这些功能。

将详细介绍以下主题：

- [常规的增强功能](#General-Enhancements)
- [访问私有用户数据](#Accessing-Private-User-Data)
    - [设置隐私项](#Setting-Privacy-Keys)
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>常规的增强功能

以下常规进行了更改与安全和隐私在 iOS 10 中：

- 常见数据安全体系结构 (CDSA) API 已弃用，因此应替换为要生成非对称密钥的 SecKey API。
- 新`NSAllowsArbitraryLoadsInWebContent`密钥可以将添加到应用的`Info.plist`文件并将允许 web 页时为应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。 有关详细信息，请参阅我们[应用传输安全](~/ios/app-fundamentals/ats.md)文档。
- 由于新剪贴板中 iOS 10 和 macOS Sierra 允许用户设备之间复制和粘贴，该 API 已扩展为允许剪贴板限制为特定设备和加盖时间戳给定时刻自动清除。 此外，命名的选不再保留，而应替换共享粘贴板容器。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议开发人员停止使用 sha-1 和 3DES 加密越早越好。

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>访问私有用户数据

在 iOS 10 （或更高版本） 上运行的应用程序必须以静态方式声明其想要通过输入中的一个或多个隐私键访问特定功能或用户信息其`Info.plist`向为什么应用程序希望获得访问权限的用户说明的文件。

> [!IMPORTANT]
> 没有提供在它们试图访问受限制的功能或用户信息之一时，系统将以无提示方式终止所需的密钥的应用_准确无误_！ 如果应用程序启动 iOS 10 上的意外失败，确保在所有所需的`Info.plist`已指定。

以下隐私相关的密钥都是可用：

- **隐私-Apple 音乐用法描述**(`NSAppleMusicUsageDescription`)-允许开发人员描述应用程序需要访问用户的媒体库的原因。
- **隐私-蓝牙外围使用率描述**(`NSBluetoothPeripheralUsageDescription`)-允许开发人员描述应用程序想要访问用户的设备上的蓝牙的原因。
- **隐私-日历使用说明**(`NSCalendarsUsageDescription`)-允许开发人员描述应用程序需要访问用户的日历的原因。
- **隐私-相机使用率描述**(`NSCameraUsageDescription`)-允许开发人员描述应用程序需要访问设备的照相机的原因。
- **隐私-联系人使用率描述**(`NSContactsUsageDescription`)-允许开发人员描述应用程序需要访问用户的联系人的原因。
- **隐私-运行状况共享使用率描述**(`NSHealthShareUsageDescription`)-允许开发人员描述为什么应用程序需要访问用户的运行状况数据。 有关详细信息，请参阅 Apple 的[HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-运行状况更新使用情况描述**(`NSHealthUpdateUsageDescription`)-允许开发人员描述应用程序想要编辑用户的运行状况数据的原因。 有关详细信息，请参阅 Apple 的[HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-HomeKit 使用率描述**(`NSHomeKitUsageDescription`)-允许开发人员描述应用程序需要访问用户的 HomeKit 配置数据的原因。
- **隐私-位置始终使用说明**(`NSLocationAlwaysUsageDescription`)-允许开发人员描述应用程序想要始终有权访问用户的位置的原因。
- [已弃用]**隐私-位置使用说明**(`NSLocationUsageDescription`)-允许开发人员描述应用程序想要访问用户位置的原因。 *注意： 在 iOS 8 （和更高版本） 中已弃用此密钥。使用`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`相反。*
- **隐私-位置时在使用使用说明**(`NSLocationWhenInUseUsageDescription`)-允许开发人员描述应用程序想要运行时访问用户的位置的原因。
- [已弃用]**隐私-媒体库使用说明**-允许开发人员描述应用程序想要访问用户的媒体库的原因。 *注意： 在 iOS 8 （和更高版本） 中已弃用此密钥。使用`NSAppleMusicUsageDescription`相反。*
- **隐私-麦克风使用率描述**(`NSMicrophoneUsageDescription`)-允许开发人员描述应用程序需要访问设备麦克风的原因。
- **隐私-运动使用率描述**(`NSMotionUsageDescription`)-允许开发人员描述应用程序需要访问设备的加速的原因。
- **隐私-照片库使用说明**(`NSPhotoLibraryUsageDescription`)-允许开发人员描述应用程序需要访问用户的照片库的原因。
- **隐私-提醒使用率描述**(`NSRemindersUsageDescription`)-允许开发人员描述为什么应用程序需要访问用户的提醒。
- **隐私-Siri 使用率描述**(`NSSiriUsageDescription`)-允许开发人员描述应用程序想要将用户数据发送到 Siri 的原因。
- **隐私-语音识别使用说明**(`NSSpeechRecognitionUsageDescription`)-允许开发人员描述应用程序想要将用户数据发送到 Apple 的语音识别服务器的原因。
- **隐私-电视提供程序使用说明**(`NSVideoSubscriberAccountUsageDescription`)-允许开发人员描述应用程序想要访问用户的电视提供程序帐户的原因。

有关详细信息使用`Info.plist`密钥，请参阅 Apple 的[信息属性列表密钥引用](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>设置隐私项

需要在 iOS 10 （和更高版本） 上访问 HomeKit 的下面的示例，开发人员将需要添加`NSHomeKitUsageDescription`到应用程序的密钥`Info.plist`文件，并提供应用程序需要访问用户的 HomeKit 数据库为什么字符串声明。 此字符串将显示给运行该应用的用户的第一个时间：

[![](security-privacy-images/info01.png "示例 NSHomeKitUsageDescription 警报")](security-privacy-images/info01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

适用于 Visual Studio 当前 Xamarin.iOS 不支持编辑安全增强`Info.plist`设置从 IDE 中，因此将需要以下解决方法：

1. 打开`Info.plist`在的外部文本编辑器中的文件。
2. 早于最后`</dict>`节点，添加以下节点： `<key>NSHomeKitUsageDescription</key>`
3. 添加以下节点来提供所需的说明： `<string>Allows the app to control HomeKit enabled devices.</string>`
4. `Info.plist`文件应如下所示： 

    [![](security-privacy-images/info02vs.png "Info.plist 文件应如下所示")](security-privacy-images/info02vs.png#lightbox)
4. 保存对文件所做的更改。
5. 返回到 Visual Studio 并重新编译的应用程序。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要设置任何隐私密钥，请执行以下操作：

1. 双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
2. 在屏幕的底部，切换到**源**视图。
3. 添加新**条目**到列表。
4. 从下拉列表中，选择隐私密钥 (如**隐私-HomeKit 使用率描述**): 

    [![](security-privacy-images/info02.png "选择隐私密钥")](security-privacy-images/info02.png#lightbox)
5. 输入应用程序想要访问给定的功能或用户信息的原因的说明： 

    [![](security-privacy-images/info03.png "输入的描述")](security-privacy-images/info03.png#lightbox)
6. 保存对文件所做的更改。

-----

> [!IMPORTANT]
> 在示例中提供更高版本，设置失败`NSHomeKitUsageDescription`中的键`Info.plist`文件将导致应用程序_以静默方式失败_（正在关闭系统在运行时） 而无需 iOS 10 （或更高版本） 中运行时的错误。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖的安全和隐私更改 Apple 已在 iOS 10 以及它们如何影响 Xamarin.iOS 应用程序。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
