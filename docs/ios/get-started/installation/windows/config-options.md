---
title: 配置 Visual Studio 以进行 iOS 开发
description: 本文介绍如何配置 Visual Studio 2019 以进行 Xamarin.iOS 开发。 其中具体讨论了如何配置已安装版本 Xamarin.iOS、iOS 工具栏和解决方案平台下拉菜单。
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 0f57c10ea263a3dcf882d2cf75a57105e7f7d0d4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434524"
---
# <a name="configuring-visual-studio-for-ios-development"></a>配置 Visual Studio 以进行 iOS 开发

本文介绍适用于 Visual Studio 的各种 Xamarin.iOS 配置选项。 

## <a name="using-matching-xamarinios-versions"></a>使用匹配的 Xamarin.iOS 版本

Visual Studio 2019 或 Visual Studio 2017 必须使用 Mac 生成主机上安装的 Xamarin.iOS 版本。 为了确保满足此要求，必须符合以下条件：

- 如果使用 Visual Studio 2019 或 Visual Studio 2017，选择 Visual Studio for Mac 中的“稳定”更新通道  。

- 如果使用 Visual Studio 2019 预览版，选择 Visual Studio for Mac 中的“Alpha”更新通道  。

> [!NOTE]
> 从 [Visual Studio 2017 版本 15.6](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) 开始，Visual Studio 2017 自动检测 Mac 生成主机是否使用与 Windows 相同的 Xamarin.iOS 版本。 如果版本不匹配，则 Visual Studio 2017 自动在 Mac 生成主机上远程安装正确版本。 有关详细信息，请查看[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南中的[自动预配 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) 部分。

## <a name="ios-toolbar"></a>iOS 工具栏

在 Visual Studio 2019 或 Visual Studio 2017 中打开 iOS 项目时，应会显示 iOS 工具栏。  默认情况下，它包含可用于 Xamarin.iOS 开发的四个按钮：

![Visual Studio 2019 的 iOS 工具栏](config-options-images/ios-toolbar.png)

- **与 Mac 配对** – 打开“与 Mac 配对”对话框。 在 Visual Studio 2019 或 Visual Studio 2017 中打开 iOS 项目时，启用该按钮。
- **显示 iOS 模拟器** - 在 Mac 生成主机上，将 iOS 模拟器放在最前面。 在 Visual Studio 2019 或 Visual Studio 2017 中打开 iOS 项目时，启用该按钮。
- **设备日志** – 打开一个可用于检查设备日志的窗口。 在 Visual Studio 2019 或 Visual Studio 2017 中打开 iOS 项目时，启用该按钮。
- **在生成服务器上显示 IPA 文件** – 在 Mac 生成主机上打开一个窗口，显示应用的 .ipa 文件的位置。 完成为其创建 .ipa 的生成后，启用该按钮。

如果未显示该工具栏，打开 Visual Studio 2019 或 Visual Studio 2017 中的“视图”菜单，并选择“工具栏”>“iOS”   ：

![启用 iOS 工具栏](config-options-images/ios-toolbar-enable.png "启用 iOS 工具栏")

## <a name="solution-platforms-drop-down-menu"></a>解决方案平台下拉菜单

使用解决方案平台下拉菜单，可选择下一个生成是以物理设备还是以模拟器为目标  。

要确保标准工具栏上显示此下拉菜单，请执行以下操作：

- 在 Visual Studio 2019 或 Visual Studio 2017 中，单击标准工具栏右边缘的向下箭头。
- 选择“添加或删除按钮”  
- 确保已选中“解决方案平台”项  ：

![启用“解决方案平台”下拉菜单](config-options-images/solution-platforms-enable.png "启用“解决方案平台”下拉菜单")

现打开 iOS 项目后，标准工具栏和 iOS 工具栏应如以下屏幕截图所示   ：

![标准和 iOS 工具栏](config-options-images/toolbars.png "标准和 iOS 工具栏")