---
title: 带 Xamarin 的 DevOps
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: davidortinau
ms.author: daortin
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: 61a7017d2ba784770d1199b6332d781b36b6d0e0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452474"
---
# <a name="devops-with-xamarin"></a>带 Xamarin 的 DevOps

借助 Xamarin，你可以使用 C#、.NET 和 Visual Studio 生成面向 Android、iOS 和 Windows 的跨平台移动应用。 Xamarin 允许在平台间共享大部分代码，只有一小部分需要特定于平台。

开发适用于现代平台的应用涉及许多活动，并不仅仅只是编写代码。 这些活动被称为 DevOps（开发 + 操作），它们跨越应用的整个生命周期，包括计划和跟踪工作、设计和实现代码、管理源代码存储库、运行生成、管理持续集成和部署、测试（包括单元测试和 UI 测试）、在开发和生产环境中运行各种形式的诊断，以及通过遥测和分析实时监控应用的性能和用户行为。

Visual Studio、Azure DevOps Services 和 Team Foundation Server 提供了各种 DevOps 功能。 其中许多功能都完全适用于跨平台的项目。 对 Xamarin 应用尤其如此，因为它们利用 C# 和 .NET 生成，一些 DevOps 工具就基于此而构建。 其他工具则需要与生成和运行时环境紧密集成。 由于 Xamarin 应用在非 Windows 平台上运行，并使用 .NET 的 Mono 实现，Xamari 为某些需求提供了专用工具。

下表标识了哪些 Visual Studio DevOps 功能预期能与 Xamarin 项目很好地配合使用、哪些功能具有局限性。 请参阅链接文档，获取功能自身的详细信息。

## <a name="agile-tools"></a>敏捷工具

参考链接：[有关敏捷工具和敏捷项目管理](/azure/devops/boards/backlogs/backlogs-overview?view=azure-devops)****

常规注释：所有的计划和跟踪功能均独立于项目类型和编码语言。

|功能|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|管理积压工作和冲刺 (sprint)|是||
|跟踪工作|是||
|团队聊天室协作|是||
|看板|是||
|报告和可视化进度|是||

## <a name="modeling"></a>建模

参考链接： **[对体系结构进行分析和建模](/visualstudio/modeling/analyze-and-model-your-architecture)**

设计功能独立于编程语言，或与 C# 之类的.NET 语言配合使用。 有关与代码相关的方面，请参阅[体系结构关系图和建模图在软件开发中的角色](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools)。

|Feature|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|序列图|是||
|依赖项关系图|是||
|调用层次结构|是||
|类设计器|是||
|体系结构资源管理器|是||
|UML 关系图（用例、活动、类、组件、序列和 DSL）|是||
|层关系图|是||
|层验证|是||

## <a name="code"></a>代码

|功能|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|[使用 Team Foundation 版本控制 (TFVC)](/azure/devops/repos/tfvc/overview?view=vsts) 或 Azure Repos|是||
|[Azure Repos 中的 Git 入门](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|是||
|[提高代码质量](/visualstudio/test/improve-code-quality)|是||
|[查找代码更改和其他历史记录](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|是|排除跨特定于平台的边界，在这些地方直到运行时才会对实现进行解析。|
|[使用代码图调试应用程序](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|是||

## <a name="build"></a>生成

参考链接： **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|功能|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|本地 TFS 服务器|是|生成计算机必须安装 Xamarin，并且能够链接到 OSX 计算机以生成适用于 iOS 的应用。 请参阅 [使用 TFVC](/azure/devops/repos/tfvc/overview?view=vsts)|
|链接到 Azure Pipelines 的本地生成服务器|是|请参阅[生成和发布代理](/azure/devops/pipelines/agents/agents?view=vsts)了解相关介绍。|
|Azure Pipelines 的托管控制器服务|是|请参阅 [Build your Xamarin app](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts)（生成 Xamarin 应用）。|
|生成带有前脚本和后脚本的定义|是||
|包括封闭签入的持续集成|是|仅在 Git 用于拉取请求（而非签入）时，封闭签入才适用于 TFVC。|

## <a name="test"></a>测试

|功能|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|规划测试、创建测试用例和组织测试套件|是||
|手动测试|是||
|测试管理器（记录和播放测试）|是|仅限 Visual Studio 中的 Windows 设备和 Android 仿真器。|
|代码覆盖率|n/a||
|[单元测试代码](/visualstudio/test/unit-test-your-code/)|是|对于 Windows 和 Android 目标，可以使用内置的 MSTest 工具。 若要在 Windows、Android 和 iOS 上运行单元测试，Xamarin 建议使用 NUnit。 请参阅[使用 TFVC](/azure/devops/repos/tfvc/overview?view=vsts)。|
|[使用 UI 自动化来测试代码](/visualstudio/test/use-ui-automation-to-test-your-code/)|仅限 Windows|Visual Studio 的 UI 测试记录器仅用于 Windows。 对于所有平台，请参阅 [Xamarin.UITest](/appcenter/test-cloud/uitest/)。|

## <a name="improve-code-quality"></a>提高代码质量

参考链接： **[提高代码质量](/visualstudio/test/improve-code-quality)**

|Feature|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|[分析托管代码的质量](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|是||
|[使用代码克隆检测功能查找重复代码](/previous-versions/hh205279(v=vs.140))|是||
|[测量托管代码的复杂性和可维护性](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|是||
|[性能资源管理器](/visualstudio/profiling/performance-explorer)|否|改为通过 Visual Studio for Mac 使用 [Xamarin Profiler](../profiler/index.md) 。 请注意 Xamarin 探查器当前处于预览状态，并且尚不适用于 Windows 目标。|
|[分析 .NET Framework 内存问题](/visualstudio/misc/analyze-dotnet-framework-memory-issues)|否|Visual Studio 工具没有深入 Mono 框架进行探查的挂钩。|

## <a name="release-management"></a>发布管理

参考链接： **[在 AZURE PIPELINES 和 TFS 中生成和发布](/azure/devops/pipelines/overview?view=vsts)**

|Feature|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|管理发布进程|是||
|通过脚本部署到旁加载的服务器|是||
|上载到应用商店|部分|提供了一些扩展，这些扩展可使某些应用商店的此进程自动化。  请参阅 [Azure DevOps Services 扩展](https://marketplace.visualstudio.com/VSTS)；例如，[Google Play 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play)。|

## <a name="monitor-with-hockeyapp"></a>使用 HockeyApp 进行监控

参考链接： **[使用 HockeyApp 进行监控](https://www.hockeyapp.net/features/)**

|功能|通过 Xamarin 提供支持|其他注释|
|-------------|----------------------------|-------------------------|
|故障分析、遥测和 beta 版本分发|是||