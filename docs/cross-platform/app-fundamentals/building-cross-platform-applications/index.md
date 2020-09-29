---
title: 生成跨平台应用程序
description: 本部分在摘要和六个部分中讨论如何使用 Xamarin 开发平台构建应用程序–从了解 Xamarin 如何设计移动应用，然后对各种应用商店进行测试和部署。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: davidortinau
ms.author: daortin
ms.date: 01/28/2016
ms.openlocfilehash: f725fe06a5438e4dbdca2773d93befc13bc8ff95
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457492"
---
# <a name="building-cross-platform-applications"></a>生成跨平台应用程序

跨平台移动应用程序之间共享代码有两个选项：共享资产项目和可移植类库。 [这里讨论](~/cross-platform/app-fundamentals/code-sharing.md)了这些选项;还提供了有关[可移植类库](~/cross-platform/app-fundamentals/pcl.md)和[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)的详细信息。

<a name="Sections"></a>

 [概述](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第1部分–了解 Xamarin Mobile 平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第2部分-体系结构](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第3部分-设置 Xamarin 跨平台解决方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第4部分–处理多个平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [第5部分–实用代码共享策略](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [第 6 部分 - 测试和应用商店审批](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies"></a>

## <a name="case-studies"></a>案例研究

本文档中所述的原则在示例应用程序 *Tasky*和 [预构建的应用程序](https://xamarin.com/prebuilt) （如 [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)）中进行实践。

 <a name="Tasky"></a>

### <a name="tasky"></a>Tasky

Tasky 是适用于 iOS、Android 和 Windows Phone 的简单待办事项列表应用程序。
它演示了使用 Xamarin 创建跨平台应用程序的基础知识，并使用了本地 SQLite 数据库。

 [ ![ tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![ tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

阅读 [Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。

## <a name="summary"></a>总结

本部分介绍 Xamarin 的应用程序开发工具，并讨论如何构建面向多个移动平台的应用程序。

它涵盖了一个分层体系结构，该体系结构可在多个平台上对代码进行重新使用，并描述了可在该体系结构中使用的不同软件模式。

示例提供了常见的应用程序功能 (例如文件和网络操作) 以及如何以跨平台的方式生成它们。

最后，它简要讨论了测试，并提供了对案例研究的参考，将这些原则纳入操作中。

## <a name="related-links"></a>相关链接

- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例应用 (github) ](/samples/xamarin/mobile-samples/taskyportable/)
- [Xamarin 移动应用程序开发：跨平台 c # 和 Xamarin. Forms 基础 (Amazon) ](https://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [使用 c # 通过 Greg Shackles (O'Reilly 的移动开发) ](https://shop.oreilly.com/product/0636920024002.do)