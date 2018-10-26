---
title: 故障排除 Xamarin Live Player
description: 本文档介绍了 Xamarin Live Player 和可能的修补程序的已知的问题。 它讨论了连接问题、 配置问题和详细信息。
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: eb9d758d72febe0fc0b705d66246c99ade1fc80f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109687"
---
# <a name="troubleshooting-xamarin-live-player"></a>故障排除 Xamarin Live Player

![预览功能](~/media/shared/preview.png)

本文介绍一些常见的问题，并提供了步骤来更正它们。

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>移动设备不会连接后扫描条形码 （或输入的代码）

运行 Xamarin Live Player 的移动设备不在与运行在 IDE 的计算机位于同一网络上时发生。 请查看以下内容：

- 确认设备和计算机位于相同的 Wi-fi 网络。
  - 如果计算机也连接到有线网络，请尝试拔出有线的连接。
- 网络可能会受到严格保护 （例如，某些公司网络），阻止通过 Xamarin Live Player 所需的端口。
- 关闭 Xamarin Live Player 应用并重新启动它。

## <a name="error-while-trying-to-deploy-message-in-ide"></a>在 IDE 中的"尝试部署时出现错误"消息

**"IOException： 无法从传输连接读取数据： 将阻止对非阻止套接字操作"**

运行 Xamarin Live Player 的移动设备不在运行 Visual Studio; 的计算机所在的同一网络上时，通常会遇到此错误这通常发生在连接到以前已成功配对的设备。

* 检查设备和计算机位于相同的 Wi-fi 网络。
* 网络可能会受到严格保护 （例如，某些公司网络），阻止通过 Xamarin Live Player 所需的端口。 以下端口所需的 Xamarin Live Player:
  * 37847 – 内部网络访问权限 
  * 8090 – 外部网络访问权限

## <a name="manually-configure-device"></a>手动配置设备

如果不可以通过 Wi-fi 连接到你的设备可以尝试手动配置你的设备配置文件中，通过使用以下步骤：

**步骤 1： 打开配置文件**

转到应用程序数据文件夹：

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

在此文件夹中将查找**PlayerDeviceList.xml**如果不存在你需要创建一个。

**步骤 2： 获取 IP 地址**

在 Xamarin Live Player 应用，请转至**有关 > 连接测试 > 开始连接测试**。

记下的 IP 地址，你将需要列出配置你的设备时的 IP 地址。

**步骤 3： 获取配对代码**

在 Xamarin Live Player 点击**对**或**再次对**，然后按**手动输入**。 数值代码将显示，这将需要更新配置文件。

**步骤 4： 生成 GUID**

转到： https://www.guidgenerator.com/online-guid-generator.aspx 和生成新 guid，并确保大写上。

**步骤 5： 配置设备**

打开**PlayerDeviceList.xml**向上如 Visual Studio 或 Visual Studio Code 编辑器中。 您需要在此文件中手动配置你的设备。 默认情况下，该文件应包含以下空`Devices`XML 元素：

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
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

**关闭并重新打开 Visual Studio。** 你的设备应显示在列表中。

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>在 IDE 中"无法找到类型或命名空间"消息

检查所选**启动项目**匹配 （例如你的设备类型。 Android) 和该配置与该设备类型 （例如。 **调试**适用于 Android)。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>在播放机中的"构造函数对类型 'InterpretedXamarin.Forms.Button 找不到"消息

某些系统类不能重写，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: Resource.Layout 不包含 Main 的定义"

使用 AXML 文件中定义的用户界面，Android 项目的出现此错误。
在 Xamarin Live Player 中当前不支持 AXML 文件。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 工具栏和选项卡的呈现错误地使用 Xamarin.Forms

Xamarin.Forms Android 项目必须使用"Toolbar.axml"和"Tabbar.axml"相关的布局文件的名称。 默认模板使用这些名称;重命名它们会导致呈现问题。

请在报告的任何其他问题[bugzilla](https://aka.ms/live-player-report-issue)。

## <a name="related-links"></a>相关链接

- [限制](~/tools/live-player/limitations.md)
- [安装](~/tools/live-player/install.md)
