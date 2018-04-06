---
title: 连接到 Mac
description: Xamarin.iOS for Visual Studio 允许开发者使用 Visual Studio IDE 在 Windows 计算机上创建、生成和调试 iOS 应用程序。 本指南介绍了 Xamarin.iOS for Visual Studio 提供的功能，以及如何连接到 Mac 生成主机。
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 5a76c443521276a66e820fa0b1877ae2a4cce8f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="connecting-to-the-mac"></a>连接到 Mac

_Xamarin.iOS for Visual Studio 允许开发人员使用 Visual Studio IDE 在 Windows 计算机上创建、生成和调试 iOS 应用程序。本指南介绍 Xamarin.iOS for Visual Studio 提供的功能，以及如何连接到 Mac 生成主机。_

Visual Studio 通过 SSH 连接到 Mac，这有几个优点，包括：

- Visual Studio 可直接启动和控制生成代理。 不再是需要手动启动和停止的用户可见的应用程序。

- Visual Studio 中的新连接管理器将发现和记住 Mac 生成主机，并对其进行身份验证。

- 由于所有通信均通过 SSH 以隧道方式进行，因此只需单个端口连接到端口 22。

- 发生更改时将立刻通知 Visual Studio。 例如，如果插入 iOS 设备，工具栏将立即更新。

- Visual Studio 的多个实例可同时连接。

- 该连接不会干涉开发。 只有在执行必须使用 Mac 的操作（如调试或使用 iOS 设计器）时，才会看到连接到 Mac 的提示。

到 Mac 的连接由其功能的不同部分（例如 iOS 设计器代理和生成代理）的多个进程组成，这些进程由一个代理控制。 此代理由 Visual Studio 控制和更新，并在独立进程即将崩溃时自动将它们重启。

下图简要概述了 Xamarin.iOS 开发工作流：

[![iOS 开发工作流](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
> 实际上，为了生成项目，Visual Studio 会启动单独的 MSBuild 进程。 此过程将创建与 Mac 的新连接，这表示当 Visual Studio 生成时，实际上将存在两个从 Windows 到 Mac 的 SSH 连接。 通过[命令行](#commandline)生成仅会创建一个 MSBuild 进程。 为使该图简洁，仅用一个箭头表示所有连接。

## <a name="requirements"></a>惠?

Xamarin.iOS for Visual Studio 可实现强大功能：让开发人员能在 Windows 计算机上使用 Visual Studio IDE 创建、生成和调试 iOS 应用程序。 它不能单独实现此功能 - 必须使用 Apple 编译器创建 iOS 应用程序，且必须通过 Apple 证书和代码签名工具进行部署。 这就是说，Xamarin.iOS for Visual Studio 安装需要连接到一台联网的 Mac OS X 计算机（称为“主机”或“生成主机”）才能执行这些任务。 配置完成后，Xamarin 的工具将使过程尽可能流畅。

### <a name="system-requirements"></a>系统要求

[在 Windows 上安装 Xamarin.iOS](~/ios/get-started/installation/windows/index.md#system-requirements) 指南中可以找到系统要求


#### <a name="compatibility"></a>兼容性

> [!IMPORTANT]
> Windows 计算机必须使用与其连接的 Mac 相同的 Xamarin.iOS 版本。 为了确保满足此要求，必须符合以下条件：                                                    
>                                                                                                                 
> - **Visual Studio 2015 及更低版本**：确保位于与 Visual Studio for Mac 相同的[更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)上。
>                                                                                                                 
> - **Visual Studio 2017 发布版**：确保位于 Visual Studio for Mac 的稳定版通道上。
>                                                                                                                 
> - **Visual Studio 2017 预览版**：确保位于 Visual Studio for Mac 的 Alpha 通道上。 Visual Studio 不会检查 Xamarin.iOS SDK 和 Xcode 是否存在且版本兼容。
>   这将由生成代理进行检查（生成的是生成错误）；并由 iOS 设计器代理进行检查（生成的是设计器错误）。

### <a name="connecting-to-the-mac"></a>连接到 Mac

#### <a name="mac-setup"></a>Mac 安装程序

若要设置 Mac 主机，必须启用 Visual Studio 和 Mac 的 Xamarin 扩展之间的通信。 若要执行此操作，请按照以下步骤允许在 Mac 上“远程登录”：

1. 打开 Spotlight (**⌘-Space**)，搜索“远程登录”，然后选择“共享”结果。 这将打开“共享”面板中的“系统首选项”：

   [![用于远程登录的 Spotlight 搜索](images/spotlight.png)](images/spotlight.png#lightbox)

2. 在左侧的“服务”列表中勾选“远程登录”选项以允许适用于 Visual Studio 的 Xamarin 连接到 Mac：

   [![选中“服务”列表中的“远程登录”选项](images/sharing.png)](images/sharing.png#lightbox)

3. 确保“远程登录”设置为“允许所有用户”访问，或确保 Mac 用户名或组包含在右侧的允许用户列表中。

除此之外，如果 OS X 防火墙默认设置为阻止已签名的应用程序，则可能需要请允许 `mono-sgen` 接收传入的连接。 若出现此情况，系统将显示警报对话框提示用户。

假设 Mac 上有当前打开的会话，如果此会话位于同一网络，则应可被 Visual Studio 发现。

Visual Studio 将在 Mac 上启动和停止代理，因此用户无需运行其他组件。

### <a name="windows-setup"></a>Windows 安装程序

请确保在 Windows 计算机上[安装](~/ios/get-started/installation/windows/index.md) Xamarin 工具。

### <a name="connecting-to-the-mac-build-host"></a>连接到 Mac 生成主机

可使用两种方法连接 Mac 生成主机：

在 iOS 工具栏上：

[![iOS 工具栏](images/image1.png)](images/image1.png#lightbox)

或在 Visual Studio 中，浏览到“工具”>“选项”，选择“Xamarin”>“iOS 设置”，然后单击“查找 Xamarin Mac 代理”按钮：

[![查找 Xamarin Mac 代理](images/image2.png)](images/image2.png#lightbox)

使用任一方法都将出现“Mac 代理”对话框，如下图所示：

[![“Mac 代理”对话框](images/image3.png)](images/image3.png#lightbox)

这将显示所有计算机的列表，包括之前连接并存储为已知的计算机，以及可“远程登录”的计算机。

双击 Mac 将其选中以与其连接。 首次连接 Mac 时，系统将提示输入 Mac 用户凭据，才能允许远程连接：

[![输入 Mac 用户凭据](images/image4.png)](images/image4.png#lightbox)

代理将使用这些凭据创建到 Mac 的新 SSH 连接。 如果成功，将创建 SSH 密钥，并将其在该 Mac 上的 `authorized_keys` 文件中[注册](#commandline)。 在后续连接上，代理将使用用户名和密钥文件连接到最近连接的已知生成主机。

> [!NOTE]
> 输入凭据时，必须使用“用户名”，而不是“全名”。  可通过在终端中使用 `whoami` 命令找到它。  例如，在下面的屏幕截图中，帐户名称是“amyb”，而不是“Amy Burns”：
>
> ![在终端应用中查找用户名](images/image5.png)

成功建立连接后，它将在“主机选择”对话框中显示，旁边有一个**已连接**图标，如下所示：

[![旁边有“已连接”图标的“主机选择”对话框](images/image6.png)](images/image6.png#lightbox)

在任意时刻只能有一个连接的 Mac。

右键单击列表中的每台计算机（无论是否连接）都会显示上下文菜单，允许根据需要选择“连接”、“断开连接”或“忘记此 Mac”：

[![“连接”、“断开连接”或“忘记此 Mac”上下文菜单](images/image7.png)](images/image7.png#lightbox)

如果选择“忘记此 Mac”，下次连接时需要重新输入凭据。

<a name="manual-add"/>

### <a name="manually-adding-a-mac"></a>手动添加 Mac

在某些情况下，如果在“主机选择”对话框看不到 Mac 的 mDNS 名称列出，不妨手动添加。 为此，请执行以下步骤：

1. 在 Mac 上浏览到“系统首选项”>“共享”>“远程登录”，可找到你的 Mac IP 地址：

   [![“系统首选项”中的 Mac IP 地址](images/image8.png)](images/image8.png#lightbox)

   或者，如果偏好使用命令行，可通过在终端中输入 `ipconfig getifaddr en0` 以找到你的 IP 地址（注意，根据不同的连接类型，变量可能为 `en1`、`en2` 等）：

   [![终端应用中的 IP 地址](images/image9.png)](images/image9.png#lightbox)

2. 返回 Visual Studio，在“主机选择”对话框中，选择“添加 Mac...”：

   [![“主机选择”对话框](images/image10.png)](images/image10.png#lightbox)

3. 在“添加 Mac”对话框中输入 Mac IP 地址，然后单击“添加”：

   [![将 Mac 的 IP 地址输入到“添加 Mac”对话框](images/image11.png)](images/image11.png#lightbox)

4. 最后，输入 Mac 管理员帐户的用户名（不是全名）以及相应密码：

   [![输入用户名和密码](images/image12.png)](images/image12.png#lightbox)

单击“登录”后，Visual Studio 将使用 SSH 登录到 Mac 计算机，并会将此 Mac 添加为已知计算机。

<a name="commandline"/>

### <a name="command-line-support"></a>命令行支持

此外，代理还支持从命令行生成 Xamarin.iOS 配置。  若要使用，请将以下所需的参数传递到 MSBuild：

- `ServerAddress` - Mac 服务器的 IP 地址。

- `ServerUser` - 用于登录 Mac 服务器的用户名（不是全名）。

- `ServerPassword` - 用于登录 Mac 主句的密码（可选）。

`ServerPassword` 参数不是必需的。

相反，如果是第一次传递密码（无论通过使用 Visual Studio 还是命令行），对于特定 Windows、Mac 和用户配置，将在 Windows 计算机上生成并存储密钥对，以供以后使用。 它位于 **%localappdata%\Xamarin\MonoTouch\id_rsa**。  如果没有传递 `ServerPassword` 参数，`id_rsa` 密钥文件将用于身份验证。

以下是使用 **xamUser** 帐户和密码 **mypassword** 连接到 Mac 10.211.55.2 的示例命令：

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

### <a name="summary"></a>总结

本文介绍了 Visual Studio 与 iOS 生成之间的连接以及 Mac 上的设计器工具，可允许使用 Visual Studio 生成 Xamarin.iOS 应用。

### <a name="related-links"></a>相关链接

- [安装](~/ios/get-started/installation/windows/index.md)
- [连接疑难解答](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [使用 XMA 将 Mac 连接到 Visual Studio 环境（视频）](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
