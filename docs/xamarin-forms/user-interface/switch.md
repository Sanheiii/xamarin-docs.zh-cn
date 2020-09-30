---
title: Xamarin.Forms 转
description: Xamarin.Forms开关是一种按钮类型，用户可以对其进行操作以在开启和关闭状态之间切换。 本文介绍如何使用 Switch 类显示切换 UI 元素。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94f77fd70fee595efd341ff7372828b12661442d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561724"
---
# <a name="no-locxamarinforms-switch"></a>Xamarin.Forms 转

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) 控件是一个水平切换按钮，用户可以对其进行操作以在打开和关闭状态之间切换，这些状态由一个 `boolean` 值表示。 `Switch`类继承自 [`View`](xref:Xamarin.Forms.View) 。

以下屏幕截图显示了在 `Switch` iOS 和 Android 上，控件在其 **打开** 和 **关闭** 切换状态：

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-default.png "IOS 和 Android 上的交换机")

`Switch`控件定义以下属性：

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 指示是否 `boolean` `Switch` **打开**的值。
- [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) 是一个 `Color` ，它会影响 `Switch` 在切换或 **开启**状态下呈现的方式。
- `ThumbColor` 是 `Color` 交换机拇指的。

对象支持这些属性 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着可以对其进行 `Switch` 样式化并成为数据绑定的目标。

`Switch`控件定义一个事件，该 `Toggled` 事件在 `IsToggled` 属性更改时通过用户操作或应用程序设置属性时引发 `IsToggled` 。 `ToggledEventArgs`事件附带的对象 `Toggled` 具有名为的单个属性 `Value` ，类型为 `bool` 。 触发事件时，属性的值将 `Value` 反映属性的新值 `IsToggled` 。

## <a name="create-a-switch"></a>创建开关

`Switch`可以在 XAML 中实例化。 `IsToggled`可以设置其属性以切换 `Switch` 。 默认情况下， `IsToggled` 属性为 `false` 。 下面的示例演示如何 `Switch` 使用可选的属性集在 XAML 中实例化 `IsToggled` ：

```xaml
<Switch IsToggled="true"/>
```

`Switch`也可以在代码中创建：

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>切换外观

除了 [`Switch`](xref:Xamarin.Forms.Switch) 继承自类的属性以外 [`View`](xref:Xamarin.Forms.View) ，还可以 `Switch` 定义 `OnColor` 和 `ThumbColor` 属性。 `OnColor`属性可以设置为在 `Switch` 其处于**开启**状态时定义颜色，并且 `ThumbColor` 属性可以设置为定义 `Color` 交换块的。 下面的示例演示如何 `Switch` 在 XAML 中使用以下属性集来实例化：

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

在代码中创建时，还可以设置属性 `Switch` ：

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

下面的屏幕截图显示了 `Switch` 和**on**属性集的打开和**关闭**切换状态 `OnColor` `ThumbColor` ：

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-colors.png "IOS 和 Android 上的交换机")

## <a name="respond-to-a-switch-state-change"></a>响应交换机状态更改

当 `IsToggled` 属性更改时，无论是通过用户操作，还是在应用程序设置属性时，都将 `IsToggled` `Toggled` 激发该事件。 可注册此事件的事件处理程序以响应更改：

```xaml
<Switch Toggled="OnToggled" />
```

代码隐藏文件包含事件的处理程序 `Toggled` ：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

`sender`事件处理程序中的参数 `Switch` 负责激发此事件。 您可以使用 `sender` 属性访问该 `Switch` 对象，或区分 `Switch` 共享同一事件处理程序的多个对象 `Toggled` 。

`Toggled`还可以在代码中指定事件处理程序：

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>数据绑定开关

`Toggled`通过使用数据绑定和触发器来响应更改的切换状态，可以消除事件处理程序 `Switch` 。

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label) 使用中的绑定表达式 `DataTrigger` 来监视 `IsToggled` 指定的的属性 `Switch` `styleSwitch` 。 当此属性变为时 `true` ， `FontAttributes` 将 `FontSize` 更改的和属性 `Label` 。 当 `IsToggled` 属性返回到时 `false` ， `FontAttributes` 的和 `FontSize` 属性将 `Label` 重置为它们的初始状态。

有关触发器的信息，请参阅[ Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="switch-visual-states"></a>切换可视状态

[`Switch`](xref:Xamarin.Forms.Switch) 具有 `On` 和 `Off` 可视状态，可用于在属性更改时启动视觉对象更改 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。

下面的 XAML 示例演示如何定义和状态的可视状态 `On` `Off` ：

```xaml
<Switch IsToggled="True">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="On">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="MediumSpringGreen" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Off">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Switch>
```

在此示例中， `On` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定当 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 属性为时 `true` ， `ThumbColor` 属性将设置为 "中等弹簧绿色"。 `Off` `VisualState` 指定当 `IsToggled` 属性为时 `false` ， `ThumbColor` 属性将设置为红色。 因此，整体效果是，当处于 `Switch` 远离其拇指的位置时，如果处于 "开" 位置，则其拇指为 "中" 的 "中" `Switch` 。

![IOS 和 Android](switch-images/on-visualstate.png "VisualState 上的开关") 
 上的 VisualState 上的开关屏幕截图![在 iOS 和 Android 上关闭 VisualState 的屏幕截图](switch-images/off-visualstate.png "关闭 VisualState")

若要详细了解可视状态，请参阅 [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-a-switch"></a>禁用交换机

应用程序可能进入正在切换的状态， `Switch` 这是无效操作。 在这种情况下， `Switch` 可以通过将其 `IsEnabled` 属性设置为来禁用 `false` 。 这会阻止用户操作 `Switch` 。

## <a name="related-links"></a>相关链接

- [切换演示](/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
- [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)