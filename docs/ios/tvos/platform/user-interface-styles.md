---
title: Xamarin 中的 tvOS 用户界面样式
description: 本文介绍 Apple 添加到 tvOS 10 的轻型和深色 UI 主题，以及如何在 tvOS 应用程序中实现它们。
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 8d9facdd35a9048a93c17f1194d5e672edd9d798
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030570"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Xamarin 中的 tvOS 用户界面样式

_本文介绍 Apple 添加到 tvOS 10 的轻型和深色 UI 主题，以及如何在 tvOS 应用程序中实现它们。_

tvOS 10 现在支持一个深色和浅色用户界面主题，所有内置 UIKit 控件都将根据用户的偏好自动调整。 此外，开发人员还可以根据用户选择的主题手动调整 UI 元素，并且可以重写给定主题。

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>关于新的用户界面样式

如上所述，tvOS 10 现在同时支持深色和浅色用户界面主题，所有内置 UIKit 控件都将根据用户的偏好自动调整。

用户可以通过转到 "**设置**" > **一般** > **外观**并在**浅色**和**深色**之间切换：

[![](user-interface-styles-images/theme01.png "The Settings app")](user-interface-styles-images/theme01.png#lightbox)

选择了**深色**主题后，所有用户界面元素都将在深色背景上切换到浅文本：

[![](user-interface-styles-images/theme02.png "The Dark theme")](user-interface-styles-images/theme02.png#lightbox)

用户可以随时切换主题，也可以基于当前活动（Apple TV 所在位置或当天的时间）来执行此操作。

轻型 UI 主题为默认主题，任何现有的 tvOS 应用程序仍将使用浅色主题，而不考虑用户的首选项，除非对 tvOS 10 进行了修改以利用深色主题。 TvOS 10 应用还能够覆盖当前主题，并始终对其部分或全部 UI 使用浅色或深色主题。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>采用浅色和深色主题

为了支持此功能，Apple 已向 `UITraitCollection` 类添加了一个新的 API，tvOS 应用必须选择支持暗外观（通过其 `Info.plist` 文件中的设置）。

若要选择支持轻型和深色主题，请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 从编辑器的底部选择**源**视图。
3. 添加新的密钥并将其调用 `UIUserInterfaceStyle`：

    [![](user-interface-styles-images/theme03.png "The UIUserInterfaceStyle key")](user-interface-styles-images/theme03.png#lightbox)
4. 将类型设置为 "`String`"，然后输入 `Automatic`值：

    [![](user-interface-styles-images/theme04.png "Enter Automatic")](user-interface-styles-images/theme04.png#lightbox)
5. 保存对该文件所做的更改。

`UIUserInterfaceStyle` 项有三个可能的值：

- **浅**-强制 tvOS 应用的 UI 始终使用浅色主题。
- **深色**-强制 tvOS 应用的 UI 始终使用深色主题。
- **自动**-基于用户在 "设置" 中的首选项在 "光源" 和 "深色" 主题之间切换。 这是首选设置。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 主题支持

如果 tvOS 应用使用的是标准内置 `UIView` 的控件，则他们将自动响应 UI 主题，无需任何开发人员干预。

此外，`UILabel` 和 `UITextView` 会根据选择的 UI 主题自动更改其颜色：

- 浅主题中的文本将为黑色。
- 深色主题中的文本将为白色。

如果开发人员曾经手动更改文本颜色（在情节提要或代码中），他们将负责根据 UI 主题处理颜色更改。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的模糊效果

为了支持 tvOS 10 应用中的浅色和深色主题，Apple 添加了两种新的模糊效果。 这些新效果将根据用户选择的 UI 主题自动调整模糊，如下所示：

- `UIBlurEffectStyleRegular`-在浅色主题和深色主题中使用浅模糊。
- `UIBlurEffectStyleProminent`-在浅色主题和深色主题中使用超大模糊效果。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特征集合

`UITraitCollection` 类的新 `UserInterfaceStyle` 属性可用于获取当前选定的 UI 主题，将是以下值之一的 `UIUserInterfaceStyle` 枚举：

- **浅**-已选择光源 UI 主题。
- **深色**-已选中深色 UI 主题。
- **未指定**-视图尚未显示在屏幕上，因此当前 UI 主题未知。

此外，特征集合在 tvOS 10 中具有以下功能：

- 可以基于给定 `UITraitCollection` 的 `UserInterfaceStyle` 自定义外观代理，以根据主题更改图像或项颜色等内容。
- TvOS 应用可以通过重写 `UIView` 或 `UIViewController` 类的 `TraitCollectionDidChange` 方法来处理特征集合更改。

> [!IMPORTANT]
> TvOS 10 的 tvOS 早期预览版尚不完全支持 `UITraitCollection` 的 `UIUserInterfaceStyle`。 未来版本中将添加完全支持。

<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>基于主题自定义外观

对于支持外观代理的用户界面元素，可以根据其特征集合的 UI 主题来调整其外观。 因此，对于给定的 UI 元素，开发人员可以为浅色主题指定一种颜色，为深色主题指定另一种颜色。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> 遗憾的是，tvOS 10 的 tvOS 预览版不完全支持 `UITraitCollection``UIUserInterfaceStyle`，因此这种类型的自定义项尚不可用。 未来版本中将添加完全支持。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接响应主题更改

在开发人员需要根据所选 UI 主题更深入地控制 UI 元素的外观，他们可以重写 `UIView` 或 `UIViewController` 类的 `TraitCollectionDidChange` 方法。

例如:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>重写特征集合

基于 tvOS 应用程序的设计，有时，开发人员可能需要重写给定用户界面元素的特征集合，并使其始终使用特定的 UI 主题。

可以通过对 `UIViewController` 类使用 `SetOverrideTraitCollection` 方法来完成此操作。 例如:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

有关详细信息，请参阅我们的[统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md)的[特性](~/ios/user-interface/storyboards/unified-storyboards.md)和[覆盖特征](~/ios/user-interface/storyboards/unified-storyboards.md)部分。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特征集合和情节提要

在 tvOS 10 中，可以将应用的情节提要设置为对特征集合做出响应，并且很多 UI 元素都可感知亮度和深色主题。 当前的 Xamarin tvOS 早期预览版 for tvOS 10 尚不支持接口设计器中的此功能，因此，需要在 Xcode 的 Interface Builder 中编辑该情节提要作为一种解决方法。

若要启用特征收集支持，请执行以下操作：

1. 右键单击 "**解决方案资源管理器**中的情节提要文件，然后选择"**打开方式**" > **Xcode" Interface Builder**：

    [![](user-interface-styles-images/theme05.png "Open With Xcode Interface Builder")](user-interface-styles-images/theme05.png#lightbox)
2. 若要启用特性集合支持，请切换到**文件检查器**并在 " **Interface Builder 文档**" 部分中选中 "**使用特征变体**" 属性：

    [![](user-interface-styles-images/theme06.png "Enable Trait Collection support")](user-interface-styles-images/theme06.png#lightbox)
3. 确认更改以使用特征变体：

    [![](user-interface-styles-images/theme07.png "The use Trait Variations alert")](user-interface-styles-images/theme07.png#lightbox)
4. 保存对情节提要文件所做的更改。

在 Interface Builder 中编辑 tvOS 情节提要时，Apple 添加了以下功能：

- 开发人员可以根据**特性检查器**中的 UI 主题来指定不同的用户界面元素变体：

  - 现在有多个属性旁有一个 **+** ，可以单击这些属性来添加 UI 主题特定版本：

    [![](user-interface-styles-images/theme08.png "Add a UI theme specific version")](user-interface-styles-images/theme08.png#lightbox)

  - 开发人员可以指定新属性或单击**x**按钮将其删除：

    [![](user-interface-styles-images/theme09.png "Specify a new property or click the x button to remove it")](user-interface-styles-images/theme09.png#lightbox)
- 开发人员可以从 Interface Builder 中预览 "浅色" 或 "深色" 主题中的 UI 设计：

  - Design Surface 的底部允许开发人员切换当前 UI 主题：

    [![](user-interface-styles-images/theme10.png "The bottom of the Design Surface")](user-interface-styles-images/theme10.png#lightbox)

  - 新主题将在 Interface Builder 中显示，并将显示任何特定于特征的集合：

    [![](user-interface-styles-images/theme11.png "The theme displayed in Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

此外，tvOS 模拟器现在提供了一个键盘快捷键，使开发人员能够在调试 tvOS 应用时在浅色和深色主题之间快速切换。 使用**Command-D**键盘序列在浅色和深色之间切换。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了 Apple 添加到 tvOS 10 的轻型和深色 UI 主题，以及如何在 tvOS 应用程序中实现它们。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
