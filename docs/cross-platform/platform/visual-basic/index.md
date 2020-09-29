---
title: Visual Basic 和 .NET Standard
description: 本指南将说明如何使用 Visual Basic 来编写可用于面向 Xamarin 和 Xamarin 的解决方案的 .NET Standard 项目。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 8ccf96e266f9d5eae69a178cfcad3d1e48fb6962
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457520"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic 和 .NET Standard

Xamarin Android 和 iOS 项目不支持本机支持 Visual Basic;但是，开发人员可以使用 [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) 库将现有 Visual Basic 代码迁移到 Android 和 iOS，或在 Visual Basic 中编写其应用程序逻辑的重要部分。 Xamarin。窗体应用程序可以完全在 Visual Basic (中创建，不包括自定义呈现器、依赖项服务和 XAML 代码隐藏) 。

## <a name="requirements"></a>要求

若要创建和编译 Visual Basic .NET Standard 库，必须在 Windows (Visual Studio 2017 或更高版本) 上使用 Visual Studio。

> [!NOTE]
> 仅可使用 Visual Studio 创建和编译 Visual Basic 库。 Xamarin 和 Xamarin 不支持 Visual Basic 语言。
>
> 如果只在 Visual Studio 中工作，则可以从 Xamarin 和 Xamarin 项目引用 Visual Basic 项目。
>
> 如果你的 Android 和 iOS 项目还必须加载到 Visual Studio for Mac 应从 Visual Basic 程序集引用输出程序集。

## <a name="creating-a-visual-basicnet-net-standard-library"></a>创建 Visual Basic.NET .NET Standard 库

本部分介绍如何使用 Visual Studio 2019 创建 Visual Basic .NET Standard 库。
然后，可以在其他项目（包括 Xamarin、Xamarin 和 Xamarin）中引用该库。

在 Visual Studio 中添加 Visual Basic .NET Standard 库时，必须注意选择正确的项目类型：

1. 从 Visual Studio 2019 中选择 " **创建新项目**"。

2. 键入 " **Visual Basic 库** " 以筛选项目选项，并选择 "类库" ** ( .NET Standard ") ** " 选项和 "Visual Basic" 图标：

    [![Visual Basic 库的筛选器](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. 在下一个屏幕上，键入项目的名称，然后按 " **创建**"。

4. Visual Basic 项目将显示如下所示的  **解决方案资源管理器** 中：

    [![空 Visual Basic 项目](images/new-library-sml.png)](images/new-library.png#lightbox)

项目现已准备好 Visual Basic 要添加的代码。  (应用程序项目或库项目) 的其他项目可以引用 .NET Standard 项目。

## <a name="summary"></a>总结

本文演示了如何使用 Visual Studio 在 Xamarin 应用程序中使用 Visual Basic 代码。 尽管 Xamarin 不支持直接 Visual Basic，但将 Visual Basic 编译到 .NET Standard 库允许将使用 Visual Basic 编写的代码包含在 Android 和 iOS 应用中。

以下页面说明了如何在本机或 Xamarin 应用程序中使用 Visual Basic.NET .NET Standard 库。

- [构建使用 VB 的本机 Xamarin 和 Xamarin Android 应用](native-apps.md)
- [用 VB 生成 Xamarin 应用程序应用](xamarin-forms.md)

## <a name="related-links"></a>相关链接

- [TaskyVB (示例) ](/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (示例) ](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard 和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)