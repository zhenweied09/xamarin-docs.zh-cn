---
title: "通过 Xamarin 为什么不支持 Jenkins？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8129229a821edd2ef4f251679ee46bca7b74c8f9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>通过 Xamarin 为什么不支持 Jenkins？

## <a name="jenkins-support-explanation"></a>Jenkins 支持说明

Jenkins 是开放源代码 CI 套件;由于这许多问题而直接引起 Jenkins*本身*需要被归为问题对你的代码; 例如[主要 Jenkins 存储库](https://github.com/jenkinsci/jenkins)，或的存储库[Jenkins.app](https://github.com/stisti/jenkins-app)。

此规则的例外是问题，可以隔离到特定 Xamarin 的工具; 中的 bug如果你怀疑存在这种情况下可以检查你[支持选项](~/cross-platform/troubleshooting/support-options.md)，但同样，此问题可能是某个地方外部 Xamarin 支持团队可以*直接*帮助。

## <a name="setup-jenkins-with-xamarin"></a>使用 Xamarin 安装程序 Jenkins

虽然如上所示 Jenkins 问题不支持直接通过我们的团队;[使用 Xamarin 使用 Jenkins](~/tools/ci/jenkins-walkthrough.md)指南可以用于设置 Jenkins CI 服务器与 Xamarin 集成。 

## <a name="fixes-for-common-issues"></a>常见的问题的修补程序
### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 是找不到 Android SDK

此问题的错误消息为类似如下内容：

> 错误 XA5205： 找不到 Android SDK 目录。 请通过 /p:AndroidSdkDirectory 设置

设置为 SDK 的位置的选项可能会有所不同，具体取决于你使用; 确切 Jenkins Android 插件查找有关如何将此项设置的好时机是插件指南中。 例如，[Android 仿真程序插件](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)自动查找的 SDK，但如果它找不到它; 还可以通过该插件的 Jenkins 系统配置页设置的位置。 


## <a name="deprecated-errors"></a>不推荐使用的错误

> [!IMPORTANT]
> Xamarin 的最新版本中，此问题已得到解决。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 报告了无效的 Xamarin 许可证
此问题的错误消息通常是类似于

> XA9008 错误： 从命令行生成需要使用一个业务许可证

或

> 错误： 初学者 Xamarin.iOS 版本不支持在 Xamarin Studio 之外的生成 

此方案的最常见原因是 Jenkins 使用通过不与 Xamarin 许可证关联的用户帐户登录。 此操作，请解决的最简单的方法是安装 Jenkins 作为直接通过用户帐户应用。 下面介绍过程和一些其他注意事项： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)

另一种可能是你 Xamarin 许可证信息已损坏由于某种原因，你可以使用[Xamarin 许可证重新同步指南](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)以对此情况进行疑难解答。


