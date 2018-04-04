---
title: 是否可以从 Visual Studio 创建.xcarchive 存档？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 23af3bf277ab68ffe93df2e1ee8ee64afc01828d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>是否可以从 Visual Studio 创建.xcarchive 存档？

## <a name="for-xamarin-4"></a>为 Xamarin 4

截至 Xamarin 4.x，现可以创建`.xcarchive`通过设置在 Windows 中`ArchiveOnBuild`属性`true`。 例如，使用`MSBuild`命令行上：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive`将放入`$HOME/Library/Developer/Xcode/Archives`Xcode 和 Xamarin Studio 搜索显示以前生成的存档的 Mac 生成主机上的目录。

请参阅此[Xamarin 论坛文章](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)的一些有关简短的其他说明`ArchiveOnBuild`属性。 请参阅文档， [Xamarin.iOS 命令行生成在 Windows 上](~/ios/get-started/installation/windows/connecting-to-mac/index.md)有关其他详细信息`ServerAddress`和`ServerUser`属性。

* * *

## <a name="for-xamarin-3-and-earlier"></a>为 Xamarin 3 和更早版本

Xamarin 3.x Visual Studio 扩展不提供一种机制来生成`.xcarchive`存档。 话虽如此，用来创建的逻辑`.xcarchive`存档在 Mac 上的 Xamarin Studio[此处所述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)，因此你可能无法创建你自己`.xcarchive`手动如果您希望。

但值得注意，不需要的是`.xcarchive`以提交到应用商店。 只要使用应用商店分发配置文件 （而非 Ad Hoc 分发配置文件） 对其签名，你可以提交 IPA 文件。

事实上，你可以即使只是压缩`.app`捆绑包 （即使用应用程序存储分发配置文件进行签名），并将该提交`.zip`应用商店的文件。

在任一情况下，你可以使用应用程序加载程序应用程序提交应用程序 （而不 Xcode）。

