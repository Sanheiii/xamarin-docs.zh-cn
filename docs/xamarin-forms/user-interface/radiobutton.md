---
title: Xamarin.Forms RadioButton
description: Xamarin.Forms单选按钮是一种允许用户从集中选择一个选项的按钮。 每个选项都由一个单选按钮表示，您只能在组中选择一个单选按钮。
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 964bac947f46e5279cbdcc6bdb61d74deba7f622
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559384"
---
# <a name="no-locxamarinforms-radiobutton"></a>Xamarin.Forms RadioButton

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin.Forms `RadioButton` 是一种允许用户从集中选择一个选项的按钮。 每个选项都由一个单选按钮表示，您只能在组中选择一个单选按钮。 `RadioButton`类继承自 [`Button`](xref:Xamarin.Forms.Button) 类。

以下屏幕截图显示在 `RadioButton` iOS 和 Android 上处于清除状态和选中状态的对象：

![IOS 和 Android 上处于选中状态和已清除状态的单选按钮的屏幕截图](radiobutton-images/radiobutton-states.png "IOS 和 Android 上的单选按钮")

> [!IMPORTANT]
> `RadioButton` 当前为试验性，只能通过设置标志来使用 `RadioButton_Experimental` 。 有关详细信息，请参阅[实验性标志](~/xamarin-forms/internals/experimental-flags.md)。

`RadioButton`控件定义以下属性：

- `IsChecked`，类型为 `bool` ，用于定义是否 `RadioButton` 选择。 此属性使用 `TwoWay` 绑定，其默认值为 `false` 。
- `GroupName`，类型为 `string` ，用于定义指定哪些 `RadioButton` 控件互相排斥的名称。 此属性的默认值为 `null` 。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`RadioButton`控件定义 `CheckedChanged` `IsChecked` 通过用户或编程操作更改属性时触发的事件。 `CheckedChangedEventArgs`事件附带的对象 `CheckedChanged` 具有名为的单个属性 `Value` ，类型为 `bool` 。 触发事件时，属性的值 `Value` 设置为属性的新值 `IsChecked` 。

此外， `RadioButton` 类从类继承以下通常使用的属性 [`Button`](xref:Xamarin.Forms.Button) ：

- [`Command`](xref:Xamarin.Forms.Button.Command)，类型为 `ICommand` ，在选择时执行 `RadioButton` 。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)，类型为 `object` ，它是传递到的参数 `Command` 。
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)，类型为 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，用于确定文本样式。
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)，类型为 `string` ，用于定义字体系列。
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)，类型为 `double` ，用于定义字体大小。
- [`Text`](xref:Xamarin.Forms.Button.Text)，类型为 `string` ，用于定义要显示的文本。
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)，类型为 [`Color`](xref:Xamarin.Forms.Color) ，用于定义显示文本的颜色。

有关控件的详细信息 [`Button`](xref:Xamarin.Forms.Button) ，请参阅[ Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)。

## <a name="create-radiobuttons"></a>创建单选按钮

下面的示例演示如何 `RadioButton` 在 XAML 中实例化对象：

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

在此示例中， `RadioButton` 对象在相同的父容器内隐式分组。 此 XAML 将显示以下屏幕截图所示的外观：

![IOS 和 Android 上隐式分组的单选按钮的屏幕截图](radiobutton-images/radiobuttons.png "IOS 和 Android 上的隐式分组单选按钮")

或者， `RadioButton` 可以在代码中创建对象：

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>组单选按钮

单选按钮适用于组中，有两种分组单选按钮的方法：

- 将它们放在同一父容器中。 这称为隐式分组。
- 将 `GroupName` 每个单选按钮上的属性设置为相同的值。 这称为显式分组。

下面的 XAML 示例演示如何 `RadioButton` 通过设置对象的属性来对其进行显式分组 `GroupName` ：

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

在此示例中，每个 `RadioButton` 都相互排斥，因为它共享相同的 `GroupName` 值。 此 XAML 将显示以下屏幕截图所示的外观：

![IOS 和 Android 上的显式分组单选按钮的屏幕截图](radiobutton-images/grouped-radiobuttons.png "IOS 和 Android 上的显式分组单选按钮")

## <a name="respond-to-a-radiobutton-state-change"></a>响应单选按钮状态更改

单选按钮有两个状态：已选择或已清除。 选中单选按钮后，其 `IsChecked` 属性为 `true` 。 清除单选按钮时，其 `IsChecked` 属性为 `false`。 某个单选按钮可以通过单击同一组中的另外一个单选按钮进行清除，但再次单击该按钮时将无法将其清除。 但是，你可以通过将单选按钮的 `IsChecked` 属性设置为 `false`，以编程方式清除它。

当 `IsChecked` 属性通过用户或编程操作更改时，将激发该 `CheckedChanged` 事件。 可注册此事件的事件处理程序以响应更改：

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

代码隐藏包含事件的处理程序 `CheckedChanged` ：

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

`sender`自变量是 `RadioButton` 此事件的责任。 您可以使用它来访问 `RadioButton` 对象，或区分 `RadioButton` 共享同一事件处理程序的多个对象 `CheckedChanged` 。

或者， `CheckedChanged` 可以在代码中注册事件的事件处理程序：

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> 响应状态更改的另一种方法 `RadioButton` 是定义 `ICommand` ，并将其分配给 `RadioButton.Command` 属性。 有关详细信息，请参阅 [Button：使用命令界面](~/xamarin-forms/user-interface/button.md#using-the-command-interface)。

## <a name="radiobutton-visual-states"></a>单选按钮视觉状态

`RadioButton` 具有一个 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) ，它可用于在选择时启动视觉对象更改 `RadioButton` 。

下面的 XAML 示例演示如何为状态定义可视状态 `IsChecked` ：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

在此示例中，隐式 [`Style`](xref:Xamarin.Forms.Style) 以 `RadioButton` 对象为目标。 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定在 `RadioButton` 选择时，其 `TextColor` 属性将设置为绿色， `Opacity` 值为1。 `Normal` `VisualState` 指定当 `RadioButton` 处于已清除状态时，其 `TextColor` 属性将设置为红色， `Opacity` 值为0.5。 因此，整体效果是，在清除时 `RadioButton` ，它是红色且部分透明的，并且在选中时，不使用透明度：

![在 iOS 和 Android 上按视觉状态设置的单选按钮外观的屏幕截图](radiobutton-images/ischecked-visualstate.png "IOS 和 Android 上的单选按钮视觉状态")

若要详细了解可视状态，请参阅 [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-a-radiobutton"></a>禁用单选按钮

有时，应用程序会进入处于选中状态的状态，该状态 `RadioButton` 是无效操作。 在这种情况下， `RadioButton` 可以通过将其 `IsEnabled` 属性设置为来禁用 `false` 。

## <a name="related-links"></a>相关链接

- [单选按钮演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)
- [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)