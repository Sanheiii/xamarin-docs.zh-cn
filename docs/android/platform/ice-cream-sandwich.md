---
title: Ice Cream Sandwich 功能
description: 本文介绍了 Android 4 API - Ice Cream Sandwich 为应用程序的开发人员提供的一些新功能。 它涵盖了几种新的用户界面技术，然后研究了 Android 4 为在应用程序之间和设备之间共享数据提供的各种新功能。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1e72a09acc08fc4db49da0e94eb64fbd523e9ecf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453684"
---
# <a name="ice-cream-sandwich-features"></a>Ice Cream Sandwich 功能

本文介绍了 Android 4 API - Ice Cream Sandwich 为应用程序的开发人员提供的一些新功能。  它涵盖了几种新的用户界面技术，然后研究了 Android 4 为在应用程序之间和设备之间共享数据提供的各种新功能。

## <a name="overview"></a>概述

Android OS 版本 4.0（API 级别 14）对 Android 操作系统进行了重大修改，包含许多重要更改和升级，具体包括：

- **更新了用户界面** - 新增了一些 UI 功能，让开发人员能够更有能力、更灵活地创建应用程序用户界面。 这些新功能包括 `GridLayout`、`PopupMenu`、`Switch` 小组件和 `TextureView`。
- **改进了硬件加速** - 现在所有的 Android 控件都可以在 GPU 上进行 2D 渲染。 此外，在为 Android 4.0 开发的所有应用程序中，硬件加速默认处于启用状态。
- **新数据 API** - 对以前不可正式访问的数据（如日历数据和设备所有者的用户配置文件）有新访问权限。
- **应用数据共享** - 现在，在应用程序和设备之间共享数据不再是难事，可以使用 `ShareActionProvider` 技术（可便于轻松地从操作栏创建共享操作），还可以使用近场通信 (NFC)  的 Android Beam  技术（这使得跨彼此紧密邻近的设备共享数据变得非常容易）。

本文将探讨这些功能以及 Android 4.0 API 的其他更改，然后将介绍如何将每项功能与 Xamarin.Android 配合使用。

## <a name="user-interface-features"></a>用户界面功能

Android 4 新增了多种用户界面技术，包括：

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** - 支持控件的 2D 网格布局。
- **[Switch 小组件](~/android/user-interface/controls/switch.md)** - 便于在“开”或“关”之间切换。
- **[TextureView](~/android/user-interface/controls/texture-view.md)** - 在视图中启用视频和 OpenGL 内容。
- **[导航栏](~/android/user-interface/controls/navigation-bar.md)** - 包含“返回”、“主页”和“多任务”的虚拟按钮。

此外，还增强了其他 UI 元素，如现在更易于使用的 `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，以及外观更精美的选项卡。

## <a name="sharing-features"></a>共享功能

Android 4 新增了几种技术，可便于跨设备和应用程序共享数据。 它还支持访问以前不可用的各种类型数据（如日历信息和设备所有者的用户配置文件）。 此部分将研究 Android 4 提供的各种功能，所涉及的领域包括：

- **[Android Beam](~/android/platform/android-beam.md)** - 便于通过 NFC 共享数据。
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** - 创建提供程序，便于开发人员从操作栏指定共享操作。
- **[用户配置文件](~/android/user-interface/user-profile.md)** - 提供对设备所有者的配置文件数据的访问权限。
- **[日历 API](~/android/user-interface/controls/calendar.md)** - 提供对日历提供程序中的日历数据的访问权限。

## <a name="x86-emulators"></a>x86 模拟器

ICS 尚不支持使用 x86 模拟器进行开发。 只有支持 Android 2.3.3（API 级别 10）才支持 x86 模拟器。 有关详细信息，请参阅[配置 x86 模拟器](~/android/get-started/installation/android-emulator/index.md)。

## <a name="summary"></a>总结

本文介绍了 Android 4 新增的各种新技术。 我们回顾了新的用户界面功能，如 GridLayout  、PopupMenu  和 Switch  小组件。 还介绍了一些用于控制系统 UI 的新支持，以及如何使用 TextureView  。 然后，我们讨论了新增的各种共享技术。 具体介绍了 Android Beam  如何便于跨使用 NFC  的设备共享信息，并讨论了新的日历 API  ，同时还展示了如何使用内置的 ShareActionProvider  。
最后研究了如何使用 ContactsContract  提供程序来访问用户配置文件数据。

## <a name="related-links"></a>相关链接

- [TextureViewDemo（示例）](/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo（示例）](/samples/xamarin/monodroid-samples/calendardemo)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)
- [Ice Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](https://developer.android.com/about/versions/android-4.0.html)