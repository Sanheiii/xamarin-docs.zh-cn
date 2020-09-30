---
title: Android 上的页面生命周期事件
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，该平台可分别禁用应用程序暂停和恢复的消失和显示页事件。
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d4a8690e7361d58a07f4fbfa7aac8aac839c2ea3
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564012"
---
# <a name="page-lifecycle-events-on-android"></a>Android 上的页面生命周期事件

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定用于对 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 使用 AppCompat 的应用程序分别禁用和页面事件。 此外，它还可以控制是否在恢复时显示软键盘（如果它在暂停时显示），前提是软键盘的操作模式设置为 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

> [!NOTE]
> 请注意，默认情况下将启用这些事件，以便为依赖于事件的应用程序保留现有行为。 禁用这些事件会使 AppCompat 事件周期与 AppCompat 事件周期匹配。

此特定于平台的可在 XAML 中使用，方法是将 [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) 、 [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) 和 [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) 附加属性设置为 `boolean` 值：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

`Application.Current.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `Application.SendDisappearingEventOnPause` ] (x： Xamarin.Forms 。PlatformConfiguration. Xamarin.Forms AndroidSpecific (。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。应用程序}，即命名空间中的) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 用于在 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) 应用程序进入后台时启用或禁用触发页面事件。 [ `Application.SendAppearingEventOnResume` ] (x： Xamarin.Forms 。PlatformConfiguration. Xamarin.Forms AndroidSpecific (。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。应用程序}，) # A3 方法用于启用或禁用 [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 在应用程序从后台恢复页面事件时触发页面事件。 [ `Application.ShouldPreserveKeyboardOnResume` ] (x： Xamarin.Forms 。PlatformConfiguration. Xamarin.Forms AndroidSpecific (。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。应用程序}，System.object) # A3 方法用于控制是否在恢复时显示软键盘（如果它是在暂停时显示的），前提是软键盘的操作模式设置为 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

结果是，不会 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 在应用程序暂停并分别恢复时触发和页面事件，并且如果在应用程序暂停时显示软键盘，则当应用程序恢复时，也会显示该事件：

[![生命周期事件平台特定](page-lifecycle-events-images/keyboard-on-resume.png)](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "生命周期事件平台特定")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)