---
title: 入门 macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 85510168f37e724563eae59dfca438177b4feffe
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-macos"></a>入门 macOS

## <a name="what-you-will-need"></a>你将需要

* 按照说明进行操作中的[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

## <a name="hello-world"></a>Hello world

首先，生成 C# 中的简单你好 world 示例。

### <a name="create-c-sample"></a>创建 C# 示例

打开 Visual Studio for Mac，创建一个名为的新 Mac 类库项目**你好从 csharp**，并将其保存到 **~/Projects/hello-from-csharp**。

中的代码替换**MyClass.cs**文件替换为以下代码段：

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

生成项目。 生成的程序集将另存为 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>将绑定的托管程序集

托管程序集之后，将其绑定通过调用.NET 嵌入。

中所述[安装](~/tools/dotnet-embedding/get-started/install/install.md)指南中，这可以作为生成后步骤在你项目中，使用自定义 MSBuild 目标，或手动：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

框架将放入 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode 并创建新 Cocoa 应用程序。 将其命名为**你好从 csharp**和选择**Objective C**语言。

打开 **~/Projects/hello-from-csharp/output**目录中查找工具中，选择**你好从 csharp.framework**、 将其拖到 Xcode 项目并将其放正上方**你好从 csharp**项目中的文件夹。

![拖放框架](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

请确保**复制项，如果需要**签入弹出，对话框并单击**完成**。

![如果需要，复制项](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

选择**你好从 csharp**项目，然后导航到**你好从 csharp**目标的**常规**选项卡。在**嵌入二进制**部分中，添加**你好从 csharp.framework**。

![嵌入二进制文件](macos-images/hello-from-csharp-mac-embedded-binaries.png)

打开**ViewController.m**，并将内容替换为：

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

最后，运行 Xcode 项目中，并如下所示将显示：

![从 C# 示例在模拟器中运行 hello](macos-images/hello-from-csharp-mac.png)

更完整的更好看示例[此处提供了](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
