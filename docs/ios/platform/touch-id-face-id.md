---
title: 具有 Xamarin 的 Touch ID 和人脸 ID
description: 本文档提供 iOS 中生物识别身份验证的概要说明。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: 526de99f32a8682cbe6862e46f90c674cf7d3dc6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430371"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>在 Xamarin 中使用 Touch ID 和人脸 ID

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS 支持两个生物识别身份验证系统：

1. **TOUCH ID** 使用 "主页" 按钮下的指纹传感器。
1. 人**脸 ID**使用正面相机传感器来使用面部扫描对用户进行身份验证。

在 ios 7 中引入了 Touch ID，在 iOS 11 中引入了人脸 ID。

这些身份验证系统依赖于一个名为 _Secure Enclave_的基于硬件的安全处理器。 Secure Enclave 负责加密面部和指纹数据的数学表示形式，并使用此信息对用户进行身份验证。 根据 Apple，人脸和指纹数据不会离开设备，也不会备份到 iCloud。 应用通过 _本地身份验证_ API 与 Secure Enclave 交互，不能检索人脸或指纹数据，也不能直接访问安全 Enclave。

在提供对受保护内容的访问权限之前，应用程序可以使用 Touch ID 和人脸 ID 对用户进行身份验证。

## <a name="local-authentication-context"></a>本地身份验证上下文

IOS 上的生物识别身份验证依赖于 _本地身份验证上下文_ 对象，该对象是 `LAContext` 类的实例。 `LAContext`通过类，可以执行以下操作：

- 检查生物识别硬件的可用性。
- 评估身份验证策略。
- 评估访问控制。
- 自定义并显示身份验证提示。
- 重新使用或使身份验证状态无效。
- 管理凭据。

## <a name="detect-available-authentication-methods"></a>检测可用的身份验证方法

示例项目包含 `AuthenticationView` 由支持的 `AuthenticationViewController` 。 此类将重写 `ViewWillAppear` 方法，以检测可用的身份验证方法：

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

`ViewWillAppear`当 UI 即将向用户显示时，将调用方法。 此方法定义的一个新实例 `LAContext` ，并使用 `CanEvaluatePolicy` 方法来确定是否启用了生物识别身份验证。 如果是这样，它将检查系统版本和 `BiometryType` 枚举，以确定哪些生物识别选项可用。

如果未启用生物识别身份验证，应用程序会尝试回退到 PIN 身份验证。 如果生物识别和 PIN 验证均不可用，则设备所有者没有启用安全功能，无法通过本地身份验证保护内容。

## <a name="authenticate-a-user"></a>对用户进行身份验证

`AuthenticationViewController`示例项目中的包含一个 `AuthenticateMe` 方法，该方法负责对用户进行身份验证：

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

`AuthenticateMe`调用方法以响应用户点击 "**登录**" 按钮。 实例化一个新的 `LAContext` 对象，并检查设备版本以确定要在本地身份验证上下文上设置的属性。

`CanEvaluatePolicy`调用方法以检查是否启用了生物识别身份验证，如果可能，则回退到 PIN 身份验证，如果没有可用的身份验证模式，最后提供不安全的模式。 如果有可用的身份验证方法，则 `EvaluatePolicy` 使用方法来显示 UI 并完成身份验证过程。

如果身份验证成功，示例项目将包含模拟数据，并显示用于显示数据的视图。

## <a name="related-links"></a>相关链接

- [使用 Touch ID 或面部 ID 的本地身份验证示例](/samples/xamarin/ios-samples/ios11-faceidsample/)
- 关于 support.apple.com 上的[TOUCH ID](https://support.apple.com/en-us/HT204587)
- 关于 support.apple.com 上的[面部 ID](https://support.apple.com/en-us/HT208108)