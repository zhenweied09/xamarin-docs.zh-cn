---
title: IOS 入门
description: 本文档介绍如何开始使用.NET 嵌入 iOS。 它讨论要求，并显示一个示例应用来演示如何将绑定的托管程序集并在 Xcode 项目中使用的输出。
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3da76e650281cbf7320b88fa9c76948865d7a2fb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793556"
---
# <a name="getting-started-with-ios"></a>IOS 入门

## <a name="requirements"></a>要求

除了从要求我们[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南中，你还需要：

* [Xamarin.iOS 10.11](https://www.visualstudio.com/xamarin/)或更高版本

## <a name="hello-world"></a>Hello world

首先，生成 C# 中的简单你好 world 示例。

### <a name="create-c-sample"></a>创建 C# 示例

打开 Visual Studio for Mac，创建新的 iOS 类库项目，将其命名为**你好从 csharp**，并将其保存到 **~/Projects/hello-from-csharp**。

中的代码替换**MyClass.cs**文件替换为以下代码段：

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

生成项目，并生成的程序集将另存为 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>将绑定的托管程序集

托管程序集之后，将其绑定通过调用.NET 嵌入。

中所述[安装](~/tools/dotnet-embedding/get-started/install/install.md)指南中，这可以作为生成后步骤在你项目中，使用自定义 MSBuild 目标，或手动：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

框架将放入 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode，创建新的 iOS 单视图应用程序，将其命名为**你好从 csharp**，然后选择**Objective C**语言。

打开 **~/Projects/hello-from-csharp/output**目录中查找工具中，选择**你好从 csharp.framework**、 将其拖到 Xcode 项目并将其放正上方**你好从 csharp**项目中的文件夹。

![拖放 framework]Images/hello-from-csharp-ios-drag-drop-framework.png)

请确保**复制项，如果需要**签入弹出，对话框并单击**完成**。

![如果需要，复制项](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

选择**你好从 csharp**项目，然后导航到**你好从 csharp**目标的**常规选项卡**。在**嵌入二进制**部分中，添加**你好从 csharp.framework**。

![嵌入二进制文件](ios-images/hello-from-csharp-ios-embedded-binaries.png)

打开**ViewController.m**，并将内容替换为：

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

.NET 嵌入当前不支持 bitcode 在 iOS 中，它启用一些 Xcode 项目模板。 

在项目设置中禁用它：

![Bitcode 选项](../../images/ios-bitcode-option.png)

最后，运行 Xcode 项目中，并如下所示将显示：

![从 C# 示例在模拟器中运行 hello](ios-images/hello-from-csharp-ios.png)
