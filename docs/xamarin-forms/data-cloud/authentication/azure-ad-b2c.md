---
title: 使用 Azure Active Directory B2C 对用户进行身份验证
description: Azure Active Directory B2C 为面向消费者的 web 和移动应用程序提供云标识管理。 本文介绍如何使用 Azure Active Directory B2C 将标识管理集成到使用 Microsoft 身份验证库的移动应用程序中。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be1de99ba42e58e375792b0a4e03d1847e2e3c87
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562881"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 对用户进行身份验证

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C 为面向消费者的 web 和移动应用程序提供云标识管理。本文介绍如何使用 Azure Active Directory B2C 将标识管理集成到使用 Microsoft 身份验证库的移动应用程序中。_

## <a name="overview"></a>概述

Azure Active Directory B2C (ADB2C) 是面向消费者的应用程序的标识管理服务。 它允许用户使用现有的社交帐户或自定义凭据（如电子邮件或用户名和密码）登录到你的应用程序。 自定义凭据帐户称为 _本地_ 帐户。

将 Azure Active Directory B2C 标识管理服务集成到移动应用程序的过程如下所示：

1. 创建 Azure Active Directory B2C 租户。
1. 将你的移动应用程序注册到 Azure Active Directory B2C 租户。
1. 为注册和登录创建策略，并忘记密码用户流。
1. 使用 Microsoft 身份验证库 (MSAL) ，通过 Azure Active Directory B2C 租户启动身份验证工作流。

> [!NOTE]
> 如果还没有 [Azure 订阅](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://aka.ms/azfree-docs-mobileapps)。

Azure Active Directory B2C 支持多个标识提供者，包括 Microsoft、GitHub、Facebook、Twitter 等。 有关 Azure Active Directory B2C 功能的详细信息，请参阅 [Azure Active Directory B2C 文档](/azure/active-directory-b2c/)。

Microsoft 身份验证库支持多个应用程序体系结构和平台。 有关 MSAL 功能的信息，请参阅 GitHub 上的 [Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) 。

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>配置 Azure Active Directory B2C 租户

若要运行示例项目，必须创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅 [在 Azure 门户中创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/active-directory-b2c-get-started/)。

创建租户后，你将需要 **租户名称** 和 **租户 ID** 来配置移动应用程序。 租户 ID 和名称由创建租户 URL 时生成的域定义。 如果生成的租户 URL 是 `https://contoso20190410tenant.onmicrosoft.com/` **租户 ID** `contoso20190410tenant.onmicrosoft.com` 且 **租户名称** 为，则为 `contoso20190410tenant` 。 单击顶部菜单中的 " **目录和订阅" 筛选器** ，查找 Azure 门户中的租户域。 以下屏幕截图显示了 Azure 目录和订阅筛选器按钮以及租户域：

[![Azure 目录和订阅筛选器视图中的租户名称](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

在示例项目中，编辑 **Constants.cs** 文件以设置 `tenantName` 和 `tenantId` 字段。 下面的代码演示如果你的租户域为，应如何设置这些值 `https://contoso20190410tenant.onmicrosoft.com/` ，并将这些值替换为门户中的值：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>将移动应用程序注册到 Azure Active Directory B2C

移动应用程序必须先在租户中注册，然后才能连接和验证用户身份。 注册过程将唯一的 **应用程序 ID** 分配给应用程序，并在身份验证后将响应的 **重定向 URL 定向** 回应用程序。 有关详细信息，请参阅 [Azure Active Directory B2C：注册应用程序](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。 你将需要知道分配给应用程序的 **应用程序 ID** ，该 ID 在 "属性" 视图中的应用程序名称之后列出。 以下屏幕截图显示了在何处可以找到应用程序 ID：

[![Azure 应用程序的 "属性" 视图中的应用程序 ID](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft 身份验证库需要将你的应用程序的 **重定向 URL** 作为前缀文本 "msal" 的 **应用程序 ID** ，后跟一个名为 "auth" 的终结点。 如果你的应用程序 ID 是 "1234abcd"，则完整 URL 应为 `msal1234abcd://auth` 。 请确保应用程序已启用 **Native client** 设置，并使用应用程序 ID 创建 **自定义重定向 URI** ，如以下屏幕截图所示：

![Azure 应用程序的 "属性" 视图中的自定义重定向 URI](azure-ad-b2c-images/azure-redirect-uri.png)

稍后将在 Android **ApplicationManifest.xml** 和 iOS **INFO.PLIST**中使用 URL。

在示例项目中，编辑 **Constants.cs** 文件以将字段设置 `clientId` 为你的 **应用程序 ID**。 下面的代码演示如果你的应用程序 ID 为，应如何设置此值 `1234abcd` ：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>创建注册和登录策略，并忘记密码策略

策略是用户完成任务（例如创建帐户或重置密码）的一种体验。 策略还指定用户从经验返回时收到的令牌的内容。 你必须为帐户注册和登录设置策略，然后重置密码。 Azure 提供了内置的策略来简化常见策略的创建。 有关详细信息，请参阅 [Azure Active Directory B2C：内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

完成策略设置后，用户应在 Azure 门户中 ** (策略 ** 中的两个策略) 视图。 以下屏幕截图演示了 Azure 门户中的两个已配置策略：

![Azure 用户中的两个已配置策略将 (策略传递) 视图](azure-ad-b2c-images/azure-application-policies.png)

在示例项目中，编辑 **Constants.cs** 文件以设置 `policySignin` 和字段， `policyPassword` 以反映在策略设置期间所选的名称：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>使用 Microsoft 身份验证库 (MSAL) 进行身份验证

必须将 Microsoft 身份验证库 (MSAL) NuGet 包添加到解决方案中的共享、.NET Standard 项目和平台项目 Xamarin.Forms 。 MSAL 包含一个 `PublicClientApplicationBuilder` 类，该类用于构造遵守接口的对象 `IPublicClientApplication` 。 MSAL 利用 `With` 子句为构造函数和身份验证方法提供其他参数。

在示例项目中， **应用** 隐藏的代码将定义名为和的静态 `AuthenticationClient` 属性 `UIParent` ，并 `AuthenticationClient` 在构造函数中实例化对象。 `WithIosKeychainSecurityGroup`子句提供 iOS 应用程序的安全组名称。 `WithB2CAuthority`子句提供将用于对用户进行身份验证的默认**证书颁发机构**。 `WithRedirectUri`子句告知 Azure 通知中心实例，如果指定了多个 uri，会重定向 URI 以供使用。 下面的示例演示如何实例化 `PublicClientApplication` ：

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .WithRedirectUri($"msal{Constants.ClientId}://auth")
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

> [!NOTE]
> 如果 Azure 通知中心实例仅定义了一个重定向 URI，则在 `AuthenticationClient` 不使用子句指定重定向 uri 的情况下，可以使用此实例 `WithRedirectUri` 。 但是，在 Azure 配置扩展以支持其他客户端或身份验证方法时，应始终指定此值。

`OnAppearing` **LoginPage.xaml.cs**代码中的事件处理程序调用 `AcquireTokenSilentAsync` 来刷新之前已登录的用户的身份验证令牌。 如果成功，则身份验证过程将重定向到， `LogoutPage` 且不执行任何操作。 下面的示例演示中的无提示身份验证过程 `OnAppearing` ：

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

`OnLoginButtonClicked`单击 "登录" 按钮) 调用时，事件处理程序 (触发 `AcquireTokenAsync` 。 MSAL 库会自动打开移动设备浏览器并导航到登录页。 登录 URL 称为 **颁发机构**，它是在 **Constants.cs** 文件中定义的租户名称和策略的组合。 如果用户选择 "忘记密码" 选项，则会将其返回给应用程序，但会出现异常，这会启动 "忘记的密码" 体验。 以下示例显示了身份验证过程：

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

`OnForgotPassword`方法与登录过程类似，但实现了自定义策略。 `OnForgotPassword` 使用的不同重载 `AcquireTokenAsync` ，这允许你提供特定的 **权限**。 下面的示例演示如何在获取令牌时提供自定义 **授权** ：

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

最后一条身份验证是注销过程。 `OnLogoutButtonClicked`当用户按 "注销" 按钮时，将调用方法。 它循环遍历所有帐户，并确保其令牌已失效。 下面的示例演示了注销实现：

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

在 iOS 上，必须在 **info.plist**中注册注册到 Azure Active Directory B2C 的自定义 URL 方案。 MSAL 要求 URL 方案符合特定模式，前面介绍了如何将 [移动应用程序注册到 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 以下屏幕截图显示了 **info.plist**中的自定义 URL 方案。

!["在 iOS 上注册自定义 URL 方案"](azure-ad-b2c-images/customurl-ios.png)

MSAL 还需要在 iOS 上注册密钥链 **权利，如**以下屏幕截图中所示：

!["在 iOS 上设置应用程序权利"](azure-ad-b2c-images/entitlements-ios.png)

Azure Active Directory B2C 完成授权请求后，它将重定向到注册的重定向 URL。 自定义 URL 方案会导致 iOS 启动移动应用程序，并将该 URL 作为启动参数传入，在此过程中，它由 `OpenUrl` 应用程序的类的替代进行处理 `AppDelegate` ，并将体验控制返回到 MSAL。 `OpenUrl`下面的代码示例演示了实现：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

在 Android 上，必须在 **AndroidManifest.xml**中注册 Azure Active Directory B2C 向注册的自定义 URL 方案。 MSAL 要求 URL 方案符合特定模式，前面介绍了如何将 [移动应用程序注册到 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 下面的示例演示了 **AndroidManifest.xml**中的自定义 URL 方案。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

`MainActivity`必须修改类，以便在 `UIParent` 调用期间向应用程序提供对象 `OnCreate` 。 Azure Active Directory B2C 完成授权请求后，它将从 **AndroidManifest.xml**重定向到注册的 URL 方案。 已注册的 URI 方案导致 Android `OnActivityResult` 使用 URL 作为启动参数调用方法，该参数由方法进行处理 `SetAuthenticationContinuationEventArgs` 。

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台上使用 MSAL 不需要其他设置

## <a name="run-the-project"></a>运行项目

在虚拟或物理设备上运行该应用程序。 点击 " **登录** " 按钮会打开浏览器并导航到可在其中登录或创建帐户的页面。 完成登录过程后，应返回到应用程序的注销页面。 以下屏幕截图显示了在 Android 和 iOS 上运行的用户登录屏幕：

!["Android 和 iOS 上的 Azure ADB2C 登录屏幕"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>相关链接

- [AzureADB2CAuth (示例) ](/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 身份验证库文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)