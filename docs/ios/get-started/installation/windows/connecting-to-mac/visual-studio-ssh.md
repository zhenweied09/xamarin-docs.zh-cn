---
title: 我的 Mac 位于何处？
description: 配置 Mac for Visual Studio 以生成 Xamarin.iOS 项目的相关说明
ms.prod: xamarin
ms.assetid: 4f792690-b3b1-4d56-a5e2-363b4f246059
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f3e2988c9700549db24ad69277df9e412c99caed
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2018
---
# <a name="wheres-my-mac"></a>我的 Mac 位于何处？

配置 Mac for Visual Studio 以生成 Xamarin.iOS 项目的相关说明

相较于以前的版本[^](#earlier-versions)，Xamarin for Visual Studio 的当前**稳定版**与 Mac 的交互方式不同。

若要将 Mac 用作 Xamarin Mac 代理，必须在 Mac 上启用“远程登录”。

1. 打开 Spotlight (`Cmd-Space`) 并搜索“远程登录”，然后选择“共享”结果。 系统将在“共享”面板打开“系统首选项”。

  ![](visual-studio-ssh-images/spotlight.png "用于远程登录的 Spotlight 搜索")

2. 在左侧的“服务”列表中标记“远程登录”选项，允许 Xamarin for Visual Studio 连接到 Mac。

  ![](visual-studio-ssh-images/sharing.png "在“服务”列表中选中“远程登录”选项")

3. 确保“远程登录”设为允许访问“所有用户”，或者 Mac 用户名或组包含在右侧的允许用户列表中。

如果 Mac 位于相同网络上，则 Visual Studio 现也可发现 Mac。
Visual Studio 尝试连接到 Mac 时，系统会提示使用 Mac 用户凭据从 Visual Studio 登录。

## <a name="where-can-i-find-more-information"></a>可在何处找到详细信息？

可通过下方列出的许多指南帮助设置和了解新代理：

- [连接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [疑难解答](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin University - Xamarin Mac 代理](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)

<a name="earlier-versions" />

## <a name="migrating-from-previous-versions"></a>从早期版本迁移

如果用过早期版本的 Xamarin for Visual Studio，则应熟悉 **Xamarin.iOS 生成主机**应用程序，此应用需在 Mac 上启动，然后通过 PIN 号码和 Visual Studio 实例进行配对。

此版本的 Xamarin for Visual Studio 不再需要生成主机应用程序。
