---
title: 高级的集成主题
description: 本文档介绍与 Xamarin 工作簿集成相关的高级的主题。 它讨论 Xamarin.Workbook.Integrations NuGet 包和 Xamarin 工作簿中的 API 公开。
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 1aa6b5d0ca574345e1d349ea53df96f554c06bc4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793831"
---
# <a name="advanced-integration-topics"></a>高级的集成主题

集成程序集应引用[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 签出我们[快速入门文档](~/tools/workbooks/sdk/index.md)有关如何开始使用 NuGet 包的详细信息。

客户端集成也受支持，和由将与代理集成程序集同名的 JavaScript 或 CSS 文件放在相同的目录。 例如，如果名为代理集成程序集 （即在引用到 NuGet） `SampleExternalIntegration.dll`，然后`SampleExternalIntegration.js`和`SampleExternalIntegration.css`将集成到客户端以及中，如果它们存在。 客户端集成是可选的。

外部集成本身可以打包为 NuGet、 提供和直接在托管代理，或只需放置旁边的应用程序内引用`.workbook`想要使用它的文件。

当引用时包，按照快速入门文档中，当工作簿随附的集成程序集将需要为因此引用时，将自动加载 NuGet 包中的外部集成 （代理和客户端）：

```csharp
#r "SampleExternalIntegration.dll"
```

引用时集成这种方式，它将不加载客户端立即&mdash;你将需要从的集成，以将其加载调用某些代码。 我们将忙于处理此 bug 在将来。

`Xamarin.Interactive` PCL 提供几个重要集成 Api。 每个集成必须至少提供一个集成入口点：

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

此时，一旦引用集成程序集时，客户端将隐式加载 JavaScript 和 CSS 集成文件。

## <a name="apis"></a>API

与任何程序集所引用的工作簿或实时检查会话，因为任何其公共 Api 都可以访问该会话。 因此它非常重要具有安全和合理的 API 图面，为用户进行浏览。

集成程序集实际上是应用程序或感兴趣的 SDK 和会话之间的桥梁。 它可以提供有意义的工作簿或实时的上下文中专门检查会话，或提供任何公共 Api 以及只需执行"在后台"任务，例如生成对象的新 Api[表示形式之间实现](~/tools/workbooks/sdk/representations.md)。

> [!NOTE]
> 这必须是公共的但不是应通过 IntelliSense 显示 Api 可标记为常规`[EditorBrowsable (EditorBrowsableState.Never)]`属性。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
