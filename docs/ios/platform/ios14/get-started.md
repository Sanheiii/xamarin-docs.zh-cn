---
title: IOS 14 入门
description: 本文档介绍如何设置以构建具有 Xamarin 的 iOS 14 应用。 它讨论了如何下载 Xcode 12 和 update Visual Studio for Mac。
ms.prod: xamarin
ms.assetid: 0d721b4b-86bd-495a-8c0f-1f2f9fd6855e
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: 1bf77ca71d5fda945c81ac3ac6b338eec0164a76
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843602"
---
# <a name="get-started-with-ios-14"></a>IOS 14 入门

本文档介绍如何开始生成 Xamarin 应用，以便调用通过 Xcode 12 for iOS 14 发布的 Api。 使用预览版需要 macOS Catalina 10.15.4 或更高版本。

## <a name="download-and-install"></a>下载并安装

1. **安装 Xcode 12** -注册的 Apple 开发人员可以从 [Apple 开发人员门户](https://developer.apple.com/download/) 或 **应用商店**下载并安装最新版本的 Xcode 12。

2. **运行 Xcode 12** -在更新和运行 Visual Studio for Mac 或 Visual Studio 2019 之前运行 Xcode 12，因为它安装了 Xamarin 所需的一些工具。

3. **更新 Visual Studio for Mac 和 Visual Studio 2019** –确保具有最新稳定版本的 Xamarin。

4.  (可选) **在 ios 设备上安装 ios 14** –对于使用 Xcode 12 引入的 api 的应用的设备测试，已注册的 Apple 开发人员可以在其设备上 [下载](https://developer.apple.com/download) 并安装操作系统。 

   > [!TIP]
   > 即使你的应用程序不使用任何新的 Api，也请务必使用最新的 Xcode 12 Sdk 生成它，并对其进行测试以确保一切按预期运行。 如果应用程序不调用任何新的 Api，你可以使用这些新的 Sdk 重新编译该应用程序，并在尚未升级到新操作系统的设备上对其进行测试。
   >
   > 在将设备升级到 Apple 的最新操作系统版本之前，请务必执行以下操作：
   >
   > - 阅读 [Apple 发行说明](https://developer.apple.com/download/) ，了解操作系统更新。

## <a name="related-links"></a>相关链接

- [下载 Xcode](https://developer.apple.com/download/)
- [Xamarin.iOS 发行说明](/xamarin/ios/release-notes/14/14.0)
- [Xcode 12 发行说明](https://developer.apple.com/documentation/xcode-release-notes/xcode-12-release-notes)
