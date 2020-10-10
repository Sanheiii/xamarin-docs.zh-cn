---
title: Xamarin.Essentials：联系人
description: 通过 Xamarin.Essentials 的 Contacts 类，用户可以选择联系人并检索联系人的相关信息。
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd239a8dcf192c0bdbc6265769208f4fc989bbbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434483"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials：联系人

通过 Contacts 类，用户可以选择联系人并检索联系人的相关信息。

![预发行版 API](~/media/shared/preview.png)

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Contacts 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

需要具有 `ReadContacts` 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加 ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadContacts)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码  。

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" /> />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中该权限。 这样会自动更新 AndroidManifest.xml 文件。

# <a name="ios"></a>[iOS](#tab/ios)

在 `Info.plist` 中添加以下项：

```xml
<key>NSContactsUsageDescription</key>
<string>This app needs access to contacts to pick a contact and get info.</string>
```

确保更新应用的特定说明中的 `<string>`，因为它将向用户显示。

# <a name="uwp"></a>[UWP](#tab/uwp)

在“功能”下的 `Package.appxmanifest` 中，确保已选中了 `Contact` 功能。

-----

## <a name="picking-a-contact"></a>选择联系人

调用 `Contacts.PickContactAsync()` 时，将显示联系人对话框，并允许用户接收用户的相关信息。


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var name = contact.Name;
    var contactType = contact.ContactType; // Unknown, Personal, Work
    var numbers = contact.Numbers; // List of phone numbers
    var emails = contact.Emails; // List of email addresses 
    
}
catch (Exception ex)
{
    // Handle exception here.
}
```


## <a name="api"></a>API

- [Contacts 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Contacts API 文档](xref:Xamarin.Essentials.Contacts)
