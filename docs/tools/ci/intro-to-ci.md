---
title: "使用 Xamarin 的连续集成简介"
description: "持续集成是一种软件工程做法在其中为自动化的生成将编译并 （可选） 测试应用程序添加或更改项目的版本控制存储库中的开发人员代码时。 本文将讨论持续集成的一般概念以及一些适用的选项，持续集成与 Xamarin 项目。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 07/19/2017
ms.openlocfilehash: 94d88246090208913c4b340eaed221ab64b78f19
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>使用 Xamarin 的连续集成简介

_持续集成是一种软件工程做法在其中为自动化的生成将编译并 （可选） 测试应用程序添加或更改项目的版本控制存储库中的开发人员代码时。本文将讨论持续集成的一般概念以及一些适用的选项，持续集成与 Xamarin 项目。_

很常见开发人员可以并行处理的软件项目。 在某些时候，它是所有这些集成所需的成一个工作的并行流基本代码，后者构成最终产品。 在软件开发的早期天内，此集成已执行项目中，这是十分困难和风险的过程末尾。

持续集成 (CI) 通过将每个开发人员的更改合并到常见间断基本代码来避免这种复杂性，通常，每当任何开发人员签入到项目的更改共享代码存储库。 每个签入触发的自动的生成并运行自动化的测试以验证新引入的代码不中断任何现有代码。  这种方式，CI 立即显示错误和问题，并确保，所有团队成员都保持最新与每个其他工作。 这将导致提供一致性和稳定的基本代码。

持续集成系统具有两个主要部分：

-  **版本控制**– 版本控制 (VC)，也称为源控件或源代码管理，将所有项目的代码合并到单个共享存储库并到每个文件保留的每个更改的完整历史记录。 此存储库，通常称为*主线*或*master*分支，包含将最终用于生成生产版本还是发布版本的应用程序的源代码。 有许多开放源代码和对于此任务，通常情况下允许团队或个人分叉的代码复制到辅助分支，即可以进行大量更改，或执行而无需向主分支的风险的试验的商业产品。 一旦辅助分支中的更改进行验证，然后在可能总之合并回主分支。
-  **连续 Integration Server** – 连续集成服务器负责收集所有项目的项目 （源代码、 图像、 视频、 数据库、 自动的测试、 等），编译应用程序中，并运行自动的测试。 同样，有许多开放源代码和商业 CI 服务器工具。

开发人员通常具有保存在其工作站，即最初完成工作的一个或多个分支的工作副本。 一组适当的工作完成后，所做的更改"签入"或者"提交"到相应的分支，将其传播到其他开发人员的工作副本。 这是如何团队，可确保它们在所有处理的相同代码。

同样，使用持续集成，提交更改的行为会导致 CI 服务器生成项目并运行自动的测试来验证对源代码的正确性。 如果没有生成错误或测试失败，CI 服务器会通知相应的开发人员 （通过电子邮件、 即时消息、 Twitter、 Growl，等等） 以便他或她可以更正该问题。 （CI 服务器可以甚至拒绝提交是否存在故障，因为它们称为"封闭签入"。）

下图说明了此过程：

[![](intro-to-ci-images/intro01-small.png "此图说明了此过程")](intro-to-ci-images/intro01.png)

移动应用程序引入持续集成的独特的难题。 应用程序可能需要如 GPS 或相机才在物理设备上可用的传感器。 此外，模拟器或仿真程序仅可大概了解硬件并且可能会掩盖或遮盖问题。 在结束时，务必测试确信它是真正的客户已准备的真实硬件上的移动应用。

[应用 Center 测试](https://docs.microsoft.com/en-us/appcenter/test-cloud)解决进行测试，以直接在数百个物理设备上的应用了此特定问题。 开发人员编写自动的接受测试，允许进行强大的 UI 测试。 这些测试上载到应用中心后, CI 服务器可以运行这些自动 CI 过程的一部分下图中所示：

[![](intro-to-ci-images/intro02-small.png "这些测试上载到应用中心后, CI 服务器可以自动运行这些 CI 过程的一部分此图中所示")](intro-to-ci-images/intro02.png)

# <a name="components-of-continuous-integration"></a>持续集成的组件

没有专为支持 CI 而设计的商业和开放源代码工具的大量生态系统。 本部分介绍几个最常见的。

## <a name="version-control"></a>版本控制

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services 和 Team Foundation Server

[Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs) (VSTS) 和[Team Foundation Server](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx) (TFS) 是 Microsoft 的协作工具的持续集成生成服务，跟踪任务、 敏捷规划和报告工具，以及版本控件。 与版本控制 VSTS 和 TFS 可以处理其自己的系统 （Team Foundation 版本控制或 TFVC） 或在 GitHub 上承载的项目。

 - Visual Studio Team Services 提供通过云服务。 它的主要优点是它不需要专用的硬件或基础结构，并且可以访问它从任意位置通过 web 浏览器和 Visual Studio 中，使它对于在地理位置的团队有吸引力等流行开发工具分发。 它是免费的五个开发人员团队为或更少之后可以购买哪些其他许可证以适应不断增长的团队。
 - TFS 是专为在本地 Windows server 设计，并通过本地网络或与该网络的 VPN 连接访问。 其主要优点是您完全控制的生成服务器的配置和可以安装不需要任何其他软件或服务。 TFS 具有对于小团队免费的入门级 Express 版本。

TFS 和 VSTS 与 Visual Studio 紧密集成，并允许开发人员可以执行许多版本控制和从中的单个 IDE 舒适的 CI 任务。 适用于 Eclipse （见下文） 的 Team Explorer Everywhere 插件也是可用的。 适用于 Mac 的 visual Studio 不提供任何支持 TFS 或 VSTS。

Visual Studio 团队服务的生成系统具有直接支持对于 Xamarin 项目，在其中创建要目标 （Android、 iOS 和 Windows） 每个平台的生成定义。 相应的 Xamarin 许可证，需要为每个生成定义。 也可以连接本地，Xamarin 支持 TFS 生成到为此目的的 Visual Studio Team Services 的服务器。 通过此设置，可以将生成排队发送至 VSTS 委派给本地服务器。 有关详细信息，请参阅[部署和配置生成服务器](https://msdn.microsoft.com/en-us/library/ms181712.aspx)。 或者，你可以使用另一生成工具，如 Jenkins 或团队城市。

所有应用程序生命周期管理 (ALM) 功能的 Visual Studio、 Visual Studio Team Services 和 Team Foundation Server，请参阅完整的摘要[适用于 Xamarin 应用的应用程序生命周期管理](https://msdn.microsoft.com/en-us/library/mt162217(v=vs.140).aspx)MSDN 上。


### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](http://msdn.microsoft.com/en-us/library/gg413285.aspx)将 Team Foundation Server 和 Visual Studio Team Services 的强大功能引入到 Visual Studio 外部开发团队。 它允许开发人员有关 OS X 和 Linux 从 Eclipse 或跨平台命令行客户端连接到团队项目在本地或云中。 团队资源管理器无处不在提供完全访问到版本控制 （包括 Git），工作项和生成非 Windows 平台的功能。


### <a name="git"></a>Git

[Git](http://git-scm.com)是一种流行的开放源代码版本控制解决方案，最初开发管理 Linux 内核的源代码。 它是一个非常快速、 灵活的系统，经常使用的所有大小的软件项目。 它可轻松扩展具有缺少 Internet 访问的单个开发人员从对分布在全球范围的大型团队。 Git 还会使分支变得非常简单，这反过来可鼓励使用风险降至最低开发的并行流。

Git 可操作完全通过 web 浏览器中，或通过[GUI 客户端](http://git-scm.com/downloads/guis)在 Linux、 Mac OSX 和 Windows 上运行。 它是免费的公共存储库;专用的存储库需要[付费计划](https://github.com/pricing)。

Visual Studio 2015 和 Visual Studio for Mac 提供本机支持 git;Microsoft Visual Studio 2013 及更早版本，提供[git 可下载扩展](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c)。 如上所述，Visual Studio Team Services 和 TFS 可以使用 Git 进行版本控制而不是 TFVC。


### <a name="subversion"></a>子版本号

[Subversion](http://subversion.apache.org) (SVN) 是一直在 2000年以来使用一个受欢迎，开放源代码版本控制系统。 在所有最新版本的 OS X、 Windows、 FreeBSD、 Linux 和 Unix 上运行 SVN。 适用于 Mac 的 visual Studio 具有用于 SVN 的本机支持。 有 SVN 支持置于 Visual Studio 的第三方扩展。


## <a name="continuous-integration-environments"></a>持续集成环境

设置持续集成环境意味着将与生成服务结合使用版本控制系统。  对于后者，两个最常见原因是：

- [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) Visual Studio Team Services 和 TFS 的生成系统。 它与 Visual Studio 中，它方便开发人员触发生成，从而使自动运行测试，并查看结果紧密集成。
- Jenkins 是一个开放源代码 CI 服务器使用插件以支持软件开发的所有类型的丰富生态系统。 它在 Windows 上运行和 Mac OS X.Jenkins 未与任何特定 IDE 集成。 相反，它是配置，并通过 web 界面管理。 Jenkins CI 很容易安装和配置这使得变得小团队有吸引力。

你可以使用 TFS/VSTS 本身，也可以使用 Jenkins 结合 TFS/VSTS 或 Git 中的以下部分所述。

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services 和 Team Foundation Server

如所述，Visual Studio Team Services 和 Team Foundation Server 提供了这两个版本控制和生成服务。 生成服务始终需要用于每个目标平台的 Xamarin 业务或企业版许可证。

使用 Visual Studio Team Services，要创建每个目标平台的单独的生成定义，并输入存在适当的许可。 VSTS 配置后，将运行生成和测试在云中。 请参阅[Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview)有关详细信息。

使用 Team Foundation Server，你配置生成计算机为特定的目标平台，如下所示：

- **Android 和 Windows:**安装 Visual Studio 和 Xamarin 工具 （适用于 Android 和 Windows 这两个） 和配置与你的 Xamarin 许可证。 它也是需要移动到其中的 TFS 生成代理服务器上的共享位置 Android SDK 可以找到它。 有关详细信息，请参阅[配置 TFVC](https://docs.microsoft.com/vsts/tfvc/overview)。
- **iOS 和 Xamarin:**安装 Visual Studio 和具有适当的许可的 Windows 服务器上的 Xamarin 工具。 然后安装 Visual Studio for Mac 在网络可访问的 Mac OS X 计算机，也不能将作为生成主机创建最终的应用包 (IPA 对于 iOS，适用于 OS X 应用) 上。

下图说明了此拓扑：

[![](intro-to-ci-images/intro03-small.png "此图描述了此拓扑")](intro-to-ci-images/intro03.png)

它还可将本地 TFS 服务器链接到 Visual Studio Team Services 项目，以便 VSTS 生成委托给本地服务器。 有关详细信息，请参阅[部署和配置生成服务器](http://msdn.microsoft.com/en-us/library/ms181712.aspx)MSDN 上。

### <a name="visual-studio-team-services-and-jenkins"></a>Visual Studio Team Services 和 Jenkins

如果你使用 Jenkins 生成你的应用，你可以在 Visual Studio Team Services 或 Team Foundation Server 中存储你的代码，并继续用于 Jenkins CI 生成。 Jenkins 生成的数据时会触发代码推送到你的团队项目的 Git 存储库或者当你检查代码到 TFVC。 有关详细信息，请参阅[利用 Visual Studio Team Services Jenkins](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/services/jenkins)。

[![](intro-to-ci-images/intro04-small.png "如果你使用 Jenkins 生成你的应用，你可以在 Visual Studio Team Services 或 Team Foundation Server 中存储你的代码，并继续用于 Jenkins CI 生成")](intro-to-ci-images/intro04.png)

### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一个常见的 CI 环境可以是完全的 OS X 基于。 此方案涉及到使用 Git 源代码管理和 Jenkins 的生成服务器。 这两种运行使用 Visual Studio 一台 Mac OS X 计算机上安装的 mac。 这是非常类似于 Visual Studio Team Services + Jenkins 环境上一节中所述：

[![](intro-to-ci-images/intro05-small.png "这是非常类似于 Visual Studio Team Services + 上一节中讨论的 Jenkins 环境")](intro-to-ci-images/intro05.png)

> [!IMPORTANT]
> **注意： Jenkins 是[不支持 Xamarin](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)。**


# <a name="summary"></a>摘要

持续集成的概念以及它给软件开发团队带来的优势，引入了此文档。 版本控制的重要性以及角色和职责的生成服务器所讨论。 该文档然后发往讨论一些可以用于源代码管理和生成服务器的工具。 我们还引入了应用 Center 测试，可帮助开发人员通过运行自动的测试，将证明的质量和其应用程序的功能来发布强大的应用程序。 提交应用和测试应用程序 Center 找不到的文档的更详细[此处](https://docs.microsoft.com/en-us/appcenter/test-cloud)。 最后，以帮助了解我们如何所有这些工具和组件组合在一起，概述组织可能建立持续集成的多个不同的 CI 环境。 通过 Xamarin 项目使用 Visual Studio Team Services 和 Team Foundation Server 的详细信息，请参阅[配置 TFVC](https://docs.microsoft.com/vsts/tfvc/overview) ，这[持续集成简介](https://docs.microsoft.com/en-us/vsts/build-release/actions/ci-cd-part-1)。 同样，如果你使用 Jenkins，请参阅[使用 Xamarin 使用 Jenkins](~/tools/ci/jenkins-walkthrough.md)有关设置持续集成的详细信息。