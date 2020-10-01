---
title: Android 无线发送
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8132be3e709e6cfe519934ce6c16ae16ac7054c6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454216"
---
# <a name="android-beam"></a>Android 无线发送

Android Beam 是 Android 4.0 中引入的近场通信 (NFC) 技术，允许应用程序在紧密邻近时通过 NFC 共享信息。

[![展示两台紧密邻近的设备在共享信息的图](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android Beam 的工作原理是，当两台设备在范围内时，通过 NFC 推送消息。 彼此相距约 4cm 的两台设备可以使用 Android Beam 共享数据。 一台设备上的活动创建消息，并指定一个或多个可以处理消息推送的活动。 如果指定的活动位于前台，且两台设备处于范围内，Android Beam 就会将消息推送到第二台设备。 接收设备上会调用包含消息数据的意向。

Android 支持通过两种方式来设置使用 Android Beam 推送消息：

- `SetNdefPushMessage` - 在 Android Beam 启动前，应用程序可以调用 SetNdefPushMessage，以指定要通过 NFC 推送的 NdefMessage，以及推送消息的活动。 此机制最适用于在应用程序使用时消息未更改的情况。

- `SetNdefPushMessageCallback` - 在 Android Beam 启动后，应用程序可以处理回拨来创建 NdefMessage。 此机制允许延迟到两台设备处于范围内时创建消息。 它支持消息可能根据应用程序中的操作而变化的方案。

无论是哪种方式，为了使用 Android Beam 发送数据，应用程序会发送 `NdefMessage`，同时将数据打包到多个 `NdefRecords` 中。 接下来看看在能够触发 Android Beam 前必须处理的要点。 首先，需要使用创建 `NdefMessage` 的回拨样式。

## <a name="creating-a-message"></a>创建消息

可以在活动的 `OnCreate` 方法中使用 `NfcAdapter` 注册回拨。 例如，假设名为 `mNfcAdapter` 的 `NfcAdapter` 在活动中声明为类变量，可以编写下面的代码，以创建用于构造消息的回拨：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

用于实现 `NfcAdapter.ICreateNdefMessageCallback` 的活动传递到上面的 `SetNdefPushMessageCallback` 方法。 在 Android Beam 启动时，系统会调用 `CreateNdefMessage`，活动可以从中构造 `NdefMessage`，如下所示：

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```

## <a name="receiving-a-message"></a>接收消息

在接收端，系统使用 `ActionNdefDiscovered` 操作调用意向，可以从中提取 NdefMessage，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

有关使用 Android Beam 的完整代码示例（如下面的屏幕截图所示），请参阅示例库中的 [Android Beam 演示](/samples/xamarin/monodroid-samples/androidbeamdemo)。

[![“Android Beam 演示”中的示例屏幕截图](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>相关链接

- [Android Beam 演示（示例）](/samples/xamarin/monodroid-samples/androidbeamdemo)