---
title: Xamarin.Forms 视觉状态管理器
description: 使用可视状态管理器根据代码中的可视状态集对 XAML 元素进行更改。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7e59cddbe9192f29ca1636c567131aad60157066
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556576"
---
# <a name="no-locxamarinforms-visual-state-manager"></a>Xamarin.Forms 视觉状态管理器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_使用可视状态管理器根据代码中的可视状态集对 XAML 元素进行更改。_

视觉对象状态管理器 (VSM) 提供一种结构化的方法来从代码对用户界面进行可视更改。 在大多数情况下，应用程序的用户界面是在 XAML 中定义的，此 XAML 包含描述视觉状态管理器如何影响用户界面视觉对象的标记。

VSM 引入了 _可视状态_的概念。 Xamarin.Forms视图（例如） `Button` 可具有多个不同的视觉外观，具体取决于 &mdash; 它是被禁用、按下还是有输入焦点。 这些是按钮的状态。

可视状态在 _视觉状态组_中收集。 可视状态组中的所有可视状态都是互斥的。 可视状态和视觉状态组均由简单文本字符串标识。

Xamarin.Forms视觉状态管理器定义了一个名为 "CommonStates" 的视觉状态组，其中包含以下可视状态：

- "Normal"
- Disabled
- 侧重于
- 选择

派生自的所有类都支持此视觉对象状态组 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，它是和的基类 [`View`](xref:Xamarin.Forms.View) [`Page`](xref:Xamarin.Forms.Page) 。

你还可以定义自己的视觉状态组和视觉状态，如本文所示。

> [!NOTE]
> Xamarin.Forms 熟悉 [触发器](~/xamarin-forms/app-fundamentals/triggers.md) 的开发人员也知道，触发器还可以根据视图属性中的更改或事件激发来更改用户界面中的视觉对象。 然而，使用触发器处理这些更改的各种组合可能会令人感到困惑。 从历史角度来看，可视状态管理器是在基于 Windows XAML 的环境中引入的，用于缓解由于视觉状态组合而产生的混淆。 使用 VSM，可视状态组中的可视状态始终是互斥的。 在任何时候，每个组中只有一个状态是当前状态。

## <a name="common-states"></a>常见状态

视觉状态管理器允许您在 XAML 文件中包含标记，如果视图正常、处于禁用状态或具有输入焦点，则可以更改视图的可视外观。 这些 _状态称为公共状态_。

例如，假设您 `Entry` 在页面上有一个视图，并且您希望的视觉外观 `Entry` 按以下方式变化：

- `Entry`禁用时，应会有粉红色的 `Entry` 背景。
- `Entry`应正常使用酸橙色。
- `Entry`如果有输入焦点，则应将其正常高度扩展到两倍。

可以将 VSM 标记附加到单个视图，也可以在样式中定义它（如果它应用于多个视图）。 接下来的两部分介绍了这些方法。

### <a name="vsm-markup-on-a-view"></a>视图上的 VSM 标记

若要将 VSM 标记附加到 `Entry` 视图，请首先将分隔 `Entry` 为开始标记和结束标记：

```xaml
<Entry FontSize="18">

</Entry>
```

由于其中一个状态将使用 `FontSize` 属性来使中的文本大小加倍，因此提供了一个显式字体大小 `Entry` 。

接下来， `VisualStateManager.VisualStateGroups` 在这些标记之间插入标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) 是由类定义的附加可绑定属性 [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) 。  (有关附加的可绑定属性的详细信息，请参阅 [附加属性](~/xamarin-forms/xaml/attached-properties.md)一文。 ) 这是 `VisualStateGroups` 属性附加到 `Entry` 对象的方式。

`VisualStateGroups`属性的类型为 [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList) ，它是对象的集合 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 。 在 `VisualStateManager.VisualStateGroups` 标记中， `VisualStateGroup` 为想要包括的每个可视状态组插入一对标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

请注意， `VisualStateGroup` 标记具有一个 `x:Name` 指示组名称的特性。 `VisualStateGroup`类定义 `Name` 可改用的属性：

```xaml
<VisualStateGroup Name="CommonStates">
```

可以 `x:Name` `Name` 在同一元素中使用或，但不能同时使用两者。

`VisualStateGroup`类定义一个名为 [`States`](xref:Xamarin.Forms.VisualStateGroup.States) 的属性，该属性是一个 [`VisualState`](xref:Xamarin.Forms.VisualState) 对象的集合。 `States` 是的 _内容属性_ `VisualStateGroups` ，因此可以在 `VisualState` 标记之间直接包括标记 `VisualStateGroup` 。 本文 [重要的 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)中讨论了 (内容属性。 ) 

下一步是为该组中的每个可视状态包含一对标记。 还可以使用或进行标识 `x:Name` `Name` ：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` 定义名为 [`Setters`](xref:Xamarin.Forms.VisualState.Setters) 的属性，它是对象的集合 [`Setter`](xref:Xamarin.Forms.Setter) 。 它们是 `Setter` 在对象中使用的相同对象 [`Style`](xref:Xamarin.Forms.Style) 。

`Setters`_不_是的内容属性 `VisualState` ，因此需要包含属性的属性元素标记 `Setters` ：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

你现在可以在每对标记之间插入一个或多个 `Setter` 对象 `Setters` 。 `Setter`定义前面所述的视觉对象状态的对象如下：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

每个 `Setter` 标记指示特定属性在该状态为当前状态时的值。 对象引用的任何属性 `Setter` 都必须由可绑定的属性支持。

与此类似的标记是**[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例程序中 "**查看**" 页的基础。 此页包含三个 `Entry` 视图，但只有第二个视图附加了 VSM 标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

请注意，第二个 `Entry` 还在 `DataTrigger` 其集合中包含 `Trigger` 。 这会导致在 `Entry` 第三个中键入内容之前禁用 `Entry` 。 下面是在 iOS、Android 和通用 Windows 平台 (UWP) 上运行时的页面：

[![视图上的 VSM：已禁用](vsm-images/VsmOnViewDisabled.png "视图上的 VSM-已禁用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

当前视觉状态为 "已禁用"，因此第二个视觉对象的背景在 `Entry` iOS 和 Android 屏幕上为粉红色。 的 UWP 实现 `Entry` 不允许在禁用时设置背景色 `Entry` 。

当你在第三个文本中输入文本时 `Entry` ，第二个 `Entry` 开关将进入 "正常" 状态，并且背景现在为酸橙色：

[![视图上的 VSM：正常](vsm-images/VsmOnViewNormal.png "视图上的 VSM-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

当你触及第二个时 `Entry` ，它将获得输入焦点。 它将切换到 "重点" 状态，并扩展到其高度的两倍：

[![视图上的 VSM：聚焦](vsm-images/VsmOnViewFocused.png "基于视图的 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

请注意，在 `Entry` 获得输入焦点时不会保留酸橙色背景。 当可视状态管理器在可视状态之间切换时，先前状态设置的属性将设置为 "未设置"。 请记住，可视状态是互斥的。 "正常" 状态并不意味着 `Entry` 已启用。 这意味着 `Entry` 已启用且没有输入焦点。

如果希望在 `Entry` "重点" 状态下有一个酸橙色背景，请将另一个添加 `Setter` 到该视觉对象状态：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

为了使这些 `Setter` 对象正常工作， `VisualStateGroup` 必须包含 `VisualState` 该组中所有状态的对象。 如果视觉对象状态不具有任何 `Setter` 对象，请将其包含为空标记：

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>样式中的可视状态管理器标记

通常需要在两个或更多视图中共享同一可视状态管理器标记。 在这种情况下，您需要将标记放在一个 `Style` 定义中。

下面是 `Style` `Entry` "查看" " **查看** " 页中的元素的现有隐式：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

`Setter`为附加的可 `VisualStateManager.VisualStateGroups` 绑定属性添加标记：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

的内容属性 `Setter` 为 `Value` ，因此 `Value` 可以在这些标记中直接指定属性的值。 该属性的类型为 `VisualStateGroupList` ：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style>
```

在这些标记中，可以包含一个或多个 `VisualStateGroup` 对象：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

VSM 标记的剩余部分与之前相同。

下面是 " **样式** " 页中显示完整的 vsm 标记的 vsm：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

现在， `Entry` 此页上的所有视图都以相同的方式对其视觉对象状态做出响应。 另请注意，"重点" 状态现在包含另一个 `Setter` ，它还提供了一个 `Entry` 酸橙色背景，同时还提供了输入焦点：

[![VSM 样式](vsm-images/VsmInStyle.png "VSM 样式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-no-locxamarinforms"></a>视觉对象状态 Xamarin.Forms

下表列出了在中定义的视觉对象状态 Xamarin.Forms ：

| 类 | 状态 | 更多信息 |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [按钮视觉状态](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CheckBox` | `IsChecked` | [复选框可视状态](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [CarouselView 视觉状态](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [ImageButton 视觉状态](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `RadioButton` | `IsChecked` | [单选按钮视觉状态](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) |
| `Switch` | `On`, `Off` | [切换可视状态](~/xamarin-forms/user-interface/switch.md#switch-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [常见状态](#common-states) |

每个状态都可以通过名为的视觉状态组进行访问 `CommonStates` 。

此外，还 `CollectionView` 实现了 `Selected` 状态。 有关详细信息，请参阅 [更改选定项的颜色](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color)。

## <a name="set-state-on-multiple-elements"></a>设置多个元素的状态

在前面的示例中，可视状态附加到单个元素上并在其上操作。 但是，也可以创建附加到单个元素的可视状态，但这会为同一范围内的其他元素设置属性。 这样就无需在运行状态的每个元素上重复视觉状态。

[`Setter`](xref:Xamarin.Forms.Setter)类型具有一个 `TargetName` 类型为的属性 `string` ，该属性表示 `Setter` 可视状态将操作的目标元素。 `TargetName`定义属性时，将 `Setter` `Property` 在中定义的元素的设置 `TargetName` 为 `Value` ：

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

在此示例中， `Label` 命名的 `label` 将其 `TextColor` 属性设置为 `Red` 。 设置属性时 `TargetName` ，必须在中指定属性的完整路径 `Property` 。 因此，若要 `TextColor` 在上设置属性 `Label` ，请 `Property` 将指定为 `Label.TextColor` 。

> [!NOTE]
> 对象引用的任何属性 `Setter` 都必须由可绑定的属性支持。

**[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例中的 "**与 Setter TargetName 的 VSM** " 页面说明了如何在单个可视状态组中设置多个元素的状态。 XAML 文件包含一个 `StackLayout` 包含 `Label` 元素、 `Entry` 和的 `Button` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 标记已附加到 `StackLayout` 。 有两个互斥状态，分别名为 "常规" 和 "按下"，每种状态包含 `VisualState` 标记。

当未按下时，"正常" 状态处于活动状态 `Button` ，并且可以输入对问题的响应：

[![VSM Setter TargetName：正常状态](vsm-images/VsmSetterTargetNameNormal.png "VSM 资源库 targetname-正常")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

按下时，"按下" 状态变为活动状态 `Button` ：

[![VSM Setter TargetName：按下状态](vsm-images/VsmSetterTargetNamePressed.png "VSM 资源库 targetname-按下")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

"按下" `VisualState` 指定在 `Button` 按下时，其 `Scale` 属性将从默认值1更改为0.8。 此外，命名的 `Entry` `entry` 将其 `Text` 属性设置为巴黎。 因此，在 `Button` 按下时，重新缩放会略小，并 `Entry` 显示巴黎。 然后，当释放时， `Button` 它的默认值为1，并 `Entry` 显示以前输入的任何文本重新缩放。

> [!IMPORTANT]
> 属性路径当前在指定属性的元素中不受支持 `Setter` `TargetName` 。

## <a name="define-your-own-visual-states"></a>定义自己的视觉状态

派生自的每个类都 `VisualElement` 支持通用状态 "正常"、"重点" 和 "已禁用"。 此外，类还 `CollectionView` 支持 "选定" 状态。 在内部， [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) 类会在其启用或禁用、焦点或失去焦点的情况下检测，并调用 static [ `VisualStateManager.GoToState` ] (x： Xamarin.Forms 。VisualStateManager. GoToState (Xamarin.Forms 。VisualElement) # A3 方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

这是你将在类中找到的唯一视觉状态管理器代码 `VisualElement` 。 由于 `GoToState` 每个对象都是根据每个派生自的类调用的 `VisualElement` ，因此，您可以将可视状态管理器与任何对象结合使用 `VisualElement` 来响应这些更改。

有趣的是，可视状态组 "CommonStates" 的名称未在中显式引用 `VisualElement` 。 组名不是可视状态管理器的 API 的一部分。 到目前为止，您可以将组的名称从 "CommonStates" 更改为其他任何程序，程序仍将运行。 组名称只是该组中状态的一般说明。 它被隐式理解，任何组中的可视状态都是互斥的：一个状态，并且在任何时候都只有一个状态是最新的。

如果要实现自己的可视状态，需要 `VisualStateManager.GoToState` 从代码调用。 大多数情况下，你将从页面类的代码隐藏文件进行此调用。

**[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例中的 " **VSM 验证**" 页显示了如何在连接到输入验证时使用视觉状态管理器。 XAML 文件包含一个， `StackLayout` 其中包含两个 `Label` 元素 `Entry` ：和 `Button` 。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

VSM 标记附加到 `StackLayout` 名为) 的 `stackLayout` (。 有两个互斥状态，分别名为 "有效" 和 "无效"，其中每种状态都包含 `VisualState` 标记。

如果不 `Entry` 包含有效的电话号码，则当前状态为 "无效"，因此 `Entry` 具有粉红色背景，第二个 `Label` 为可见， `Button` 禁用：

[![VSM 验证：无效状态](vsm-images/VsmValidationInvalid.png "VSM 验证-无效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

输入有效的电话号码时，当前状态将变为 "有效"。 `Entry`获取一个酸橙色背景，第二个 `Label` 消失， `Button` 现已启用：

[![VSM 验证：有效状态](vsm-images/VsmValidationValid.png "VSM 验证-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

代码隐藏文件负责处理 `TextChanged` 中的事件 `Entry` 。 处理程序使用正则表达式来确定输入字符串是否有效。 代码隐藏文件中名为的方法 `GoToState` 调用的静态 `VisualStateManager.GoToState` 方法 `stackLayout` ：

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

另请注意， `GoToState` 从构造函数调用方法来初始化状态。 应始终具有当前状态。 但在代码中不存在任何引用视觉状态组的名称，但在 XAML 中将其作为 "ValidationStates" 引用是为了清楚起见。

请注意，代码隐藏文件只需考虑定义可视状态的页面上的对象，并为 `VisualStateManager.GoToState` 该对象调用。 这是因为，这两种可视状态都在页面上定位多个对象。

您可能想知道：代码隐藏文件必须引用定义可视状态的页面上的对象，为什么代码隐藏文件无法直接访问此对象和其他对象？ 当然可以。 不过，使用 VSM 的优点是，你可以控制视觉对象如何完全在 XAML 中响应不同状态，这会将所有 UI 设计保存在一个位置。 这可以避免通过直接从代码隐藏访问视觉对象来设置视觉外观。

## <a name="visual-state-triggers"></a>可视状态触发器

可视状态支持状态触发器，这些触发器是一组专用的触发器，用于定义 [`VisualState`](xref:Xamarin.Forms.VisualState) 应用应用的条件。

状态触发器添加到 [`VisualState`](xref:Xamarin.Forms.VisualState) 的 [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) 集合。 此集合可以包含一个或多个状态触发器。 当此集合中的任何状态触发器处于活动状态时，便会应用 [`VisualState`](xref:Xamarin.Forms.VisualState)。

使用状态触发器来控制视觉对象状态时，Xamarin.Forms 使用以下优先规则来确定哪个触发器（以及相应的 [`VisualState`](xref:Xamarin.Forms.VisualState)）处于活动状态：

1. 任何派生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 的触发器。
1. 因满足 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) 条件而激活的 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)。
1. 因满足 [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) 条件而激活的 [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)。

如果多个触发器同时处于活动状态（例如，两个自定义触发器），则标记中声明的第一个触发器优先。

有关状态触发器的详细信息，请参阅[状态触发器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)。

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>使用视觉对象状态管理器进行自适应布局

Xamarin.Forms通常，在手机上运行的应用程序可以在纵向或横向纵横比上查看，并且 Xamarin.Forms 可以调整桌面上运行的程序的大小，以采用许多不同的大小和纵横比。 设计良好的应用程序可能会以不同的方式显示这些各种页面或窗口外形因素的内容。

此方法有时称为 _自适应布局_。 因为自适应布局只涉及程序的视觉对象，所以它是可视状态管理器的理想应用。

一个简单的示例是应用程序，它显示了影响应用程序内容的小按钮集合。 在纵向模式下，可能会在页面顶部的水平行中显示这些按钮：

[![VSM 自适应布局：纵向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 自适应布局-纵向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在横向模式下，按钮数组可以移到一边，并显示在列中：

[![VSM 自适应布局：横向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自适应布局-横向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

从上到下，程序正在通用 Windows 平台、Android 和 iOS 上运行。

[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)示例中的 " **VSM 自适应布局**" 页面定义名为 "OrientationStates" 的组，其中包含两个名为 "纵向" 和 "横向" 的可视状态。  (更复杂的方法可能基于几个不同的页面或窗口宽度。 ) 

VSM 标记出现在 XAML 文件中的四个位置。 `StackLayout`指定的 `mainStack` 包含了菜单和内容，这是一个 `Image` 元素。 这 `StackLayout` 应在纵向模式下具有垂直方向，在横向模式下为水平方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">
                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">
                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">
                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>
                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

`ScrollView`名为的内部 `menuScroll` ， `StackLayout` 命名为 `menuStack` 实现按钮菜单。 这些布局的方向相反 `mainStack` 。 菜单应在纵向模式下水平滚动，在横向模式下垂直。

VSM 标记的第四部分是按钮本身的隐式样式。 此标记将设置 `VerticalOptions` 、 `HorizontalOptions` 和 `Margin` 属性，这些属性特定于纵向和横向方向。

代码隐藏文件设置 `BindingContext` 的属性 `menuStack` 以实现 `Button` 命令，并且还将处理程序附加到 `SizeChanged` 页面的事件：

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

`SizeChanged`处理程序将调用 `VisualStateManager.GoToState` 两个 `StackLayout` 和两个 `ScrollView` 元素，然后循环遍历的子级 `menuStack` 以调用 `VisualStateManager.GoToState` `Button` 元素。

这似乎是代码隐藏文件可以通过设置 XAML 文件中元素的属性来更直接地处理方向更改，但视觉对象状态管理器是一种结构化方法。 所有视觉对象都保存在 XAML 文件中，在此文件中，它们会更易于检查、维护和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>具有 Xamarin 大学的视觉对象状态管理器

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Forms 3.0 视觉状态管理器视频**

## <a name="related-links"></a>相关链接

- [VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [状态触发器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)