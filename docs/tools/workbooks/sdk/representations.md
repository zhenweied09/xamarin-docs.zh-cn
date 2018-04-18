---
title: Xamarin 的工作簿中的表示形式
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: a5593ac902bfd2478cd8587aeef7e4a3926627dd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 的工作簿中的表示形式

## <a name="representations"></a>表示形式之间实现

在工作簿或检查器会话中，通过在代理中表示管道进行处理，执行并输出的结果 （例如方法返回一个值或表达式的结果） 的代码。 所有对象，除了基元整数，如将反映生成交互式成员关系图，并且将经历一个过程来提供客户端可能会使更丰富的备用表示形式。 （包括周期和无限的可枚举对象），任何大小和深度的对象安全地由于延迟和交互式反射和远程处理支持。

Xamarin 的工作簿的所有代理和客户端，便于丰富呈现结果提供几种常见类型。 [`Color`][xir-color] 是这样的类型的一个示例，例如在 iOS 上，代理负责进行相互转换`CGColor`或`UIColor`将对象插入`Xamarin.Interactive.Representations.Color`对象。

除了公共的表示形式，集成 SDK 提供用于序列化代理中的自定义表示形式和呈现在客户端中的表示形式之间实现的 Api。

## <a name="external-representations"></a>外部表示形式

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] 提供注册的能力[ `RepresentationProvider` ] [ repp]，集成必须实现从任意对象转换为以不可知的形式呈现该。 这些不可知的窗体必须实现[ `ISerializableObject` ] [ serobj]接口。

实现`ISerializableObject`接口将添加精确地控制如何序列化对象的序列化方法。 `Serialize`方法需要开发人员将完全指定哪些属性要进行序列化，以及将哪些的最终名称。 查看`Person`对象中我们 [`KitchenSink`示例] [示例]，则我们可以看到此工作原理：

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果我们想要提供超集或从原始对象的属性的子集，可以做到这一点与`Serialize`。 例如，我们可能会执行类似于以下提供预先计算`Age`属性`Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> 生成的 Api`ISerializableObject`对象直接无需由处理`RepresentationProvider`。 如果你想要显示的对象是**不** `ISerializableObject`，你将想要处理包装在您`RepresentationProvider`。

### <a name="rendering-a-representation"></a>呈现表示形式

呈现器在 JavaScript 中实现，并将有权访问通过表示的对象的 JavaScript 版本`ISerializableObject`。 JavaScript 复制也将具有`$type`字符串指示的.NET 类型名称的属性。

我们建议使用 TypeScript 进行客户端集成代码，这是当然的编译为香草 JavaScript。 无论哪种方式，该 SDK 提供[typings] [ typings]它们可以直接引用 TypeScript 或简称为手动如果编写香草 JavaScript 首选。

呈现的主要集成点是`xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

在这里，`PersonRenderer`实现`Renderer`接口。 请参阅[typings] [ typings]有关详细信息。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
