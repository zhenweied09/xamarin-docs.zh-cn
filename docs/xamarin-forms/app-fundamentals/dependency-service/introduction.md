---
title: DependencyService 简介
description: 此文章介绍了 Xamarin.Forms DependencyService 类访问本机平台功能的工作原理。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 8f32255b6451b5b672293c8db42bb8b1ab38a7fd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061833"
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 简介

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

## <a name="overview"></a>概述

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 允许应用从共享代码中调用特定于平台的功能。 此功能允许 Xamarin.Forms 应用执行本机应用可以执行的任何操作。

`DependencyService` 是服务定位符。 在实践中，定义了一个界面且 `DependencyService` 在多种平台项目中发现该界面的正确实现。

> [!NOTE]
> 默认情况下，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 仅解析具有无参数构造函数的平台实现。 但是，依赖项解析方法可以注入到使用依赖项注入容器或工厂方法的 Xamarin.Forms 中，以解析平台实现。 这种方法可用于解析具有带有参数的构造函数的平台实现。 有关详细信息，请参阅 [Xamarin.Forms 中的依赖项解析](~/xamarin-forms/internals/dependency-resolution.md)。

## <a name="how-dependencyservice-works"></a>DependencyService 的工作原理

Xamarin.Forms 应用需要四个组件才能使用 `DependencyService`：

- **接口** &ndash; 所需的功能由共享代码中的接口定义。
- **按平台实现** &ndash; 实现接口的类必须添加到每个平台项目。
- **注册** &ndash; 必须通过元数据属性向 `DependencyService` 注册每个实现类。 注册使 `DependencyService` 能够找到实现类并在运行时将其提供到位。
- **DependencyService 的调用** &ndash; 共享代码需要显式调用 `DependencyService`，以寻求接口实现。

请注意，必须为解决方案中的每个平台项目提供实现。 没有实现的平台项目在运行时将失败。

下图介绍了应用程序的结构：

![](introduction-images/overview-diagram.png "DependencyService 应用程序结构")

### <a name="interface"></a>接口

你设计的接口将定义如何与特定于平台的功能进行交互。 如果正在开发要以组件或 NuGet 包形式共享的组件，请小心。 API 设计可以创建或破坏包。 下面的示例指定语音文本的简单接口，该接口允许灵活指定要说出的字词，但还需要为每个平台自定义实现：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>按平台的实现

设计了合适的接口后，必须在针对面向的每个平台的项目中实现该接口。 例如，以下类可在 iOS 上实现 `ITextToSpeech` 接口：

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

接口的每个实现都需要向具有元数据属性的 `DependencyService` 注册。 下面的代码注册适用于 iOS 的实现：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

综合来讲，特定于平台的实现如下所示：

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

注意：在命名空间级别，而不是类级别进行注册。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台 .NET Native 编译

初始化 Xamarin.Forms 时，使用 .NET Native 编译选项的 UWP 项目应遵循[略有不同的配置](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception)。 对于依赖项服务，.NET Native 编译还要求注册略有不同。

在 App.xaml.cs 文件中，使用 `Register<T>` 方法，手动注册 UWP 项目中定义的每个依赖项服务，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

注意：使用 `Register<T>` 的手动注册仅在使用 .NET Native 编译的发行内部版本中有效。 如果省略此行，调试内部版本仍能运行，但发行内部版本将无法加载依赖项服务。

### <a name="call-to-dependencyservice"></a>调用 DependencyService

使用通用接口和每个平台的实现设置项目后，请使用 `DependencyService` 在运行时获取正确的实现：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 将查找接口 `T` 的正确实现。

### <a name="solution-structure"></a>解决方案结构

适用于 iOS 和 Android 的[示例 UsingDependencyService 解决方案](https://developer.xamarin.com/samples/UsingDependencyService/)如下所示，并且突出显示上述代码更改。

 [![iOS 和 Android 解决方案](introduction-images/solution-sml.png "DependencyService 示例解决方案结构")](introduction-images/solution.png#lightbox "DependencyService Sample Solution Structure")

> [!NOTE]
> 必须在每个平台项目中提供实现。 如果未注册任何接口实现，则 `DependencyService` 无法在运行时解析 `Get<T>()` 方法。

## <a name="related-links"></a>相关链接

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
