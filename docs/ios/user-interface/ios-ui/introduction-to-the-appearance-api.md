---
title: Xamarin 中的外观 API
description: 使用 iOS，你可以在静态类级别（而不是单个对象）应用视觉对象属性设置，以便将更改应用于应用程序中该控件的所有实例。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7ba6eca8f74c10254ae93b95725bc73ae100be70
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433849"
---
# <a name="appearance-api-in-xamarinios"></a>Xamarin 中的外观 API

_使用 iOS，你可以在静态类级别（而不是单个对象）应用视觉对象属性设置，以便将更改应用于应用程序中该控件的所有实例。_

在 `Appearance` 支持它的所有 UIKit 控件上，通过静态属性在 Xamarin 中公开此功能。 因此，可以轻松地自定义视觉外观 (诸如淡色颜色和背景图像) 等属性，使应用程序具有一致的外观。 此外观 API 是在 iOS 5 中引入的，而它的某些部分已在 iOS 9 中弃用，但仍是在 Xamarin iOS 应用程序中实现某些样式和主题效果的好办法。

## <a name="overview"></a>概述

iOS 允许您自定义许多 UIKit 控件的外观，使标准控件符合您要应用于应用程序的品牌。

可以通过两种不同的方法来应用自定义外观：

- **直接在控件实例上** -可以设置色调颜色、背景图像和标题位置 (以及许多控件) （包括工具栏、导航栏、按钮和滑块）。

- **设置外观静态属性中的默认值** –每个控件的可自定义属性通过 `Appearance` 静态属性公开。 应用于这些属性的任何自定义将用作在设置该属性后创建的该类型的任何控件的默认值。

外观示例应用程序演示了所有这三种方法，如以下屏幕截图所示：

[![外观示例应用程序演示了所有这三种方法](introduction-to-the-appearance-api-images/appearance01-sml.png)](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

在 iOS 8 中，外观代理已扩展到 TraitCollections。
 `AppearanceForTraitCollection` 可用于设置特定特征集合的默认外观。 有关详细信息，请参阅 [情节提要指南简介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

## <a name="setting-appearance-properties"></a>设置外观属性

在第一个屏幕中，使用静态外观类来为按钮和黄色/橙色元素样式，如下所示：

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

在 `ViewDidLoad` 重写默认值和 *外观* 静态类的方法中，此绿色元素样式的设置如下所示：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>在 Xamarin 中使用 UIAppearance

在 Xamarin. Forms 解决方案中 [设置 iOS 应用的样式](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api) 时，外观 API 会很有用。 类中的几行 `AppDelegate` 有助于实现特定配色方案，而无需创建 [自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

### <a name="custom-themes-and-uiappearance"></a>自定义主题和 UIAppearance

iOS 允许多个用户界面控件的可视属性使用 *UIAppearance* api "主题"，以强制特定控件的所有实例具有相同的外观。 这在许多用户界面控件类（而不是控件的单个实例）上作为外观属性公开。 设置静态属性的显示属性将 `Appearance` 影响应用程序中该类型的所有控件。

为了更好地理解这一概念，请考虑一个示例。

若要将特定的更改 `UISegmentedControl` 为具有洋红色淡色，我们将在屏幕上引用此类控件，如下所示 `ViewDidLoad` ：

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在设计器的 "属性" pad 中设置值：

[![Properties Pad 淡色](introduction-to-the-appearance-api-images/propertiespadtint.png)](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下图说明，这只会在名为 "sg1" 的控件上设置色调。

[![设置各个控件的色调](introduction-to-the-appearance-api-images/image53.png)](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要以这种方式设置许多控件完全低效，可以改为在 `Appearance` 类本身上设置静态属性。 下面的代码对此进行了演示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下图显示了将外观设置为洋红色的分段控件：

[![设置外观控件色调](introduction-to-the-appearance-api-images/image54.png)](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` 在应用程序生命周期（如 AppDelegate 的 `FinishedLaunching` 事件中）或 ViewController 中显示受影响的控件之前，应提前设置属性。

有关更多详细信息，请参阅 [外观 API 简介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) 。

## <a name="related-links"></a>相关链接

- [外观 (示例) ](/samples/xamarin/ios-samples/appearance)
- [UIAppearance 协议参考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin 中的外观](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api)