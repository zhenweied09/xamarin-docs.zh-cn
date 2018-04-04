---
title: 使用 CCTextureCache 的纹理缓存
description: CocosSharp 的 CCTextureCache 类提供了一种标准的方式来组织，缓存中，和卸载内容。 它是非常适合大型游戏完全读入 RAM，简化的分组和释放的纹理的过程可能容纳不下。
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: f0a8121d5e90d2f39fb1b0cbd8d283253c2bf76d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="texture-caching-using-cctexturecache"></a>纹理缓存使用 CCTextureCache

_CocosSharp 的 CCTextureCache 类提供了一种标准的方式来组织，缓存中，和卸载内容。它是非常适合大型游戏完全读入 RAM，简化的分组和释放的纹理的过程可能容纳不下。_

`CCTextureCache`类是必不可少的组成部分 CocosSharp 游戏开发。 大多数 CocosSharp 游戏使用`CCTextureCache`对象，即使未显式尽可能多的 CocosSharp 方法内部使用*共享*纹理缓存。

本指南涵盖`CCTextureCache`和游戏开发非常重要的原因。 具体而言，本文涵盖：

 - 为什么纹理缓存问题
 - 纹理使用期限
 - 使用 SharedTextureCache
 - 延迟加载与使用 AddImage 预加载
 - 释放纹理


## <a name="why-texture-caching-matters"></a>为什么纹理缓存问题

纹理加载会耗时的操作，纹理需要占用大量的 RAM 在运行时，纹理缓存是游戏开发中的一个重要注意事项。

与任何文件操作中，从磁盘加载纹理可以是高昂的操作。 如果所加载的文件需要处理，如正在解压缩 （按原样 png 和 jpg 图像的大小写），纹理加载可能需要额外的时间。 纹理缓存可以减少应用程序必须从磁盘加载文件的次数。

如上所述，纹理还会占用大量的运行时内存。 例如背景图像的大小调整为 iPhone 6 (1344 x 750) 的分辨率将占用 4 兆字节为单位的 RAM – 即使 PNG 文件是仅几千字节的大小。 纹理缓存提供了一种共享在应用内的纹理引用方法以及不同的游戏状态之间转换时卸载所有内容的简单办法。


## <a name="texture-lifespan"></a>纹理使用期限

CocosSharp 纹理可能保存在内存中的整个长度的应用的执行，或者它们可能短生存期。 若要最小化内存使用情况应用应释放纹理时不再需要的类型。 当然，这意味着纹理可能释放和重新加载，更高版本时，这可以增加加载时间或在加载期间的性能会降低。 

纹理加载通常需要内存使用情况和负载时间之间的权衡 / 运行时性能。 使用少量的纹理内存的游戏可以根据需要内存中保留所有纹理，但更大的游戏可能需要卸载纹理以释放空间。

下图显示一个简单的游戏，后者根据需要加载纹理并将它们保留在内存中执行的整个长度为：

![](texture-cache-images/image1.png "此图显示了一个简单的游戏，后者根据需要加载纹理并将它们保留在内存中执行的整个长度为")

前两个条表示此程序在游戏的执行后立即所必需的纹理。 以下三个条形图表示每个级别，将根据需要加载的纹理。

如果游戏大型足够它最终将加载足够纹理填充提供的设备和操作系统的所有内存。 若要解决此问题，游戏可能不再需要时卸载纹理数据。 例如下, 图显示其卸载 Level1Texture，当它不再需要将加载的下一个级别 Level2Texture 游戏。 最终结果是在任何给定时间，内存中保存只有三个纹理： 

![](texture-cache-images/image2.png "最终结果是在任何给定时间，内存中保存只有三个纹理")


上面所示的图表指示纹理内存使用量可以减少通过卸载，但这可能需要其他的加载次数，如果玩家决定重播级别。 此外值得注意的是，加载和永远不会卸载 UITexture 和 MainCharacter 纹理。 这意味着，这些纹理所必需的所有级别，因此它们始终保留在内存中。 


## <a name="using-sharedtexturecache"></a>使用 SharedTextureCache

CocosSharp 自动缓存纹理时加载它们通过`CCSprite`构造函数。 例如以下代码仅创建一个纹理实例：


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp 自动缓存`star.png`纹理以避免创建大量的昂贵的替代方案相同`CCTexture2D`实例。 这通过实现`AddImage`调用上共享`CCTextureCache`具体实例`CCTextureCache.SharedTextureCache.Shared`。 若要了解如何`SharedTextureCache`使用我们可以看看下面的代码，这在功能上等同于调用`CCSprite`字符串参数构造函数：


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` 检查是否参数文件 (在这种情况下`star.png`) 已加载。 如果是这样，则返回的缓存的实例。 如果从文件系统中，然后不加载它并对纹理的引用的内部存储后续`AddImage`调用。 换而言之`star.png`映像仅加载一次，并且在后续调用需要没有更多的磁盘访问或其他纹理内存。


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>延迟加载与使用 AddImage 预加载

`AddImage` 允许编写相同的代码是否请求的纹理是否已加载。 将不加载内容，这意味着，直到需要它时;但是，这也会导致在运行时由于加载的内容不可预测的性能问题。

例如，考虑其中可以升级玩家的武器游戏。 升级时的武器和炮弹将明显改变，从而导致正在使用的新纹理。 如果内容是延迟加载然后与升级后的武器关联的纹理将不加载最初，但会在以后当玩家获取升级。 

此中旬玩游戏加载可能会导致到游戏*pop*，这是在执行了短暂而明显冻结。 若要防止此情况，代码可以预测可能需要的纹理，事先并预加载它们。 例如，以下可能用于预加载纹理：


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

此预加载可能导致浪费内存，且可能会增加启动时间。 例如，玩家可能永远不会实际获取电由`powerup3.png`纹理，因此将不必要地加载。 当然，这可能是必要的成本要支付以避免潜在 pop 中玩游戏，因此如果它将 RAM 中容纳不下，通常最好的办法预加载内容。


## <a name="disposing-textures"></a>释放纹理

如果游戏不需要为最小规格的设备上可用，则纹理不需要释放更多的纹理内存。 另一方面，较大游戏可能需要释放纹理内存，以便腾出空间供新内容。 例如游戏可能会使用大量的内存存储环境的纹理。 如果仅在特定级别中使用环境则它应卸载级别结束时。


### <a name="disposing-a-single-texture"></a>释放单个纹理

删除单个纹理首先需要调用`Dispose`方法，然后手动删除从`CCTextureCache`。

以下代码演示如何以及其纹理子后台画面中完全删除：


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

在处理大量小的纹理，但这可能会变得容易出错时处理的更大的纹理集时，直接释放纹理很有效。

纹理可以分组到自定义 （非共享）`CCTextureCache`实例，以简化纹理清理。

例如，考虑一个示例其中已预加载内容使用级别特定`CCTextureCache`实例。 `CCTextureCache`可能定义级别的类中定义实例 (这可能会`CCLayer`或`CCScene`):


```csharp
CCTextureCache levelTextures; 
```

`levelTextures`然后可以使用实例预加载特定级别的纹理： 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

最后级别结束时，纹理可以一次通过所有释放`CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Dispose 方法将释放所有内部纹理，清除这些纹理占用的内存。 组合`CCTextureCache.Shared`具有级别或游戏的模式的特定`CCTextureCache`实例会导致某些纹理保持通过整个游戏中，而另一些正在卸载模块如级别结束，类似于关系图显示在本指南的开头： 

![](texture-cache-images/image2.png "CCTextureCache.Shared 级别或游戏模式特定 CCTextureCache 实例将结果组合通过整个游戏中，而另一些正在卸载模块如级别结束，类似于关系图显示在本指南的开头保留一些纹理中")




## <a name="summary"></a>总结

本指南演示如何使用`CCTextureCache`类平衡内存使用情况和运行时性能。 `CCTexturCache.SharedTextureCache` 可以显式或隐式地使用来加载并缓存应用程序的整个生命周期的纹理，而`CCTextureCache`实例可以用于卸载纹理以减少内存使用量。

## <a name="related-links"></a>相关的链接

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
