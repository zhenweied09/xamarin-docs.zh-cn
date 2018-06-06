---
title: 中 Xamarin.iOS 的.xib 代码生成
description: 本文档介绍如何 Xamarin.iOS 生成代码以将.xib 文件映射到 C# 中，使 visual 控件以编程方式访问。
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 064e17393747a36cd761cb2464e3239cfc17141c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786143"
---
# <a name="xib-code-generation-in-xamarinios"></a>中 Xamarin.iOS 的.xib 代码生成

> [!IMPORTANT]
>  本文档介绍 Visual Studio 的 Mac 的集成使用 Xcode 的接口生成器仅，如操作插座不使用和 Xamarin 设计器中为 iOS。 有关 iOS 设计器的详细信息，请查看[iOS 设计器](~/ios/user-interface/designer/index.md)文档。

Apple 接口生成器工具 ("IB") 可以用于以可视方式设计用户界面。 创建的 IB 接口定义保存在 **.xib**文件。 小组件和中的其他对象 **.xib**文件中可能会获得"类标识"，它可以是自定义的用户定义类型。 这允许您自定义小组件的行为，并编写自定义小组件。

这些用户类通常是 UI 控制器类的子类。 它们具有*outlet* （类似于属性） 和*操作*（类似于事件），可以连接到界面对象。 在运行时，加载 IB 文件后，在创建这些对象，和的插座和操作连接到各种 UI 对象动态。 在定义这些托管的类时，必须定义所有操作和插座与 IB 期望的名称一致。 适用于 Mac 的 visual Studio 使用类似的代码隐藏模型，若要简化此。 这是与 Xcode 于 OBJECTIVE-C 的操作类似，但代码生成模型和约定具有已调整为更熟悉的.NET 开发人员。

使用 **.xib**文件当前不支持在 Xamarin.iOS for Visual Studio。

## <a name="xib-files-and-custom-classes"></a>.xib 文件和自定义类

使用从 Cocoa Touch 的现有类型，以及它是可以定义中的自定义类型 **.xib**文件。 还有可能使用在其他定义的类型 **.xib**文件，或只是在 C# 代码中定义。 目前，接口生成器不是当前的外部定义的类型的详细信息的感知 **.xib**文件，因此它将不列出这些站点或显示其自定义容器和操作。 删除此限制已计划在将来某个时间。

可以在定义自定义类 **.xib**文件在接口生成器的"类"选项卡中使用"添加子类"命令。 我们将此称为"代码隐藏"类。 如果 **.xib**文件具有"。 xib.designer.cs"在项目中，然后 Visual Studio for Mac 的对应文件将自动填充的中的所有自定义类的分部类定义 **.xib**。 我们将这些部分类称为"设计器类"。

## <a name="generating-code"></a>生成代码

对于任何 **{0}.xib**生成操作的文件*页*，如果 **{0}。 xib.designer.cs**文件也存在在项目中，Visual Studio for Mac将可以在中找到的所有用户类设计器文件中生成分部类 **.xib**文件，使用容器的属性和分部方法以便的所有操作。 代码生成是只需通过存在此文件。

设计器的文件将自动更新时 **.xib**文件更改和 Visual Studio Mac 重新获得焦点。 设计器的文件在如的更改都将覆盖下一次 Visual Studio Mac 更新文件不应手动修改。

## <a name="registration-and-namespaces"></a>注册和命名空间

适用于 Mac 的 visual Studio 生成使用项目的默认命名空间为设计器文件的位置，以使其与普通的.NET 项目 namespacing 一致的设计器类。 设计器文件的命名空间根据项目的"默认命名空间"和".NET 命名策略"设置。 请注意，是否你的项目的默认命名空间发生更改，MD 将重新生成新的命名空间中的类以便您可能会发现，分部类不再匹配。

若要使类成为可发现由 Objective C 运行时，Visual Studio for Mac 应用`[Register (name)]`到类属性。 尽管 Xamarin.iOS 自动注册`NSObject`-派生类中，它使用的完全限定的.NET 名称。 有关 Mac 重写此选项可确保每个类由 Visual Studio 应用的属性注册中使用的名称与 **.xib**文件。 如果在 IB 中使用自定义类，而不使用 Visual Studio for Mac 来生成设计器的文件，你可能需要应用此手动以使你的预期的 OBJECTIVE-C 类名称匹配的托管的类。

无法在多个中定义类 **.xib**，或它们将发生冲突。

## <a name="non-designer-class-parts"></a>非设计器类部件

设计器的分部类并不能作为-是。 容器是专用的并指定没有基类。 预计每个类，将另一个文件，后者设置的基类、 使用或公开 outlet，并定义所需加载时实例化从本机代码的类的构造函数中有相应的"非设计器"类部件 **.xib**。 默认值 **.xib**模板执行此操作，但定义中的任何其他自定义类 **.xib**，您必须手动添加非设计器的一部分。

这样做的原因是为了地提高灵活性需求。 例如，多个代码隐藏类无法一组公共管理抽象类，哪些子类由 IB 要子类化的类的子类。

它是常规要置于这些 **{0}。 xib.cs**旁置文件 **{0}。 xib.designer.cs**设计器文件。

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>生成的操作和容器

中的部分的设计器类，适用于 Mac 的 Visual Studio 将生成到 IB 和对应于连接的任何操作的分部方法中定义的任何连接 outlet 相对应的属性。

### <a name="outlet-properties"></a>Outlet 属性

设计器类包含与自定义类上定义的所有容器相对应的属性。 这些是属性的事实是实现详细信息的 OBJECTIVE-C 桥接 Xamarin.iOS，以启用延迟的绑定。 你应将其视为等效于私有字段，应使用只能从代码隐藏类。 如果你想要公开它们，将添加到非设计器类的部分中，访问器属性，像为任何其他私有字段。

如果将 outlet 属性定义为具有一种`id`(等效于`NSObject`) 则设计器代码生成器当前确定根据对象连接到该插座，为方便起见，最高可能类型。
但是，这可能不支持在将来版本中，因此建议定义的自定义类时显式强键入 outlet。

### <a name="action-properties"></a>操作属性

设计器类包含分部方法对应于对自定义类定义的所有操作。 这些是不包含实现的方法。 分部方法的目的是双重的：

1.  如果你键入`partial`在非设计器类部件的类正文中，适用于 Mac 的 Visual Studio 将向提供记忆式键入的所有非实现分部方法签名。
2.  分部方法签名具有对应用的属性，将它们公开到 Objective C 世界中，以便可以获取当作相应的操作。


如果您愿意，可能忽略分部方法，并通过将属性应用到不同的方法，实现操作或将其贯穿基类。

如果定义操作的类型为发件人`id`(等效于`NSObject`)，则设计器代码生成器当前确定基于连接到该操作的对象的最高可能类型。 但是，这可能不支持在将来版本中，因此建议定义的自定义类时显式强类型的操作。

请注意，这些分部方法仅为创建 C# 中，因为 CodeDOM 不支持分部方法，因此它们不会生成其他语言。

## <a name="cross-xib-class-usage"></a>跨 XIB 类使用情况

有时，用户希望从多个引用同一个类 **.xib**文件，例如，使用选项卡控制器。 这可以通过从另一个引用类定义的显式 **.xib**文件，或通过在第二个定义相同的类名称 **.xib**。

后一种情况可能会产生问题由于 Visual Studio 进行 Mac 处理 **.xib**单独文件。 它无法自动检测并合并重复的定义，因此你可能会得到应用注册属性多次，相同的分部类定义在设计器的多个文件时的冲突。 最新版本的 Visual Studio for Mac 尝试解决此问题，但它可能无法始终按预期工作。 在将来，这很可能会变得不受支持，并改为适用于 Mac 的 Visual Studio 将在所有中定义的所有类型 **.xib**文件和托管的代码中直接显示来自所有项目 **.xib**文件。

## <a name="type-resolution"></a>类型解析

IB 中使用的类型是 OBJECTIVE-C 的类型名称。 它们被映射到 CLR 类型，通过使用寄存器特性。 生成 outlet，操作代码时, 适用于 Mac 的 Visual Studio 将解决所有 Objective C 类型包装在 Xamarin.iOS core 上的相应 CLR 类型和完全限定类型名称。

但是，代码生成器当前无法解析在用户代码或库中的 OBJECTIVE-C 的类型名称的 CLR 类型，因此在这种情况下它将输出原义的类型名称。 这意味着相应的 CLR 类型必须具有相同的名称作为 Objective C 类型而且必须在相同的命名空间正在使用它的代码。 计划必须修复过一段时间在将来通过在代码生成期间考虑将项目中的所有 OBJECTIVE-C 的类型。
