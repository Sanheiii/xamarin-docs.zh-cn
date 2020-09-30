---
title: 附加属性
description: 本文提供附加属性的简介，并演示如何创建和使用它们。
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61edbb347b4d3466d1ca756208adb5d173d63b34
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561542"
---
# <a name="attached-properties"></a>附加属性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


附加属性使对象可以为其自己的类未定义的属性分配值。 例如，子元素可以使用附加属性来通知其父元素在用户界面中的显示方式。 [`Grid`](xref:Xamarin.Forms.Grid)控件允许通过设置 `Grid.Row` 和附加属性来指定子项的行和列 `Grid.Column` 。 `Grid.Row` 和 `Grid.Column` 是附加属性，因为它们是针对的元素设置的，而不是在自身的子元素上设置的 `Grid` `Grid` 。

在以下情况下，应将可绑定属性作为附加属性实现：

- 需要为定义类之外的类提供属性设置机制时。
- 当类表示需要轻松地与其他类集成的服务时。

有关可绑定属性的详细信息，请参阅可 [绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="create-an-attached-property"></a>创建附加属性

创建附加属性的过程如下所示：

1. [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)使用其中一个方法重载创建实例 [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) 。
1. 提供 `static` `Get` *propertyname*和 `Set` *propertyname*方法作为附加属性的访问器。

### <a name="create-a-property"></a>创建属性

当创建附加属性以用于其他类型时，创建属性的类不必派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。 但是，访问器的 *target* 属性应为，或派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

可以通过声明类型的属性来创建附加属性 `public static readonly` [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 可绑定的属性应设置为 [ `BindableProperty.CreateAttached` ] (x：的其中一个的返回值 Xamarin.Forms 。BindableProperty，CreateAttached (，system.object，system.object，，，， Xamarin.Forms 。System.windows.data.bindingmode>、 Xamarin.Forms 。BindableProperty. ValidateValueDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate， Xamarin.Forms 。BindableProperty. CoerceValueDelegate， Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法重载。 声明应在所属类的主体中，但在任何成员定义的外部。

> [!IMPORTANT]
> 附加属性的命名约定是：附加属性标识符必须与方法中指定的属性名称匹配 `CreateAttached` ，并追加 "property"。

下面的代码演示了附加属性的示例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

这将创建一个名为 `HasShadowProperty` 的附加属性，类型为 `bool` 。 属性由 `ShadowEffect` 类拥有，其默认值为 `false` 。

有关创建可绑定属性的详细信息，包括在创建过程中可以指定的参数，请参阅 [创建可绑定属性](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property)。

### <a name="create-accessors"></a>创建访问器

静态 `Get` *PropertyName*和 `Set` *PropertyName*方法是附加属性的访问器所必需的，否则，属性系统将无法使用附加属性。 `Get` *PropertyName*访问器应符合以下签名：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*访问器应返回附加属性的相应字段中包含的值 `BindableProperty` 。 可以通过调用 [ `GetValue` ] (x：来实现此目的 Xamarin.Forms 。Msds-bindableobject (Xamarin.Forms 。BindableProperty) # A3 方法，传递要获取其值的可绑定属性标识符，然后将生成的值强制转换为所需的类型。

`Set` *PropertyName*访问器应符合以下签名：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*访问器应 `BindableProperty` 为附加属性设置相应字段的值。 可以通过调用 [ `SetValue` ] (x：来实现此目的 Xamarin.Forms 。Msds-bindableobject (Xamarin.Forms 。BindableProperty，System.object) # A3 方法，并传入要设置值的可绑定属性标识符，以及要设置的值。

对于这两个访问器， *目标* 对象应为，或派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

下面的代码示例演示附加属性的访问器 `HasShadow` ：

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consume-an-attached-property"></a>使用附加属性

创建附加属性后，可以从 XAML 或代码中使用它。 在 XAML 中，这是通过以下方式实现的：使用前缀声明命名空间，命名空间声明指示公共语言运行时 (CLR) 命名空间名称，并选择性地包含程序集名称。 有关详细信息，请参阅 [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示包含附加属性的自定义类型的 XAML 命名空间，该属性在与引用自定义类型的应用程序代码相同的程序集中进行定义：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

然后，在对特定控件设置附加属性时使用命名空间声明，如下面的 XAML 代码示例所示：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

以下代码示例显示相应的 C# 代码：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>使用带有样式的附加属性

附加属性还可以通过样式添加到控件。 下面的 XAML 代码示例演示了一个使用附加属性的 *显式* 样式 `HasShadow` ，该样式可应用于 [`Label`](xref:Xamarin.Forms.Label) 控件：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) 可通过使用 `StaticResource` 标记扩展将 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 属性设置为 `Style` 实例来应用于 [`Label`](xref:Xamarin.Forms.Label)，如以下代码示例所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>高级方案

创建附加属性时，有许多可选参数可设置为启用高级附加属性方案。 这包括检测属性更改、验证属性值并强制属性值。 有关详细信息，请参阅 [高级方案](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios)。

## <a name="related-links"></a>相关链接

- [可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [阴影效果（示例）](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [Msds-bindableobject API](xref:Xamarin.Forms.BindableObject)