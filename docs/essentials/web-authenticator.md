---
title: Xamarin.Essentials：Web 验证器
description: 本文档介绍 Xamarin.Essentials 中的 WebAuthenticator 类，使用此类可以启动基于浏览器的身份验证流，以侦听对应用的回调。
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c4437f05eddd6885f88fc57ddc108f4fc9f4376d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433532"
---
# <a name="no-locxamarinessentials-web-authenticator"></a>Xamarin.Essentials：Web 验证器

使用 WebAuthenticator 类可以启动基于浏览器的流，以侦听对注册到应用的特定 URL 的回调。

## <a name="overview"></a>概述

许多应用需要添加用户身份验证，这通常表示用户能够登录到其现有 Microsoft、Facebook、Google 帐户，以及现在的 Apple 登录帐户。

[Microsoft 身份验证库 (MSAL)](/azure/active-directory/develop/msal-overview) 提供了出色的统包解决方案，用于向应用添加身份验证。 客户端 NuGet 包甚至还支持 Xamarin 应用。

如果你有兴趣使用自己的 Web 服务进行身份验证，可以使用 WebAuthenticator 来实现客户端功能。

## <a name="why-use-a-server-back-end"></a>为什么使用服务器后端？

许多验证提供程序已转向仅提供显式或两步身份验证流，以确保获得更好的安全性。 这意味着，你将需要提供程序提供的“客户端密码”才能完成身份验证流。 遗憾的是，要存储机密和存储在移动应用代码中的任何内容、二进制文件，移动应用并不是理想的存储位置，这样做通常被视为不安全。

在这种情况下，最佳做法是将 Web 后端用作移动应用与验证提供程序之间的中间层。

> [!IMPORTANT]
> 强烈建议不要使用旧的仅移动身份验证库和模式，这些库和模式在身份验证流中没有利用 Web 后端，因为它们本身缺乏存储客户端密码的安全性。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 WebAuthenticator 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

Android 要求设置意图筛选器来处理回调 URI。 这很容易实现，只需要将 `WebAuthenticatorCallbackActivity` 类作为子类即可：

> [!NOTE]
> 应考虑实现 [Android 应用链接](https://developer.android.com/training/app-links/)来处理回调 URI，并确保你的应用程序是唯一可以注册以处理回调 URI 的应用程序。

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

还需要从 `MainActivity` 中的 `OnResume` 重写回调到 Essentials：

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 上，需要将应用的回调 URI 模式添加到 Info.plist 中。

> [!NOTE]
> 应考虑使用[通用应用链接](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content)来注册应用的回调 URI，这是最佳做法。

还需要重写 `AppDelegate` 的 `OpenUrl` 方法以调入 Essentials：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

对于 UWP，需要在 `Package.appxmanifest` 文件中声明回调 URI：

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>使用 WebAuthenticator

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

此 API 主要包含一个方法 `AuthenticateAsync`，该方法采用两个参数：一个是应该用于启动 Web 浏览器流的 URL，另一个是你希望流最终回调到的 URI（需要注册应用才能处理该 URI）。

结果为 `WebAuthenticatorResult`，其中包括通过回调 URI 分析的任何查询参数：

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

`WebAuthenticator` API 负责在浏览器中启动 URL 并等待至收到回调：

![典型的 Web 身份验证流](images/web-authenticator.png)

如果用户在任何时候取消流，就会抛出 `TaskCanceledException`。

## <a name="platform-differences"></a>平台差异

# <a name="android"></a>[Android](#tab/android)

如果提供自定义选项卡，则使用这些选项卡，否则，为 URL 启动“意向”。

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 12 或更高版本中，使用 `ASWebAuthenticationSession`。  在 iOS 11 上，使用 `SFAuthenticationSession`。  在早期 iOS 版本上，如果提供 `SFSafariViewController`，则使用它，否则使用 Safari。

# <a name="uwp"></a>[UWP](#tab/uwp)

在 UWP 上，如果支持 `WebAuthenticationBroker`，则使用它，否则使用系统浏览器。

-----

## <a name="apple-sign-in"></a>Apple 登录

根据 [Apple 的审核准则](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)，如果应用使用任何社交登录服务进行身份验证，则还必须提供“Apple 登录”选项。

若要将“Apple 登录”添加到应用，首先需要[将应用配置为使用“Apple 登录”](../ios/platform/ios13/sign-in.md)。

对于 iOS 13 和更高版本，需要调用 `AppleSignInAuthenticator.AuthenticateAsync()` 方法。 这会在后台使用本机 Apple 登录 API，以便用户可以在这些设备上获得最佳体验。 可以编写共享代码，以便在运行时使用正确的 API，如下所示：

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var accessToken = r?.AccessToken;
```

> [!TIP]
> 对于非 iOS 13 设备，这将启动 Web 身份验证流，也可用于在 Android 和 UWP 设备上启用 Apple 登录。
> 可在 iOS 模拟器上登录 iCloud 帐户来测试“Apple 登录”功能。

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET Core 服务器后端

可以将 `WebAuthenticator` API 与任何 Web 后端服务一起使用。  要将它与 ASP.NET Core 应用一起使用，首先需要通过以下步骤配置 Web 应用：

1. 在 ASP.NET Core Web 应用中设置所需的[外部社交验证提供程序](/aspnet/core/security/authentication/social/?tabs=visual-studio&view=aspnetcore-3.1)。
2. 在 `.AddAuthentication()` 调用中设置 `CookieAuthenticationDefaults.AuthenticationScheme` 的默认身份验证方案。
3. 在 Startup.cs `.AddAuthentication()` 调用中使用 `.AddCookie()`。
4. 必须将所有提供程序配置为 `.SaveTokens = true;`。

> [!TIP]
> 如果要添加“Apple 登录”，可以使用 `AspNet.Security.OAuth.Apple` NuGet 包。  可以在 Essentials GitHub 存储库中查看完整的 [Startup.cs 示例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60)。

### <a name="add-a-custom-mobile-auth-controller"></a>添加自定义移动身份验证控制器

对于移动身份验证流，通常需要直接向用户选择的提供程序发起流程（例如，通过单击应用登录屏幕上的“Microsoft”按钮）。  另外，还必须能够通过特定回调 URI 将相关信息返回到应用以结束身份验证流。

为此，请使用自定义 API 控制器：

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

使用此控制器是为了推断应用正在请求的方案（提供程序），并启动社交提供程序的身份验证流。 当提供程序回调到 Web 后端时，控制器将分析出结果，并使用参数重定向到应用的回调 URI。

有时，你可能希望将数据（例如提供程序的 `access_token`）返回给应用（可通过回调 URI 的查询参数执行此操作）。 或者，你可能需要对服务器创建自己的标识，并将自己的令牌重新传递到应用。 选择哪种做法，以及如何完成，这都完全取决于你！

请查看 Essentials 存储库中的[完整控制器示例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs)。

-----
## <a name="api"></a>API

- [WebAuthenticator 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [WebAuthenticator API 文档](xref:Xamarin.Essentials.WebAuthenticator)
- [ASP.NET Core 服务器示例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
