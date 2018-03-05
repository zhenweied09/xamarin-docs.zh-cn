---
title: "可调试属性"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: db09ebe29b6c404bac892fd76389faf0b9e03d5b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="debuggable-attribute"></a>可调试属性

<a name="Overview" />


若要进行调试，Android 需支持 Java 调试线协议 (JDWP)。 这是一种允许 ADB 等工具与 JVM 通信的技术。 尽管在开发期间 JDWP 非常重要，仍应在发布应用程序之前将其禁用。

JDWP 可以是 Android 应用程序中 `android:debuggable` 属性的值。 Xamarin.Android 提供了以下方式来设置此属性：

1.  创建 `AndroidManifext.xml` 文件，并在其中设置 `android:debuggable` 属性。
1.  将 `ApplicationAttribute` 包含在 `.CS` 文件中，例如：`[assembly: Application(Debuggable=false)]`。


如果 `AndroidManifest.xml` 和 `ApplicationAttribute` 均存在，则 `AndroidManifest.xml` 的内容将优先于 `ApplicationAttribute` 所指定的内容。

如果既没有 `AndroidManifest.xml` 也没有 `ApplicationAttribute`，则 `android:debuggable` 属性的默认值将取决于是否生成了调试符号。 如果调试符号存在，则 Xamarin.Android 会将 `android:debuggable` 属性设为 `true`。

请注意，`android:debuggable` 属性的值不一定依赖于生成配置。 发行版本可能会将 `android:debuggable` 属性设为 true。


## <a name="related-links"></a>相关链接

- [Android 市场中的可调试应用](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
