---
title: 可以将文件添加到或删除从 IPA 文件后生成 Visual Studio 中的文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8b61ba38491b2085233dd1b30a82bc57d2baaed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>可以将文件添加到或删除从 IPA 文件后生成 Visual Studio 中的文件？

是，但它通常将需要重新签名`.app`捆绑后进行更改。

请注意该修改`.ipa`文件不需要在正常使用。 本文提供纯粹仅供参考。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>示例： 删除文件从`.ipa`存档

对于此示例假定 Xamarin.iOS 项目的名称`iPhoneApp1`和`generated session id`是 `cc530d20d6b19da63f6f1c6f67a0a254`

1.  生成`.ipa`文件作为普通从 Visual Studio。

2.  切换至 Mac 构建的主机。

3.  查找中的生成`~/Library/Caches/Xamarin/mtbs/builds`文件夹。 你可以粘贴到此路径**Finder > 转 > 转到文件夹**浏览在查找工具中的文件夹。 查找匹配项目名称的文件夹。 在该文件夹内查找匹配的文件夹`generated session id`生成。 这将很可能是具有最新的修改时间的子文件夹。

4.  打开一个新`Terminal.app`窗口。

5.  类型`cd `Terminal.app 窗口，然后拖放到`generated session id`文件夹导入到`Terminal.app`窗口：

    ![](modify-ipa-images/session-id-folder.png "在查找工具中查找生成的会话 id 文件夹")

6.  键入 return 键来更改目录到`generated session id`文件夹。

7.  解压缩`.ipa`到一个临时文件`old/`文件夹使用以下命令。 调整`Ad-Hoc`和`iPhoneApp1`根据需要为特定项目的名称。

    > ditto -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa old/

8.  保留`Terminal.app`窗口处于打开状态。

9.  删除所需的文件从`.ipa`。 你可以将它们移到垃圾桶使用查找工具，或删除在命令行使用`Terminal.app`。 若要查看的内容`Payload/iPhone`文件中查找工具中，单击该文件，然后选择**显示包内容**。

10.  使用相同的常规方法，如步骤 3，查找下的日志文件`~/Library/Logs/Xamarin/MonoTouchVS/`具有项目名称和`generated session id`名称中： ![ ](modify-ipa-images/build-log.png "在查找工具中找到项目生成日志")

11.  生成日志从步骤 10，例如双击以打开它。

12.  查找包含的行， `tool /usr/bin/codesign execution started with arguments: -v --force --sign`。

13.  类型`/usr/bin/codesign `到步骤 8 中 Terminal.app 窗口。

14.  将所有开头的自变量复制`-v`中的行从步骤 12 中，并将其粘贴到 Terminal.app 窗口。

15.  更改最后一个自变量为`.app`捆绑位于`old/Payload/`文件夹，并再次运行该命令。

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  将更改为`old/`目录在终端中：

```bash
cd old
```

17.  压缩到一个新目录的内容`.ipa`文件使用`zip`命令。 你可以更改`"$HOME/Desktop/iPhoneApp1-1.0.ipa"`自变量输出`.ipa`文件只要你想要：

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>常见的错误消息

如果你看到`Invalid Signature. A sealed resource is missing or invalid.`，这通常意味着内容已更改内`.app`捆绑包，并且`.app`捆绑包未正确重新签名之后。 另请注意，如果你想要创建`.ipa`与分发配置文件，你_必须_生成原始`.ipa`与分发配置文件。 否则为`Entitlements.xcent`会不正确。

若要运行以下命令，可授予的方式可能会出现此错误，具体的示例`codesign --verify`命令在终端窗口中在步骤 9 之后，你将看到错误的确切原因以及错误：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

和应用商店验证过程将报告类似的错误消息：

> 错误 ITMS-90035:"无效的签名。 密封的资源已丢失或无效。 在路径 [iPhoneApp1.app/iPhoneApp1] 二进制文件包含无效签名。 请确保您已经注册你的应用程序与分发证书、 不是临时证书或开发证书。 验证在目标级别 （它们将替代在项目级别的任何值） 在 Xcode 中的代码签名设置正确。 此外，请确保在 Xcode 中，不模拟器目标使用版本目标生成要上载的捆绑包。 如果不能确定你的代码签名设置正确无误，在 Xcode 中选择"清除所有"、 删除在查找工具中，"生成"目录和重新生成你的发布目标。 有关详细信息，请参阅[ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
