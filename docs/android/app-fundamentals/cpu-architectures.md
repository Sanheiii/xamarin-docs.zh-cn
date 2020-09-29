---
title: CPU 体系结构
description: Xamarin 支持多个 CPU 体系结构，包括32位和64位设备。 本文介绍如何将应用定位到一个或多个支持 Android 的 CPU 体系结构。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 40cbc8b18c9f0bd177d2c8829aa0eac72fc6e2b8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456179"
---
# <a name="cpu-architectures"></a>CPU 体系结构

_Xamarin 支持多个 CPU 体系结构，包括32位和64位设备。本文介绍如何将应用定位到一个或多个支持 Android 的 CPU 体系结构。_

## <a name="cpu-architectures-overview"></a>CPU 体系结构概述

准备要发布的应用时，必须指定应用支持的平台 CPU 体系结构。 单个 APK 可包含计算机代码，以支持多个不同的体系结构。 特定于体系结构的代码的每个集合都与 (ABI) 的 *应用程序二进制接口* 相关联。 每个 ABI 定义在运行时此机器代码如何与 Android 进行交互。
有关其工作原理的详细信息，请参阅 [多核设备 &amp; Xamarin](~/android/deploy-test/multicore-devices.md)。

## <a name="how-to-specify-supported-architectures"></a>如何指定支持的体系结构

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

通常，在将应用配置为 **发布**时， (或体系结构) 显式选择体系结构。 当你的应用程序配置为进行 **调试**时，将启用 " **使用共享运行时** " 和 " **使用快速部署** " 选项，这将禁用显式体系结构选择。

在 Visual Studio 中，右键单击 " **解决方案资源管理器** 下的项目，然后选择" **属性**"。 在 " **Android 选项** " 页下，检查 " **打包属性** " 部分并验证 " **使用共享运行时** 已禁用 (关闭此选项可明确选择支持) 的 abi。 单击 " **高级** " 按钮，在 " **支持的体系结构**" 下，检查要支持的体系结构：

[![选择 armeabi 和 armeabi-armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

通常，在将应用配置为 **发布**时， (或体系结构) 显式选择体系结构。 当你的应用程序配置为进行 **调试**时，将启用 " **使用共享 Mono 运行时** " 和 " **快速程序集部署** " 选项，这会阻止显式体系结构选择。

在 Visual Studio for Mac 中，在 " **解决方案** 板" 中找到你的项目，单击你的项目旁边的齿轮图标，然后选择 " **选项**"。 在 " **项目选项** " 对话框中，单击 " **Android 生成**"。 单击 " **常规** " 选项卡，验证是否已禁用 " **使用共享 Mono 运行时** " (关闭此选项可显式选择要支持) 哪些 abi。 单击 " **高级** " 选项卡，在 " **受支持的 abi**" 下，检查要支持的体系结构的 abi：

[![选择 armeabi 和 armeabi-armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----

Xamarin.Android 支持以下体系结构：

- **armeabi** &ndash; 至少支持 ARMv5TE 指令集的基于 ARM 的 Cpu。 请注意，不 `armeabi` 是线程安全的，不应在多 CPU 设备上使用。

> [!NOTE]
>  从 [Xamarin.Android 9.2](/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture) 开始，不再支持 `armeabi`。

- **armeabi-armeabi-v7a** &ndash; 基于 ARM 的 Cpu，具有硬件浮点运算和多个 CPU (SMP) 设备。 请注意， `armeabi-v7a` 计算机代码不会在 ARMv5 设备上运行。

- **arm64-arm64-v8a** &ndash; 基于64位 ARMv8 体系结构的 Cpu。

- **x86** &ndash; 支持 x86 (或 IA-32) 指令集的 Cpu。 此指令集等效于 Pentium Pro 的说明，包括 MMX、SSE、SSE2 和 SSE3 指令。

- **x86_64** 支持64位 x86 (的 Cpu 也称为   *x64* 和 *AMD64*) 指令集。

`armeabi-v7a`对于**发布**版本，Xamarin Android 默认值为。 此设置提供比更好 `armeabi` 的性能。 如果以64位 ARM 平台为目标 (例如结点 9) ，请选择 `arm64-v8a` 。 如果要将应用部署到 x86 设备，请选择 `x86` 。 如果目标 x86 设备使用64位 CPU 体系结构，请选择 `x86_64` 。

## <a name="targeting-multiple-platforms"></a>面向多个平台

若要以多个 CPU 体系结构为目标，可以选择多个 ABI (，同时) 较大的 APK 文件大小。 你可以使用 "为每个 **选定的 ABI () 生成一个包** " 选项 ([) 中所](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties) 述为每个受支持的体系结构创建单独的 apk。

无需选择 **arm64-arm64-v8a** 或 **x86_64** 来面向64位设备;在64位硬件上运行你的应用程序不需要64位支持。 例如，64位 ARM 设备 (例如 [结点 9](https://www.google.com/nexus/9/)) 可以运行为配置的应用 `armeabi-v7a` 。 启用64位支持的主要优点是使应用程序能够处理更多的内存。

> [!NOTE]
> 自 2018 年 8 月起新应用需要面向 API 级别 26，自 2019 年 8 月起，除 32 位版本之外，应用还[需要提供 64 位版本](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

## <a name="additional-information"></a>其他信息

在某些情况下，您可能需要为每个体系 (结构创建一个单独的 APK，以减小 APK 的大小，或者因为您的应用程序具有特定于特定 CPU 体系结构) 的共享库。
有关此方法的详细信息，请参阅 [生成 ABI 特定的 apk](~/android/deploy-test/building-apps/abi-specific-apks.md)。