---
title: "疑难解答"
description: "本文介绍了解使用 Xamarin 的 tvOS 支持时，你可能会遇到的问题。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7b6d0901f8b01668626fc3b6a70a091e99e2287e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshooting"></a>疑难解答

_本文介绍了解使用 Xamarin 的 tvOS 支持时，你可能会遇到的问题。_

<a name="Known-Issues" />

## <a name="known-issues"></a>已知问题

当前版本的 Xamarin 的 tvOS 支持具有以下已知的问题：

- **Mono 框架**– Mono 4.3 Cryptography.ProtectedData 无法解密从 Mono 4.2 的数据。 因此，NuGet 包将失败并出现错误还原`Data unprotection failed`如果受保护的 NuGet 源配置。
    - **解决方法**– Visual Studio 中用于将需要重新添加任何 NuGet 包源重新尝试还原程序包之前，使用密码身份验证的 Mac。
- **适用于 Mac 带 F # 外接程序的 visual Studio** – 错误在 Windows 上创建的 F # Android 模板时。 这应仍正常 mac 上
- **Xamarin.Mac** – 时运行的目标 Framework Xamarin.Mac 统一的模板项目设置为`Unsupported`，弹出项`Could not connect to the debugger`可能会出现。
    - **可能的解决方法**– 降级我们稳定的通道中可用的 Mono 框架版本。
- **Xamarin Visual Studio 和 Xamarin.iOS** – 如果部署 WatchKit 应用程序在 Visual studio 中，错误`The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist`可能会出现。

请报表任何 bug 你找到指向[Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

## <a name="troubleshooting"></a>疑难解答

以下部分列出使用 tvOS 9 Xamarin.tvOS 与这些问题的解决方案时可能发生的一些已知的问题：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>无效的可执行文件的可执行文件不包含 bitcode

当尝试 Xamarin.tvOS 将应用提交到 Apple 电视应用商店，你可能会收到一条错误消息的形式_"无效的可执行文件的可执行文件不包含 bitcode"_。

若要解决此问题，请执行以下操作：

1. 在 Visual Studio for Mac 中，右键单击你 Xamarin.tvOS 项目文件中**解决方案资源管理器**和选择**选项**。
2. 选择**tvOS 生成**并确保你位于**版本**配置： 

    [![](troubleshooting-images/ts01.png "选择 tvOS 生成选项")](troubleshooting-images/ts01.png#lightbox)
3. 添加`--bitcode=asmonly`到**其他 mtouch 参数**字段，然后单击**确定**按钮。
4. 重新生成你的应用程序**版本**配置。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>验证你 tvOS 应用包含 Bitcode

若要验证你 Xamarin.tvOS 应用的生成包含 Bitcode，打开终端应用并输入以下命令：

```csharp
otool -l /path/to/your/tv.app/tv
```

在输出中，查找以下：

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` 和`size`将会不同，但应该是相同的其他字段。

你将需要确保静态任何第三方 (`.a`) 正在使用的库生成对 tvOS 库 （而不是 iOS 库） 和它们还包括 bitcode 信息。

应用程序或库，包括有效 bitcode 程序`size`将大于 1。 在某些情况下，库可以具有 bitcode 标记中，但不包含有效 bitcode 其中。 例如:

**无效的 Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**有效 Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

请注意中的差异`size`之间在列出的示例中的两个库上的运行。 库必须 Xcode 存档生成中生成与 bitcode 启用 (Xcode 设置`ENABLE_BITCODE`) 作为此大小问题的解决方案。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>仅包含 arm64 切片应用还必须在 Info.plist 中 UIRequiredDeviceCapabilities 列表中具有"arm64"

提交时发布 Apple 电视应用商店应用程序，可能会在窗体中出现错误：

_"仅包含 arm64 切片应用还必须具有"arm64"在列表中在 Info.plist 中 UIRequiredDeviceCapabilities"_

如果发生这种情况，编辑你`Info.plist`文件，并确保它具有以下项：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

重新编译的应用程序版本并将重新提交至 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>任务"MTouch"执行-失败

如果你使用的 （如 MonoGame) 是第三方库和版本编译失败，出现错误消息以一长串`Task "MTouch" execution -- FAILED`，尝试添加`-gcc_flags="-framework OpenAL"`到你**其他触摸参数**:

[![](troubleshooting-images/mtouch01.png "任务 MTouch 执行")](troubleshooting-images/mtouch01.png#lightbox)

此外应包括`--bitcode=asmonly`中**其他触摸参数**，链接器选项设置为**链接所有**并执行干净的编译。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS 90471 错误。 大图标是缺少

如果你收到一条消息的形式"ITMS 90471 错误。 大图标是缺少"时尝试 Xamarin.tvOS 将应用提交到 Apple 电视应用商店，对于版本，请检查下列项目：

1. 确保你已包含在大图标资产你`Assets.car`使用创建的文件[应用图标](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons)文档。
2. 确保你包含`Assets.car`文件从[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)最终应用程序捆绑中的文档。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>无效的捆绑 – 支持游戏控制器的应用还必须支持 Apple TV 远程

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>无效的捆绑包 – Apple TV 的 GameController 框架的应用程序必须在应用的 info.plist 文件中包括 GCSupportedGameControllers 密钥

游戏控制器可以用于提高玩游戏并提供了在游戏中的高质量的意义。 它们还可以用于控制标准 Apple TV 接口，以便用户不具有远程和控制器之间进行切换。

如果你正在提交到 Apple 电视应用商店游戏控制器支持的 Xamarin.tvOS 应用并形式收到一条错误消息：


_我们发现了一个或多个针对"应用程序名称"你最近的传送问题。你的传送成功，但你可能想要更正你下一步传送中的以下问题：_

_无效的捆绑 – 支持游戏控制器的应用还必须支持 Apple TV 远程。_

或 

_无效捆绑 – GameController framework 使用的 Apple TV 应用必须在应用的 info.plist 文件中包含 GCSupportedGameControllers 密钥。_

解决方法是添加对使用 Siri 远程的支持 (`GCMicroGamepad`) 到应用程序的`Info.plist`文件。 已由 Apple 面向 Siri 远程添加 Micro 游戏控制器配置文件。 例如，包括以下项：

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> **注意：**蓝牙游戏控制器最终用户可能会使可选购买，您的应用程序不能强制用户在购买一个。 如果你的应用程序支持游戏控制器，它还必须支持 Siri 远程，以便游戏已由 Apple TV 的所有用户使用。

有关详细信息，请参阅我们[使用游戏控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)一部分我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>不兼容的目标框架:。NetPortable，Version = v4.5，配置文件 = Profile78

尝试将可移植类库 (PCL) 包含在 Xamarin.tvOS 项目时可能会出现一条消息以形成：

_不兼容的目标框架:。NetPortable，Version = v4.5，配置文件 = Profile78_

若要解决此问题，将添加一个 XML 文件称为` Xamarin.TVOS.xml`包含以下内容：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

到以下路径：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

请注意，在路径中的配置文件数必须匹配的 PCL 配置文件数。

与此文件之后，你应能够成功将 PCL 文件添加到 Xamarin.tvOS 项目。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
