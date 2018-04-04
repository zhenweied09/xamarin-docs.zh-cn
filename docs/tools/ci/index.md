---
title: 使用 Xamarin 的连续集成简介
description: 持续集成是一种软件工程做法在其中为自动化的生成将编译并 （可选） 测试应用程序添加或更改项目的版本控制存储库中的开发人员代码时。 本文将讨论持续集成的一般概念以及一些适用的选项，持续集成与 Xamarin 项目。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/04/2017
ms.openlocfilehash: b5bccfa38a9f382789585284765183efa42b6a3d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>使用 Xamarin 的连续集成简介

_持续集成是一种软件工程做法在其中为自动化的生成将编译并 （可选） 测试应用程序添加或更改项目的版本控制存储库中的开发人员代码时。本文将讨论持续集成的一般概念以及一些适用的选项，持续集成与 Xamarin 项目。_

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]


##  <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[持续集成简介](~/tools/ci/intro-to-ci.md)

本部分介绍使用持续集成和它们之间的关系所涉及的不同组件。 它概述了特定的以下各节所述的持续集成环境。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="working-with-continuous-integration-environments"></a>使用持续集成环境


### <a name="using-app-center-build-with-xamarinappcenterbuildxamarin"></a>[结合使用 App Center 版本和 Xamarin](/appcenter/build/xamarin/)

直接从 GitHub、 VSTS 或 Bitbucket 生成 Xamarin.iOS 和 Xamarin.Android 应用 Center 解决方案。

### <a name="using-teamcity-with-xamarintoolsciteamcitymd"></a>[结合使用 TeamCity 和 Xamarin](~/tools/ci/teamcity.md)

本指南介绍使用 TeamCity 编译移动应用，然后将它们提交到 Xamarin 测试云所涉及的步骤。

###  <a name="using-jenkins-with-xamarintoolscijenkins-walkthroughmd"></a>[结合使用 Jenkins 和 Xamarin](~/tools/ci/jenkins-walkthrough.md)

本指南演示如何设置为持续集成服务器 Jenkins 和自动执行编译使用 Xamarin 创建的移动应用。 它描述如何在 OS X 上安装 Jenkins、 配置和设置来编译 Xamarin.iOS 和 Xamarin.Android 应用程序时更改会提交到版本控制系统的作业。

