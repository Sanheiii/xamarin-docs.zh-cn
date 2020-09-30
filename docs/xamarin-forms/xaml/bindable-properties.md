---
title: Xamarin.Forms 可绑定属性
description: 本文介绍了可绑定的属性，并演示了如何创建和使用它们。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df2cf99ef0ea1fcbb1b52dda7abb6c8cfdd2d2e7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561529"
---
# <a name="no-locxamarinforms-bindable-properties"></a>Xamarin.Forms 可绑定属性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

可绑定属性通过使用类型来支持属性来扩展 CLR 属性功能 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，而不是使用字段来支持属性。 可绑定属性的用途是提供支持通过父子关系设置数据绑定、样式、模板和值的属性系统。 此外，可绑定属性可提供默认值、属性值验证和监视属性更改的回调。

属性应作为可绑定属性实现，以支持下列一项或多项功能：

- 用作数据绑定的有效 *目标* 属性。
- 通过 [样式](~/xamarin-forms/user-interface/styles/index.md)设置属性。
- 提供不同于属性类型默认值的默认属性值。
- 验证属性的值。
- 监视属性更改。

可绑定的属性的示例 Xamarin.Forms 包括 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 、 [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) 和 [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 。 每个可绑定属性都具有一个对应的 `public static readonly` 类型字段 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，该字段在同一类上公开，这是可绑定属性的标识符。 例如，属性对应的可绑定属性标识符 `Label.Text` 是 [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty) 。

## <a name="create-a-bindable-property"></a>创建可绑定属性

创建可绑定属性的过程如下所示：

1. [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)使用其中一个方法重载创建实例 [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) 。
1. 定义实例的属性访问器 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。

所有 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 实例都必须在 UI 线程上创建。 这意味着，只有在 UI 线程上运行的代码才能获取或设置可绑定属性的值。 但是， `BindableProperty` 可通过使用方法封送到 UI 线程，从其他线程访问实例 [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) 。

### <a name="create-a-property"></a>创建属性

若要创建 `BindableProperty` 实例，包含类必须派生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 类。 但是，类 `BindableObject` 层次结构中的类很高，因此，用于用户界面功能的大多数类都支持可绑定属性。

可以通过声明类型的属性来创建可绑定的属性 `public static readonly` [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 可绑定的属性应设置为 [ `BindableProperty.Create` ] (x：的其中一个的返回值 Xamarin.Forms 。BindableProperty (System.string，System.string，，system.object，， Xamarin.Forms .. 类型。System.windows.data.bindingmode>、 Xamarin.Forms 。BindableProperty. ValidateValueDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate， Xamarin.Forms 。BindableProperty. CoerceValueDelegate， Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法重载。 声明应位于派生类的正文中 [`BindableObject`](xref:Xamarin.Forms.BindableObject) ，但在任何成员定义的外部。

创建时，必须至少指定标识符 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 以及以下参数：

- 的名称 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。
- 属性的类型。
- 所属对象的类型。
- 属性的默认值。 这可确保属性在未设置时始终返回特定的默认值，并且它可以与属性类型的默认值不同。 当 [ `ClearValue` ] (x：时，将还原默认值 Xamarin.Forms 。Msds-bindableobject. System.windows.dependencyobject.clearvalue (Xamarin.Forms 。BindableProperty 在可绑定属性上调用了 # A3 方法) 。

> [!IMPORTANT]
> 可绑定属性的命名约定是可绑定的属性标识符必须与方法中指定的属性名称匹配 `Create` ，并追加 "property"。 

下面的代码演示一个可绑定属性的示例，其中包含四个必需参数的标识符和值：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

这将创建一个 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) `EventNameProperty` 类型为的实例 `string` 。 属性由 `EventToCommandBehavior` 类拥有，其默认值为 `null` 。

（可选）创建 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 实例时，可以指定以下参数：

- 绑定模式。 此值用于指定属性值更改将传播到的方向。 在默认绑定模式下，更改*将从源传播到**目标*。
- 当设置属性值时将调用的验证委托。 有关详细信息，请参阅 [验证回调](#validation-callbacks)。
- 属性值更改后将调用的属性更改的委托。 有关详细信息，请参阅 [检测属性更改](#detect-property-changes)。
- 一个属性更改委托，该委托将在属性值更改时调用。 此委托与属性更改的委托具有相同的签名。
- 当属性值发生更改时将调用的强制值委托。 有关详细信息，请参阅 [强制值回调](#coerce-value-callbacks)。
- `Func`用于初始化默认属性值的。 有关详细信息，请参阅 [使用 Func 创建默认值](#create-a-default-value-with-a-func)。

### <a name="create-accessors"></a>创建访问器

属性访问器需要使用属性语法来访问可绑定属性。 `Get`取值函数应返回相应的可绑定属性中包含的值。 可以通过调用 [ `GetValue` ] (x：来实现此目的 Xamarin.Forms 。Msds-bindableobject (Xamarin.Forms 。BindableProperty) # A3 方法，传递要获取其值的可绑定属性标识符，然后将结果转换为所需的类型。 `Set`访问器应设置相应的可绑定属性的值。 可以通过调用 [ `SetValue` ] (x：来实现此目的 Xamarin.Forms 。Msds-bindableobject (Xamarin.Forms 。BindableProperty，System.object) # A3 方法，并传入要设置值的可绑定属性标识符，以及要设置的值。

下面的代码示例演示可绑定属性的访问器 `EventName` ：

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>使用可绑定属性

创建可绑定属性后，可以从 XAML 或代码中使用它。 在 XAML 中，这是通过使用前缀来声明命名空间来实现的，命名空间声明指示 CLR 命名空间名称，也可以是程序集名称。 有关详细信息，请参阅 [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示一个自定义类型的 XAML 命名空间，该自定义类型包含一个可绑定属性，该属性在与引用自定义类型的应用程序代码相同的程序集中定义：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

设置可绑定属性时，将使用命名空间声明 `EventName` ，如下面的 XAML 代码示例所示：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>高级方案

创建实例时 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，有许多可选参数可设置为启用高级可绑定属性方案。 本部分将探讨这些方案。

### <a name="detect-property-changes"></a>检测属性更改

`static`通过为 `propertyChanged` [ `BindableProperty.Create` ] (x：指定参数，可以将属性更改的回调方法注册到可绑定属性 Xamarin.Forms 。BindableProperty (System.string，System.string，，system.object，， Xamarin.Forms .. 类型。System.windows.data.bindingmode>、 Xamarin.Forms 。BindableProperty. ValidateValueDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate， Xamarin.Forms 。BindableProperty. CoerceValueDelegate， Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法。 当可绑定属性的值发生更改时，将调用指定的回调方法。

下面的代码示例演示了可 `EventName` 绑定的属性如何将 `OnEventNameChanged` 方法注册为属性更改的回调方法：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

在属性更改的回调方法中， [`BindableObject`](xref:Xamarin.Forms.BindableObject) 参数用于表示拥有的类的哪个实例报告了更改，这两个参数的值 `object` 表示可绑定属性的新旧值。

### <a name="validation-callbacks"></a>验证回调

`static`通过为 `validateValue` [ `BindableProperty.Create` ] (x：指定参数，可以将验证回调方法注册到可绑定的属性 Xamarin.Forms 。BindableProperty (System.string，System.string，，system.object，， Xamarin.Forms .. 类型。System.windows.data.bindingmode>、 Xamarin.Forms 。BindableProperty. ValidateValueDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate， Xamarin.Forms 。BindableProperty. CoerceValueDelegate， Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法。 如果设置了可绑定属性的值，则将调用指定的回调方法。

下面的代码示例演示了可 `Angle` 绑定的属性如何将 `IsValidValue` 方法注册为验证回叫方法：

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

验证回调通过一个值提供， `true` 如果值对属性有效，则应返回，否则返回 `false` 。 如果验证回调返回 `false` （应由开发人员处理），则将引发异常。 当设置可绑定属性时，验证回调方法的典型使用会约束整数值或双精度值。 例如， `IsValidValue` 方法检查属性值是否为 `double` 介于0到360的范围内。

### <a name="coerce-value-callbacks"></a>强制值回调

`static`通过为 `coerceValue` [ `BindableProperty.Create` ] (x：指定参数，可以将强制值回调方法注册到可绑定的属性 Xamarin.Forms 。BindableProperty (System.string，System.string，，system.object，， Xamarin.Forms .. 类型。System.windows.data.bindingmode>、 Xamarin.Forms 。BindableProperty. ValidateValueDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate， Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate， Xamarin.Forms 。BindableProperty. CoerceValueDelegate， Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法。 当可绑定属性的值发生更改时，将调用指定的回调方法。

> [!IMPORTANT]
> 该 `BindableObject` 类型具有一个 `CoerceValue` 方法，通过调用该方法的 `BindableProperty` 强制值回调，可以调用该方法来强制重新计算其参数的值。

强制值回调用于在属性的值更改时强制重新计算可绑定的属性。 例如，强制值回调可用于确保一个可绑定属性的值不大于另一个可绑定属性的值。

下面的代码示例演示了可 `Angle` 绑定的属性如何将 `CoerceAngle` 方法注册为强制值回叫方法：

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

`CoerceAngle`方法检查属性的值 `MaximumAngle` ，如果 `Angle` 属性值大于该值，则将值强制转换为 `MaximumAngle` 属性值。 此外，在 `MaximumAngle` 属性更改时， `Angle` 通过调用方法对属性调用强制值回调 `CoerceValue` 。

### <a name="create-a-default-value-with-a-func"></a>使用 Func 创建默认值

`Func`可用于初始化可绑定属性的默认值，如下面的代码示例所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator`参数设置为 `Func` 调用 [ `Device.GetNamedSize` ] (x：的 Xamarin.Forms 。GetNamedSize (Xamarin.Forms 。NamedSize，system.string) # A3 方法返回一个 `double` ，该对象表示在本机平台上用于的字体的命名大小 [`Label`](xref:Xamarin.Forms.Label) 。

## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [事件到命令行为 (示例) ](/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [验证回调 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [强制值回调 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [Msds-bindableobject API](xref:Xamarin.Forms.BindableObject)