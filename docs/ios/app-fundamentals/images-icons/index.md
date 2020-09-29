---
title: Xamarin 中的图像和图标
description: 本部分包括一系列文章，这些文章介绍如何在 Xamarin iOS 应用程序中使用图像，如将其用作图标、启动屏幕或在控件中包含它们以及为自定义文档类型提供图标。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6048690bc68c3998b67dc89fdc191ea9158ae952
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437267"
---
# <a name="images-and-icons-in-xamarinios"></a>Xamarin 中的图像和图标

_本部分包括一系列文章，这些文章介绍如何在 Xamarin iOS 应用程序中使用图像，如将其用作图标、启动屏幕或在控件中包含它们以及为自定义文档类型提供图标。_

可以通过多种方式在 iOS 应用中使用映像资产。 从简单地将图像显示为应用程序 UI 的一部分，将其分配给 UI 控件（如 `UIButton` 或）， `UIImageView` 以提供图标和启动屏幕，Xamarin 使你可以通过以下方式轻松地将精彩图稿添加到 iOS 应用： 

- **独立于分辨率的图像** –使用 iOS 的内置支持，在不同设备分辨率和类型 (IPhone、iPad 等 ) 中处理图像。
- **资产目录图像集** -使用 **资产目录图像集** 来管理应用所需的给定映像资产的所有版本并对其进行分组。
- **Ios 设计器中的图像** -使用 Ios 设计器设置控件的图像。
- **代码中的图像** –使用 `UIImage` 类的方法加载和处理图像资产，并使用 c # 代码将其分配给 UI 控件。
- **应用程序图标** -定义每个 iOS 应用所需的应用图标。 这是用户将在 iOS 主屏幕上点击以启动应用程序的图标。 此外，Game Center 使用此图标（如果适用）。
- **聚光灯图标** -定义应用的聚焦图标。 每当用户在聚焦搜索中输入应用名称时，就会显示此图标。
- **设置图标** -定义应用的 **设置** 图标。 如果用户在其 iOS 设备上输入 " **设置** " 应用，则此图标将显示在应用的 "设置" 列表的末尾。 
- **启动屏幕** -定义应用程序的启动屏幕。 用户点击 "应用" 图标并显示第一个视图之前，将显示一个空白屏幕。 幸运的是，iOS 支持通过使用情节提要来显示图像以代替空白屏幕。 
- **ITunes 图标** -提供 iTune 图标。 如果使用向企业用户提供应用的即席方法 (或在真实设备上进行 beta 测试) ，则开发人员还需要包含一个512x512 和一个1024x1024 映像，该映像将用于表示 iTunes 中的应用。
- **文档图标** -使用图像作为 Xamarin iOS 应用支持或创建的任何特定文档类型的图标。

为 iOS 应用程序创建图像资产时，应考虑几个注意事项，以及这些资产将使用的多个位置。 其中每个都影响的是需要多少映像资产，而不是创建这些资产的方式。 以下主题介绍了所需的映像的类型、这些资产在应用程序捆绑包中的包含方式以及如何使用映像资产提供所需的功能：

## <a name="displaying-an-image"></a>[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文介绍如何在 Xamarin iOS 应用中包括图像资产，并使用 c # 代码或通过将其分配给 iOS 设计器中的控件来显示该图像。

## <a name="application-icons"></a>[应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)

本文介绍如何在要用作应用图标的 Xamarin iOS 应用中包括和管理映像资产。

## <a name="alternate-app-icons"></a>[备用应用图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple 向 iOS 10.3 添加了几项增强功能，使应用程序可以管理其图标：

- `ApplicationIconBadgeNumber` -获取或设置 Springboard 中应用程序图标的徽章。
- `SupportsAlternateIcons` -如果 `true` 应用程序具有一组备用图标，则为。
- `AlternateIconName` -返回当前选定的备用图标的名称， `null` 如果使用主图标，则返回。
- `SetAlternameIconName` -使用此方法将应用的图标切换到给定的替代图标。

## <a name="launch-screens"></a>[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文介绍如何使用一种特殊类型的情节提要为每个 iOS 设备大小和分辨率提供通用启动屏幕。

## <a name="custom-document-types"></a>[自定义文档类型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

本文介绍如何在 Xamarin iOS 应用中包括和管理要用作自定义文档类型图标的图像资产。

## <a name="related-links"></a>相关链接

- [使用图像 (示例) ](/samples/xamarin/ios-samples/workingwithimages)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)