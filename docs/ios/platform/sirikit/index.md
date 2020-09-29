---
title: Xamarin 中的 SiriKit
description: 本文介绍如何在 Xamarin iOS 应用程序中使用 SiriKit，以提供可在 iOS 设备上使用 Siri 的用户访问的服务。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c50d02b4fc806a9ce466da450cfeb6afaa68df93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433701"
---
# <a name="sirikit-in-xamarinios"></a>Xamarin 中的 SiriKit

_本文介绍如何在 Xamarin iOS 应用程序中使用 SiriKit，以提供可在 iOS 设备上使用 Siri 的用户访问的服务。_

使用 iOS 10 的新功能，SiriKit 允许 iOS 应用提供使用 Siri 的用户可访问的服务，以及使用应用扩展 **和新的** 方法和 **意向 UI** 框架在 iOS 设备上映射应用的服务。

Siri 适用于 **域**的概念、相关任务的一组已知操作。 应用使用 Siri 时，每个交互都必须属于其已知的服务域之一，如下所示：

- 音频或视频呼叫。
- 预订一本。
- 管理 workouts。
- 彩信.
- 搜索照片。
- 发送或接收付款。

当用户发出涉及某个应用扩展服务的 Siri 请求时，SiriKit 将向该扩展发送 **意向** 对象，该对象描述用户的请求以及所有支持数据。 然后，应用扩展会针对给定**意向**生成相应的**响应**对象，并详细说明扩展如何处理请求。

## <a name="understanding-sirikit-concepts"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文介绍了在 Xamarin iOS 应用程序中使用 SiriKit 时所需的重要概念。 它介绍了新的方法和意向 UI 扩展点，以及它们如何与应用和用户词汇表一起使用来打开应用到 Siri。

## <a name="implementing-sirikit"></a>[实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文介绍了在 Xamarin iOS 应用程序中实现 SiriKit 支持所需的步骤。 开发人员应在尝试将 SiriKit 支持添加到应用之前，先阅读下面的 "了解 SiriKit 概念" 指南，因为已涵盖成功实现所需的关键概念。

## <a name="related-links"></a>相关链接

- [ElizaChat 示例](/samples/xamarin/ios-samples/ios10-elizachat)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意向框架参考](https://developer.apple.com/reference/intents)
- [意向 UI 框架引用](https://developer.apple.com/reference/intentsui)