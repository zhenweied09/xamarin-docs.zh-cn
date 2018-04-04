---
title: 核心 NFC
description: 使用 iOS 11 读取近距离通信 (NFC) 标记
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2016
ms.openlocfilehash: db0c417c1c79a988821a93b1cf20d94161f5c47a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="core-nfc"></a>核心 NFC

_使用 iOS 11 读取近距离通信 (NFC) 标记_

CoreNFC 中是一个新框架提供对访问权限的 iOS 11_近场通信_(NFC) 单选读取从应用中的标记。 它适用于 iPhone 7，7 Plus，8，8，此外，与 X。

在 iOS 设备的 NFC 标记读取器支持所有的 NFC 标记类型 1 至步骤 5 包含_NFC 数据交换格式_(NDEF) 信息。

有一些限制需要注意的：

- CoreNFC 仅支持读取 （不编写或格式设置） 的标记。
- 标记扫描必须是用户启动的并在 60 秒后的超时。
- 应用必须在用于扫描前台中可见。
- 仅可以在 （而不是在模拟器中） 的真实设备上测试 CoreNFC。

此页描述使用 CoreNFC 所需的配置，并演示如何使用 API 使用["TFCTagReader"示例代码](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)。

## <a name="configuration"></a>配置

若要启用 CoreNFC，必须在你的项目中配置三个项：

- **Info.plist**私钥。
- **Entitlements.plist**条目。
- 使用预配配置文件**NFC 标记读取**功能。

### <a name="infoplist"></a>Info.plist

添加**NFCReaderUsageDescription**私钥和扫描正在进行中时，向用户显示的文本。 使用适合你的应用程序消息 （例如，说明扫描的目的）：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

你的应用程序必须请求**字段通信标记读取附近**中的功能是使用以下键/值对你**Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>设置配置文件

创建一个新**应用程序 ID**并确保**NFC 标记读取**勾选了服务：

[![与选择的 NFC 标记读取的开发人员门户新应用程序 ID 页](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

然后应为此应用程序 ID，创建新的预配配置文件，然后下载并将其安装在你开发的 mac。

## <a name="reading-a-tag"></a>读取一个标记

你的项目配置后，添加`using CoreNFC;`到顶部文件并按照以下三个步骤来实现 NFC 标记读取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1.实现 `INFCNdefReaderSessionDelegate`

接口有两种方法来实现：

- `DidDetect` – 当成功读取标记时调用。
- `DidInvalidate` – 当发生错误或达到 60 秒超时时调用。

#### <a name="diddetect"></a>DidDetect

在示例代码中，每个扫描的消息已添加到表格视图：

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

此方法可能多次调用 （和可能在传递消息的数组） 如果该会话允许进行多个标记读取。 该值通过第三个参数设置`Start`方法 (中所述[步骤 2](#step2))。

#### <a name="didinvalidate"></a>DidInvalidate

多种原因可导致产生失效：

- 扫描时出错。
- 上限时间停止应用程序要在前台。
- 用户选择取消扫描。
- 扫描已取消应用程序。

下面的代码演示如何处理错误：

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

一旦已失效会话，必须创建新的会话对象以重新扫描。

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2.启动 `NFCNdefReaderSession`

扫描应该开头的用户请求，如按下按钮。
下面的代码创建并启动扫描的会话：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

参数`NFCNdefReaderSession`构造函数如下所示：

- `delegate` – 的实现`INFCNdefReaderSessionDelegate`。 在示例代码中，委托实现在表视图控制器，因此`this`用作委托参数。
- `queue` – 回调处理的队列。 它可以是`null`，在这种情况下，一定要使用`DispatchQueue.MainQueue`更新用户界面控件 （如示例所示） 时。
- `invalidateAfterFirstRead` – 当`true`，在第一次成功扫描; 后停止扫描时`false`扫描将继续，直到扫描被取消或达到 60 秒的超时，将返回多个结果。


### <a name="3-cancel-the-scanning-session"></a>3.取消了扫描的会话

用户可以取消通过用户界面中系统提供的按钮的扫描会话：

![扫描时的取消按钮](corenfc-images/scan-cancel-sml.png)

应用程序可以通过调用以编程方式取消扫描`InvalidateSession`方法：

```csharp
Session.InvalidateSession();
```

在这两种情况下，该委托的`DidInvalidate`将调用方法。

## <a name="summary"></a>总结

CoreNFC 让你的应用从 NFC 标记，读取数据。 它支持读取各种标记格式 （NDEF 类型 1 至步骤 5），但不支持编写或格式设置。


## <a name="related-links"></a>相关链接

- [NFCTagReader （示例）](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [引入了核心 NFC (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/718/)
