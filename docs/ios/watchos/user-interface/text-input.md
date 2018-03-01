---
title: "使用文本输入"
ms.topic: article
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: b606773b44d32b724fe7f4dfa2ef18fe34b85cae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-text-input"></a>使用文本输入

Apple Watch 不提供用户输入文本的键盘，但是它支持一些监视友好的替代方案：

- 从预定义的文本选项列表中选择
- Siri 听写
- 选择 emoji
- Scribble （watchOS 3 中引入） 的按字母手写识别。

模拟器当前不支持听写但您仍然可以测试文本输入控制器的其他选项，如 Scribble，如下所示：

![](text-input-images/textinput-sml.png "测试 scribble 选项")

若要接受监视应用程序中的文本输入：

1. 创建预定义的选项的字符串的数组。
2. 调用`PresentTextInputController`与阵列，是否允许 emoji，，和`Action`用户完成时调用。
3. 在完成操作，测试输入结果并采取相应的操作 （可能设置标签的文本值） 的应用中。

下面的代码段向用户显示三个预定义的选项：

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

`WKTextInputMode`枚举具有三个值：

- 纯
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>纯

当设置的纯模式时，用户可以选择：

- 听写，
- Scribble，或
- 从应用程序提供的预定义列表。

[ ![](text-input-images/plain-scribble-sml.png "听写，Scribble，或从应用程序提供的预定义列表")](text-input-images/plain-scribble.png)

始终将结果返回为`NSObject`可以强制转换为`string`。

## <a name="emoji"></a>Emoji

有两种类型的 emoji:

- 正则 Unicode emoji
- 动画的映像

当用户选择 Unicode emoji 时，它是作为字符串返回。

如果选择动画的图像 emoji`result`中完成处理程序将包含`NSData`对象，其中包含 emoji `UIImage`。

## <a name="accepting-dictation-only"></a>仅接受听写

若要获得直接到听写屏幕用户而不显示任何建议 （或 Scribble 选项）：

- 传递的建议列表中，为一个空数组和
- Set `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

当用户来讲时，监视屏幕将显示下面的屏幕，其中包括文本公认 （例如，"This is a test"）：

![](text-input-images/dictation.png "如果用户来讲，监视屏幕将显示文本，为能够被理解")

一旦他们按**完成**按钮将返回的文本。



## <a name="related-links"></a>相关链接

- [Apple 的文本和标签的文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [简介 watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
