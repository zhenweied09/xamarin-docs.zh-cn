---
title: 调试集成
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 1fb5de7fac831ab4c763fd3674cf79e98d688343
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="debugging-integrations"></a>调试集成

## <a name="debugging-agent-side-integrations"></a>调试代理端集成

调试代理端集成最好的做法是通过使用提供的日志记录方法`Log`类`Xamarin.Interactive.Logging`。 请参阅[ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/)要调用的方法。

在 macOS，日志消息显示在日志查看器菜单 (**窗口 > 日志查看器**) 和客户端日志中。 在 Windows 上，消息仅显示在客户端日志中，因为没有任何日志查看器存在。

客户端日志位于在 macOS 和 Windows 上的以下位置：

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

需要注意的一点是，当加载通过常用的集成`#r`在开发过程中的机制，集成程序集将会拾取作为_依赖_工作簿和绝对路径是否与它一起打包未使用。 这可能导致更改才会显示不传播，就像重新生成集成未执行任何操作。

## <a name="debugging-client-side-integrations"></a>调试客户端集成

为将用 JavaScript 编写的客户端集成并将其加载到我们的 web 浏览器图面 (请参阅[体系结构](~/tools/workbooks/sdk/architecture.md)文档)，对其进行调试的最佳方法是在 Mac 上使用 WebKit 开发人员工具或在 Windows 上使用 f12 键选择器.

这两个集的工具允许您查看 JavaScript/TypeScript 源、 设置断点、 查看控制台输出和检查和修改 DOM

### <a name="mac"></a>Mac

若要启用 Mac 上的 Xamarin 工作簿的开发人员工具，请在终端中运行以下命令：

```shell
defaults write com.xamarin.Inspector WebKitDeveloperExtras -bool true
```

然后重新启动 Xamarin 的工作簿。 一旦您这样做，你应看到**检查元素**出现在你右键单击上下文菜单中，和新**开发人员**窗格中将可在工作簿首选项中。 此选项可以选择你是否想在启动时打开的开发人员工具：

[![开发人员窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

对此首选项以及是只重新启动的-你将需要重新启动以使其生效新工作簿上的工作簿客户端。 激活通过上下文菜单或首选项的开发人员工具将显示熟悉的 Safari UI:

[![Safari 开发人员工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

有关使用 Safari 开发人员工具的信息，请参阅[WebKit 检查器文档][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows 上，IE 团队提供称为"f12 键选择器"工具，它是一个嵌入的 Internet 资源管理器实例的远程调试器。 你可以在以下位置找到工具：

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

运行 f12 键选择器，你应会看到为列表中的工作簿客户端面提供支持的嵌入的实例。 选择它，然后从 Internet 资源管理器的调试工具不会显示，熟悉 F12 附加到客户端：

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector