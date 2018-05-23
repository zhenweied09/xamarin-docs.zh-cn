---
title: 解决库安装错误
description: 在某些情况下，可能会安装 Android 支持库时收到错误。 本指南提供了一些常见的错误的解决方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2018
ms.openlocfilehash: a54c69ff708ff7438ef1a8fd14c17e77b5375039
ms.sourcegitcommit: f52aa66de4d07bc00931ac8af791d4c33ee1ea04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="resolving-library-installation-errors"></a>解决库安装错误

_在某些情况下，可能会安装 Android 支持库时收到错误。本指南提供了一些常见的错误的解决方法。_

## <a name="overview"></a>概述

时生成 Xamarin.Android 应用程序项目，Visual Studio 或 Visual Studio for Mac 尝试下载并安装依赖项库时，可能会生成错误。 许多这些错误是由网络连接问题、 文件损坏或版本控制问题导致的。 此指南描述的最常见的支持库安装错误，并提供解决这些问题和获取再次生成应用程序项目的步骤。 

 
 
## <a name="errors-while-downloading-m2repository"></a>下载 m2Repository 时的错误

你可能会看到**m2repository**引用的 Android 支持库或 Google Play 服务 NuGet 包时的错误。 该错误消息类似于以下内容：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

此示例适用于**android\_m2repository\_r16**，但你可能看到此相同的错误消息的不同版本，如**android\_m2repository\_r18**或**android\_m2repository\_r25**。 



### <a name="automatic-recovery-from-m2repository-errors"></a>从 m2repository 错误自动恢复 

通常情况下，可以通过删除有问题的库和重建根据以下步骤解决此问题： 

1. 导航到你的计算机上的支持库目录：

    -   在 Windows 上，支持库位于**c:\\用户\\_用户名_\\AppData\\本地\\Xamarin**。 

    -   在 Mac OS X 上，支持库位于 **/Users/_用户名_/.local/share/Xamarin**。 

2. 找到对应的错误消息的库和版本的文件夹。 例如，上面的错误消息的库和版本文件夹位于**Android.Support.v4\\22.2.1**:

    [![支持库的 22.2.1 的示例文件夹位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. 删除版本文件夹的内容。 请务必删除 **.zip**文件以及**内容**和**嵌入**此文件夹中的子目录。 上面的文件和子目录显示此屏幕截图中所示的示例错误消息 (**内容**，**嵌入**，和**android_m2repository_r16.zip**) 都是到删除：

    [![示例内容 22.2.1 支持库文件夹](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   请注意，若要删除的重要的是*整个*此文件夹的内容。 尽管此文件夹可能最初包含框 **android\_m2repository\_r16.zip**文件，此文件可能已部分下载或已损坏。

4. 重新生成项目&ndash;这样做将导致生成过程以重新下载缺少的库。

在大多数情况下，这些步骤将解决生成错误并允许您继续。 如果删除此库不能解决生成错误，必须手动下载并安装**android\_m2repository\_r_nn_.zip**文件中的下一节所述。 



### <a name="manually-downloading-m2repository"></a>手动下载 m2repository

如果你已尝试使用上述自动恢复步骤，并且仍具有生成错误，则可以手动下载**android\_m2repository\_r_nn_.zip**文件 （使用 web 浏览器） 并将其根据安装以下步骤。 此过程也是有用的如果你的开发计算机上没有 internet 访问权限，但能够下载存档使用不同的计算机。 

1.  下载**android\_m2repository\_r_nn_.zip**对应的错误消息文件&ndash;（以及每个链接的相应 MD5 哈希以下列表中提供了链接URL):

    -   [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    如果**m2repository**存档中不会显示此表中，你可以创建通过预先计算的下载 URL **https://dl-ssl.google.com/android/repository/** 的名称**m2repository**下载。 例如，使用 **https://dl-ssl.google.com/android/repository/android \_m2repository\_r10.zip**下载**android\_m2repository\_r10.zip**。

2.  将该文件重命名为上述表中所示的下载 URL 的相应 MD5 哈希。 例如，如果你下载**android\_m2repository\_r25.zip**，将其命名为**0B3F1796C97C707339FB13AE8507AF50.zip**。 如果下载的文件的下载 URL 的 MD5 哈希表中未显示，则可以使用[联机 MD5 生成器](http://www.webconfs.com/online-md5-generator.php)将 URL 转换为一个 MD5 哈希字符串。 

3.  将文件复制到 Xamarin**快速**文件夹： 

    -   在 Windows 上，此文件夹位于**c:\\用户\\***用户名***\\AppData\\本地\\Xamarin\\快速**。 

    -   在 Mac OS X 上，此文件夹位于 **/Users/***用户名***/.local/share/Xamarin/zips**。 

    例如，下面的屏幕截图显示了结果时**android\_m2repository\_r16.zip**会下载和重命名为其下载 URL 在 Windows 上的 MD5 哈希：

    [![正在重命名为 0595E577D19D31708195A83087881EE6.zip r16.zip 存储库的示例](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


如果此过程不能解决生成错误，则必须手动下载**android\_m2repository\_r_nn_.zip**文件，请将其解压缩，并在下一部分中所述安装其内容。 


### <a name="manually-downloading-and-installing-m2repository-files"></a>手动下载和安装 m2repository 文件

从恢复的完全手动过程**m2repository**错误需要下载**android\_m2repository\_r_nn_.zip**文件 （使用 web 浏览器），解压缩它，并将其内容复制到你的计算机上的支持库目录。 在下面的示例中，我们将从此错误消息中恢复： 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用以下步骤来下载**m2repository**和安装其内容：

1.  删除对应于错误消息的库文件夹的内容。 例如，上面的错误消息中将删除的内容**c:\\用户\\***用户名***\\AppData\\本地\\Xamarin\\Android.Support.v4\\23.1.1.0**。 
    如前面所述，你必须删除此目录的全部内容：

    [![删除内容，嵌入，和从 23.1.1.0 android_m2repository 文件夹文件夹](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2.  下载**android\_m2repository\_r_nn_.zip**文件从 Google 对应于错误消息 （请参见链接的上一节中的表）。

3.  提取此 **.zip**存档到任何位置 （例如桌面）。 这应会创建目录的名称对应 **.zip**存档。 在此目录中，你应会发现调用子目录**m2repository**: 

    [![提取的 zip 存档中找到的 m2repository 文件夹](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4.  在版本控制的库目录中清除在步骤 1 中，重新创建**内容**和**嵌入**子目录。 例如，下面的屏幕截图显示了**内容**和**嵌入**子目录中创建**23.1.1.0**文件夹**android\_m2repository\_r25.zip**: 

    [![创建内容和嵌入的文件夹中 23.1.1.0 文件夹](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5.  复制**m2repository**从提取 **.zip**到**内容**你在上一步中创建的目录： 

    [![M2repository 复制到 23.1.1.0/content 文件夹中的屏幕截图](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6.  在提取 **.zip**目录中，浏览到**m2repository\\com\\android\\支持\\支持 v4**和中打开相应的文件夹上面创建的版本号 (在此示例中， **23.1.1**):

    [![示例 support-v4/23.1.1 文件夹中包含的文件列表](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7.  将所有文件复制到此文件夹中**嵌入**在步骤 4 中创建目录：

    [![文件复制到 23.1.1.0/embedded 文件夹中的示例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8.  验证通过将复制所有文件。 **嵌入**目录现在应包含文件如 **.jar**， **.aar**，和 **.pom**。

9.  任何提取的内容解压缩 **.aar**文件到**嵌入**目录。 在 Windows 上，追加 **.zip**扩展 **.aar**文件，打开它，并复制到内容**嵌入**目录。
    在 macOS 上, 解压缩 **.aar**文件使用**解压缩**命令在终端中 (例如，**解压缩 file.aar**)。

此时，你已手动安装缺少的组件和你的项目应顺利生成且未发生错误。 如果没有，请验证你已下载**m2repository** **.zip**存档完全与在错误消息中，版本相对应的版本，并验证已安装在其内容更正位置，如上述步骤中所述。 



## <a name="summary"></a>总结 

本文介绍了如何从期间自动下载和安装的依赖项库可以进行的常见错误中恢复。 它描述如何删除有问题的库，作为一种方式重新下载并重新安装库重新生成项目。 它描述了如何下载库并将其在安装**快速**文件夹。 它还描述了用于手动下载并安装所需的文件作为一种方式，若要解决无法通过自动方式解决的问题的更多地涉及的过程。 
