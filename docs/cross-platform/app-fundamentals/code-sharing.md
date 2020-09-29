---
title: 共享代码概述
description: 本文档比较了跨平台项目之间共享代码的不同方法：共享项目、可移植类库和 .NET Standard，包括每种方法的优点和缺点。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: b097a0549db6178576d9e10eb3282c88e96b9ddb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453293"
---
# <a name="sharing-code-overview"></a>共享代码概述

_本文档比较跨平台项目之间共享代码的不同方法： .NET Standard、共享项目和可移植类库，包括每种方法的优点和缺点。_

有三种方法可以在跨平台应用程序之间共享代码：

- [**.NET Standard 库**](#Net_Standard) – .NET Standard 项目可实现在多个平台之间共享的代码，并可根据) 版本来访问大量 .net api (。 .NET Standard 1.0-1.6 实现逐渐扩大的 Api 集，而 .NET Standard 2.0 提供 .NET BCL 的最佳覆盖 (，其中包括 Xamarin 应用) 中提供的 .NET Api。
- [**共享项目**](#Shared_Projects) –使用共享资产项目类型来组织你的源代码，并 `#if` 根据需要使用编译器指令来管理特定于平台的要求。
- [**可移植类库**](#Portable_Class_Libraries) (弃用) –可移植类库 (pcl) 可以使用一个公共 API 图面以多个平台为目标，并使用接口来提供特定于平台的功能。 Visual Studio 的最新版本中不推荐 &ndash; 使用 .NET Standard。

代码共享策略的目标是支持此关系图中显示的体系结构，在该体系结构中，多个平台可使用单个基本代码。

 ![共享代码应用程序体系结构](code-sharing-images/conceptualarchitecture.png "共享代码应用程序体系结构")

本文比较了可用的方法，帮助您为应用程序选择正确的项目类型。

<a name="Net_Standard"></a>

## <a name="net-standard-libraries"></a>.NET Standard 库

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) 库提供一组定义完善的基类库，可以在不同的项目类型中引用这些库，包括 Xamarin 和 xamarin 等跨平台项目。 建议使用 .NET Standard 2.0，以最大程度地兼容现有 .NET Framework 代码。

![.NET Standard 关系图](code-sharing-images/netstandard.png ".NET Standard 关系图")

### <a name="benefits"></a>优点

- 允许你在多个项目之间共享代码。
- 重构操作始终更新所有受影响的引用。
- 与 PCL 配置文件相比，.NET 基类库 (BCL) 的图面面积更大。 具体而言，.NET Standard 2.0 与 .NET Framework 几乎是相同的 API 图面，建议用于新应用并移植现有 Pcl。

### <a name="disadvantages"></a>缺点

- 不能使用类似的编译器指令 `#if __IOS__` 。

### <a name="remarks"></a>注解

.NET Standard [类似于 PCL](/dotnet/standard/net-standard#comparison-to-portable-class-libraries)，但使用更简单的平台支持模式，并且来自 BCL 的类更多。

<a name="Shared_Projects"></a>

## <a name="shared-projects"></a>共享项目

[共享项目](~/cross-platform/app-fundamentals/shared-projects.md) 包含代码文件和任何引用它们的项目中包含的资产。 共享项目不会自行生成编译的输出。

此屏幕截图显示一个解决方案文件，其中包含 (适用于 Android、iOS 和 Windows) 的三个应用程序项目，其中包含一个包含常见 c # 源代码文件的 **共享** 项目：

![共享项目解决方案](code-sharing-images/sharedsolution.png "共享项目解决方案")

概念体系结构显示在下图中，其中每个项目都包含所有共享源文件：

![共享项目关系图](code-sharing-images/sharedassetproject.png "共享项目关系图")

### <a name="example"></a>示例

支持 iOS、Android 和 Windows 的跨平台应用程序需要适用于每个平台的应用程序项目。 常见代码位于共享项目中。

示例解决方案包含以下文件夹和项目 (为表现力选择项目名称，你的项目不必遵循这些命名准则) ：

- **共享** –包含所有项目通用的代码的共享项目。
- **AppAndroid** – Xamarin Android 应用程序项目。
- **AppiOS** – Xamarin iOS 应用程序项目。
- **AppWindows** – Windows 应用程序项目。

通过这种方式，这三个应用程序项目共享同一源代码 (共享) 中的 c # 文件。 对共享代码进行的任何编辑都将在所有三个项目之间共享。

### <a name="benefits"></a>优点

- 允许你在多个项目之间共享代码。
- 共享代码可以使用编译器指令根据平台进行分支 (例如。 `#if __ANDROID__`如[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档中所述，使用来) 。
- 应用程序项目可能包括共享代码可以使用的特定于平台的引用 (例如，  `Community.CsharpSqlite.WP7` 在 Windows Phone) 的 Tasky 示例中使用。

### <a name="disadvantages"></a>缺点

- 影响 "非活动" 编译器指令内的代码的重构不会更新这些指令中的代码。
- 与大多数其他项目类型不同，共享项目没有 "输出" 程序集。 在编译过程中，这些文件被视为引用项目的一部分，并编译到该程序集中。 如果希望将代码共享为程序集，则 .NET Standard 或可移植类库是更好的解决方案。

<a name="Shared_Remarks"></a>

### <a name="remarks"></a>注解

适用于应用程序开发人员的一个不错的解决方案，它只用于在其应用程序中共享的代码 (，而不是分发给其他开发人员) 。

<a name="Portable_Class_Libraries"></a>

## <a name="portable-class-libraries"></a>可移植类库

> [!TIP]
> 建议通过可移植类库使用 .NET Standard 2.0 库。

[此处详细讨论](~/cross-platform/app-fundamentals/pcl.md)了可移植类库。

![可移植类库关系图](code-sharing-images/portableclasslibrary.png "可移植类库关系图")

### <a name="benefits"></a>优点

- 允许你在多个项目之间共享代码。
- 重构操作始终更新所有受影响的引用。

### <a name="disadvantages"></a>缺点

- 建议在最新版本的 Visual Studio 中弃用 .NET Standard 库。 请参阅以下 [说明](/dotnet/standard/net-standard#comparison-to-portable-class-libraries) PCL 与 .NET Standard 之间的差异。
- 不能使用编译器指令。
- 仅可使用 .NET framework 的子集，由所选的配置文件决定 (参阅  [PCL 简介](~/cross-platform/app-fundamentals/pcl.md) ，了解详细信息) 。

### <a name="remarks"></a>注解

在最新版本的 Visual Studio 中，PCL 模板被视为已弃用。

## <a name="summary"></a>总结

你选择的代码共享策略将由你所面向的平台驱动。 选择最适合您的项目的方法。

.NET Standard 是构建可共享的代码库的最佳选择， (尤其是在 NuGet) 上发布。 共享项目适用于计划在其跨平台应用中使用多种平台特定功能的应用程序开发人员。

虽然在 Visual Studio 中继续支持 PCL 项目，但建议将 .NET Standard 用于新项目。

## <a name="related-links"></a>相关链接

- [构建跨平台应用程序 (主文档) ](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例 (github) ](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [使用 PCL (github 的 Tasky 示例) ](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)