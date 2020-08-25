---
title: Xamarin.iOS 的自动预配
description: Xamarin.iOS 成功安装后，iOS 开发的下一步是 iOS 设备预配。 本指南介绍如何使用自动签名请求开发证书和配置文件。
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 09d20ef2e4fece36ce67a7891892defb05a5f3df
ms.sourcegitcommit: ca1eca5bcf6c82bc4ec05db12ee1be585006d59f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88514624"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Xamarin.iOS 的自动预配

Xamarin.iOS 成功安装后，iOS 开发的下一步是预配 iOS 设备。  本指南介绍如何使用自动预配请求开发证书和配置文件。

## <a name="requirements"></a>要求

Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017（版本 15.7 及更高版本）上提供了自动预配功能。 

> [!NOTE]
> 此外，必须拥有付费的 Apple 开发者帐户才能使用此功能。 有关 Apple 开发者帐户的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。
> 如果没有付费的 Apple 开发人员帐户，请参阅 [免费 Xamarin.iOS 预配] (~/ios/get-started/installation/device-provisioning/free-provisioning.md) 指南。

> [!NOTE]
> 在开始之前，请务必先接受 [Apple 开发者门户](https://developer.apple.com/account/)或 [App Store Connect](https://appstoreconnect.apple.com/) 中的任何许可协议。


## <a name="enable-automatic-provisioning"></a>启用自动设置

在开始自动签名过程之前，应确保已按 [Apple 帐户管理](~/cross-platform/macios/apple-account-management.md)指南中的说明将 Apple ID 添加到 Visual Studio 中。 

一旦添加了 Apple ID，便可以使用任意相关联的团队  。 此操作可以指定团队的证书、配置文件和其他 ID。 团队 ID 还可用于创建预配配置文件中包含的应用 ID 的前缀。 Apple 可以通过此团队 ID 验证你的身份是否和你所述一致。

若要自动签名应用以在 iOS 设备上开发，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开 iOS 项目。

2. 打开 Info.plist  文件。

3. 选择“应用程序”选项卡。

4. 在“签名”部分中，选择“自动设置”********：

    ![团队选择器下拉列表](automatic-provisioning-images/image2.png)

5. 从“团队”**** 下拉列表中选择团队。

6. 几秒后，便会创建签名证书和设置配置文件：

    ![成功创建证书和配置文件](automatic-provisioning-images/image5.png)

    如果自动签名失败，则“自动签名板”将显示错误的原因****。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> 如果你使用的是 Visual Studio 2017 或 Visual Studio 2019（版本 16.4 及更早版本），需要先[配对到 Mac 生成主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，然后才能继续操作。

1. 在“解决方案资源管理器”中，右键单击 iOS 项目名称，然后选择“属性” 。 然后，导航至“iOS 捆绑签名”选项卡：

    ![iOS 属性中的“捆绑签名”页的屏幕截图。](automatic-provisioning-images/bundle-signing-win.png)

2. 选择“自动预配”方案。

3. 从“团队”下拉菜单中选择团队，启动自动签名过程。 Visual Studio 将指示该过程是否已成功完成：

    ![“捆绑签名”页的屏幕截图，其中突出显示消息“自动预配已成功完成”。](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>运行自动预配

启用自动预配后，如果发生以下任何情况，Visual Studio 将在必要时重新运行该过程：

- iOS 设备插入到 Mac
  - 这会自动检查设备是否已在 Apple 开发人员门户中注册。 如果未注册，则会添加该设备并生成包含该设备的新的预配配置文件。
- 应用的捆绑 ID 发生更改
  - 这会更新应用 ID。 创建包含此应用 ID 的新预配配置文件。
- 一个受支持的功能在 Entitlements.plist 文件中启用。
  - 此功能将添加到应用 ID，并生成具有更新后的应用 ID 的新预配配置文件。
  - 当前并非所有功能均受支持。 有关受支持功能的详细信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="wildcard-app-ids"></a>通配符应用 ID

在 Visual Studio for Mac 和 Visual Studio 2019（版本 16.5 或更高版本）中，自动预配将默认尝试创建并使用通配符应用 ID 和预配配置文件，而不是基于 Info.plist 中指定的捆绑包标识符的显式应用 ID 。 通配符应用 ID 可减少 Apple 开发人员门户中要维护的配置文件和 ID 的数量。

在某些情况下，应用的权利需要显式应用 ID。 以下权利不支持通配符应用 ID：

- 应用组
- 关联的域
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- 热点
- 应用内购买
- 多路径
- NFC
- 个人 VPN
- 推送通知
- 无线附件配置

如果应用使用其中一种权利，Visual Studio 将尝试创建显式（而不是通配符）应用 ID。

## <a name="troubleshoot"></a>疑难解答 

- 新的 Apple 开发者帐户可能需要几个小时才能获得批准。 在帐户获得批准之前，你将无法启用自动预配。
- 如果自动预配过程失败，并显示错误消息 `Authentication Service Is Unavailable`，请登录到 [App Store Connect](https://appstoreconnect.apple.com/) 或 [appleid.apple.com](https://appleid.apple.com) 以检查是否已接受最新的服务协议。
- 如果收到错误消息 `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`，请确保所选团队具有 Apple 开发者计划的有效付费成员资格。 若要使用付费的 Apple 开发者帐户，请参阅[免费预配 Xamarin.iOS 应用](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

## <a name="related-links"></a>相关链接

- [可用设置](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [应用分发](~/ios/deploy-test/app-distribution/index.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
- [Apple - 应用分发指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
