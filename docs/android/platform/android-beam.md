---
title: "Android 无线发送"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: bea8480c66a2ecf499375636c98511ca55ce7693
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="android-beam"></a>Android 无线发送

Android 无线数据交换是一项新近距离通信 (NFC) 技术，允许应用程序共享信息在近距离的 NFC Android 4。

[![图示共享信息的近距离的两个设备](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png)

Android 无线发送的工作原理是通过 NFC 推送消息，当两个设备都在范围之内。 设备相互大约 4 cm 可以共享使用 Android 无线发送的数据。 另一台设备上的一个活动创建一条消息，并指定活动 （或是活动），可以处理并将其推送。 当指定的活动在前台，而设备都在范围之内时，Android 无线发送会将消息推送到第二个设备。 在接收设备上，为打算将调用包含消息数据。

Android 支持设置有 Android 无线数据交换的消息的两种的方法：

-   `SetNdefPushMessage` -启动 Android 无线发送之前，应用程序可以调用 SetNdefPushMessage 指定 NdefMessage 推送通过 NFC 和将其推的活动。 当应用程序正在使用时，不会更改消息时，最好使用此机制。

-   `SetNdefPushMessageCallback` -Android 无线发送启动时，将应用程序可以处理回调来创建 NdefMessage。 此机制允许消息创建，直到设备处于范围会延迟。 它支持基于应用程序中发生了什么情况，消息可能会有所不同的方案。


在任一情况下，将数据有 Android 无线数据交换，发送应用程序发送`NdefMessage`，打包在多个数据`NdefRecords`。 让我们看看我们可以触发 Android 无线发送之前必须解决的关键点。 首先，我们将使用创建的回调样式`NdefMessage`。

<a name="Creating_a_Message" />

## <a name="creating-a-message"></a>创建一条消息

我们可以注册使用回调和`NfcAdapter`在活动的`OnCreate`方法。 例如，假设`NfcAdapter`名为`mNfcAdapter`声明为类变量在活动中，我们可以编写以下代码以创建将构造消息的回调：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

实现的活动`NfcAdapter.ICreateNdefMessageCallback`，传递给`SetNdefPushMessageCallback`上面方法。 当启动 Android 无线发送时，系统将调用`CreateNdefMessage`，从活动可以构造`NdefMessage`如下所示：

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```

<a name="Receiving_a_Message" />

## <a name="receiving-a-message"></a>接收消息

在接收端，系统调用与为打算`ActionNdefDiscovered`操作，从中我们可以提取 NdefMessage，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

完整代码示例使用 Android 无线发送，显示在下面的屏幕截图中运行，请参阅[Android 无线发送演示](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)示例库中。

[![从 Android 无线发送演示示例屏幕快照](android-beam-images/24.png)](android-beam-images/24.png)



## <a name="related-links"></a>相关链接

- [Android 无线发送演示 （示例）](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
