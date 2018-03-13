---
title: "社交 Framework"
description: "社交框架提供一个统一的 API 的用户在中国包括 Twitter 和 Facebook，以及 SinaWeibo 的社交网络进行交互。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 9892577d7e0ed3d3f622f881cc51db09eb44a8fd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="social-framework"></a>社交 Framework

_社交框架提供一个统一的 API 的用户在中国包括 Twitter 和 Facebook，以及 SinaWeibo 的社交网络进行交互。_


使用社交框架允许应用程序而无需管理身份验证与社交网络从单个 API 进行交互。 它包括提供用于编写文章，以及允许通过 HTTP 使用每个社交网络 API 的抽象视图控制器的系统。

> [!IMPORTANT]
> **注意：**连接到各种社交网络的跨平台 API，请参阅[Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) Xamarin 组件应用商店中为组件。

## <a name="connecting-to-twitter"></a>连接到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帐户设置

若要连接到 Twitter 使用社交框架，帐户需要在设备设置中配置，如下所示：

 [![](social-framework-images/twitter01.png "Twitter 帐户设置")](social-framework-images/twitter01.png#lightbox)

输入并使用 Twitter 验证帐户后，设备用于访问 Twitter 的社交 Framework 类上的任何应用程序将使用此帐户。

### <a name="sending-tweets"></a>发送推文

社交 Framework 包括调用的控制器`SLComposeViewController`，提供了用于编辑和发送推文的系统提供视图。 以下屏幕截图显示此视图的一个示例：

 [![](social-framework-images/twitter02.png "此屏幕截图显示了 SLComposeViewController 的示例")](social-framework-images/twitter02.png#lightbox)

若要使用`SLComposeViewController`与 Twitter 的控制器实例必须创建通过调用`FromService`方法替换`SLServiceType.Twitter`如下所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

后`SLComposeViewController`返回实例，所以可以用来显示用于发布到 Twitter 的 UI。 但是，首先要做是通过调用在此情况下，检查的社交网络 Twitter 可用性`IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 永远不会发送推文直接无需用户交互。 但是，它可以使用以下方法初始化：

-   `SetInitialText` – 将添加要显示推文中的初始文本。 
-  `AddUrl` – 将 Url 添加到推文。
-  `AddImage` – 将图像添加到推文。


初始化后，调用`PresentVIewController`显示创建的视图`SLComposeViewController`。 用户可以然后根据需要编辑并发送推文，或取消发送它。 在任一情况下，应在关闭控制器`CompletionHandler`，其中还可以检查结果以查看推文已发送，或者取消时，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文示例

下面的代码演示如何使用`SLComposeViewController`提供用于发送推文的视图：

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>调用 Twitter API

社交 Framework 还包括对社交网络进行 HTTP 请求的支持。 它封装中的请求`SLRequest`用于面向特定的社交网络的 API 的类。

例如，下面的代码向 Twitter 若要获取公共时间线 （通过在详述上面给出的代码） 发出请求：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

让我们看一下此代码中详细信息。 首先，它可以访问帐户存储，并获取 Twitter 帐户的类型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下来，是否你的应用程序可以有权访问他们的 Twitter 帐户并且，如果授予访问权限，该帐户加载到内存和更新用户界面要求用户：

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

当用户请求的时间线数据 （通过轻按在 UI 中的按钮） 时，则应用程序首先窗体从 Twitter 访问数据的请求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
此示例是将返回的结果限制为 10 个最新条目通过包括`?count=10`在 URL 中。 最后，它将请求附加到的 Twitter 帐户 （已加载上面） 并执行调用 Twitter 以提取数据：

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

如果成功加载了数据，原始的 JSON 数据将显示 （如下面的示例输出中）：

[![](social-framework-images/twitter03.png "下面举例说明原始的 JSON 数据显示")](social-framework-images/twitter03.png#lightbox)

在实际应用中，JSON 结果然后无法分析为 normal 和向用户显示的结果。 请参阅[简介 Web 服务](~/cross-platform/data-cloud/web-services/index.md)有关如何分析 JSON 的信息。

## <a name="connecting-to-facebook"></a>连接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帐户设置

连接到 Facebook 社交 framework 操作几乎与用于 Twitter 上面所示的过程相同。 Facebook 用户帐户必须在设备设置中配置，如下所示：

[![](social-framework-images/facebook01.png "Facebook 帐户设置")](social-framework-images/facebook01.png#lightbox)

配置后，在使用社交框架的设备上的任何应用程序将使用此帐户连接到 Facebook。

### <a name="posting-to-facebook"></a>发布到 Facebook

由于社交 Framework 是一个统一的 API，为访问多个社交网络而设计，代码将保留而不考虑所使用的社交网络几乎完全相同。

例如，`SLComposeViewController`可以使用方式与更早版本，显示仅不同切换到特定 Facebook 的设置和选项的 Twitter 示例完全相同。 例如:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

与 Facebook 一起使用时`SLComposeViewController`显示查找到 Twitter 示例中，显示几乎完全相同的视图**Facebook**为在此情况下标题：

[![](social-framework-images/facebook02.png "SLComposeViewController 显示")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>调用 Facebook Graph API

类似于 Twitter 的示例，社交 Framework 的`SLRequest`对象可以用于 Facebook 的 graph API。 例如，以下代码将返回信息从 graph API 的 Xamarin 帐户有关 （由在详述上面给出的代码）：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

此代码，上面介绍的 Twitter 版本之间唯一的真正的区别是 Facebook 的要求，若要获取开发人员/应用程序特定 ID (可以从 Facebook 开发人员门户中生成） 发出请求时必须将其设置为一个选项：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

未能设置此选项 （或使用无效的键） 将导致错误或返回任何数据。

## <a name="summary"></a>摘要

本文介绍了如何使用社交 Framework 与 Twitter 和 Facebook 进行交互。 它介绍了在设备设置中配置为每个社交网络的帐户的位置。 它还讨论了如何使用`SLComposeViewController`提供发布到社交网络的统一的视图。 此外，它会检查`SLRequest`用于调用每个社交网络 API 的类。


## <a name="related-links"></a>相关链接

- [SocialFrameworkDemo (sample)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
