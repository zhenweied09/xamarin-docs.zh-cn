---
title: "程序集"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 509d4f465956d56e8efa5b69153764f5ae0949a9
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="assemblies"></a>程序集

## <a name="supported-assemblies"></a>支持的程序集

这是通过 Xamarin 支持为 Xamarin.tvOS 应用程序的程序集的列表。下面列出的这些详细信息列表。  一些值得注意的遗漏包括`System.EnterpriseServices`，ASP.NET 堆栈和 Windows.Forms。

|Assembly|已添加|API 兼容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|编译器编写器。|
|Mono.Data.Sqlite.dll|1.2|SQLite; 的 ADO.NET 提供程序请参阅[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 协议支持;用于[System.Data.SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/)内支持[System.Data](~/ios/data-cloud/system.data.md)。|
|Mono.Security.dll|1.0|加密 Api。|
|monotouch.dll|1.0|此程序集包含[C# 绑定到 CocoaTouch API](https://developer.xamarin.com/api/root/ios-unified/)。|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL 对象面向 Api，[扩展，使其提供 iPhone 设备支持](https://developer.xamarin.com/api/namespace/OpenGLES/)。|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)，[某些删除了功能](~/ios/data-cloud/system.data.md)。|
|System.Data.Service.Client.dll|3.x|完整的 oData 客户端。|
|System.Drawing|1.0|System.Drawing API-仅经典 API。<br />_System.Drawing 不支持在统一 API Xamarin.Mac.NET 4.5 或移动的框架。_|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)作为存在在堆栈[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)，加上以下命名空间中的类型： <ul><li>系统</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); 的一部分[System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支持。|
|System.Web.Services|1.1|[基本 Web 服务](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)从.NET 3.5 配置文件中的删除了服务器功能。|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可移植类库

除了 Mac 绑定中，可以使用 Xamarin.tvOS [.NET 可移植类库](~/cross-platform/app-fundamentals/pcl.md)。



## <a name="related-links"></a>相关链接

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
