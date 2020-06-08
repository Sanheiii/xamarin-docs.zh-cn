---
title: "Xamarin.Essentials：Email" description:"Xamarin.Essentials 中的 Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。"
ms.assetid：5FBB6FF0-0E7B-4C29-8F06-91642AF12629 author: jamesmontemagno ms.custom: video ms.author: jamont ms.date:2019 年 8 月 20 日 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-email"></a>Xamarin.Essentials：电子邮件

Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。

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

并非所有电子邮件客户端都支持发送附件。 有关详细信息，请参阅[文档](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email)。

-----

## <a name="api"></a>API

- [Email 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Email API 文档](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
