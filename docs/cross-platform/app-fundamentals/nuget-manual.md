---
title: 手动为 Xamarin 创建 NuGet 包
description: 本文档包含有助于构建面向 Xamarin 平台的 NuGet 包的提示。 它介绍了 NuGet 包 Xamarin 配置文件、带有平台依赖项的 PCL Nuget 以及指向各种开源示例的链接。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3e67d2c329e5ea0ad91eebcc4301965682d647aa
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457999"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手动为 Xamarin 创建 NuGet 包

_本页包含有助于构建面向 Xamarin 平台的 NuGet 包的一些提示。_

> [!NOTE]
> Xamarin Studio 6.2 (和 Visual Studio for Mac) 包括 _自动_ 从 PCL、.NET Standard 或共享项目中生成 NuGet 包的能力。 有关更多详细信息，请参阅代码共享指南中的多 [平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) 。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 包 Xamarin 配置文件

NuGet 网站 [支持多个 .NET Framework 版本和配置文件](https://docs.nuget.org/create/enforced-package-conventions) 讨论如何支持不同的 Microsoft 框架和配置文件，但不包括 Xamarin 使用的目标框架名称。

目前使用的主要 Xamarin 目标框架是：

- **MonoAndroid** -Xamarin
- **Xamarin** -xamarin ios [Unified API](~/cross-platform/macios/unified/index.md) (支持64位) 
- **Xamarin** -xamarin-xamarin 的移动配置文件，等效于 Xamarin 和 xamarin API surface。

旧的 iOS [Classic API](~/cross-platform/macios/unified/index.md)还有一个目标：

- **Monotouch.dialog** -iOS Classic API

以 **nuspec** 文件为目标，其内容如下所示：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

以上将忽略任何可移植类库。

大多数 **nuspec** 文件指定目标框架的版本号，但如果您的程序集适用于该目标框架的所有版本，它是可选的。 如果你的目标是 **lib\MonoAndroid** ，这意味着它适用于任何版本的 Xamarin。

您可以使用一组不带小数点的数字来指定版本，也可以使用小数点指定它。 如果不使用小数点，NuGet 将仅提取每个数字，并在每个数字之间插入一个 "."，从而将其转换为版本。

在上述 "MonoAndroid10" 中，表示 "Android 1.0"。 这就意味着，项目的 [目标框架](~/android/app-fundamentals/android-api-levels.md) 需要 MonoAndroid 版本1.0 或更高版本。 版本是在项目文件的元素中指定的 `<TargetFrameworkVersion>` 。

具体公式：

- **MonoAndroid403** 匹配 Android 4.0.3 和更高版本 (ie API level 15) 
- **IOS10** 与 Xamarin 1.0 和更高版本匹配
- **Xamarin 1.0** 还与 Xamarin 1.0 和更高版本匹配

## <a name="pcl-nugets-with-platform-dependencies"></a>带有平台依赖项的 PCL Nuget

PCL 配置文件限制了可访问的 .NET framework Api，它们当然无法访问特定于平台的代码。 这些第三方链接介绍了创建使用 PCL 和本机 Api 为 Xamarin 和其他平台提供兼容性的 NuGet 包的不同方法：

- [如何使可移植类库适用于你](https://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Bait 和交换机 PCL 技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [创建适用于 Xamarin 的 NuGet PCL](https://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

此外部 [的 PCL 配置文件列表及其 NuGet 目标名称](https://portablelibraryprofiles.stephencleary.com) 也是一个有用的参考。

## <a name="examples"></a>示例

可以引用的一些开源示例：

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) –使用系统 .net 编写你的应用程序，但将其放置在中，它将以更快的速度 (查看 [源](https://github.com/paulcbetts/ModernHttpClient)) 。
- [**Splat**](https://www.nuget.org/packages/Splat/) –一个用于实现跨平台的任务的库，该平台应 (查看 [源](https://github.com/paulcbetts/Splat)) 。
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) -用于在 .net 上呈现矢量图形的跨平台库 (查看 [源](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)) 。

## <a name="related-links"></a>相关链接

- [Nugetizer-3000 自动创建 NuGet](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [在项目中包括 NuGet](/visualstudio/mac/nuget-walkthrough)