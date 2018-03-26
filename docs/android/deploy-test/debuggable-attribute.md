---
title: 可调试属性
ms.topic: article
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: fe516a9780b8b1cdc478a49fe3b6963097649a80
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2018
---
# <a name="debuggable-attribute"></a>可调试属性



若要进行调试，Android 需支持 Java 调试线协议 (JDWP)。 这是一种允许 ADB 等工具与 JVM 通信的技术。 尽管在开发期间 JDWP 非常重要，仍应在发布应用程序之前将其禁用。

JDWP 可以是 Android 应用程序中 `android:debuggable` 属性的值。 Xamarin.Android 提供了以下方式来设置此属性：

1.  创建 `AndroidManifext.xml` 文件，并在其中设置 `android:debuggable` 属性。
2.  将 `ApplicationAttribute` 包含在 `.CS` 文件中，例如：`[assembly: Application(Debuggable=false)]`。


如果 `AndroidManifest.xml` 和 `ApplicationAttribute` 均存在，则 `AndroidManifest.xml` 的内容将优先于 `ApplicationAttribute` 所指定的内容。

如果既没有 `AndroidManifest.xml` 也没有 `ApplicationAttribute`，则 `android:debuggable` 属性的默认值将取决于是否生成了调试符号。 如果调试符号存在，则 Xamarin.Android 会将 `android:debuggable` 属性设为 `true`。

请注意，`android:debuggable` 属性的值不一定依赖于生成配置。 发行版本可能会将 `android:debuggable` 属性设为 true。


## <a name="related-links"></a>相关链接

- [Android 市场中的可调试应用](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
