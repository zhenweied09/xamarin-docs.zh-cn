---
title: Xamarin 防火墙配置说明
description: 一个主机列表，需要在防火墙中允许这些列表才可让 Xamarin 的平台适合你的公司。
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 34ac0b0eb2563b2122d7b093b19ac3a6ac78cbe5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-firewall-configuration-instructions"></a>Xamarin 防火墙配置说明

_一个主机列表，需要在防火墙中允许这些列表才可让 Xamarin 的平台适合你的公司。_

为了使 Xamarin 产品正常安装和工作，必须有权访问某些终结点，下载软件必需的工具和更新。 如果你或你公司的防火墙设置严苛，则可能遇到安装、授权和组件等方面的问题。 本文档概述了一些需要在防火墙中允许以使 Xamarin 正常工作的已知终结点。 本列表不包括下载文件中的任何第三方工具所需的终结点。 如果仔细检查此列表后仍遇到问题，请参阅 Apple 或 Android 安装疑难解答指南。

## <a name="endpoints-to-whitelist"></a>允许列表终结点

### <a name="xamarin-installer"></a>Xamarin 安装程序

需要添加以下已知地址，以便使用 Xamarin 安装程序的最新版本时正常安装软件：

-  xamarin.com（安装程序清单）
-  dl.xamarin.com（包下载位置）
-  dl.google.com（用于下载 Android SDK）
-  download.oracle.com (JDK)
-  visualstudio.com（安装包下载位置）
-  go.microsoft.com（安装程序 URL 解析）
-  aka.ms（安装程序 URL 解析）

如果正使用 Mac，并且遇到 Xamarin.Android 安装问题，请确保 macOS 能够下载 Java。


### <a name="components-store-and-nuget-including-xamarinforms"></a>Components Store 和 NuGet（包括 Xamarin.Forms）

需要添加以下地址才可访问 Xamarin Component Store 或 NuGet（Xamarin.Forms 打包为 NuGet）：

-  components.xamarin.com（用于使用 Xamarin Components Store）
-  xampubdl.blob.core.windows.net（托管 Components Store 下载）
-  www\.nuget.org（用于访问 NuGet）
-  az320820.vo.msecnd.net（NuGet 下载文件）
-  dl-ssl.google.com（Google 组件）


### <a name="software-updates"></a>软件更新

需要添加以下地址，确保可正常下载该软件：

-  software.xamarin.com（更新程序服务）
-  download.visualstudio.microsoft.com
-  dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Xamarin Mac 代理

若要使用 Xamarin Mac 代理将 Visual Studio 连接到 Mac 生成主机， 需要打开 SSH 端口。 该端口默认为**端口 22**。

## <a name="summary"></a>总结

本指南介绍了需要加入允许列表以使 Xamarin 产品能够在计算机上正常安装和更新的终结点。
