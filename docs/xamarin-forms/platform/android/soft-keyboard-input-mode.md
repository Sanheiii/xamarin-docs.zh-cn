---
title: Android 上的软键盘输入模式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的来设置软键盘输入区域的运行模式。
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3baadfcb06a595c5809dbabd484a8f886ab84aec
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562361"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Android 上的软键盘输入模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定用于设置软键盘输入区域的运行模式，通过将 [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) 附加属性设置为枚举的值，可在 XAML 中使用该模式 [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) ：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法指定此平台特定的仅在 Android 上运行。 [ `Application.UseWindowSoftInputModeAdjust` ] (x： Xamarin.Forms 。PlatformConfiguration. UseWindowSoftInputModeAdjust (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。应用程序}， Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust) # A3 方法在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空间中，用于设置软键盘输入区域的运行模式， [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) 枚举提供了两个值： [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) 和 [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。 `Pan`该值使用 [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) 调整选项，该选项不会在输入控件具有焦点时调整窗口的大小。 相反，窗口的内容是平移的，因此不会由软键盘遮盖当前焦点。 `Resize`该值使用 [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) 调整选项，该选项可在输入控件有焦点的情况下调整窗口的大小，以便为软键盘腾出空间。

结果是，当输入控件具有焦点时，可以设置软键盘输入区域操作模式：

[![软键盘操作模式特定于平台](soft-keyboard-input-mode-images/pan-resize.png)](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "软键盘操作模式特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)