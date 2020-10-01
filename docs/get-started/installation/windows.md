---
title: 在 Visual Studio 2019 中安装 Xamarin
description: 本文档介绍了如何在 Visual Studio 2019 中安装 Xamarin。 其中讨论了相关要求、安装过程以及如何验证安装。
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 255c870407f1657196abd826b46c7e9b114285c9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433315"
---
# <a name="installing-xamarin-in-visual-studio-2019"></a>在 Visual Studio 2019 中安装 Xamarin

<a name="requirements"></a>

在开始前请查看[系统要求](~/cross-platform/get-started/requirements.md)。

## <a name="installation"></a>安装

[!include[](~/cross-platform/includes/install-xamarin-windows-2019.md)]

在 Visual Studio 2019 中，通过单击“帮助”菜单来验证是否安装了 Xamarin。 如果安装了 Xamarin，应该看到 Xamarin 菜单项，如此屏幕截图所示：

![“帮助”菜单上的 Xamarin 菜单项](windows-images/12-xamarin-menu-item.png "“帮助”菜单上的 Xamarin 菜单项")

也可单击“帮助”>“关于 Microsoft Visual Studio”，滚动浏览已安装的产品列表，查看是否已安装 Xamarin：

![显示 Visual Studio 2019 已安装产品的屏幕](windows-images/13-xamarin-is-installed.png "显示 Visual Studio 2019 已安装产品的屏幕")

有关查找版本信息的详细信息，请参阅 [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md)（在何处可找到我的版本信息和日志？）

## <a name="next-steps"></a>后续步骤

通过在 Visual Studio 2019 上安装 Xamarin，可开始编写应用代码，但若要构建应用并将其部署到模拟器、仿真器和设备，则需要其他设置。 请访问以下指南，完成安装并开始跨平台应用构建。

### <a name="ios"></a>iOS

有关详细信息，请参阅[在 Windows 上安装 Xamarin.iOS](~/ios/get-started/installation/windows/index.md) 指南。

1. [安装 Visual Studio for Mac](/visualstudio/mac/installation)
2. [将 Visual Studio 连接到 Mac 生成主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [iOS 开发人员设置](~/ios/get-started/installation/device-provisioning/index.md) - 要求在设备上运行应用程序
4. [远程 iOS 模拟器](~/tools/ios-simulator/index.md)
5. [Xamarin.iOS for Visual Studio 简介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

有关详细信息，请参阅[在 Windows 上安装 Xamarin.Android](~/android/get-started/installation/windows.md) 指南。

1. [Xamarin.Android 配置](~/android/get-started/installation/windows.md#configuration)
2. [使用 Xamarin Android SDK 管理器](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Android SDK 仿真器](~/android/get-started/installation/android-emulator/index.md)
4. [设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)