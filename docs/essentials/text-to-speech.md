---
title: Xamarin.Essentials 文本到语音转换
description: 应用程序文本到语音转换引擎进行沟通后的文本，从设备和可用查询引擎可以支持的语言中利用内置 TextToSpeech 类可实现。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2fcb97eafda15a73e2b7e5441f1d6c94f75af0ce
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials 文本到语音转换

![预发行 NuGet](~/media/shared/pre-release.png)

**TextToSpeech**类可实现一个应用程序利用内置文本到语音转换引擎进行沟通后的文本，从设备和可用查询引擎可以支持的语言。

## <a name="using-text-to-speech"></a>使用文本到语音转换

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

文本到语音转换功能的工作方式是调用`SpeakAsync`文本和可选参数以及返回 utterance 完毕后的方法。 

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

此方法采用可选 CancellationToken 停止 utterance 一个它的起始位置。 
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

文本到语音转换会自动加入队列语音请求在同一个线程。 

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

更灵活地控制如何读出音频一下`SpeakSettings`，它允许设置音量、 音调、 和区域设置。

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

支持为这些参数的值如下：

| 参数 | 最低 | 最大值 |
| --- | :---: | :---: |
| 音调 | 0 | 2.0 |
| 卷 | 0 | 1.0 |

### <a name="speech-locales"></a>语音区域设置

每个平台提供区域设置进行沟通中多个语言和重音符号后的文本。 每个平台都有不同代码和指定此操作，请的方式，这正是 Essentials 提供了跨平台`Locale`类和一种方法来查询它们与`GetLocalesAsync`。

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

- 如果在多个线程间调用，不保证 utterance 队列。
- 后台音频播放未正式受到支持。

## <a name="api"></a>API

- [TextToSpeech 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/TextToSpeech)
- [TextToSpeech API 文档](xref:Xamarin.Essentials.TextToSpeech)
