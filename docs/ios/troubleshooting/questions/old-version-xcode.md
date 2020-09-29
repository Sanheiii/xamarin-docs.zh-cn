---
title: 能否使用较旧版本的 Xcode 或 Xamarin
description: 本指南概述了使用较旧版本的 Xamarin 或 Xcode (比当前稳定版本) 的问题。
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 6c6a0491f7989f2f76afabc3412e38be74a06da4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431578"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>能否使用较旧版本的 Xcode 或 Xamarin？

Xamarin 文档假设使用最新的 Xamarin 和 Xcode （建议使用）。 但是，某些客户希望使用较旧的 Xamarin 和/或 Xcode，并想要了解结果的详细信息。

发行说明包含以下警告：

> [!WARNING]
> **使用较旧的 Xcode 版本**
>
> 通常情况下，使用较旧的 Xcode 版本 (与上述 [要求](/xamarin/ios/release-notes/12/12.8#requirements)) 中所述的版本相同，但某些功能可能不可用。 此外，某些限制可能需要解决方法，例如：
>
> - 静态注册机构需要 Xcode 头文件来生成应用程序，如果缺少 Api，则会导致 [`MT0091`](../mtouch-errors.md#MT0091) [`MT4109`](../mtouch-errors.md#MT4109) 错误。 在大多数情况下，启用托管链接器会通过删除 API) 来帮助 (。
> - TvOS 和 watchOS) 的 Bitcode 生成 (可能无法提交到 App Store，除非使用 Xcode 9.0 + 工具链。

## <a name="further-information"></a>更多信息

Microsoft 强烈建议在开发和提交应用程序时使用最新的 Xcode 和最新的 Xamarin 版本。 提交应用程序时，Apple 要求使用最新的 Xcode。

请注意，使用最新的 Xcode 不会阻止应用程序面向较早的 iOS 版本。 你支持的 iOS 版本基于你的 **info.plist** 条目和你的应用程序使用的 api。

可以并行安装多个版本的 Xcode，其中包含不同的名称，如 **Xcode101** 和 **Xcode102**。 如果使用多个版本，请确保在 [Visual Studio for Mac 设置](~/ios/troubleshooting/questions/ios-sdk.md) "中使用 [`xcode-select`](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [命令行工具](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_)设置活动的 Xcode。

但罕见的情况可能需要使用较旧的组件。 此文档介绍使用最新版本时可能会遇到的一般问题。

从 Apple 的每个版本都是唯一的，你可能会遇到此处未介绍的其他缺陷。

这些挑战有时并不是很难解决的，因此，只要有可能会坚持最新的 Xcode 和最新的 Xamarin。

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>将旧的 Xamarin iOS 用于旧的 Xcode

如果不更新 Xamarin 和 Xcode，则至少会有一段时间。 限制在于，在某些时候，Apple 需要 Xcode 的最低版本来提交你的应用程序。 此时，应将 (macOS、Xcode 和 Xamarin) 的所有组件更新为最新版本 (或 Apple 所需的最新 Xcode 版本，以及匹配的 Xamarin 版本) 。

通常可以更轻松地进行更新，并跟上小更改。 对于需要更难维持更新的大型项目，使用已知的工作集会很好地折衷。

## <a name="use-of-new-xamarinios-with-older-xcode"></a>使用新的 Xamarin iOS 与较旧的 Xcode

通常，在可能的情况中，Xamarin iOS 支持较旧的 Xcode 版本。 一些潜在的挑战包括：

- 较新的 Xamarin 可能支持所选 Xcode 中不存在的某些功能和 Api。 
- **静态注册机构**需要 Xcode 头文件来生成应用程序， [`MT0091`](~/ios/troubleshooting/mtouch-errors.md#MT0091) 如果缺少 api，则会导致 [`MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109) 错误。
  - 在大多数情况下，如果未使用，则启用托管链接器将会删除新 API) 的托管绑定，从而有助于 (。
- TvOS 和 watchOS) 的 Bitcode 生成 (可能无法提交到 App Store，除非使用 Xcode 9.0 + 工具链。

## <a name="use-of-new-xcode-with-older-xamarinios"></a>将 new Xcode 与较旧的 Xamarin 一起使用

因为 Xamarin 不能预测新 Xcode 的不断变化的要求，所以这种用例更难。 MacOS 的更新还可能会导致问题，而没有兼容性修补程序的许多部分可能会受到影响。 

在许多情况下，可能会出现问题，其中包括：

- 不兼容 `mlaunch` ：
  -  (或全部) ，模拟器支持可能无法正常工作
  - 设备支持可能无法正常 (或全部) 
- 未知支持 `mtouch` 
  - 不支持新框架
  - 不支持新的权利
  - 不支持新的或更新的工具
    - 这也会影响代码签名

### <a name="new-appstore-submission-rules"></a>新 AppStore 提交规则

Apple 保留随时更新 AppStore 报送规则的权利。 仅提前公告这些规则更改。 其中一些更改需要工具更改以支持，这需要更新的 Xamarin iOS 组件。

除了规则更改之外，Apple 通常还会将其他验证添加到提交的应用或 tightens 现有的应用。 其中一些需要在我们的工具中进行更改， (例如) 个新的列入黑名单的符号。 其中许多都是由提交的客户第一次遇到的，因为没有任何公告 (或列出规则) 。

## <a name="summary"></a>总结

如果可能，请遵循 Apple 指南，并在应用商店上发布的最新 Xcode 进行开发并提交。

反过来，使用最新的 Xamarin。 这将跟踪可能影响提交的应用程序并符合最新规则更改的最新修补程序。

如果这是不可行的，请考虑使用匹配的旧 Xcode 和 Xamarin 版本。 此操作可用于一段时间，但在某些时候，Apple 会坚持使用较新的工具。