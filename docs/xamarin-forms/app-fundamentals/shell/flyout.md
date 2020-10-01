---
title: Xamarin.Forms Shell 浮出控件
description: 浮出控件是 Shell 应用程序的根菜单，可通过图标或从屏幕一侧轻扫进行访问。 浮出控件由可选标头、浮出控件项和可选菜单项组成。
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a0ec978c1947cde4bd6f7fd619ad8cd2f0dbdbce
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563037"
---
# <a name="no-locxamarinforms-shell-flyout"></a>Xamarin.Forms Shell 浮出控件

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

浮出控件是 Shell 应用程序的根菜单，可通过图标或从屏幕一侧轻扫进行访问。 浮出控件由可选标头、浮出控件项和可选菜单项组成：

![Shell 附注浮出控件的屏幕截图](flyout-images/flyout-annotated.png "批注浮出控件")

如果需要，可以通过 `Shell.FlyoutBackgroundColor` 可绑定属性将浮出控件的背景色设置为 [`Color`](xref:Xamarin.Forms.Color)。 还可以从级联样式表 (CSS) 设置此属性。 有关详细信息，请参阅 [Xamarin.Forms Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="flyout-icon"></a>浮出控件图标

默认情况下，Shell 应用程序具有一个汉堡图标，按该图标时将打开浮出控件。 可以通过设置类型为 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 的 `Shell.FlyoutIcon` 可绑定属性来将此图标更改为相应的图标：

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>浮出控件行为

访问浮出控件的方式有两种：按汉堡图标或从屏幕一侧轻扫。 但是，将 `Shell.FlyoutBehavior` 附加属性设置为 `FlyoutBehavior` 枚举成员之一可以更改此行为：

- `Disabled` – 指示用户无法打开浮出控件。
- `Flyout` – 指示用户可以打开和关闭浮出控件。 这是 `FlyoutBehavior` 属性的默认值。
- `Locked` – 指示用户无法关闭浮出控件，并且不会重叠内容。

以下示例演示如何禁用浮出控件：

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> 可以在 `Shell`、`FlyoutItem`、`ShellContent` 和页面对象上设置 `FlyoutBehavior` 附加属性以替代默认浮出控件行为。

此外，还可以通过将 `Shell.FlyoutIsPresented` 可绑定属性设置为指示浮出控件当前是否可见的 `boolean` 值来以编程方式打开和关闭浮出控件：

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="flyout-header"></a>浮出控件标头

浮出控件标头是根据需要显示在浮出控件顶部的内容，使用可通过 `Shell.FlyoutHeader` 属性值设置的 `object` 定义其外观：

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
```

`FlyoutHeader` 类型如以下示例所示：

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

这将生成以下浮出控件标头：

![浮出控件标头的屏幕截图](flyout-images/flyout-header.png "浮出控件标头")

还可以通过将 `Shell.FlyoutHeaderTemplate` 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的方式来定义浮出控件标头的外观：

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <Grid BackgroundColor="Black"
              HeightRequest="200">
            <Image Aspect="AspectFill"
                   Source="xamarinstore.jpg"
                   Opacity="0.6" />
            <Label Text="Animals"
                   TextColor="White"
                   FontAttributes="Bold"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center" />
        </Grid>            
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

默认情况下，在浮出控件中，浮出控件标头是固定的，在有足够的项时会滚动其下方的内容。 但是，将 `Shell.FlyoutHeaderBehavior` 可绑定属性设置为 `FlyoutHeaderBehavior` 枚举成员之一可以更改此行为：

- `Default` – 指示将使用的平台的默认行为。 这是 `FlyoutHeaderBehavior` 属性的默认值。
- `Fixed` – 指示浮出控件标头始终保持可见且不变。
- `Scroll` – 指示当用户滚动项时，浮出控件标头将卷出视图外。
- `CollapseOnScroll` – 指示当用户滚动项时，浮出控件标头将仅折叠到标题。

以下示例演示如何在用户滚动时折叠浮出控件标头：

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-background-image"></a>浮出控件背景图像

浮出控件可以有一个可选背景图像，该图像显示在浮出控件标头的下方，并且位于任何浮出控件项和菜单项的后面。 可以通过将类型 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 的 `FlyoutBackgroundImage` 可绑定属性设置为文件、嵌入的资源、URI 或流来指定背景图像。

可以通过将类型 [`Aspect`](xref:Xamarin.Forms.Aspect) 的 `FlyoutBackgroundImageAspect` 可绑定属性设置为 `Aspect` 枚举成员之一来配置背景图像的纵横比：

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) - 剪裁图像，使其填充显示区域，同时保持纵横比。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) - 根据需要对图像上下加框，以使其适合显示区域，并根据图像的宽度或高度，将空白添加到顶部/底部或侧边。
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) - 拉伸图像，以完全、准确填充显示区域。 这可能会导致图像失真。

默认情况下，`FlyoutBackgroundImageAspect` 属性将设置为 `AspectFit`。

以下示例演示如何设置这些属性：

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

这会导致浮出控件中出现背景图像：

![浮出控件背景图像的屏幕截图](flyout-images/flyout-backgroundimage.png "浮出控件背景图像")

## <a name="flyout-items"></a>浮出控件项

应用程序的导航模式包括浮出控件时，子类 `Shell` 对象必须包含一个或多个 `FlyoutItem` 对象，其中每个 `FlyoutItem` 对象都表示浮出控件上的一个项。 每个 `FlyoutItem` 对象应是 `Shell` 对象的子对象。

以下示例创建包含一个浮出控件标头和两个浮出控件项的浮出控件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

在此示例中，只能通过浮出控件项访问每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

[![iOS 和 Android 上显示了浮出控件项的 Shell 双页应用屏幕截图](flyout-images/two-page-app-flyout.png "显示了浮出控件项的 Shell 双页应用")](flyout-images/two-page-app-flyout-large.png#lightbox "显示了浮出控件项的 Shell 双页应用")

> [!NOTE]
> 当浮出控件标头不存在时，浮出控件项会显示在浮出控件顶部。 否则，它们显示在浮出控件标头下方。

Shell 具有隐式转换运算符，可以简化 Shell 的视觉层次结构，而无需在可视化树中引入额外的视图。 这是可能的，因为子类 `Shell` 对象只能包含 `FlyoutItem` 对象或 `TabBar` 对象，它们只能包含 `Tab` 对象，而此对象只能包含 `ShellContent` 对象。 这些隐式转换运算符可用于从前面的示例中删除 `FlyoutItem`、`Tab` 和 `ShellContent` 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

此隐式转换自动将每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象包装在 `ShellContent` 对象中，`ShellContent` 对象包装在 `Tab` 对象中，`Tab` 对象包装在 `FlyoutItem` 对象中。

> [!IMPORTANT]
> 在 Shell 应用程序中，作为 `ShellContent` 对象子对象的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在应用程序启动期间创建的。 使用该方法添加其他 `ShellContent` 对象将导致在应用程序启动期间创建额外页面，这可能导致糟糕的启动体验。 不过，Shell 还能够根据需要创建页面，以响应导航。 有关详细信息，请参阅 [Xamarin.Forms Shell 选项卡](tabs.md)指南中的[高效页面加载](tabs.md#efficient-page-loading)。

### <a name="flyoutitem-class"></a>浮出控件类

`FlyoutItem` 类包含以下控制浮出控件项外观和行为的属性：

- `FlyoutDisplayOptions`，属于 `FlyoutDisplayOptions` 类型，定义项及其子项在浮出控件中的显示方式。 默认值为 `AsSingleItem`。
- `CurrentItem`，属于 `Tab` 类型，表示所选项。
- `Items`，属于 `IList<Tab>` 类型，定义 `FlyoutItem` 中的所有选项卡。
- `FlyoutIcon`，属于 `ImageSource` 类型，表示用于项的图标。 如果未设置此属性，则会回退为使用 `Icon` 属性值。
- `Icon`，属于 `ImageSource` 类型，定义要在 chrome 的非浮出控件部分显示的图标。
- `IsChecked`，属于 `boolean` 类型，定义项当前是否在浮出控件中突出显示。
- `IsEnabled`，属于 `boolean` 类型，定义项是否可在 chrome 中选择。
- `IsTabStop`，属于 `bool` 类型，指示 `FlyoutItem` 是否包含在 Tab 导航中。 其默认值是 `true`，当它的值是 `false` 时，Tab 导航基础设施将忽略 `FlyoutItem`，而不管是否设置了 `TabIndex`。
- `IsVisible`：类型为 `bool`，指明 `FlyoutItem` 是否隐藏在浮出控件菜单中。 默认值为 `true`。
- `TabIndex`，属于 `int` 类型，指示当用户通过按 Tab 键导航项时 `FlyoutItem` 对象接收焦点的顺序。 此属性的默认值为 0。
- `Title`，属于 `string` 类型，表示在 UI 中显示的标题。
- `Route`属于 `string` 类型，表示用于对项进行寻址的字符串。

所有这些属性（`Route` 属性除外）都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

> [!NOTE]
> 子类 Shell 对象中的所有 `FlyoutItem` 对象都会添加到 `Shell.Items` 集合，该集合定义将在浮出控件中显示的项的列表。

此外，`FlyoutItem` 类公开下列可重写的方法：

- 只要 `TabIndex` 属性更改就会调用 `OnTabIndexPropertyChanged`。
- 只要 `IsTabStop` 属性更改就会调用 `OnTabStopPropertyChanged`。
- `TabIndexDefaultValueCreator` 返回 `int`，并调用它以设置 `TabIndex` 属性的默认值。
- `TabStopDefaultValueCreator` 返回 `bool`，并调用它以设置 `TabStop` 属性的默认值。

## <a name="flyout-backdrop"></a>浮出控件背景

通过将 `Shell.FlyoutBackdrop` 附加属性设置为 `Brush`，可以指定浮出控件的背景（即浮出控件叠加层的外观）：

```xaml
<Shell ...
       FlyoutBackdrop="Silver">
    ...
</Shell>
```

在此示例中，浮出控件背景是用银色 `SolidColorBrush` 绘制的。

> [!IMPORTANT]
> `FlyoutBackdrop` 附加属性可以在任何 Shell 元素上设置，但只有在 `Shell`、 `FlyoutItem` 或 `TabBar` 对象上设置时才会应用。

下面的示例展示了如何将 `FlyoutItem` 上的浮出控件背景设置为 `LinearGradientBrush`：

```xaml
<Shell ...>
    <FlyoutItem ...>
      <Shell.FlyoutBackdrop>
          <LinearGradientBrush StartPoint="0,0"
                               EndPoint="1,1">
              <GradientStop Color="#8A2387"
                            Offset="0.1" />
              <GradientStop Color="#E94057"
                            Offset="0.6" />
              <GradientStop Color="#F27121"
                            Offset="1.0" />
          </LinearGradientBrush>
      </Shell.FlyoutBackdrop>
      ...
    </FlyoutItem>
    ...
</Shell>
```

若要详细了解画笔，请参阅 [Xamarin.Forms 画笔](~/xamarin-forms/user-interface/brushes/index.md)。

## <a name="flyout-vertical-scroll"></a>浮出控件垂直滚动

默认情况下，当浮出控件中容纳不下浮出控件项时，可以垂直滚动浮出控件。 将 `Shell.FlyoutVerticalScrollMode` 可绑定属性设置为 `ScrollMode` 枚举成员之一可以更改此行为：

- `Disabled` – 指示将禁用垂直滚动。
- `Enabled` – 指示将启用垂直滚动。
- `Auto` – 指示当浮出控件中容纳不下浮出控件项时，将启用垂直滚动。 这是 `Shell.FlyoutVerticalScrollMode` 属性的默认值。

以下示例演示如何禁用垂直滚动：

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>浮出控件显示选项

`FlyoutDisplayOptions` 枚举定义下列成员：

- `AsSingleItem`，指示项将作为单个项显示。
- `AsMultipleItems`，指示项及其子项将作为一组项显示在浮出控件中。

通过将 `FlyoutItem.FlyoutDisplayOptions` 属性设置为 `AsMultipleItems`，将创建 `FlyoutItem` 内的每个 `Tab` 的浮出控件项：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

在此示例中，将为 `Tab` 对象（`FlyoutItem` 对象的子对象）以及 `ShellContent` 对象（`FlyoutItem` 对象的子对象）创建浮出控件项。 发生这种情况是因为作为 `FlyoutItem` 对象子对象的每个 `ShellContent` 对象自动包装在 `Tab` 对象中。 此外，将为最终的 `ShellContent` 对象创建浮出控件项，该对象包装在 `Tab` 对象中，然后包装在 `FlyoutItem` 对象中。

这将生成以下浮出控件项：

[![iOS 和 Android 上包含 FlyoutItem 对象的浮出控件屏幕截图](flyout-images/flyout-reduced.png "包含 FlyoutItem 对象的 Shell 浮出控件")](flyout-images/flyout-reduced-large.png#lightbox "包含 FlyoutItem 对象的 Shell 浮出控件")

## <a name="define-flyoutitem-appearance"></a>定义 FlyoutItem 外观

将 `Shell.ItemTemplate` 附加属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可自定义每个 `FlyoutItem` 的外观：

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

此示例以斜体显示每个 `FlyoutItem` 对象的标题：

[![iOS 和 Android 上模板化的 FlyoutItem 对象的屏幕截图](flyout-images/flyoutitem-templated.png "Shell 模板化的 FlyoutItem 对象")](flyout-images/flyoutitem-templated-large.png#lightbox "Shell 模板化的 FlyoutItem 对象")

`Shell.ItemTemplate` 是一个附加属性，因此可将不同的模板附加到特定的 `FlyoutItem` 对象。

> [!NOTE]
> Shell 向 `ItemTemplate` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 提供 `Title` 和 `FlyoutIcon` 属性。

此外，Shell 还包括三个可自动应用于 `FlyoutItem` 对象的样式类。 有关详细信息，请参阅 [FlyoutItem 和 MenuItem 样式类](#flyoutitem-and-menuitem-style-classes)。

### <a name="default-template-for-flyoutitems"></a>FlyoutItem 的默认模板

用于每个 `FlyoutItem` 的默认 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 如下所示：

```xaml
<DataTemplate x:Key="FlyoutTemplate">
    <Grid x:Name="FlyoutItemLayout"
          HeightRequest="{x:OnPlatform Android=50}"
          ColumnSpacing="{x:OnPlatform UWP=0}"
          RowSpacing="{x:OnPlatform UWP=0}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor"
                                    Value="{x:OnPlatform Android=#F2F2F2, iOS=#F2F2F2}" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50, UWP=Auto}" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Image x:Name="FlyoutItemImage"
               Source="{Binding FlyoutIcon}"
               VerticalOptions="Center"
               HorizontalOptions="{x:OnPlatform Default=Center, UWP=Start}"
               HeightRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}"
               WidthRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}">
            <Image.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="UWP"
                            Value="12,0,12,0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Image.Margin>
        </Image>
        <Label x:Name="FlyoutItemLabel"
               Grid.Column="1"
               Text="{Binding Title}"
               FontSize="{x:OnPlatform Android=14, iOS=Small}"
               HorizontalOptions="{x:OnPlatform UWP=Start}"
               HorizontalTextAlignment="{x:OnPlatform UWP=Start}"
               FontAttributes="{x:OnPlatform iOS=Bold}"
               VerticalTextAlignment="Center">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

此模板可用作对现有浮出控件布局进行更改的基础，还显示了为浮出控件项实现的视觉状态。

此外，[`Grid`](xref:Xamarin.Forms.Grid)、[`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 元素都具有 `x:Name` 值，因此它们可作为可视状态管理器的目标。 有关详细信息，请参阅[设置多个元素的状态](~/xamarin-forms/user-interface/visual-state-manager.md#set-state-on-multiple-elements)。

> [!NOTE]
> 还可将同一模板用于 `MenuItem` 对象。

## <a name="flyoutitem-tab-order"></a>FlyoutItem Tab 键顺序

默认情况下，`FlyoutItem` 对象的 Tab 键顺序与在 XAML 中列出的顺序或以编程方式添加到子集合中的顺序相同。 此顺序是使用键盘导航 `FlyoutItem` 对象的顺序，通常这种默认顺序是最佳顺序。

可以通过设置 `FlyoutItem.TabIndex` 属性来更改默认 Tab 键顺序，该属性指示当用户通过按 Tab 键导航项时 `FlyoutItem` 对象接收焦点的顺序。 属性的默认值是 0，可以将其设置为任何 `int` 值。

使用默认 Tab 键顺序或设置 `TabIndex` 属性时，以下规则适用：

- `FlyoutItem` 对象（`TabIndex` 等于 0）将根据 XAML 或子集合中的声明顺序添加到 Tab 键顺序中。
- `FlyoutItem` 对象（`TabIndex` 大于 0）将根据其 `TabIndex` 值添加到 Tab 键顺序中。
- `FlyoutItem` 对象（`TabIndex` 小于 0）将添加到 Tab 键顺序中，并显示在任何零值之前。
- `TabIndex` 中的冲突可通过声明顺序解决。

定义 Tab 键顺序后，按 Tab 键焦点将按升序 `TabIndex` 顺序循环通过 `FlyoutItem` 对象，并在到达最后一个对象后绕到开头。

除了设置 `FlyoutItem` 对象的 Tab 键顺序之外，可能还需要从 Tab 键顺序中排除某些对象。 这可以通过 `FlyoutItem.IsTabStop` 属性实现，该属性指示 Tab 导航中是否包含 `FlyoutItem`。 其默认值是 `true`，当它的值是 `false` 时，Tab 导航基础设施将忽略 `FlyoutItem`，而不管是否设置了 `TabIndex`。

## <a name="set-the-current-flyoutitem"></a>设置当前 FlyoutItem

`Shell` 类有一个名为 `CurrentItem` 的可绑定属性，该类属于 `FlyoutItem` 类型，表示当前选中的 `FlyoutItem`。 当首次运行 Shell 应用程序时，此属性将设置为子类 `Shell` 对象中的第一个 `FlyoutItem`。 但是，此属性可以设置为另一个 `FlyoutItem`，如以下示例所示：

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

此代码将名为 `aboutItem` 的 `ShellContent` 对象设置为 `CurrentItem` 属性，从而显示该属性。 在此示例中，隐式转换用于将 `ShellContent` 对象包装在 `Tab` 对象中，`Tab` 对象包装在 `FlyoutItem` 对象中。

假设 `ShellContent` 名为 `aboutItem`，则等效的 C# 代码为：

```csharp
CurrentItem = aboutItem;
```

在此示例中，`CurrentItem` 属性是在设为子类的 `Shell` 类中设置的。 或者，可通过 `Shell.Current` 静态属性在任何类中设置 `CurrentItem` 属性：

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>菜单项

菜单项可以选择性地添加到浮出控件，每个菜单项都由 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 对象表示。 浮出控件上的 `MenuItem` 对象的位置依赖于它们在 Shell 视觉层次结构中的声明顺序。 因此，`FlyoutItem` 对象之前声明的任何 `MenuItem` 对象将显示在浮出控件顶部，`FlyoutItem` 对象之后声明的任何 `MenuItem` 对象将显示在浮出控件底部。

> [!NOTE]
> `MenuItem` 类具有 [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 事件和 [`Command`](xref:Xamarin.Forms.MenuItem.Command) 属性。 因此，`MenuItem` 对象支持以下场景：执行一个操作以响应被点击的 `MenuItem`。 这些场景包括执行导航，以及在特定网页上打开浏览器。

可将 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 对象添加到浮出控件，如下面的示例所示：

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

此代码将向浮出控件添加两个 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 对象，显示在所有浮出控件项之下：

[![iOS 和 Android 上包含 MenuItem 对象的浮出控件屏幕截图](flyout-images/flyout.png "包含 MenuItem 对象的 Shell 浮出控件")](flyout-images/flyout-large.png#lightbox "包含 MenuItem 对象的 Shell 浮出控件")

第一个 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 对象执行名为 `RandomPageCommand` 的 `ICommand`，这将导航到应用程序中的随机页面。 第二个 `MenuItem` 对象执行名为 `HelpCommand` 的 `ICommand`，这将在 Web 浏览器中打开由 `CommandParameter` 属性指定的 URL。

> [!NOTE]
> 每个 `MenuItem` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 都继承自子类 `Shell` 对象。

## <a name="define-menuitem-appearance"></a>定义 MenuItem 外观

将 `Shell.MenuItemTemplate` 附加属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可自定义每个 `MenuItem` 的外观：

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

此示例会将 Shell 级别的 `MenuItemTemplate` 附加到每个 `MenuItem` 对象，以斜体显示每个 `MenuItem` 对象的标题：

[![iOS 和 Android 上模板化的 MenuItem 对象的屏幕截图](flyout-images/menuitem-templated.png "Shell 模板化的 MenuItem 对象")](flyout-images/menuitem-templated-large.png#lightbox "Shell 模板化的 MenuItem 对象")

> [!NOTE]
> Shell 向 `MenuItemTemplate` 的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 提供 [`Text`](xref:Xamarin.Forms.MenuItem.Text) 和 [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 属性。 你也可使用 `Title` 代替 `Text` 并使用 `Icon` 代替 `IconImageSource`，这样便可对菜单项和浮出控件项重复使用相同的模板

因为 `Shell.MenuItemTemplate` 是一个附加属性，所以可以将不同的模板附加到特定的 `MenuItem` 对象：

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```

此示例会将 Shell 级别的 `MenuItemTemplate` 附加到第一个 `MenuItem` 对象，并将内联的 `MenuItemTemplate` 附加到第二个 `MenuItem`。

> [!NOTE]
> `FlyoutItem` 对象的默认模板还可用于 `MenuItem` 对象。 有关详细信息，请参阅 [FlyoutItem 的默认模板](#default-template-for-flyoutitems)。

## <a name="flyoutitem-and-menuitem-style-classes"></a>FlyoutItem 和 MenuItem 样式类

Shell 包括三个可自动应用于 `FlyoutItem` 和 `MenuItem` 对象的样式类。 样式类名为：

- `FlyoutItemLabelStyle`
- `FlyoutItemImageStyle`
- `FlyoutItemLayoutStyle`

以下 XAML 演示一个示例，该示例定义了这些样式类的样式：

```xaml
<Style TargetType="Label"
       Class="FlyoutItemLabelStyle">
    <Setter Property="TextColor"
            Value="Black" />
    <Setter Property="HeightRequest"
            Value="100" />
</Style>

<Style TargetType="Image"
       Class="FlyoutItemImageStyle">
    <Setter Property="Aspect"
            Value="Fill" />
</Style>

<Style TargetType="Layout"
       Class="FlyoutItemLayoutStyle"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Teal" />
</Style>
```

这些样式可自动应用于 `FlyoutItem` 和 `MenuItem` 对象，而无需将其 [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) 属性设置为样式类名。

此外，还可以定义自定义样式类并将其应用于 `FlyoutItem` 和 `MenuItem` 对象。 有关样式类的详细信息，请参阅 [Xamarin.Forms 样式类](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms 样式类](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms 画笔](~/xamarin-forms/user-interface/brushes/index.md)