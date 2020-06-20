---
title: Xamarin.Forms公共控件属性、方法和事件
description: 本文介绍了在 VisualElement 类中定义的、通常用于派生类的常见属性、方法和事件。
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3ab70dc20dda78e3acf400cf51d0ee9df84ff93
ms.sourcegitcommit: 16847681df17ed59b3b3528761c02e8fb48ffc4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104323"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Xamarin.Forms公共控件属性、方法和事件

Xamarin.Forms `VisualElement` 类是应用程序中使用的大多数控件的基类 Xamarin.Forms 。 `VisualElement`类定义了许多用于派生类的[属性](#properties)、[方法](#methods)和[事件](#events)。

## <a name="properties"></a>属性

以下属性在对象上可用 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

`AnchorX`属性是一个 `double` 值，用于定义 X 轴上用于缩放和旋转等转换的中心点。 默认值为0.5。

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

`AnchorY`属性是一个 `double` 值，用于定义 X 轴上用于缩放和旋转等转换的中心点。 默认值为0.5。

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

`BackgroundColor`属性是一个 `Color` ，它确定控件的背景色。 如果未设置，则背景将是 `Color` 以透明方式呈现的默认对象。

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

`Behaviors`属性是对象的 `List` `Behavior` 。 利用行为，你可以通过将可重用的功能添加到列表来将它们附加到元素 `Behaviors` 。 有关类的详细信息 `Behavior` ，请参阅[ Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

`Bounds`属性是一个只读 `Rectangle` 对象，它表示控件占用的空间。 `Bounds`属性值在布局周期期间分配。 `Rectangle` `struct` 包含用于测试矩形的交集和包容的有用属性和方法。 有关详细信息，请参阅[ Xamarin.Forms 矩形 API](xref:Xamarin.Forms.Rectangle)。

### `Clip`

`Clip`属性是一个 `Geometry` 对象，该对象定义元素内容的轮廓。 若要定义剪辑，请使用 `Geometry` 对象（例如） `EllipseGeometry` 来设置元素的 `Clip` 属性。 只有位于几何区域内的区域才可见。 有关详细信息，请参阅[剪辑几何图形](~/xamarin-forms/user-interface/shapes/geometries.md#clip-geometries)。

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

`Effects`属性是 `List` `Effect` 从 `Element` （x：）继承的对象的 Xamarin.Forms 。元素）类。 效果允许自定义本机控件，通常用于小样式更改。 有关类的详细信息 `Effect` ，请参阅[ Xamarin.Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

`FlowDirection`属性是一个 `FlowDirection` 枚举值。 流方向可以设置为 `MatchParent` 、 `LeftToRight` 或，还可以 `RightToLeft` 确定布局顺序和方向。 `FlowDirection`属性通常用于支持从右向左阅读的语言。

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

`Height`属性是一个只读 `double` 值，用于描述控件的呈现高度。 `Height`属性在布局周期期间计算，不能直接设置。 可以使用[HeightRequest 属性](#heightrequest)请求控件的高度。

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

`HeightRequest`属性是一个 `double` 值，该值确定控件所需的高度。 控件的绝对高度可能与请求的值不匹配。 有关详细信息，请参阅[请求属性](#request-properties)。

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

`InputTransparent`属性是一个 `bool` ，它确定控件是否接收用户输入。 默认值为 `false` ，确保元素接收输入。 设置此属性时，会将其传输到子元素。 `InputTransparent`将布局类的属性设置为 `true` 时，将导致布局中的所有元素都不接收输入。

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

`IsEnabled`属性是一个 `bool` 值，该值确定控件是否响应用户输入。 默认值是 `true`。 如果将此属性设置为 false，则控件不接受用户输入。

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

`IsFocused`属性是一个 `bool` 值，该值描述控件当前是否为重点对象。 [`Focus`](#focus)对控件调用方法将导致 `IsFocused` 值设置为 true。 调用 [`Unfocus`](#unfocus) 方法会将此属性设置为 false。

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

`IsTabStop`属性是一个 `bool` 值，用于定义当用户通过 tab 键向前移动控件时，控件是否接收焦点。 如果此属性为 false，则 `TabIndex` 属性将不起作用。

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

`IsVisible`属性是一个 `bool` 值，该值确定是否呈现控件。 `IsVisible`将不会显示属性设置为 false 的控件，也不会考虑布局周期期间的空间计算，也不能接受用户输入。

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

`MinimumHeightRequest`属性是一个 `double` 值，用于确定在两个元素争用有限空间时如何处理溢出。 设置 `MinimumHeightRequest` 属性允许布局过程将元素向下缩放到所请求的最小尺寸。 如果未 `MinimumHeightRequest` 指定，则默认值为-1，并且布局过程将认为 `HeightRequest` 是最小值。 这意味着没有值的元素 `MinimumHeightRequest` 将不具有可缩放的高度。

有关详细信息，请参阅[最小请求属性](#minimum-request-properties)。

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

`MinimumWidthRequest`属性是一个 `double` 值，用于确定在两个元素争用有限空间时如何处理溢出。 设置 `MinimumWidthRequest` 属性允许布局过程将元素向下缩放到所请求的最小尺寸。 如果未 `MinimumWidthRequest` 指定，则默认值为-1，并且布局过程将认为 `WidthRequest` 是最小值。 这意味着没有值的元素 `MinimumWidthRequest` 将无法缩放宽度。

有关详细信息，请参阅[最小请求属性](#minimum-request-properties)。

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

`Opacity`属性是 `double` 从零到1之间的一个值，该值确定在呈现过程中控件的不透明度。 此属性的默认值为1.0。 范围在0到1范围内的值将为限制。 `Opacity`仅当属性为时，才会应用属性 [`IsVisible`](#isvisible) `true` 。 不透明度是以迭代方式应用的。 因此，如果父控件具有0.5 不透明度，而其子控件具有0.5 不透明度，则该子控件将以有效的0.25 不透明度值呈现。 将 `Opacity` 输入控件的属性设置为0具有未定义的行为。

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

`Parent` 属性继承自 `Element` 类。 此属性是一个 `Element` 对象，它是控件的父级。 `Parent`如果将元素添加为另一个元素的子元素，则该属性通常自动设置为。

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

`Resources`属性是 `ResourceDictionary` 使用键/值对填充的实例，这些键/值对通常在运行时通过 XAML 填充。 此字典允许应用程序开发人员在编译时和运行时重复使用 XAML 中定义的对象。 字典中的键是从 `x:Key` XAML 标记的属性填充的。 从 XAML 创建的对象将插入到 `ResourceDictionary` 指定键的中。 初始化后。

有关详细信息，请参阅[资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

`Rotation`属性是一个 `double` 介于0到360之间的值，用于定义围绕 Z 轴的旋转（以度为单位）。 此属性的默认值为 0。 相对于和值应用旋转 [`AnchorX`](#anchorx) [`AnchorY`](#anchory) 。

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

`RotationX`属性是一个 `double` 介于0到360之间的值，用于定义围绕 X 轴的旋转（以度为单位）。 此属性的默认值为 0。 相对于和值应用旋转 [`AnchorX`](#anchorx) [`AnchorY`](#anchory) 。

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationY`属性是一个 `double` 介于0到360之间的值，用于定义围绕 Y 轴的旋转（以度为单位）。 此属性的默认值为 0。 相对于和值应用旋转 [`AnchorX`](#anchorx) [`AnchorY`](#anchory) 。

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

`Scale`属性是 `double` 定义控件的小数位数的值。 此属性的默认值为1.0。 根据 [`AnchorX`](#anchorx) 和值应用缩放 [`AnchorY`](#anchory) 。

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

`ScaleX`属性是一个 `double` 值，用于定义控件沿 X 轴的刻度。 此属性的默认值为1.0。 `ScaleX`相对于值应用属性 [`AnchorX`](#anchorx) 。

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

`ScaleY`属性是一个 `double` 值，用于定义控件沿 Y 轴的刻度。 此属性的默认值为1.0。 `ScaleY`相对于值应用属性 [`AnchorY`](#anchory) 。

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

`Style` 属性继承自 `NavigableElement` 类。 此属性是类的实例 `Style` 。 `Style`类包含用于定义可视元素外观和行为的触发器、setter 和行为。 有关详细信息，请参阅[ Xamarin.Forms XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

`StyleClass`属性是 `string` 对象的列表，这些对象表示类的名称 `Style` 。 此属性继承自 `NavigableElement` 类。 `StyleClass`属性允许将多个样式属性应用到 `VisualElement` 实例。 有关详细信息，请参阅[ Xamarin.Forms 样式类](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

`TabIndex`属性是一个 `int` 值，用于定义使用 tab 键向前滚动控件时的控件顺序。 `TabIndex`属性是类实现的接口上定义的属性的实现 `ITabStopElement` `VisualElement` 。

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

`TranslationX`属性是 `double` 定义要在 X 轴上应用的增量转换的值。 平移在布局之后应用，通常用于应用动画。 将元素转换为其父容器的边界外，我会阻止输入工作。

有关详细信息，请参阅[中 Xamarin.Forms 的动画](~/xamarin-forms/user-interface/animation/index.md)。

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

`TranslationY`属性是一个 `double` 值，用于定义要在 Y 轴上应用的增量转换。 平移在布局之后应用，通常用于应用动画。 将元素转换为其父容器的边界外，我会阻止输入工作。

有关详细信息，请参阅[中 Xamarin.Forms 的动画](~/xamarin-forms/user-interface/animation/index.md)。

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

`Triggers`属性是对象的只读 `List` `TriggerBase` 。 触发器允许应用程序开发人员在 XAML 中表达操作，这些操作更改控件的视觉外观，以响应事件或属性更改。 有关详细信息，请参阅[ Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

`Visual`属性是一个 `IVisual` 实例，该实例允许创建呈现器并有选择地应用于 `VisualElement` 实例。 `Visual`设置属性以匹配其父级，因此，在组件上定义呈现器也适用于该组件的任何子级。 如果未对控件或其上级设置自定义呈现器，则 Xamarin.Forms 将使用默认呈现器。 有关详细信息，请参阅[ Xamarin.Forms 视觉对象](~/xamarin-forms/user-interface/visual/index.md)。

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

`Width`属性是一个只读 `double` 值，用于描述控件的呈现宽度。 `Width`属性在布局周期期间计算，不能直接设置。 可以使用[WidthRequest 属性](#widthrequest)请求控件的宽度。

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

`WidthRequest`属性是一个 `double` 值，该值确定控件所需的宽度。 控件的绝对宽度可能与请求的值不匹配。 有关详细信息，请参阅[请求属性](#request-properties)。

### [`X`](xref:Xamarin.Forms.VisualElement.X)

`X`属性是一个只读 `double` 值，用于描述控件的当前 X 位置。

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

`Y`属性是一个只读 `double` 值，用于描述控件的当前 Y 位置。

## <a name="methods"></a>方法

类中提供了以下方法 `VisualElement` 。 有关完整列表，请参阅[VISUALELEMENT API 方法](xref:Xamarin.Forms.VisualElement#methods)。

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

此 `FindByName` 方法是从类继承的 `Element` ，并具有以下签名：

```csharp
public object FindByName (string name)
```

此方法搜索提供的参数的所有子元素 `name` ，并返回具有指定名称的元素。 如果未发现匹配项，则返回 `null`。

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

此 `Focus` 方法尝试将焦点设置到元素上。 此方法具有以下签名：

```csharp
public bool Focus ()
```

`Focus` `true` 如果成功设置了键盘焦点并且 `false` 方法调用未导致焦点更改，则此方法将返回。 元素必须能够接收此方法工作的焦点。 `Focus`对处于屏幕外或未实现的元素调用方法具有未定义的行为。

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

此 `Unfocus` 方法尝试移除元素上的焦点。 此方法具有以下签名：

```csharp
public void Unfocus ()
```

元素必须已有此方法的焦点才能工作。

## <a name="events"></a>事件

以下事件在类上可用 `VisualElement` 。 有关完整列表，请参阅[ Xamarin.Forms VisualElement 事件](xref:Xamarin.Forms.VisualElement#events)。

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

`Focused`只要 `VisualElement` 实例接收到焦点，就会引发事件。 此事件不是通过堆栈冒泡 Xamarin.Forms ，而是直接从本机控件接收的。 此事件由 [`IsFocused`](#isfocused) 属性 setter 发出。

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`SizeChanged`在 `VisualElement` 实例 `Height` 或 `Width` 属性发生更改时引发事件。 如果开发人员希望直接响应大小更改，而不是响应更改后事件，则它们应改为实现 [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) 虚方法。

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

`Unfocused`只要实例失去焦点，就会引发事件 `VisualElement` 。 此事件不是通过堆栈冒泡 Xamarin.Forms ，而是直接从本机控件接收的。 此事件由 [`IsFocused`](#isfocused) 属性 setter 发出。

## <a name="units-of-measurement"></a>度量单位

Android、iOS 和 UWP 平台都有不同的度量单位，它们在不同的设备上可能有所不同。 Xamarin.Forms使用独立于平台的度量单位，在设备和平台之间规范化单元。 每英寸有160单位，或每厘米64单位 Xamarin.Forms 。

## <a name="request-properties"></a>请求属性

名称包含 "request" 的属性定义所需的值，这可能与实际呈现的值不匹配。 例如， `HeightRequest` 可能设置为150，但如果布局仅允许100单元空间，则该控件的呈现 `Height` 将仅为100。 呈现的大小受可用空间和包含的组件影响。

## <a name="minimum-request-properties"></a>最小请求属性

最小请求属性包括 `MinimumHeightRequest` 和 `MinimumWidthRequest` ，旨在实现更精确地控制元素如何相互处理溢出。 但是，与这些属性相关的布局行为有一些重要的注意事项。

### <a name="unspecified-minimum-property-values"></a>未指定的最小属性值

如果未设置最小值，则 "最小值" 属性默认为-1。 布局过程将忽略此值，并将绝对值视为最小值。 此行为的实际结果是没有指定最小值的元素**将不会**收缩。 **将**收缩具有指定最小值的元素。

下面的 XAML 以水平显示了两个 `BoxView` 元素 `StackLayout` ：

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

第一个 `BoxView` 实例请求宽度为500，未指定最小宽度。 第二个 `BoxView` 实例请求宽度为500，最小宽度为250。 如果父元素的宽度 `StackLayout` 不足以在其请求的宽度内包含两个组件，则 `BoxView` 布局过程将考虑第一个实例的最小宽度为500，因为没有指定其他有效的最小值。 允许第二个 `BoxView` 实例向下缩放到250，它将收缩到最适合，直到其宽度达到250个单位。

如果所需行为是第一个 `BoxView` 实例缩小，且没有最小宽度，则 `MinimumWidthRequest` 必须设置为有效的值，例如0。

### <a name="minimum-and-absolute-property-values"></a>最小和绝对属性值

如果最小值大于绝对值，则该行为是不确定的。 例如，如果 `WidthRequest` 设置为100，则 `MinimumWidthRequest` 属性不应超过100。 指定最小属性值时，应始终指定一个绝对值，以确保绝对值大于最小值。

### <a name="minimum-properties-within-a-grid"></a>网格内的最小属性

`Grid`布局具有其自己的系统，用于相对调整行和列的大小。 `MinimumWidthRequest` `MinimumHeightRequest` 在布局中使用或 `Grid` 不会产生效果。 有关详细信息，请参阅[ Xamarin.Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md)。

## <a name="related-links"></a>相关链接

- [VisualElement API](xref:Xamarin.Forms.VisualElement)
