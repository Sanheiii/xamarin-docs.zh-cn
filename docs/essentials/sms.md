---
title: Xamarin.Essentials：SMS
description: Xamarin.Essentials 中的 SMS 类允许应用程序使用要发送给收件人的指定消息打开默认短信应用程序。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d981a7ed2bffbbff12cf69ee4d0cda27ce319040
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353377"
---
# <a name="no-locxamarinessentials-sms"></a>Xamarin.Essentials：SMS

SMS 类允许应用程序使用要发送到收件人的指定消息打开默认短信应用程序。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问短信功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

如果项目的目标 Android 版本设置为 Android 11 (R API 30)，则必须使用与新的[包可见性要求](https://developer.android.com/preview/privacy/package-visibility)一起使用的查询来更新 Android 清单。

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  ：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="smsto"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="using-sms"></a>使用 SMS

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

SMS 功能通过调用 `ComposeAsync` 方法工作，该方法是包含消息收件人和消息正文（两者都是可选的）的 `SmsMessage`。

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

此外，还可以向 `SmsMessage` 传入多个收件人：

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [SMS 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Sms)
- [SMS API 文档](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
