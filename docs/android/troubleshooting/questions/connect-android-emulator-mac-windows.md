---
title: "它可能连接到 Android 仿真程序在 Mac 上从运行 Windows VM？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2f0ef027d8a2d40ccf85e35d5a85eba4cd7c7ccc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>它可能连接到 Android 仿真程序在 Mac 上从运行 Windows VM？

若要连接到 Windows 虚拟机从运行在 Mac 上的 Google Android 仿真程序，请使用以下步骤：

1.  在 mac 上启动仿真程序

2.  终止`adb`在 Mac 上的服务器：

    ```bash
    adb kill-server
    ```

3.  请注意仿真程序正在侦听环回网络接口上的 2 TCP 端口：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇数端口是用于连接到`adb`。 另请参阅[http://developer.android.com/tools/devices/emulator.html#emulatornetworking](http://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4.  _选项 1_： 使用[ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)转发入站的 TCP 数据包外部端口上接收 5555 （或你喜欢的任何其他端口） 上的环回接口奇数端口 (**127.0.0.1 5555**在此示例中)，将转发出站数据包重新另一种方法：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0 < backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要`nc`命令运行保持在终端窗口中，将转发数据包，按预期方式。 你可以在退出终端窗口中键入 CONTROL-C`nc`命令完成后，使用仿真程序。

    (选项 1 是通常比选项 2，尤其是如果**系统首选项 > 安全和隐私 > 防火墙**已打开。) 

    _选项 2_： 使用[ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)重定向来自端口的 TCP 数据包`5555`（或你喜欢的任何其他端口） 上[共享网络](http://kb.parallels.com/en/4948)到奇数端口接口环回接口 (`127.0.0.1:5555`在此示例中):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令将设置端口转发使用`pf packet filter`系统服务。 分行符很重要。 请务必使它们保持不变时复制粘贴。 你还需要调整接口名称从*vmnet8*如果你使用 Parallels。 `vmnet8` 是特殊名称*NAT 设备*为*共享网络*VMWare 合成中的模式。 在 Parallels 中适当的网络接口很可能[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5.  从 Windows 计算机连接到仿真程序：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    将"ip 地址的--mac"使用 IP 地址的 Mac 上，例如作为通过列出`ifconfig vmnet8 | grep 'inet '`。 如果需要替换`5555`与要在步骤 4 中的其他端口\. (注意： 一种方法来获取命令行访问权`adb`是通过[**工具 > Android > Android Adb 命令提示符**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) Visual Studio 中。)

### <a name="alternate-technique-using-ssh"></a>备用技术使用 `ssh`

如果已启用_远程登录_在 Mac 上，则可以使用`ssh`端口转发，以连接到模拟器。

1.  在 Windows 上安装 SSH 客户端。 一种选择是安装[Git for Windows](https://git-for-windows.github.io/)。 `ssh`命令将会在可用**Git Bash**命令提示符。

2.  按照步骤 1-3 上述要启动仿真程序，则终止`adb`服务器在 Mac 上，并确定的仿真程序端口。

3.  运行`ssh`设置 Windows 上的本地端口之间的双向端口转发的 Windows 上 (`localhost:15555`在此示例中) 和 Mac 的环回接口上的奇数的仿真程序端口 (`127.0.0.1:5555`在此示例中):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    替换`mac-username`与你的 Mac 用户名列出由`whoami`。 替换`ip-address-of-the-mac`与 mac 的 IP 地址

4.  连接到仿真程序在 Windows 上使用的本地端口：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意： 一种简单获取命令行访问权`adb`是通过[**工具 > Android > Android Adb 命令提示符**Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。)

小警告： 如果使用端口`5555`作为本地端口，`adb`会认为在 Windows 上本地运行仿真程序。 这不会遇到问题导致在 Visual Studio 中，但适用于 Mac 的 Visual Studio 中它将导致应用程序启动后立即退出。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>另一种方法使用`adb -H`尚不支持

从理论上讲，另一种方法是使用`adb`的内置功能，以连接到`adb`在远程计算机上运行的服务器 (请参阅例如[http://stackoverflow.com/a/18551325](http://stackoverflow.com/a/18551325))。
但 Xamarin.Android IDE 扩展当前不提供某种方法来配置该选项。

## <a name="contact-information"></a>联系信息

本文档讨论自 2016 年 3 月日起的当前行为。 本文档中所述的技术不为 Xamarin，稳定的测试套件的一部分，因此它可能会在将来中断。

如果你注意到，则方法将不再起作用，或者如果你注意到文档中的任何其他错误，请尝试将添加到以下论坛线程上的讨论： [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
谢谢!

