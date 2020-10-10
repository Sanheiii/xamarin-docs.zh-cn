---
title: Xamarin.Essentials：电子邮件
description: Xamarin.Essentials 中的 Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 059405d4e3219162022b3f8c0208ee5cc4ac2d38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434540"
---
# <a name="no-locxamarinessentials-email"></a>Xamarin.Essentials：电子邮件

Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。

若要访问电子邮件功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

如果项目的目标 Android 版本设置为 Android 11 (R API 30)，则必须使用与新的[包可见性要求](https://developer.android.com/preview/privacy/package-visibility)一起使用的查询来更新 Android 清单。

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  ：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.SENDTO" />
    <data android:scheme="mailto" />
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> 若要在 iOS 上使用电子邮件 API，必须在物理设备上运行它，否则将引发异常。

## <a name="using-email"></a>使用 Email

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `ComposeAsync` 方法和包含有关电子邮件信息的 `EmailMessage` 来使用 Email 功能：

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```

## <a name="file-attachments"></a>文件附件

借助此功能，应用可以在设备上的电子邮件客户端中通过电子邮件发送文件。 Xamarin.Essentials 将自动检测文件类型 (MIME) 并请求以附件形式添加文件。 每个电子邮件客户端都不同，可能只支持特定文件扩展名，也可能不支持任何文件扩展名。

以下是将文本写入磁盘并将其作为电子邮件附件添加的示例：

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="platform-differences"></a>平台差异

# <a name="android"></a>[Android](#tab/android)

并非所有适用于 Android 的电子邮件客户端都支持 `Html`，因为无法检测此差异，因此我们建议使用 `PlainText` 发送电子邮件。

# <a name="ios"></a>[iOS](#tab/ios)

无平台差异。

# <a name="uwp"></a>[UWP](#tab/uwp)

仅支持 `PlainText`，因为尝试发送 `Html` 的 `BodyFormat` 将引发 `FeatureNotSupportedException`。

并非所有电子邮件客户端都支持发送附件。 有关详细信息，请参阅[文档](/windows/uwp/contacts-and-calendar/sending-email)。

-----

## <a name="api"></a>API

- [Email 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Email)
- [Email API 文档](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]