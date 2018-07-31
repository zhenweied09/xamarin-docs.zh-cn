---
title: 提交到 App Store 时的错误:"捆绑无效-不允许在 bitcode 中嵌入的选项中检测到的提交"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 393c1ed81c68d21b610781dfe09de97969e031d1
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350919"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交到 App Store 时的错误:"捆绑无效-不允许在 bitcode 中嵌入的选项中检测到的提交"

watchOS 应用和 tvOS 应用_需要_bitcode 提交到 App Store 时。 以下的错误时生成并提交 watchOS 和 tvOS 应用使用 Xcode 8.3 或更早版本，可能会发生 （通过电子邮件通知） 时尝试将上传到应用商店：

>捆绑无效-不能处理应用程序，因为在提交中检测到不允许在 bitcode 中嵌入的选项。 很可能不构建应用程序与在 Xcode 中提供的工具链。

此问题的解决方案是生成使用 Xcode 9 和最新版本的 Xamarin.iOS 应用程序。
