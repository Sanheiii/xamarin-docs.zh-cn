---
title: 创建 Xamarin.Forms 行为
description: Xamarin.Forms 行为是通过从行为或行为 <T> 类派生来创建的。 本文演示如何创建和使用 Xamarin.Forms 行为。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f265d1da894b195402c91cbf9468a11837c53bcf
ms.sourcegitcommit: f6a2f07d2e689e0cfd01b30008d50c83c63fa70c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "86933702"
---
# <a name="create-no-locxamarinforms-behaviors"></a>创建 Xamarin.Forms 行为

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Xamarin.Forms 行为是通过从 Behavior 或 Behavior&lt;T&gt; 类派生来创建的。本文演示如何创建和使用 Xamarin.Forms 行为。_

## <a name="overview"></a>概述

创建 Xamarin.Forms 行为的过程如下所示：

1. 创建一个继承自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类的类，其中 `T` 是施加该行为的控件类型。
1. 重写 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法以执行任何所需的设置。
1. 重写 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法以执行任何所需的清理。
1. 实现行为的核心功能。

这将导致以下代码示例所示的结构：

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法在行为附加到控件后立即触发。 此方法接收对其附加的控件的引用，并可用于注册事件处理程序或执行支持行为功能所需的其他设置。 例如，你可以订阅控件上的事件。 然后，行为功能将在事件的事件处理程序中实现。

[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法在从控件中删除行为时触发。 此方法接收对其附加的控件的引用，并用于执行任何所需的清理。 例如，可以取消订阅控件上的事件，以防止内存泄漏。

然后，可以通过将其附加到相应控件的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合来使用该行为。

## <a name="creating-a-no-locxamarinforms-behavior"></a>创建 Xamarin.Forms 行为

示例应用程序演示一个 `NumericValidationBehavior`，并用红色突出显示用户输入到 [`Entry`](xref:Xamarin.Forms.Entry) 控件的值（如果该值不是 `double`）。 此行为如下面的代码示例所示：

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` 派生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类，其中 `T` 是 [`Entry`](xref:Xamarin.Forms.Entry)。 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) event, with the [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法取消注册 `TextChanged` 事件以防止内存泄漏。 该行为的核心功能由 `OnEntryTextChanged` 方法提供，该方法将用户输入的值解析为 `Entry`，如果该值不是 `double`，则将 [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 属性设置为红色。

> [!NOTE]
> Xamarin.Forms 不会设置行为的 `BindingContext`，因为可以通过样式共享行为并将其应用于多个控件。

## <a name="consuming-a-no-locxamarinforms-behavior"></a>使用 Xamarin.Forms 行为

每个 Xamarin.Forms 控件都有一个 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，其中可以添加一个或多个行为，如以下 XAML 代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

下面的代码示例介绍了 C# 中的等效 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在运行时，根据行为实现，行为将响应与控件的交互。 以下屏幕截图演示了响应无效输入的行为：

[![带有 Xamarin.Forms 行为的示例应用程序](creating-images/screenshots-sml.png)](creating-images/screenshots.png#lightbox "带有 Xamarin.Forms行为的示例应用程序")

> [!NOTE]
> 行为是为特定的控件类型（或者可以应用于许多控件的超类）编写的，它们只应添加到兼容的控件中。 试图将行为附加到不兼容控件将引发异常。

### <a name="consuming-a-no-locxamarinforms-behavior-with-a-style"></a>使用具有样式的 Xamarin.Forms 行为

行为也可以通过显式或隐式样式使用。 但是，不能创建设置控件的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 属性的样式，因为该属性只读。 解决方案是向行为类添加附加属性，以控制添加和删除行为。 流程如下：

1. 将附加属性添加到行为类中，以控制将附加行为的控件添加行为和删除行为。 确保附加的属性注册 `propertyChanged` 委托，该委托将在属性值更改时执行。
1. 为附加属性创建 `static` getter 和 setter。
1. 在 `propertyChanged` 委托中实现逻辑以添加和删除行为。

下面的代码示例显示了一个附加属性，用于控制添加和删除 `NumericValidationBehavior`：

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

`NumericValidationBehavior` 类包含带有 `static` getter 和 setter 且名为 `AttachBehavior` 的附加属性，该属性控制将附加行为的控件添加和删除行为。 该附加属性注册属性值更改时执行的 `OnAttachBehaviorChanged` 方法。 该方法根据 `AttachBehavior` 附加属性的值向控件添加或移除行为。

下面的代码示例显示使用 `AttachBehavior` 附加属性的 `NumericValidationBehavior` 的显式样式，该样式可应用于 [`Entry`](xref:Xamarin.Forms.Entry) 控件：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) 通过使用 `StaticResource` 标记扩展将其 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 属性设置为 `Style` 实例使其可应用于 [`Entry`](xref:Xamarin.Forms.Entry) 控件，如以下代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 虽然可以向 XAML 中设置或查询的行为添加可绑定属性，但如果你确实创建了有状态的行为，则这些行为不应该在 `ResourceDictionary` 的`Style` 中的控件之间共享。

### <a name="removing-a-behavior-from-a-control"></a>从控件中删除行为

[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a `Remove` or `Clear` method. The following code example demonstrates removing a specific behavior from a control's `Behaviors` 集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，可以清除控件的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如下面的代码示例所示：

```csharp
entry.Behaviors.Clear();
```

此外，请注意，当从导航堆栈中弹出页面时，行为不会从控件中隐式删除。 相反，必须在页面超出范围之前显式删除它们。

## <a name="summary"></a>总结

本文演示如何创建和使用 Xamarin.Forms 行为。 Xamarin.Forms 行为由 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类派生创建而成。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 行为（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [通过样式应用的 Xamarin.Forms 行为（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为\<T>](xref:Xamarin.Forms.Behavior`1)
