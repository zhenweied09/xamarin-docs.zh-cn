---
title: 更新组件引用到 NuGet
description: 将组件引用替换为 NuGet 程序包添加到将来证明你的应用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 020a5a2182458e759626b9bdbf45b62b6e13d71a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="updating-component-references-to-nuget"></a>更新组件引用到 NuGet

> [!NOTE]
> Xamarin 组件在 Visual Studio 中，不再受支持，并应替换为 NuGet 包。 按照下面的说明手动从项目中删除组件的引用。

这些说明中的添加 NuGet 包是指[Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package)或[Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

## <a name="manually-removing-component-references"></a>手动删除的组件引用

自 2017 年 11 月，它是在[宣布](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/)将停止使用 Xamarin 组件应用商店。 为了向前移动进行的组件 sunsetting，15.6 版本的 Visual Studio 和适用于 Mac 的 7.4 版本的 Visual Studio 不再支持你的项目中的组件。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果将项目加载到 Visual Studio 时，将显示以下对话框，以解释，你必须任何组件从你的项目中手动删除：

![警报对话框说明组件已找到你的项目中，并且必须删除](component-nuget-images/component-alert-vs.png)

若要删除你的项目的组件：

1. 打开 **.csproj**文件。 为此，右键单击项目名称，然后选择**卸载项目**。 

2. 在已卸载的项目上再次右键单击并选择**编辑 {你项目名称}.csproj**。

3. 在文件中找到的任何引用`XamarinComponentReference`。 其外观应类似于下面的示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. 删除对引用`XamarinComponentReference`并保存该文件。 在上面的示例中，则可以安全地删除整个`ItemGroup`。

5. 已保存该文件，右键单击项目名称，然后选择**重新加载项目**。

6. 你的解决方案中每个项目重复上述步骤。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果你将项目加载到 Visual Studio for Mac，将显示以下对话框，以解释，你必须任何组件从你的项目中手动删除：

![警报对话框说明组件已找到你的项目中，并且必须删除](component-nuget-images/component-alert.png)

若要删除你的项目的组件：

1. 打开.csproj 文件。 为此，右键单击项目名称，然后选择**工具 > 编辑文件**。

2. 在文件中找到的任何引用`XamarinComponentReference`。 其外观应类似于下面的示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. 删除对引用`XamarinComponentReference`并保存该文件。 在上面的示例中，则可以安全地删除整个 `ItemGroup`

4. 你的解决方案中每个项目重复上述步骤。

-----

> [!WARNING]
> 以下说明只适用于 Visual Studio 早期版本。
> **组件**节点不再可用在当前版本的 Visual Studio 2017 或 Visual Studio for mac。

以下各节说明如何更新现有的 Xamarin 解决方案，若要更改组件引用到 NuGet 程序包。

- [包含 NuGet 包的组件](#contain)
- [了 NuGet 替换的组件](#replace)

大多数组件都属于上述类别之一。
如果你使用的组件未出现具有等效的 NuGet 包，请阅读[不含 NuGet 迁移路径的组件](#require-update)下面一节。

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 包的组件

许多组件已包含 NuGet 包，并且迁移路径只是删除组件的引用。

您可以确定该组件是否通过双击解决方案中的组件上的 NuGet 包已包括：

![展开的组件节点](component-nuget-images/solution-sml.png)

**包**选项卡将列出组件中包括的任何 NuGet 包：

![包选项卡包含 NuGet](component-nuget-images/packages-tab-sml.png)

请注意，**程序集**选项卡将为空：

![程序集选项卡上为空](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>更新解决方案

若要更新你的解决方案，删除**组件**从解决方案的条目：

![删除组件](component-nuget-images/delete-component-sml.png)

NuGet 包将保留在中列出**包**节点和应用程序将编译并按常规方式运行。 在将来，将通过执行对此包更新**Nuget**更新功能：

![更新 NuGet 包](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>了 NuGet 替换的组件

如果组件信息页**程序集**选项卡包含项，如下所示，你将需要手动查找等效的 NuGet 包。

![包含程序集](component-nuget-images/assemblies-tab-sml.png)

请注意，**包**选项卡将可能为空：

![](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 依赖关系，但是可以忽略这些警告。_

若要确认更换 NuGet 程序包是否存在，请搜索[NuGet.org](https://www.nuget.org/packages)，使用该组件名称，或者由作者。

例如，你可以找到流行**sqlite net pcl**通过搜索包：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – 产品名称。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – 作者配置文件。

### <a name="updating-the-solution"></a>更新解决方案

一旦确认该组件位于 NuGet，请按照下列步骤：

#### <a name="delete-the-component"></a>删除组件

右键单击该解决方案中的组件，然后选择**删除**:

![删除组件](component-nuget-images/remove-component-sml.png)

这将删除的组件，并且任何引用。 这将中断生成，直到添加等效的 NuGet 包，以将其替换。

#### <a name="add-the-nuget-package"></a>添加 NuGet 包

1. 右键单击**包**节点，然后选择**添加包...**.
2. 搜索按名称或作者 NuGet 替换：

  ![](component-nuget-images/nuget-search-sml.png)

3. 按**添加包**。

NuGet 程序包将添加到你的项目，以及任何依赖关系。
这样应该会修复生成。 如果生成仍然失败，请调查每个错误，以查看是否存在组件和 NuGet 包之间的 API 差异。

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>不含 NuGet 迁移路径的组件

请不要担心，如果您没有立即找到你的应用程序中使用的组件的替换。 现有组件将继续在 Visual Studio 15.5，和**组件**节点将出现在你的解决方案像往常一样。

将来的 Visual Studio 版本，但是，将_不_还原或更新组件。
这意味着如果你打开新的计算机上的解决方案，该组件将下载并安装;和作者将不能为您提供更新。 你应计划：

* 从组件中提取程序集，并直接在你的项目中引用它们。
* 请与组件作者联系并询问有关计划将迁移到 NuGet。
* 调查备用 NuGet 包，或如果组件为开放源代码查找的源代码。

迁移到 NuGet，还在处理许多组件供应商和其他人 （包括年年底上市产品） 可能调查另一种传递选项。


## <a name="related-links"></a>相关链接

- [安装和使用 NuGet 包 (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [包括 NuGet 包 (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
