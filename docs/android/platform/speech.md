---
title: Android 语音
description: 本文介绍如何使用非常强大 Android.Speech 命名空间的基础知识。 自推出以来已能够识别语音并将其输出以文本形式 Android。 它是一个相对简单的过程。 文本到语音，但是，对于过程是更为复杂，因为不仅不语音引擎必须考虑到帐户，但还语言可用且已安装从文本到语音转换 (TTS) 系统。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/02/2018
ms.openlocfilehash: bdaa9bf09485c06551a2df15a2e3a4b410a53e75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="android-speech"></a>Android 语音

_本文介绍如何使用非常强大 Android.Speech 命名空间的基础知识。自推出以来已能够识别语音并将其输出以文本形式 Android。它是一个相对简单的过程。文本到语音，但是，对于过程是更为复杂，因为不仅不语音引擎必须考虑到帐户，但还语言可用且已安装从文本到语音转换 (TTS) 系统。_

## <a name="speech-overview"></a>语音概述

具有系统，也不能"了解"人工语音 enunciates 正在键入的内容-语音到文本和文本到语音 — 是内移动开发的某个不断发展区域，如与我们的设备的自然通信的需求上升而变化。 有多个实例，具有一种功能，将文本转换为语音，或反之，是非常有用的工具，用于将合并到 android 应用程序。

例如，使用向下移动电话使用，同时还能降低夹具，用户需要运行他们的设备的一之手可用方式。 过多的不同的 Android 外形因素 — 例如 Android 磨损-并且不断扩大的那些包含能够使用 Android 设备 （如平板电脑和记事本） 具有更大的焦点在上创建好 TTS 应用程序。

Google 提供开发人员使用一组 Android.Speech 命名空间中丰富的 Api，以涵盖的设备"语音识别"（如 for the blind 而设计的软件） 的大多数实例。  该命名空间包含的工具，以便允许文本转换为语音通过`Android.Speech.Tts`，用于执行转换，以及大量的引擎控制`RecognizerIntent`s 允许语音可转换为文本。

在设施还有适用于语音为，可以在基于使用的硬件的限制。 不太可能设备成功解释说到它在每个语言中可用的所有内容。

## <a name="requirements"></a>要求

有本指南中，你的设备具有麦克风和扬声器以外没有特殊要求。

解释语音的 Android 设备的核心是使用`Intent`与相应`OnActivityResult`。
很重要，不过，若要识别不理解语音-但解释为文本。 差异十分重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>了解和解释之间的差异

了解简单定义是您能够确定色调和上下文的内容的大意的实际含义。 只需解释意味着能够拍摄单词，并在另一种形式中输出，它们。

请考虑以下日常对话中使用的简单示例： 

<kbd>喂，你好吗？</kbd>

而无需音调 （强调放置在特定单词或单词的一部分），这是个简单的问题。 但是，如果慢的速度应用于线条，侦听的人员将检测提问者不是太高兴并且可能需要 cheering 或者提问者可 unwell。 如果强调了在"是"，要求的人是通常更感兴趣的响应。

不带非常强大的音频处理以使音调和度人工智能 (AI) 使用以了解上下文，该软件甚至无法开始了解所说的内容-简单 phone 可以做的最好是将语音转换为文本。

## <a name="setting-up"></a>设置

在使用之前语音系统，它始终明智的做法是检查以确保该设备具有麦克风。 将小点尝试 Kindle 或 Google 注意小键盘上没有安装麦克风的情况下运行你的应用。

下面的代码示例演示查询麦克风是否可用以及是否不是，创建警报。 如果没有麦克风是可用在此时你将则退出活动，或禁用记录语音的能力。

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>创建意图

语音系统的目的使用特定类型的调用的意图`RecognizerIntent`。 此意向控制大量的参数，包括多长时间记录视为通过任何其他语言来识别并输出，等待与静默和要包括在任何文本`Intent`的作为种指令的模式对话框。 在此代码段，`VOICE`是`readonly int`用于识别`OnActivityResult`。

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>语音的转换

从语音解释的文本将传递内`Intent`，活动已完成，并且通过访问时返回这`GetStringArrayListExtra(RecognizerIntent.ExtraResults)`。 这将返回`IList<string>`，而该索引可以使用并显示，具体取决于在调用方意向中请求的语言的数目 (和指定的`RecognizerIntent.ExtraMaxResults`)。 与任何列表不过，它是值得检查，以确保没有要显示数据。

当返回值为侦听`StartActivityForResult`、`OnActivityResult`方法具有待提供。

在示例中，`textBox`是`TextBox`用于输出决定什么。 同样无法用于将文本传递给某种形式的解释器，并从，应用程序可以比较文本和应用程序的另一部分的分支。

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
             if (matches.Count != 0)
             {
                  string textInput = textBox.Text + matches[0];
                  textBox.Text = textInput;
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>文本到语音转换

文本到语音转换不是非常的相反值语音到文本，并依赖于两个关键组件;文本到语音转换引擎正在设备上安装并正在安装一种语言。

使用默认值，Android 设备有很大程度上，Google TTS 服务安装并在至少一种语言。 这建立当第一次启动设备，并且将基于设备是时 （例如，手机设置德国将安装德语语言，而另一个中 America 将具有美国英语）。

### <a name="step-1---instantiating-texttospeech"></a>步骤 1-实例化 TextToSpeech

`TextToSpeech` 可能需要最多 3 个参数，在前两个需要与第三个正在可选 (`AppContext`， `IOnInitListener`， `engine`)。 侦听器用于使用正在任意数量的可用 Android 的文本到语音转换引擎的引擎将绑定到的服务和失败的测试。 在最低限度上，设备将有 Google 自己引擎。

### <a name="step-2---finding-the-languages-available"></a>步骤 2-查找可用的语言

`Java.Util.Locale`类包含一个很有帮助的方法调用`GetAvailableLocales()`。 然后可以针对已安装的语言测试语音引擎支持的语言的此列表。

它是一个普通以生成"理解"语言的列表。 始终会默认语言 （用户设置时它们首次设置其设备的语言），因此，在此示例`List<string>`"Default"作为第一个参数，将根据的结果填充列表的其余部分`textToSpeech.IsLanguageAvailable(locale)`。

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

此代码调用[TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/)来测试给定区域设置的语言包是否已在设备上存在。 此方法返回[LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/)，指示是否可传递的区域设置的语言。 如果`LanguageAvailableResult`指示的语言是`NotSupported`，则没有可用 （即使对于下载） 的语音程序包针对该语言。 如果`LanguageAvailableResult`设置为`MissingData`，则可能如下所述在步骤 4 中下载新的语言包。

### <a name="step-3---setting-the-speed-and-pitch"></a>步骤 3-设置速度和音调变化

Android 允许用户通过更改 alter 语音的声音`SpeechRate`和`Pitch`（的速度和语音的语气速度）。 此范围为 0 到 1，与"正常"语音正在为 1。

### <a name="step-4---testing-and-loading-new-languages"></a>步骤 4-测试和加载新语言

使用下载的新语言执行`Intent`。 此方法的结果导致[OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/)要调用的方法。 与语音到文本的示例不同 (后者使用[RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/)作为`PutExtra`参数`Intent`)，测试和加载`Intent`是`Action`-基于：

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash;从平台中启动活动`TextToSpeech`引擎以验证正确安装和在设备上的语言资源的可用性。

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash;启动提示用户下载需要的语言的活动。

下面的代码示例演示如何使用这些操作以测试语言资源，并下载新的语言：

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` 语言资源的可用性测试。 `OnActivityResult` 此测试完成时调用。 如果需要下载，语言资源`OnActivityResult`激发一次`TextToSpeech.Engine.ActionInstallTtsData`操作启动的活动，允许用户下载需要的语言。 请注意此`OnActivityResult`实现不会检查`Result`因为在此简化的示例中，确定已进行了语言包需要下载的代码。

`TextToSpeech.Engine.ActionInstallTtsData`操作原因**Google TTS 语音数据**活动以选择要下载的语言显示给用户：

![Google TTS 语音数据活动](speech-images/01-google-tts-voice-data.png)

例如，用户可能会选取法语，然后单击下载图标以下载法语语音数据：

![下载法语语言的示例](speech-images/02-selecting-french.png)

在下载完成后，此数据的安装将自动发生。


### <a name="step-5---the-ioninitlistener"></a>步骤 5-IOnInitListener

为活动能够将转换文本到语音，接口方法`OnInit`必须实现 (这是指定的实例化的第二个参数`TextToSpeech`类)。 此初始化侦听器并测试结果。

侦听器应进行测试以同时`OperationResult.Success`和`OperationResult.Failure`最少。
下面的示例演示就是这些：

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>总结

在本指南中我们讨论过将文本到语音转换和语音转换为文本和如何将其包含在你自己的应用程序中的可能方法的基础知识。 虽然它们并未涵盖每个特定用例，你应该已经基本了解如何解释语音、 如何安装新的语言，以及如何增加你的应用程序的 inclusivity。



## <a name="related-links"></a>相关链接

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [文本到语音转换 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [语音到文本 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Android.Speech 命名空间](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Android.Speech.Tts 命名空间](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
