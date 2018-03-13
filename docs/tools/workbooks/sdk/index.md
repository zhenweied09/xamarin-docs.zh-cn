---
title: "开始使用 Xamarin 工作簿 SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 3978b046a8ab4d42cbf86bf524452a033b5dbb4d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>开始使用 Xamarin 工作簿 SDK

本文档提供的快速开发针对 Xamarin 工作簿的集成入门指南。 大部分此会使用稳定的 Xamarin 工作簿，但**在工作簿 1.3 中仅支持加载通过 NuGet 包的集成**，在编写时的 alpha 通道。

# <a name="general-overview"></a>常规概述

Xamarin 的工作簿集成是使用的小型库[ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK 以与 Xamarin 工作簿和检查器中集成代理以提供增强的体验。

有三个主要步骤开发集成入门-我们将在此处概述它们。

## <a name="creating-the-integration-project"></a>创建集成项目

最多平台的库作为开发集成库。 由于你想要在所有可用的代理、 过去和未来提供最佳的集成，你将需要选择一组广泛支持的库。 建议使用最广泛的支持"可移植类库"模板：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![适用于 Mac 的可移植运行库模板 Visual Studio](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![可移植运行库模板 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中，你将需要确保你为可移植库中选择以下的目标平台：

[![可移植运行库平台 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

一旦创建类库项目，添加对的引用我们`Xamarin.Workbooks.Integration`NuGet 库通过 NuGet 程序包管理器。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

你将需要删除为你作为项目的一部分创建的空类，你将需要它此。 完成这些步骤后，你已准备好开始构建你的集成。

## <a name="building-an-integration"></a>生成集成

我们要生成简单的集成。 我们实际上喜欢颜色绿色，因此我们会将绿色的颜色添加到每个对象的表示形式。 首先，创建新的类称为`SampleIntegration`，并使其实现我们[ `IAgentIntegration` ] [ integration-type]接口：

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我们希望做什么是添加[表示](~/tools/workbooks/sdk/representations.md)为每个对象，它是绿色的颜色。 我们将执行这可以通过使用表示提供程序。 提供程序从此继承[ `RepresentationProvider` ] [ reppr]类-对于我们的工具，我们只需重写[ `ProvideRepresentations` ] [ prrep]:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

我们要返回[ `Color` ] [ color]、 预生成表示我们 SDK 中的类型。
你会注意到，此处的返回类型是`IEnumerable<object>`&mdash;一个表示提供程序可能会返回多个表示形式之间实现对象 ！ 所有表示提供程序都称为对于每个对象，所以务必要不进行任何假设哪些对象传递给你。

最后一步是实际向代理注册我们提供程序，并告知在哪里可以找到我们集成类型的工作簿。 若要注册提供程序，此将代码添加到`IntegrateWith`中的方法`SampleIntegration`我们之前创建的类：

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

设置集成类型是通过程序集级特性。 在你 AssemblyInfo.cs、 或在同一类中作为集成类型为方便起见，你可以将此：

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在开发期间，你可能会发现它更方便地使用[`AddProvider`重载][ addprovider]上`RepresentationManager`方法允许你注册了一个简单的回调，以提供工作簿内的表示形式然后转到该代码你`RepresentationProvider`后的实现。 一个示例，用于呈现[ `OxyPlot` ] [ oxyplot] `PlotModel`可能如下所示：

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 这些 Api 为你提供的快速方法来启动和运行，但我们不建议仅使用它们传送整个集成&mdash;它们提供对你的类型由客户端的处理方式的很少控制。

注册的表示，与你的集成已准备好提供 ！

## <a name="shipping-your-integration"></a>传送你的集成

在发运你的集成，你将需要将其添加到 NuGet 包。
你可以将它发运与你现有的库 NuGet，或如果你要创建新包，你可以使用此模板.nuspec 文件作为起始点。
你将需要填写与你的集成相关的部分。 最重要的部分是所有的你的集成的文件必须位于`xamarin.interactive`根目录下的包目录。 这使我们能够轻松地找到你的集成，而不考虑是否使用现有包或创建一个新的相关的所有文件。

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

一旦你已创建.nuspec 文件，你可以包你 NuGet 如下所示：

```csharp
nuget pack MyIntegration.nuspec
```

然后发布它到[NuGet][nugetorg]。 后存在，你将能够从任何工作簿中引用它，并查看其操作。 在下面的屏幕截图中，我们已打包的示例集成我们在本文档中生成和工作簿中安装 NuGet 包：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![使用集成的工作簿](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![使用集成的工作簿](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

请注意你未看到任何`#r`指令或任何操作即可初始化集成-工作簿进行处理的所有这些为你在幕后 ！

## <a name="next-steps"></a>后续步骤

签出我们的移动部分的构成 SDK，有关详细信息的其他文档和我们[示例集成](~/tools/workbooks/samples/index.md)有关你可以从你的集成，如提供自定义在中运行的 JavaScript 执行操作的其他事项工作簿客户端。

[integration-type]: /api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: /api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: /api/type/Xamarin.Interactive.Representations.Color/
[xir]: /api/namespace/Xamarin.Interactive.Representations/
[reppr]: /api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: /api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: /api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
