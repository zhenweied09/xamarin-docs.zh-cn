---
title: 实现文本到语音转换
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类来调入每个平台的本机文本到语音转换 API。
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: d39902f2269d3eb241b48831b8eb1b181ff11e7a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997538"
---
# <a name="implementing-text-to-speech"></a>实现文本到语音转换

创建使用的跨平台应用这篇文章将指导你[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)访问本机文本到语音转换 Api:

- **[创建接口](#Creating_the_Interface)** &ndash;了解如何共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash;了解如何在适用于 iOS 的本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash;了解如何适用于 Android 的本机代码中实现的接口。
- **[UWP 实现](#WindowsImplementation)** &ndash;了解如何为通用 Windows 平台 (UWP) 中的本机代码实现的接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`来调入本机实现从共享代码。

应用程序使用`DependencyService`将具有以下结构：

![](text-to-speech-images/tts-diagram.png "DependencyService 应用程序结构")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，表达你打算实现的功能的共享代码中创建的接口。 对于此示例，该接口包含一个方法， `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

针对此接口在共享代码中编写代码将允许 Xamarin.Forms 应用程序访问每个平台上的语音 Api。

> [!NOTE]
> 实现接口的类必须具有无参数的构造函数，以使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

必须在每个特定于平台的应用程序项目中实现该接口。 请注意，类具有无参数构造函数，以便`DependencyService`可以创建新实例。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

`[assembly]`属性的实现作为注册类`ITextToSpeech`接口，这意味着`DependencyService.Get<ITextToSpeech>()`可用于共享代码中创建它的一个实例。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

Android 代码将更复杂的 iOS 版本比： 它需要实现的类从特定于 Android 的继承`Java.Lang.Object`并实现`IOnInitListener`接口也。 它还需要访问当前的 Android 上下文，它公开由`MainActivity.Instance`属性。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

`[assembly]`属性的实现作为注册类`ITextToSpeech`接口，这意味着`DependencyService.Get<ITextToSpeech>()`可用于共享代码中创建它的一个实例。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台实现

通用 Windows 平台具有中的语音 API`Windows.Media.SpeechSynthesis`命名空间。 唯一需要注意的是记住勾选**麦克风**功能在清单中，否则访问到语音 Api 被阻止。

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

`[assembly]`属性的实现作为注册类`ITextToSpeech`接口，这意味着`DependencyService.Get<ITextToSpeech>()`可用于共享代码中创建它的一个实例。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

现在我们可以编写并测试访问文本到语音转换接口的共享的代码。 此简单的页面包括触发的语音功能的按钮。 它使用`DependencyService`若要获取的实例`ITextToSpeech`接口&ndash;此实例将在运行时是具有完全访问权限的本机 SDK 的特定于平台的实现。

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

在 iOS、 Android 或 UWP 上运行此应用程序并按下按钮会讲到，每个平台上使用本机语音 SDK 的应用程序。

 ![iOS 和 Android 的文本到语音转换按钮](text-to-speech-images/running.png "文本到语音转换示例")


## <a name="related-links"></a>相关链接

- [使用 DependencyService （示例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [文本到语音转换工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
