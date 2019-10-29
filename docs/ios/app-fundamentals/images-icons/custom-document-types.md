---
title: Xamarin 中的自定义文档图标
description: 本文介绍如何在 Xamarin iOS 应用中包括和管理要用作自定义文档类型图标的图像资产。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: ac8ee96d6183f9a62233d217c75b03da15605bd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004226"
---
# <a name="custom-document-icons-in-xamarinios"></a>Xamarin 中的自定义文档图标

_本文介绍如何在 Xamarin iOS 应用中包括和管理要用作自定义文档类型图标的图像资产。_

如果 Xamarin iOS 应用支持加载特定的文档类型，则开发人员可以提供系统在遇到该文档类型时将使用的图标，例如，当用户在*邮件应用程序*中按下某个附件时，如下所示：

 [![](custom-document-types-images/17.png "An example of document type icons")](custom-document-types-images/17.png#lightbox)

开发人员可以通过在应用的 `Info.plist`中包含 `CFBundleTypeName` 字符串的字典条目和 `LSItemContentTypes` 数组，为应用可以打开的文件格式添加文档类型信息。 文档类型的图标在 `CFBundleTypeIconFiles` 数组中。 如果未提供文档图标，则 iOS 将从应用程序图标派生一个。
可以为各种设备分辨率提供多种大小的图标。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中分配这些值，请使用 "`Info.plist` 编辑器" 的 "**高级**" 选项卡下的 "**文档类型**" 部分添加文档类型并向其分配图像图标。 例如，以下是显示 PDF 支持注册的屏幕截图：

 [![](custom-document-types-images/18.png "The Document Types section under the Advanced tab on the `Info.plist` editor")](custom-document-types-images/18.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中分配这些值，请使用 `Info.plist`上 "**高级**" 选项卡下的 "**文档类型**" 部分：

 ![](custom-document-types-images/doc01w.png "Open the Document Types section under the Advanced tab")

单击 "**添加文档类型**" 按钮并填写必填字段：

![](custom-document-types-images/doc02w.png "The Add Document Type form")

-----

有关文档类型的详细信息，请参阅适用于 iOS 的 Apple[统一类型标识符参考](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)和[文档交互编程主题](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。

## <a name="related-links"></a>相关链接

- [使用图像（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
