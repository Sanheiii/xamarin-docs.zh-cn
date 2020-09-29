---
title: Xamarin.Mac 扩展支持
description: 本文档介绍 Xamarin for Finder、Share 和今日扩展的支持。 它将检查限制和已知问题、指向演练和示例应用的链接，并提供使用扩展的技巧。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: abb14f2074a2f519037ef2a9e85f2bf66613b836
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436789"
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 扩展支持

在 Xamarin 2.10 中，为多个 macOS 扩展点添加了支持：

- 程序
- 共享
- 今天

<a name="Limitations-and-Known-Issues"></a>

## <a name="limitations-and-known-issues"></a>限制和已知问题

下面是在 Xamarin 中开发扩展时可能会出现的限制和已知问题：

- Visual Studio for Mac 中当前没有调试支持。 所有调试都需要通过 **NSLog** 和 **控制台**完成。 有关详细信息，请参阅下面的提示部分。
- 扩展必须包含在主机应用程序中，该应用程序运行一次并向系统注册一次。 然后必须在 "**系统首选项**" 的 "**扩展**" 部分启用。 
- 某些扩展崩溃可能会使主机应用程序不稳定，并导致异常行为。 特别是，**通知中心**的 "**查找**器" 和 "**今日**" 部分可能会变为 "卡住" 并无响应。 这也是在 Xcode 中的扩展项目中遇到的，当前与 Xamarin 无关。 通常可以通过 **控制台**在系统日志 (中查看此信息，有关详细信息) 打印重复的错误消息，请参阅提示。 重新启动 macOS 将会解决此问题。

<a name="Tips"></a>

## <a name="tips"></a>提示

使用 Xamarin 中的扩展时，以下提示会很有用：

- 因为 Xamarin 目前不支持调试扩展，所以调试体验主要依赖于执行和 `printf` like 语句。 但是，扩展在沙盒进程中运行，因此 `Console.WriteLine` 不会像在其他 Xamarin 应用程序中那样工作。 [ `NSLog` 直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)调用会将调试消息输出到系统日志。
- 任何未捕获的异常都将导致扩展过程崩溃，仅在 **系统日志**中提供少量有用的信息。 在 `try/catch` 重新引发之前，在 (异常) 块中包装有麻烦的代码 `NSLog` 可能会很有用。
- 可以从**应用**程序实用工具下的**控制台**应用程序访问**系统日志**  >  **Utilities**：

    [![系统日志](extensions-images/extension02.png)](extensions-images/extension02.png#lightbox)
- 如上所述，运行扩展主机应用程序会将其注册到系统。 删除应用程序包，并将其取消注册。 
- 如果注册了应用扩展的 "游离" 版本，请使用以下命令将它们查找 (以便可以将其删除) ： `plugin kit -mv`

<a name="Walkthrough-and-Sample-App"></a>

## <a name="walkthrough-and-sample-app"></a>演练和示例应用

由于开发人员将使用与 Xamarin iOS 扩展相同的方式来创建和使用 Xamarin 扩展，请参阅我们的 [扩展文档简介](~/ios/platform/extensions.md) 了解更多详细信息。

可在 [此处](/samples/xamarin/mac-samples/extensionsamples)找到包含每个扩展类型的小型工作示例的 Xamarin Mac 项目示例。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文大致介绍了如何在 Xamarin 版本 2.10 (和更高版本) 应用中使用扩展。

## <a name="related-links"></a>相关链接

- [了解 Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](/samples/xamarin/mac-samples/extensionsamples)
- [macOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)