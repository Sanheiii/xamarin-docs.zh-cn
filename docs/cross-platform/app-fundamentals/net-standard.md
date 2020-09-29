---
title: 使用 .NET Standard 库来共享代码
description: 本文档介绍如何使用 .NET Standard 库来共享代码。 它讨论了如何创建 .NET Standard 库，编辑其设置，以及在应用程序中使用它。
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: 6c2f0ef1d34b60e712dd713d4fff3fe5f9856926
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458027"
---
# <a name="net-standard-library-code-sharing"></a>.NET Standard 库代码共享

.NET Standard 库具有适用于所有 .NET 平台（包括 Xamarin 和 .NET Core）的统一 API。 创建单个 .NET Standard 库，并在支持 .NET Standard 平台的任何运行时中使用它。 有关支持的平台的详细信息，请参阅 [此图表](/dotnet/standard/net-standard#net-implementation-support) 。

虽然 .NET Standard 版本1.0 到1.6 提供增量更大的 .NET Framework 子集，.NET Standard 2.0 为 Xamarin 应用程序和移植现有可移植类库提供了最佳的支持。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本部分介绍如何使用 Visual Studio for Mac 创建和使用 .NET Standard 库。

### <a name="creating-a-net-standard-library"></a>创建 .NET Standard 库

可以通过以下步骤将 .NET Standard 库添加到解决方案中：

1. 在 " **添加新项目** " 对话框中，选择 " **.net Core** " 类别，然后选择 " **.NET Standard 库**"：

    ![创建 .NET Standard 库](net-standard-images/vsm01-m157.png "创建新 .NET Standard 库")

2. 在下一个屏幕上，选择目标框架-建议使用 **.NET Standard 2.0** ：

    [![选择 .NET Standard 2。0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. 在最后一个屏幕上，键入项目名称，然后单击 " **创建**"。

4. 将显示 .NET Standard 库项目，如解决方案资源管理器所示。 "依赖关系" 节点将指示库使用 [NETStandard](https://www.nuget.org/packages/NETStandard.Library/)。

    ![解决方案中的依赖项节点指示 .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>编辑 .NET Standard 库设置

可以通过右键单击项目并选择 `Options` （如以下屏幕截图中所示）来查看和更改 .NET Standard 库设置：

![在项目选项中编辑 .NET Standard 目标框架](net-standard-images/vsm03-m157.png "在项目选项中编辑 .NET Standard 目标框架的版本")

在中，可以 `netstandard` 通过更改下拉列表值来更改的版本 `Target Framework` 。

**此外：** 可以 `.csproj` 直接编辑以更改此值。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows) 

本部分介绍如何使用 Visual Studio 创建和使用 .NET Standard 库。

### <a name="creating-a-net-standard-library"></a>创建 .NET Standard 库

将 .NET Standard 库添加到解决方案非常直接。

1. 在 " **新建项目** " 对话框中，选择 " **.NET Standard** " 类别，然后选择 " **类库 ( .NET Standard") **"。

    ![创建新的 .NET Standard 类库](net-standard-images/vs01-w157.png "创建新 .NET Standard 类库")

2. 将显示 .NET Standard 库项目，如解决方案资源管理器所示。 "依赖关系" 节点将指示库使用 [NETStandard](https://www.nuget.org/packages/NETStandard.Library/)。

    ![项目文件夹中的 NETStandard](net-standard-images/vs02-w157.png "解决方案中的 .NET Standard 项目")

### <a name="editing-net-standard-library-settings"></a>编辑 .NET Standard 库设置

可以通过右键单击项目并选择 " **属性** " 来查看和更改 .NET Standard 库设置，如以下屏幕截图所示：

![在项目属性中编辑 .NET 标准目标框架](net-standard-images/vs03-w157.png "以与其他项目相同的方式引用 .NET Standard 库")

**此外：** 可以 `.csproj` 直接编辑来编辑 `TargetFramework` 元素，并更改目标 (例如的目标版本。 `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>使用 .NET Standard 库项目

创建了 .NET Standard 库后，可以采用与通常添加引用相同的方式从任何兼容的应用程序或库项目中添加对它的引用。 在 Visual Studio 中，右键单击 "引用" 节点，然后选择 " **添加引用 ...** "，然后切换到 " **项目" > 解决方案** "选项卡，如下所示：

![引用 .NET Standard 库](net-standard-images/vs04.png "在 Visual Studio 中，右键单击 "引用" 节点，然后选择 "添加引用 ..."然后切换到 "解决方案项目" 选项卡，如下所示")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET 开发人员 (视频的 .NET Standard 和 Xamarin) 

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>相关链接

* [.NET Standard](/dotnet/standard/net-standard) -详细信息并与 PCL 进行比较。