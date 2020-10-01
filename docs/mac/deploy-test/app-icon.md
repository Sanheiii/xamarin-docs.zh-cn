---
title: Xamarin.Mac 应用的应用程序图标
description: 本文介绍如何创建 Xamarin.Mac 应用程序图标所需的图像，将图像捆绑到 .icns 文件以及将该图标包含在 Xamarin.Mac 项目中。
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fdc132143905db6cc32ebd9aeb07268e69bf2070
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434679"
---
# <a name="application-icon-for-xamarinmac-apps"></a>Xamarin.Mac 应用的应用程序图标

 本文介绍如何创建 Xamarin.Mac 应用程序图标所需的图像，将图像捆绑到 .icns 文件以及将该图标包含在 Xamarin.Mac 项目中。

## <a name="overview"></a>概述

在 Xamarin.Mac 应用程序中使用 C# 和 .NET 时，开发人员有权访问其在 *Objective-C* 和 *Xcode* 中操作时访问的相同图像和图标工具。

一个很好的图标需指明 Xamarin.Mac 应用的主要用途，并指出用户在使用应用时应期待的体验。 本文介绍了所有必需步骤，帮助你创建图标所需的图像资产，将这些资产打包到 `AppIcon.appiconset` 文件中并在 Xamarin.Mac 应用中使用该文件。

![AppIcon.appiconset 编辑器](app-icon-images/intro01.png "AppIcon.appiconset 编辑器")

## <a name="application-icon"></a>应用程序图标

一个很好的图标需指明 Xamarin.Mac 应用的主要用途，并指出用户在使用应用时应期待的体验。 每个 macOS 应用都必须包括几种图标大小，以便在 Finder、Dock、Launchpad 和计算机中的其他位置显示。

## <a name="designing-the-icon"></a>设计图标

Apple 建议在设计应用程序的图标时使用以下技巧：

- 考虑给图标设计一个真实且唯一的形状。
- 如果 macOS 应用具有 iOS 对应，则不要重复使用 iOS 应用的图标。
- 使用用户可轻松识别的通用图像。
- 力求简单。
- 使用少许颜色和阴影有助于图标展示应用的相关内容。
- 避免混用实际文本和_以马赛克形式显示的_文本/行来暗示文本。
- 创建图标主题的理想化版本而不是使用真实的照片。
- 避免在图标中使用 macOS UI 元素。
- 不要在图标中使用 Apple 图标的副本。

在设计 Xamarin.Mac 应用的图标之前，请阅读 Apple [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[应用图标库](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1)和[设计应用图标](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1)部分。

## <a name="required-image-sizes-and-filenames"></a>所需图像大小和文件名

像开发人员将在 Xamarin.Mac 应用中使用的任何其他图像资源一样，应用图标需要提供有标准和 Retina 分辨率版本。 同样，与任何其他图像一样，命名图标文件时请使用 `@2x` 格式：

- 标准分辨率    -   ImageName **.**  filename-extension（示例：  icon_512x512.png）
- 高分辨率    -   ImageName **@2x.**  filename-extension（示例：icon_512x512@2x.png  ）

例如，若要提供应用图标的 512 x 512 版本，文件将命名为  icon_512x512.png 和 icon_512x512@2x.png  。

若要确保图标在用户会看到它的所有位置良好呈现，请提供以下所列大小的资源：

|Filename|大小（像素）|
|---|---|
|icon_512x512@2x.png|1024x1024|
|icon_512x512.png|512x512|
|icon_256x256@2x.png|512x512|
|icon_256x256.png|256x256|
|icon_128x128@2x.png|256x256|
|icon_128x128.png|128x128|
|icon_32x32@2x.png|64x64|
|icon_32x32.png|32x32|
|icon_16x16@2x.png|32x32|
|icon_16x16.png|16x16|

有关详细信息，请参阅 Apple 的 [Provide High-Resolution Versions of All App Graphics Resources](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3)（提供所有应用图形资源的高分辨率版本）文档。

## <a name="packaging-the-icon-resources"></a>打包图标资源

设计图标并将其保存为所需的文件大小和名称后，Visual Studio for Mac 就可将其请轻松分配给图像资产，以在 Xamarin.Mac 中使用。

请执行以下操作：

1. 在“Solution Pad”  中，打开  “Assets.xcassets” >   “AppIcons.appiconset”： 

    ![编辑 AppIcon.appiconset](app-icon-images/intro01.png "编辑 AppIcon.appiconset")
2. 对于每个所需的图标大小，单击图标并选择上面创建的相应图像文件： 

    [![选择图标图像](app-icon-images/intro02.png "选择图标图像")](app-icon-images/intro02-large.png#lightbox)
3. 保存更改。

## <a name="using-the-icon"></a>使用图标

生成 `AppIcon.appiconset` 文件后，需要将其分配给 Visual Studio for Mac 中的 Xamarin.Mac 项目。

请执行以下操作：

1. 在“Solution Pad”  中双击 **Info.plist** 打开“项目选项”  。
2. 在“Mac OS X 应用程序目标”  部分，单击“应用图标”  选择 `AppIcon.appiconset` 文件： 

    [![设置图标集](app-icon-images/icon01.png "设置图标集")](app-icon-images/icon01-large.png#lightbox)
3. 保存更改。

运行应用时，将在停靠中显示新图标：

![macOS Dock 中应用图标的示例](app-icon-images/icon04.png "macOS Dock 中应用图标的示例")

## <a name="summary"></a>总结

本文详细介绍了如何使用创建 macOS 应用图标所需的图像、打包图标以及在 Xamarin.Mac 项目中包含图标。

## <a name="related-links"></a>相关链接

- [MacImages（示例）](/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用图像](~/mac/app-fundamentals/image.md)
- [macOS 人机接口指南 - 图标和图像](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/)
- [有关适用于 OS X 的高分辨率](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
- [Icns 生成器](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12)