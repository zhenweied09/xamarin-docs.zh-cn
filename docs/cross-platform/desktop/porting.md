---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面应用程序迁移指南
description: 如何将现有的 Windows 窗体或 WPF 应用中，若要创建跨平台应用，以在 macOS、 iOS、 Android，以及 UWP/Windows 10 上运行的简单说明。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: cdf70065893a4da268f628369fa94336291ead1f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="desktop-app-porting-guidance"></a>桌面应用程序迁移指南

大多数应用程序代码可以分为以下几个方面之一：

* 用户界面代码 （如。 windows 和按钮）
* 第三方控件 （如。 图）
* 业务逻辑 （例如。 验证规则）
* 本地数据存储和访问
* Web 服务和远程数据访问

Windows 窗体和 WPF 应用程序使用 C# （或 Visual Basic.NET） 编写少量令人惊讶的业务逻辑，本地数据访问，并可以在平台之间共享 web 服务代码。

## <a name="net-portability-analyzer"></a>.NET 可移植性分析器

Visual Studio 2015 和 2017年支持[.NET 可移植性分析器](https://docs.microsoft.com/en-us/dotnet/articles/standard/portability-analyzer)([下载适用于 Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) 可以检查现有应用程序并将其告知你多少代码都可以移植"按原样"到其他平台. 你可以了解有关它从此[第 9 频道视频](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)。

此外还有一个命令行工具可以从下载[GitHub 上的可移植性分析器](https://github.com/Microsoft/dotnet-apiport)，用于提供相同的报表。

## <a name="x-of-my-code-is-portable-what-next"></a>"x %我的代码的可移植。 接下来怎么？"

希望分析器显示大型部分代码可移植，但存在肯定要进行每个应用的某些部分，_无法_移动到其他平台。

不同的数据块的代码可能将划分为这些存储桶，更详细地中所述之一：

* 重新可用的可移植代码
* 需要更改的代码
* 不是可移植并需要重新编写的代码

### <a name="re-useable-portable-code"></a>重新可用的可移植代码

在所有平台对可用的 Api 编写的.NET 代码时可以执行跨平台保持不变。 理想情况下，你将能够将所有此代码移到可移植类库、 共享库或.NET 标准库，然后在现有应用程序。

然后可以针对其他平台 （例如 Android、 iOS、 macOS） 的应用程序项目添加该共享的库。

### <a name="code-that-requires-changes"></a>需要更改的代码

一些.NET Api 可能不能在所有平台上。 如果你的代码中存在这些 Api，你将需要重新编写这些部分来使用跨平台的 Api。

这样的示例包括使用反射 api，可用于.NET 4.6，但上不可在所有平台之间。

已重新编写使用可移植的 Api 的代码后，你应能够包共享库中的代码并在现有应用程序对其进行测试。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>不是可移植并需要重新编写的代码

不可能是跨平台的代码的示例包括：

- **用户界面**– Windows 窗体或 WPF 屏幕不例如 Android 或 iOS 上的项目中使用。 你的用户界面将需要重写，使用此[控件比较](~/cross-platform/desktop/controls/index.md)作为引用。

- **特定于平台的存储**-依赖于特定于平台的技术 （如本地 SQL Server Express 数据库） 的代码。 你将需要重新编写这使用跨平台的替代项 （如数据库引擎的 SQLite)。
某些文件系统操作可能还需要进行调整，由于 UWP 具有到 Android 和 iOS （如略有不同的 Api 某些文件系统是区分大小写和其他不）。

- **第三方组件**– 检查是否在应用程序中的第三方组件在其他平台上可用。 一些，如非视觉 NuGet 程序包可能可用但他人 （尤其是可视控件如图表或媒体播放器）

## <a name="tips-for-making-code-portable"></a>为使代码更可移植的提示

- **依赖关系注入**– 提供有关每个平台的不同实现和

- **分层方法**– 是否 MVVM、 MVC、 MVP 或某些其他模式，可帮助你将可移植代码与特定于平台的代码分开。

- **消息传送**– 你可以在代码中使用消息传递，以取消将应用程序的不同部分之间的交互。
