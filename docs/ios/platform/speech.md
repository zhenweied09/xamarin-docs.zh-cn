---
title: "语音识别"
description: "本文提供了新的语音 API，并演示如何在 Xamarin.iOS 应用程序以支持连续语音识别和理赔 （从实时或录制音频流） 的语音到文本中实现它。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e868c0ee71688e208c5217d9f5a89ea3acec988c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="speech-recognition"></a>语音识别

_本文提供了新的语音 API，并演示如何在 Xamarin.iOS 应用程序以支持连续语音识别和理赔 （从实时或录制音频流） 的语音到文本中实现它。_

新到 iOS 10，Apple 发布了用于转换为文本的 iOS 应用程序支持连续语音识别和语音 （从实时或录制音频流），理赔语音识别 API。

根据 Apple，该语音识别 API 具有以下功能和优势：

- 高度准确
- 最先进的
- 易于使用
- 快速
- 支持多种语言
- 尊重用户隐私

## <a name="how-speech-recognition-works"></a>语音识别的工作原理

语音识别由实现 iOS 应用程序中获取 （在任何使用该 API 支持的语言） 的实时或预先录制音频，并将其传递到语音识别器返回朗读单词的纯文本脚本。

[![](speech-images/speech01.png "语音识别的工作原理")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>键盘听写

当大多数用户的 iOS 设备上的语音识别认为时，它们将与 iPhone 4S iOS 5 中发布以及键盘听写内置 Siri 语音助手。

键盘听写受支持 TextKit 任何界面元素 (如`UITextField`或`UITextArea`)，并通过单击 iOS 虚拟键盘中的听写按钮 （直接到空格键左） 的用户激活。

Apple 已发布以下键盘听写统计信息 （收集自 2011年）：

- 自发布 iOS 5 中后已广泛使用键盘听写。
- 大约 65000 应用每天可以使用它。
- 有关的所有 iOS 第三个听写是在第三方应用程序中完成的。

键盘听写是非常易于使用，因为它不需要开发人员的一部分，而不是在应用程序的 UI 设计使用 TextKit 界面元素上的任何工作。 键盘听写还有不需要从应用任何特殊权限请求，然后才能使用它的优点。

使用新的语音识别 Api 的应用将需要特殊权限才能被授予用户，因为语音识别需要的传输和 Apple 的服务器上的数据的临时存储。 请参阅我们[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)有关详细信息的文档。

轻松地实现键盘听写时，它确实会对几个限制和缺点：

- 它需要文本输入字段的使用和显示键盘。
- 它可以使用实时音频输入只有且应用程序具有不能控制音频录制过程。
- 它提供了用于解释用户的语音的语言不控制。
- 没有为应用程序可以了解如果听写按钮甚至可供用户方法。
- 应用程序不能自定义的音频录制过程。
- 它提供了非常浅表的结果集中缺少计时和置信度等信息。

### <a name="speech-recognition-api"></a>语音识别 API

新到 iOS 10，Apple 已发布语音识别 API，提供了更强大的方法来实现语音识别为 iOS 应用。 此 API 已同一个 Apple 用于 power Siri 和键盘听写并且能够快速脚本提供最先进的准确性。

语音识别 API 提供的结果以透明方式自定义于单个用户，而无需应用程序无需收集或访问任何私有用户数据。

语音识别 API 提供结果返回到调用应用程序中近似实时语速用户以及它提供有关转换的结果比只包含文本的详细信息。 这些方法包括：

- 用户所说的内容的多个解释。
- 单个翻译的置信度级别。
- 计时信息。

如上面所述，是通过实时源，或从预记录源和中的任何 50 多个语言和方言，支持的 iOS 10 可以提供适用于转换的音频。

语音识别 API 可以在运行 iOS 10 的任何 iOS 设备上使用，并且由于翻译大量发生在 Apple 的服务器上，则在大多数情况下，需要实时的 internet 连接。 也就是说，某些较新的 iOS 设备始终支持、 特定于语言的设备上的转换。

Apple 包含一个可用性 API，用于确定是否可用于在当前时间转换给定的语言。 应用程序应使用此 API，而不是直接对其自身的 internet 连接进行测试。

如上所述键盘听写部分中，语音识别将需要的传输和 Apple 的服务器上的数据的临时存储，通过 internet，并因此，应用程序_必须_请求执行的用户的权限通过包括识别`NSSpeechRecognitionUsageDescription`中的键其`Info.plist`文件和调用`SFSpeechRecognizer.RequestAuthorization`方法。 

其他基于源的音频正用于语音识别，更改到应用程序的`Info.plist`文件可能需要。 请参阅我们[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)有关详细信息的文档。

## <a name="adopting-speech-recognition-in-an-app"></a>采用应用程序中的语音识别

有四个开发人员采用 iOS 应用程序中的语音识别时必须执行的主要步骤：

- 提供在应用程序的使用说明`Info.plist`文件使用`NSSpeechRecognitionUsageDescription`密钥。 例如，相机应用可能包括以下说明中， _"这样你只需通过说单词 cheese 拍摄一张照片。"_
- 通过调用请求授权`SFSpeechRecognizer.RequestAuthorization`方法以提供说明 (中提供`NSSpeechRecognitionUsageDescription`密钥更高版本) 的应用程序为什么想语音识别一个对话框中的用户访问，使他们可以接受或拒绝。
- 创建语音识别请求：
    * 对于磁盘上的预先录制音频，使用`SFSpeechURLRecognitionRequest`类。
    * 对于实时音频 （或从内存的音频），使用`SFSPeechAudioBufferRecognitionRequest`类。
- 将传递到语音识别器语音识别请求 (`SFSpeechRecognizer`) 以开始识别。 应用程序可以选择性地保留到返回`SFSpeechRecognitionTask`来监视和跟踪识别结果。

将在下面将详细介绍这些步骤。

### <a name="providing-a-usage-description"></a>提供使用说明

若要提供所需`NSSpeechRecognitionUsageDescription`中的键`Info.plist`文件中，执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击`Info.plist`文件以打开进行编辑。
2. 切换到**源**视图： 

    [![](speech-images/speech02.png "源视图")](speech-images/speech02.png#lightbox)
3. 单击**添加新项**，输入`NSSpeechRecognitionUsageDescription`为**属性**，`String`为**类型**和**使用率描述**作为**值**。 例如： 

    [![](speech-images/speech03.png "添加 NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. 如果应用程序将处理实时音频脚本，它还将需要麦克风使用说明。 单击**添加新项**，输入`NSMicrophoneUsageDescription`为**属性**，`String`为**类型**和**使用率描述**作为**值**。 例如： 

    [![](speech-images/speech04.png "添加 NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. 保存对文件所做的更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击`Info.plist`文件以打开进行编辑。
3. 单击**添加新项**，输入`NSSpeechRecognitionUsageDescription`为**属性**，`String`为**类型**和**使用率描述**作为**值**。 例如： 

    [![](speech-images/speech03w.png "添加 NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. 如果应用程序将处理实时音频脚本，它还将需要麦克风使用说明。 单击**添加新项**，输入`NSMicrophoneUsageDescription`为**属性**，`String`为**类型**和**使用率描述**作为**值**。 例如： 

    [![](speech-images/speech04w.png "添加 NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. 保存对文件所做的更改。

-----

> [!IMPORTANT]
> 无法向上述任一提供`Info.plist`密钥 (`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription`) 可能会导致应用程序在尝试访问语音识别或实时音频麦克风时失败而不发出警告。




### <a name="requesting-authorization"></a>请求的授权

若要请求允许应用程序访问语音识别所需的用户授权，编辑主视图控制器类并添加以下代码：

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

`RequestAuthorization`方法`SFSpeechRecognizer`类将为使用开发人员所述的原因的访问语音识别从用户请求权限`NSSpeechRecognitionUsageDescription`键`Info.plist`文件。

A`SFSpeechRecognizerAuthorizationStatus`结果返回到`RequestAuthorization`可用于采取操作的方法的回调例程基于用户的权限。 

> [!IMPORTANT]
> Apple 提供的建议等待用户已请求此权限之前需要语音识别应用程序在启动某项操作。

### <a name="recognizing-pre-recorded-speech"></a>识别预记录的语音

如果应用想要识别语音从的预录的 WAV 或 MP3 文件，它可以使用下面的代码：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

查看详细信息在此代码，首先，尝试创建语音识别器 (`SFSpeechRecognizer`)。 如果默认语言不支持语音识别`null`返回并在函数退出。

如果语音识别器用于默认语言，应用程序检查以查看它是否为当前可用于识别使用`Available`属性。 例如，识别可能不可用，如果设备不具有有效的 internet 连接。

A`SFSpeechUrlRecognitionRequest`从创建`NSUrl`上 iOS 设备和它的预录文件的位置传递给语音识别器要处理的回调例程。

调用回调时，如果`NSError`不`null`已必须处理的错误。 由于语音识别以增量方式执行的可能会超过一次，因此调用回调例程`SFSpeechRecognitionResult.Final`属性测试以查看是否转换完成，转换的最佳版本写出 (`BestTranscription`)。

### <a name="recognizing-live-speech"></a>识别实时语音

如果应用想要识别实时语音，过程是非常类似于识别预录制的语音。 例如：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

查看详细信息在此代码，它会创建多个私有变量，用于处理识别过程：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它将传递给的录制音频使用 AV Foundation`SFSpeechAudioBufferRecognitionRequest`来处理识别请求：

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

应用程序将尝试开始记录和记录不能启动是否处理任何错误：

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

识别任务已启动并且句柄将保持为识别任务 (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

中的预录语音上上面使用的一个类似的方式使用该回调。

如果用户停止录制，系统将通知音频引擎和语音识别请求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果用户取消识别，音频引擎和识别任务将通知：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

务必要调用`RecognitionTask.Cancel`如果用户取消的转换以释放内存和设备的处理器。

> [!IMPORTANT]
> 无法向提供`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription``Info.plist`密钥可能会导致应用程序在尝试访问语音识别或实时音频麦克风时失败而不发出警告 (`var node = AudioEngine.InputNode;`)。 请参阅**提供使用率描述**上面部分以了解详细信息。

## <a name="speech-recognition-limits"></a>语音识别限制

使用 iOS 应用中的语音识别时，Apple 将施加以下限制：

- 语音识别是免费的所有应用程序，但其使用情况不是不受限制：
    - 单个的 iOS 设备具有有限的数量的荣誉可以每日执行。
    - 应用基于每日的请求将全局被阻止。
- 应用程序必须准备好处理语音识别网络连接和使用情况速率限制失败。
- 语音识别可因此在用户的 iOS 设备上具有在电池消耗和较高网络流量较高的成本、 Apple 施加的严格音频的持续时间限制为大约一分钟的最大的语音。

如果应用例行达到其速率限制的限制，则会要求 Apple 开发人员，与他们联系。

## <a name="privacy-and-usability-considerations"></a>隐私和可用性注意事项

Apple 提供有关正在透明并在 iOS 应用程序中包括语音识别时尊重用户隐私以下建议：

- 记录用户的语音时, 一定要清楚地指示录制在应用程序的用户界面中正在进行。 例如，该应用可能播放"录制"声音并显示录制的指示器。
- 不要使用语音识别诸如密码、 运行状况数据或财务信息等敏感用户信息。
- 显示识别结果_之前_对它们进行操作。 这不仅提供有关应用程序正在执行，但允许用户处理识别错误所做的反馈。

## <a name="summary"></a>总结

这篇文章已提供新的语音 API，并介绍了如何在 Xamarin.iOS 应用程序以支持连续语音识别和理赔 （从实时或录制音频流） 的语音到文本中实现它。 



## <a name="related-links"></a>相关链接

- [SpeakToMe （示例）](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
