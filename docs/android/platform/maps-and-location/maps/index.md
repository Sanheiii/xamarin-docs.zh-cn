---
title: 如何在 Xamarin.Android 中使用 Google Maps 和位置
description: 本文讨论如何在 Xamarin.Android 中使用地图和位置。 其中涵盖了从利用内置地图应用程序到直接使用 Google Maps Android API V2 的所有内容。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 11feb8e3e10e12bfb2c647182ec4ba953ab34e24
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456894"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何在 Xamarin.Android 中使用 Google Maps 和位置

_本文讨论如何在 Xamarin.Android 中使用地图和位置。其中涵盖了从利用内置地图应用程序到直接使用 Google Maps Android API V2 的所有内容。_

## <a name="maps-overview"></a>地图概述

地图技术是对移动设备的普遍补充。 台式计算机和笔记本电脑通常不会内置位置感知功能。 另一方面，移动设备使用此类应用程序来定位设备并显示更改位置信息。 Android 具有功能强大的内置技术，可使用设备上可用的位置硬件在地图上显示位置数据。 本文介绍 Xamarin.Android 下的地图应用程序必须提供的内容，包括： 

- 使用内置地图应用程序快速添加地图功能。
- 使用地图 API 来控制地图的显示。
- 使用多种技术添加图形覆盖。

本节中的主题涵盖了广泛的地图功能。
首先，介绍如何利用 Android 的内置地图应用程序以及如何显示某个位置的全景街道视图。 然后，讨论如何使用地图 API 在应用程序内直接合并地图功能，涵盖如何控制地图的位置和显示以及如何添加图形覆盖。

## <a name="related-links"></a>相关链接

- [MapsAndLocationDemo_v3（示例）](/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [获取 Google Maps API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [意向列表：在 Android 设备上调用 Google 应用程序](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置和地图](https://developer.android.com/guide/topics/location/index.html)