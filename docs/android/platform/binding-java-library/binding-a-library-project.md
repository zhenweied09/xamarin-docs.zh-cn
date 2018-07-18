---
title: 绑定 Eclipse 库项目
description: 本演练说明了如何使用 Xamarin.Android 项目模板将绑定一个 Eclipse Android 库项目。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1c3c33f1b958aff5818b26e4408e60f449b46f41
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764503"
---
# <a name="binding-an-eclipse-library-project"></a>绑定 Eclipse 库项目

_本演练说明了如何使用 Xamarin.Android 项目模板将绑定一个 Eclipse Android 库项目。_


## <a name="overview"></a>概述

尽管。越来越多地越来越的标准 Android 库分发 AAR 文件，在某些情况下它是创建的绑定所需*Android 库项目*。 Android 库项目是特殊的 Android 项目包含共享代码和 Android 应用程序项目可以引用的资源。 通常情况下，你将绑定到 Android 库项目时在 Eclipse IDE 中创建了库。
本演练提供了如何创建 Android 库项目的示例。ZIP 从 Eclipse 项目的目录结构。

Android 库项目不同于常规的 Android 项目，具有因为它们不会编译到 APK 和不可用，他们自己，可部署到设备。 相反，Android 库项目旨在以 Android 应用程序项目引用。 当生成的 Android 应用程序项目时，Android 库项目编译第一次。 Android 应用程序项目然后将其插入已编译的 Android 库项目，并将代码和资源包含在分发 APK。 因为这一区别，创建一个 Android 库项目的绑定是略有不同于为 Java 创建绑定。JAR 或。AAR 文件。



## <a name="walkthrough"></a>演练

若要在 Xamarin.Android Java 绑定项目中使用的 Android 库项目很首先需要构建在 Eclipse 中的 Android 库项目。 编译完成后，下面的屏幕截图显示一个 Android 库项目示例： 

[![在 Eclipse 中的示例库项目](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

请注意，从 Android 库项目源代码已编译到临时。名为的 JAR 文件**android mapviewballoons.jar**，和资源已复制到**bin/res/危机**文件夹。 

一旦已在 Eclipse 中编译的 Android 库项目，它可以然后绑定使用 Xamarin.Android Java 绑定项目。 第一个。必须创建 ZIP 文件，其中包含**bin**和**res** Android 库项目文件夹中。 很重要，删除干扰**危机**子目录，以便资源驻留在**bin/res**。下面的屏幕截图显示的内容的一个此类。ZIP 文件： 

[![Android 库的内容项目.zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

这。ZIP 文件然后添加到 Java 绑定 Xamarin.Android 项目中，如下面的屏幕截图中所示：

[![添加到 Java 绑定项目的 zip](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

请注意，生成操作的。ZIP 文件的自动设置为**LibraryProjectZip**。

如果有任何。所需的 Android 库项目的 JAR 文件，应将它们添加到**Jar** Java 绑定库项目的文件夹和**生成操作**设置为**ReferenceJar**. 可以在下面的屏幕截图中看到举例说明： 

[![生成操作设置为 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成这些步骤后，如本文档中的上前面所述，可以使用 Xamarin.Android Java 绑定项目。

> [!NOTE]
> 目前不支持在其他 Ide 编译 Android 库项目。 相同的目录结构或中的文件，可能不会创建其他 Ide **bin** Eclipse 的文件夹。 


## <a name="summary"></a>总结

在本文中，我们将遍历通过将绑定 Android 库项目的过程。 我们构建 Android 库项目在 Eclipse 中，然后我们创建了从一个 zip 文件**bin**和**res** Android 库项目文件夹中。 接下来，我们可以使用此 zip 创建 Xamarin.Android Java 绑定项目。 

