---
title: 提交到应用商店时的错误:"提交对无效捆绑-不允许在 bitcode 中嵌入的选项是否检测到"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: cacb9040ddc8582490c68bcfd24e80c4c4679eb4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交到应用商店时的错误:"提交对无效捆绑-不允许在 bitcode 中嵌入的选项是否检测到"

watchOS 应用和 tvOS 应用_需要_bitcode 在提交到应用商店时。 生成和提交 watchOS 和 tvOS 使用 Xcode 8.3 或更早版本的应用，以下错误可能会发生 （通过电子邮件通知） 尝试上载到应用商店时：

>无效的捆绑-无法处理应用程序，因为不允许在 bitcode 中嵌入的选项是否检测到提交。 很可能不生成应用程序与在 Xcode 中提供了工具链。

此问题的解决方案是生成使用 Xcode 9 和最新版本的 Xamarin.iOS 应用程序。
