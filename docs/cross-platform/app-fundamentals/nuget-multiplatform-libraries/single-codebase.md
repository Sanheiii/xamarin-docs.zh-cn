---
title: 为 NuGet 创建新的多平台库
description: 本文档介绍如何创建用于 NuGet 的多平台库。 此方法适用于可完全在 .NET 基类库中表达的业务逻辑和算法，因此在没有特定于平台的代码的所有目标平台上运行。
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 3226820dddbd6ecb83b87b29ef1991d19104b2a6
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936976"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>为 NuGet 创建新的多平台库

创建使用 PCL 或 .NET Standard 的多平台库项目意味着生成的 NuGet 可以添加到支持目标配置文件的任何 .NET 项目，包括 ASP.NET 项目，或使用 WinForms、WPF 或 UWP 的桌面应用程序。

库只能包含所选 PCL 或 .NET Standard 配置文件支持的代码，以及任何其他添加的 Nuget。
这适用于可以完全用 .NET 基类库表达的业务逻辑和算法。

创建一个程序集，并将其内置于 NuGet 包中。

如果以后需要特定于平台的功能，则[可以添加特定于平台的项目](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>创建多平台库 NuGet 的步骤

1. 选择 "**文件" > "新建解决方案**" （或者右键单击现有解决方案，然后选择 "**添加 >" 新建项目**"）。

2. 从多**平台 > 库**部分中选择多**平台库**：

   [![为单个基本代码配置多平台库](single-codebase-images/mulitplatform-library-sml.png)](single-codebase-images/mulitplatform-library.png#lightbox)

3. 输入**名称**和**说明**，并**为所有平台**选择 "单个"：

   [![为单个基本代码配置多平台库](single-codebase-images/single-configure-sml.png)](single-codebase-images/single-configure.png#lightbox)

4. 完成向导。 在解决方案中创建单个库项目。

5. 右键单击新库项目，然后选择 "**选项**"。 "**生成 > 常规**" 部分允许设置**目标框架**–选择 ".net 可移植 PCL 配置文件" 或 ".NET Standard 版本"：

   [![为库类型选择 PCL 或 .NET Standard](single-codebase-images/single-choose-type-sml.png)](single-codebase-images/single-choose-type.png#lightbox)

6. 同时，在 "**项目选项**" 窗口中，打开**NuGet 包 "> 元数据**" 部分，并输入[所需的元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何可选的元数据）：

   [![输入所需的元数据](single-codebase-images/single-metadata-sml.png)](single-codebase-images/single-metadata.png#lightbox)

7. 右键单击库项目，然后选择 "**创建 NuGet 包**" （或 "生成或部署解决方案"），并将**nupkg** NuGet 包文件保存在 **/Bin/** 文件夹中（"调试" 或 "发布"，具体取决于配置）：

   ![NuGet 包文件将保存在 bin 文件夹中，无论是调试还是发布，具体取决于配置](single-codebase-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>验证输出

NuGet 包也是 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了基于 PCL 的 NuGet 的内容–只包含一个 PCL 程序集：

![NuGet 包中包含的文件](single-codebase-images/nuget-output.png)

<a name="add-platforms"></a>

## <a name="adding-platform-specific-code"></a>添加特定于平台的代码

基于 PCL 的项目和基于 .NET Standard 的项目不能包含特定于平台的引用（例如 iOS 或 Android 功能）。

如果需要扩展现有 PCL 项目或 .NET Standard 项目以包含特定于平台的代码，则可以通过右键单击该项目并选择 "**添加 > 添加平台实现 ...**" 来完成此操作：

[![添加平台实现菜单](single-codebase-images/add-later-sml.png)](single-codebase-images/add-later.png#lightbox)

可以将一个或多个平台项目添加到解决方案中，并且可以选择将现有 PCL 或 .NET Standard 库转换为共享项目：

[![添加平台选项，如 iOS、Android 和共享项目](single-codebase-images/add-later-platforms-sml.png)](single-codebase-images/add-later-platforms-sml.png#lightbox)

转换为共享项目后，请访问**项目选项 > NuGet 包 > 引用程序集**" 
 [部分](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)，并确保选择任何所需的配置文件（以便 NuGet 继续与以前用于的项目兼容）。

## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
