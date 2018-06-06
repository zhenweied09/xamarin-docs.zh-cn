---
title: 与 Xamarin 在 AVAudioPlayer tvOS 中播放声音
description: 这篇文章演示如何使用一个帮助器类来控制播放的声音在 Xamarin.iOS 应用程序中使用 AVAudioPlayer。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7d95a8ea6c22c0d897d8ccfe0c2ca401f6523783
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788624"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>与 Xamarin 在 AVAudioPlayer tvOS 中播放声音

## <a name="about-the-avaudioplayer"></a>有关 AVAudioPlayer

`AVAudioPlayer`从内存或文件用于播放音频数据。 Apple 建议使用此类播放音频应用程序中，除非你正在流式处理的网络，或者需要低延迟音频 I/O。

你可以使用`AVAudioPlayer`以执行以下操作：

- 播放声音的任何可选循环的持续时间。
- 可选同步同时播放多个声音。
- 控制卷、 播放速率和播放每个声音的立体声定位。
- 支持功能，如快速前进或后退。
- 获取播放级别计数数据。

`AVAudioPlayer` 提供的 iOS、 tvOS 和 OS X 如任何音频格式支持声音`.aif`，`.wav`或`.mp3`。

## <a name="playing-sounds-in-tvos"></a>在 tvOS 播放声音

因为 tvOS 支持相同的音频工具箱类，因为 iOS，请参阅我们的 iOS[与 AVAudioPlayer 播放声音](http://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/)Xamarin.tvOS 应用中的播放音频的完整详细信息的文档。



## <a name="related-links"></a>相关链接

- [AVAudioPlayer 引用](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
