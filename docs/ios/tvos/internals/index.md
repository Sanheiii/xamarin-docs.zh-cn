---
title: Xamarin 中的 tvOS –内部
description: 描述基于 Xamarin 的 Xamarin 上的 tvOS 的内部工作原理的文档。 链接内容讨论程序集、目标框架和相关的 iOS 概念。
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 4712b7b75e735da047d7f44f7c6c47f42b9ad7a8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030663"
---
# <a name="tvos-in-xamarin-internals"></a>Xamarin 中的 tvOS –内部 

## <a name="assembliesiostvosinternalsassembliesmd"></a>[程序集](~/ios/tvos/internals/assemblies.md)

Xamarin 为你的 tvOS 应用程序支持的程序集列表。

## <a name="target-frameworksiostvosinternalsframeworksmd"></a>[目标框架](~/ios/tvos/internals/frameworks.md)

本文介绍了 tvOS 中提供的目标框架（基类库）的类型，以及为 tvOS 应用程序选择特定目标的影响。

## <a name="related-ios-articles"></a>相关的 iOS 文章

以下文章特定于 iOS，但与 tvOS 相关，因为 tvOS 9 是 iOS 9 的子集。

### <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[Unified API](~/cross-platform/macios/unified/index.md)

引入了新的统一 Api，使 Apple TV 和 iOS 基本代码之间的代码共享变得更简单，并引入了对64位 Api 和64位编译的支持。  

### <a name="api-designiosinternalsapi-designindexmd"></a>[API 设计](~/ios/internals/api-design/index.md)

介绍 API 绑定背后的设计原则。

### <a name="limitationsiosinternalslimitationsmd"></a>[限制](~/ios/internals/limitations.md)

本部分说明了有关 Xamarin 的缺陷和限制，其中许多应用于 tvOS。

### <a name="linkeriosdeploy-testlinkermd"></a>[链接器](~/ios/deploy-test/linker.md)

说明链接器如何工作以确保尽可能小的应用程序包，以及如何修改其设置和用法。

### <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[本地化和国际化](~/ios/app-fundamentals/localization/index.md)

本指南介绍如何向 Xamarin iOS 应用程序添加编码以支持国际化。

### <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

有关 mtouch.exe 的注意事项和信息，该命令行工具用于将项目内置到 iOS 可使用的应用程序中。

### <a name="linking-native-librariesiosplatformnative-interopmd"></a>[链接本机库](~/ios/platform/native-interop.md)

Xamarin 支持与本机 C 库和目标 C 库链接。 本文档介绍如何将本机 C 库与你的 Xamarin iOS 项目链接在一起。 有关为目标 C 库执行相同操作的详细信息，请参阅&nbsp;文档中的&nbsp;[绑定目标-C 类型](~/ios/platform/binding-objective-c/index.md)。

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[目标-C 选择器](~/ios/internals/objective-c-selectors.md)

直接调用目标 C 选择器（方法）的说明和使用情况。

### <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

有关使用 System.object 访问内置 SQLite 数据库系统的信息和说明。

### <a name="threadingiosapp-fundamentalsthreadingmd"></a>[线程处理](~/ios/app-fundamentals/threading.md)

有关在 Xamarin iOS 应用程序中使用线程处理的说明。

### <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 代码生成](~/ios/internals/xib-code-generation.md)

Visual Studio for Mac 如何与 Xcode 的 Interface Builder 集成，以便你可以使用 Interface Builder 来设计 UI。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
