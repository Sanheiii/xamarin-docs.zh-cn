---
title: Xamarin.Forms 材料视觉对象
description: Xamarin.Forms 材料视觉对象可用于创建 Xamarin.Forms 在 iOS 和 Android 上大致完全相同的应用程序。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 753644b372fafc7c3ea22008267844251527050d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556472"
---
# <a name="no-locxamarinforms-material-visual"></a>Xamarin.Forms 材料视觉对象

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材料设计](https://material.io) 是 Google 创建的固执设计系统，它规定了视图和布局的外观和行为方式的大小、颜色、间距和其他方面。

Xamarin.Forms 材料视觉对象可用于将材料设计规则应用于 Xamarin.Forms 应用程序，从而创建在 iOS 和 Android 上大致完全相同的应用程序。 启用材料视觉对象后，支持的视图将采用相同的设计跨平台，从而形成统一的外观。

[![材料可视化屏幕截图](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

Xamarin.Forms在应用程序中启用材料视觉对象的过程如下：

1. 添加[ Xamarin.Forms 。](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)适用于 iOS 和 Android 平台项目的视觉对象 NuGet 包。 此 NuGet 包提供 iOS 和 Android 上经过优化的材料设计呈现器。 在 iOS 上，包提供与 [MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可传递依赖项，这是一个 c # 绑定到 Google 的 [ios 材料组件](https://material.io/develop/ios/)。 在 Android 上，包提供了生成目标，以确保正确设置 TargetFramework。
1. 初始化每个平台项目中的材料视觉对象。 有关详细信息，请参阅 [初始化材料视觉对象](#initialize-material-visual)。
1. 通过将 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 属性设置为， `Material` 在应采用材料设计规则的任何页上，创建材料视觉对象。 有关详细信息，请参阅 [使用材料呈现](#apply-material-visual)器。
1. 可有可无自定义材料控件。 有关详细信息，请参阅 [自定义材料控件](#customize-material-visual)。

> [!IMPORTANT]
> 在 Android 上，材料视觉对象要求最低版本 5.0 (API 21) 或更高版本，以及版本 9.0 (API 28) 的 TargetFramework。 此外，你的平台项目需要28.0.0 或更高版本的 Android 支持库，其主题需要从材料组件主题继承或继续从 AppCompat 主题继承。 有关详细信息，请参阅 [适用于 Android 的材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)入门。

材料视觉对象目前支持以下控件：

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

材料控件通过材料呈现器来实现，这些呈现器应用材料设计规则。 在功能上，内容呈现器与默认呈现器没有区别。 有关详细信息，请参阅 [自定义材料视觉对象](#customize-material-visual)。

## <a name="initialize-material-visual"></a>初始化材料视觉对象

安装后[ Xamarin.Forms 。"视觉对象](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)" NuGet 包，必须在每个平台项目中初始化材料呈现器。

在 iOS 上，此方法应在 **AppDelegate.cs** 中出现，方法是在 `Xamarin.Forms.FormsMaterial.Init` 方法 *之后* 调用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，此方法应在 **MainActivity.cs** 中出现，方法是在 `Xamarin.Forms.FormsMaterial.Init` 方法 *之后* 调用方法 `Xamarin.Forms.Forms.Init` ：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>应用材料视觉对象

应用程序可以通过将 [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) 页面、布局或视图上的属性设置为来启用材料视觉对象 `Material` ：

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

如果将 `VisualElement.Visual` 属性设置为，则 `Material` 指示应用程序使用材料视觉呈现器而不是默认呈现器。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性可以设置为任何实现的类型 `IVisual` ， [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) 该类提供以下 `IVisual` 属性：

- `Default` –指示应使用默认呈现器呈现视图。
- `MatchParent` –指示视图应使用与它的直接父级相同的呈现器。
- `Material` –指示视图应使用材料呈现器呈现。

> [!IMPORTANT]
> [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性是在类中定义的 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，视图 `Visual` 从其父项继承属性值。 因此，设置 `Visual` 上的属性 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以确保页面中任何受支持的视图都将使用该视觉对象。 此外， `Visual` 可以在视图上重写属性。

以下屏幕截图显示了使用默认呈现器呈现的用户界面：

[![IOS 和 Android 上默认呈现器的屏幕截图](material-visual-images/default-renderers.png "使用默认呈现器的视图")](material-visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示了使用材料呈现器呈现的相同用户界面：

[![IOS 和 Android 上的材料呈现器的屏幕截图](material-visual-images/material-renderers.png "使用材料呈现器的视图")](material-visual-images/material-renderers-large.png#lightbox)

默认呈现器和材料呈现器之间的主要可见差异（如下所示）是材料呈现器将文本呈现为大写 [`Button`](xref:Xamarin.Forms.Button) ，并将边框的角变大 [`Frame`](xref:Xamarin.Forms.Frame) 。 但是，材料呈现器使用本机控件，因此，字体、阴影、颜色和提升等区域的平台之间可能仍然存在用户界面差异。

> [!NOTE]
> 材料设计组件严格遵守 Google 的准则。 因此，材料设计呈现器倾向于调整大小和行为。 如果需要更好地控制样式或行为，则仍可创建自己的 [效果](~/xamarin-forms/app-fundamentals/effects/index.md)、 [行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)或 [自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) 器来实现所需的详细信息。

## <a name="customize-material-visual"></a>自定义材料视觉对象

材料 Visual NuGet 包是实现控件的呈现器的集合 Xamarin.Forms 。 自定义材料视觉对象与自定义默认控件完全相同。

如果目标是自定义现有控件，则建议使用效果。 如果存在材料可视化呈现器，则使用效果自定义控件的效果要比将呈现器划分为子类更少。 有关效果的详细信息，请参阅[ Xamarin.Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

当材料呈现器不存在时，建议使用自定义呈现器。 以下呈现器类包含在材料视觉对象中：

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

对于材料呈现器，与非材料呈现器的子类几乎相同。 但是，当导出子类呈现时，必须为指定类型的属性提供第三个参数 `ExportRenderer` `VisualMarker.MaterialVisual` ：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

在此示例中， `ExportRendererAttribute` 指定 `CustomMaterialProgressBarRenderer` 类将用于呈现 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 视图，并将 `IVisual` 类型注册为第三个参数。

> [!NOTE]
> 指定一个 `IVisual` 类型（作为其一部分）的呈现器 `ExportRendererAttribute` 将用于呈现所选择的视图，而不是默认呈现器。 在呈现器选择时间，将 `Visual` 检查视图的属性并将其包含在呈现器选择过程中。

有关自定义呈现器的详细信息，请参阅 [自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

## <a name="related-links"></a>相关链接

- [材料视觉 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [创建 Xamarin.Forms 可视化呈现器](create.md)
- [Xamarin.Forms 结果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)