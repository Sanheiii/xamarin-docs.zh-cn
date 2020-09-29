---
title: MacOS Mojave 入门
description: 本文档介绍了如何设置以构建具有 Xamarin 的 macOS Mojave 应用程序。 它讨论了如何下载 Xcode 10 和更新 Visual Studio for Mac。
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: f6461c1d571afb29f6ac3c0615165d8d0380a66d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429660"
---
# <a name="get-started-with-macos-mojave"></a>MacOS Mojave 入门

本文档介绍了如何设置以构建具有 Xamarin 的 macOS Mojave 应用程序。 它讨论了如何下载 Xcode 10 和更新 Visual Studio for Mac。

## <a name="download-and-install"></a>下载并安装

1. **安装最新的 Xcode 10 beta** -已注册的 apple 开发人员可从 [Apple 开发人员门户](https://developer.apple.com/download/)下载并安装最新版本的 Xcode 10。

2. **运行 Xcode 10** –在更新和运行 Visual Studio for Mac 之前运行 Xcode 10;它会安装 Xamarin 所需的一些工具。

3. **更新 Visual Studio for Mac** –使用 Visual Studio for Mac 的最新稳定版本，使用 [Xamarin 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/mac/xamarin.mac_5/xamarin.mac_5.0.md) 或更高版本。

4. _ (可选) _ **在 Mac 上安装 macOS Mojave** –

   > [!TIP]
   > 即使你的应用不使用任何新的 macOS Mojave Api，也请务必使用 macOS Mojave SDK 生成它，并对其进行测试以确保一切按预期运行。 如果应用程序不调用任何新的 Api，可以使用 macOS Mojave SDK 重新编译该应用程序并对其进行测试，而无需升级 Mac 的操作系统。
   >
   > 在将 Mac 升级到 macOS Mojave 之前，生成并测试调用新的 macOS Mojave Api 的 Xamarin 应用程序：
   >
   > - 阅读 [Apple 发行说明](https://developer.apple.com/download/) ，了解操作系统更新。

## <a name="related-links"></a>相关链接

- [下载 Xcode 10](https://developer.apple.com/download/)
- [Xamarin 5.0 发行说明](/xamarin/mac/release-notes/5/5.0/)