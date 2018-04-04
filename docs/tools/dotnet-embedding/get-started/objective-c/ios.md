---
title: IOS 入门
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 34afdd9e91ebfbe7ad57c7eec6ba7f05fff1a2aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-ios"></a>IOS 入门


## <a name="requirements"></a>要求

除了从要求我们[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南还需要：

* [Xamarin.iOS 10.11](https://www.visualstudio.com/xamarin/)或更高版本

## <a name="hello-world"></a>Hello world

第一个让我们生成 C# 中的简单你好 world 示例。

### <a name="create-c-sample"></a>创建 C# 示例

打开 Visual Studio for Mac，创建新的 iOS 类库项目，将其命名为`hello-from-csharp`，并将其保存到`~/Projects/hello-from-csharp`。

中的代码替换`MyClass.cs`文件替换为以下代码段：

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

生成项目，生成的程序集将另存为`~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll`。

### <a name="bind-the-managed-assembly"></a>将绑定的托管程序集

运行 embeddinator 为的托管程序集创建本机框架：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

框架将放入`~/Projects/hello-from-csharp/output/hello-from-csharp.framework`。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode 并创建新的 iOS 单视图应用程序，将其命名为`hello-from-csharp`和选择**Objective C**语言。

打开`~/Projects/hello-from-csharp/output`目录中查找工具中，选择`hello-from-csharp.framework`、 将其拖到 Xcode 项目并将其放正上方`hello-from-csharp`项目中的文件夹。

![拖放 framework]Images/hello-from-csharp-ios-drag-drop-framework.png)

请确保`Copy items if needed`签入弹出，对话框并单击`Finish`。

![如果需要，复制项](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

选择`hello-from-csharp`项目，然后导航到`hello-from-csharp`目标的**常规选项卡**。在**嵌入二进制**部分中，添加`hello-from-csharp.framework`。

![嵌入二进制文件](ios-images/hello-from-csharp-ios-embedded-binaries.png)

打开 ViewController.m 中，并将内容替换为：

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

最后运行 Xcode 项目中，并将显示类似如下内容：

![从 C# 示例在模拟器中运行 hello](ios-images/hello-from-csharp-ios.png)
