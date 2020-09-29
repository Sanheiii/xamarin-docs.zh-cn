---
title: 绑定 iOS Swift 库
description: '本文档介绍如何创建对 Swift 代码的 c # 绑定，使其能够在 Xamarin iOS 应用程序中使用本机库和 CocoaPods。'
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: f678ec02ca5b9b47f6f78eea77547b038274190f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435511"
---
# <a name="bind-ios-swift-libraries"></a>绑定 iOS Swift 库

> [!IMPORTANT]
> 我们当前正在调查 Xamarin 平台上的自定义绑定使用情况。 请参与[此调查](https://www.surveymonkey.com/r/KKBHNLT)，告诉我们将来应该进行哪些开发工作。

IOS 平台及其本机语言和工具不断发展，并使用最新产品/服务开发了大量第三方库。 最大化代码和组件的重用性是跨平台开发的主要目标之一。 随着开发人员不断增长，使用 Swift 生成的组件的功能对 Xamarin 开发人员越来越重要。 您可能已熟悉了对常规 [目标-C](../binding-objective-c/walkthrough.md) 库的绑定过程。 现在可以使用其他文档介绍 [绑定 Swift 框架](walkthrough.md)的过程，以便 Xamarin 应用程序可以采用相同的方式使用它们。 本文档的目的是介绍为 Xamarin 创建 Swift 绑定的高级方法。

## <a name="high-level-approach"></a>高级方法

借助 Xamarin，可以绑定任何第三方本机库，使其可供 Xamarin 应用程序使用。 Swift 是新语言并为用此语言构建的库创建绑定需要一些额外的步骤和工具。 此方法涉及以下四个步骤：

1. 生成本机库
1. 准备 Xamarin 元数据，使 Xamarin 工具能够生成 c # 类
1. 使用本机库和元数据生成 Xamarin 绑定库
1. 在 Xamarin 应用程序中使用 Xamarin 绑定库

以下各节详细介绍了这些步骤。

### <a name="build-the-native-library"></a>生成本机库

第一步是使用创建了客观-C 标头的本机 Swift 框架准备就绪。 此文件是一个自动生成的标头，该标头公开了所需的 Swift 类、方法和字段，使其可通过 Xamarin 绑定库对 Ansi-c 和最终 c # 均可访问。 此文件位于以下路径中的框架内： ** \<FrameworkName> . Framework/标头/ \<FrameworkName> -Swift。** 如果公开的接口具有所有必需的成员，则可以跳到下一步。 否则，需要执行更多步骤才能公开这些成员。 此方法将取决于你是否有权访问 Swift 框架源代码：

- 如果你有权访问代码，则可以使用特性修饰必需的 Swift 成员 (s) `@objc` 并应用一些其他规则，让 Xcode 生成工具了解这些成员应公开给目标-C 环境和标头。
- 如果你没有源代码访问权限，则需要创建一个代理 Swift 框架，该框架包装原始 Swift 框架，并使用属性定义应用程序所需的公共接口 `@objc` 。

### <a name="prepare-the-xamarin-metadata"></a>准备 Xamarin 元数据

第二步是准备 API 定义接口，这些接口由绑定项目用来生成 c # 类。 这些定义可以手动创建，也可以由[目标 Sharpie](../../../cross-platform/macios/binding/objective-sharpie/index.md)工具和前面的自动生成** \<FrameworkName> 的**头文件自动创建。 生成元数据后，应手动对其进行验证和验证。

### <a name="build-the-xamarinios-binding-library"></a>生成 Xamarin iOS 绑定库

第三步是创建特殊的项目-Xamarin iOS 绑定库。 它引用在上一步准备的框架和元数据，以及相应框架所依赖的任何其他依赖项。 它还处理所引用的本机框架与使用的 Xamarin iOS 应用程序的链接。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 绑定库

第四步和最后一步是在 Xamarin iOS 应用程序中引用绑定库。 在面向 iOS 12.2 及更高版本的 Xamarin iOS 应用程序中，可以使用本机库。 对于以较低版本为目标的应用程序，需要执行一些额外的步骤：

- 为运行时支持添加 Swift .dylib 依赖项。 从 iOS 12.2 和 Swift 5.1 开始，语言变为 ABI (应用程序二进制接口) 稳定和兼容。 这就是，面向较低 iOS 版本的任何应用程序都需要包含框架使用的 Swift dylib 依赖关系。 使用 [SwiftRuntimeSupport NuGet 包](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) 自动将所需的 .dylib 依赖项包含到生成的应用程序包中。
- 添加带签名 dylib 的 **SwiftSupport** 文件夹，该文件夹在上载过程中由 AppStore 进行验证。 应使用 Xcode 工具对包进行签名并将其分发到 AppStore 连接，否则将自动拒绝此包。

## <a name="walkthrough"></a>演练

上述方法概述了为 Xamarin 创建 Swift 绑定所需的高级步骤。 在实践中准备这些绑定时，涉及许多较低级别的步骤，并需要考虑更多详细信息，包括适应本机工具和语言的更改。 目的是帮助你更深入地了解此概念和此过程中涉及的高级步骤。 有关详细的分步指南，请参阅 [Xamarin Swift 绑定演练](walkthrough.md) 文档。

## <a name="related-links"></a>相关链接

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [目标 Sharpie](../../../cross-platform/macios/binding/objective-sharpie/index.md)
- [Sharpie 元数据验证](../../../cross-platform/macios/binding/objective-sharpie/platform/verify.md)
- [绑定目标-C 框架](../binding-objective-c/walkthrough.md)
- [Gigya iOS SDK (Swift 框架) ](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5.1 ABI 稳定性](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Xamarin UITest 自动化](/appcenter/test-cloud/uitest/)
- [Xamarin UITest 配置](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [示例项目存储库](https://github.com/xamcat/xamarin-binding-swift-framework)