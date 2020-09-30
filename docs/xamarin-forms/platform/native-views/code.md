---
title: 采用 C# 的本机视图
description: '可以直接从 Xamarin.Forms 使用 c # 创建的页面中引用 iOS、Android 和 UWP 的本机视图。 本文演示如何将本机视图添加到 Xamarin.Forms 使用 c # 创建的布局中，以及如何替代自定义视图的布局来更正其度量 API 使用情况。'
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 71df780c648bcaa5a2ca4db388b52ac77a64d158
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560541"
---
# <a name="native-views-in-c"></a>C 中的本机视图\#

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_可以直接从 Xamarin.Forms 使用 c # 创建的页面中引用 iOS、Android 和 UWP 的本机视图。本文演示如何将本机视图添加到 Xamarin.Forms 使用 c # 创建的布局中，以及如何替代自定义视图的布局来更正其度量 API 使用情况。_

## <a name="overview"></a>概述

Xamarin.Forms允许 `Content` 设置或具有集合的任何控件 `Children` 都可以添加平台特定的视图。 例如， `UILabel` 可以将 iOS 直接添加到 [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 属性，或添加到 [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) 集合。 但请注意，此功能需要 `#if` 在 Xamarin.Forms 共享项目解决方案中使用定义，并且不能从 Xamarin.Forms .NET Standard 库解决方案中使用。

以下屏幕截图演示了已添加到中的平台特定的视图 Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

[![包含特定于平台的视图的 StackLayout](code-images/screenshots-sml.png)](code-images/screenshots.png#lightbox "包含特定于平台的视图的 StackLayout")

将平台特定的视图添加到布局的功能 Xamarin.Forms 在每个平台上都由两个扩展方法启用：

- `Add` –将平台特定的视图添加到 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 布局的集合。
- `ToView` –获取特定于平台的视图，并将其包装为 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 可设置为控件的 `Content` 属性的。

在共享项目中使用这些方法 Xamarin.Forms 需要导入相应的特定于平台的 Xamarin.Forms 命名空间：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>在每个平台上添加平台特定的视图

以下部分演示如何 Xamarin.Forms 在每个平台上将平台特定的视图添加到布局。

### <a name="ios"></a>iOS

下面的代码示例演示如何将添加 `UILabel` 到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

该示例假设 `stackLayout` `contentView` 之前已在 XAML 或 c # 中创建了和实例。

### <a name="android"></a>Android

下面的代码示例演示如何将添加 `TextView` 到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

该示例假设 `stackLayout` `contentView` 之前已在 XAML 或 c # 中创建了和实例。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下面的代码示例演示如何将添加 `TextBlock` 到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

该示例假设 `stackLayout` `contentView` 之前已在 XAML 或 c # 中创建了和实例。

## <a name="overriding-platform-measurements-for-custom-views"></a>重写自定义视图的平台度量

每个平台上的自定义视图通常只为其设计的布局方案正确实现了度量值。 例如，自定义视图可能设计为仅占用一半的设备可用宽度。 但是，在与其他用户共享后，可能需要使用自定义视图来占用设备的完整可用宽度。 因此，在布局中重复使用时，可能需要重写自定义视图度量实现 Xamarin.Forms 。 出于此原因， `Add` 和 `ToView` 扩展方法提供了允许指定度量值委托的替代，这可以在将自定义视图布局添加到布局时覆盖该视图布局 Xamarin.Forms 。

以下各节演示如何重写自定义视图的布局，以纠正其度量 API 使用情况。

### <a name="ios"></a>iOS

下面的代码示例演示 `CustomControl` 类，该类继承自 `UILabel` ：

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

此视图的实例将添加到中 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下面的代码示例所示：

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

但是，因为 `CustomControl.SizeThatFits` 重写总是返回高度150，所以该视图将显示在其上方和下方的空白区域，如以下屏幕截图所示：

![具有不良 SizeThatFits 实现的 iOS CustomControl](code-images/ios-bad-measurement.png)

此问题的解决方案是提供一个 `GetDesiredSizeDelegate` 实现，如以下代码示例所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

此方法使用由方法提供的宽度 `CustomControl.SizeThatFits` ，但将高度150替换为70的高度。 将 `CustomControl` 实例添加到时 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ， `FixSize` 可以将方法指定为 `GetDesiredSizeDelegate` 以修复类提供的错误度量 `CustomControl` ：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

这会导致自定义视图正确显示，但不会在其上方和下方显示空空间，如以下屏幕截图所示：

![具有 GetDesiredSize 替代的 iOS CustomControl](code-images/ios-good-measurement.png)

### <a name="android"></a>Android

下面的代码示例演示 `CustomControl` 类，该类继承自 `TextView` ：

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

此视图的实例将添加到中 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下面的代码示例所示：

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

但是，因为 `CustomControl.OnMeasure` 重写总是返回所请求宽度的一半，所以该视图将仅占用一半的设备可用宽度，如以下屏幕截图所示：

![带有不良 OnMeasure 实现的 Android CustomControl](code-images/android-bad-measurement.png)

此问题的解决方案是提供一个 `GetDesiredSizeDelegate` 实现，如以下代码示例所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

此方法使用由方法提供的宽度 `CustomControl.OnMeasure` ，但将其乘以2。 将 `CustomControl` 实例添加到时 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ， `FixSize` 可以将方法指定为 `GetDesiredSizeDelegate` 以修复类提供的错误度量 `CustomControl` ：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

这会导致自定义视图正确显示，占用了设备的宽度，如以下屏幕截图所示：

![带有自定义 GetDesiredSize 委托的 Android CustomControl](code-images/android-good-measurement.png)

### <a name="universal-windows-platform"></a>通用 Windows 平台

下面的代码示例演示 `CustomControl` 类，该类继承自 `Panel` ：

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

此视图的实例将添加到中 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下面的代码示例所示：

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

但是，因为 `CustomControl.ArrangeOverride` 重写总是返回所请求宽度的一半，所以该视图将被剪裁到设备的一半可用宽度，如以下屏幕截图所示：

![具有不良 System.windows.frameworkelement.arrangeoverride 实现的 UWP CustomControl](code-images/winrt-bad-measurement.png)

此问题的解决方法是 `ArrangeOverrideDelegate` 在将视图添加到时提供一个实现， [`StackLayout`](xref:Xamarin.Forms.StackLayout) 如以下代码示例所示：

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

此方法使用由方法提供的宽度 `CustomControl.ArrangeOverride` ，但将其乘以2。 这会导致自定义视图正确显示，占用了设备的宽度，如以下屏幕截图所示：

![具有 System.windows.frameworkelement.arrangeoverride 委托的 UWP CustomControl](code-images/winrt-good-measurement.png)

## <a name="summary"></a>总结

本文介绍了如何向 Xamarin.Forms 使用 c # 创建的布局添加本机视图，以及如何重写自定义视图的布局来纠正其度量 API 使用。

## <a name="related-links"></a>相关链接

- [NativeEmbedding (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [本机窗体](~/xamarin-forms/platform/native-forms.md)