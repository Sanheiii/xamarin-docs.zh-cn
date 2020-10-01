---
title: Xamarin.Forms 辅助功能
description: 用户希望能在用户界面完成一系列的需求和体验，生成可访问的应用程序才能确保该应用程序可供用户使用。
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: f2fc7000df2c14dfdd863bd431df67643c5501cf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561282"
---
# <a name="no-locxamarinforms-accessibility"></a>Xamarin.Forms 辅助功能

用户希望能在用户界面完成一系列的需求和体验，生成可访问的应用程序才能确保该应用程序可供用户使用。

确保 Xamarin.Forms 应用程序可访问意味着需要考虑许多用户界面元素的布局和设计。 有关需考虑的问题的指南，请参阅[辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md)。 Xamarin.Forms API 已解决许多辅助功能问题（例如较大字体、合适的颜色和对比度设置）。

[Android 辅助功能](~/android/app-fundamentals/accessibility.md)和 [iOS 辅助功能](~/ios/app-fundamentals/accessibility.md)指南包含 Xamarin 公开的本机 API 详细信息，此外 [MSDN 上的 UWP 辅助功能指南](/windows/uwp/design/accessibility/basic-accessibility-information)介绍了该平台上的本机方法。 这些 API 用于在各个平台上完全实现可访问的应用程序。

针对各个基础平台上可用的所有辅助功能 API，Xamarin.Forms 目前尚不提供内置支持。 但是，它支持在用户界面元素上设置自动化属性，以支持屏幕阅读器和导航辅助工具，这是构建可访问应用程序的一个重要部分。 有关详细信息，请参见[自动化属性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)。

Xamarin.Forms 应用程序还可以指定控件的 Tab 键顺序，以改进可用性和辅助功能。 有关详细信息，请参阅[键盘辅助功能](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)。

其他辅助功能 API（例如 [iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)）可能更适用于 [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 或[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)实现。 本指南不涉及此内容。

## <a name="testing-accessibility"></a>测试辅助功能

通常情况下，Xamarin.Forms 应用程序将面向多个平台，这意味着需要根据平台测试辅助功能。 请按照以下链接，了解如何在每个平台上测试辅助功能：

- [iOS 测试](~/ios/app-fundamentals/accessibility.md)
- [Android 测试](~/android/app-fundamentals/accessibility.md)
- [Windows AccScope (MSDN)](/windows/win32/winauto/accscope)

## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [自动化属性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [键盘辅助功能](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]