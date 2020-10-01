---
title: 指纹身份验证指南
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: aaa67972afeffd10c038a145a5703e917647b0fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453865"
---
# <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

## <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

现在，我们已经了解了关于 Android 6.0 指纹身份验证的概念和 API，接下来将讨论使用指纹 API 的一些一般建议。

1. **使用 Android 支持库 v4 兼容性 API** &ndash; 通过从代码中删除 API 检查简化应用程序代码，尽量使应用程序适用于更多的设备。
2. **提供指纹身份验证的替代方法** &ndash; 指纹身份验证是应用程序对用户进行身份验证的一种便捷方法，但是，无法确保它将一直有效/可用。 例如，指纹扫描器可能会发生故障，镜头可能变脏，用户可能未将设备配置为使用指纹身份验证或指纹会丢失。 此外，用户也可能不希望在你的应用程序中使用指纹身份验证。 由于这些原因，Android 应用程序应提供备用身份验证流程，例如用户名和密码。
3. **使用 Google 的指纹图标** &ndash; 所有应用程序都应使用 Google 提供的同一指纹图标。 使用标准图标可以使 Android 用户轻松识别在应用中使用指纹身份验证的位置： 
    
    ![Android 指纹图标](summary-images/ic-fp-40px.png)
    
4. **通知用户** &ndash; 应用程序应向用户显示某种形式的通知，表明指纹扫描器处于活动状态并正在等待触摸或滑动。 

## <a name="summary"></a>总结

指纹身份验证是一种让 Xamarin.Android 应用程序快速验证用户的理想方法，使用户可以更轻松地与敏感功能（如应用内购买）交互。 本指南讨论了在 Xamarin.Android 应用程序中合并 Android 6.0 指纹 API 所需的概念和代码。

首先，我们讨论了指纹 API `FingerprintManager`（和 `FingerprintManagerCompat`）本身。 我们研究了 `FingerprintManager.AuthenticationCallbacks` 抽象类必须如何由应用程序扩展，并用作指纹硬件和应用程序本身之间的媒介。 然后，我们探究了如何使用 Java `Cipher` 对象验证指纹扫描器结果的完整性。 最后，我们介绍了如何在设备上注册指纹，并使用 adb 在仿真器上模拟指纹刷，执行了一些简单测试。 

如果你尚未了解这些内容，应查看本指南随附的[示例应用程序](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)。 [指纹对话框示例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)已从 Java 移植到 Xamarin.Android，它还提供了另一个示例，说明如何将指纹身份验证添加到 Android 应用程序。

## <a name="related-links"></a>相关链接

- [指纹指南示例应用](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指纹对话框示例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [指纹图标](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)