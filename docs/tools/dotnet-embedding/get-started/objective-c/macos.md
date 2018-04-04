---
title: 入门 macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c129079aad14ac9e8aad6f73670ce9a43a36f222
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-macos"></a>入门 macOS


## <a name="what-you-will-need"></a>你将需要

* 按照说明进行操作，在我们[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

* 若要使用的.NET 程序集**Embeddinator 4000**。

* MacOS Cocoa 应用程序

您已按照中的说明进行操作后，请继续我们[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。 如果你已有的.NET 程序集则可以直接跳过**使用 Embeddinator-4000**部分。

## <a name="creating-a-net-assembly"></a>创建.NET 程序集

若要生成.NET 程序集将需要打开[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)并创建新**.NET 库项目**通过这样做，*文件 > 新解决方案 > 其他 >.NET > 库*. 单击下一步并授予*天气*作为*项目名称*，然后单击*创建*。

请按照下一步的步骤操作：

1. 删除**MyClass.cs**文件和**属性**文件夹。

2. 右键单击*天气项目 > 添加 > 新文件。*

3. 选择*空类*并用**XAMWeatherFetcher**作为名称，然后单击新建。

4. 内容替换*XAMWeatherFetcher.cs*替换为以下代码：

```csharp
using System;
using System.Json;
using System.Net;

public class XAMWeatherFetcher {

    static string urlTemplate = @"https://query.yahooapis.com/v1/public/yql?q=select%20item.condition%20from%20weather.forecast%20where%20woeid%20in%20(select%20woeid%20from%20geo.places(1)%20where%20text%3D%22{0}%2C%20{1}%22)&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys";
    public string City { get; private set; }
    public string State { get; private set; }

    public XAMWeatherFetcher (string city, string state)
    {
        City = city;
        State = state;
    }

    public XAMWeatherResult GetWeather ()
    {
        try {
            using (var wc = new WebClient ()) {
                var url = string.Format (urlTemplate, City, State);
                var str = wc.DownloadString (url);
                var json = JsonValue.Parse (str)["query"]["results"]["channel"]["item"]["condition"];
                var result = new XAMWeatherResult (json["temp"], json["text"]);
                return result;
            }
        }
        catch (Exception ex) {
            // Log some of the exception messages
            Console.WriteLine (ex.Message);
            Console.WriteLine (ex.InnerException?.Message);
            Console.WriteLine (ex.InnerException?.InnerException?.Message);

            return null;
        }

    }
}

public class XAMWeatherResult {
    public string Temp { get; private set; }
    public string Text { get; private set; }

    public XAMWeatherResult (string temp, string text)
    {
        Temp = temp;
        Text = text;
    }
}
```

你将注意到，`Using System.Json;`将会出错; 若要修复此，我们需要执行以下操作：

1. 双击**引用**文件夹。

2. 单击**包**选项卡。

3. 检查**System.Json**。

4. Click **Ok**.

完成上述操作后我们需要做是生成我们.NET 程序集通过单击*生成菜单 > 所有生成*或 ⌘ + b。 生成的成功消息应显示在顶部的状态栏。

右键单击在上*天气*项目节点，然后选择*在查找工具中显示*。 在查找工具中，转到*bin/Debug*文件夹; 内部它将查找**Weather.dll。**

## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

如果安装 Embeddinator 4000 使用我们 pkg 的安装程序并启动新的终端会话，在安装之后，你应该能够使用**objcgen**命令 (否则，你可以使用其绝对路径： `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`);**objcgen**是我们需要从.NET 程序集生成本机库的工具。

打开终端`cd`到包含 Weather.dll 的文件夹并执行**objcgen**与下面显示的自变量：

```shell
cd /Users/Alex/Projects/Weather/Weather/bin/Debug

objcgen --debug --outdir=output -c Weather.dll
```

你需要将被放入的所有内容**输出**目录下一步*Weather.dll*。 如果你有自己的.NET 程序集，将*Weather.dll*使用它，并按照上面的相同步骤。

## <a name="using-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode 并创建一个新**macOS Cocoa 应用程序**并将其命名**MyWeather**。 右键单击*MyWeather 项目节点*，选择*将文件添加到"MyWeather"*，导航到**输出**创建目录*Embeddinator 4000*，并添加以下文件：

* bindings.h
* embeddinator.h
* glib.h
* mono-support.h
* mono_embeddinator.h
* objc-support.h
* libWeather.dylib
* Weather.dll

请确保**复制项，如果需要**签入文件对话框的选项面板。

现在我们需要确保**libWeather.dylib**和**Weather.dll**进入应用程序捆绑包：

* 单击*MyWeather 项目节点*。
* 选择*生成阶段*选项卡。
* 添加新*复制文件阶段*。
* 上*目标*选择**框架**并添加**libWeather.dylib**。
* 添加新*复制文件阶段*。
* 上*目标*选择**可执行文件**，添加**Weather.dll**并确保*在副本上进行代码签名*已选中。

现在打开**ViewController.m**并将其与内容：

```objective-c
#import "ViewController.h"
#import "bindings.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    XAMWeatherFetcher * fetcher = [[XAMWeatherFetcher alloc] initWithCity:@"Boston" state:@"MA"];
    XAMWeatherResult * weather = [fetcher getWeather];

    NSString * result;
    if (weather)
        result = [NSString stringWithFormat:@"%@ °F - %@", weather.temp, weather.text];
    else
        result = @"An error occured";

    NSTextField * textField = [NSTextField labelWithString:result];
    [self.view addSubview:textField];
}

@end
```

最后运行 Xcode 项目中，并将显示类似如下内容：

![MyWeather 示例运行](macos-images/weather-from-csharp-macos.png)

更完整和更好地查找示例[此处](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
