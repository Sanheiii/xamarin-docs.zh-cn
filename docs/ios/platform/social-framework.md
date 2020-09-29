---
title: Xamarin 中的社交框架
description: 社交框架提供了一个统一的 API，用于与中国的用户（包括 Twitter 和 Facebook）以及 SinaWeibo 的社交网络交互。
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 0c5f2a5c6a3274b298d3de216a2a0f42ed590611
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436483"
---
# <a name="social-framework-in-xamarinios"></a>Xamarin 中的社交框架

_社交框架提供了一个统一的 API，用于与中国的用户（包括 Twitter 和 Facebook）以及 SinaWeibo 的社交网络交互。_

使用社交框架，应用程序可以从单个 API 与社交网络交互，而无需管理身份验证。 它包括系统提供的用于撰写帖子的视图控制器，以及允许通过 HTTP 使用各个社交网络 API 的抽象。

## <a name="connecting-to-twitter"></a>连接到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帐户设置

若要使用社交框架连接 Twitter，需要在设备设置中配置帐户，如下所示：

 [![Twitter 帐户设置](social-framework-images/twitter01.png)](social-framework-images/twitter01.png#lightbox)

使用 Twitter 输入和验证帐户后，设备上使用社交框架类访问 Twitter 的任何应用程序都将使用此帐户。

### <a name="sending-tweets"></a>发送推文

社交框架包含一个名为 `SLComposeViewController` 的控制器，该控制器提供用于编辑和发送推文的系统提供视图。 以下屏幕截图显示了此视图的一个示例：

 [![此屏幕截图显示了 SLComposeViewController 的示例](social-framework-images/twitter02.png)](social-framework-images/twitter02.png#lightbox)

若要将用于 `SLComposeViewController` Twitter，必须通过调用方法来创建控制器实例，如下 `FromService` `SLServiceType.Twitter` 所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

`SLComposeViewController`返回实例后，可以使用它来呈现 UI，以发布到 Twitter。 但首先要做的事情就是通过调用以下方法来检查社交网络（在本例中为 Twitter）的可用性 `IsAvailable` ：

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 从不直接发送推文，无需用户交互。 但是，可以通过以下方法对其进行初始化：

- `SetInitialText` –添加要在推文中显示的初始文本。
- `AddUrl` –将 Url 添加到推文。
- `AddImage` –将图像添加到推文。

初始化后，调用会 `PresentVIewController` 显示由创建的视图 `SLComposeViewController` 。 然后，用户可以选择编辑并发送推文，或取消发送。 无论是哪种情况，控制器都应该在中解除 `CompletionHandler` ，还可以检查结果，以查看推文是已发送还是已取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文示例

下面的代码演示如何使用 `SLComposeViewController` 来显示用于发送推文的视图：

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

社交框架还包括对向社交网络发出 HTTP 请求的支持。 它将请求封装到 `SLRequest` 用于面向特定社交网络 API 的类中。

例如，以下代码通过扩展) 上给出的代码，向 Twitter 发出请求以获取公共时间线 (：

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

让我们详细了解一下此代码。 首先，它获得帐户存储的访问权限，并获取 Twitter 帐户的类型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下来，如果你的应用程序可以访问其 Twitter 帐户，则会询问用户，如果已授予访问权限，则该帐户将加载到内存中并更新 UI：

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

当用户通过点击 UI) 中的按钮 (请求时间线数据时，应用首先会形成从 Twitter 访问数据的请求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

此示例通过在 URL 中包含来将返回的结果限制为最后10个条目 `?count=10` 。 最后，它会将请求附加到) 上面加载的 Twitter 帐户 (，并对 Twitter 执行调用以提取数据：

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

如果已成功加载数据，原始 JSON 数据将显示 (如下面的示例输出中所示) ：

[![原始 JSON 数据显示的示例](social-framework-images/twitter03.png)](social-framework-images/twitter03.png#lightbox)

在实际应用中，可以将 JSON 结果分析为正常，并向用户显示结果。 请参阅 [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md) ，了解有关如何分析 JSON 的信息。

## <a name="connecting-to-facebook"></a>连接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帐户设置

使用社交框架连接到 Facebook 几乎与用于 Twitter 的过程完全相同。 必须在设备设置中配置 Facebook 用户帐户，如下所示：

[![Facebook 帐户设置](social-framework-images/facebook01.png)](social-framework-images/facebook01.png#lightbox)

配置后，设备上使用社交框架的任何应用程序都将使用此帐户连接到 Facebook。

### <a name="posting-to-facebook"></a>发布到 Facebook

由于社交框架是设计用于访问多个社交网络的统一 API，因此无论使用何种社交网络，代码几乎都是相同的。

例如， `SLComposeViewController` 可以完全像之前所示的 Twitter 示例中一样使用，唯一不同的是切换到特定于 Facebook 的设置和选项。 例如：

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

与 Facebook 一起使用时， `SLComposeViewController` 会显示一个视图，该视图与 Twitter 示例大致相同，在这种情况下显示 **Facebook** 为标题：

[![SLComposeViewController 显示](social-framework-images/facebook02.png)](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>调用 Facebook 图形 API

类似于 Twitter 示例，社交框架的 `SLRequest` 对象可与 Facebook 的 GRAPH API 一起使用。 例如，以下代码通过扩展) 上给出的代码，从 graph API 返回有关 Xamarin 帐户 (的信息：

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

此代码与上面提供的 Twitter 版本之间唯一的区别在于 Facebook 需要获取开发人员/应用特定 ID (可以从 Facebook 的开发人员门户生成) 该 ID 在发出请求时必须设置为一个选项：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

如果未设置此选项 (或使用无效的密钥) 会导致错误或不返回数据。

## <a name="summary"></a>总结

本文演示了如何使用社交框架与 Twitter 和 Facebook 交互。 其中显示了在何处为设备设置中的每个社交网络配置帐户。 还介绍了如何使用 `SLComposeViewController` 来显示用于发布到社交网络的统一视图。 此外，它还检查了 `SLRequest` 用于调用每个社交网络 API 的类。

## <a name="related-links"></a>相关链接

- [SocialFrameworkDemo (示例) ](/samples/xamarin/ios-samples/socialframeworkdemo)
- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)