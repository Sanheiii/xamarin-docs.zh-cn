---
title: macOS Mojave 简介
description: 本文档提供了 macOS Mojave 中新增和更新功能的概述。
ms.prod: xamarin
ms.assetid: 4A41CD85-C807-44C9-85AB-B5441B145A73
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: dc3def5d5c709515574a72cf9f61d71d67690d87
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429708"
---
# <a name="introduction-to-macos-mojave"></a>macOS Mojave 简介

本文档提供了 macOS Mojave 中新增和更新功能的概述。

若要开始使用 Xamarin 生成 macOS Mojave 应用，请查看[Xamarin 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/mac/xamarin.mac_5/xamarin.mac_5.0.md)的入门[指南](~/mac/platform/introduction-to-macos-mojave/get-started.md)。

## <a name="dark-mode"></a>深色模式

深色模式是在 macOS Mojave 中使用一种动态、深灰色的彩色方案来显示用户界面元素的系统范围深色主题。 它还引入了新的强调文字颜色、颜色效果和内容色调颜色，以帮助第三方应用在用户的颜色设置的情况下正常工作。

## <a name="user-notifications-framework"></a>用户通知框架

用户通知框架包含在 macOS Mojave 中，更改 Mac 应用用于处理用户通知的 Api。

## <a name="natural-language-framework"></a>自然语言框架

自然语言框架使应用程序可以执行各种类型的语言分析。 例如，它可用于标识词性的各个部分，并确定文本块所代表的语言。

## <a name="vision-framework"></a>远景框架

远景框架包含可检测各种方向的人脸的改进面部检测器。 而且，现在可以使用请求修订来选择特定的远景框架算法修订版本。

## <a name="network-framework"></a>网络框架

网络框架是 iOS 应用程序中常用的 api 基础的网络堆栈， `URLSession` 现作为独立框架提供，使其更易于使用 TCP、UDP、TLS、IPv4/IPv6 等。

## <a name="deprecations"></a>弃用功能

对于 macOS Mojave，Apple 已弃用 OpenGL ES 和 OpenCL， [鼓励开发人员](https://developer.apple.com/macos/whats-new/) 采用金属和金属绩效着色。

## <a name="related-links"></a>相关链接

- [Xamarin.Mac 示例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [macOS-Apple 开发人员](https://developer.apple.com/macos/)
- [Xamarin 5.0 发行说明](/xamarin/mac/release-notes/5/5.0/)