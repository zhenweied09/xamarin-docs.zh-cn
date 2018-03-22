---
title: "沙盒处理 Xamarin.Mac 应用"
description: "本文介绍如何沙盒处理用于在应用商店上发布的 Xamarin.Mac 应用程序。 它涵盖的所有元素转到沙盒处理，如容器目录、 权利，确定用户的权限、 权限分离和内核强制。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 71f076bb0dc7d552a041d48a6ce531dcff444de6
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="sandboxing-a-xamarinmac-app"></a>沙盒处理 Xamarin.Mac 应用

_本文介绍如何沙盒处理用于在应用商店上发布的 Xamarin.Mac 应用程序。它涵盖的所有元素转到沙盒处理，如容器目录、 权利，确定用户的权限、 权限分离和内核强制。_

## <a name="overview"></a>概述

在处理时使用 C# 和.NET Xamarin.Mac 应用程序中，可以对沙盒应用程序的相同功能使用 OBJECTIVE-C 或 Swift 打开时一样。

[![正在运行的应用程序示例](sandboxing-images/intro01.png "正在运行的应用程序示例")](sandboxing-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用沙盒处理 Xamarin.Mac 应用程序及其所有进入沙盒处理的元素中的基础知识： 容器目录、 权利、-确定用户的权限、 权限分离和内核强制。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性用于连接你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。

## <a name="about-the-app-sandbox"></a>有关应用沙盒

应用沙盒提供强防范可能引起运行在 Mac 上通过限制应用程序对系统资源的访问权限的恶意应用程序的损坏。

非沙盒应用程序拥有完全控制权限的用户的运行应用程序和可以访问或执行任何操作，用户可以。 如果此应用程序包含安全漏洞 （或正在使用的任何框架），黑客可以可能利用这些安全漏洞，并使用应用程序的 Mac 上运行的控制。

通过限制对基于每个应用程序的资源的访问，沙盒应用程序提供一系列针对被盗、 损坏或恶意用户的计算机上运行的应用程序部分的防御措施。

应用沙盒是一种内置提供具有双重策略的 macOS （在内核级别强制执行） 的访问控制技术：

1. 应用沙盒使得开发人员可以描述_如何_与操作系统以及在这种方式，应用程序将进行交互，它被授予和都需要来获取作业完成后，没有更多的访问权限。
2. 应用沙盒允许用户无缝地授予对通过打开系统的进一步访问权限和保存对话框、 拖放操作和其他常见的用户交互。

### <a name="preparing-to-implement-the-app-sandbox"></a>准备实现应用沙盒

将在文章中详细讨论应用沙盒的元素如下所示：

- 容器目录
- 权利
- 确定用户的权限
- 特权分离
- 内核强制

了解这些详细信息后，你将能够为采用应用沙盒 Xamarin.Mac 应用程序中创建计划。

首先，你将需要确定你的应用程序是否适合进行沙盒处理 （大多数应用程序是）。 接下来，你将需要解决任何 API 不兼容问题，并确定你将需要应用程序在沙盒哪些元素。 最后，考虑使用特权分离以最大化你应用程序的防御级别。

当采用应用沙盒，某些应用程序使用的文件系统位置将会不同。 具体来说，是你的应用程序将有一个将用于应用程序支持文件、 数据库、 缓存和不是用户文档的任何其他文件的容器目录。 MacOS 和 Xcode 提供支持，以将这些类型的文件从旧位置迁移到容器。

## <a name="sandboxing-quick-start"></a>沙盒处理快速入门

在此部分中，我们将作为示例，了解如何开始使用应用沙盒中创建的简单 Xamarin.Mac 应用程序将使用 Web 视图 （它需要受到限制，在沙盒处理除非明确请求的网络连接）。

我们将验证应用程序实际沙盒，并且了解如何进行排除故障并解决常见应用沙盒错误。

### <a name="creating-the-xamarinmac-project"></a>创建 Xamarin.Mac 项目

让我们执行以下操作来创建我们的示例项目：

1. 针对 Mac 和单击启动 Visual Studio**新解决方案...** 列表中。
2. 从**新项目**对话框中，选择**Mac** > **应用** > **Cocoa 应用**: 

    [![创建新 Cocoa 应用](sandboxing-images/sample01.png "创建新 Cocoa 应用程序")](sandboxing-images/sample01-large.png#lightbox)
3. 单击**下一步**按钮，输入`MacSandbox`为该项目名称并单击**创建**按钮： 

    [![输入应用名称](sandboxing-images/sample02.png "输入应用名称")](sandboxing-images/sample02-large.png#lightbox)
4. 在**解决方案 Pad**，双击**Main.storyboard**文件以进行编辑在 Xcode 中打开它： 

    [![编辑主情节提要](sandboxing-images/sample03.png "编辑主情节提要")](sandboxing-images/sample03-large.png#lightbox)
5. 拖动**Web 视图**到窗口中，调整其大小以填充内容的区域，并将其设置为增加和减少与窗口： 

    [![添加 web 视图](sandboxing-images/sample04.png "添加 web 视图")](sandboxing-images/sample04-large.png#lightbox)
6. 创建名为 web 视图的电源插座`webView`: 

    [![创建一个新插座](sandboxing-images/sample05.png "创建一个新插座")](sandboxing-images/sample05-large.png#lightbox)
7. 返回到 Visual Studio 针对 Mac 和双击**ViewController.cs**文件中**解决方案 Pad**以将其打开以进行编辑。
8. 添加以下 using 语句： `using WebKit;`
9. 请`ViewDidLoad`方法外观如下所示： 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. 保存更改。

运行应用程序，并确保 Apple 网站显示在窗口中，如下所示：

[![显示示例应用程序运行](sandboxing-images/sample06.png "显示示例应用程序运行")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>签名和预配应用程序

我们可以启用应用程序沙盒之前，我们首先需要设置和我们 Xamarin.Mac 应用程序签名。

让以下操作：

1. 登录到 Apple 开发人员门户： 

    [![登录到 Apple 开发人员门户](sandboxing-images/sign01.png "登录到 Apple 开发人员门户")](sandboxing-images/sign01-large.png#lightbox)
2. 选择**证书、 标识符和配置文件**: 

    [![选择证书、标识符和配置文件](sandboxing-images/sign02.png "选择证书、标识符和配置文件")](sandboxing-images/sign02-large.png#lightbox)
3. 下**Mac 应用**，选择**标识符**: 

    [![选择标识符](sandboxing-images/sign03.png "选择标识符")](sandboxing-images/sign03-large.png#lightbox)
4. 创建应用程序的新 ID: 

    [![创建新的应用程序 ID](sandboxing-images/sign04.png "创建新的应用程序 ID")](sandboxing-images/sign04-large.png#lightbox)
5. 下**预配配置文件**，选择**开发**: 

    [![选择开发](sandboxing-images/sign05.png "选择开发")](sandboxing-images/sign05-large.png#lightbox)
6. 创建新的配置文件，然后选择**Mac 应用程序开发**: 

    [![创建新的配置文件](sandboxing-images/sign06.png "创建新的配置文件")](sandboxing-images/sign06-large.png#lightbox)
7. 选择我们在上面创建的应用程序 ID: 

    [![选择的应用程序 ID](sandboxing-images/sign07.png "选择的应用程序 ID")](sandboxing-images/sign07-large.png#lightbox)
8. 选择此配置文件的开发人员： 

    [![添加开发人员](sandboxing-images/sign08.png "添加开发人员")](sandboxing-images/sign08-large.png#lightbox)
9. 选择此配置文件的计算机： 

    [![选择允许的计算机](sandboxing-images/sign09.png "选择允许的计算机")](sandboxing-images/sign09-large.png#lightbox)
10. 为配置文件提供一个名称： 

    [![为配置文件提供一个名称](sandboxing-images/sign10.png "给提供的名称的配置文件")](sandboxing-images/sign10-large.png#lightbox)
11. 单击**完成**按钮。

> [!IMPORTANT]
> 在某些情况下你可能需要直接从 Apple 的开发人员门户下载新的预配配置文件，并双击它以安装。 你可能还需要停止并重启 Visual Studio for Mac 之前它将能够访问新的配置文件。

接下来，我们需要在开发计算机上加载新的应用程序 ID 和配置文件。 让我们执行以下操作：

1. 启动 Xcode，然后选择**首选项**从**Xcode**菜单： 

    ![编辑在 Xcode 中的帐户](sandboxing-images/sign11.png "编辑在 Xcode 中的帐户")
2. 单击**查看详细信息...**按钮： 

    ![单击查看详细信息按钮](sandboxing-images/sign12.png "单击查看详细信息按钮")
3. 单击**刷新**按钮 （在左下角）。
4. 单击**完成**按钮。

接下来，我们需要我们 Xamarin.Mac 项目中选择新的应用程序 ID 和预配配置文件。 让我们执行以下操作：

1. 在**解决方案 Pad**，双击**Info.plist**文件以打开进行编辑。
2. 确保**捆绑标识符**匹配我们我们在上面创建的应用程序 ID (示例： `com.appracatappra.MacSandbox`): 

    [![编辑的捆绑标识符](sandboxing-images/sign13.png "编辑的捆绑标识符")](sandboxing-images/sign13-large.png#lightbox)
3. 接下来，双击**Entitlements.plist**文件，并确保我们**iCloud 键-值存储**和**iCloud 容器**所有匹配我们我们在上面创建的应用程序 ID (示例：`com.appracatappra.MacSandbox`): 

    [![编辑 Entitlements.plist 文件](sandboxing-images/sign17.png "编辑 Entitlements.plist 文件")](sandboxing-images/sign17-large.png#lightbox)
3. 保存更改。
4. 在**解决方案 Pad**，双击要打开以进行编辑其选项的项目文件：  

    ![Editign 解决方案的选项](sandboxing-images/sign14.png "Editign 解决方案的选项")
5. 选择**Mac 签名**，然后检查**登录应用程序捆绑包**和**签名的安装程序包**。 下**预配配置文件**，请选择我们在上面创建的一个： 

    ![设置预配配置文件](sandboxing-images/sign15.png "设置预配配置文件")
6. 单击**完成**按钮。

> [!IMPORTANT]
> 你可能必须退出并重新启动 Visual Studio for Mac 来获取它，能够识别新的应用程序 ID 和预配配置文件已安装 Xcode。

#### <a name="troubleshooting-provisioning-issues"></a>设置问题疑难解答

此时应尝试运行该应用程序，并确保所有内容都是签名并正确设置。 如果应用程序仍在运行前面一样，所有内容都很好。 出现故障，可能会出现类似以下对话框中：

[![设置问题对话框示例](sandboxing-images/sign16.png "设置问题对话框示例")](sandboxing-images/sign16-large.png#lightbox)

以下是最常见的预配和签名问题的原因：

- 应用捆绑 ID 与所选配置文件的应用程序 ID 不匹配。
- 开发人员 ID 与所选配置文件的开发人员 ID 不匹配。
- Mac 上进行测试未注册的所选配置文件一部分的 UUID。

对于出现问题，更正 Apple 开发人员门户上的问题，请刷新在 Xcode 中的配置文件，并执行干净的生成，在 Visual Studio for mac。

### <a name="enable-the-app-sandbox"></a>启用应用程序沙盒

通过在你项目的选项中选择一个复选框启用应用程序沙盒。 请执行以下操作：

1. 在**解决方案 Pad**，双击**Entitlements.plist**文件以打开进行编辑。
2. 同时检查**启用的权利**和**启用应用程序沙盒**: 

    [![编辑权利和启用沙盒处理](sandboxing-images/sign17.png "编辑权利和启用沙盒处理")](sandboxing-images/sign17-large.png#lightbox)
3. 保存更改。

此时，已启用应用程序沙盒，但是你未提供所需的网络访问的 Web 视图。 如果你运行应用程序现在，你会获得一个空白的窗口：

[![被阻止的 web 访问](sandboxing-images/sample08.png "被阻止的 web 访问")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>验证应用程序进行沙箱处理

阻止行为的资源，除了有三种主要方法来告诉 Xamarin.Mac 应用程序是否已被成功沙盒：

1. 在查找工具中，请检查内容`~/Library/Containers/`文件夹-如果应用程序为沙盒解决方案，将有类似应用的捆绑标识符名为的文件夹 (示例： `com.appracatappra.MacSandbox`): 

    [![打开应用的捆绑](sandboxing-images/sample09.png "打开应用的捆绑包")](sandboxing-images/sample09-large.png#lightbox)
2. 系统将其视为活动监视器在沙盒应用程序：
    - 启动活动监视器 (下`/Applications/Utilities`)。 
    - 选择**视图** > **列**并确保**沙盒**选中菜单项。
    - 确保沙盒列读取`Yes`你的应用程序： 

    [![检查活动监视器中的应用程序](sandboxing-images/sample10.png "检查活动监视器中的应用程序")](sandboxing-images/sample10-large.png#lightbox)
3. 检查应用程序二进制沙盒：
    - 启动终端应用程序。
    - 导航到应用程序`bin`目录。
    - 发出此命令： `codesign -dvvv --entitlements :- executable_path` (其中`executable_path`是你的应用程序的路径): 

    [![检查命令行上的应用](sandboxing-images/sample11.png "检查命令行上的应用")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>调试沙盒应用程序

调试器连接到 Xamarin.Mac 应用的整个 TCP，这意味着，默认情况下启用后沙盒处理，它将无法连接到应用程序，因此如果你尝试在不适当的权限启用的情况下运行应用程序，你将收到一个错误*"无法连接到调试器"*。 

[![设置所需的选项](sandboxing-images/debug01.png "设置所需的选项")](sandboxing-images/debug01-large.png#lightbox)

**允许传出网络连接 （客户端）**权限是所需的调试器，启用此操作将允许通常调试。 你无法调试没有它，因为我们已更新`CompileEntitlements`目标`msbuild`自动将该权限添加到权利，为仅是沙盒处理以用于调试任何应用版本。 发布版本应使用在权利文件中，不做任何修改指定的权利。

### <a name="resolving-an-app-sandbox-violation"></a>解决了应用沙盒冲突

如果应用程序尝试访问具有的资源的沙盒 Xamarin.Mac 不显式允许，则会发生应用沙盒冲突。 例如，我们 Web 视图不再能够显示 Apple 网站。

指定 Visual Studio 中适用于 Mac 的授权设置不匹配的应用程序的要求时发生应用沙盒冲突的最常见的源。 同样，回本示例中，缺少的网络连接 Web 视图防止工作的权利。

#### <a name="discovering-app-sandbox-violations"></a>发现应用沙盒冲突

如果你怀疑应用沙盒冲突发生 Xamarin.Mac 应用程序中，发现此问题的最快方法是使用**控制台**应用。

请执行以下操作：

1. 编译问题的应用并从 Visual Studio 运行为 mac。
2. 打开**控制台**应用程序 (从`/Applications/Utilties/`)。
3. 选择**所有消息**侧栏中，然后输入`sandbox`在搜索中： 

    [![在控制台中的沙盒处理问题的一个示例](sandboxing-images/resolve01.png "控制台中的沙盒处理问题的示例")](sandboxing-images/resolve01-large.png#lightbox)

对于我们上面的示例应用程序，您可以看到正在阻止 Kernal`network-outbound`由于应用沙盒，因为我们不已请求该权限的流量。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>修复应用沙盒与权利相关的冲突

现在，我们已了解如何查找应用沙盒处理冲突，让我们看看如何可以通过调整我们的应用程序的权利解决它们。

请执行以下操作：

1. 在**解决方案 Pad**，双击**Entitlements.plist**文件以打开进行编辑。
2. 下**权利**部分，选中**允许传出网络连接 （客户端）**复选框： 

    [![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)
3. 将所做的更改保存到应用程序。

如果我们执行上述操作为我们的示例应用程序，然后生成并运行它，web 内容将立即显示按预期方式。

## <a name="the-app-sandbox-in-depth"></a>深入的沙盒应用程序

提供应用沙盒的访问控制机制是几个并易于理解。 但是，每个应用程序将采用应用沙盒的方式是唯一，并且基于应用程序要求。

给定防止 Xamarin.Mac 应用程序正在被恶意代码利用你最大努力，需要有单个漏洞任一应用程序中 （或它所消耗的库或框架中的一个） 能够控制与应用程序的交互系统。

应用沙盒旨在防止接管 （或限制它可能会导致的损害），它允许你指定与系统的应用程序的预期的交互。 系统将仅授予应用程序以获取完成其工作所需的资源而不安装其他访问权限。

应用沙盒的设计时，您正在设计为最糟糕的情况。 如果应用程序未受到安全威胁的恶意代码，它被限制为访问的文件和应用程序的沙盒中的资源。

### <a name="entitlements-and-system-resource-access"></a>权利和系统资源访问

正如我们所看到上面，尚未沙盒 Xamarin.Mac 应用程序被授予完全控制权限和运行应用程序的用户的访问。 如果受到恶意代码，未受保护的应用程序可以使用范围可能会执行损害范围内充当对于恶意行为，代理中。

通过启用应用程序沙盒，在删除所有除最少的一组的您的权限，然后重新启用基于需要仅使用 Xamarin.Mac 应用的权利。 

通过编辑修改应用程序的应用程序沙盒资源其**Entitlements.plist**文件和检查或选择的编辑器下拉框从所需的权限：

[![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器目录和文件系统访问权限

当你 Xamarin.Mac 的应用程序采用应用沙盒时，它有权访问以下位置：

- **对应用程序容器目录**的第一个运行时，操作系统将创建一个特殊_容器目录_它的所有资源转，只有它可以访问的位置。 应用程序会向此目录的完全读/写访问。
- **应用程序组容器目录**-您的应用程序可以被授予访问权限的一个或多_组容器_同一组需要在应用之间共享的。
- **用户指定的文件**-你的应用程序会自动获得对显式打开或拖放和放置到应用程序用户的文件的访问。
- **相关项**-与合适的授权，你的应用程序可以访问具有相同名称但不同的扩展名的文件。 例如，文档已保存为这两者`.txt`文件和一个`.pdf`。
- **临时目录、 命令行工具目录和 world 可读的特定位置**-你的应用程序中其他定义明确的位置由系统指定的文件的不同程度的访问。

#### <a name="the-app-container-directory"></a>对应用程序容器目录

Xamarin.Mac 应用程序的应用程序容器目录具有以下特征：

- 在用户的主目录中的隐藏位置 (通常`~Library/Containers`) 并可通过访问`NSHomeDirectory`应用程序中的函数 （见下文）。 因为它是主目录中，每个用户对应用程序将获得自己的容器。
- 应用程序可以无限制读/写访问容器目录及其所有子目录和文件中。
- 大部分 macOS 的路径查找 Api 都是相对于应用程序的容器。 例如，容器将有其自己的**库**(通过访问`NSLibraryDirectory`)，**应用程序支持**和**首选项**子目录。
- macOS 建立并强制执行之间的连接和应用程序和通过代码签名其容器。 即使是另一个应用尝试使用欺骗应用其**捆绑标识符**，它将不能访问容器，因为代码签名。
- 容器不为用户生成的文件中。 而是它为你的应用程序使用如数据库、 缓存或其他特定类型的数据的文件。
- 有关_鞋盒_类型的应用 （比如 Apple 的照片应用），用户的内容将转到容器。

> [!IMPORTANT]
> 遗憾的是，Xamarin.Mac 不具有 100 %api 覆盖率尚未 （而不像 Xamarin.iOS)，因此`NSHomeDirectory`Xamarin.Mac 的当前版本中尚未映射 API。

临时的解决方法，你可以使用下面的代码：

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>应用程序组容器目录

应用程序第一页为 Mac macOS 10.7.5 （和更高版本），可以使用`com.apple.security.application-groups`授权访问普遍适用于组中的所有应用程序共享的容器。 对于非用户面向内容，如数据库或其他类型的支持文件 （例如缓存），你可以使用此共享的容器。

（如果它们是组的一部分），组容器会自动添加到每个应用程序的沙盒容器和存储在`~/Library/Group Containers/<application-group-id>`。 组 ID_必须_开始使用你的开发团队 ID 和一个句点，例如：

```plist
<team-id>.com.company.<group-name>
```

有关详细信息，请参阅 Apple 的[应用程序添加到应用程序组](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)中[授权密钥引用](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>在对应用程序容器之外的 Powerbox 和文件系统访问

沙盒 Xamarin.Mac 应用程序可以通过以下方式访问该容器以外的文件系统位置：

- 在用户 （通过打开和保存对话框或其他方法，例如拖放） 的特定方向。
- 通过使用适用于特定的文件系统位置权利 (如`/bin`或`/usr/lib`)。
* 在某些特定目录是完全可读 （例如共享） 时的文件系统位置。

_Powerbox_是 macOS 替换进行展开沙盒 Xamarin.Mac 应用的文件访问权限的用户交互的安全技术。 Powerbox 不具有任何 API，但该应用程序调用时，将以透明方式激活`NSOpenPanel`或`NSSavePanel`。 通过设置为 Xamarin.Mac 应用程序的权利启用 Powerbox 访问。

时沙盒应用程序显示已打开或保存对话框时，窗口将提供通过 Powerbox （并不 AppKit），并因此有权访问任何文件或目录用户有权访问。

当用户选择文件或目录从打开或保存对话框 （或拖动到应用程序的图标是） 时，Powerbox 就会将关联的路径添加到应用程序的沙盒中。

此外，系统自动允许以下沙盒应用程序：

- 系统连接输入方法。
- 调用服务从用户选择**服务**菜单 (仅为服务标记为_安全的沙盒应用_服务提供商)。
- 通过从用户选择打开的文件**最近打开**菜单。
- 使用其他应用程序之间的复制和粘贴。
- 从以下位置完全公开读取文件：
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- 读取和写入文件中创建的目录`NSTemporaryDirectory`。

为默认，打开或沙盒 Xamarin.Mac 应用所保存的文件之前仍然能够访问应用程序终止 （除非应用程序退出时，该文件已仍处于打开状态）。 打开的文件将自动还原到通过 macOS 恢复功能的应用程序的沙盒应用程序将启动下一次。

若要提供对位于外部 Xamarin.Mac 应用程序的容器的文件的持久性，使用安全作用域书签 （见下文）。

#### <a name="related-items"></a>相关的项

应用沙盒允许应用程序需要访问具有相同的文件名，但不同的扩展的相关的项。 该功能由两部分组成： a） 的相关扩展的应用程序的列表`Info.plst`文件，b） 代码，以告诉沙盒什么应用程序将执行使用这些文件。

有两种方案，这是有意义：

1. 应用程序需要能够进行保存 （用新的扩展名） 文件的不同版本。 例如，导出`.txt`文件为`.pdf`文件。 若要处理这种情况下，你必须使用`NSFileCoordinator`访问该文件。 将调用`WillMove(fromURL, toURL)`方法首先，将文件移动到新的扩展，然后调用`ItemMoved(fromURL, toURL)`。
2. 应用程序需要使用不同的扩展中打开一个扩展名的主要文件和几个支持文件。 例如，影片和副标题文件。 使用`NSFilePresenter`来访问辅助文件。 提供的主文件`PrimaryPresentedItemURL`属性和辅助文件到`PresentedItemURL`属性。 主要文件打开时，调用`AddFilePresenter`方法`NSFileCoordinator`类注册辅助文件。

在这两种情况下，应用程序的**Info.plist**文件必须声明应用程序可以打开的文档类型。 对于任何文件类型，添加`NSIsRelatedItemType`(值为`YES`) 中的条目到`CFBundleDocumentTypes`数组。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>打开和保存与沙盒应用程序的对话框行为

以下限制位于`NSOpenPanel`和`NSSavePanel`时调用这些方法从沙盒 Xamarin.Mac 应用：

- 不能以编程方式调用**确定**按钮。
- 不能以编程方式更改中的用户的选择`NSOpenSavePanelDelegate`。

此外，以下继承修改位于的位置：

- **非沙盒应用程序** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全作用域书签和永久资源访问

如上面所述，沙盒 Xamarin.Mac 应用程序可以访问的文件或通过直接用户交互 （如由 PowerBox） 该容器以外的资源。 但是，这些资源的访问权限不会自动保留跨应用程序启动或系统重新启动。

通过使用_Security-Scoped 书签_，沙盒 Xamarin.Mac 应用程序可以保留用户的意图和维护访问给定的资源应用程序后重新启动。

#### <a name="security-scoped-bookmark-types"></a>安全作用域书签类型

当使用 Security-Scoped 书签和持续资源访问，有两个 sistine 用例：

- **App-Scoped 书签提供持久性访问用户指定的文件或文件夹。** 

    例如，如果沙盒 Xamarin.Mac 应用程序允许用于打开以进行编辑的外部文档 (使用`NSOpenPanel`)，应用程序可以创建 App-Scoped 书签，以便它可以在以后再次访问同一文件。
- **Document-Scoped 书签提供特定文档持续地访问子文件。** 

例如，视频的编辑应用程序创建的项目文件有权访问单个图像、 视频剪辑和更高版本将合并到单个电影的声音文件。

当用户导入项目中的资源文件 (通过`NSOpenPanel`)，应用程序创建在项目中，存储的项 Document-Scoped 书签，以便文件始终可访问到应用。

可以通过任何应用程序可以打开书签数据和文档本身解决 Document-Scoped 书签。 此设置支持可移植性，从而允许用户将项目文件发送到另一个用户并具有的所有书签也适用于它们。

> [!IMPORTANT]
> Document-Scoped 书签可以_仅_指向单个文件而不是文件夹，并且该文件不能在系统使用的位置 (如`/private`或`/Library`)。

#### <a name="using-security-scoped-bookmarks"></a>使用安全作用域的书签

使用其中任何一种 Security-Scoped 书签，需要你执行以下步骤：

1. **需要使用 Security-Scoped 书签 Xamarin.Mac 应用中设置的相应权利**-For App-Scoped 书签设置`com.apple.security.files.bookmarks.app-scope`授权密钥到`true`。 对于 Document-Scoped 书签设置`com.apple.security.files.bookmarks.document-scope`授权密钥到`true`。
2. **创建 Security-Scoped 书签**-你需要执行此操作的任何文件或用户提供访问的文件夹 (通过`NSOpenPanel`例如)，应用程序需要持续地访问。 使用`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`方法`NSUrl`类，以创建书签。
3. **解决 Security-Scoped 书签**-在应用程序需要 （例如，在重新启动后） 访问该资源再次时，它将需要解析为安全作用域的 URL 的书签。 使用`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`方法`NSUrl`类来解析书签。
4. **显式通知你想要从 Security-Scoped URL 访问文件系统**-此步骤需要获取上述 Security-Scoped URL 后立即执行或当你以后要具有后重新获得对资源的访问放弃您对其访问权限。 调用`StartAccessingSecurityScopedResource ()`方法`NSUrl`类来开始访问 Security-Scoped URL。
5. **显式通知你已完成的系统访问的文件从 Security-Scoped URL** -越早越好，应通知系统，当应用不再需要对文件的访问 （例如，如果用户将其关闭） 时。 调用`StopAccessingSecurityScopedResource ()`方法`NSUrl`类，用于中止访问 Security-Scoped URL。

放弃对资源的访问后，你将需要返回到步骤 4 以重新建立访问。 如果重新启动 Xamarin.Mac 应用时，必须返回到步骤 3，并将书签重新解析。

> [!IMPORTANT]
> 未能释放 Security-Scoped URL 资源的访问权限将导致用于泄漏内核资源的 Xamarin.Mac 应用。 因此，应用程序将不再能够将文件系统位置添加到其容器中，直到它重新启动。

### <a name="the-app-sandbox-and-code-signing"></a>应用沙盒和代码签名

启用应用程序沙盒并启用的特定要求 （通过权利） Xamarin.Mac 应用后，你必须代码签名项目进行沙盒处理才会生效。 你必须执行的代码签名，因为所需的应用程序沙盒处理的权利链接到应用程序的签名。 

macOS 强制实施应用程序的容器和其代码签名，以任何其他应用程序可以访问该容器中，即使它正在欺骗应用捆绑包 id。 这种方式之间的链接 此机制的工作原理，如下所示：

1. 当系统创建应用程序的容器时，它将设置_访问控制列表_(ACL) 对该容器。 初始访问控制项列表中的包含应用程序的_指定要求_(DR)，该主题描述了如何将来版本的应用程序可以 （如果已升级） 识别。
2. 具有相同的捆绑 ID 的应用启动时，每次系统将检查应用程序的代码签名，符合指定要求一个容器的 ACL 中的条目中指定。 如果系统未找到匹配项，它阻止应用启动。

代码签名工作原理如下：

1. 在创建之前 Xamarin.Mac 项目，从 Apple 开发人员门户中获取开发证书、 分发证书和开发人员 ID 证书。
2. Mac 应用商店分发 Xamarin.Mac 应用程序，它使用 Apple 代码签名处进行签名。

时测试和调试，您将会使用已签名 （这将用于创建应用程序容器） Xamarin.Mac 应用程序的版本。 更高版本，如果你想要测试或从 Apple 应用商店安装的版本，它将使用的 Apple 签名进行签名，并且将无法启动 （因为它不能与原始的应用程序容器相同的代码签名）。 在此情况下，您将收到类似于以下的崩溃报告：

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要解决此问题，你将需要调整 ACL 条目以指向应用程序的 Apple 签名版本。

有关创建和下载所需的沙盒处理的预配配置文件的详细信息，请参阅[签名和预配应用](#Signing_and_Provisioning_the_App)上面一节。

#### <a name="adjusting-the-acl-entry"></a>调整 ACL 条目

若要允许 Xamarin.Mac 应用运行的 Apple 签名的版本，请执行以下操作：

1. 打开终端应用 (在`/Applications/Utilities`)。
2. 打开一个到 Xamarin.Mac 应用的 Apple 签名版本的查找工具窗口。
3. 类型`asctl container acl add -file `终端窗口中。
4. 从查找工具窗口 Xamarin.Mac 应用程序的图标拖放在终端窗口上。
5. 该文件的完整路径将添加到终端中的命令。
6. 按**Enter**以执行该命令。

容器的 ACL 现在包含两个版本的 Xamarin.Mac 应用程序的指定的代码要求和 macOS 现在允许版本以运行。

#### <a name="display-a-list-of-acl-code-requirements"></a>显示 ACL 代码要求的列表

可以通过执行以下操作来查看容器的 ACL 中的代码要求列表：

1. 打开终端应用 (在`/Applications/Utilities`)。
2. 键入 `asctl container acl list -bundle <container-name>`。
3. 按**Enter**以执行该命令。

`<container-name>`通常是 Xamarin.Mac 应用的捆绑标识符。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>应用沙盒设计 Xamarin.Mac 的应用

没有常见的应用程序沙盒设计 Xamarin.Mac 应用程序时应遵循的工作流。 也就是说，在应用程序中实施沙盒处理细节会成为唯一的给定的应用的功能。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>采用应用沙盒的六个步骤

通常应用沙盒设计 Xamarin.Mac 的应用包括以下步骤：

1. 确定应用是否适用于沙盒处理。
2. 设计一个开发和分发策略。
3. 解决任何 API 不兼容问题。
4. 适用于 Xamarin.Mac 项目所需的应用沙盒权利。
5. 添加使用 XPC 特权分离。
6. 实现迁移策略。

> [!IMPORTANT]
> 你必须不仅沙盒应用程序捆绑包，但也是每个包含的帮助器主可执行文件的应用程序或该捆绑中的工具。 这需要的任何应用程序，从 Mac 应用商店分发和，如果可能，应出于任何其他形式的应用分发。

Xamarin.Mac 应用的捆绑中的所有可执行二进制文件的列表，请在终端中键入以下命令：

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`是名称和应用程序的包路径。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>确定 Xamarin.Mac 应用是否适用于沙盒处理

大多数 Xamarin.Mac 应用并与应用沙盒完全兼容，因此，适用于沙盒处理。 如果应用所需的应用程序沙盒不允许的行为，则应考虑的备用方法。

如果你的应用程序需要以下行为之一，它与不兼容应用沙盒：

- **授权服务**-与应用沙盒中，您不能处理这两个函数中所述[授权服务 C 参考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)。
- **可访问性 Api** -无法沙盒辅助应用程序，例如屏幕阅读器或控制其他应用程序的应用。
- **将 Apple 事件发送到任意应用**-如果应用需要 Apple 事件发送到未知的任意应用程序，它不能进行沙盒处理。 有关调用应用程序的已知列表中，应用程序仍能沙盒和权利需要包括被调用的应用列表。
- **将用户信息字典中分发通知发送到其他任务**-与应用沙盒中，不能包括`userInfo`字典时发布到`NSDistributedNotificationCenter`消息传递其他任务的对象。
- **加载内核扩展**-加载的内核扩展禁止的应用程序沙盒。
- **模拟用户输入中打开和保存对话框**-以编程方式操作打开或保存对话框，以便模拟或更改用户输入是被禁止的应用程序沙盒。
- **访问或对其他应用程序的设置首选项**-操作的其他应用的设置禁止的应用程序沙盒。
- **配置网络设置**-操作网络设置禁止的应用程序沙盒。
- **终止其他应用**-应用沙盒禁止使用`NSRunningApplication`终止其他应用。

### <a name="resolving-api-incompatibilities"></a>解决 API 不兼容性

设计时 Xamarin.Mac 应用应用沙盒，可能会遇到的一些 macOS Api 的使用情况与不兼容。

下面是几个常见的问题和怎样才能解决它们的操作：

- **打开、 保存和跟踪文档**-如果你正在管理而不使用任何技术文档`NSDocument`，由于应用沙盒的内置支持应切换到它。 `NSDocument` 自动配合 PowerBox 并为保留在你的沙盒中的文档，如果用户将其移动在查找工具中提供支持。
- **保留到文件系统资源的访问权限**-如果应用程序依赖于对该容器以外的资源的永久访问 Xamarin.Mac 使用安全作用域书签来维持的访问。
- **为应用创建登录名项**-与应用沙盒中，无法创建项使用的登录名`LSSharedFileList`也可以操作使用的启动服务的状态不`LSRegisterURL`。 使用`SMLoginItemSetEnabled`函数同类中所述[添加使用服务管理框架的登录名项](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)文档。
- **访问用户数据**-如果你正在使用 POSIX 函数，如`getpwuid`若要从目录服务中获取用户的主目录，请考虑使用 Cocoa 或核心 Foundation 符号，如`NSHomeDirectory`。
- **访问其他应用首选项的**-因为应用沙盒指示路径查找 Api，到应用程序的容器，修改该容器中的首选项发生和访问中的另一个应用程序首选项不允许。 
- **在 Web 视图中使用 HTML5 嵌入视频**-如果 Xamarin.Mac 应用使用 WebKit 播放嵌入的 HTML5 视频，你还必须将针对 AV Foundation 框架应用链接。 应用沙盒将阻止 CoreMedia play 否则为这些视频。

### <a name="applying-required-app-sandbox-entitlements"></a>应用所需的应用沙盒权利

你将需要编辑为你想要在应用沙盒中运行，并检查任何 Xamarin.Mac 应用程序的权利**启用应用程序沙盒处理**复选框。

基于应用程序的功能，你可能需要启用其他操作系统功能或资源访问的授权。 应用沙盒处理效果就越好最大程度减少到运行你的应用程序所需的最低请求的权利因此不仅仅是以随机启用的权利。

若要确定 Xamarin.Mac 应用程序需要的权利，请执行以下操作：

1. 启用应用程序沙盒并运行 Xamarin.Mac 应用。
2. 通过应用的功能运行。
3. 打开控制台应用程序 (位于`/Applications/Utilities`)，并查找`sandboxd`中的冲突**所有消息**日志。
4. 每个`sandboxd`冲突，通过使用应用程序容器，而不其他文件系统位置解决问题还是应用应用沙盒权利，以允许访问受限制的 OS 功能。
5. 重新运行，然后再次测试所有 Xamarin.Mac 应用功能。
6. 重复直至将所有`sandboxd`冲突已得到解决。

### <a name="add-privilege-separation-using-xpc"></a>添加使用 XPC 特权分离

在开发时 Xamarin.Mac 应用应用沙盒中的术语特权和访问权限，查看应用程序的行为，则考虑将高风险操作划分自己 XPC 服务。

有关详细信息，请参阅 Apple 的[创建 XPC 服务](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)和[守护程序和服务编程指南](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>实施迁移策略

如果发布不是以前沙盒 Xamarin.Mac 应用程序的新沙盒版本，你将需要确保当前用户有平滑的升级路径。 

 有关如何实现容器迁移清单的信息，请参阅 Apple 的[将应用迁移到沙盒](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)文档。

## <a name="summary"></a>总结

本文已详细的介绍了的沙盒处理 Xamarin.Mac 应用程序。 首先，我们创建了只需 Xamarin.Mac 应用以显示应用程序在沙盒的基础知识。 接下来，我们介绍了如何解决沙盒冲突。 然后，我们需要深入查看应用沙盒，最后，我们讨论过应用沙盒设计 Xamarin.Mac 的应用。



## <a name="related-links"></a>相关链接

- [发布到 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [有关应用沙盒](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [常见应用沙盒处理问题](https://developer.apple.com/library/content/qa/qa1773/_index.html)
