---
title: 获取 Google 地图 API 密钥
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c37fce491b2e6f5e0211fcc6aa7906643a1bac2a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="obtaining-a-google-maps-api-key"></a>获取 Google 地图 API 密钥

若要在 Android 中使用 Google 映射功能，你需要注册与 Google 的地图 API 键。 之前执行此操作时，你只需将你的应用程序中看到而不是一个图的空白网格。 你必须获得 Google 地图 Android API v2 密钥-从较旧的 Google 地图 Android API 密钥 v1 的密钥将不可用。

获取地图 API v2 密钥涉及以下步骤：

1.  检索用于对应用程序进行签名的 keystore 的 sha-1 指纹。
2.  在 Google Api 控制台中创建一个项目。
3.  获取 API 密钥。


## <a name="obtaining-your-signing-key-fingerprint"></a>获取签名密钥指纹

若要从 Google 请求地图 API 密钥，你需要知道 keystore，用于对应用程序进行签名的 sha-1 指纹。
通常情况下，这意味着你将需要确定调试密钥库的 sha-1 指纹和 sha-1 然后 keystore，用于对你的应用程序版本进行签名的指纹。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

默认情况下，用于对应用程序可以是 Xamarin.Android 的调试版本进行签名的 keystore 找到以下位置：

**C:\\用户\\[USERNAME]\\AppData\\本地\\Xamarin\\适用于 Android 单声道\\debug.keystore**

可通过从 JDK 运行 `keytool` 命令来获取有关密钥存储的信息。 此工具通常 Java bin 目录中找到：

**C:\\Program Files (x86)\\Java\\jdk [VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

默认情况下，用于对应用程序可以是 Xamarin.Android 的调试版本进行签名的 keystore 找到以下位置：

**有关 Android/debug.keystore /Users/[USERNAME]/.local/share/Xamarin/Mono**

可通过从 JDK 运行 `keytool` 命令来获取有关密钥存储的信息。 此工具通常 Java bin 目录中找到：

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


运行 keytool 使用以下命令 （使用上面所示的文件路径）：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug.keystore 示例

默认调试键 （该列自动为你创建用于调试），使用此命令：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>生产密钥

在将应用部署到 Google Play，必须将其[使用私钥签名](~/android/deploy-test/signing/index.md)。
`keytool`将需要使用私有密钥的详细信息和用于创建生产 Google 地图 API 密钥生成 sha-1 指纹运行。 请记住更新**AndroidManifest.xml**与正确的 Google 地图 API 密钥，在部署前的文件。

### <a name="keytool-output"></a>Keytool 输出

你应看到以下在控制台窗口输出类似：

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

你将使用 sha-1 指纹 (列出后**SHA1**) 在本指南后面。


## <a name="creating-an-api-project"></a>创建 API 项目

检索到签名密钥库的 sha-1 指纹后，有必要在 Google Api 控制台中创建新的项目 （或向现有项目添加 Google 地图 Android API v2 服务）。

1. 在浏览器中，导航到[Google 开发人员控制台](https://console.developers.google.com/)： 单击**创建项目**:

   [![Google 开发人员控制台创建项目按钮](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 在**新项目**对话框中，输入项目名称。
   对话框将制造取决于你的项目名称，一个唯一项目 ID，如本示例中所示：

   [![新项目命名为 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 单击“创建”按钮。 在一分钟后操作，将创建项目并且你将转到**API Manager**页。 在**库**部分中，单击**Google 地图 Android API**:

   [![单击库部分中的 Google 地图 Android API](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 在顶部**Google 地图 Android API**页上，单击**启用**若要打开此项目的服务：

   [![单击仪表板部分中的启用按钮](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)


在此时创建 API 项目和 Google 地图 Android API v2 已添加到它。 但是，不能使用此 API 在你的项目中，直到为它创建凭据。 接下来我们将探讨如何创建 API 密钥和允许列表 Xamarin.Android 应用程序，以便其有权使用此密钥。


## <a name="obtaining-the-api-key"></a>获取 API 密钥

后**Google 开发人员控制台**API 项目已被创建，它是创建 Android API 密钥所需。 Xamarin.Android 应用程序必须具有 API 密钥，然后它们被授予到 Android 映射 API v2 的访问权限。

1. 在**Google 地图 Android API**显示的页 (单击后**启用**上一步中)，单击**转到凭据**按钮：

   [![此 API 已启用消息](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 在**凭据**页上，单击**我需要什么凭据？**按钮：

   [![将凭据添加到你的项目对话框](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 单击此按钮之后，将生成 API 密钥。 接下来是必要限制此密钥，从而使你的应用可以使用此密钥调用 Api。 单击**限制密钥**:

   [![单击凭据页上的限制的密钥](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 更改**名称**字段从**API 密钥 1**到将帮助你记住该密钥用于的名称 (**XamarinMapsDemoKey**此示例中使用)。 接下来，单击**Android 应用**单选按钮：

   [![选择凭据页上的 Android 应用](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 若要添加的 sha-1 指纹，请单击**+ 添加包名称和指纹**:

   [![单击添加包名称和指纹](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 输入你的应用包名称并且输入 sha-1 证书指纹 (通过获取`keytool`本指南中前面所述)。 在下面的示例中，包名称`XamarinMapsDemo`是输入后, 跟从获取 sha-1 证书指纹**debug.keystore**:

   [![输入的包名称是 com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 请注意，为了使你 APK 来访问 Google 地图，你必须包括 sha-1 指纹并打包你用于登录你 APK 每个密钥库 （调试和发布） 的名称。 例如，如果一台计算机用于调试和用于生成为发布 APK 的另一台计算机，则应包含从第一台计算机的调试密钥库的 sha-1 证书指纹和从的版本密钥库的 sha-1 证书指纹第二台计算机。 单击**+ 添加包名称和指纹**添加另一个的指纹和包名称，如本示例中所示：

   [![添加另一个指纹创建另一个 sha-1 证书](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 单击“保存”按钮保存更改。 接下来，你将返回到的 API 密钥的列表。 如果你有必须提前创建其他 API 密钥，它们还将此处列出。 在此示例中，只有一个 （在前面的步骤中创建） 的 API 密钥被列出：

   [![在 API 密钥列表中显示 XamarinMapsDemoKey](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)



## <a name="adding-the-key-to-your-project"></a>将项添加到你的项目

最后，此 API 将密钥添加到**AndroidManifest.XML** Xamarin.Android 应用程序文件。 在下面的示例中，`YOUR_API_KEY`是替换为前面步骤中生成的 API 密钥：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...

  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```


## <a name="related-links"></a>相关链接

- [Google Api 控制台](https://code.google.com/apis/console/)
- [Google 地图 API 密钥](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
