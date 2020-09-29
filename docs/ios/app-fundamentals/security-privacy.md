---
title: iOS 安全和隐私功能
description: 本文档介绍了 iOS 的安全和隐私功能，并讨论了如何将它们与 Xamarin 一起使用。 其中介绍了在 iOS 10 中进行的更新以及如何访问专用用户数据。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: f665d2de767721cd2f5eedd45653e36c01395569
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435279"
---
# <a name="ios-security-and-privacy-features"></a>iOS 安全和隐私功能

_本文介绍如何在 iOS 中使用安全和隐私以及它们如何影响 Xamarin iOS 应用。_

Apple 在 iOS 10 (和更高) 版本中对安全和隐私进行了多项改进，有助于开发人员提高应用程序的安全性并确保最终用户的隐私。 本文介绍如何在 Xamarin iOS 应用程序中实现这些功能。

<a name="General-Enhancements"></a>

## <a name="general-enhancements"></a>常规增强功能

IOS 10 中对安全和隐私进行了以下常规更改：

- 常见的数据安全体系结构 (CDSA) API 已弃用，应该替换为 SecKey API 以生成非对称密钥。
- 新 `NSAllowsArbitraryLoadsInWebContent` 密钥可以添加到应用的 **info.plist** 文件中，并允许在 Apple 传输安全 (ATS) 保护仍为应用的其余部分启用时正确加载网页。 有关详细信息，请参阅我们的 [应用传输安全](~/ios/app-fundamentals/ats.md) 文档。
- 由于 iOS 10 和 macOS Sierra 中的新剪贴板允许用户在设备之间进行复制和粘贴，因此已将 API 扩展为允许将剪贴板限制为特定设备，并在给定的时间戳自动清除。 此外，已命名的 pasteboards 不再保留，并且应替换为共享粘贴板容器。
- 对于所有 SSL/TLS 连接，默认情况下，RC4 对称密码处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，并且建议开发人员尽快使用 SHA-1 和3DES 加密。

<a name="Accessing-Private-User-Data"></a>

## <a name="accessing-private-user-data"></a>访问私有用户数据

在 iOS 10 (或更高版本上运行的应用程序) 必须通过在其 **info.plist** 文件中输入一个或多个隐私密钥来以静态方式声明它们的意图，以向用户说明应用希望获取访问权限的原因。

> [!IMPORTANT]
> 当用户尝试访问其中一个受限制的功能或用户信息时，系统将以无提示方式终止提供所需密钥的应用， _而不会出现错误_！ 如果应用在 iOS 10 上意外地启动失败，请确保已指定了所有必需的 **信息。 info.plist** 已指定。

提供以下与隐私相关的密钥：

- **隐私-Apple Music 使用说明** (`NSAppleMusicUsageDescription`) -允许开发人员描述应用要访问用户的媒体库的原因。
- **隐私-蓝牙外围设备使用说明** (`NSBluetoothPeripheralUsageDescription`) -允许开发人员描述应用要在用户设备上访问蓝牙的原因。
- **隐私-日历使用说明** (`NSCalendarsUsageDescription`) -允许开发人员描述应用要访问用户的日历的原因。
- **隐私-照相机使用说明** (`NSCameraUsageDescription`) -允许开发人员描述应用要访问设备照相机的原因。
- **隐私-联系人使用说明** (`NSContactsUsageDescription`) -允许开发人员描述应用要访问用户的联系人的原因。
- **隐私-运行状况共享用法说明** (`NSHealthShareUsageDescription`) -允许开发人员描述应用要访问用户的运行状况数据的原因。 有关详细信息，请参阅 Apple 的 [HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-健康更新使用说明** (`NSHealthUpdateUsageDescription`) -允许开发人员描述应用要编辑用户的运行状况数据的原因。 有关详细信息，请参阅 Apple 的 [HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-HomeKit 使用说明** (`NSHomeKitUsageDescription`) -允许开发人员描述应用要访问用户的 HomeKit 配置数据的原因。
- **隐私-位置始终使用描述** (`NSLocationAlwaysUsageDescription`) -允许开发人员描述应用始终有权访问用户位置的原因。
- 弃用 **隐私-位置使用说明** (`NSLocationUsageDescription`) -允许开发人员描述应用要访问用户位置的原因。 *注意：此密钥在 iOS 8 (和更高) 版本中已弃用。请改用 `NSLocationAlwaysUsageDescription` 或 `NSLocationWhenInUseUsageDescription` 。*
- **隐私-位置使用时的使用情况描述** (`NSLocationWhenInUseUsageDescription`) -允许开发人员描述应用在运行时要访问用户位置的原因。
- 弃用 **隐私-媒体库使用说明** -允许开发人员描述应用要访问用户的媒体库的原因。 *注意：此密钥在 iOS 8 (和更高) 版本中已弃用。改为使用 `NSAppleMusicUsageDescription` 。*
- **隐私-麦克风使用说明** (`NSMicrophoneUsageDescription`) -允许开发人员描述应用要访问设备麦克风的原因。
- **隐私-运动使用说明** (`NSMotionUsageDescription`) -允许开发人员描述应用要访问设备的加速感应的原因。
- **隐私-照片库使用说明** (`NSPhotoLibraryUsageDescription`) -允许开发人员描述应用要访问用户照片库的原因。
- **隐私-提醒使用说明** (`NSRemindersUsageDescription`) -允许开发人员描述应用要访问用户提醒的原因。
- **隐私-Siri 使用说明** (`NSSiriUsageDescription`) -允许开发人员描述应用要将用户数据发送到 Siri 的原因。
- **隐私-语音识别使用说明** (`NSSpeechRecognitionUsageDescription`) -允许开发人员描述应用要将用户数据发送到 Apple 语音识别服务器的原因。
- **隐私-电视提供商使用说明** (`NSVideoSubscriberAccountUsageDescription`) -允许开发人员描述应用要访问用户的电视提供商帐户的原因。

有关使用 **info.plist** 密钥的详细信息，请参阅 Apple 的 [信息属性列表关键参考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys"></a>

## <a name="setting-privacy-keys"></a>设置隐私密钥

使用以下示例在 iOS 10 (和更高) 上访问 HomeKit，开发人员需要将 `NSHomeKitUsageDescription` 密钥添加到应用的 **info.plist** 文件中，并提供一个字符串来声明应用要访问用户的 HomeKit 数据库的原因。 首次运行应用时，将向用户显示此字符串：

![NSHomeKitUsageDescription 警报示例](security-privacy-images/info01.png "NSHomeKitUsageDescription 警报示例")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

适用于 Visual Studio 的 Xamarin 目前不支持在默认的 iOS 清单编辑器中编辑 **info.plist** 的私钥。 相反，你将需要使用一般 Info.plist 编辑器，因此请执行以下操作：

1. 右键单击**解决方案资源管理器**中的**info.plist**文件，然后选择 "**打开方式 ...**"。
2. 从程序列表中选择 **通用 Info.plist 编辑器** 打开该文件，然后单击 **"确定"**。

    ![选择一般 Info.plist 编辑器](security-privacy-images/InfoEditorSelectionVs.png "选择一般 Info.plist 编辑器")
3. 单击 **+** 编辑器中最后一行上的按钮，向列表中添加新条目。 这将称为 "自定义属性"，类型设置为， `String` 空值。
4. 单击属性名称，将显示一个下拉列表。
5. 从下拉列表中，选择 "隐私密钥" (如 **HomeKit 使用说明**) ： 

    ![选择私钥](security-privacy-images/InfoPListEditorSelectKey.png "选择私钥")
6. 输入 "值" 列中的说明，了解应用要访问给定的功能或用户信息的原因： 

    ![输入描述](security-privacy-images/InfoPListSetValue.png "输入描述")
7. 保存对文件所做的更改。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要设置任何隐私密钥，请执行以下操作：

1. 在“解决方案资源管理器”  中，双击 **Info.plist** 文件，打开它进行编辑。
2. 在屏幕底部，切换到 " **源** " 视图。
3. 向列表中添加新 **项** 。
4. 从下拉列表中，选择 "隐私密钥" (如 **HomeKit 使用说明**) ： 

    ![选择私钥](security-privacy-images/info02.png "选择私钥")
5. 输入应用要访问给定的功能或用户信息的原因说明： 

    ![输入描述](security-privacy-images/info03.png "输入描述")
6. 保存对文件所做的更改。

-----

> [!IMPORTANT]
> 在上面给出的示例中，在 `NSHomeKitUsageDescription` **info.plist** 文件中设置密钥失败会导致应用程序在运行时以 _静默方式失败_ (在 iOS 10 (或更高) 版本中运行时不会出错) 。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了 Apple 在 iOS 10 中进行的安全和隐私更改以及它们如何影响 Xamarin iOS 应用。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)