---
title: "使用 Android 资产"
ms.topic: article
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: bbf20155fe097f0229aa28c1f0d046cb3ef31a63
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-android-assets"></a>使用 Android 资产

_资产_提供一种方法在你的应用程序中包括如文本、 xml、 字体、 音乐和视频的任意文件。 如果你尝试以包括这些文件为"资源"，Android 将其进行处理到其资源系统和将无法获取原始数据。 如果你想要访问数据不变，资产是一种方法执行此操作。

添加到项目的资产将显示从你的应用程序使用可以读取的文件系统一样[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。
在此简单演示中我们将向我们项目中，添加文本文件资产读取它使用`AssetManager`，并将其显示在 TextView。

<a name="Add_Asset_to_Project" />

## <a name="add-asset-to-project"></a>将资产添加到项目

资产进入`Assets`项目文件夹。 将新的文本文件添加到此文件夹调用`read_asset.txt`。 在其中填充部分文本，如"我来自资产 ！"。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 应该将设置**生成操作**到此文件**AndroidAsset**:

![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

应该将设置适用于 Mac 的 visual Studio**生成操作**到此文件**AndroidAsset**:

[![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png)

-----

选择正确**BuildAction**可确保，将在编译时到 APK 中打包该文件。

<a name="Reading_Assets" />

## <a name="reading-assets"></a>读取资产

资产使用读取[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。 实例`AssetManager`可访问[资产](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/)属性`Android.Contet.Context`，如的活动。
在下面的代码中，我们打开我们**read_asset.txt**资产，读取内容，并将其使用 TextView 显示。

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

<a name="Running_the_Application" />

## <a name="running-the-application"></a>运行应用程序

运行应用程序和你应该会看到如下：

![示例屏幕快照](android-assets-images/screenshot.png)


## <a name="related-links"></a>相关链接

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
