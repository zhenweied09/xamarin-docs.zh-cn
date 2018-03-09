---
title: "在 Android 应用程序链接"
description: "本指南将讨论如何 Android 6.0 支持应用程序的链接，该技术允许移动应用以响应在网站上的 Url。 它将讨论什么应用链接是一种、 如何实现应用程序链接，在 Android 6.0 应用程序，以及如何配置网站以向移动应用程序域授予权限。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DDE54082-6E2B-9ED9-05FB-D9C1D1B1258E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 726890e48407dd26f52c5aeaecf4eab51dcc5182
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="app-linking-in-android"></a>在 Android 应用程序链接

_本指南将讨论如何 Android 6.0 支持应用程序的链接，该技术允许移动应用以响应在网站上的 Url。它将讨论什么应用链接是一种、 如何实现应用程序链接，在 Android 6.0 应用程序，以及如何配置网站以向移动应用程序域授予权限。_

## <a name="app-linking-overview"></a>应用程序链接的概述

移动应用程序不再位于接收器&ndash;在许多情况下它们是他们的企业，以及其网站的重要组件。 最好让企业无缝地将其网络影响力和移动应用程序，连接与启动移动应用程序和移动应用程序中显示相关内容的网站上的链接。 *应用程序链接*(也称为*深层链接*) 是一个允许移动设备以响应一个 URI，然后启动的移动应用程序与该 URI 相对应的技术。

Android 处理通过应用链接*意向系统*&ndash;移动浏览器时在移动浏览器中的链接上单击用户，将分发 Android 将委托给已注册的应用程序意向。 例如，单击烹饪网站上的链接将打开该网站与关联的移动应用并向用户显示特定原因之一。 如果有多个应用程序注册用于处理该意向，则 Android 将引发了称为*消除歧义对话框*，将要求用户选择的应用程序应为处理的意图，哪些应用程序示例：

![消除歧义对话框的示例屏幕快照](app-linking-images/01-disambiguation-dialog.png)

通过使用自动链接处理情况下，android 6.0 提高此状态。 适用于 Android 的 uri，用作默认处理程序自动注册应用程序可以&ndash;应用程序将自动启动并直接导航到相关活动。 Android 6.0 决定如何处理 URI 单击依赖于以下条件：

1. **现有应用程序已与 URI 关联**&ndash;用户可能已经关联现有应用程序 URI。 在这种情况下，Android 将继续使用该应用程序。
2. **任何现有的应用程序使用的 URI、 相关联，但支持的应用程序安装**&ndash;在此方案中，用户未指定现有应用程序，因此 Android 将使用已安装支持应用程序来处理该请求。
3. **任何现有的应用程序与 URI，但许多支持的应用程序会安装**&ndash;由于有多个应用程序支持的 URI，将显示错误对话框中，用户必须选择哪些应用将处理 URI。

如果用户具有没有安装的应用支持 URI，并且随后安装一个，然后 Android 将会设置该应用程序的默认处理程序 URI 后验证与 URI 相关联的网站之间的关联。

本指南将讨论如何配置 Android 6.0 应用程序以及如何创建和发布该数字资产链接文件，以支持 Android 6.0 中的应用程序链接。

## <a name="requirements"></a>惠?

本指南需要 Xamarin.Android 6.1 和面向 Android 6.0 （API 级别 23） 的应用程序或更高版本。

应用程序链接是早期版本的 Android 中可能使用[铆钉 NuGet 包](https://www.nuget.org/packages/Rivets/)从 Xamarin 组件应用商店。 铆钉程序包不兼容与 Android 6.0; 中的应用程序链接它不支持 Android 6.0 应用链接。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中配置应用程序链接

设置 Android 6.0 中的应用程序链接涉及两个主要步骤：

1. **添加一个或多个意向-筛选器的网站 URI 的**&ndash;意向的筛选器指导如何处理在移动浏览器 URL，请单击 Android。
2. **发布*数字资产链接 JSON*网站上的文件**&ndash;这是一个文件上载到网站和 Android 用于验证移动应用程序和网站的域之间的关系。 如果没有此，Android 能安装应用程序作为默认处理 uri;用户必须手动执行此操作。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>配置意向的筛选器

它是配置将从网站的 URI （或可能一组 Uri） 映射到 Android 应用程序中的活动的意向筛选器所必需的。 在 Xamarin.Android，此关系通过装饰的活动建立[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 意向的筛选器必须声明以下信息：

* **`Intent.ActionView`** &ndash; 此操作将注册意向的筛选器，以响应请求，查看信息
* **`Categories`** &ndash;  意向的筛选器应注册同时**[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)**和**[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)**为了能够正确处理 web URI。
* **`DataScheme`** &ndash; 意向的筛选器必须声明`http`和/或`https`。 这些是仅有两个有效的方案。
* **`DataHost`** &ndash; 这是 Uri 将来自的域。
* **`DataPathPrefix`** &ndash; 这是一个可选路径对网站上的资源。
* **`AutoVerify`** &ndash; `autoVerify`属性告知 Android 以验证应用程序和网站之间的关系。 这将下面详细讨论。

下面的示例演示如何使用[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)来处理来自链接`https://www.recipe-app.com/recipes`和从`http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe"),
              AutoVerify=true]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android 将验证由针对网站上的数字资产文件的意向筛选器之前应用程序的默认处理程序的注册的 uri，标识每个主机。 Android 可以建立应用程序的默认处理程序之前，所有意向的筛选器必须通过验证。

### <a name="creating-the-digital-assets-link-file"></a>创建数字资产链接文件

应用程序链接的 android 6.0 需要 Android 验证在对 uri 设置的默认处理程序的应用程序前应用程序和网站之间的关联。 首次安装应用程序时，将发生此验证。 *数字资产链接*文件是由相关 webdomain(s) 承载的 JSON 文件。

> [!NOTE]
> **注意：** `android:autoVerify`属性必须设置意向的筛选器&ndash;否则 Android 将不会执行验证。

该文件位于域中的位置的网络管理员通过**https://domain/.well-known/assetlinks.json**。

该数字的资产文件包含元数据所必需的 Android 验证关联。 **Assetlinks.json**文件具有以下键 / 值对：

* `namespace` &ndash; Android 应用程序的命名空间。
* `package_name` &ndash; （在应用程序清单中声明） 的 Android 应用程序的包名称。
* `sha256_cert_fingerprints` &ndash; 签名的应用程序的 SHA256 指纹。 请参阅指南[查找密钥库的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)有关如何获取应用程序的 SHA1 指纹的详细信息。

下面的代码段演示了**assetlinks.json**与单个应用程序列出：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

它可以注册多个 SHA256 指纹，以支持不同版本，或生成你的应用程序。 此下一步**assetlinks.json**文件是注册多个应用程序的一个示例：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

[Google 数字资产链接网站](https://developers.google.com/digital-asset-links/tools/generator)具有一个在线工具，可帮助创建和测试的数字资产文件。

### <a name="testing-app-links"></a>测试应用程序链接

实现应用程序链接后, 的各种不同块应进行测试，以确保它们按预期方式工作。

就可以确认数字资产文件正确格式并托管通过使用 Google 的数字资产链接 API，此示例中所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

有两个测试，可以执行以确保已正确配置意向的筛选器和的 uri，该应用程序设置的默认处理程序：

1.  数字资产文件正确托管上文所述。 第一个测试将发送其 Android 应重定向到移动应用程序意向。 Android 应用程序应启动并显示为 URL 注册活动。 在命令提示符下键入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  显示处理在给定设备上安装的应用程序的策略的现有链接。 以下命令将转储链接策略使用以下信息设备上的每个用户的列表。 在命令提示符处，键入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; 应用程序的包名称。
    * **`Domain`** &ndash; 应用程序将处理其 web 链接域 （由空格分隔）
    * **`Status`** &ndash; 这是应用程序的当前链接处理状态。 值为**始终**意味着应用程序具有`android:autoVerify=true`声明，并且已通过系统验证。 它后跟表示 Android 系统的记录的首选项的十六进制数字。

    例如:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>摘要

本指南讨论如何将应用链接在 Android 6.0 中的工作。 它然后介绍如何配置 Android 6.0 应用程序支持和响应的应用程序的链接。 它还讨论了如何测试应用程序将 Android 应用程序中的链接。


## <a name="related-links"></a>相关链接

- [查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)
- [活动和意向](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google 数字资产链接](https://developers.google.com/digital-asset-links/)
- [语句列表生成器和测试人员](https://developers.google.com/digital-asset-links/tools/generator)
