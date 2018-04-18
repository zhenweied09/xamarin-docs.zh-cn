---
title: 使用 Xamarin 团队城市
description: 本指南将讨论使用 TeamCity 编译移动应用程序，然后将它们提交到 Xamarin 测试云所涉及的步骤。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 34702fafdd0d767362b0ca32ab56e880ed7cb366
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="using-team-city-with-xamarin"></a>使用 Xamarin 团队城市

_本指南将讨论使用 TeamCity 编译移动应用程序，然后将它们提交到 Xamarin 测试云所涉及的步骤。_

中所述[连续集成简介](~/tools/ci/intro-to-ci.md)指南中，当开发质量移动应用程序时持续集成 (CI) 很有用的做法。 有许多可行选项的持续集成服务器软件;本指南将重点[TeamCity](http://www.jetbrains.com/teamcity/)从 JetBrains。

有几个不同的 TeamCity 安装排列。 下面是其中的某些类的列表：

- **Windows 服务**– 在此方案中，TeamCity Windows 作为 Windows 服务的启动时启动。 它必须与 Mac 生成主机用于编译任何 iOS 应用程序配对。

- **启动在 OS X 上的后台程序**– 从概念上讲，这是非常类似于作为 Windows 服务中上一步所述运行。 默认情况下将在根帐户下运行生成。

- **在 OS X 上的用户帐户**– 可以在启动每次用户登录的用户帐户下运行 TeamCity。

前面的方案中，在 OS X 上用户帐户下运行 TeamCity 是最简单和容易进行安装。

有几个步骤涉及设置 TeamCity:

- **安装 TeamCity** – 本指南中未涉及的 TeamCity 安装。 本指南假定 TeamCity 已安装并在用户帐户下运行。 说明[安装 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation)可以位于[TeamCity 8 文档](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)JetBrains 通过。

- **准备生成服务器**– 此步骤涉及安装必要的软件工具，和所需的证书生成移动应用程序并准备用于分发。

- **创建生成脚本**– 此步骤不是绝对必需的但生成脚本是构建应用程序无人参与了有用的指导。 使用生成脚本将可能出现，并提供一种一致、 可重复的方法，若要创建的二进制文件的分发，即使不练习持续集成生成问题进行疑难解答帮助。

- **创建 TeamCity 项目**– 前面三个步骤完成后，我们必须创建 TeamCity 项目将包含的所有元数据检索的源代码、 编译项目，并提交到 Xamarin 测试云测试需要。

## <a name="requirements"></a>要求

体验[Xamarin 测试云](https://developer.xamarin.com/guides/testcloud)是必需的。

熟悉 TeamCity 8.1 是必需的。 TeamCity 安装不在本文档的范围。 假定 TeamCity OS X Mavericks 上安装，并且在常规用户帐户而不是根帐户下运行。

生成服务器应运行 OS X，专用于持续集成的独立计算机。 理想情况下，生成服务器不将负责任何其他角色，例如数据库服务器、 web 服务器或开发人员工作站。

> [!IMPORTANT]
> 本指南不涉及 Xamarin 的"无外设"安装。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>准备生成服务器

配置生成服务器中的一个重要步骤是安装所有必要的工具、 软件和证书生成移动应用程序。 很重要，生成服务器必须能够编译移动解决方案并运行任何测试。 为了尽量减少配置问题，软件和工具应安装在承载 TeamCity 的相同用户帐户。 下面是什么是必需的列表：

1. **Visual Studio for Mac** – 这包括 Xamarin.iOS 和 Xamarin.Android。
2. **登录到 Xamarin 组件应用商店**– 这是一个可选步骤，只是如果你的应用程序使用从 Xamarin 组件应用商店的组件，则需要。 TeamCity 生成尝试编译该应用程序时，主动此时登录到组件应用商店将防止出现任何问题。
3. **Xcode** – Xcode 是需要编译和登录 iOS 应用程序。
4. **Xcode 命令行工具**– 这在步骤 1 的安装部分中所述[更新使用 rbenv 的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南。
5. **签名标识和预配配置文件**– 导入证书和预配配置文件通过 XCode。 请参阅 Apple 的指南[导出的签名标识和预配配置文件](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)有关详细信息。
6. **Android 密钥库**– 复制到 TeamCity 用户即有权访问，目录中的所需的 Android 密钥库`~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** – 这是一个可选步骤，如果你的应用程序具有使用 Calabash 编写的测试。 请参阅[OS X Mavericks 上安装 Calabash](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/)指南和[更新使用 rbenv 的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南以获取详细信息。

下图说明了所有这些组件：

![](teamcity-images/image1.png "此图显示了所有这些组件")

安装的所有软件之后, 登录的用户帐户，并确认所有软件具有已正确安装且正常工作。 这涉及方应该包括编译解决方案中，将提交到测试云应用程序。 这可以极大地简化通过运行生成脚本中下, 一节中所述。

## <a name="create-a-build-script"></a>创建生成脚本

尽管它是完全有可能的 TeamCity 处理方面的编译和提交到测试云移动应用程序本身的所有工作，但强烈建议创建的生成脚本。 生成脚本提供以下优点：

1. **文档**– 生成脚本用作一种形式的有关如何生成软件的文档。 这消除了一些"妙处"，与部署应用程序相关联，并允许开发人员能够集中精力功能。
1. **可重复性**– 生成脚本可确保每次编译应用程序并将其部署，它发生的方式完全相同，而不考虑用户或所执行的操作的工作。 任何问题或可能会发生一些由于不正确地执行生成的错误或人为错误中删除此可重复的一致性。
1. **版本控制**– 生成脚本可以包含在源代码管理系统。 这意味着，可以跟踪、 监视，并更正如果找不到错误或不准确的生成脚本更改。
1. **准备环境**– 生成脚本可以包含用于安装任何所需的第三方依赖项的逻辑。 这将确保应用程序都用了正确的组件生成。

生成脚本可以作为 Powershell 文件 （在 Windows 上) 或 bash 脚本 （在 OS X) 一样简单。 在创建生成脚本时，有了几种脚本语言选择：

- [**Rake** ](https://github.com/jimweirich/rake) – 这是用于生成项目，基于 Ruby 的域特定语言 (DSL)。 Rake 具有受欢迎程度的优点和库的丰富生态系统。

- [**psake** ](https://github.com/psake/psake) – 这是用于构建软件的 Windows Powershell 库

- [**但是，伪造**](http://fsharp.github.io/FAKE/) – 这是基于在 F # 这样就可以利用现有的.NET 库，如有必要 DSL。

所使用的脚本语言取决于你的首选项和要求。 [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash)示例包含的使用 Rake 作为示例[生成脚本](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile)。

> [!NOTE]
> 可使用基于 XML 生成系统如 MSBuild 或 NAnt，但这些缺少的表现力和专用于构建软件 DSL 的可维护性。

### <a name="parameterizing-the-build-script"></a>生成脚本中参数化

构建和测试软件的过程需要应保密的信息。 尤其是创建 APK 可能要求提供密码来 keystore 和/或密钥库中密钥的别名。 同样，测试云需要是唯一的开发人员的 API 密钥。 这些类型的值不应为硬编码在生成脚本中。 改为它们应作为变量传递到生成脚本。

敏感程度较低不等 iOS 设备 ID 或标识的设备测试云应该用于测试的 Android 设备 ID 运行的值。 这些不是需要受保护的值，但他们可以更改内部版本。

将存储这些类型的变量在生成脚本之外还可以更轻松地与开发人员例如共享组织内的生成脚本。 开发人员可以使用完全相同的脚本的生成服务器，但可以使用他们自己的密钥库和 API 密钥。

有两个可能的选项，用于存储这些敏感值：

- **配置文件**– 若要保护此值不应签入版本控制的测试云 API 密钥。 可以为每台计算机创建文件。 从该文件的读取值的方式取决于使用的脚本语言。

- **环境变量**– 这些可以轻松地设置上的每台计算机进行和 ared 独立于基础的脚本语言。

有一些优点和缺点的每一个这些选择。 TeamCity 都可以很好地与环境变量，因此创建生成脚本时，本指南将建议此技术。

### <a name="build-steps"></a>生成步骤

生成脚本必须能够执行以下步骤：

- **编译该应用程序**– 这包括签名具有正确的预配配置文件的应用程序。

- **将应用提交到 Xamarin 测试云**– 这包括签名和 zip 对齐使用相应密钥库 APK。

将下面更详细地说明这些两个步骤。

#### <a name="compiling-a-xamarinios-application"></a>编译 Xamarin.iOS 应用程序

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>编译 Xamarin.Android 应用程序

若要编译的 Android 应用程序，使用**xbuild** (或**msbuild** Windows 上):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

请注意，若要编译 Xamarin Android 应用**xbuild**使用项目中，并且生成 iOS 应用程序**xbuild**要求解决方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>提交到测试云 Xamarin.UITests

使用提交 UITests`test-cloud.exe`应用程序，如下面的代码段中所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

当测试运行时，将调用 NUnit 样式 XML 文件的形式返回测试结果**报告.xml**。 TeamCity 将生成日志中显示的信息。

有关如何将提交到测试云 UITests 的详细信息，请查阅 》 上 Xamarin 的指南[提交测试配置为测试云](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>提交 Calabash 测试来测试云

使用提交 Calabash 测试`test-cloud`gem，如下面的代码段中所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
若要提交到测试云 Android 应用程序，它不需要首先重新构建使用 calabash android APK 测试服务器：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
有关提交 Calabash 测试的详细信息，请参阅 Xamarin 的指南在[提交测试云 Calabash 测试](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)。

## <a name="creating-a-teamcity-project"></a>创建 TeamCity 项目

一旦安装 TeamCity 和适用于 Mac 的 Visual Studio 可以生成项目时，是时候在 TeamCity 以生成项目并将其提交到测试云中创建项目。

1. 通过登录到 TeamCity 通过 web 浏览器启动。 导航到的根项目：

    ![导航到根项目](teamcity-images/image2.png "导航至根项目")下方根项目中，创建一个新的子项目：

    ![导航到的根项目在此之下根项目，创建一个新的子项目](teamcity-images/image3.png "导航到根项目在此之下根项目，创建一个新的子项目")
2. 一旦创建子项目后，添加新的生成配置：

    ![一旦创建子项目后，添加新的生成配置](teamcity-images/image5.png "一旦创建子项目后，添加新的生成配置")
3. 将 VCS 项目附加到生成配置。 这是通过版本控制设置屏幕：

    ![这是通过版本控制设置屏幕](teamcity-images/image6.png "这是通过版本控制设置屏幕")

    如果不没有创建任何 VCS 项目，你可以选择创建一个从新 VCS 根页所示：

    ![如果不没有创建任何 VCS 项目，你可以选择创建一个新的 VCS 根页从](teamcity-images/image7.png "如果不没有创建任何 VCS 项目，你可以选择创建一个从新 VCS 根页")

    一旦附加 VCS 根，TeamCity 将签出该项目，然后重试自动检测生成步骤。 如果你熟悉 TeamCity，你可以选择将检测到的生成步骤之一。 则可以安全地忽略现在将检测到的生成步骤。

4. 接下来，配置生成触发器。 这将生成进行排队时满足特定条件，例如当用户提交到存储库代码。 以下屏幕截图显示如何添加生成触发器：

    ![此屏幕截图显示如何添加生成触发器](teamcity-images/image8.png "此屏幕截图显示如何添加生成触发器")配置生成触发器的示例所示的以下屏幕截图：

    ![配置生成触发器的示例可在此屏幕截图中查看](teamcity-images/image9.png "配置生成触发器的示例所示此屏幕截图")

5. 上一节，参数化生成脚本中，建议将某些值存储为环境变量。 可以将这些变量添加到通过参数屏幕的生成配置。 将变量添加测试云 API 密钥、 iOS 设备 ID，和 Android 的设备 ID，如下面的屏幕截图中所示：

    ![将变量添加测试云 API 密钥、 iOS 设备 ID，和 Android 的设备 ID](teamcity-images/image11.png "将变量添加测试云 API 密钥、 iOS 设备 ID，和 Android 的设备 ID")

6. 最后一步是添加将调用用于编译的应用程序和队列时，测试云应用程序的生成脚本的生成步骤。 下面的屏幕截图演示了使用 Rakefile 来生成应用程序的生成步骤：

    ![此屏幕截图演示了使用 Rakefile 来生成应用程序的生成步骤](teamcity-images/image12.png "此屏幕截图演示了使用 Rakefile 来生成应用程序的生成步骤")

7. 此时，已完成的生成配置。 它是一个好办法触发要确认已正确配置项目的生成。 执行此操作一种好方法是将小型、 无关紧要的更改提交到存储库。 TeamCity 应检测提交并开始执行生成。

8. 完成生成后，检查生成日志，并查看是否存在任何问题或需要关注与生成的警告。

## <a name="summary"></a>总结

本指南介绍如何使用 TeamCity 构建 Xamarin 移动应用程序，然后将它们提交到测试云。 我们讨论了创建自动执行生成过程生成脚本。 生成脚本负责编译应用程序，将提交到测试云，并等待返回的结果

然后我们将介绍如何在 TeamCity，将对生成进行排队每个开发人员提交代码的时间，将调用生成脚本中创建一个项目。

## <a name="related-links"></a>相关链接

- [提交到 Xamarin 测试云 (UITest) 的测试](https://developer.xamarin.com~/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)
- [提交到 Xamarin 测试云 (Calabash) 的测试](https://developer.xamarin.com~/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)
- [安装和配置 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
