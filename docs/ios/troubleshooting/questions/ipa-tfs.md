---
title: 如何将 IPA 输出文件复制到 TFS 放置文件夹？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
ms.locfileid: "31883704"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何将 IPA 输出文件复制到 TFS 放置文件夹？

打开`.csproj`文件，以在文本编辑器中的 iOS 应用项目，然后再在末尾添加以下行 (在关闭之前立即`</Project>`标记):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>说明

-   这是上所述的相同常规技术[可以更改 IPA 文件的输出路径？](~/ios/troubleshooting/questions/ipa-output-path.md)。 要设置两个重要事项`$(TF_BUILD_BINARIESDIRECTORY)`作为目标文件夹，并因此将添加额外条件`CopyIpa`才会运行为 TFS 版本。

-   有关的说明`TF_BUILD_BINARIESDIRECTORY`请参阅[ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx)。

## <a name="additional-references"></a>其他参考

- [用于 Xamarin 安装 TFS 的文档](https://docs.microsoft.com/vsts/tfvc/overview)
- [TFS 生成任务： Xamarin.Android](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [TFS 生成任务： Xamarin.iOS](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>后续步骤
本文档讨论为 Visual Studio 的 Xamarin 3.11.666 截至的当前行为和 Xamarin.iOS 8.10.3 在 Mac 上的构建的主机。 有关进一步的帮助，请与我们联系，或如果此问题仍然存在即使利用的上述信息，请参阅[哪些支持选项均可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要的文件的新 bug。 



