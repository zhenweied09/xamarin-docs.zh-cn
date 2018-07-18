---
title: Xamarin.Essentials： 文本到语音转换
description: 中 Xamarin.Essentials 启用应用程序在要朗读后的文本，从设备和该引擎可以支持的查询可用语言的文本到语音转换引擎中使用内置的 TextToSpeech 类。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 9383411074bc43af1034138aadbb6ac5494c2c01
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815656"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials： 文本到语音转换

![预发行版 NuGet](~/media/shared/pre-release.png)

**TextToSpeech**类，在要朗读后的文本，从设备和该引擎可以支持的查询可用语言的文本到语音转换引擎中使用内置的应用程序。

## <a name="using-text-to-speech"></a>使用文本到语音转换

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

文本到语音转换功能适用于通过调用`SpeakAsync`方法与文本和可选参数，并返回完成后将语音样本。 

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) => 
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

此方法采用可选 CancellationToken 中停止它启动后将语音样本。 
```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

文本到语音转换将自动排队，在同一个线程的语音请求。 

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>语音设置

更灵活地控制如何解说音频一下`SpeakSettings`，它允许设置音量、 音调、 和区域设置。

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

以下是支持这些参数的值：

| 参数 | 最低 | 最大值 |
| --- | :---: | :---: |
| 间距 | 0 | 2.0 |
| 卷 | 0 | 1.0 |

### <a name="speech-locales"></a>语音的区域设置

每个平台提供了要朗读后的文本以多种语言和重音符号的区域设置。 每个平台都有不同的代码和方法的指定此操作，这就是为什么 Essentials 提供了跨平台`Locale`类和方法进行查询，使用`GetLocalesAsync`。

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>限制

- 不能保证查询文本队列，如果在多个线程调用。
- 背景音频播放不正式支持。

## <a name="api"></a>API

- [TextToSpeech 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [TextToSpeech API 文档](xref:Xamarin.Essentials.TextToSpeech)
