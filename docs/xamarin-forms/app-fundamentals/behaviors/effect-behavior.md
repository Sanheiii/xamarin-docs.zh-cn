---
title: 可重用 EffectBehavior
description: 行为是一种非常有用的方法，用于向控件添加效果、从代码隐藏文件中删除冗余重复的效果处理代码。 本文演示如何创建 Xamarin.Forms 行为并将其用于向控件添加效果。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a085226da3c63f4f151bc657976bba384c02170
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561139"
---
# <a name="reusable-effectbehavior"></a>可重用 EffectBehavior

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

行为是一种非常有用的方法，用于向控件添加效果、从代码隐藏文件中删除冗余重复的效果处理代码。本文演示如何创建 Xamarin.Forms 行为并将其用于向控件添加效果。

## <a name="overview"></a>概述

`EffectBehavior` 类是可重用的 Xamarin.Forms 自定义行为，当行为附加到控件时，它会将 [`Effect`](xref:Xamarin.Forms.Effect) 实例添加到控件，当行为与控件分离时，它将删除 `Effect` 实例。

必须设置以下行为属性才能使用该行为：

- **组** - 效果类的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 属性的值。
- **名称** - 效果类的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 属性的值。

有关效果的更多信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

> [!NOTE]
> `EffectBehavior` 是一个自定义类，它可以位于[效果行为示例](/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)中，但并不属于 Xamarin.Forms。

## <a name="creating-the-behavior"></a>创建行为

`EffectBehavior` 类派生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类，其中 `T` 是 [`View`](xref:Xamarin.Forms.View)。 这意味着，`EffectBehavior` 类可以附加到任何 Xamarin.Forms 控件。

### <a name="implementing-bindable-properties"></a>实现可绑定属性

`EffectBehavior` 类定义了两个 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 实例，当行为附加到控件时，这些实例用于向控件添加 [`Effect`](xref:Xamarin.Forms.Effect)。 以下代码示例中显示了这些属性：

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

当使用 `EffectBehavior` 时，应将 `Group` 属性设置为效果的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 特性的值。 此外，应将 `Name` 属性设置为效果类的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 特性的值。

### <a name="implementing-the-overrides"></a>实现替代

`EffectBehavior` 类将重写 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，如下面的代码示例所示：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the `AddEffect` method, passing in the attached control as a parameter. The [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法通过调用 `RemoveEffect` 方法执行清理，并将附加的控件作为参数传入。

### <a name="implementing-the-behavior-functionality"></a>实现行为功能

此行为的目的是，当行为附加到控件时，将 `Group` 和 `Name` 属性中定义的 [`Effect`](xref:Xamarin.Forms.Effect) 添加到控件，并在行为与控件分离时删除 `Effect`。 核心行为功能如以下代码示例所示：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

执行 `AddEffect` 方法以响应要附加到控件的 `EffectBehavior`，并且它将以参数形式接收所附加的控件。 然后，该方法将检索到的效果添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 执行 `RemoveEffect` 方法以相应要与控件拆离的 `EffectBehavior`，并且它将以参数形式接收所附加的控件。 然后该方法从控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中移除效果。

`GetEffect` 方法使用 [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 方法检索 [`Effect`](xref:Xamarin.Forms.Effect)。 通过串联 `Group` 和 `Name` 属性值来定位该效果。 如果平台不提供此效果，则 `Effect.Resolve` 方法将返回一个非 `null` 值。

## <a name="consuming-the-behavior"></a>使用行为

`EffectBehavior` 类可以附加到控件的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如以下 XAML 代码示例所示：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

以下代码示例显示相应的 C# 代码：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

行为的 `Group` 和 `Name` 属性被设置为每个平台特定项目的效果类的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 和 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 特性的值。

在运行时，当行为附加到 [`Label`](xref:Xamarin.Forms.Label) 控件时，`Xamarin.LabelShadowEffect` 将添加到控件的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中。 这就使阴影被添加到 `Label` 控件显示的文本中，如以下屏幕截图所示：

![具有 EffectsBehavior 的示例应用程序](effect-behavior-images/screenshots.png)

使用这种行为从控件中添加和删除效果的优点是，可以从代码隐藏文件中删除冗余重复的效果处理代码。

## <a name="summary"></a>总结

本文演示了如何使用行为向控件添加效果。 `EffectBehavior` 类是可重用的 Xamarin.Forms 自定义行为，当行为附加到控件时，它会将 [`Effect`](xref:Xamarin.Forms.Effect) 实例添加到控件，当行为与控件分离时，它将删除 `Effect` 实例。

## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [影响行为（示例）](/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为&lt;&gt;](xref:Xamarin.Forms.Behavior`1)