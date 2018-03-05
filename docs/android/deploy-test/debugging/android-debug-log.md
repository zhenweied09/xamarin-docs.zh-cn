---
title: "Android 调试日志"
ms.topic: article
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 99b9ed9e3c71766f483f7b00996137aae7a247d1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="android-debug-log"></a>Android 调试日志

## <a name="android-debug-log-overview"></a>Android 调试日志概述

开发人员用于调试应用程序的一个非常常见的技巧是调用 `Console.WriteLine`。 但是，在移动平台（如 Android）上没有控制台。 Android 设备会提供日志，可以在编写应用时使用。 这有时称为“logcat”，原因在于为检索它而输入的命令。

## <a name="accessing-from-visual-studio"></a>从 Visual Studio 访问

在 Visual Studio 2015 及更高版本中，统一了 Android 和 iOS 日志面板。

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 和 2017

Visual Studio 新的“设备日志”工具窗口可以显示 Android 和 iOS 设备的日志。 执行以下任一命令均可显示此窗口： 

-   “视图”>“其他窗口”>“设备日志”
-   “工具”>“Android”>“设备日志”
-   “Android 工具栏”>“设备日志”

显示工具窗口后，即可从设备组合框中选择物理设备。 选择设备后，该设备会自动启动，并从表中的运行应用添加日志条目。 在设备之间切换会停止和启动设备日志记录。若要在组合框中显示设备，必须加载 Android 项目。 如果设备未在组合框中显示，请先检查其在“启动调试”下拉列表中是否可用。 

此工具窗口提供：一个日志条目表、一个用于选择设备的组合框、一种清除日志条目的方法、一个搜索框，以及播放/停止/暂停按钮。 


<a name="Accessing_from_the_Command_Line" />

## <a name="accessing-from-the-command-line"></a>从命令行访问

查看调试日志的另一种方法是通过命令行。 打开控制台窗口，并导航到 Android SDK 平台工具文件夹（如 **C:\android-sdk-windows\platform-tools**）。 

如果仅连接了一个设备，则可通过以下方法查看日志：

```shell
$ adb logcat
```

如果连接了多个设备，则必须对设备进行标识。 例如，`adb -d logcat` 显示已连接的唯一物理设备的日志，而 `adb -e logcat` 显示运行中的唯一仿真器的日志。 

运行 **adb** 即可找到更多命令。

<a name="Writing_to_the_Debug_Log" />


## <a name="writing-to-the-debug-log"></a>写入调试日志

使用 [Android.Util.Log](https://developer.xamarin.com/api/type/Android.Util.Log/) 类上的方法可将消息写入调试日志： 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

这可生成：

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

<a name="Interesting_Messages" />

## <a name="interesting-messages"></a>兴趣消息

读取日志时，尤其是向其他人提供日志片段时（如果完整日志文件 (1) 多余，(2) 不可读），首先启动的最重要行类似于：

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

具体而言，此行与以下正则表达式匹配：

```shell
^I.*ActivityManager.*Starting: Intent
```

且包含应用程序包名称。 这是对应于活动开始的行，大多数（并非全部）以下消息均应与应用程序相关。 

尤其是，每条消息均包含生成该消息的进程的进程标识符 (pid)。 在上一 `ActivityManager` 消息中，进程 `12944` 生成了消息。 若要确定哪个进程是正在调试的应用程序进程，请查找 mono.MonoRuntimeProvider 消息： 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

此消息来源于已启动的进程，以下包含相同 pid 的所有消息均来源于该进程。 
