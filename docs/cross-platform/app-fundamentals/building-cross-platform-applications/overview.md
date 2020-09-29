---
title: 构建跨平台应用程序概述
description: '本文档提供了构建跨平台应用程序的高级概述。 它讨论 c # 的值、设计模式（如 MVC/MVVM）和本机 Ui。'
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 72b41854160d0810bbc4a58972f638ef920a4068
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457479"
---
# <a name="building-cross-platform-applications-overview"></a>构建跨平台应用程序概述

本指南介绍了 Xamarin 平台，并介绍了如何构建跨平台应用程序以最大程度地重复使用代码，并在所有主要移动平台上提供高质量的本机体验： iOS、Android 和 Windows Phone。

本文档中使用的方法通常适用于生产力应用和游戏应用，不过，重点在于工作效率和实用工具 (非游戏应用程序) 。 请参阅 [MonoGame 文档简介](~/graphics-games/monogame/introduction/index.md) 或查看跨平台游戏开发指南 [Visual Studio Tools for Unity](/visualstudio/cross-platform/visual-studio-tools-for-unity) 。

短语 "写入一次、在所有位置运行" 通常用于赞美在多个平台上未修改的单个代码库的优点。 尽管它具有代码重复使用的优点，但这种方法通常会导致应用程序的级别最低，并使用不适合任何目标平台的通用用户界面。

Xamarin 并非只是一次 "只写一次，可在任何位置运行" 平台，因为它的一个优点是能够为每个平台专门实现本机用户界面。 但是，在精心设计中，仍可以共享大多数非用户界面代码，并获得这两个领域的最佳优势：编写数据存储和业务逻辑代码一次，并在每个平台上呈现本机 Ui。 本文档介绍了实现此目标的通用体系结构方法。

下面总结了创建 Xamarin 跨平台应用程序的要点：

- **使用 c #** 编写你的应用（c #）。 用 c # 编写的现有代码可以很容易地通过 Xamarin 移植到 iOS 和 Android，并且很明显用于 Windows 应用程序。
- **利用 MVC 或 MVVM 设计模式** -使用模型/视图/控制器模式开发应用程序的用户界面。 使用模型/视图/控制器方法或模型/视图/ViewModel 方法来设计应用程序，在该方法中，"模型" 和其余部分之间存在明显的分隔。 确定应用程序的哪些部分将使用每个平台的本机用户界面元素 (iOS、Android、Windows 和 Mac) ，并将此应用程序拆分为两个组件： "核心" 和 "用户界面"。
- **构建本机** ui-每个特定于 OS 的应用程序都提供了不同的用户界面层 (在 c # 中实现并具有本机 UI 设计工具的帮助) ：

1. 在 iOS 上，使用 UIKit Api 创建外观真实的应用程序，还可以选择利用 Xamarin 的 iOS 设计器以可视方式创建 UI。
1. 在 Android 上，使用 Android 创建外观上的应用程序，利用 Xamarin 的 UI 设计器。
1. 在 Windows 上，你将使用在 Visual Studio 或 Blend 的 UI 设计器中创建的表示层的 XAML。
1. 在 Mac 上，你将使用在 Xcode 中创建的表示层的情节提要。

Xamarin. 在所有平台上均支持窗体项目，并允许你创建可使用 Xamarin XAML 在平台之间共享的用户界面。 

重复使用的代码量很大程度上取决于在共享内核中保存的代码量，以及特定于用户界面的代码量。 核心代码是不直接与用户交互的任何内容，而是为应用程序中将收集并显示此信息的部分提供服务。

为了增加重复使用的代码量，可以采用跨平台组件，这些组件提供所有这些系统中的通用服务，例如：

1. 用于本地 SQL 存储的[SQLite 网络](https://www.nuget.org/packages/sqlite-net-pcl/)，
1. 用于访问设备特定功能（包括相机、联系人和地理位置）的[Xamarin 插件](https://xamarin.com/plugins)
1. 与 Xamarin 项目兼容的[NuGet 包](https://nuget.org)，如[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)、
1. 为网络、web 服务、IO 等使用 .NET framework 功能。

其中一些组件是在 *Tasky* 案例研究中实现的。

 <a name="Separate_Reusable_Code_into_a_Core_Library"></a>

## <a name="separate-reusable-code-into-a-core-library"></a>将可重用代码分为核心库

通过对应用程序体系结构进行分层，然后将平台不可知的核心功能移到可重复使用的核心库中，可以将责任分离原则分离，因为下图所示：

 ![通过对应用程序体系结构进行分层，然后将平台不可知的核心功能移到可重复使用的核心库中，可以将责任分离的原则](overview-images/layers2.png)

 <a name="Case_Studies"></a>

## <a name="case-studies"></a>案例研究

本文档提供了一个案例研究– *Tasky Pro*。 每个案例研究都讨论了在实际示例中此文档中所述的概念的实现。 代码是开放源代码，在 [github](https://github.com/xamarin/mobile-samples/)上可用。