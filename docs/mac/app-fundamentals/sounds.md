---
title: 播放声音与 AVAudioPlayer
description: 这篇文章演示如何使用一个帮助器类来控制播放的声音使用 AVAudioPlayer。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 25e3285a1da1b6a112629001d5412fdd0788c705
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>播放声音与 AVAudioPlayer

_这篇文章演示如何使用一个帮助器类来控制播放的声音使用 AVAudioPlayer。_

## <a name="about-the-avaudioplayer"></a>有关 AVAudioPlayer

`AVAudioPlayer`类用于播放音频数据从内存或文件。 Apple 建议使用此类播放音频应用程序中，除非你正在流式处理的网络，或者需要低延迟音频 I/O。

你可以使用`AVAudioPlayer`类来执行以下操作：

- 播放声音的任何可选循环的持续时间。
- 可选同步同时播放多个声音。
- 控制卷、 播放速率和播放每个声音的立体声定位。
- 支持功能，如快速前进或后退。
- 获取播放级别计数数据。

`AVAudioPlayer` 支持 iOS、 tvOS 以及如.aif、.wav 或.mp3 macOS 提供的任何音频格式的声音。

## <a name="playing-sounds-in-macos"></a>在 macOS 播放声音

因为 macOS 支持相同的音频工具箱类，因为 iOS，请参阅我们的 iOS[与 AVAudioPlayer 播放声音](https://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/)Xamarin.Mac 应用中的播放音频的完整详细信息的文档。



## <a name="related-links"></a>相关链接

- [AVAudioPlayer 引用](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
