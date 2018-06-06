---
title: 在 Apple Watch 设备上测试
description: 本文档介绍如何部署用于测试实际的 Apple Watch 上使用 Xamarin 生成的 watchOS 应用。 它讨论设备，预配配置文件，测试，并提供一些故障排除技巧。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a960d81d41ff127fa3316e6190dfbf4881305c02
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790856"
---
# <a name="testing-on-apple-watch-devices"></a>在 Apple Watch 设备上测试

你已按照后[部署步骤](~/ios/watchos/deploy-test/index.md)若要创建应用 Id 和应用程序组 （如果需要），请使用此页上的说明：

- [设置你的设备](#devices)Apple 开发人员中心，并
- [创建预配配置文件的开发](#profiles)，然后
- [部署和测试](#testing)Apple Watch 上。

<a name="devices" />

## <a name="devices"></a>设备

测试真实 iPhone 或 iPad 上的 iOS 应用始终要求要在开发人员中心上注册的设备。 设备列表如下所示 (单击加号**+** 来添加新设备):

![](device-images/devices-sml.png "设备列表如下所示")

监视并没有什么区别-你现在需要将应用部署到它之前添加你的 Apple Watch 设备。 发现监视的 UDID 使用**Xcode** (**Windows > 设备**列表)。 连接配对的 phone 时也会显示监视的信息：

[![](device-images/xcode-devices-sml.png "配对的监视信息")](device-images/xcode-devices.png#lightbox)

当您知道手表的 UDID，将其添加到开发人员中心中的设备列表：

![](device-images/devices-watch-sml.png "监视的设备列表中的 UDID")

已添加了监视设备，确保在任何新的或现有的开发或你创建临时预配配置文件中选中该选项：

![](device-images/devices-provisioning.png "可用的设备列表")

不要忘记是否编辑了现有的配置文件，以下载并重新安装它 ！

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>预配配置文件的开发

若要生成适用于你需要创建在设备上测试**开发预配配置文件**你的解决方案中每个应用程序 id。

如果你有通配符应用程序 ID，*只有一个预配配置文件将需要*; 但是，如果你有独立的应用程序 ID 的每个项目都需要预配配置文件为每个应用程序 ID:

![](device-images/provisioningprofile-development.png "开发预配配置文件")

一旦你已创建所有三个配置文件，它们将显示在列表中。 请记住下载并安装每个：

![](device-images/provisioningprofiles.png "可用的开发设置配置文件")

你可以验证在预配配置文件**项目选项**通过选择**生成 > iOS 捆绑签名**屏幕并选择**版本**或**调试 iPhone**配置。

**预配配置文件**列表将显示所有匹配的配置文件-您应看到已创建此下拉列表中的匹配配置文件：

![](device-images/options-selectprofile.png "预配配置文件列表")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>监视设备上测试

配置你的设备、 应用程序 Id 和预配配置文件后，你可以进行测试。

1. 请确保你的 iPhone 已接通电源，并监视已与 iPhone 的配对。

2. 确保配置设置为**版本**或**调试**。

3. 确保在目标列表中选择了连接的 iPhone 设备。

4. IOS 应用程序项目 （不监视或扩展） 右击并选择**设为启动项目**。

5. 单击**运行**按钮 (或选择**启动**选项从**运行**菜单)。

6. 将生成解决方案并将 iOS 应用将部署到 iPhone。
  如果 iOS 应用程序或监视扩展设置未正确设置向 iPhone 部署将会失败。

7. 如果部署成功完成，iPhone 将自动尝试将监视应用程序发送到配对的监视。 您的应用程序图标会出现在循环与监视屏幕*安装*进度指示器。

8. 如果成功安装 watch 应用，图标将仍保留在监视屏幕中的动它若要开始测试您的应用程序 ！


## <a name="troubleshooting"></a>疑难解答

如果部署使用过程中发生错误**视图 > 填充 > 设备日志**以查看有关错误的详细信息。 下面列出了一些错误的原因：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>错误 MT3001： 无法不 AOT 程序集

这可能是在调试模式下部署到 Apple Watch 设备进行生成时。

到*暂时*要解决此问题，禁用**增量生成**的监视扩展中**项目选项 > 生成 > watchOS 生成**窗口：

[![](device-images/disable-incremental-sml.png "增量生成复选框")](device-images/disable-incremental.png#lightbox)

将在其后增量生成可以重新启用以利用更快的生成时间的未来版本中解决此问题。


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>监视应用程序无法启动时在设备上进行调试

当尝试调试在物理设备上，仅将图标与加载微调 watch 应用出现 （和最终超时）。 这将在将来的版本; 中解决一种解决方法是运行生成的发布 （将不允许调试）。


### <a name="invalid-application-executable-or-application-verification-failed"></a>无效的应用程序可执行文件或应用程序验证失败

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "无效的可执行应用程序警报")

如果出现这些消息*监视屏幕上*应用程序已尝试安装后，可能有几个问题：

- 监视设备本身不已添加作为 Apple 开发人员中心上的设备。 按照说明[正确配置设备](#devices)。

- 开发预配配置文件用于测试不具有包含; 监视设备或监视已添加到预配配置文件后在未重新下载并重新安装。 按照说明[正确配置预配配置文件](#profiles)。

- 如果**iOS 设备日志**包含`The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3`然后监视应用程序的**Info.plist**错误**MinimumOSVersion**值。
  这应是**8.2** -如果你已安装 Xcode 6.3 可能需要手动编辑源到插入将其设置为 8.2。

- 监视应用程序的**Entitlements.plist**正确了授权启用了 （如应用程序组），它不应具有。

- 监视应用程序的**应用程序 ID**不正确地具有它不应具有开发人员中心中的 （如应用程序组） 启用了授权。



### <a name="install-never-finished"></a>永远不会安装完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

此错误可能指示 Watch 应用中的不必要 （和无效） 密钥**Info.plist**文件。 不应包含用于监视应用程序中的 iOS 应用程序或监视扩展的密钥。

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"正在等待调试器连接"

如果**应用程序输出**窗口时遇到困难显示

```csharp
waiting for debugger to connect
```

检查是否任何你的项目中包含了 NuGets 具有依赖关系**Microsoft.Bcl.Build**。 这会自动添加包括常用某些 Microsoft 发布库与[Microsoft Http 客户端库](http://www.nuget.org/packages/Microsoft.Net.Http/)。

**Microsoft.Bcl.Build.targets**文件添加到 **.csproj**可能会干扰在部署过程中的 iOS 扩展打包。 你可以跟踪[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
可能的解决方法是编辑.csproj 文件，进行手动移动**Microsoft.Bcl.Build.targets**为最后一个元素。

