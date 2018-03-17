---
title: Touch ID
description: "Touch ID 是 Apple 的指纹生物识别身份验证技术。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 3564b4f7d41822fdd9ab167fb3e756f26678a17b
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="touch-id"></a>Touch ID

作为一种进行身份验证的用户-类似于密码 iOS 7 中引入了 touch ID。 但是，它只限于解锁设备、 使用应用商店、 使用 iTunes 和 iCloud keychain 仅进行身份验证。

现在有两种方法可以使用 Touch ID 作为 iOS 8 应用程序使用本地身份验证 API 中的身份验证机制。 目前不能为使用本地身份验证进行远程身份验证。

若要完全了解 Touch ID 和其相当，我们应该仔细查看 Keychain 服务，但这些新的更改你的用户数据的含义。 Keychain Access 中也已后在 iOS 8 通过新的访问控制列表 (Acl) 功能使用扩展。

## <a name="keychain--secure-enclave"></a>密钥链和安全 Enclave

Keychain 是大型数据库提供安全存储密码、 密钥、 证书和说明的一个单独的 Apple id。 在 iOS 8 应用程序始终有权访问自己唯一的密钥链的项，以及不能访问其他应用程序的任何 keychain 项。 这不同于 OS X 的密钥链被解除锁定都使用一个密码，让任何 Keychain 服务感知的应用程序使用密钥链。 在本文中我们将重点介绍在 iOS 8 中的密钥链工作原理。

Keychain 是一个专用的数据库，其中每一行称为_Keychain 项_。 每个项由 keychain 属性描述，并由组成加密值。 若要有效地使用密钥链的允许，它非常适合较小的项，或_机密_。
密钥链中的每一项受用户密码和唯一的设备密码。 Keychain 项应受到保护，即使用户不使用其设备。 这实现的 iOS 中通过仅允许在设备处于解锁状态时可用的项-锁定设备时它们变得不可用。 它们也可以存储在加密的备份。 密钥链的主要功能之一是强制实施访问控制;应用程序有权访问密钥链，其部分，并已将阻止所有其他应用程序。 下图演示应用程序如何与密钥链交互：

[![](touchid-images/image1.png "此图描述了应用程序如何与密钥链交互")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>安全 Enclave

密钥链无法解密密钥链项重试。而是在中执行*安全 Enclave*。 安全 enclave 是协同处理器中的 A7 芯片，负责确定成功匹配的指纹对已注册的打印 Touch ID 传感器数据。 然后，它将解密密钥链项，并返回到密钥链的解密的密钥。

### <a name="working-with-keychain"></a>使用密钥链

第一次你的应用程序应查询到密钥链，以查看密码是否存在。 如果不存在，你可能需要提示输入密码，以便用户不一直在要求。 如果需要更新密码，提示用户输入新密码，并将在更新后的值传递给密钥链。

> [!NOTE]
> 使用机密从密钥链中检索后，应从内存中清除对数据的所有引用。 永远不会将其分配给全局变量。

## <a name="keychain-acl-and-touch-id"></a>Keychain ACL 和 Touch ID

访问控制列表是在 iOS 8 描述有关必须发生什么情况以允许进行特定操作的信息中的新密钥链项属性。 这可能是形式显示警报对话框或请求密码。 ACL 允许你设置可访问性和 keychain 项的身份验证。 下图显示了这一新特性如何与密钥链项的其余部分：

[![](touchid-images/image2.png "此图显示了这一新特性如何与密钥链项的其余部分")](touchid-images/image2.png#lightbox)

截至 iOS 8，现在有了新的用户是否存在策略， `SecAccessControl`，其中强制执行安全 enclave iPhone 5 条和更高版本。 我们可以看到下，只需如何设备配置可以影响策略评估表中：

|设备配置|策略评估|备份机制，|
|--- |--- |--- |
|而无需密码的设备|没有访问权限|无|
|使用密码的设备|需要密码|无|
|Touch id 的设备|首选 Touch ID|允许密码|

在安全 Enclave 内的所有操作可以相互都信任。 这意味着我们可以使用 Touch ID 身份验证结果授权 Keychain 项解密。 安全 Enclave 还能保持保持失败的 Touch ID 匹配，在其中情况下用户将必须还原为使用该密码的计数器。
IOS 8，称为的新 framework_本地身份验证_，支持此身份验证的设备中的过程。 在下一部分中，我们将探讨这。

## <a name="local-authentication"></a>本地身份验证

随着我们建立上一节中，应用程序可以使用本地身份验证已在设备上设置的安全策略的遵守情况中对用户进行身份验证。

目前，该 API 提供了仅有两个功能： 首先，它帮助通过使用新密钥链访问控制列表 (Acl) 的现有密钥链服务。 Keychain 数据可以是与成功的身份验证的用户指纹解锁。

其次，LocalAuthentication 提供两种方法进行身份验证的本地应用程序。 开发人员应使用`CanEvaluatePolicy`来确定设备是否能够接受 Touch ID，然后`EvaluatePolicy`启动身份验证操作。

虽然这两项功能提供本地身份验证，它们不提供用于应用程序或用户向远程服务器进行身份验证的机制。
本地身份验证提供的新标准用户界面，用于身份验证。 对于 Touch ID，这是与如下所示的两个按钮的警报视图。 到取消，另一个用于身份验证 – 密码的回退方法的一个按钮。 此外，还有必须设置的自定义消息。 它是用于向用户 Touch ID 身份验证为何需要解释的好办法。

[![](touchid-images/image12.png "Touch ID 身份验证警报")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>使用密钥链服务

我们讨论过有点前面 keychain 项，则如何使用安全 enclave 验证我们密码的解密。 在 iOS 8，我们可以使用本地身份验证请求与提供的回退机制或密码的实现上的访问控制列表功能结合使用 Touch ID 验证。
若要使用的 ACL 应使用`SecAccessControl`策略，，然后检查的设备使用的状态`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`。

#### <a name="considerations-with-acl"></a>使用 ACL 的注意事项

我们应使用密钥链的 ACL 时需要牢记的很多东西，下面列出了其中的某些类：

-   只能对使用前台应用程序 – 如果调用任何 keychain 操作在后台线程的调用将失败。
-   添加和更新 keychain 项可能需要身份验证。
-   如果请求在 keychain 中返回多个匹配项，可能需要身份验证。
-   ACL 的受保护项可以仅设备，并因此不同步或备份。

### <a name="using-local-authentication-without-keychain-services"></a>使用本地身份验证不使用密钥链服务

作为一种方法，以收集的凭据，例如密码或 Touch ID，并使用安全 Enclave 完成对用户进行身份验证已创建本地身份验证。 可以将其作为你的应用程序和安全 Enclave，可以永远不会直接相互通信之间的桥梁。 它还可以用于你的应用程序的策略评估。

若要执行此操作应用程序将调用内启动安全 Enclave 内部的操作的本地身份验证策略评估。 你可以利用这无需直接查询/访问安全 Enclave 提供到你的应用的身份验证。

[![](touchid-images/image13a.png "使用本地身份验证不使用密钥链服务")](touchid-images/image13a.png#lightbox)

使用本地身份验证在你的应用程序提供一种简单的实现用户验证，例如，若要解锁的个人的目的只是为了眼中设备所有者，例如银行应用程序，或以便帮助家长控制该功能应用程序。 你还可以使用它作为一种方法来扩展已存在的身份验证 – 用户喜欢它们信息是安全的但它们还希望能够进行选择。

本地身份验证的安全性与不同的密钥链。 例如，使用密钥链，信任时，操作系统和安全 Enclave 之间。 使用本地身份验证，它是应用程序和操作系统，这意味着你仅可以访问的安全 Enclave，不安全 Enclave 本身结果之间。

在安全的主题，它也是非常重要，以了解是否有**不能访问**到已注册的指或指纹映像。 安全 Enclave 是此信息的所有者，因此没有其他系统组件有权访问它。

若要通过利用本地身份验证 API，而密钥链没有使用 Touch ID，有少数几个我们可以使用的函数。 这些下面详细介绍：

*   `CanEvaluatePolicy` -这只需将检查设备能够接受 Touch ID
*   `EvaluatePolicy` -这启动身份验证操作，并显示的 UI，并返回`true`或`false`应答。
*   `DeviceOwnerAuthenticationWithBiometrics` -这是可以用于显示 Touch ID 屏幕的策略。 值得注意的，没有密码的此处回退机制，而是应在你的应用程序，以允许用户跳过 Touch ID 身份验证中实现此回退。

有几条注意事项与使用本地身份验证，如下所示：

*   使用密钥链，它只能运行在前台。 在后台线程上调用它将导致失败。
*   请记住，策略评估可能会失败。 密码按钮将需要作为回退实现。
*   必须提供`localizedReason`以解释为什么需要身份验证。 这可帮助用户与建立信任。

接下来，在下面的部分中，我们将了解如何实现考虑到这些警告的 API。

## <a name="adding-touch-id-to-your-application"></a>将 Touch ID 添加到你的应用程序

在前面的部分中，我们讨论过的理论基础访问和使用密钥链和本地身份验证的身份验证。 现在，我们将看看你可以将在 Touch ID 集成到你的应用程序。

### <a name="walkthrough"></a>演练

让我们看一下将某些 Touch ID 身份验证添加到我们的应用程序。 在本演练中我们将使用[情节提要表](https://developer.xamarin.com/samples/StoryboardTable/)示例。 我们想要确保仅设备所有者可以将内容添加到此列表中，我们不想允许将项添加的任何人都混乱不堪 ！

1.  下载示例和运行 Visual Studio 中的 mac。
2.  双击`MainStoryboard.Storyboard`以在 iOS 设计器中打开示例。 此示例中，我们想要将新的屏幕添加到我们的应用程序，将控制身份验证。 这将在当前前处于`MasterViewController`。
3.  将一个新**视图控制器**从**工具箱**到**设计图面**。 设置为**根视图控制器**通过**按 Ctrl 并拖动**从**导航控制器**:

    [![](touchid-images/image4.png "设置根视图控制器")](touchid-images/image4.png#lightbox)
4.  命名新视图控制器`AuthenticationViewController`。
5.  接下来，将按钮拖动并将其置于`AuthenticationViewController`。 我们称之为`AuthenticateButton`，并为其提供文本`Add a Chore`。
6.  创建事件`AuthenticateButton`调用`AuthenticateMe`。
7.  创建手动从 segue`AuthenticationViewController`通过单击底部的黑色栏和**按 Ctrl 并拖动**在菜单栏上到`MasterViewController`并选择**推送**(或**显示**如果使用大小类）：

    [![](touchid-images/image5.png "将其从栏拖到 MasterViewController 和选择推送或显示")](touchid-images/image6.png#lightbox)
8.  单击新创建的 segue 并为其提供标识符`AuthenticationSegue`，如下所示：

    [![](touchid-images/image7.png "将 segue 标识符设置为 AuthenticationSegue")](touchid-images/image7.png#lightbox)
9.  将下列代码添加到 `AuthenticationViewController`：

    ```
    partial void AuthenticateMe (UIButton sender)
        {
            var context = new LAContext();
            NSError AuthError;
            var myReason = new NSString("To add a new chore");


            if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
                var replyHandler = new LAContextReplyHandler((success, error) => {

                    this.InvokeOnMainThread(()=>{
                        if(success){
                            Console.WriteLine("You logged in!");
                            PerformSegue("AuthenticationSegue", this);
                        }
                        else{
                            //Show fallback mechanism here
                        }
                    });

                });
                context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
            };
        }
    ```

这是你需要实现使用本地身份验证的 Touch ID 身份验证的所有代码。 下图中突出显示的行显示了使用本地身份验证：

[![](touchid-images/image8.png "突出显示的行显示了使用本地身份验证")](touchid-images/image8.png#lightbox)

首先，我们需要建立设备是否可以接受输入，通过使用 Touch ID 的`CanEvaluatePolicy`并传递在策略中`DeviceOwnerAuthenticationWithBiometrics`。 如果这是 true，则我们可以使用显示 Touch ID UI `EvaluatePolicy`。 有三个部分的信息，我们必须将传递到`EvaluatePolicy`– 策略本身，解释为什么身份验证必需的一个字符串和答复处理程序。 答复处理程序向应用程序告知其应在成功，或不成功，身份验证的情况下执行的操作。 我们来看更详细地答复处理程序：

[![](touchid-images/image9.png "答复处理程序")](touchid-images/image9.png#lightbox)

答复处理程序将指定类型的`LAContextReplyHandler`，它将采用的参数成功 –`bool`值，和`NSError`调用`error`。 如果成功，这是我们将实际执行地方它是我们想要进行身份验证 – 的任何这种情况下显示屏幕可让我们将添加新混乱。 请记住的本地身份验证需要注意的问题之一是，它必须是在前台上运行，因此请务必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "InvokeOnMainThread 用于本地身份验证")](touchid-images/image10.png#lightbox)

最后，身份验证成功后，我们希望转换到`MasterViewController`。 `PerformSegue`方法可以用于执行此操作：

[![](touchid-images/image11.png "调用转换到 MasterViewController PerformSegue 方法")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>总结
在本指南中我们讨论在 Keychain 和此在 iOS 中的工作原理。 我们还探讨了 keychain ACL，并在 iOS 中的对此更改。 接下来，我们所花费的本地身份验证框架，它是 iOS 8 中的新增功能，在我们的应用程序中实现 Touch ID 身份验证，然后看一看。

## <a name="related-links"></a>相关链接

- [Touch ID 示例](https://developer.xamarin.com/samples/StoryboardTable_LocalAuthentication)
- [Keychain WWDC 示例](https://developer.xamarin.com/samples/KeychainTouchID/)
- [密钥链 （示例）](https://developer.xamarin.com/samples/Keychain/)
