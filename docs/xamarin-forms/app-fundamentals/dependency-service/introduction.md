---
title: "DependencyService 简介"
description: "了解 DependencyService 以访问本机平台功能的工作原理"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: e599c56f732f918d2a9c82255bc01182651d506c
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 简介

## <a name="overview"></a>概述

[`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 允许应用程序共享代码中调入特定于平台的功能。 此功能使 Xamarin.Forms 应用需执行任何操作可以执行的本机应用。

`DependencyService` 依赖项解析程序。 在实践中，定义了一个接口和`DependencyService`查找正确实现该接口从各种平台项目。

## <a name="how-dependencyservice-works"></a>DependencyService 的工作原理

Xamarin.Forms 应用程序需要三个组件使用`DependencyService`:

- **接口**&ndash;由在共享代码中的接口定义所需的功能。
- **实现每个平台**&ndash;实现接口的类必须添加到每个平台项目。
- **注册**&ndash;必须已注册每个实现类`DependencyService`通过元数据属性。 注册启用`DependencyService`若要查找实现的类并在运行时为其提供替代接口。
- **调用 DependencyService** &ndash;共享代码需要显式调用`DependencyService`以询问有关接口的实现。

请注意，必须为每个平台项目解决方案中提供实现。 不包含实现的平台项目将在运行时失败。

下图解释了应用程序的结构：

![](introduction-images/overview-diagram.png "DependencyService 应用程序结构")

### <a name="interface"></a>接口

你设计的接口将定义如何与特定于平台的功能进行交互。 请注意，如果你正在开发的组件或 Nuget 包作为共享的组件。 API 设计可以使或中断包。 下面的示例指定语速允许灵活地指定单词要朗读但由实现为每个平台自定义的文本的简单接口：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>每个平台的实现

已设计合适的接口之后, 必须在您面向的每个平台的项目中实现该接口。 例如，下面的类实现`ITextToSpeech`Windows Phone 上的接口：

```csharp
namespace TextToSpeech.WinPhone
{
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

请注意，每个实现必须具有默认 （无参数） 构造函数，顺序`DependencyService`能够其进行实例化。 无参数构造函数不能由接口定义。

### <a name="registration"></a>注册

每个接口的实现需要与注册`DependencyService`与元数据属性。 下面的代码注册 Windows Phone 的实现：

```csharp
using TextToSpeech.WinPhone;

[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  ...
```

将所有集中到一起，特定于平台的实现类似如下所示：

```csharp
[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

注意： 在命名空间级别，而不是类级别执行注册。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台.NET Native 编译

使用.NET Native 编译选项的 UWP 项目应遵循[略有不同的配置](~/xamarin-forms/platform/windows/installation/universal.md#target-invocation-exception)初始化 Xamarin.Forms 时。 .NET native 编译依赖关系服务还要求略有不同的注册。

在**App.xaml.cs**文件中，手动注册每个定义在 UWP 项目中使用的依赖关系服务`Register<T>`方法，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

注意： 手动注册使用`Register<T>`是版本中的才有效版本使用.NET 本机编译。 如果省略此行，调试版本中仍将起作用，但发行版本将无法加载依赖关系服务。

### <a name="call-to-dependencyservice"></a>调用 DependencyService

一旦项目已使用公共接口和实现的每个平台已设置，使用`DependencyService`在运行时获取正确的实现：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 将查找接口的正确实现`T`。

### <a name="solution-structure"></a>解决方案结构

[示例 UsingDependencyService 解决方案](https://developer.xamarin.com/samples/UsingDependencyService/)是下面显示了 iOS 和 Android，与上面所述的代码更改突出显示。

 [ ![iOS 和 Android 解决方案](introduction-images/solution-sml.png "DependencyService 示例解决方案结构")](introduction-images/solution.png "DependencyService 示例解决方案结构")

> [!NOTE]
> **注意：**你**必须**提供每个平台项目中的实现。 如果不注册任何接口的实现，则`DependencyService`将不能解决`Get<T>()`方法在运行时。


## <a name="related-links"></a>相关链接

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
