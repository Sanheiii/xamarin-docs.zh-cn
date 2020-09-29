---
title: 'NuGet 多平台库项目 (称为 Nugetizer 3000) '
description: 本文档介绍如何使用 Nugetizer 3000 工具自动创建 NuGet 包，以便跨平台共享代码。
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: f0d1195d9159623ec865b1ea1fec26d9969925ca
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456595"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>NuGet 多平台库项目 (Nugetizer 3000) 

_使用 "Nugetizer 3000" 自动创建 NuGet 包，以便跨平台共享代码！_

使用 _Nugetizer 3000_，可以自动创建 NuGet 包，以便跨平台共享代码。 这使得可以从现有库项目或通过创建新的多 **平台库项目**来创建 NuGet 包。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Nugetizer 3000 随附在 &ndash; **文件 > "新建**" 窗口中 Visual Studio for Mac 查找**库 > 库**项目类型：

[![创建新的多平台库窗口](images/mulitplatform-library-sml.png)](images/mulitplatform-library.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中使用 Nugetizer 3000，请 [下载并运行 VSIX 安装程序](https://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>构建 NuGet 包

配置完成后，项目的每个版本都将输出完整的 NuGet 包，可用于在内部与其他应用程序共享代码或将代码上传到 [NuGet.org](https://www.nuget.org)。

使用此功能的方案有三种：

- [现有库项目](existing-library.md)

  从现有 PCL (或 .NET Standard) 项目创建 NuGet 包。

- [创建新的多平台库项目](single-codebase.md)

  使用 PCL 或 .NET Standard 创建新的库，以便通过 NuGet 共享公共代码。

- [创建新的特定于平台的库项目](platform-specific.md)

  创建一个新的库和 NuGet，其中包含适用于 iOS 和 Android 的特定于平台的代码，并使用共享项目来包含通用代码和特定于平台的项目，以支持特定于 iOS 或 Android 的功能。

有关必须添加到任何 NuGet 包的必需和可选元数据的详细信息，请参阅 [元数据指南](metadata.md) 。

## <a name="further-nuget-information"></a>其他 NuGet 信息

详细了解如何 [为 Xamarin 手动创建 nuget](~/cross-platform/app-fundamentals/nuget-manual.md) ，以及如何 [在应用中包含 NuGet 包](/visualstudio/mac/nuget-walkthrough)。

Microsoft 的 [NuGet 文档](/nuget/) 包含有关 **nupkg** 格式的更多详细信息，以及如何在 Visual Studio 中使用 NuGet 包。

 (也称为的 NuGet 包项目的设计讨论 NuGetizer 3000) 在 [NuGet GitHub 存储库](https://github.com/NuGet/Home/wiki/NuGetizer-3000)中提供。

## <a name="related-links"></a>相关链接

- [NuGetizer-3000 用例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [手动为 Xamarin 创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文档](/nuget/)