---
title: 绑定 Android Kotlin 库
description: 本文档介绍如何创建与 Kotlin 代码的 C# 绑定，以便在 Xamarin.Android 应用程序中使用本机库。
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 4fcb36f76ff2714a7a783b716abfe44de51b2855
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454138"
---
# <a name="bind-android-kotlin-libraries"></a>绑定 Android Kotlin 库

> [!IMPORTANT]
> 我们当前正在调查 Xamarin 平台上的自定义绑定使用情况。 请参与[此调查](https://www.surveymonkey.com/r/KKBHNLT)，告诉我们将来应该进行哪些开发工作。

Android 平台及其本地语言和工具正在不断发展，并且已经有许多使用最新产品/服务开发的第三方库。 最大化代码和组件的重用性是跨平台开发的主要目标之一。 随着 Xamarin 开发人员在开发人员队伍中的受欢迎程度不断提高，重用 Kotlin 生成的组件的能力已变得越来越重要。 你可能已熟悉绑定常规 [Java](../binding-java-library/index.md) 库的过程。 现在可以使用其他文档介绍[绑定 Kotlin 库](walkthrough.md)的过程，以便 Xamarin 应用程序可以采用相同的方式使用它们。 本文档旨在介绍为 Xamarin 创建 Kotlin 绑定的高级方法。

## <a name="high-level-approach"></a>高级方法

借助 Xamarin，可以绑定任何第三方本机库，使其可供 Xamarin 应用程序使用。 由于 Kotlin 是新语言，因此，为使用该语言生成的库创建绑定需要一些其他步骤和工具。 此方法涉及以下四个步骤：

1. 生成本机库
1. 准备 Xamarin 元数据，使 Xamarin 工具能够生成 C# 类
1. 使用本机库和元数据生成 Xamarin 绑定库
1. 在 Xamarin 应用程序中使用 Xamarin 绑定库

以下各节详细介绍了这些步骤。

### <a name="build-the-native-library"></a>生成本机库

第一步是获取本机 Kotlin 库（即 AAR 包，它是一个 Android 存档）。 你可以直接向供应商索取，也可以自行生成。

### <a name="prepare-the-xamarin-metadata"></a>准备 Xamarin 元数据

第二步是准备元数据转换文件，Xamarin 工具将使用该文件来生成相应的 C# 类。 在最理想的情况下，此文件可能是空的，其中所有类均可通过 Xamarin 工具发现和生成。 在某些情况下，必须应用元数据转换才能生成正确的和/或所需的 C# 代码。 在许多情况下，必须使用 AAR 反汇编程序（如 [Java 反编译工具 (JD)](http://java-decompiler.github.io/)）来识别隐藏的依赖项，以及需要从要生成的 C# 类的最终列表中排除的不需要的类。 最终的元数据应表示引用的 Xamarin.Android 应用程序将与之交互的公共接口。

### <a name="build-a-xamarinandroid-binding-library"></a>生成 Xamarin.Android 绑定库

第三步是创建一个特殊的项目，即一个 Xamarin.Android 绑定库。 它包括 Kotlin 库作为本机引用，以及上一步中定义的元数据转换。 在撰写本文时，每个被引用的 AAR 包都需要一个单独的 Android 绑定库项目。 绑定库必须添加 [Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) 包才能支持 Kotlin 标准库。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 绑定库

第四步（也是最后一步）是在 Xamarin.Android 应用程序中引用绑定库。 通过添加对 Xamarin.Android 绑定库的引用，你的 Xamarin 应用程序可以使用该包中公开的 Kotlin 类。

## <a name="walkthrough"></a>演练

上述方法概括了为 Xamarin 创建 Kotlin 绑定所需的高级步骤。 在实践中准备这些绑定时，涉及许多较低级别的步骤，并需要考虑更多详细信息，包括适应本机工具和语言的更改。 目的是帮助你更深入地了解此概念和此过程中涉及的高级步骤。 有关详细的分步指南，请参阅 [Xamarin Kotlin 绑定演练](walkthrough.md)文档。

## <a name="related-links"></a>相关链接

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安装](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 反编译程序](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 库](https://github.com/igalata/Bubble-Picker)
- [绑定 Java 库](../binding-java-library/index.md)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 绑定元数据](../binding-java-library/customizing-bindings/java-bindings-metadata.md)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [示例项目存储库](https://github.com/xamcat/xamarin-binding-kotlin-framework)