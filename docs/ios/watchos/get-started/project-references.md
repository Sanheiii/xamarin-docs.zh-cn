---
title: Xamarin 中的 watchOS 项目引用
description: 本文档介绍 iOS 应用程序、监视应用程序和监视应用扩展之间的关系。 它讨论了项目引用和捆绑标识符。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: a7b0f0af887232da52cdca6f6506124fa5e0eef8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930086"
---
# <a name="watchos-project-references-in-xamarin"></a>Xamarin 中的 watchOS 项目引用

_说明 iOS 应用、监视应用和监视扩展之间的关系。_

WatchOS 解决方案中的三个项目*自动配置*为以特定方式相互引用，以便正确生成和捆绑 watchOS 3 应用。 下面介绍了这些项目引用和捆绑标识符设置以供参考。

## <a name="project-references"></a>项目引用

通过双击每个项目的 "引用" 节点来查看引用：

- **iPhone 应用**引用**监视应用**

  ![iPhone 应用引用监视应用](project-references-images/catalog-reference1.png)

- **监视应用**引用**监视应用扩展**

  ![iPhone 应用引用监视应用](project-references-images/catalog-reference2.png)

- **Watch 应用扩展**未引用任何其他项目

  ![监视应用扩展未引用其他项目](project-references-images/catalog-reference3.png)

## <a name="bundle-identifiers"></a>捆绑标识符

还需要确保**捆绑标识符**是正确的。
所有三个项目都应具有*相同*的标识符前缀，两个监视项目具有和的预定义扩展，如下所示 `watchkitextension` `watchkitapp` （对于**WatchKitCatalog**示例）：

- Xamarin iOS 统一项目-`com.xamarin.WatchKitCatalog`

- WatchKit 扩展项目-`com.xamarin.WatchKitCatalog.watchkitextension`

- 监视应用项目-`com.xamarin.WatchKitCatalog.watchkitapp`

此外，请确保这些**信息 info.plist**设置正确：

- Watch 应用项目 `WKCompanionAppBundleIdentifier` 与父/容器应用的捆绑 ID （即在 iPhone 上运行的应用程序）匹配;

- 手表套件扩展项目的**WKApp 捆绑 id**与 watch 应用项目的捆绑 id 匹配。

您可以通过双击每个项目中的**info.plist**文件来编辑标识符。

此屏幕截图是**监视扩展的**info.plist 文件，还显示了**watch 应用的**标识符：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](project-references-images/infoplist-extension.png "This screenshot is the Watch Extension's Info.plist file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](project-references-images/infoplist-extension-vs.png "This screenshot is the Watch Extension's Info.plist file")

-----

此屏幕截图是**监视应用的**info.plist 文件。
当前**监视操作系统**版本为8.2，因此监视应用的**部署目标**应为**8.2**。 请注意，如果安装了 Xcode 6.3，此值可能设置为 8.3-应将其更改为8.2。

![Watch info.plist 文件](project-references-images/infoplist-watchapp.png)

监视应用的部署目标可以不同于监视扩展和 iOS 应用。
