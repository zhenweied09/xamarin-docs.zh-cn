---
title: "Android 音频"
description: "Android OS 为多媒体，包含音频和视频提供广泛支持。 本指南重点介绍在 Android 中的音频，并介绍如何播放和记录音频使用内置的音频播放器和记录器类，以及低级别的音频 API。 此外将介绍使用其他应用程序，广播的音频事件，从而使开发人员能够生成良好的应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ea3fd7d73f104f7b9650431a5531fe4399a2630c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="android-audio"></a>Android 音频

_Android OS 为多媒体，包含音频和视频提供广泛支持。本指南重点介绍在 Android 中的音频，并介绍如何播放和记录音频使用内置的音频播放器和记录器类，以及低级别的音频 API。此外将介绍使用其他应用程序，广播的音频事件，从而使开发人员能够生成良好的应用程序。_

<a name="Overview" />

## <a name="overview"></a>概述

现代移动设备已采用以前将具有所需的设备的专用的部件的功能&ndash;摄像头、 音乐播放器和视频刻录机。 因此，多媒体框架已变得移动 Api 中的第一类功能。

Android 为多媒体提供广泛支持。 这篇文章检查使用音频在 Android 中，并涵盖以下主题

1.  **播放音频使用 MediaPlayer** &ndash;使用内置`MediaPlayer`类播放音频，包括本地音频文件和经过流处理的音频文件与`AudioTrack`类。

2.  **录制音频**&ndash;使用内置`MediaRecorder`录制音频的类。

3.  **使用音频通知**&ndash;使用音频通知通过暂停或取消其音频输出来创建功能良好的应用程序正确响应事件 （如电话呼叫）。

4.  **使用低级别音频**&ndash;播放音频使用`AudioTrack`通过直接写入内存缓冲区的类。 录制音频使用`AudioRecord`类，并直接从内存缓冲区中读取。


## <a name="requirements"></a>惠?

本指南需要 Android 2.0 （API 级别 5） 或更高版本。 请注意，必须在设备上完成调试在 Android 上的音频。

则有必要进行请求`RECORD_AUDIO`中的权限**AndroidManifest.XML**:

![所需的 Android 清单与记录的权限部分\_启用的音频](android-audio-images/image01.png)


<a name="Playing_Audio_with_the_MediaPlayer_Class" />

## <a name="playing-audio-with-the-mediaplayer-class"></a>播放音频与 MediaPlayer 类

播放音频 Android 中的最简单方法是使用内置[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)类。
`MediaPlayer` 通过传入的文件路径，可以播放本地或远程文件。 但是，`MediaPlayer`非常状态区分和调用其方法之一在错误状态将导致异常引发。 务必与交互`MediaPlayer`若要避免错误下面所述的顺序。


<a name="Initializing_and_Playing" />

### <a name="initializing-and-playing"></a>初始化和播放

播放音频与`MediaPlayer`需要按以下顺序：

1. 实例化一个新[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)对象。

1. 配置文件以通过播放[SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/)方法。

1. 调用[准备](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/)方法以初始化播放器。

1. 调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/)方法开始音频播放。


下面的代码示例阐释了这种用法：

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```

<a name="Suspending_and_Resuming_Playback" />

### <a name="suspending-and-resuming-playback"></a>挂起和继续执行播放

可以通过调用挂起播放[暂停](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/)方法：

```csharp
player.Pause();
```

若要恢复已暂停的播放，请调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/)方法。
这将从在播放暂停的位置进行恢复：

```csharp
player.Start();
```

调用[停止](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/)播放器方法结束正在进行播放：

```csharp
player.Stop();
```

当不再需要播放器时，必须通过调用释放资源[版本](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/)方法：

```csharp
player.Release();
```

<a name="Using_the_MediaRecorder_Class_to_Record_Audio" />


## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用记录音频 MediaRecorder 类

到推论`MediaPlayer`Android 中的录制音频为[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)类。 如`MediaPlayer`，它状态区分，并将经历若干状态，可用于访问它可以在那里开始录制的点。 若要录制音频，`RECORD_AUDIO`权限必须设置。 有关说明如何将应用程序设置权限请参阅[使用 AndroidManifest.xml](~/android/platform/android-manifest.md)。

<a name="Initializing_and_Recording" />

### <a name="initializing-and-recording"></a>初始化和记录

录制音频和`MediaRecorder`需要执行下列步骤：

1. 实例化一个新[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)对象。

2. 指定要用于捕获通过音频的输入的硬件设备[SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/)方法。

3. 设置输出文件音频格式使用[SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/)方法。 有关支持的音频类型的列表，请参阅[Android 支持媒体格式](http://developer.android.com/guide/appendix/media-formats.html)。

4. 调用[SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/)方法以设置音频编码类型。

5. 调用[SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/)方法，以指定的音频数据写入到输出文件的名称。

6. 调用[准备](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/)方法以初始化记录器。

7. 调用[启动](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/)方法若要开始录制。


下面的代码示例阐释了这一序列：

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```

<a name="Stopping_recording" />

### <a name="stopping-recording"></a>停止录制

若要停止记录，请调用`Stop`方法`MediaRecorder`:

```csharp
recorder.Stop();
```

<a name="Cleaning_up" />


### <a name="cleaning-up"></a>清理

一次`MediaRecorder`已停止，调用[重置](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/)方法将其放回其空闲状态：

```csharp
recorder.Reset();
```

当`MediaRecorder`是不再需要其必须释放资源通过调用[版本](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/)方法：

```csharp
recorder.Release();
```

<a name="Managing_Audio_Notifications" />

## <a name="managing-audio-notifications"></a>管理音频通知

<a name="The_AudioManager_Class" />


### <a name="the-audiomanager-class"></a>AudioManager 类

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)类提供对音频让知道音频事件发生时的应用程序的通知的访问。 此服务还提供对其他音频的功能，如卷和响铃模式控制访问。 `AudioManager`允许应用程序处理音频通知来控制音频播放。

<a name="Managing_Audio_Focus" />


### <a name="managing-audio-focus"></a>管理音频焦点

设备 （内置播放器和记录器） 的音频资源共享的所有正在运行的应用程序。

从概念上讲，它类似于其中只有一个应用程序具有键盘焦点的台式计算机上的应用程序： 选择一个正在运行的应用程序通过鼠标单击它之后, 将转到该应用程序仅的键盘输入。

音频焦点一个类似的想法，可防止从播放或在同一时间录制音频的多个应用程序。 它是比键盘焦点更复杂的因为它是自愿&ndash;应用程序可以忽略这一事实其不当前不具有音频焦点，而不考虑播放&ndash;和因为不同类型的可能的音频焦点请求。 例如，如果请求者只需播放音频的时间很短，它可能会请求暂时性的焦点。

音频焦点可以立即授予，或最初拒绝并授予更高版本。 例如，如果应用程序请求音频焦点在电话呼叫期间，它将被拒绝，但完成电话呼叫后，也可以授予焦点。 在这种情况下，如果音频焦点移走相应地做出响应以便注册侦听器。 请求音频焦点用于确定它正常播放或录制音频。

有关 audio 焦点的详细信息，请参阅[管理音频焦点](http://developer.android.com/training/managing-audio/audio-focus.html)。


<a name="Registering_the_Callback_for_Audio_Focus" />

#### <a name="registering-the-callback-for-audio-focus"></a>音频焦点注册回调

注册`FocusChangeListener`来自回调`IOnAudioChangeListener`是获取和释放音频焦点的重要组成部分。 这是因为授予的音频焦点可能会延迟到更高版本的时间。 例如，应用程序可能会请求正在进行电话呼叫时播放音乐。 完成电话呼叫之前，将不授予音频焦点。

为此，回调对象传递到参数作为`GetAudioFocus`方法`AudioManager`，它并且注册回调，此调用。 应用程序如果音频焦点是最初被拒绝，但更高版本授予，将得到通知，通过调用`OnAudioFocusChange`在回调上。 相同的方法用于告知应用程序是消失花费音频焦点。

当应用程序完成后使用的音频的资源时，它将调用`AbandonFocus`方法`AudioManager`，并再次将传递回调中。 这注销回调，并释放的音频的资源，以便其他应用程序可能会收到音频焦点。


<a name="Requesting_Audio_Focus" />

#### <a name="requesting-audio-focus"></a>请求的音频焦点

请求设备的音频资源所需的步骤如下所示：

1.  获取的句柄`AudioManager`系统服务。

2.  创建回调类的实例。

3.  通过调用请求设备的音频资源`RequestAudioFocus`方法`AudioManager`。 参数是回调对象、 流类型 （音乐、 语音呼叫，环等） 以及右请求的访问的类型 （音频资源可能会请求暂时或无限期，例如）。

4.  如果请求被授予，`playMusic`立即，调用方法，并且音频开始播放。

5.  如果请求被拒绝，不执行任何进一步操作。 在这种情况下，如果请求批准在更高版本时，仅将播放音频。


下面的代码示例说明了这些步骤：

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```

<a name="Releasing_Audio_Focus" />

#### <a name="releasing-audio-focus"></a>释放音频焦点

轨道的播放完成后，`AbandonFocus`方法`AudioManager`调用。 这样，另一个应用程序来获取设备的音频资源。 如果用户已注册其自己的侦听器，其他应用程序将接收此音频焦点更改的通知。

<a name="Low_Level_Audio_API" />

## <a name="low-level-audio-api"></a>低级别的音频 API

这些低级别的音频 Api 提供更好地控制音频播放和录制，因为它们直接与而不是使用文件 Uri 的内存缓冲区进行交互。 有某些场景中，这种方法是更可取的方法。 此类方案包括：

1.  当从播放加密的音频文件。

2.  当播放较短的剪辑的一系列。

3.  音频流。


 <a name="AudioTrack_Class" />


### <a name="audiotrack-class"></a>AudioTrack 类

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)类录制，使用低级别的音频 Api 和低级别等效于`MediaPlayer`类。

<a name="Initializing_and_Playing" />

#### <a name="initializing-and-playing"></a>初始化和播放

若要播放音频的新实例`AudioTrack`必须实例化。 自变量列表传递给[构造函数](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist)指定如何播放音频缓冲区中包含的示例。 参数是：

1.  流类型&ndash;语音、 铃声、 音乐、 系统或警报。

2.  频率&ndash;以 Hz 表示的采样速率。

3.  通道配置&ndash;Mono 或立体声。

4.  音频格式&ndash;8 位或 16 位编码。

5.  缓冲区大小&ndash;以字节为单位。

6.  缓冲区模式&ndash;流式处理或静态。


构造之后,[播放](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/)方法`AudioTrack`调用，以将其设置为开始播放。 写入到的音频缓冲区`AudioTrack`开始播放：

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```

<a name="Pausing_and_Stopping_the_Playback" />

#### <a name="pausing-and-stopping-the-playback"></a>暂停和停止播放

调用[暂停](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/)方法来暂停播放：

```csharp
audioTrack.Pause();
```

调用[停止](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/)方法将永久终止播放：

```csharp
audioTrack.Stop();
```

<a name="Cleaning_up" />

#### <a name="cleanup"></a>清理

当`AudioTrack`是不再需要其必须释放资源通过调用[版本](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```

<a name="The_AudioRecord_Class" />

### <a name="the-audiorecord-class"></a>AudioRecord 类

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)类等同于`AudioTrack`录制一侧。 如`AudioTrack`，它使用内存缓冲区直接，来代替文件和 Uri。 它要求`RECORD_AUDIO`在清单中设置权限。

<a name="Initializing_and_Recording" />

#### <a name="initializing-and-recording"></a>初始化和记录

第一步是构造新[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)对象。 自变量列表传递给[构造函数](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist)提供录制所需的所有信息。 与不同在`AudioTrack`，其中的自变量是很大程度上是枚举中的等效参数`AudioRecord`均为整数。 这些方法包括：

1.  硬件输入的源如麦克风音频。

2.  流类型&ndash;语音、 铃声、 音乐、 系统或警报。

3.  频率&ndash;以 Hz 表示的采样速率。

4.  通道配置&ndash;Mono 或立体声。

5.  音频格式&ndash;8 位或 16 位编码。

6.  缓冲区大小中字节


一次`AudioRecord`构造时，其[StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/)调用方法。 它现在已准备好开始录制。 `AudioRecord`持续读取输入的音频缓冲区，并将出此输入到音频文件。

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```

<a name="Stopping_the_Recording" />

#### <a name="stopping-the-recording"></a>停止录制

调用[停止](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/)方法终止该录制：

```csharp
audRecorder.Stop();
```

<a name="Clean_Up" />

#### <a name="cleanup"></a>清理

当`AudioRecord`不再需要对象，调用其[版本](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/)方法释放与之关联的所有资源：

```csharp
audRecorder.Release();
```

<a name="Summary" />

## <a name="summary"></a>摘要

Android OS 提供用于播放、 记录和管理音频功能强大的框架。 本文介绍如何播放和记录音频，使用的高级`MediaPlayer`和`MediaRecorder`类。 接下来，它探讨了如何使用音频通知共享之间不同的应用程序设备的音频资源。 最后，它处理如何播放和记录音频，使用低级别 Api，该接口直接与内存缓冲区。


## <a name="related-links"></a>相关链接

- [使用与音频 （示例）](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [媒体播放器](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [媒体记录器](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [音频管理器](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [音频轨迹](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [音频记录器](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
