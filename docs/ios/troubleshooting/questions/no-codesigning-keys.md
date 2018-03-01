---
title: "为什么不会通过与 iOS 生成： 在 keychain 中找到签名密钥没有有效的 iPhone 代码？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 6a853bc7186647dbdae1d5d12f3b185d302a8088
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>为什么不会通过与 iOS 生成： 在 keychain 中找到签名密钥没有有效的 iPhone 代码？

## <a name="cause-of-the-error"></a>错误的原因
此错误消息时发生问题项目寻找代码签名的有效凭据，但找不到它们。 代码签名是测试和在物理 iOS 设备; 上的部署所必需的以及在存储生成的临时 （&） 应用程序。 


### <a name="provisioning-devices"></a>预配设备
如果你尚未设置 iOS 设备之前，以下指南将指导您完成的完整分步过程：[设备资源调配指南](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模拟器时的 bug

> [!NOTE]
> 此问题已得到解决在最新版本的 Xamarin for Visual Studio。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。


这将导致 Xamarin.Forms 模板添加 codesign 到模拟器的 Entitlements.plist 生成; 中的 iOS 项目 Xamarin.Visual Studio 3.11 出现 bug有效地阻止使用模拟器进行测试。

### <a name="how-to-fix"></a>如何修复错误
要解决该问题可以通过移除`<CodesignEntitlements>`.csproj 文件中生成从调试的标志。 你可以按如下所示进行操作：

*警告：.csproj 文件中的出现错误可以中断你的项目，因此很好办法在尝试此之前备份你的文件。*

1. 右键单击解决方案窗格中的 iOS 项目，然后选择**卸载项目**
2. 右键单击项目，然后选择**编辑 [ProjectName].csproj**
3. 找到调试移除，它们应该开头，如下所示的标志：
   - 调试： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 发行版： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在每个使用模拟器的生成，删除或注释掉以下属性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重新加载项目，你应该能够将部署到模拟器。

### <a name="next-steps"></a>后续步骤
有关进一步的帮助，请与我们联系，或如果此问题仍然存在即使利用的上述信息，请参阅[哪些支持选项均可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要的文件的新 bug。 
