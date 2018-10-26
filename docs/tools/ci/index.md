---
title: 与 Xamarin 持续集成简介
description: 本文档所链接到指南描述与 Xamarin 持续集成。 链接的内容提供了持续集成的概述，并讨论了应用中心的生成、 TeamCity 和 Jenkins。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: lobrien
ms.author: laobri
ms.date: 10/23/2018
ms.openlocfilehash: 9171eeb461f2e4d363de6e01ea92fa448f04d41a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121628"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>与 Xamarin 持续集成简介

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[持续集成简介](~/tools/ci/intro-to-ci.md)

本部分介绍通过持续集成和它们之间的关系所涉及的不同组件。 它概述了特定的以下各节所述的持续集成环境。

## <a name="devops-with-xamarintoolscidevopsmd"></a>[使用 Xamarin 进行开发运营](~/tools/ci/devops.md)

本部分介绍的 DevOps 功能在 Azure 中，你可能需要很好地配合 Xamarin 项目的 Visual Studio。

## <a name="working-with-continuous-integration-environments"></a>使用持续集成环境

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[构建 Xamarin 应用程序与 Azure 的管道](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

使用 Azure 管道自动构建面向 Android 和 iOS 的 Xamarin 应用。

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[构建 Xamarin 应用使用 App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

直接从 GitHub、 Azure DevOps 或 Bitbucket 中生成 Xamarin.iOS 和 Xamarin.Android 使用 App Center 的解决方案。

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[生成具有 TeamCity 的 Xamarin 应用](~/tools/ci/teamcity.md)

本指南介绍使用 TeamCity 编译移动应用，然后将它们提交到 App Center Test 所涉及的步骤。

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[生成具有 Jenkins 的 Xamarin 应用](~/tools/ci/jenkins-walkthrough.md)

本指南说明如何设置 Jenkins 持续集成服务器，并自动执行编译使用 Xamarin 创建的移动应用。 它介绍如何在 OS X 上安装 Jenkins，其进行配置，并将作业设置为时更改会提交到版本控制系统编译 Xamarin.iOS 和 Xamarin.Android 应用。
