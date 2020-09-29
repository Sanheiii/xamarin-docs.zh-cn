---
title: Android 穿戴设备简介
description: '引入 Google 的 Android 磨损后，在开发强大的 Android 应用时，你不再局限于手机和平板电脑。 Xamarin Android 对 Android 损耗的支持使你能够在手腕上运行 c # 代码！ 本简介提供了 Android 磨损的基本概述，介绍了其关键功能，并概述了 Android 磨损2.0 中提供的功能。 它列出了一些更常见的 Android 磨损设备，并提供了指向必要的 Google Android 磨损文档的链接，以供进一步阅读。'
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a0e352850dced5c78c68859912de8f41c1fe87f9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458096"
---
# <a name="introduction-to-android-wear"></a>Android 穿戴设备简介

_引入 Google 的 Android 磨损后，在开发强大的 Android 应用时，你不再局限于手机和平板电脑。Xamarin Android 对 Android 损耗的支持使你能够在手腕上运行 c # 代码！本简介提供了 Android 磨损的基本概述，介绍了其关键功能，并概述了 Android 磨损2.0 中提供的功能。它列出了一些更常见的 Android 磨损设备，并提供了指向必要的 Google Android 磨损文档的链接，以供进一步阅读。_

## <a name="overview"></a>概述

Android 磨损在各种设备上运行，包括第一代 Motorola 360、LG 的 G 手表和 Samsung 齿轮。 第二代（包括索尼的 SmartWatch 3）也已通过其他功能发布，其中包括内置 GPS 和脱机播放音乐。 对于 Android 磨损2.0，Google 已与 LG 的两个新监视： LG Watch 运动和 LG 手表样式组合在一起。

![Android 磨损2.0 设备](intro-to-wear-images/hero-image.png "示例 Android 磨损2.0 设备")

Xamarin 5.0 和更高版本通过 Android 4.4 W (API 20) 支持和用于添加其他特定于特定的 UI 控件的 NuGet 包，支持 Android 磨损。 Xamarin 5.0 及更高版本还包括打包你的应用程序的功能。 NuGet 包还适用于 Android 磨损2.0，如本指南后面所述。

## <a name="android-wear-basics"></a>Android 磨损基础知识

Android 磨损具有与 Android 手持应用不同的用户界面模式。 第一种声音应用程序设计为以某种方式扩展随附的手持式应用程序，但从 Android 磨损2.0 开始，可以独立使用应用程序。 当你部署应用程序时，它会与一个配套的手持式应用一起打包。 由于大多数应用程序都依赖于手持设备，因此需要通过某种方式与手持式应用进行通信。 以下部分介绍了这些使用方案，并概述了重要的 Android 磨损功能。 

### <a name="usage-scenarios"></a>使用方案

第一个版本的 Android 磨损主要侧重于通过增强的通知扩展当前手持应用程序，并在掌上应用和可穿戴应用之间同步数据。 因此，这些方案的实现相对简单。

#### <a name="wearable-notifications"></a>可穿戴通知

支持 Android 磨损的最简单方法是利用手持设备与可穿戴设备之间通知的共享特性。 通过使用支持 v4 通知 API 和 `WearableExtender` 类 (在 [Xamarin Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)) 中可用，你可以利用平台的本机功能，如收件箱样式卡或语音输入。 [RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant)示例提供了演示如何将通知列表发送到 Android 磨损设备的示例代码。 

#### <a name="companion-applications"></a>助理应用程序

另一种策略是创建完整的应用程序，该应用程序在可穿戴设备上以本机方式运行，并与随附的手持式应用配对。 此方法的一个很好的示例是 " [测验](/samples/xamarin/monodroid-samples/wear-quiz) 示例" 应用程序，该示例演示如何创建在手持式设备上运行的测验并在可穿戴设备上提出测验问题。 

### <a name="user-interface"></a>用户界面

磨损的主导航模式是垂直排列的一系列纸牌。 其中每个卡都可以具有在同一行中分层的相关操作。 `GridViewPager`类提供此功能; 它遵循与相同的适配器概念 `ListView` 。 您通常将 `GridViewPager` 与 `FragmentGridPagerAdaptor` (或) 相关联 `GridPagerAdaptor` ，使您可以将每个行和列单元格表示为 `Fragment` ： 

[![磨损导航](intro-to-wear-images/2d-picker-sml.png "磨损导航")](intro-to-wear-images/2d-picker.png#lightbox)

磨损还利用了操作按钮，这些按钮由带有小说明文本的大彩色圆圈 (如以上) 所示。  [GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)示例演示如何 `GridViewPager` `GridPagerAdapter` 在应用程序中使用和。

Android 磨损2.0 向磨损用户界面添加导航抽屉、操作抽屉和内联操作按钮。 有关 Android 磨损2.0 用户界面元素的详细信息，请参阅 Android[分析主题。](https://www.google.com/design/spec-wear/system-overview/anatomy.html) 

### <a name="communications"></a>通信

Android 磨损提供了两个不同的通信 Api，以促进可穿戴应用和随附的手持式应用之间的通信： 

**数据 API** &ndash; 此 API 类似于可穿戴设备和手持设备之间的同步数据存储。 当最佳情况时，Android 会在可穿戴和手持型之间传播更改。 当可穿戴超出范围时，它会将同步排队一段时间。 此 API 的主入口点为 `WearableClass.DataApi` 。 有关此 API 的详细信息，请参阅 Android [同步数据项](https://developer.android.com/training/wearables/data-layer/data-items.html) 主题。 

**消息 API** &ndash; 使用此 API 可以使用较低级别的通信路径：在手持式应用和可穿戴应用之间无同步发送单向有效负载。
此 API 的主入口点为 `WearableClass.MessageApi` 。
有关此 API 的详细信息，请参阅 Android [发送和接收消息](https://developer.android.com/training/wearables/data-layer/messages.html) 主题。

您可以选择注册回调，以便通过每个 API 侦听器接口接收这些消息，或在应用程序中实现从派生的服务 `WearableListenerService` 。
此服务将由 Android 磨损自动实例化。
[FindMyPhone](/samples/xamarin/monodroid-samples/wear-findmyphonesample)示例演示如何实现 `WearableListenerService` 。

### <a name="deployment"></a>部署

每个可穿戴应用都部署了其自己的 APK 文件，并将其嵌入到主应用程序 APK 中。 在 Xamarin 5.0 及更高版本中会自动处理此打包，但必须为早于版本5.0 的 Xamarin 版本手动执行此打包。 
[使用打包](~/android/wear/deploy-test/packaging.md) 将更详细地介绍部署。 

## <a name="going-further"></a>深入探索 

熟悉 Android 磨损的最佳方式是构建和测试你的第一个应用。 下面的列表提供了一个建议的读取顺序，可帮助你快速入门：

1. 安装[& 安装](~/android/wear/get-started/installation.md)提供了有关安装和配置开发环境以生成 Xamarin Android 应用的详细说明。 

2. 安装了所需的包并配置了仿真程序或设备后，请参阅  [Hello，](~/android/wear/get-started/hello-wear.md) 提供分步说明，了解如何创建处理按钮单击并在磨损设备上显示单击计数器的小型 Android 损耗项目。 

3. [部署 & 测试](~/android/wear/deploy-test/index.md) 提供有关配置和部署到模拟器和设备的更多详细信息，包括有关如何通过蓝牙将应用部署到磨损设备的说明。

4. 使用[屏幕大小](~/android/wear/screen-sizes.md)说明了如何在磨损设备上预览和优化各种可用屏幕大小的用户界面。 

5. [使用打包](~/android/wear/deploy-test/packaging.md) 介绍了在 Google Play 上手动打包要分发的应用程序的步骤。

创建第一个应用程序后，可能需要尝试生成一个适用于 Android 的自定义监视面。 
[创建手表面](~/android/wear/platform/creating-a-watchface.md) 提供了分步说明和示例代码，可用于开发一小的数字监视人脸服务，并提供了更多代码，使其可用于额外功能，从而增强了模拟样式的监视面。 

## <a name="android-wear-20"></a>Android 磨损2。0

Android 磨损2.0 引入了各种新特性和功能，如 *复杂性*、曲线布局、导航和操作抽屉以及扩展的通知。 此外，磨损2.0 使你可以构建独立于手持应用的独立应用。 全新的 *手腕手势* 功能实现了与应用的单向交互。 以下各节重点介绍这些功能，并提供可帮助你在应用程序中开始使用这些功能的链接。

### <a name="install-wear-20-packages"></a>安装磨损2.0 包

若要使用 Xamarin 生成磨损2.0 应用，必须将 **Xamarin** v2.0 包添加到项目中 (单击 " **浏览" 选项卡**) ：

[![Xamarin v2.0 v2。0](intro-to-wear-images/wear-nuget-2.0-sml.png "安装 Xamarin v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

此 NuGet 包包含 Android 支持可穿戴和磨损兼容库的绑定。

除 **xamarin**外，我们还建议安装 **GooglePlayServices. 可穿戴** NuGet： 

[![GooglePlayServices. 可穿戴](intro-to-wear-images/gpsw-nuget-sml.png "安装 GooglePlayServices. 可穿戴 NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>磨损2.0 的主要功能

自上一次2014启动后，android 磨损2.0 是对 Android 磨损的重大更新。 以下各节重点介绍 Android 磨损2.0 的主要功能，并提供了链接来帮助你在应用程序中开始使用这些新功能。 

#### <a name="complications"></a>复杂情况

*复杂* 的是可以一目了然地看到的小型观看面小组件，无需刷出手表面。 复杂情况类似于桌面样式的仪表板小组件;它们显示天气、电池使用情况、日历事件和健身应用统计信息等信息： 

![复杂示例](intro-to-wear-images/complications.png "复杂示例")

有关复杂情况的详细信息，请参阅 Android [手表面临的复杂](https://developer.android.com/wear/preview/features/complications.html) 主题。 

#### <a name="navigation-and-action-drawers"></a>导航和操作抽屉 

磨损2.0 中附带了两个新的抽屉。 在屏幕顶部显示的 *导航抽屉*允许用户在应用视图 (之间导航，如以下) 中所示。 在屏幕底部显示的 *操作抽屉* (如右) 中所示，允许用户从操作列表中进行选择。 

![导航和操作抽屉](intro-to-wear-images/drawers.png "导航和操作抽屉")

有关这两个新的交互式抽屉的详细信息，请参阅 Android [磨损导航和操作](https://developer.android.com/wear/preview/features/ui-nav-actions.html) 主题。 

#### <a name="curved-layouts"></a>弯曲布局 

磨损2.0 引入了新功能，用于在圆角设备上显示弯曲的布局。 具体而言，新 `WearableRecyclerView` 类经过优化，可用于显示轮上垂直项的列表： 

![弯曲布局示例](intro-to-wear-images/curved-layout.png "弯曲布局示例")

`WearableRecyclerView` 扩展 `RecyclerView` 类以支持曲线布局和循环滚动手势。 有关详细信息，请参阅 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 文档。 

#### <a name="standalone-apps"></a>独立应用 

Android 磨损2.0 应用可独立于手持应用工作。 例如，这意味着，即使在可穿戴设备上关闭或远离设备，智能手表仍可继续提供完整功能。 有关此功能的详细信息，请参阅 Android [独立应用](https://developer.android.com/wear/preview/features/standalone-apps.html) 主题。

#### <a name="wrist-gestures"></a>手腕手势 

手腕手势使用户能够与你的应用进行交互，而无需使用触摸屏 &ndash; 用户可以使用一只手来响应应用。 支持两个手腕手势： 

- 笔锋输出
- 笔锋 in

有关详细信息，请参阅 Android [手腕手势](https://developer.android.com/wear/preview/features/gestures.html) 主题。 

还有其他许多磨损2.0 功能，如内联操作、智能回复、远程输入、扩展通知和新的新桥接模式。 有关新的磨损2.0 功能的详细信息，请参阅 Android [API 概述](https://developer.android.com/wear/preview/api-overview.html)。 

## <a name="devices"></a>设备

下面是可以运行 Android 磨损的设备的一些示例：

- [Motorola 360](https://moto360.motorola.com/)
- [LG G Watch](https://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [LG G Watch R](https://www.lg.com/us/smartwatch/g-watch-r)
- [Samsung 齿轮](https://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [索尼 SmartWatch 3](https://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](https://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>深入阅读

查看 Google 的 Android 磨损文档：

- [关于 Android 磨损](https://www.android.com/wear/)
- [Android 应用程序设计](https://developer.android.com/design/wear/index.html)
- [可穿戴库](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Android 磨损2。0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>总结

本简介提供了 Android 磨损的概述。 它介绍了 Android 磨损的基本功能，并概述了 Android 磨损2.0 中引入的功能。 它提供了指向基本阅读的链接，以帮助开发人员开始进行 Xamarin 开发，并列出了目前市场上的一些 Android 磨损设备的示例。

## <a name="related-links"></a>相关链接

- [安装和设置](~/android/wear/get-started/installation.md)
- [入门](~/android/wear/get-started/index.md)