---
title: 在中创建自定义布局 Xamarin.Forms
description: 本文介绍如何编写自定义布局类，并演示一个区分方向的 WrapLayout 类，该类在页面上水平排列其子元素，然后将后续子项的显示范围封装到其他行。
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 63a939e7093bcbe52f1aed376253c7aa78b078bf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563843"
---
# <a name="create-a-custom-layout-in-no-locxamarinforms"></a>在中创建自定义布局 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Forms 定义五个布局类-StackLayout、AbsoluteLayout、RelativeLayout、Grid 和 FlexLayout，并以不同的方式排列其子级。但是，有时需要使用未提供的布局来组织页面内容 Xamarin.Forms 。本文介绍如何编写自定义布局类，并演示一个区分方向的 WrapLayout 类，该类在页面上水平排列其子元素，然后将后续子项的显示范围封装到其他行。_

在中 Xamarin.Forms ，所有布局类均派生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 类，并将泛型类型约束为 [`View`](xref:Xamarin.Forms.View) 及其派生类型。 反过来， `Layout<T>` 该类派生自 [`Layout`](xref:Xamarin.Forms.Layout) 类，该类提供了用于定位和调整子元素大小的机制。

每个可视元素都负责确定其自己的首选大小，这称为 *请求* 的大小。 [`Page`](xref:Xamarin.Forms.Page)、 [`Layout`](xref:Xamarin.Forms.Layout) 和 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 派生类型负责确定其子元素的位置和大小，或子级相对于其自身的位置和大小。 因此，布局涉及父子关系，其中的父关系确定其子级的大小，但会尝试容纳子请求的大小。

Xamarin.Forms若要创建自定义布局，需要全面了解布局和失效循环。 现在将讨论这些周期。

## <a name="layout"></a>布局

布局从可视化树的顶部到页面开始，并在可视化树的所有分支中进行，以涵盖页面上的每个可视元素。 作为其他元素的父级的元素负责调整其子级的相对于其自身的大小和位置。

[`VisualElement`](xref:Xamarin.Forms.VisualElement)类定义了 [ `Measure` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) # A3 方法，该方法度量布局操作的元素，[ `Layout` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms 。Rectangle) # A7 方法，该方法指定元素将在其中呈现的矩形区域。 当应用程序启动并显示第一页时，将在对象上启动一个 *布局循环* ，其中包含第一个 `Measure` 调用，然后 `Layout` 调用 [`Page`](xref:Xamarin.Forms.Page) ：

1. 在布局循环过程中，每个父元素都负责 `Measure` 在其子元素上调用方法。
1. 测量子级后，每个父元素都将负责在其子元素 `Layout` 上调用方法。

此循环可确保页上的每个可视元素都接收对 `Measure` 和 `Layout` 方法的调用。 下图显示了此过程：

![：： no (Xamarin) ：：： Layout 循环](custom-images/layout-cycle.png)

> [!NOTE]
> 请注意，如果更改了影响布局的内容，则布局循环也可能出现在可视化树的子集上。 这包括在集合中添加或删除的项，例如中的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 、元素属性中的 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 更改或元素的大小更改。

Xamarin.Forms具有或属性的每个类 `Content` `Children` 都具有可重写的 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法。 派生自的自定义布局类 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必须重写此方法，并确保 [ `Measure` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) # A3 和 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms 。在元素的所有子级上调用矩形) # A7 方法，以提供所需的自定义布局。

此外，派生自或的每个类都 [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必须重写 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法，在此方法中，布局类通过调用 [ `Measure` ] (x：来确定所需的大小 Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) 其子代的 # A3 方法。

> [!NOTE]
> 元素根据约束确定其大小，这些 *约束*指示元素父元素中的元素的可用空间量。 传递给 [ `Measure` ] (x：的约束 Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) # A3 和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法的范围可以介于0到之间 `Double.PositiveInfinity` 。 当某个元素收到对其 [] 的调用时，它将被 *约束*或 *完全约束* `Measure` (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags 使用非无限参数) # A3 方法-元素被限制为特定大小。 当元素接收到其方法的调用时，如果该元素具有至少一个等于的参数，则该*元素是不受约束的或**部分约束* `Measure` `Double.PositiveInfinity` 的–无限大约束可以视为指示自动调整。

## <a name="invalidation"></a>失效

失效是指页面中元素的更改会触发新的布局周期的过程。 当元素不再具有正确的大小或位置时，它们被视为无效。 例如，如果更改了的 [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 属性 [`Button`](xref:Xamarin.Forms.Button) ，则被认为是无效的， `Button` 因为它将不再具有正确的大小。 调整大小后， `Button` 可能会在页面的其余部分中更改布局的波纹效果。

元素通过调用方法使自身无效 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) ，通常是在元素的属性发生更改时，这可能会导致元素的新大小。 此方法将引发 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 事件，该事件由元素的父处理以触发新的布局周期。

[`Layout`](xref:Xamarin.Forms.Layout)类 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 在添加到其属性或集合的每个子元素上设置事件的处理程序 `Content` `Children` ，并在移除子级时分离该处理程序。 因此，当其中的一个子元素更改大小时，将会提醒可视化树中具有子级的每个元素。 下图说明了可视化树中元素的大小变化如何导致树上的更改出现：

![可视化树中的无效](custom-images/invalidation.png)

但是， `Layout` 类会尝试限制更改对页面布局的更改所造成的影响。 如果布局为大小限制，则子大小更改不会影响任何比可视化树中的父布局更高的布局。 但是，布局大小变化通常会影响布局排列其子级的方式。 因此，布局的大小中的任何更改都将开始布局的布局周期，并且布局将接收对其 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 和方法的调用 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 。

[`Layout`](xref:Xamarin.Forms.Layout)该类还定义了一个 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法，该方法具有与方法类似的用途 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) 。 `InvalidateLayout`无论布局的位置和大小如何调整其子级，都应调用方法。 例如，只要在 `Layout` `InvalidateLayout` 布局中添加或删除子级，类就会调用方法。

[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)可以重写以实现缓存，以最大程度地减少 [ `Measure` ] (x：的重复调用次数 Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) 布局子项的 # A3 方法。 重写 `InvalidateLayout` 方法将提供在布局中添加或删除子级时的通知。 同样， [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 可以重写方法，以便在布局中的一个子级更改大小时提供通知。 对于这两种方法重写，自定义布局应通过清除缓存来做出响应。 有关详细信息，请参阅 [计算和缓存布局数据](#calculate-and-cache-layout-data)。

## <a name="create-a-custom-layout"></a>创建自定义布局

创建自定义布局的过程如下所示：

1. 创建一个从 `Layout<View>` 类派生的类。 有关详细信息，请参阅 [创建 WrapLayout](#create-a-wraplayout)。
1. [*可选*]为应在 layout 类上设置的任何参数，添加由可绑定属性支持的属性。 有关详细信息，请参阅 [添加由可绑定属性支持的属性](#add-properties-backed-by-bindable-properties)。
1. 重写 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法以调用 [ `Measure` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags 对所有布局的子级) # A3 方法，并返回所请求的布局大小。 有关详细信息，请参阅 [重写 OnMeasure 方法](#override-the-onmeasure-method)。
1. 重写 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法以调用 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms 。矩形对所有布局的子级) # A3 方法。 未能调用 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms 。对于布局中的每个子元素) # A3 方法，子页面从不会接收到正确的大小或位置，因此子页面不会在页面上可见。 有关详细信息，请参阅 [重写 LayoutChildren 方法](#override-the-layoutchildren-method)。

    > [!NOTE]
    > 枚举和重写中的子级时 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) ，跳过其 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 属性设置为的任何子级 `false` 。 这将确保自定义布局不会为不可见子级留出空间。

1. [*可选*]重写 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法，以便在布局中添加或删除子级时收到通知。 有关详细信息，请参阅 [重写 InvalidateLayout 方法](#override-the-invalidatelayout-method)。
1. [*可选*]重写 [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 方法，以便在布局中的某个子元素的大小改变时收到通知。 有关详细信息，请参阅 [重写 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

> [!NOTE]
> 请注意， [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 如果布局的大小由父级而不是其子级控制，则不会调用该重写。 但是，如果其中一个或两个约束都是无限的，或者如果布局类具有非默认 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 值或属性值，则将调用该重写 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 出于此原因， [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 重写不能依赖于方法调用期间获得的子大小 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 。 相反， `LayoutChildren` 必须调用 [ `Measure` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags 在调用 [] (x 之前，对布局的子级) # A3 方法 `Layout` ： Xamarin.Forms 。VisualElement (Xamarin.Forms 。矩形) # A7 方法。 或者，可以缓存在重写中获得的子级的大小 `OnMeasure` ，以避免 `Measure` 在重写中出现以后的调用 `LayoutChildren` ，但 layout 类需要知道何时需要重新获得大小。 有关详细信息，请参阅 [计算和缓存布局数据](#calculate-and-cache-layout-data)。

然后，可以通过将布局类添加到 [`Page`](xref:Xamarin.Forms.Page) ，并将子级添加到布局来使用布局类。 有关详细信息，请参阅 [使用 WrapLayout](#consume-the-wraplayout)。

### <a name="create-a-wraplayout"></a>创建 WrapLayout

该示例应用程序演示了一个区分方向的 `WrapLayout` 类，它在页面上水平排列其子级，然后将后续子项的显示包装到其他行。

`WrapLayout`类为每个子元素分配相同的空间量（称为*单元大小*），这取决于子元素的最大大小。 小于单元大小的子级可以基于其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和属性值定位在单元格中 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。

`WrapLayout`类定义如以下代码示例所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>计算和缓存布局数据

`LayoutData`结构将有关子集合的数据存储在多个属性中：

- `VisibleChildCount` -布局中的可见子级的数目。
- `CellSize` –所有子级的最大大小，调整为布局的大小。
- `Rows` –行数。
- `Columns` –列数。

此 `layoutDataCache` 字段用于存储多个 `LayoutData` 值。 当应用程序启动时， `LayoutData` 会将两个对象缓存到 `layoutDataCache` 当前方向的字典中–一个用于重写的约束参数 `OnMeasure` ，另一个用于重写的 `width` 和 `height` 参数 `LayoutChildren` 。 在将设备旋转到横向方向时， `OnMeasure` 重写和 `LayoutChildren` 重写将再次调用，这将导致将另两个 `LayoutData` 对象缓存到字典中。 但是，在将设备返回到纵向方向时，无需进一步计算，因为 `layoutDataCache` 已具有所需的数据。

下面的代码示例演示 `GetLayoutData` 方法，该方法 `LayoutData` 基于特定大小计算结构化的属性：

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

`GetLayoutData`方法执行以下操作：

- 它确定计算所得的 `LayoutData` 值是否已在缓存中，如果可用，则将其返回。
- 否则，它将枚举所有子级，并 `Measure` 在每个具有无限宽度和高度的子级上调用方法，并确定最大子大小。
- 如果至少有一个可见子级，它将计算所需的行数和列数，然后根据的尺寸计算子级的单元大小 `WrapLayout` 。 请注意，单元格的大小通常比最大子大小稍微大一些，但如果对于最大的子级或更高的子级足够宽，则该单元格的大小也可能更小 `WrapLayout` 。
- 它将新 `LayoutData` 值存储在缓存中。

#### <a name="add-properties-backed-by-bindable-properties"></a>添加可绑定属性支持的属性

`WrapLayout`类定义 `ColumnSpacing` 和 `RowSpacing` 属性，其值用于分隔布局中的行和列，以及由可绑定属性支持的列。 下面的代码示例显示了可绑定的属性：

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

每个可绑定属性的属性更改处理程序将调用 `InvalidateLayout` 方法重写，以在上触发新的布局传递 `WrapLayout` 。 有关详细信息，请参阅 [重写 InvalidateLayout 方法](#override-the-invalidatelayout-method) 并重 [写 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

#### <a name="override-the-onmeasure-method"></a>重写 OnMeasure 方法

`OnMeasure`下面的代码示例显示了替代：

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

重写调用 `GetLayoutData` 方法，并 `SizeRequest` 从返回的数据构造对象，同时考虑 `RowSpacing` 和 `ColumnSpacing` 属性值。 有关方法的详细信息 `GetLayoutData` ，请参阅 [计算和缓存布局数据](#calculate-and-cache-layout-data)。

> [!IMPORTANT]
> [ `Measure` ] (x： Xamarin.Forms 。VisualElement (Xamarin.Forms ，则为。MeasureFlags) # A3 和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法不应通过返回 [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) 属性设置为的值来请求无限维度 `Double.PositiveInfinity` 。 但至少有一个约束参数 `OnMeasure` 可以为 `Double.PositiveInfinity` 。

#### <a name="override-the-layoutchildren-method"></a>重写 LayoutChildren 方法

`LayoutChildren`下面的代码示例显示了替代：

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

重写将从对方法的调用开始 `GetLayoutData` ，然后将所有子级枚举为大小，并将它们放置在每个子单元格内。 这是通过调用 [ `LayoutChildIntoBoundingRegion` ] (x：来实现的 Xamarin.Forms 。LayoutChildIntoBoundingRegion (Xamarin.Forms 。VisualElement、 Xamarin.Forms 。矩形) # A3 方法，该方法用于根据元素的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和属性值在矩形中定位子元素 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 这等效于对子级的 [ `Layout` ] (x：进行调用 Xamarin.Forms 。VisualElement (Xamarin.Forms 。矩形) # A3 方法。

> [!NOTE]
> 请注意，传递给方法的矩形 `LayoutChildIntoBoundingRegion` 包括子可驻留的整个区域。

有关方法的详细信息 `GetLayoutData` ，请参阅 [计算和缓存布局数据](#calculate-and-cache-layout-data)。

#### <a name="override-the-invalidatelayout-method"></a>重写 InvalidateLayout 方法

在将 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 子级添加到布局中或从布局中删除子级时，或在其中一个 `WrapLayout` 属性更改值时调用替代，如下面的代码示例所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

重写使布局失效，并放弃所有缓存的布局信息。

> [!NOTE]
> 若要在 [`Layout`](xref:Xamarin.Forms.Layout) [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 从布局中添加或删除子级时停止调用方法的类，请重写 [ `ShouldInvalidateOnChildAdded` ] (x： Xamarin.Forms 。ShouldInvalidateOnChildAdded (Xamarin.Forms 。查看) # A3 和 [ `ShouldInvalidateOnChildRemoved` ] (x： Xamarin.Forms 。ShouldInvalidateOnChildRemoved (Xamarin.Forms 。查看) # A7 方法，并返回 `false` 。 然后，layout 类可以在添加或移除子级时实现自定义进程。

#### <a name="override-the-onchildmeasureinvalidated-method"></a>重写 OnChildMeasureInvalidated 方法

[`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)当某个布局的子级更改大小时，将调用替代，如以下代码示例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

重写会使子布局失效，并放弃所有缓存的布局信息。

### <a name="consume-the-wraplayout"></a>使用 WrapLayout

`WrapLayout`通过将类放置在派生类型上，可以使用类 [`Page`](xref:Xamarin.Forms.Page) ，如下面的 XAML 代码示例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

下面显示了等效的 c # 代码：

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

然后，可以根据需要将子级添加到 `WrapLayout` 。 下面的代码示例演示 [`Image`](xref:Xamarin.Forms.Image) 添加到中的元素 `WrapLayout` ：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

当包含的页 `WrapLayout` 出现时，示例应用程序会以异步方式访问包含照片列表的远程 JSON 文件， [`Image`](xref:Xamarin.Forms.Image) 并为每张照片创建一个元素，并将其添加到中 `WrapLayout` 。 这会导致如以下屏幕截图中所示的外观：

![示例应用程序纵向屏幕截图](custom-images/portait-screenshots.png)

下面的屏幕截图在 `WrapLayout` 旋转到横向后显示：

![示例 iOS 应用程序横向屏幕截图示例 Android 应用程序横向屏幕截图示例 ](custom-images/landscape-ios.png)
 ![ ](custom-images/landscape-android.png)
 ![ UWP 应用程序环境屏幕截图](custom-images/landscape-uwp.png)

每行中的列数取决于照片大小、屏幕宽度和每个与设备无关的单位的像素数。 这些 [`Image`](xref:Xamarin.Forms.Image) 元素将异步加载照片，因此， `WrapLayout` 类将接收对其方法的频繁调用， [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 因为每个 `Image` 元素都会收到基于已加载照片的新大小。

## <a name="related-links"></a>相关链接

- [WrapLayout (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [自定义布局](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [在 (视频中创建自定义布局 Xamarin.Forms) ](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [布局\<T>](xref:Xamarin.Forms.Layout`1)
- [布局](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)