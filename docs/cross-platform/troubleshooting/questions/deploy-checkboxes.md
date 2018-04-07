---
title: “部署”复选框在 Configuration Manager 中处于禁用状态
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: c0f116565a2741c62a00ed2a255cfde8c57b8569
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>“部署”复选框在 Configuration Manager 中处于禁用状态

从 Xamarin 3.5 起，无论何时按“开始”工具栏按钮或选择“调试”>“开始调试”菜单项，Xamarin.iOS 项目都会自动进行部署。 但仍需要将所需的 Xamarin.iOS 应用项目设为**启动项目**，然后才能运行上述任一命令。

因此，会在 Visual Studio Configuration Manager for Xamarin.iOS 项目中故意禁用“部署”复选框：

![](deploy-checkboxes-images/configuration.png "Visual Studio 配置管理器显示禁用 Xamarin 3.5 中的 Xamarin.iOS 项目的部署复选框")

此更改可消除在较旧版本的 Xamarin（版本 3.3 及更早版本）中，当 Xamarin.iOS 应用项目尚未设为待部署时，可能出现的错误：

![](deploy-checkboxes-images/error.png "错误对话框： 项目 iPhoneApp1 必须将其部署，然后才能启动。验证选择了项目时要部署解决方案 Configuration Manager 中。")
