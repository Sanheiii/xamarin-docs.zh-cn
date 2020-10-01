---
title: 在 Windows 上安装 Xamarin.iOS
description: 本文档介绍了如何设置 Windows 计算机和 Mac 生成主机，以及如何将 Windows 与 Mac 配对，以便进行 Xamarin.iOS 开发。
zone_pivot_groups: platform-win
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 89c6125b4afacd23ddf2c2877c97dff34b5692e3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436640"
---
# <a name="installing-xamarinios-on-windows"></a>在 Windows 上安装 Xamarin.iOS

本文介绍如何设置适用于 Xamarin.iOS 开发的 Windows 计算机和 Mac 生成主机。__

::: zone pivot="windows"

## <a name="overview"></a>概述

若要在 Windows 上使用 Visual Studio 2019 生成 Xamarin.iOS 应用，需要满足以下条件：

- Windows 计算机上需安装 Visual Studio 2019。 可以是物理或虚拟计算机。

  - [Windows 系统要求](~/cross-platform/get-started/requirements.md#windows-requirements)

- 使用 Apple 生成工具和 Xamarin.iOS 设置可访问网络的 Mac。 Visual Studio 2019 通过网络连接访问此计算机，以使用 Apple 的生成工具，这些工具是编译本机 iOS 应用程序所必需的。

  - [Mac 系统要求](~/cross-platform/get-started/requirements.md#macos-requirements)

  > [!TIP]
  > 没有访问 Mac 的权限？
  >
  > 如果没有访问 Mac 的权限，则可以使用 [MacinCloud](https://www.macincloud.com/pages/visual-studio-mac.html) 或 [MacStadium](https://www.macstadium.com/)；这两个服务会提供基于云的 Mac 硬件，你可以使用该硬件生成 Xamarin.iOS 项目。

## <a name="setup"></a>设置

要完成相关设置以在 Visual Studio 2019 中进行 Xamarin.iOS 开发，需执行以下步骤：

1. 设置 Windows（安装 Visual Studio 2019）

    Xamarin.iOS 适用于独立或虚拟计算机上的 Visual Studio 2019 社区版、专业版和企业版。

    - [安装 Visual Studio 2019](~/get-started/installation/windows.md)。

2. 设置 Mac（安装 Xcode 和 Visual Studio for Mac）

    要生成、调试 iOS 应用程序以及对其进行签名以进行分发，Visual Studio 2017 必须能够通过网络访问通过 Apple 开发人员工具 (Xcode) 和 Xamarin.iOS 配置的 Mac 生成主机。

    - [从 Mac App Store 下载和安装 Xcode ](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。
    - [安装 Visual Studio for Mac](/visualstudio/mac/installation)，这同时会安装 Xamarin.iOS。

    > [!NOTE]
    > 如果不想安装 Visual Studio for Mac、Visual Studio 2019，可使用生成 Xamarin.iOS 应用程序所需的软件[自动配置](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) Mac 生成主机。
    > 有关详细信息，请参阅[自动预配 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning)。

3. 与 Mac 配对（将 Visual Studio 2019 连接到 Mac）

    为了使 Visual Studio 2019 能够在 Mac 上使用 iOS 生成工具，两台计算机必须通过网络连接。

    - [阅读“与 Mac 配对”指南](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

::: zone-end
::: zone pivot="win-vs2017"

## <a name="overview"></a>概述

若要在 Windows 上使用 Visual Studio 2017 生成 Xamarin.iOS 应用，需要满足以下条件：

- Windows 计算机上需安装 Visual Studio 2017。 可以是物理或虚拟计算机。
  - [Windows 系统要求](~/cross-platform/get-started/requirements.md#windows-requirements)

- 使用 Apple 生成工具和 Xamarin.iOS 设置可访问网络的 Mac。 Visual Studio 2017 通过网络连接访问此计算机，以使用 Apple 的生成工具，这些工具是编译本机 iOS 应用程序所必需的。
  - [Mac 系统要求](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>设置

要完成相关设置以在 Visual Studio 2017 中进行 Xamarin.iOS 开发，需执行以下步骤：

1. 设置 Windows（安装 Visual Studio 2017）

    Xamarin.iOS 适用于独立或虚拟计算机上的 Visual Studio 2017 社区版、专业版和企业版。

    - [安装 Visual Studio 2017](~/get-started/installation/windows.md)。

2. 设置 Mac（安装 Xcode 和 Visual Studio for Mac）

    要生成、调试 iOS 应用程序以及对其进行签名以进行分发，Visual Studio 2017 必须能够通过网络访问通过 Apple 开发人员工具 (Xcode) 和 Xamarin.iOS 配置的 Mac 生成主机。

    - [从 Mac App Store 下载和安装 Xcode ](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。
    - [安装 Visual Studio for Mac](/visualstudio/mac/installation)，这同时会安装 Xamarin.iOS。

    > [!NOTE]
    > 如果不想安装 Visual Studio for Mac，从 [Visual Studio 2017 版本 15.6](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) 开始，Visual Studio 2017 可自动使用生成 Xamarin.iOS 应用程序的软件来配置 Mac 生成主机。 有关详细信息，请参阅[自动预配 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning)。

3. 与 Mac 配对（将 Visual Studio 2017 连接到 Mac）

    为了使 Visual Studio 2017 能够在 Mac 上使用 iOS 生成工具，两台计算机必须通过网络连接。

    - [阅读“与 Mac 配对”指南](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

::: zone-end

## <a name="summary"></a>总结

本文介绍如何设置适用于 Xamarin.iOS 开发的 Windows 计算机和与其关联的 Mac 生成主机。

## <a name="next-steps"></a>后续步骤

- [Xamarin.iOS for Visual Studio 简介](introduction-to-xamarin-ios-for-visual-studio.md)
- [配置 Visual Studio 以进行 iOS 开发](config-options.md)
- [设备预配](~/ios/get-started/installation/device-provisioning/index.md)