---
title: Android 穿戴设备
description: 生成适用于 Android 可穿戴设备的应用。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 67698ae7fe3ef9a7586d83e26ed276ba473396e5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457505"
---
# <a name="android-wear"></a>Android 穿戴设备

Android 磨损是适用于智能手表等可穿戴设备的 Android 版本。 本部分包含有关如何安装和配置应用程序所需的工具的说明，这是创建第一个磨损设备的分步演练，以及可用于创建自己的应用程序的示例列表。

## <a name="getting-started"></a>[入门](~/android/wear/get-started/index.md)

介绍 Android 磨损，介绍如何安装和配置计算机以进行磨损开发，并提供相关步骤来帮助您在模拟器或磨损设备上创建和运行第一个 Android 应用程序。

## <a name="user-interface"></a>[用户界面](~/android/wear/user-interface/index.md)

介绍 Android 特定于控件的控件，并提供指向演示如何使用这些控件的示例的链接。

## <a name="platform-features"></a>[平台功能](~/android/wear/platform/index.md)

本部分中的文档涵盖特定于 Android 磨损的功能。 可在此处找到介绍如何创建 WatchFace 的主题。

## <a name="screen-sizes"></a>[屏幕大小](~/android/wear/screen-sizes.md)

预览并优化可用屏幕大小的用户界面。

## <a name="deployment--testing"></a>[部署和测试](~/android/wear/deploy-test/index.md)

介绍如何将 Android 应用部署到 Android 磨损设备或配置为磨损的 Android 仿真程序。 它还包括有关如何在开发计算机和 Android 设备之间设置蓝牙连接的调试提示和信息。

## <a name="wear-apis"></a>[磨损 Api](https://developer.android.com/reference/android/support/wearable)

Android 开发人员网站提供了有关关键损耗 Api 的详细信息，如 [可穿戴活动](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)、 [意向](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)、 [身份验证](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)、 [复杂性](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)、 [复杂渲染](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)、 [通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html)、 [视图](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)和 [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。

## <a name="samples"></a>示例

你可以使用 Android 磨损 (找到大量 [示例](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) ，或者直接) [github](https://github.com/xamarin/monodroid-samples/tree/master/wear) 。

|示例|说明|屏幕快照|
|--- |--- |--- |
|[SkeletonWear](/samples/xamarin/monodroid-samples/wear-skeletonwear)|可穿戴项目基础知识的简单示例，包括 GridViewPager 和交互式通知。|![Skeletonwear 的屏幕截图](images/skeleton.png)|
|[WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub)|WatchViewStub 控件的简单演示，可检测屏幕形状并自动加载正确的布局。 查看 WatchViewStub 在 **资源/布局/main_activity.xml** 布局中的工作方式。|![WatchViewStub 的屏幕截图](images/watchview.png)|
|[RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant)|用食谱步骤的形式演示了磨损通知页。 通知是在 RecipeService.cs 中创建的。|![RecipeAssistant 的屏幕截图](images/recipeassist.png)|
|[ElizaChat](/samples/xamarin/monodroid-samples/wear-elizachat)|与名为 Eliza 的 "个人助手" 交互的有趣示例，使用磨损交互式通知创建使用固定响应的会话。|![ElizaChat 的屏幕截图](images/eliza.png)|
|[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager 实现2D 导航模式，用户可在其中垂直 swipes，然后水平滚动以通过选项和内容进行导航。|![GridViewPager 的屏幕截图](images/gridviewpager.png)|
|[WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace 是一种自定义的监视面，具有模拟样式的小时、分钟和秒。 此示例演示如何创建一个用于绘制当前时间并处理环境模式和可见性更改事件的手表人脸服务。 它包括侦听时区更改的广播接收器，并会相应地自动更新时间。|![WatchFace 的屏幕截图](images/gridviewpager.png)|

## <a name="videos"></a>视频

查看以下视频链接，其中讨论了支持磨损的 Xamarin：

|说明|屏幕快照|
|--- |--- |
|[Android L](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; 等等Android L 开发者预览版引入了一个很多的新 Api，使开发人员能够利用这些 Api，其中包含一些内容设计、通知和新的动画。|![演示的视频屏幕截图](images/video-android-l.png)|
|[C # 在我的耳中，在我的眼睛中： Google 玻璃和 Android 磨损](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; 可穿戴计算看起来可能与未来 (或检查器小剧集) 的内容类似，但很多人现在都在使用未来！ C # 开发人员了解这一点，并且已经具备了用于充分利用 2014) 的可穿戴 (设备功能的工具和技能。|![演示的视频屏幕截图](images/video-eyes-ears.png)|
|Xamarin 中的[新增功能](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash;Android L、Android 磨损、Android 电视机、Android 自动、材料设计和艺术;这对你作为 Xamarin 开发人员意味着什么？from 演化为2014。|![演示的视频屏幕截图](Images/video-whats-new.png)|

<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->