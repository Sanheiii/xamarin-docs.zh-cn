---
title: 用 Xamarin 创建用户界面
description: 为 Xamarin Android 应用创建用户界面
ms.prod: xamarin
ms.assetid: F67B7C33-BC53-2BB6-CDA7-16E4AB4A9EFB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 08085877080c954142729e75e88235011c3a1324
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997132"
---
# <a name="user-interfaces-with-xamarinandroid"></a>具有 Xamarin 的用户界面

以下各节介绍了用于在 Xamarin Android 应用程序中撰写用户界面的各种工具和构建基块。

## <a name="android-designer"></a>[Android Designer](~/android/user-interface/android-designer/index.md)

本部分介绍如何使用 Android Designer 直观地布局控件和编辑属性。 还介绍了如何使用设计器跨各种配置（如主题、语言和设备配置）使用用户界面和资源，以及如何设计横向和纵向等替代视图。

## <a name="material-theme"></a>[Material Theme](~/android/user-interface/material-theme.md)

*材料主题*是用户界面样式，用于确定 Android 中的视图和活动的外观和感觉。 材料主题内置于 Android 中，因此它由系统 UI 和应用程序使用。 本指南介绍了材料设计原则，并说明了如何使用内置的材料主题或自定义主题来为应用程序建立主题。

## <a name="user-profile"></a>[用户配置文件](~/android/user-interface/user-profile.md)

本指南说明如何访问设备所有者的个人配置文件，包括设备所有者姓名和电话号码之类的联系人数据。

## <a name="splash-screen"></a>[初始屏幕](~/android/user-interface/splash-screen.md)

Android 应用程序需要一些时间才能启动，特别是在设备上首次启动应用程序时。 初始屏幕可能会显示用户的启动进度。 本指南说明了如何为应用程序创建初始屏幕。

## <a name="layouts"></a>[布局](~/android/user-interface/layouts/index.md)

布局用于定义用户界面的视觉对象结构。
布局（如 `ListView` 和） `RecyclerView` 是 Android 应用程序的最基本的构建基块。 通常，布局会使用 `Adapter` 来充当从布局到用于填充布局中数据项的基础数据的桥。 本部分介绍如何使用、、、 `LinearLayout` 和等 `RelativeLayout` 布局 `TableLayout` `RecyclerView` `GridView` 。

## <a name="controls"></a>[控件](~/android/user-interface/controls/index.md)

Android 控件（也称为*小组件*）是用于生成用户界面的 UI 元素。 本部分介绍如何使用控件，例如按钮、工具栏、日期/时间选取器、日历、spinners、开关、弹出菜单、查看寻呼机和 web 视图。
