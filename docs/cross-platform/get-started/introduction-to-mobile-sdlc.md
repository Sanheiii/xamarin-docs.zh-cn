---
title: 移动软件开发生命周期
description: 本文介绍了移动软件开发生命周期，以及 UX 设计、UI 设计、开发、稳定、分发等。
ms.prod: xamarin
ms.assetid: 420c5fdf-4610-4e71-9db5-fe894c961924
author: davidortinau
ms.author: daortin
ms.date: 11/22/2016
ms.openlocfilehash: b08293727a585ff68c4bac8a25b26d249505b1aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016400"
---
# <a name="mobile-software-development-lifecycle"></a>移动软件开发生命周期

生成移动应用程序可以像打开 Visual Studio、将一些内容拖放在一起、快速做些测试，并提交到 App Store 一样简单 - 一个下午即可完成所有操作。 也可以是特别复杂的过程，包括严苛的预先设计，可用性测试，在数以千计的设备上进行 QA 测试，完整的 beta 生命周期，然后通过很多不同的方式进行部署。

在本文档中，我们将全面介绍如何生成移动应用程序，包括：

1. **过程** – 软件开发的过程称为软件开发生命周期 (SDLC)。 我们将介绍与移动应用程序开发有关的 SDLC 的所有阶段，包括：开始、设计、开发、稳定、部署和维护。
1. **注意事项** – 生成移动应用程序时有一些注意事项，尤其是与传统 Web 或桌面应用程序相比。 我们将介绍这些注意事项以及它们会如何影响移动开发。

本文档旨在面向初学者和有经验的应用程序开发人员这类人员，回答有关移动应用开发的基本问题。 它采用相当全面的方法来介绍在整个软件开发生命周期 (SDLC) 过程中会遇到的大多数概念。 但是，本文档可能并不适合每个人，如果你渴望立刻开始生成应用程序，建议向前跳转到[移动开发简介](~/cross-platform/get-started/introduction-to-mobile-development.md)指南，然后在以后返回到本文档。

## <a name="mobile-development-software-lifecycle"></a>移动软件开发生命周期

移动开发的生命周期在很大程度上与 Web 或桌面应用程序的 SDLC 没有什么不同。 与这些应用程序一样，该过程通常有 5 个主要部分：

1. **开始** – 所有应用都从一个构想开始。 该构想通常会完善为应用程序的坚实基础。
1. **设计** – 设计阶段包括定义应用的用户体验 (UX)（如常规布局是什么、其工作原理等）以及将该 UX 转换为合适的用户界面 (UI) 设计（通常是在图形设计人员的帮助下）。
1. **开发** – 这通常是资源密集程度最高的阶段，是应用程序的实际构建。
1. **稳定** – 当开发进行到足够程度时，QA 通常会开始测试应用程序并修复 bug。 通常情况下，应用程序会进入有限测试阶段，在此期间会使更广泛的用户群体有机会使用它，并提供反馈和通知更改。
1. **部署**

通常这些片段中的很多是重叠的，例如，开发常常在用户界面正在最终完成时便已在进行了，它甚至可能会通知 UI 设计。 此外，应用程序可能会在向新版本添加新功能的同时进入稳定阶段。

而且，这些阶段可以在任何数量的 SDLC 方法（如敏捷、螺旋、瀑布等）中使用。

以下各部分会更详细地说明其中每个阶段。

### <a name="inception"></a>开始

人们与移动设备进行的交互的无处不在和级别意味着几乎每个人都对移动应用有想法。 移动设备开辟了与计算、Web 甚至是公司基础结构进行交互的全新方式。

开始阶段就是为应用定义和完善构想。
若要创建成功的应用，需要提出一些基本问题，这一点十分重要。 下面是在公共应用商店之一中发布应用之前要考虑的一些事项：

- **竞争优势** – 是否已存在类似应用？ 如果是这样，此应用程序如何与其他应用程序区别开来？

对于会在企业中分发的应用：

- **基础结构集成** – 它会集成或扩展哪种现有基础结构？

此外，应用应在移动外形规格环境中进行评估：

- **价值** – 此应用为用户带来了什么价值？ 他们会如何使用它？
- **形式/移动性** – 此应用如何采用移动外形规格进行工作？ 如何使用移动技术（如位置感知、摄像头等）增加价值？

为了帮助设计应用的功能，定义参与者和[用例](https://en.wikipedia.org/wiki/Use_case)可能会十分有用。 参与者是应用程序中的角色，通常是用户。 用例通常是操作或意向。

例如，跟踪应用程序的任务可能具有两个参与者：用户和朋友   。 用户可以创建任务  ，以及与好友共享任务  。 在这种情况下，创建任务和共享任务是两个不同的用例，它们与参与者相结合，可告知你需要构建的屏幕，以及需要开发的业务实体和逻辑。

捕获了适当数量的用例和参与者之后，开始设计应用程序便会容易得多。 开发随后可以侧重于如何创建应用，而不是应用是什么或是应该做什么。

### <a name="designing-mobile-applications"></a>设计移动应用程序

确定了应用的特性和功能之后，下一步是开始尝试解决用户体验（或 UX）。

#### <a name="ux-design"></a>UX 设计

UX 通常通过线框或模型使用众多[设计工具包](https://docs.microsoft.com/windows/uwp/design/downloads/)之一来实现。 通过 UX 原型可以设计 UX，而不必担心实际 UI 设计：

 [![](introduction-to-mobile-sdlc-images/balsamiq.png "UX is usually done via wireframes or mockups using tools such as Balsamiq")](introduction-to-mobile-sdlc-images/balsamiq.png#lightbox)

创建 UX 原型时，需要考虑应用所面向的各种平台的界面指南，这一点十分重要。 应用应在每种平台上都“轻松自如”。 每种平台的正式设计指南有：

1. **Apple** -  [人体学接口指南](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
1. **Android** –  [设计指南](https://developer.android.com/design/index.html)
1. **UWP** - [UWP 设计基础知识](https://docs.microsoft.com/windows/uwp/design/basics/)

例如，每个应用都提供一种工具以用于在应用程序中的各部分之间进行切换。 iOS 使用屏幕底部的选项卡栏，Android 使用屏幕顶部的选项卡栏，而 UWP 则使用[透视或选项卡](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tabs-pivot)视图。

此外，硬件本身也会决定 UX 决策。 例如，iOS 设备没有物理返回  按钮，因此引入了导航控制器工具：

 ![](introduction-to-mobile-sdlc-images/01-navigation-controller.png "iOS devices have no physical back button, and therefore introduce the Navigation Controller metaphor")

而且，外形规格也会影响 UX 决策。 平板电脑具有大得多的空间，因此可以显示更多信息。 通常，在手机上需要多个屏幕的内容对于平板电脑会压缩到一个屏幕中：

 [![](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png "Often what needs multiple screens on a phone is compressed into one for a tablet")](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png#lightbox)

由于存在各种外形规格，因此你还可能要面向中型外形规格（介于手机与平板电脑之间的某种外形规格）。

#### <a name="user-interface-ui-design"></a>用户界面 (UI) 设计

确定了 UX 之后，下一步是创建 UI 设计。 虽然 UX 通常只是黑白原型，不过在 UI 设计阶段中会引入并最终完成颜色、图形等。 在良好的 UI 设计上花费时间是非常重要的，通常情况下，最受欢迎的应用具有专业的设计。

与 UX 一样，务必要了解每种平台都具有自己的设计语言，因此设计良好的应用程序在每种平台上的外观可能仍有所不同：

 [![](introduction-to-mobile-sdlc-images/multiplatform-1.png "A well-designed application may still look different on each platform")](introduction-to-mobile-sdlc-images/multiplatform-1.png#lightbox)

### <a name="development"></a>开发

开发阶段通常在非常早的时候开始。 实际上，一旦构想在概念/灵感阶段中具有一定的成熟度之后，通常会开发一个工作原型，用于验证功能、假设以及帮助了解工作范围。

在本教程的其余部分，我们会在很大程度上侧重于开发阶段。

### <a name="stabilization"></a>稳定

稳定是解决应用中的 bug 的过程。 不只是从功能角度，例如：“它在我单击此按钮时崩溃”，还涉及可用性和性能。 最好在开发过程中非常早的时候开始进行稳定，以便可以在成本高昂之前进行修正。 通常，应用程序会进入原型  、Alpha  、Beta  和候选发布  阶段。 不同人员会以不同方式定义这些阶段，但是它们通常遵循以下模式：

1. **原型** – 应用仍处于概念证明阶段，只有核心功能（或应用程序的特定部分）在工作。 存在主要 bug。
1. **Alpha** – 核心功能通常已代码完成（已生成，但未进行完整测试）。 仍存在主要 bug，可能仍然不存在外围功能。
1. **Beta** – 大多数功能现已完成，至少进行了轻度测试和 bug 修复。 可能仍然存在主要已知问题。
1. **候选发布** – 所有功能都已完成并经过测试。 除了新 bug 以外，应用是进行发布的候选版本。

应尽早开始测试应用程序。 例如，如果在原型阶段中发现主要问题，则仍可以修改应用的 UX 以适应它。 如果在 alpha 阶段中发现性能问题，则可以在基于错误假设生成大量代码之前，足够早地修改体系结构。

通常，随着应用程序在生命周期中进一步发展，会开放给更多人来尝试它、测试它、提供反馈，等等。例如，原型应用程序只能向关键利益干系人显示或提供，而候选发布应用程序可以分发给为抢先体验而注册的客户。

对于早期测试和部署到相对较少的设备，通常直接从开发计算机进行部署便足够了。 但是，随着受众的扩大，这可能会很快便十分麻烦。 因此，有许多测试部署选项，使你可以邀请人们加入测试池、通过 Web 发布生成以及提供可以进行用户反馈的工具，从而大大降低此过程的难度。

对于测试和部署，可以使用 [App Center](https://appcenter.ms/) 持续生成、测试、发布和监视应用。

### <a name="distribution"></a>分布

一旦应用程序处于稳定状态，便可以将它分发出去。 有许多不同的分发选项，具体取决于平台。

#### <a name="ios"></a>iOS

Xamarin.iOS 和 Objective-C 应用采用完全相同的方式进行分发：

1. **Apple 应用商店** – Apple 应用商店是全球提供的在线应用程序存储库，通过 iTunes 内置在 Mac OS X 中。 它是迄今为止最受欢迎的应用程序分发方法，使开发人员可以不费吹灰之力地在线将其应用投入市场和进行分发。
1. **内部部署** – 内部部署旨在用于不能通过应用商店公开提供的企业应用程序的内部分发。
1. **临时部署** – 临时部署主要用于开发和测试，使你可以部署到数量有限的经过正确设置的设备。 通过 Xcode 或 Visual Studio for Mac 部署到设备时，这称为临时部署。

#### <a name="android"></a>Android

所有 Android 应用程序都必须在分发之前进行签名。 开发人员使用自己的通过私钥进行保护的证书对其应用程序进行签名。 此证书可以提供验证链，该链将应用程序开发人员绑定到该开发人员生成和发布的应用程序。
必须注意的是，虽然适用于 Android 的开发证书可以由认可的证书颁发机构进行签名，不过大多数开发人员不会选择利用这些服务，而对其证书进行自签名。 证书的主要用途是区分不同开发人员和应用程序。
Android 使用此信息来帮助在 Android OS 内运行的应用程序和组件之间实施权限委派。

与其他常用移动平台不同，Android 采用非常开放的应用分发方法。 设备不会锁定到单个、经过审批的应用商店。
相反，任何人都可以自由地创建应用商店，而大多数 Android 手机允许从这些第三方商店安装应用。

这样，开发人员便可以将可能更大但更复杂的分发渠道用于其应用程序。 [Google Play](https://play.google.com/store?hl=en) 是 Google 的官方应用商店，不过还有许多其他应用商店。 几个常见应用商店有：

1. [AppBrain](https://www.appbrain.com/)
1. [适用于 Android 的 Amazon 应用商店](https://www.amazon.com/mobile-apps/b?ie=UTF8&amp;node=2350149011)
1. [Handango](https://www.handango.com/)
1. [GetJar](https://www.getjar.com/)

#### <a name="uwp"></a>UWP

UWP 应用程序通过 Microsoft Store 分发给用户。 开发人员提交其应用进行审批，此后它们会出现在应用商店中。 有关发布 Windows 应用的详细信息，请参阅 UWP 的[发布](https://docs.microsoft.com/windows/uwp/publish/)文档。

## <a name="mobile-development-considerations"></a>移动开发注意事项

虽然开发移动应用程序与传统 Web/桌面开发在过程或体系结构方面没有根本差异，不过需要了解一些注意事项。

### <a name="common-considerations"></a>一般注意事项

#### <a name="multitasking"></a>多任务

移动设备上的多任务（一次运行多个应用程序）面临两个艰巨挑战。 首先，由于屏幕空间有限，因此难以同时显示多个应用程序。 因此在移动设备上，一次只能有一个应用处于前台。 其次，让多个应用程序打开并执行任务可能会快速耗尽电池电量。

每种平台以不同方式处理多任务，我们会对此进行一些探讨。

#### <a name="form-factor"></a>外形规格

移动设备通常可分为两种类别，即手机和平板电脑，在两者之间有一些交叉设备。 针对这些外形规格进行开发通常非常类似，但是，为它们设计应用程序可能会非常不同。
手机的屏幕空间非常有限，而平板电脑，虽然更大，不过仍然是屏幕空间甚至小于大多数笔记本电脑的移动设备。 因此，移动平台 UI 控件经过专门设计，以便在较小外形规格上有效。

#### <a name="device-and-operating-system-fragmentation"></a>设备和操作系统碎片

请务必在整个软件开发生命周期中考虑不同设备：

1. **概念化和规划** – 请记住，硬件和功能因设备而异，依赖于某些功能的应用程序可能无法在某些设备上正常工作。 例如，并非所有设备都具有摄像头，因此如果在构建视频消息传递应用程序，则某些设备可能能够播放视频，但不能拍摄视频。
1. **设计** – 设计应用程序的用户体验 (UX) 时，请注意设备间的不同屏幕比率和大小。 此外，设计应用程序的用户界面 (UI) 时，应考虑不同屏幕分辨率。
1. **开发** – 在代码中使用某个功能时，应始终首先测试该功能是否存在。 例如，在使用某个设备功能（如摄像头）之前，请始终首先在 OS 中查询该功能是否存在。 随后在初始化功能/设备时，确保从 OS 请求当前支持的有关该设备的信息，然后使用这些配置设置。
1. **测试** – 应及早测试应用程序和经常在实际设备上进行测试，这非常重要。 即使是具有相同硬件规格的设备的行为也可能差异很大。

#### <a name="limited-resources"></a>有限的资源

移动设备一直在变得越来越强大，但它们与台式机或笔记本计算机相比，仍然是功能有限的移动设备。 例如，桌面开发人员通常不必担心内存容量；他们习惯于具有丰富的物理和虚拟内存，而在移动设备上，仅仅是加载少量高质量图片便可能会快速消耗所有可用内存。

此外，大量占用处理器的应用程序（如游戏或文本识别）可能会在实际上使移动 CPU 不堪重负，并对设备性能产生负面影响。

出于上述这类注意事项，请务必巧妙地编写代码以及尽早并经常部署到实际设备以验证响应性。

### <a name="ios-considerations"></a>iOS 注意事项

#### <a name="multitasking"></a>多任务

多任务在 iOS 中受到非常严格的控制，当其他应用程序转到前台时，你的应用程序时必须遵循一些规则和行为，否则 iOS 会终止你的应用程序。

#### <a name="device-specific-resources"></a>特定于设备的资源

在特定外形规格中，硬件可能在不同型号之间差异很大。 例如，某些设备具有后置摄像头，某些设备还具有前置摄像头，而某些设备没有摄像头。

某些较旧设备（iPhone 3G 和更低版本）甚至不允许多任务。

由于设备型号之间的这些差异，请务必在尝试使用某个功能之前检查它是否存在。

#### <a name="os-specific-constraints"></a>特定于 OS 的约束

为了确保应用程序响应迅速并且安全可靠，iOS 强制实施了应用程序必须遵守的一些规则。 除了与多任务有关的规则，还有一些应用必须在特定时间量内从中返回的事件方法，否则 iOS 会终止它。

另外值得注意的是，应用在所谓的沙盒中运行，这种环境会实施安全约束，限制应用可以访问的内容。 例如，应用可以对自己的目录进行读取和写入，但如果它尝试写入其他应用目录，则会被终止。

### <a name="android-considerations"></a>Android 注意事项

#### <a name="multitasking"></a>多任务

Android 中的多任务具有两个组成部分；第一个是活动生命周期。 Android 应用程序中的每个屏幕都由一个活动进行表示，当应用程序位于后台或转到前台时会发生一组特定事件。 应用程序必须遵循此生命周期以创建响应迅速、行为良好的应用程序。 有关详细信息，请参阅[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。

Android 中的多任务的第二个组成部分是服务的使用。
服务是长时间运行的进程，独立于应用程序存在，用于在应用程序处于后台时执行进程。 有关详细信息，请参阅[创建服务](~/android/app-fundamentals/services/index.md)指南。

#### <a name="many-devices-and-many-form-factors"></a>许多设备和许多外形规格

Google 不会可以运行 Android OS 的设备强制实施任何限制。 这种开放模式导致产品环境包含大量具有截然不同的硬件、屏幕分辨率和比率、设备特性和功能的不同设备。

由于 Android 设备极其分散，因此大多数人选择最受欢迎的 5 或 6 种设备进行设计和测试，并优先考虑这些设备。

#### <a name="security-considerations"></a>安全注意事项

Android OS 中的应用程序全都采用具有有限权限的不同独立标识来运行。 默认情况下，应用程序可以执行的操作非常少。 例如，如果没有特殊权限，则应用程序不能发送短信、确定收集状态甚至是访问 Internet！ 若要访问这些功能，应用程序必须在其应用程序清单文件中指定它们需要的权限，在安装它们时，OS 会读取这些权限，向用户通知应用程序正在请求这些权限，然后允许用户继续或取消安装。
由于开放的应用程序商店模型（例如由于应用程序的管理方式与 iOS 不同），因此这在 Android 分发模型中是不可或缺的步骤。 有关应用程序权限的列表，请参阅 Android 文档中的[清单权限](https://developer.android.com/reference/android/Manifest.permission.html)参考文章。

### <a name="windows-considerations"></a>Windows 注意事项

#### <a name="multitasking"></a>多任务

UWP 中的多任务具有两个部分：页面和应用程序的生命周期以及后台进程。 应用程序中的每个屏幕都是 Page 类的实例，它具有与设为活动或非活动状态关联的事件（具有用于处理非活动状态或“逻辑删除”的特殊规则）。

第二个部分是为处理任务提供后台代理，即使在应用程序不在前台运行时。

#### <a name="device-capabilities"></a>设备功能

虽然 UWP 硬件同构程度非常高，但是仍存在可选组件，因此需要在编码时进行特殊考虑。 可选硬件功能包括摄像头、指南针和陀螺仪。 还有一类需要特殊考虑的特殊低内存 (256MB)，或是开发人员可以选择退出低内存支持。

#### <a name="security-considerations"></a>安全注意事项

有关 UWP 中的重要安全注意事项的信息，请参阅[安全](https://docs.microsoft.com/windows/uwp/security/)文档。

## <a name="summary"></a>总结

本指南提供了 SDLC 简介，因为它与移动开发相关。 它介绍了有关构建移动应用程序的一般注意事项，并讨论了一些特定于平台的注意事项（包括设计、测试和部署）。

## <a name="next-steps"></a>后续步骤

- [什么是 Xamarin？](~/cross-platform/get-started/introduction-to-mobile-development.md)
- [Xamarin 入门](~/get-started/index.yml)
- [跨平台共享代码](~/cross-platform/app-fundamentals/index.md)
