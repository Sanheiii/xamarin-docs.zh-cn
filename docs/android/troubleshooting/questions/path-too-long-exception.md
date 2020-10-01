---
title: 如何解决 PathTooLongException 错误？
description: 本文介绍了如何解决构建应用时可能发生的 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: d58cb676b347caac00c39a381de94954219d1865
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456855"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解决 PathTooLongException 错误？

## <a name="cause"></a>原因

Xamarin.Android 项目中生成的路径名可能很长。
例如，可能会在构建过程中生成如下路径：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上（路径的最大长度为 [260 个字符](/windows/win32/fileio/naming-a-file)），如果生成的路径超过最大长度，则在构建项目时可能会生成 PathTooLongException  。 

## <a name="fix"></a>修补程序

默认情况下，将 `UseShortFileNames` MSBuild 属性设置为 `True` 以规避此错误。 如果将此属性设置为 `True` 时，构建过程将使用较短的路径名称来减少生成 PathTooLongException  的可能性。
例如，将 `UseShortFileNames` 设置为 `True` 时，以上路径将缩短为类似以下的路径：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\lp\\1\\jl\\assets**

若要手动设置此属性，请将以下 MSBuild 属性添加到项目 .csproj  文件中：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果设置此标记不修复 PathTooLongException  错误，则另一种方法通过在项目 .csproj 文件  中设置 `IntermediateOutputPath` 来为解决方案中的项目指定[公共中间输出根](/archive/blogs/kirillosenkov/using-a-common-intermediate-and-output-directory-for-your-solution)。 尝试使用相对较短的路径。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

有关设置生成属性的更多信息，请参阅[生成过程](~/android/deploy-test/building-apps/build-process.md)。