---
title: "程序集"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 0a435e80173ca3ccb76dba0719ec2eea6eb72611
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="assemblies"></a>程序集

## <a name="supported-assemblies"></a>支持的程序集

这是通过 Xamarin 支持为 Xamarin.tvOS 应用程序的程序集的列表。下面列出的这些详细信息列表。  一些值得注意的遗漏包括`System.EnterpriseServices`，ASP.NET 堆栈和 Windows.Forms。

<table align="center" border="1" cellpadding="1" cellspacing="1" width="90%">
      <tbody>
        <tr>
          <td>
            <strong>程序集</strong>
          </td>
          <td>
            <strong>添加</strong>
          </td>
          <td>
            <strong>API 兼容性</strong>
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.CompilerServices.SymbolWriter.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td valign="top">
编译器编写器。
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.Data.Sqlite.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
SQLite; 的 ADO.NET 提供程序请参阅&nbsp;<a href="~/ios/data-cloud/system.data.md">限制</a>。
          </td>
        </tr>
        <tr>
          <td valign="top">
Mono.Data.Tds.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
TDS 协议支持;用于&nbsp;<a href="https://developer.xamarin.com/api/namespace/System.Data.SqlClient/" target="_blank">System.Data.SqlClient</a>&nbsp;内支持&nbsp;<a href="~/ios/data-cloud/system.data.md">System.Data</a>。
          </td>
        </tr>
        <tr>
          <td>
Mono.Security.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
加密 Api。
          </td>
        </tr>
        <tr>
          <td valign="top">
monotouch.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
此程序集包含&nbsp;<a href="https://developer.xamarin.com/api/root/ios-unified/" target="_blank">C# 绑定到 CocoaTouch API</a>。
          </td>
        </tr>
        <tr>
          <td valign="top">
mscorlib.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
OpenTK.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
OpenGL/OpenAL 对象面向 Api，&nbsp;<a href="https://developer.xamarin.com/api/namespace/OpenGLES/" target="_blank">扩展，使其提供 iPhone 设备支持</a>。
          </td>
        </tr>
        <tr>
          <td align="left" valign="top">
System.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <p><a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>，加上以下命名空间中的类型：</p>
    
            <ul>
              <li>System.Collections.Specialized</li>
              <li>System.ComponentModel</li>
              <li>System.ComponentModel.Design</li>
              <li>System.Diagnostics</li>
              <li>System.IO.Compression</li>
              <li>System.Net</li>
              <li>System.Net.Cache</li>
              <li>System.Net.Mail</li>
              <li>System.Net.Mime</li>
              <li>System.Net.NetworkInformation</li>
              <li>System.Net.Security</li>
              <li>System.Net.Sockets</li>
              <li>System.Security.Authentication</li>
              <li>System.Security.Cryptography</li>
              <li>System.Timers</li>
            </ul>
    
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Core.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Data.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>&nbsp;，&nbsp;<a href="~/ios/data-cloud/system.data.md">某些删除了功能</a>。
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Data.Service.Client.dll </td>
          <td align="center" valign="top">
3.x </td>
          <td>
完整的 oData 客户端。
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Drawing </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <p>System.Drawing API-仅经典 API。</p>
            <p><i>System.Drawing 不支持在统一 API Xamarin.Mac.NET 4.5 或移动的框架。</i></p>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Json.dll </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Runtime.Serialization.dll </td>
          <td align="center" valign="top">
?
          </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.ServiceModel.dll </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services" target="_blank">WCF</a>&nbsp;作为存在在堆栈&nbsp;<a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.ServiceModel.Web.dll </td>
          <td align="center" valign="top">
?
          </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/cc838194(VS.95).aspx" target="_blank">Silverlight</a>，加上以下命名空间中的类型： <p>&nbsp;</p>
    
            <ul>
              <li>系统</li>
              <li>System.ServiceModel.Channels</li>
              <li>System.ServiceModel.Description</li>
              <li>System.ServiceModel.Web</li>
            </ul>
    
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Transactions.dll </td>
          <td align="center" valign="top">
1.2 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>; 的一部分&nbsp;<a href="~/ios/data-cloud/system.data.md">System.Data</a>&nbsp;支持。
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Web.Services </td>
          <td align="center" valign="top">
1.1 </td>
          <td>
            <a href="http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services" target="_blank">基本 Web 服务</a>&nbsp;从.NET 3.5 配置文件中的删除了服务器功能。
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Xml.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a>
          </td>
        </tr>
        <tr>
          <td valign="top">
System.Xml.Linq.dll </td>
          <td align="center" valign="top">
1.0 </td>
          <td>
            <a href="http://msdn.microsoft.com/en-us/library/ms229335.aspx" target="_blank">.NET 3.5</a><br>
            <br>
&nbsp; </td>
        </tr>
      </tbody>
    </table>

<a name="Summary" />

## <a name="portable-class-libraries"></a>可移植类库

除了 Mac 绑定中，可以使用 Xamarin.tvOS [.NET 可移植类库](~/cross-platform/app-fundamentals/pcl.md)。



## <a name="related-links"></a>相关链接

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
