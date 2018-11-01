---
title: DependencyService 简介
description: 此文章介绍了 Xamarin.Forms DependencyService 类以访问本机平台功能的工作方式。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675011"
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 简介

## <a name="overview"></a>概述

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 允许应用从共享代码调用特定于平台的功能。 此功能通过 Xamarin.Forms 应用，若要执行的本机应用程序可以执行任何操作。

`DependencyService` 是服务定位器。 在实践中，定义了一个接口和`DependencyService`查找该接口从各种平台项目的正确实现。

> [!NOTE]
> 默认情况下[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)将唯一解析平台具有无参数构造函数的实现。 但是，依赖关系解析方法可以注入到 Xamarin.Forms 使用依赖关系注入容器或工厂方法来解析平台的实现。 这种方法可以用于解决平台实现个构造函数的参数。 有关详细信息，请参阅[Xamarin.Forms 中的依赖项解析](~/xamarin-forms/internals/dependency-resolution.md)。

## <a name="how-dependencyservice-works"></a>DependencyService 的工作原理

Xamarin.Forms 应用需要四个组件来使用`DependencyService`:

- **接口**&ndash;由共享代码中的接口定义所需的功能。
- **实现每个平台**&ndash;实现接口的类必须添加到每个平台项目。
- **注册**&ndash;每个实现类必须与注册`DependencyService`通过元数据属性。 注册启用`DependencyService`来找到实现类并将其提供替代该接口在运行时。
- **调用到 DependencyService** &ndash;共享代码需要显式调用`DependencyService`寻求接口的实现。

请注意，必须为每个平台项目在解决方案中提供实现。 平台项目中的不包含实现将在运行时失败。

下图介绍了应用程序的结构：

![](introduction-images/overview-diagram.png "DependencyService 应用程序结构")

### <a name="interface"></a>接口

您设计的接口将定义如何与特定于平台的功能进行交互。 注意如果你正在开发的组件或 NuGet 包作为共享的组件。 API 设计可以执行或破坏包。 下面的示例指定语音文本的允许的灵活指定字要朗读但由要为每个平台自定义的实现简单的界面：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>每个平台的实现

一旦合适接口在设计时，必须为您面向的每个平台项目中实现该接口。 例如，以下类实现`ITextToSpeech`在 iOS 上的接口：

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>注册

每个接口的实现需要使用注册`DependencyService`与元数据特性。 下面的代码注册适用于 iOS 的实现：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

将所有组合在一起，特定于平台的实现如下所示：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

注意： 在命名空间级别而不是类级别执行注册。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台.NET Native 编译

使用.NET Native 编译选项的 UWP 项目应遵循[略有不同的配置](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception)初始化 Xamarin.Forms 时。 .NET native 编译依赖关系服务还要求略有不同的注册。

在中**App.xaml.cs**文件中，手动注册每个依赖关系服务，在 UWP 项目中使用定义`Register<T>`方法，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

注意： 手动注册使用`Register<T>`是使用.NET Native 编译生成的版本中的才有效。 如果省略此行，调试版本中仍将起作用，但发布版本将无法加载依赖关系服务。

### <a name="call-to-dependencyservice"></a>调用到 DependencyService

一旦项目已具有一个公共接口和实现为每个平台已设置，使用`DependencyService`若要在运行时获取正确的实现：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 将找到正确的接口实现`T`。

### <a name="solution-structure"></a>解决方案结构

[示例 UsingDependencyService 解决方案](https://developer.xamarin.com/samples/UsingDependencyService/)是适用于 iOS 和 Android 如下所示，使用上面所述的代码更改突出显示。

 [![iOS 和 Android 的解决方案](introduction-images/solution-sml.png "DependencyService 示例解决方案结构")](introduction-images/solution.png#lightbox "DependencyService 示例解决方案结构")

> [!NOTE]
> 您**必须**提供每个平台项目中的实现。 如果不注册了任何接口实现，则`DependencyService`将不能解决`Get<T>()`方法在运行时。

## <a name="related-links"></a>相关链接

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
