---
title: "通过 Xamarin 使用 Jenkins"
description: "本指南演示如何设置为持续集成服务器 Jenkins 和自动执行编译使用 Xamarin 创建的移动应用程序。 它描述如何在 OS X 上安装 Jenkins、 配置和设置来编译 Xamarin.iOS 和 Xamarin.Android 应用程序时更改会提交到源代码管理系统的作业。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 8d23211e28cb1b1dae13d67e32462888c66ff065
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="using-jenkins-with-xamarin"></a>通过 Xamarin 使用 Jenkins

_本指南演示如何设置为持续集成服务器 Jenkins 和自动执行编译使用 Xamarin 创建的移动应用程序。它描述如何在 OS X 上安装 Jenkins、 配置和设置来编译 Xamarin.iOS 和 Xamarin.Android 应用程序时更改会提交到源代码管理系统的作业。_

[使用 Xamarin 的连续集成简介](~/tools/ci/intro-to-ci.md)引入了有用的软件开发做法提供早期警告的损坏或不兼容的代码的持续集成。 CI 允许开发人员解决问题和问题，因为它们，则会出现并将该软件保存在部署的某种合适的状态。 本演练涵盖如何结合使用这两个文档中的内容。

本指南演示如何在运行 OS X 的专用计算机上安装 Jenkins 并将其配置为在计算机启动时自动运行。 安装后 Jenkins，我们将安装更多插件以支持 MS Build。 Jenkins 支持现成的 Git。 如果 TFS 用于源代码管理，则还必须安装其他插件和命令行实用程序。

一旦配置 Jenkins，且已安装任何必要的插件，我们将创建一个或多个作业以编译 Xamarin.Android 和 Xamarin.iOS 项目。 作业是步骤和执行某些工作所需的元数据的集合。 作业通常以下内容组成：

-  **源代码管理 (SCM)** – 这是包含有关如何连接到源代码管理和要检索的文件信息的元数据条目 Jenkins 配置文件中。
-  **触发器**– 触发器用于启动基于某些操作，如当开发人员的更改提交到源代码存储库的作业。
-  **版本说明**– 这是插件或脚本编译源代码并生成可以在移动设备安装的二进制文件。
-  **可选的生成操作**– 这可能包括运行单元测试，代码中，签名的代码中，对执行静态分析或正在启动另一个作业来执行其他生成相关的工作。
-  **通知**– 作业可能会发送出一些种类的生成的有关状态通知。
-  **安全**– 虽然可选的强烈建议还启用 Jenkins 安全功能。


本指南将指导如何设置 Jenkins 服务器涵盖的每个这些点。 它的结束时，我们应具有非常了解如何设置和配置 Jenkins 为我们 Xamarin 移动项目创建 IPA 和 APK 的。

## <a name="requirements"></a>惠?

理想的生成服务器是专用于生成和可能测试应用程序的唯一目的的独立计算机。 专用的计算机可确保可能要求的其他角色 （例如，web 服务器） 的项目不会污染生成。 例如，如果生成服务器还充当 web 服务器时，web 服务器可能需要一些常见的库的冲突版本。 由于此冲突的 web 服务器可能无法正常运行或 Jenkins 可能创建生成部署到用户时不起作用。

用于 Xamarin 移动应用程序的生成服务器设置非常非常类似于开发人员的工作站。 它具有哪些 Jenkins，适用于 Mac，Visual Studio 中的用户帐户以及将安装 Xamarin.iOS 和 Xamarin.Android。 必须同时安装的所有代码签名证书，预配配置文件和密钥存储。 *通常为生成服务器的用户帐户是独立于开发人员帐户-请务必安装和配置所有软件、 密钥和证书时使用的生成服务器用户帐户登录。*

下图说明了所有这些元素在典型的 Jenkins 生成服务器上：

 [![](jenkins-walkthrough-images/image1.png "此图显示了所有这些元素在典型的 Jenkins 生成服务器上")](jenkins-walkthrough-images/image1.png#lightbox)

iOS 应用程序可以仅生成并在运行 Mac OS X 的计算机上进行签名。Mac Mini 是一个合理的成本较低的选项，但任何一台计算机能够运行 OS X 10.10 (Yosemite) 或更高版本就足够了。

如果 TFS 用于源代码管理，你将想要安装[Team Explorer Everywhere](http://www.microsoft.com/en-ca/download/details.aspx?id=40785)，可从 Microsoft 获得。 Team Explorer Everywhere 提供对在终端中 OS X 的 TFS 的跨平台访问。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安装 Jenkins

使用 Jenkins 的第一个任务是将其安装。 有三种方法在 OS x： 上运行 Jenkins

-  作为后台进程，在后台运行。
-  在如 Tomcat、 Jetty 或 JBoss servlet 容器。
-  作为正常进程在用户帐户下运行。


大多数传统的持续集成应用程序运行，在后台，方法是作为守护程序 (OS X 上或\*nix) 或 （在 Windows 上) 的服务。 必需的没有 GUI 交互以及在其中生成环境的安装程序可以轻松地执行，这是适用于方案。 移动应用程序也需要密钥库和签名证书作为 daemon 运行 Jenkins 时可能会访问出现问题。 由于这些问题，本文档重点介绍第三个方案 – 用户帐户下运行 Jenkins，在生成服务器上。

Jenkins.App 是安装 Jenkins 便捷途径。 这是 AppleScript 包装，它简化了启动和停止的 Jenkins 服务器。 而不是在 bash shell 中运行，Jenkins 将作为运行，应用程序中停靠、 图标中的以下屏幕截图所示：

 [![](jenkins-walkthrough-images/image2.png "而不是在 bash shell 中运行，Jenkins 将作为运行，应用程序中停靠、 图标此屏幕截图中所示")](jenkins-walkthrough-images/image2.png#lightbox)

启动或停止 Jenkins 是作为启动或停止 Jenkins.App 一样简单。

若要安装 Jenkins.App，请从项目的下载页，显示在下面的屏幕截图中下载最新版本：

 [![](jenkins-walkthrough-images/image3.png "应用程序，请从项目的最新版本的下载页，图中显示此屏幕截图中的下载")](jenkins-walkthrough-images/image3.png#lightbox)

解压缩 zip 文件到`/Applications`上你的生成服务器和它就像任何其他 OS X 应用程序的启动文件夹。
第一次启动 Jenkins.App，它将显示对话框告知你它将下载 Jenkins:

 [![](jenkins-walkthrough-images/image4.png "应用程序，它将显示对话框告知你它将下载 Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

下载完成后 Jenkins.App，它将显示另一个对话框，要求你是否你想要自定义 Jenkins 启动，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image5.png "应用已完成其下载，因此它将显示另一个对话框，要求你是否你想要自定义 Jenkins 启动时，此屏幕截图中所示")](jenkins-walkthrough-images/image5.png#lightbox)

自定义 Jenkins 是可选的不需要为 Jenkins 将适用于大多数情况下执行每次启动应用程序时-默认设置。

如果需要自定义 Jenkins，请单击**更改默认值**按钮。 这将给你带来两个连续的对话框： 一个询问对于 Java 命令行参数，而另一个的询问 Jenkins 命令行参数。 以下两个屏幕快照显示这些两个对话框：

 [![](jenkins-walkthrough-images/image6.png "此屏幕快照显示对话框")](jenkins-walkthrough-images/image6.png#lightbox)

 [![](jenkins-walkthrough-images/image7.png "此屏幕快照显示对话框")](jenkins-walkthrough-images/image7.png#lightbox)

Jenkins 运行后，你可能想要将其设置为登录名项，以便它将开始向上每次用户登录到计算机。 你可以执行此操作中停靠的 Jenkins 图标右键单击并选择**选项...打开在登录**，如以下屏幕截图中所示：

 [![](jenkins-walkthrough-images/image8.png "此屏幕截图中所示，可以执行此中停靠的 Jenkins 图标右键单击并选择在登录，OptionsOpen 操作")](jenkins-walkthrough-images/image8.png#lightbox)

这将导致 Jenkins.App 以自动启动每次用户登录，但不是在计算机启动。 可指定 OS X 在启动时将使用自动使用登录的用户帐户。 打开**系统首选项**，然后选择**用户和组**图标此屏幕截图中所示：

 [![](jenkins-walkthrough-images/image9.png "打开系统首选项，然后选择用户组图标，此屏幕截图中所示")](jenkins-walkthrough-images/image9.png#lightbox)

单击**登录选项**按钮，，然后选择 OS X 将在启动时用于登录的帐户。

此时已安装 Jenkins。 但是，如果我们想要构建 Xamarin 移动应用程序，我们将需要安装某些插件。


### <a name="installing-plugins"></a>安装插件

完成 Jenkins.App 安装程序后，它将启动 Jenkins 并启动 web 浏览器中的使用 URL http://localhost:8080/，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image10.png "8080，此屏幕截图中所示")](jenkins-walkthrough-images/image10.png#lightbox)

在此页中，选择**Jenkins > 管理 Jenkins > 管理插件**从菜单中的左上角，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image11.png "从此页中，从菜单中的左上角选择 Jenkins 管理的 Jenkins 管理插件")](jenkins-walkthrough-images/image11.png#lightbox)

这将显示**Jenkins 插件管理器**页。 如果单击可用选项卡上，你将看到 600 即可下载和安装的插件的列表。 这被显示在下面的屏幕截图：

 [![](jenkins-walkthrough-images/image12.png "如果单击可用选项卡上，你将看到 600 即可下载和安装的插件的列表")](jenkins-walkthrough-images/image12.png#lightbox)

滚动浏览所有 600 插件来查找一些可能会很麻烦，而且容易出错。 Jenkins 提供接口的右上角中的筛选器搜索字段。 使用此筛选器字段来搜索将简化找到并安装一个或所有以下插件：

-  **Jenkins MSBuild 插件**– 此插件可以生成 Visual Studio 和 Visual Studio 适用于 Mac 解决方案 (.sln) 和项目 (.csproj)。
-  **环境 Injector 插件**– 这是一个可选但十分有用的插件，使作业在设置环境变量和生成级别。 如对代码使用的密码登录应用程序，它还提供对变量的额外保护。 它有时缩写为*EnvInject 插件*。
-  **Team Foundation Server 插件**– 这是仅一个可选插件所需如果你使用 Team Foundation Server 或 Team Foundation Services 进行源代码管理。

Jenkins 没有任何额外的插件的情况下支持 Git。

在安装所有的插件之后, 你将想要重新启动 Jenkins 和配置的每个插件的全局设置。 可以通过选择找到全局设置为插件**Jenkins > 管理 Jenkins > 配置系统**从左上角，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image13.png "可以通过选择 Jenkins 找到全局设置为插件 / 管理 Jenkins / 配置系统从左上另一方面角")](jenkins-walkthrough-images/image13.png#lightbox)

当选择此菜单选项时，你将转至**配置系统 [Jenkins]**页。 此页包含部分配置 Jenkins 本身以及设置某些全局插件值。  下面的屏幕截图演示了此页的示例：

 [![](jenkins-walkthrough-images/image14.png "此屏幕截图演示了此页的示例")](jenkins-walkthrough-images/image14.png#lightbox)


#### <a name="configuring-the-msbuild-plugin"></a>配置 MSBuild 插件

MSBuild 插件必须配置为使用**/Library/Frameworks/Mono.framework/Commands/xbuild**来为 Mac 解决方案和项目文件进行编译时 Visual Studio。 向下滚动**配置系统 [Jenkins]**直到页上**添加 MSBuild**按钮将出现，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image15.png "向下滚动配置系统 Jenkins 页之前将添加 MSBuild 按钮显示")](jenkins-walkthrough-images/image15.png#lightbox)

单击此按钮，并填写**名称**和**路径**到**MSBuild**上出现的表单中的字段。 名称你**MSBuild**安装应为有意义的名称，同时**MSBuild 路径**应为路径`xbuild`，这通常是**/Library/框架 /Mono.framework/Commands/xbuild**。 我们通过单击保存或在页面底部的应用按钮保存所做的更改后，Jenkins 将能够使用`xbuild`来编译你的解决方案。

#### <a name="configuring-the-tfs-plugin"></a>配置 TFS 插件

本部分是如果你想要使用 TFS 进行源代码管理必需的。

为了使与 TFS 服务器进行交互的 OS X 工作站，Team Explorer Everywhere 必须安装在工作站上。 Team Explorer Everywhere 是一套从包括跨平台命令行客户端用于访问 TFS 的 Microsoft 工具。 Team Explorer Everywhere 可以从 Microsoft 下载并安装在三个步骤：

1. 将存档文件提取到用户帐户可以访问的目录。 例如，可能会将文件提取到**~/tee**。
2. 配置命令行程序或系统路径，以包括保存了在上述步骤中解压缩的文件的文件夹。 例如，应用于对象的

        echo export PATH~/tee/:$PATH' >> ~/.bash_profile

3. 若要确认安装 Team Explorer Everywhere，请打开终端会话中，并执行`tf`命令。 如果 tf 已正确配置，你将在终端会话中看到以下输出：

        $ tf
        Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

        Available commands and their options:

安装 TFS 的命令行客户端后，必须使用的完整路径配置 Jenkins`tf`命令行客户端。 向下滚动**配置系统 [Jenkins]**页直到找到 Team Foundation Server 部分中，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image17.png "向配置系统 Jenkins 页下滚动直到找到 Team Foundation Server 部分")](jenkins-walkthrough-images/image17.png#lightbox)

输入的完整路径`tf`命令，并单击**保存**按钮。

### <a name="configure-jenkins-security"></a>配置 Jenkins 安全性

首次安装，Jenkins 将具有禁用的安全性，因此可以为任何用户设置和匿名运行任何类型的作业。 本部分介绍如何配置安全使用 Jenkins 用户数据库配置身份验证和授权。

可以通过选择找到安全设置**Jenkins > 管理 Jenkins > 配置全局安全**，此屏幕截图中所示：

 [![](jenkins-walkthrough-images/image18.png "可以通过选择 Jenkins 找到安全设置 / 管理 Jenkins / 配置全局安全")](jenkins-walkthrough-images/image18.png#lightbox)

上**配置全局安全**页上，选中**启用安全**复选框和**访问控制**窗体应出现，类似于下一步的屏幕截图：

 [![](jenkins-walkthrough-images/image19.png "在配置全局安全页上，选中启用安全复选框和访问控制窗体应出现，类似于此屏幕截图")](jenkins-walkthrough-images/image19.png#lightbox)

切换的单选按钮**Jenkins 自己的用户数据库**中**安全领域部分**，并确保**允许用户注册**也已选中中, 所示以下屏幕快照：

 [![](jenkins-walkthrough-images/image20.png "切换 Jenkins 自己的用户数据库，在安全性领域部分的单选按钮，并确保也选中允许用户注册")](jenkins-walkthrough-images/image20.png#lightbox)

最后，重新启动 Jenkins 并创建新的帐户。 创建的第一个帐户的根帐户中，并且此帐户将自动提升为管理员。 向后定位到**配置全局安全**页，然后勾选**基于矩阵的安全性**单选按钮。 应为根帐户授予完全访问权限，并匿名帐户应为只读访问权限，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image21.png "应为根帐户授予完全访问权限，和匿名帐户应为只读访问权限")](jenkins-walkthrough-images/image21.png#lightbox)

一旦保存这些设置并重新启动 Jenkins，将开启安全。


#### <a name="disabling-security"></a>禁用安全性

在出现忘记了的密码或 Jenkins 级锁定，则可以通过执行以下步骤禁用安全性：

1. 停止 Jenkins。 如果你使用的 Jenkins.app，你可以执行此操作通过右键单击中停靠、 的 Jenkins.App 图标并从弹出菜单中选择退出：

    ![](jenkins-walkthrough-images/image19.png "在停靠，并从弹出菜单中选择退出应用程序图标")
2. 打开文件**~/.jenkins/config.xml**在文本编辑器中。
3. 更改的值`<usesecurity></usesecurity>`元素从`true`到`false`。
4. 删除`<authorizationstrategy></authorizationstrategy>`和`<securityrealm></securityrealm>`文件中的元素。
5. 重新启动 Jenkins。

## <a name="setting-up-a-job"></a>设置作业

在顶层，Jenkins 将组织生成软件转换所需的各种任务的所有*作业*。 作业还具有关联的元数据，提供有关如何获取源代码、 运行生成频率、 所需的生成过程中，任何特殊变量等如何通知开发人员，在生成失败时生成的信息。

通过选择创建作业**Jenkins > 新作业**从菜单中的右上角，如下面的屏幕截图中所示：


![](jenkins-walkthrough-images/image22.png "通过选择右上角菜单中的 Jenkins 新作业创建作业")

这将显示**新作业 [Jenkins]**页。 输入此作业的名称并选择**生成自由格式的软件项目**单选按钮。 下面的屏幕截图显示了此示例：

![](jenkins-walkthrough-images/image23.png "输入此作业的名称并选择一个生成自由格式的软件项目单选按钮")

单击**确定**按钮显示作业的配置页。 这应类似于下面的屏幕快照：

![](jenkins-walkthrough-images/image24.png "这应类似于此屏幕截图")

Jenkins 将组织中的目录位于以下路径的硬盘上的作业： **~/.jenkins/jobs/[JOB 名称]**

此文件夹包含所有文件和特定于如日志、 配置文件和需要编译的源代码作业的项目。

一旦创建初始作业后，它必须使用一个或多个以下配置：

 - 必须指定源代码管理系统。
 - 一个或多个*生成操作*必须添加到项目。 这些是步骤或生成应用程序所需的任务。
 - 作业必须分配一个*生成触发器*– 一组指令告知 Jenkins 频率用于检索代码和生成最终的项目。

### <a name="configuring-source-code-control"></a>配置源代码管理

Jenkins 未在第一个任务是从源代码管理系统中检索的源代码。 Jenkins 支持许多常用的源代码代码管理系统如今可用。 本部分介绍两个常用系统，Git 和 Team Foundation Server。 下面的部分中的更详细地讨论了每这些源代码管理系统。

#### <a name="using-git-for-source-code-control"></a>使用 Git 为源代码管理

如果你正使用 TFS 进行源代码管理，[跳过](#Using_TFS_for_Source_Code_Management)此部分并转到下一步使用 TFS 一节。

Jenkins 支持现成的 – 没有额外的插件所需的 Git。 若要使用 Git，请单击**Git**单选按钮和 Git 存储库中，输入的 URL，如下面的屏幕截图中所示：

![](jenkins-walkthrough-images/image25.png "若要使用 Git，请单击 Git 单选按钮并输入 Git 存储库的 URL")

保存所做的更改，Git 配置已完成。

#### <a name="using-tfs-for-source-code-management"></a>使用 TFS 源代码管理

本部分仅适用于 TFS 用户。

单击**Team Foundation Server**单选按钮和 TFS 配置节应出现，类似于以下屏幕截图中：


![](jenkins-walkthrough-images/image26.png "单击 Team Foundation Server 单选按钮，TFS 配置节应会显示")

提供用于 TFS 的必要信息。 以下屏幕截图显示完成的窗体的示例：

![](jenkins-walkthrough-images/image27.png "此屏幕截图显示了已完成的窗体的示例")

#### <a name="testing-the-source-code-control-configuration"></a>测试的源的代码控制配置

一旦配置了相应的源代码，请单击**保存**以保存所做的更改。 这将使你返回到该作业，将类似于以下屏幕截图的主页上：

![](jenkins-walkthrough-images/image28.png "这将使你返回到该作业，将类似于此屏幕截图中的主页")

验证已正确配置源代码管理的最简单方法是手动触发的生成即使不没有指定任何生成操作。 若要手动启动生成，主页上的作业具有**立即生成**链接在左侧，菜单中，如下面的屏幕截图中所示：

![](jenkins-walkthrough-images/image29.png "若要手动启动生成，该作业的主页上具有的立即生成链接在左侧菜单中")

在启动生成后，生成历史记录对话框将显示一个闪烁的蓝色圆圈，进度栏、 版本号和生成开始，类似于下面的屏幕快照的时间：

![](jenkins-walkthrough-images/image30.png "在启动生成后，生成历史记录对话框显示一个闪烁的蓝色圆圈，进度栏、 版本号和生成开始的时间")

如果作业成功，则将显示蓝色圆圈。 如果作业失败时，将显示一个红圈。

为了帮助进行故障排除作为生成的一部分，可能会出现的问题，Jenkins 将捕获所有作业的控制台输出。 若要查看控制台输出中，单击中的作业**生成历史记录**，然后在**控制台输出**左侧菜单中的链接。 以下屏幕快照显示**控制台输出**链接，以及一些成功的作业的输出：

![](jenkins-walkthrough-images/image31.png "此屏幕快照显示控制台输出链接，以及一些成功的作业的输出")

#### <a name="location-of-build-artifacts"></a>生成项目的位置

Jenkins 将检索到一个名为的特殊文件夹的完整源代码*工作区*。 在以下位置的文件夹内找不到此目录：

    ~/.jenkins/jobs/[JOB NAME]/workspace

工作区中的路径将存储在名为的环境变量`$WORKSPACE`。

可以通过导航到作业的登录页，然后单击浏览的工作区文件夹中 Jenkins**工作区**左侧菜单中的链接。 下面的屏幕截图显示了一个名为作业的工作区的示例**HelloWorld**:

![](jenkins-walkthrough-images/image32.png "此屏幕截图显示了一个名为 HelloWorld 作业的工作区的示例")

### <a name="build-triggers"></a>生成触发器

有几种不同策略适用于启动在 Jenkins 中的生成 – 它们称为*生成触发器*。 生成触发器可帮助 Jenkins 决定何时启动作业以及生成项目。 两个更常见的生成触发器是：

- **定期生成**– 此触发器使 Jenkins 在指定的时间间隔，例如每隔两小时或每周日期的午夜启动作业。 生成将启动而不管是否在源代码存储库中进行了任何更改。
- **轮询 SCM** – 此触发器将轮询定期源代码管理。 如果任何更改都已提交到源代码存储库，Jenkins 将启动新的生成。

轮询 SCM 是常用的触发器，因为开发人员提交会导致生成中断的更改时，它提供快速的反馈。 这可用于警报团队，一些最近提交的代码导致问题，并允许开发人员解决该问题仍全新记住所做的更改时。

定期生成通常用于创建为测试人员可以分发应用程序的版本。 例如，定期生成可能安排在星期五夜间，以便 QA 团队的成员可测试与前一周的工作。


### <a name="compiling-a-xamarinios-applications"></a>编译 Xamarin.iOS 应用程序
可以在使用命令行编译 Xamarin.iOS 项目`xbuild`或`msbuild`。 Shell 命令将运行 Jenkins 的用户帐户的上下文中执行。 很重要的用户帐户有权访问预配配置文件，以便应用程序可以正确打包以便分发。 很可能将此 shell 命令添加到作业配置页。

向下滚动到**生成**部分。 单击**添加生成步骤**按钮，然后选择**执行 shell**，如下面的屏幕截图所示：

![](jenkins-walkthrough-images/image33.png "单击添加生成步骤按钮，然后选择执行 shell")


[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>生成 Xamarin.Android 项目

生成 Xamarin.Android 项目是在概念上非常类似于生成 Xamarin.iOS 项目。 若要从 Xamarin.Android 项目中创建 APK，Jenkins 必须配置为执行以下两个步骤：

 - 编译项目使用的 MSBuild 插件。
 - 登录和 zip 对齐与有效版本 keystore APK。

将在接下来的两部分中的更详细地介绍这两个步骤。

### <a name="creating-the-apk"></a>创建 APK

单击**添加生成步骤**按钮，然后选择**生成 Visual Studio 项目或解决方案使用 MSBuild**，下面的屏幕截图中所示：

![](jenkins-walkthrough-images/image36.png "创建 APK 单击上添加生成步骤按钮，然后选择生成 Visual Studio 项目或解决方案使用 MSBuild")

一旦生成步骤添加到项目中，填写显示的窗体字段。 下面的屏幕截图是窗的已完成体的一个示例：

![](jenkins-walkthrough-images/image37.png "一旦生成步骤添加到项目中，填写显示的窗体字段")


此生成步骤将执行`xbuild`中**$WORKSPACE**文件夹。 MSBuild 生成文件设置为**Xamarin.Android.csproj**文件。 **命令行自变量**指定发布版本的目标**PackageForAndroid**。 此步骤的产品将 APK，在以下位置：

    $WORKSPACE/[PROJECT NAME]/bin/Release

以下屏幕截图显示此 APK 的示例：

![](jenkins-walkthrough-images/image38.png "此屏幕截图显示了此 APK 的示例")

此 APK 不准备好进行部署，因为使用私有密钥库尚未签名，就会对它进行，并必须对齐 zip。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>签名和 Zipaligning 发布 APK

签名和 zipaligning APK 是从技术上讲两个单独的任务由 Android SDK 中的两个单独的命令行工具执行。 但是，很方便地在一个生成操作中执行它们。 有关签名和 zipaligning APK 的详细信息，请参阅准备用于版本的 Android 应用程序的 Xamarin 的文档。

这两种命令需要可能不同项目到项目的命令行参数。 此外，某些这些命令行参数是时不应出现在控制台输出中生成运行的密码。 我们将在环境变量中存储的某些这些命令行参数。 下表所述所需的签名和/或 zip 对齐的环境变量：

<table>
    <thead>
        <tr>
            <td>环境变量</td>
            <td>描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>KEYSTORE_FILE</td>
            <td>这是进行签名 APK 密钥库的路径</td>
        </tr>
        <tr>
            <td>KEYSTORE_ALIAS</td>
            <td>将用于签署 APK 密钥库中的键。</td>
        </tr>
        <tr>
            <td>INPUT_APK</td>
            <td>通过创建 APK `xbuild`。</td>
        </tr>
        <tr>
            <td>SIGNED_APK</td>
            <td>签名的 APK 由`jarsigner`。</td>
        </tr>
        <tr>
            <td>FINAL_APK</td>
            <td>这是 zip 对齐由生成的 APK `zipalign`。</td>
        </tr>
        <tr>
            <td>STORE_PASS</td>
            <td>这是用于访问有关个人文件都按照密钥库的内容的密码。</td>
        </tr>
    </tbody>
</table>

如所述的要求部分中，可以使用 EnvInject 插件在生成过程中设置这些环境变量。 作业均应具有新的生成步骤添加基于 Inject 环境变量中的下一步的屏幕截图所示：

![](jenkins-walkthrough-images/image39.png "作业均应具有新的生成步骤已添加了基于 Inject 环境变量")

在**属性内容**形成字段将显示、 环境变量将添加、 每个行，一个采用以下格式：

    ENVIRONMENT_VARIABLE_NAME = value

以下屏幕截图显示所需的签名 APK 的环境变量：

![](jenkins-walkthrough-images/image40.png "此屏幕快照显示环境变量所需的签名 APK")

请注意一些 APK 文件的环境变量都基于`WORKSPACE`环境变量。

最终的环境变量是密钥库的内容进行访问的密码： `STORE_PASS`。 密码是应被遮盖或省略在日志文件中的敏感值。 可以配置 EnvInject 插件来保护这些值，以便它们不会显示在日志中。

立即之前**生成**作业配置的部分是**生成环境中**部分。 当**注入密码**切换复选框，某种形式的字段显示。 这些窗体字段用于捕获的名称和环境变量的值。 下面的屏幕截图是添加的一个示例`STORE_PASS`环境变量：

![](jenkins-walkthrough-images/image41.png "此屏幕截图是添加 STOREPASS 环境变量的示例")

已经初始化环境变量下, 一步是添加用于签名的生成步骤和 zip 对齐 APK。 立即插入环境变量的生成步骤将另一个后**执行 shell**将执行的命令生成`jarsigner`和`zipalign`。 下面的代码段中所示，每个命令将上移一行，执行：


    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK

下面的屏幕截图显示了如何输入的示例`jarsigner`和`zipalign`到步骤的命令：

![](jenkins-walkthrough-images/image42.png "此屏幕截图显示了如何为步骤输入 jarsigner 和 zipalign 命令的示例")

位置生成的所有操作后，很好的做法触发手动生成以验证一切工作。 如果生成失败，**控制台输出**应评审有关导致生成失败的原因的信息。

### <a name="submitting-tests-to-test-cloud"></a>正在提交测试与测试云

自动的测试可以提交到测试云使用 shell 命令。 有关 Xamarin 测试云中的测试运行设置的详细信息，我们制定了指南针对使用[Xamarin.UITest](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)或[Calabash](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)。


## <a name="summary"></a>摘要

本指南中我们引入 Jenkins 为 Mac OS X 上的生成服务器，并将其配置为编译和准备 Xamarin 移动应用程序版本。 我们以及多个插件以支持生成过程的 Mac OS X 计算机上安装 Jenkins。 我们创建并配置的作业，将从 TFS 或 Git、 拉取代码，然后将该代码编译到版本准备应用程序。 我们还解决了两种不同的方式来计划应运行作业。

## <a name="related-links"></a>相关链接

- [持续集成](https://developer.xamarin.com~/testcloud/ci/)
- [正在提交测试 Xamarin 测试云](https://developer.xamarin.com~/testcloud/submitting/)
- [为什么 Jenkins 不支持通过 Xamarin？](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)
