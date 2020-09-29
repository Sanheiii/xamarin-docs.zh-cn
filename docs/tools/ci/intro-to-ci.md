---
title: 与 Xamarin 持续集成简介
description: 本文档介绍与 Xamarin 的持续集成。 它讨论了版本控制和各种持续集成环境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: davidortinau
ms.author: daortin
ms.date: 07/19/2017
ms.openlocfilehash: 98606173913b57b7cf55f24c2581acbf82a587e3
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457856"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>与 Xamarin 持续集成简介

_持续集成是一种软件工程实践，在项目的版本控制存储库中，当开发人员添加或更改代码时，自动生成将编译并选择性地测试应用。本文将讨论持续集成的一般概念，以及一些可用于与 Xamarin 项目进行持续集成的选项。_

开发人员经常使用软件项目。 在某些时候，需要将所有这些工作流集成到一个构成最终产品的基本代码。 在软件开发的早期阶段，此集成是在项目结束时执行的，这是一项困难且有风险的过程。

持续集成 (CI) 通过将每个开发人员的更改合并到通用代码库中来避免这种复杂性，通常是在任何开发人员签入对项目的共享代码存储库的更改时进行。 每个签入触发自动生成并运行自动测试，以验证新引入的代码是否未破坏任何现有代码。  通过这种方式，CI 会立即显示错误和问题，并确保所有团队成员都保持最新的工作。 这会生成一个内聚且稳定的基本代码。

持续集成系统分为两个主要部分：

- **版本控制** – (VC) 的版本控制（也称为源代码管理或源代码管理）将项目的所有代码合并到单个共享存储库中，并保留每个文件的每个更改的完整历史记录。 此存储库（通常称为 *主线* 或 *master* 分支）包含最终将用于生成应用程序的生产版本或发布版本的源代码。 此任务有许多开源和商业产品，这些产品通常允许团队或人员将代码副本分叉为辅助分支，在这些分支中，他们可以进行大量更改或执行试验，而不会对主分支产生风险。 辅助分支中的更改进行验证后，它们可以合并回主分支。
- **持续集成服务器** –持续集成服务器负责收集项目的所有项目 (源代码、图像、视频、数据库、自动测试等 ) 、编译应用程序和运行自动测试。 同样，还有许多开源和商业 CI 服务器工具。

开发人员通常会在其工作站上有一个或多个分支的工作副本，其中的工作最初完成。 一组适当的工作完成后，所做的更改会 "签入" 或 "提交" 到相应的分支中，这会将其传播到其他开发人员的工作副本。 这是团队确保它们都在处理相同代码的方式。

同样，在持续集成的情况下，提交更改的行为会导致 CI 服务器生成项目，并运行自动测试来验证源代码的正确性。 如果存在生成错误或测试失败，则 CI 服务器会通过电子邮件、即时消息、Twitter、Growl 等 ) 通知负责开发人员 (，这样他或她就可以更正问题。  (CI 服务器甚至可以在发生故障时拒绝提交，这称为 "封闭签入"。 ) 

下图说明了此过程：

[![此图说明了此过程](intro-to-ci-images/intro01-small.png)](intro-to-ci-images/intro01.png#lightbox)

移动应用为持续集成带来了独特的挑战。 应用可能需要仅在物理设备上可用的传感器，如 GPS 或相机。 此外，模拟器或仿真程序只是硬件的近似值，可能会隐藏或掩盖问题。 最终，需要在真实硬件上测试移动应用，以确保它真正可供客户使用。

[App Center 测试](/appcenter/test-cloud)通过直接在数百台物理设备上测试应用来解决此特定问题。 开发人员编写自动化的验收测试，以实现功能强大的 UI 测试。 将这些测试上传到 App Center 后，CI 服务器就可以将它们作为 CI 过程的一部分自动运行，如下图所示：

[![将这些测试上传到 App Center 后，CI 服务器就可以将它们作为 CI 过程的一部分自动运行，如下图所示](intro-to-ci-images/intro02-small.png)](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>持续集成的组件

有一个广泛的商业和开源工具，旨在支持 CI。 本部分介绍一些最常见的部分。

### <a name="version-control"></a>版本控制

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) 和 [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) 是 Microsoft 的用于持续集成生成服务、任务跟踪、敏捷计划和报告工具以及版本控制的协作工具。 使用版本控制，Azure DevOps 和 TFS 可以使用其自己的 system (Team Foundation 版本控制或 TFVC) 或与 GitHub 上托管的项目一起使用。

- Azure DevOps 通过云提供服务。 其主要优点是不需要专用的硬件或基础结构，可以通过 web 浏览器从任何位置进行访问，也可以通过常用的开发工具（如 Visual Studio）进行访问，使其成为地理上分散的团队的吸引力。 对于五个开发人员或更少的团队而言，它是免费的，在此之后，可以购买更多的许可证来容纳不断增长的团队。
- TFS 专用于本地 Windows 服务器，并通过本地网络或与该网络的 VPN 连接进行访问。 其主要优点是你完全控制生成服务器的配置，并可以安装所需的任何其他软件或服务。 TFS 具有适用于小型团队的免费入门级速成版。

TFS 和 Azure DevOps 与 Visual Studio 紧密集成，使开发人员能够轻松地在单个 IDE 中执行许多版本控制和 CI 任务。 Eclipse)  (的 Team Explorer Everywhere 插件也可用。 Visual Studio for Mac 具有 [可用的 TFVC 预览](/visualstudio/mac/tf-version-control/)。

[Azure DevOps 管道](/azure/devops/pipelines/languages/xamarin/) 具有对 Xamarin 项目的直接支持，在这些项目中，你将为你要面向 (Android、IOS 和 Windows) 的每个平台创建生成定义。 每个生成定义都需要相应的 Xamarin 许可证。 出于此目的，还可以将支持 Xamarin 的本地 TFS 生成服务器连接到 Azure DevOps。 在此设置中，排队到 Azure DevOps 的生成将委托给本地服务器。 有关详细信息，请参阅 [生成和发布代理](/azure/devops/pipelines/agents/agents)。 或者，可以使用其他生成工具，如 Jenkins 或团队市县。

有关 Visual Studio、Azure DevOps 和 Team Foundation Server 的 ALM) 功能的所有应用程序生命周期 (管理的完整摘要，请参阅 [DevOps With Xamarin Apps](/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps)。

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) 为在 Visual Studio 外开发的团队提供 Team Foundation Server 和 Azure DevOps 的强大功能。 它允许开发人员从 Eclipse 或适用于 OS X 和 Linux 的跨平台命令行客户端连接到本地或云中的团队项目。 Team Explorer Everywhere 提供对版本控制的完全访问 (包括 Git) 、工作项和非 Windows 平台的生成功能。

#### <a name="git"></a>Git

[Git](https://git-scm.com) 是一个受欢迎的开源版本控制解决方案，最初开发它是为了管理 Linux 内核的源代码。 它是一种非常快速灵活的系统，最常用于各种规模的软件项目。 它可以轻松地从具有不良 Internet 访问的单一开发人员到全球范围内的大型团队进行缩放。 Git 还可使分支非常简单，进而鼓励并行开发流，同时降低风险。

Git 可以通过 web 浏览器或在 Linux、Mac OSX 和 Windows 上运行的 [GUI 客户端](https://git-scm.com/downloads/guis) 完全运行。 它可用于公共存储库;专用存储库需要 [付费计划](https://github.com/pricing)。

当前版本的 Visual Studio for Windows 和 Mac 为 Git 提供本机支持。 Microsoft 为早期版本的 Visual Studio 提供了 [适用于 Git 的可下载扩展](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) 。 如上所述，Azure DevOps 和 TFS 可以使用 Git 进行版本控制而不是 TFVC。

#### <a name="subversion"></a>Subversion

 (SVN) 的[Subversion](https://subversion.apache.org)是一个受欢迎的开源版本控制系统，自2000起已被使用。 SVN 在所有新式版本的 OS X、Windows、FreeBSD、Linux 和 Unix 上运行。 Visual Studio for Mac 具有对 SVN 的本机支持。 有第三方扩展将 SVN 支持引入 Visual Studio。

### <a name="continuous-integration-environments"></a>持续集成环境

设置持续集成环境意味着将版本控制系统与生成服务结合使用。  对于后者，最常见的两个是：

- [Azure Pipelines](/azure/devops/pipelines/) 是 Azure DEVOPS 和 TFS 的生成系统。 它与 Visual Studio 紧密集成，使开发人员能够更方便地触发生成、自动运行测试和查看结果。
- Jenkins 是一台开源 CI 服务器，具有丰富的插件生态系统，可支持各种软件开发。 它在 Windows 上运行，Mac OS X。 Jenkins 未与任何特定 IDE 集成。 而是通过 web 界面进行配置和管理。 Jenkins CI 也易于安装和配置，使其对小型团队很有吸引力。

可以单独使用 TFS/Azure DevOps，也可以将 Jenkins 与 TFS/Azure DevOps 或 Git 结合使用，如以下部分中所述。

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

如前文所述，Azure DevOps 和 Team Foundation Server 提供了版本控制和生成服务。 对于每个目标平台，生成服务始终需要 Xamarin Business 或 Enterprise 许可证。

使用 Azure DevOps，你可以为每个目标平台创建单独的生成定义，并在其中输入适当的许可证。 配置后，Azure DevOps 将在云中运行生成和测试。 有关更多详细信息，请参阅 [Azure Pipelines](/azure/devops/pipelines/) 。

在 Team Foundation Server 中，将生成计算机配置为针对特定目标平台执行以下操作：

- **Android 和 Windows：** 安装 Visual Studio 和适用于 Android 和 Windows 的 Xamarin 工具 () 并配置 Xamarin 许可证。 还需要将 Android SDK 移动到 TFS 生成代理可在其中找到的服务器上的共享位置。 有关详细信息，请参阅 [配置 TFVC](/azure/devops/repos/tfvc/overview)。
- **iOS 和 Xamarin：** 在 Windows server 上安装 Visual Studio 和 Xamarin 工具并提供相应的许可证。 然后，将 Visual Studio for Mac 安装在可通过网络访问的 Mac OS X 计算机上，该计算机将充当生成主机，并为适用于 iOS 的应用创建最终应用程序包 (适用于 OS X 的应用) 。

下图说明了此拓扑：

[![此图说明了此拓扑](intro-to-ci-images/intro03-small.png)](intro-to-ci-images/intro03.png#lightbox)

还可以将本地 TFS 服务器链接到 Azure DevOps 项目，以便将 Azure DevOps 构建委托给本地服务器。 有关详细信息，请参阅 [生成和发布代理](/azure/devops/pipelines/agents/agents/)。

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps 和 Jenkins

如果使用 Jenkins 生成应用，则可以将代码存储在 Azure DevOps 或 Team Foundation Server 中，并继续为 CI 生成使用 Jenkins。 当你将代码推送到你的团队项目的 Git 存储库或将代码签入 TFVC 时，可以触发 Jenkins 生成。 有关详细信息，请参阅 [Jenkins With Azure DevOps](/azure/devops/service-hooks/services/jenkins)。

[![如果使用 Jenkins 生成应用，则可以将代码存储在 Azure DevOps 或 Team Foundation Server 中，并继续对 CI 生成使用 Jenkins](intro-to-ci-images/intro04-small.png)](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git 和 Jenkins

另一种常见的 CI 环境可以是完整的操作系统 X。 此方案涉及使用 Git 进行源代码控制，并使用 Jenkins 生成服务器。 这两个在安装 Visual Studio for Mac 的单个 Mac OS X 计算机上运行。 这非常类似于上一节中讨论的 Azure DevOps + Jenkins 环境：

[![这非常类似于上一节中讨论的 Azure DevOps + Jenkins 环境](intro-to-ci-images/intro05-small.png)](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **[Microsoft 不支持](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)Jenkins。**