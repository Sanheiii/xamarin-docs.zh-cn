---
title: Xamarin 中的 Core NFC
description: 本文档介绍了如何使用 iOS 11 中引入的 Api 在 Xamarin 中阅读附近的通信标记。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 17f5caa7849b076fd8ac2b7a22459fbf8fed1c9d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432798"
---
# <a name="core-nfc-in-xamarinios"></a>Xamarin 中的 Core NFC

_使用 iOS 11 读取近现场通信 (NFC) 标记_

CoreNFC 是 iOS 11 中的一个新框架，可用于访问 _近现场通信_ (NFC) 收音机，从应用中读取标记。 CoreNFC 适用于 iPhone 7、iPhone 7 Plus、iPhone 8、iPhone 8 Plus、iPhone X、iPhone XS 和 iPhone 11 模型 (虽然 iPhone 6 和 iPhone 6 Plus 型号具有 NFC 支付功能，但不支持 CoreNFC) 。

IOS 设备中的 NFC 标记读取器支持所有 NFC 标记类型1到5，其中包含 (NDEF) 信息的 _Nfc 数据交换格式_ 。

存在一些需要注意的限制：

- CoreNFC 仅支持标记读取 (不) 写入或设置格式。
- 标记扫描必须是用户启动的，并且在60秒后超时。
- 应用必须在前台可见，以便进行扫描。
- CoreNFC 只能在实际设备上进行测试， (不在模拟器) 上。

本页介绍使用 CoreNFC 所需的配置，并演示如何使用 ["NFCTagReader" 示例代码](/samples/xamarin/ios-samples/ios11-nfctagreader)。

## <a name="configuration"></a>配置

若要启用 CoreNFC，必须在项目中配置三个项：

- **Info.plist**密钥。
- **Info.plist**项。
- 具有 **NFC 标记读取** 功能的预配配置文件。

### <a name="infoplist"></a>Info.plist

添加 **NFCReaderUsageDescription** 的隐私密钥和文本，在扫描发生时向用户显示该密钥和文本。 使用适用于应用程序的消息 (例如，说明扫描) 的用途：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的应用程序必须在您的权利中使用以下键/值对，请求 **近字段通信标记读取** 功能 **。 info.plist**：

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>设置配置文件

创建新的 **应用 ID** ，并确保 **NFC 标记读取** 服务为勾选：

[![已选择 NFC 标记的开发人员门户新应用 ID 页面](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

然后，你应该为此应用 ID 创建新的预配配置文件，然后将其下载并安装到你的开发 Mac 上。

## <a name="reading-a-tag"></a>读取标记

配置项目后，将添加 `using CoreNFC;` 到文件顶部，并按照以下三个步骤来实现 NFC 标记读取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. 实现 `INFCNdefReaderSessionDelegate`

接口具有两个要实现的方法：

- `DidDetect` –当标记成功读取时调用。
- `DidInvalidate` –当发生错误或达到60秒超时时调用。

#### <a name="diddetect"></a>DidDetect

在示例代码中，每个已扫描的消息都添加到表视图中：

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

此方法可能会多次调用 (并且如果会话允许多个标记读取，则可以在) 中传入消息的数组。 这是使用第 `Start` [2 步](#step2)) 中介绍的 (方法的第三个参数设置的。

#### <a name="didinvalidate"></a>DidInvalidate

可能有多种原因会导致无效：

- 扫描时出错。
- 应用程序不再处于前台。
- 用户选择取消扫描。
- 此扫描已被应用取消。

下面的代码演示如何处理错误：

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

会话失效后，必须创建新的会话对象以再次扫描。

<a name="step2"></a>

### <a name="2-start-an-nfcndefreadersession"></a>2. 启动 `NFCNdefReaderSession`

扫描应以用户请求开始，如按钮按下。
下面的代码创建并启动一个扫描会话：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

构造函数的参数 `NFCNdefReaderSession` 如下所示：

- `delegate` –的实现 `INFCNdefReaderSessionDelegate` 。 在示例代码中，委托是在表视图控制器中实现的，因此 `this` 用作委托参数。
- `queue` –处理回调的队列。 它可以为 `null` ，在这种情况下，请务必在 `DispatchQueue.MainQueue` 更新用户界面控件时使用 (如示例) 中所示。
- `invalidateAfterFirstRead` – `true` 扫描在第一次成功扫描之后停止; 扫描 `false` 将继续，返回多个结果，直到取消扫描或达到60秒超时。

### <a name="3-cancel-the-scanning-session"></a>3. 取消扫描会话

用户可以通过用户界面中系统提供的按钮取消扫描会话：

![扫描时的 "取消" 按钮](corenfc-images/scan-cancel-sml.png)

应用可以通过调用方法以编程方式取消扫描 `InvalidateSession` ：

```csharp
Session.InvalidateSession();
```

在这两种情况下， `DidInvalidate` 将调用委托的方法。

## <a name="summary"></a>总结

CoreNFC 使应用能够从 NFC 标记读取数据。 它支持读取各种标记格式 (NDEF 类型1到 5) ，但不支持写入或格式设置。

## <a name="related-links"></a>相关链接

- [NFCTagReader (示例) ](/samples/xamarin/ios-samples/ios11-nfctagreader)
- [介绍核心 NFC (WWDC)  (视频) ](https://developer.apple.com/videos/play/wwdc2017/718/)