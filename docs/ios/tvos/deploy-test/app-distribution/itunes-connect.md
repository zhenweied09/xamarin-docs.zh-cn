---
title: 在 iTunes Connect 中配置你 tvOS 应用
description: 本文提供了 iOS 配置你的应用在 iTunes Connect tvOS 特定配置中的补充指南。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中配置你 tvOS 应用

_本文提供了 iOS 配置你的应用在 iTunes Connect tvOS 特定配置中的补充指南。_


除了配置和设置，你将需要进行以下 iOS[在 iTunes Connect 中配置你的应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南中，本文档介绍了需要释放 Xamarin.tvOS 的特定配置Apple 电视应用商店中的应用程序。

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>添加 tvOS 发行版本

是否要创建新的应用程序中，若要发布的 Apple 电视应用商店，或将 Apple TV 支持添加到现有的 iOS 应用程序，你将需要创建 iTunes Connect 记录并将其配置使用以下 iOS 指导特定：

- [创建 iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理应用视频和屏幕快照](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名称、说明、新增功能、关键字和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [维护的一般信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

（可选） 你可能还需要：

- [维护 Game Center 信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [维护应用内购买信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

所有完成上述步骤中，打开你的应用，iTunes Connect 记录并选择添加 tvOS 支持使用左下侧栏：

[![](itunes-connect-images/connect01.png "添加使用左下侧栏 tvOS 支持")](itunes-connect-images/connect01.png#lightbox)

TvOS 的特定信息屏幕然后将可供给定 iTunes Connect 记录：

[![](itunes-connect-images/connect02.png "TvOS 的特定信息屏幕")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本信息

从左侧栏中，选择**1.0 准备提交**tvOS 应用部分下：

[![](itunes-connect-images/connect03.png "tvOS 版本信息")](itunes-connect-images/connect03.png#lightbox)

在此屏幕上提供以下信息：

- 所需的屏幕快照、 说明、 关键字和 Url。
- 常规应用程序信息，如版本号、 版权和年龄分级。
- 可选的应用内购买。
- 通过排名和成就可选游戏中心支持。
- 所需应用程序查看信息，如联系人、 演示帐户和注释。

输入所需的信息后, 单击**保存**中以保存所做的更改屏幕的右上角的按钮：

[![](itunes-connect-images/connect04.png "tvOS 准备好进行提交的版本信息")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>准备提交供审阅

终于准备好你 Xamarin.tvOS 将应用提交到 Apple 电视应用商店，供查看时，返回到应用程序的 iTunes Connect 记录并单击**提交供其评审**屏幕的右上角的按钮：

[![](itunes-connect-images/connect05.png "提交供审阅")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本文提供在 iTunes Connect 中所需发布到 Apple 电视 App Store tvOS 应用 tvOS 特定设置的概述。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
