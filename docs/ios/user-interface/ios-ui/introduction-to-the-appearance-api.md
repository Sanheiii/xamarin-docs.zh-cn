---
title: Xamarin 中的外观 API
description: 使用 iOS，你可以在静态类级别（而不是单个对象）应用视觉对象属性设置，以便将更改应用于应用程序中该控件的所有实例。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7a7f0fe9d0dc07d892686e6596f3cc09a2587513
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003382"
---
# <a name="appearance-api-in-xamarinios"></a>Xamarin 中的外观 API

_使用 iOS，你可以在静态类级别（而不是单个对象）应用视觉对象属性设置，以便将更改应用于应用程序中该控件的所有实例。_

此功能在支持它的所有 UIKit 控件上通过静态 `Appearance` 属性在 Xamarin 中公开。 因此，可以轻松地自定义视觉外观（如淡色颜色和背景图像之类的属性），以使应用程序具有一致的外观。 此外观 API 是在 iOS 5 中引入的，而它的某些部分已在 iOS 9 中弃用，但仍是在 Xamarin iOS 应用程序中实现某些样式和主题效果的好办法。

## <a name="overview"></a>概述

iOS 允许您自定义许多 UIKit 控件的外观，使标准控件符合您要应用于应用程序的品牌。

可以通过两种不同的方法来应用自定义外观：

- **直接在控件实例上**–您可以在许多控件（包括工具栏、导航栏、按钮和滑块）上设置淡色颜色、背景图像和标题位置（以及其他某些属性）。

- **设置外观静态属性中的默认值**–每个控件的可自定义属性通过 `Appearance` 静态属性公开。 应用于这些属性的任何自定义将用作在设置该属性后创建的该类型的任何控件的默认值。

外观示例应用程序演示了所有这三种方法，如以下屏幕截图所示：

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "The Appearance sample application demonstrates all three methods")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

在 iOS 8 中，外观代理已扩展到 TraitCollections。
 `AppearanceForTraitCollection` 可用于设置特定特征集合的默认外观。 有关详细信息，请参阅[情节提要指南简介](~/ios/user-interface/storyboards/unified-storyboards.md)。

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

绿色元素样式的设置如下所示 `ViewDidLoad` 方法，该方法将重写默认值和*外观*静态类：

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

在 Xamarin. Forms 解决方案中[设置 iOS 应用的样式](~/xamarin-forms/platform/ios/formatting.md#uiappearance)时，外观 API 会很有用。 `AppDelegate` 类中的几行有助于实现特定配色方案，而无需创建[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

### <a name="custom-themes-and-uiappearance"></a>自定义主题和 UIAppearance

iOS 允许多个用户界面控件的可视属性使用*UIAppearance* api "主题"，以强制特定控件的所有实例具有相同的外观。 这在许多用户界面控件类（而不是控件的单个实例）上作为外观属性公开。 设置静态 `Appearance` 属性的显示属性将影响应用程序中该类型的所有控件。

为了更好地理解这一概念，请考虑一个示例。

若要将特定 `UISegmentedControl` 更改为具有洋红色淡色，请在 `ViewDidLoad`中引用此类控件，如下所示：

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在设计器的 "属性" pad 中设置值：

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Properties Pad Tint")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下图说明，这只会在名为 "sg1" 的控件上设置色调。

[![](introduction-to-the-appearance-api-images/image53.png "Setting the individual control tint")](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要以这种方式设置许多控件，这会完全低效，因此，我们可以在类本身上设置静态 `Appearance` 属性。 下面的代码对此进行了演示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下图显示了将外观设置为洋红色的分段控件：

[![](introduction-to-the-appearance-api-images/image54.png "Setting the Appearance control tint")](introduction-to-the-appearance-api-images/image54.png#lightbox)

应在应用程序生命周期中及早设置 `Appearance` 属性，如在 AppDelegate 的 `FinishedLaunching` 事件中，或在显示受影响的控件之前的 ViewController 中。

有关更多详细信息，请参阅[外观 API 简介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。

## <a name="related-links"></a>相关链接

- [外观（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/appearance)
- [UIAppearance 协议参考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin 中的外观](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
