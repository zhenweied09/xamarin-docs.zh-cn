---
title: 疑难解答
description: 使用 Xamarin 实时播放器，以及如何修复它们的已知的问题。
ms.topic: article
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: ab075cad0c3f3456ed23f3eb175dcdb3aa493510
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshooting"></a>疑难解答

![预览功能](~/media/shared/preview.png)

此文章介绍一些常见的问题，并提供的步骤来纠正它们。


## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>移动设备不会连接后扫描条形码 （或输入的代码）

运行 Xamarin 实时播放器的移动设备不在与运行 IDE 的计算机位于同一网络上时发生。 请查看以下内容：

- 确认的设备和计算机均在同一 Wi-fi 网络上。
  - 如果计算机也连接到有线网络，请尝试拔掉电源有线的连接。
- 网络可能紧密保护 （例如，某些公司网络）、 阻止通过实时的 Xamarin Player 所需的端口。
- 关闭 Xamarin 实时播放器应用程序并重新启动它。


## <a name="error-while-trying-to-deploy-message-in-ide"></a>在 IDE 中的"错误时尝试部署"消息

**"IOException： 无法从传输连接中读取数据： 非阻止套接字上的操作将阻塞"**

如果运行实时的 Xamarin Player 的移动设备不在与运行 Visual Studio; 的计算机位于同一网络上，通常会遇到此错误这通常发生在连接到以前成功配对的设备。

* 检查的设备和计算机均在同一 Wi-fi 网络上。
* 网络可能紧密保护 （例如，某些公司网络）、 阻止通过实时的 Xamarin Player 所需的端口。 以下的端口所需的 Xamarin 实时播放器：
  * 37847 – 内部网络访问权限 
  * 8090 – 外部的网络访问权限

## <a name="manually-configure-device"></a>手动配置设备

如果你无法通过 Wi-fi 连接到你的设备可以尝试手动配置你的设备配置文件中，通过，通过执行以下步骤：

**步骤 1： 打开配置文件**

前往应用程序数据文件夹：

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

在此文件夹中，你将找到**PlayerDeviceList.xml**如果它不存在你需要创建一个。

**步骤 2： 获取 IP 地址**

在实时的 Xamarin Player 应用中，转到**有关 > 连接测试 > 启动连接测试**。

记下的 IP 地址，你将需要配置你的设备时列出的 IP 地址。

**步骤 3： 获取配对代码**

在实时的 Xamarin Player tap 内部**对**或**再次对**，然后按**手动输入**。 数值代码将显示，你将需要更新配置文件。

**步骤 4： 生成 GUID**

转到：https://www.guidgenerator.com/online-guid-generator.aspx并生成新的 guid，并确保大写上。


**步骤 5： 配置设备**

打开**PlayerDeviceList.xml**如 Visual Studio 或 Visual Studio 代码编辑器中。 你需要在此文件中手动配置你的设备。 默认情况下，该文件应包含以下空`Devices`XML 元素：

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**添加 iOS 设备：**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>iPhone Player</Name>
<Platform>iOS</Platform>
<AndroidApiLevel>0</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:36:03.9492291Z</LastConnectTimeUtc>
</PlayerDevice>
```


**添加 Android 设备：**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**关闭并重新打开 Visual Studio。** 在列表中应显示你的设备。


## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>在 IDE 中的"无法找到类型或命名空间"消息

你已选择的检查**启动项目**匹配你的设备类型 （iOS 或 Android） 和该配置与该设备类型 （如。 **调试 | iPhone 模拟器**适用于 iOS)。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Player 中的"类型 InterpretedXamarin.Forms.Button 找不到构造函数"消息

某些系统类不能重写，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: Resource.Layout 不包含 Main 的定义"

带有 AXML 文件中定义的用户界面，对于 Android 项目出现此错误。
在 Xamarin 实时播放器中当前不支持 AXML 文件。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 工具栏和选项卡呈现不正确使用 Xamarin.Forms

Xamarin.Forms Android 项目必须使用"Toolbar.axml"和"Tabbar.axml"相关的布局文件的名称。 默认模板使用这些名称;重命名它们会导致呈现问题。


任何其他问题请报告上[bugzilla](https://aka.ms/live-player-report-issue)。


## <a name="related-links"></a>相关链接

- [限制](~/tools/live-player/limitations.md)
- [安装](~/tools/live-player/install.md)
