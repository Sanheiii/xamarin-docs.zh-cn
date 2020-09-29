---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面应用程序移植指南
description: 简单说明了如何将现有的 Windows 窗体或 WPF 应用程序分离，以创建跨平台应用程序，以便在 macOS、iOS、Android 和 UWP/Windows 10 上运行。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 0bb5a7b851a80cb92881968729e561caeaf48d12
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456842"
---
# <a name="desktop-app-porting-guidance"></a>桌面应用程序移植指南

大多数应用程序代码可以分为以下几个方面：

- 用户界面代码 (例如。 窗口和按钮) 
- 第三方控件 (例如。 图表) 
- 业务逻辑 (例如。 验证规则) 
- 本地数据存储和访问
- Web 服务和远程数据访问

对于用 c # (或 Visual Basic.NET 编写的 Windows 窗体和 WPF 应用程序) ，可以在多个平台之间共享大量的业务逻辑、本地数据访问和 web 服务代码。

## <a name="net-portability-analyzer"></a>.NET 可移植性分析器

Visual Studio 2017 和更高版本支持适用于 Windows) 的 [.Net 可移植性分析器](/dotnet/articles/standard/portability-analyzer) ([下载](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) ，可检查你的现有应用程序，并告诉你哪些代码可以 "按原样" 移植到其他平台。 可以通过此第 [9 频道视频](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)了解更多相关信息。

此外，还可以从 [GitHub 上的可移植性分析器](https://github.com/Microsoft/dotnet-apiport) 下载命令行工具，并使用该工具提供相同的报表。

## <a name="x-of-my-code-is-portable-what-next"></a>"我的代码的 x% 是可移植的。 下一步是什么？ "

但愿分析器显示您的代码的一部分是可移植的，但实际上，每个应用程序的某些部分 _不能_ 移到其他平台。

不同的代码块可能属于这些存储桶中的一种，下面将对此进行更详细的说明：

- 重新可用的可移植代码
- 需要更改的代码
- 不能移植并且需要重新写入的代码

### <a name="re-useable-portable-code"></a>重新可用的可移植代码

针对所有平台上可用的 Api 编写的 .NET 代码可以跨平台进行更改。 理想情况下，您可以将所有这些代码移到可移植类库、共享库或 .NET Standard 库中，然后在现有应用中进行测试。

然后，可以将该共享库添加到其他平台的应用程序项目中 (例如 Android、iOS、macOS) 。

### <a name="code-that-requires-changes"></a>需要更改的代码

某些 .NET Api 可能无法在所有平台上使用。 如果你的代码中存在这些 Api，你将需要重新编写这些节才能使用跨平台 Api。

这种情况的示例包括：使用 .NET 4.6 中提供的反射 Api，但在所有平台上都不可用。

使用可移植 Api 重新编写代码后，应能够将该代码打包到共享库中并在现有应用中对其进行测试。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>不能移植并且需要重新写入的代码

不可能跨平台的代码示例包括：

- **用户界面** –例如，不能在 Android 或 iOS 的项目中使用 WINDOWS 窗体或 WPF 屏幕。 需要重新编写用户界面，并将此 [控件](~/cross-platform/desktop/controls/index.md) 作为引用使用。

- **平台特定的存储** 代码，它依赖于特定于平台的技术 (例如本地 SQL Server Express 数据库) 。 你需要使用跨平台替代 (（例如用于数据库引擎) 的 SQLite）重新编写此项。
某些文件系统操作也可能需要进行调整，因为 UWP 对于 Android 和 iOS 有略微不同的 Api (例如。 某些文件系统区分大小写，其他文件系统不) 。

- **第三方组件** –检查应用程序中的第三方组件是否在其他平台上可用。 某些功能（例如非视觉 NuGet 包）可能可用，但其他一些 (特别直观的视觉对象（如图表或媒体播放器）) 

## <a name="tips-for-making-code-portable"></a>使代码可移植的技巧

- **依赖关系注入** –为每个平台提供不同的实现，

- **分层方法** –可帮助你将可移植代码与特定于平台的代码分开的其他模式。

- **消息** 传递–您可以在代码中使用消息传递来在应用程序的不同部分之间进行两次交互。