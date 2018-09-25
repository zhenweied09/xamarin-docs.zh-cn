---
title: 字体
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/13/2018
ms.openlocfilehash: 086576ea7d806bb0768fbe4563df7fca99244ccb
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044816"
---
# <a name="fonts"></a>字体

## <a name="overview"></a>概述

从 API 级别 26 开始，Android SDK 允许字体以作为资源，就像布局一样处理或 drawables。 [Android 支持库 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1)将新字体 API 到这些目标 API 级别 14 或更高版本的应用程序的向后移植。

针对 API 26 或后安装 Android 支持库 v26，有两种方法可以在 Android 应用程序中使用字体：

1. **打包为 Android 资源字体**&ndash;这可确保字体始终对应用程序可用，但会增加 APK 的大小。 
2. **下载字体** &ndash; Android 还支持从作为字体下载_字体提供程序_。 字体提供程序检查字体是否已在设备上。 如有必要，则将下载字体，并将其设备上缓存。 可以在多个应用程序之间共享此字体。

相似的字体 （或可能有几种不同样式的字体） 可能分组到_字体系列_。 这使开发人员指定的字体，例如它的权重，某些属性，并且 Android 将自动选择合适的字体的字体系列。

Android 支持库 v26 将向后移植到 API 级别 26 字体支持。 如果目标较旧的 API 级别，就需要声明`app`XML 命名空间，并使用具有各种字体属性`android:`命名空间和`app:`命名空间。 如果仅`android:`使用命名空间，然后字体将不会运行 API 级别 25 或更少的显示的设备。 例如，此 XML 代码段中声明一个新[_字体系列_](#font_families)资源，在 API 级别 14 和更高版本中可用：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular" 
            android:fontStyle="normal" 
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular" 
            app:fontStyle="normal" 
            app:fontWeight="400" />

</font-family>
```

只要字体提供给 Android 应用程序中使用正确的方法，它们可以应用到 UI 小组件通过设置[`fontFamily`属性](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)。 例如，下面的代码段演示如何在 TextView 中显示一种字体：

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

本指南将首先讨论如何为 Android 的资源，使用字体，然后移动，讨论如何下载在运行时的字体。

## <a name="fonts-as-a-resource"></a>字体作为资源

将字体打包进 Android APK 确保它对应用程序始终可用。字体文件（或者是.TTF文件，或者是.OTF文件）像任何其他资源一样，被添加到 Xamarin.Android 应用程序中，通过将文件复制到 Xamarin.Android 项目的 **Resources** 文件夹中的子目录。字体资源保存在项目 **Resources** 文件夹的 **font** 子目录中。

> [!NOTE]
> 字体应有**生成操作**的**AndroidResource**或它们不会打包到最终 APK。 应该通过 IDE 自动将设置的生成操作。

有许多类似的字体文件 （例如，使用不同的权重或样式的相同字体） 时，可能将它们分组到字体系列。

<a name="font_families" />

### <a name="font-families"></a>字体系列

字体系列是一套有不同的权重和样式的字体。 例如，可能有不同的字体文件粗体或斜体字体。 由定义的字体系列`font`就会保留在 XML 文件中的元素**Resources/font**目录。 每个字体系列应具有其自己的 XML 文件。

若要创建字体系列，第一次添加到的所有字体**Resources/font**文件夹。 然后在字体系列的字体文件夹中创建新的 XML 文件。 XML 文件的名称所引用; 字体有没有相关性或关系资源文件可以是任何合法的 Android 资源文件名称。 此 XML 文件将包含一个根`font-family`包含一个或多个元素`font`元素。 每个`font`元素声明的字体特性。

以下 XML 是一种字体 Sources Sans Pro 的示例，定义多个不同的字体权重的字体。它作为文件，名为 **sourcesanspro.xml** ，保存在 **Resources/font** 文件夹下 ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular" 
          android:fontStyle="normal" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular" 
          app:fontStyle="normal" 
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold" 
          android:fontStyle="normal" 
          android:fontWeight="800" 
          app:font="@font/sourcesanspro_bold" 
          app:fontStyle="normal" 
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic" 
          android:fontStyle="italic" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic" 
          app:fontStyle="italic" 
          app:fontWeight="400" />
</font-family>
```

`fontStyle`属性具有两个可能值：

* **normal** &ndash;普通字体
* **italic** &ndash;倾斜字体

`fontWeight`特性都对应于 CSS`font-weight`属性，引用的字体粗细。 这是为 100-900 范围中的值。 以下列表描述常见的字体粗细值和其名称：

* **Thin** &ndash; 100
* **Extra Light** &ndash; 200
* **Light** &ndash; 300
* **Normal** &ndash; 400
* **Medium** &ndash; 500
* **Semi Bold** &ndash; 600
* **Bold** &ndash; 700
* **Extra Bold** &ndash; 800
* **Black** &ndash; 900

一旦定义了字体系列，它可以是以声明方式使用通过设置`fontFamily`， `textStyle`，和`fontWeight`布局文件中的属性。  例如 （正常） 400 权重字体和斜体文本样式，将设置以下 XML 代码段：

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>以编程方式指定字体

可以通过使用以编程方式设置字体[ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))方法来检索[ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html)对象。 许多视图具有`TypeFace`可以用于将字体分配给该小组件的属性。 此代码段演示如何以编程方式在 TextView 上设置的字体：

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont`方法将自动加载字体系列中的第一种字体。  若要加载匹配特定样式的字体，使用`Typeface.Create`方法。 此方法将尝试加载与匹配的指定的样式的字体。 例如，此代码段将尝试加载一个粗体`Typeface`对象中定义的字体系列从**Resources/font**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>下载字体

而不是为应用程序资源的打包字体，Android 可以从远程源下载字体。 这将产生减小 APK 大小的理想效果。 

字体下载的帮助下_字体提供程序_。 这是专用的内容提供程序管理的下载和缓存的设备上的所有应用程序的字体。 Android 8.0 包括字体提供程序以下载中的字体[Google 字体存储库](http://fonts.google.com)。 此默认字体的提供程序向后移植到与 Android 支持库 v26 的 API 级别 14。

当应用一种字体提出的请求时，将首先检查字体提供程序字体已在设备上。 否则，它将尝试下载该字体。 如果字体不能下载，然后 Android 将使用默认系统字体。 字体具有下载后，可供在设备上，而不仅仅是应用程序进行初始请求的所有应用程序。

当发出请求以下载一种字体时，应用程序不直接查询字体提供程序。 相反，应用将使用的实例[ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (或[ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)如果正在使用支持库 26)。  

Android 8.0 支持两种不同的方式下载字体：

1. **声明为资源的可下载字体**&ndash;应用程序可能声明可下载字体复制到 Android 通过 XML 资源文件。 这些文件将包含的所有元数据的 Android 需要以异步方式字体时下载应用程序启动并在设备上对它们进行缓存。
2. **以编程方式**&ndash;中 Android API 级别 26 Api 允许应用程序的应用程序运行时以编程方式，下载该字体。 应用程序将创建`FontRequest`对象给定字体，并将传递到此对象`FontsContract`类。 `FontsContract`采用`FontRequest`并检索从字体_字体提供程序_。 Android 以同步方式将下载该字体。 创建的示例`FontRequest`将在本指南后面所示。

无论使用哪种方法，可以下载必须添加到 Xamarin.Android 应用程序，然后字体的资源文件。 首先，必须在 XML 文件中声明所字体**Resources/font**字体系列的一部分的目录。 此代码段演示了如何下载中的字体[Google 字体打开源集合](https://fonts.google.com)使用的默认字体提供程序附带了 Android 8.0 （或支持库 v26）：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts" 
             android:fontProviderPackage="com.google.android.gms" 
             android:fontProviderQuery="VT323" 
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts" 
             app:fontProviderPackage="com.google.android.gms" 
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

`font-family`元素包含以下属性，声明 Android 需要下载字体的信息：

1. **fontProviderAuthority** &ndash;字体提供程序的用于请求的权限。
2. **fontPackage** &ndash;请求使用的字体提供程序的包。 这用于验证提供程序的标识。
3. **fontQuery** &ndash;这是将帮助定位所请求的字体的字体提供程序的字符串。 字体查询的详细信息是特定于字体提供程序。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类[可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用程序提供了一些有关查询格式对于字体 Google 字体打开源集合中。
4. **fontProviderCerts** &ndash;的集提供程序应使用签名的证书的哈希值列表的资源数组。

字体定义后，可能需要提供了一些信息_字体证书_涉及在下载。

### <a name="font-certificates"></a>字体证书

如果字体提供程序没有预装在设备上，或者使用应用程序`Xamarin.Android.Support.Compat`库，Android 需要字体提供程序的安全证书。 将保留在数组资源文件中列出这些证书**Resources/values**目录。 

例如，名为以下 XML **Resources/values/fonts_cert.xml** ，并将证书存储 Google 字体提供程序： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

这些资源中的文件位置，该应用是能够下载字体。

### <a name="declaring-downloadable-fonts-as-resources"></a>为资源声明可下载字体

通过列出中的可下载字体**AndroidManifest.XML**，应用程序首次启动时，Android 以异步方式将下载该字体。 字体的本身列示在数组资源文件中，类似于此： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

若要下载这些字体，它们必须在声明**AndroidManifest.XML**通过添加`meta-data`的子级`application`元素。 例如，如果在资源文件中声明的可下载字体**Resources/values/downloadable_fonts.xml**，则此代码段将需要添加到清单： 

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>下载与字体 Api 字体

可以通过实例化某字体，以编程方式下载[ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)对象并传递给`FontContractCompat.RequestFont`方法。 `FontContractCompat.RequestFont`方法将首先检查以查看在设备上，是否存在字体，然后如有必要将异步查询字体提供程序，然后重试下载应用程序的字体。 如果`FontRequest`是无法下载字体，则 Android 将使用默认系统字体。 

A`FontRequest`对象包含字体提供程序将使用查找和下载一种字体的信息。 A`FontRequest`需要四个条信息：

1. **字体提供程序颁发机构**&ndash;字体提供程序的用于请求的权限。
2. **字体包**&ndash;请求使用的字体提供程序的包。 这用于验证提供程序的标识。
3. **字体查询**&ndash;这是将帮助定位所请求的字体的字体提供程序的字符串。 字体查询的详细信息是特定于字体提供程序。 字符串的详细信息是特定于字体提供程序。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类[可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用程序提供了一些有关查询格式对于字体 Google 字体打开源集合中。
4. **字体提供程序证书**&ndash;的集提供程序应使用签名的证书的哈希值列表的资源数组。 

此代码段演示实例化一个新的`FontRequest`对象：

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

在上一个代码段`FontToDownload`是将帮助字体从 Google 字体开放源集合的查询。 

然后才传递`FontRequest`到`FontContractCompat.RequestFont`方法，必须创建的两个对象：

* **`FontsContractCompat.FontRequestCallback`** &ndash; 这是一个抽象类，后者必须扩展。 它是将回调时调用`RequestFont`完成。 一个 Xamarin.Android 应用程序必须子类`FontsContractCompat.FontRequestCallback`，并重写`OnTypefaceRequestFailed`和`OnTypefaceRetrieved`，提供下载失败，或者分别成功执行的操作。
* **`Handler`** &ndash; 这是`Handler`这将使用`RequestFont`下载上一个线程，字体，如有必要。 字体应**不**在 UI 线程上下载。

此代码片段是以异步方式将一种字体下载 Google 字体打开源集合中的 C# 类的一个示例。 它实现`FontRequestCallback`接口，并引发 C# 事件时`FontRequest`已完成。 

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };


    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

若要使用此帮助器，一个新`FontDownloadHelper`创建，并将其分配一个事件处理程序：  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) => 
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>总结

本指南讨论 Android 8.0 以支持可下载的字体和字体作为资源中的新 Api。 它讨论如何将现有字体嵌入 APK，以及如何在布局中使用它们。 它还讨论了如何 Android 8.0 支持从字体提供程序，下载字体以编程方式或通过声明资源文件中的字体元的数据。

## <a name="related-links"></a>相关链接

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Android 支持库 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [在 Android 中使用字体](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [CSS 字体权重规范](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Google 字体打开源集合](https://fonts.google.com/)
- [San Pro 的源](https://fonts.google.com/specimen/Source+Sans+Pro)
