---
title: Xamarin.Forms Shell 布局
description: 浮出控件后，Shell 应用程序中的下一级别导航为底部选项卡栏。 或者，应用程序的导航模式可以以底部选项卡开头且不使用浮出控件。 在两种情况下，当底部选项卡包含多个页面时，页面将可通过顶部选项卡导航。
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 59c7435273d7c01f93ef0bbfb0240e3917f8b716
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563011"
---
# <a name="no-locxamarinforms-shell-tabs"></a>Xamarin.Forms Shell 选项卡

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

当应用程序的导航模式包括浮出控件时，应用程序中的下一级导航为底部选项卡栏。 此外，浮出控件关闭时，系统可以将底部选项卡栏视作顶级导航。

或者，应用程序的导航模式可以以底部选项卡开头且不使用浮出控件。 在此方案中，`Shell` 对象的子对象应为 `TabBar` 对象，用于表示底部选项卡栏。

> [!NOTE]
> `TabBar` 类型禁用浮出控件。

每个 `FlyoutItem` 或 `TabBar` 对象可以包含一个或多个 `Tab` 对象，每个 `Tab` 对象表示底部选项卡栏上的一个选项卡。 每个 `Tab` 对象可以包含一个或多个 `ShellContent` 对象，每个 `ShellContent` 对象将显示单个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象。 当一个 `Tab` 对象中存在多个 `ShellContent` 对象时，`ContentPage` 对象将可通过顶部选项卡导航。

在每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象中，可以导航到其他 `ContentPage` 对象。 有关导航的详细信息，请参阅 [Xamarin.Forms Shell 导航](navigation.md)。

## <a name="single-page-application"></a>单页应用程序

最简单的 Shell 应用程序为单页应用程序，可以通过向 `TabBar` 对象添加单个 `Tab` 对象来创建该应用程序。 在 `Tab` 对象中，应将 `ShellContent` 对象设置为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

此代码示例生成了以下单页应用程序：

[![iOS 和 Android 上的 Shell 单页应用的屏幕截图](tabs-images/single-page-app.png "Shell 单页应用")](tabs-images/single-page-app-large.png#lightbox "Shell 单页应用")

> [!NOTE]
> 如果需要，可以通过在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象上将 `Shell.NavBarIsVisible` 附加属性设置为 `false` 来隐藏导航栏。

Shell 具有隐式转换运算符，可以简化 Shell 的视觉层次结构，而无需在可视化树中引入额外的视图。 这是可能的，因为子类 `Shell` 对象只能包含 `FlyoutItem` 对象或 `TabBar` 对象，它们只能包含 `Tab` 对象，而此对象只能包含 `ShellContent` 对象。 这些隐式转换运算符可用于从前面的示例中删除 `TabBar`、`Tab` 和 `ShellContent` 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

此隐式转换自动将 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象包装在 `ShellContent` 对象中，`ShellContent` 对象包装在 `Tab` 对象中，`Tab` 对象包装在 `FlyoutItem` 对象中。 单页应用程序中不需要浮出控件，因此将 `Shell.FlyoutBehavior` 属性设置为 `Disabled`。

> [!IMPORTANT]
> 在 Shell 应用程序中，作为 `ShellContent` 对象子对象的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在应用程序启动期间创建的。 使用该方法添加其他 `ShellContent` 对象将导致在应用程序启动期间创建额外页面，这可能导致糟糕的启动体验。 不过，Shell 还能够根据需要创建页面，以响应导航。 有关详细信息，请参阅[高效页面加载](tabs.md#efficient-page-loading)。

## <a name="bottom-tabs"></a>底部选项卡

倘若一个 `TabBar` 对象中有多个 `Tab` 对象，则 `Tab` 对象呈现为底部选项卡：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

选项卡标题和图标在每个 `Tab` 对象上设置，并在底部选项卡上显示：

[![iOS 和 Android 上显示了底部选项卡的 Shell 双页应用屏幕截图](tabs-images/two-page-app-bottom-tabs.png "显示了底部选项卡的 Shell 双页应用")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "显示了底部选项卡的 Shell 双页应用")

如果有五个以上的选项卡，会显示“更多”选项卡，可用于访问其他选项卡：

[![iOS 和 Android 上显示了“更多”选项卡的 Shell 应用的屏幕截图](tabs-images/more-tabs.png "显示了“更多”选项卡的 Shell 应用")](tabs-images/more-tabs-large.png#lightbox "显示了“更多”选项卡的 Shell 应用")

或者，可以使用 Shell 的隐式转换运算符删除前面示例中的 `ShellContent` 和 `Tab` 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

此隐式转换自动将每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象包装在 `ShellContent` 对象中，然后将两者均包装在 `Tab` 对象中。

> [!IMPORTANT]
> 在 Shell 应用程序中，作为 `ShellContent` 对象子对象的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在应用程序启动期间创建的。 使用该方法添加其他 `ShellContent` 对象将导致在应用程序启动期间创建额外页面，这可能导致糟糕的启动体验。 不过，Shell 还能够根据需要创建页面，以响应导航。 有关详细信息，请参阅[高效页面加载](tabs.md#efficient-page-loading)。

### <a name="tab-class"></a>Tab 类

`Tab` 类包含以下控制选项卡外观和行为的属性：

- `CurrentItem`，属于 `ShellContent` 类型，表示所选项。
- `FlyoutDisplayOptions`，属于 `FlyoutDisplayOptions` 类型，定义项及其子项在浮出控件中的显示方式。 默认值为 `AsSingleItem`。
- `FlyoutIcon`，属于 `ImageSource` 类型，定义将在浮出控件中显示的图标。
- `Icon`，属于 `ImageSource` 类型，定义要在 chrome 的非浮出控件部分显示的图标。
- `IsChecked`，属于 `boolean` 类型，定义项当前是否在浮出控件中突出显示。
- `IsEnabled`，属于 `boolean` 类型，定义项是否可在 chrome 中选择。
- `IsTabStop`，属于 `bool` 类型，指示 `Tab` 是否包含在 Tab 导航中。 其默认值是 `true`，当它的值是 `false` 时，Tab 导航基础设施将忽略 `Tab`，而不管是否设置了 `TabIndex`。
- `Items`，属于 `IList<ShellContent>` 类型，定义 `Tab` 中的所有内容。
- `TabIndex`，属于 `int` 类型，指示当用户通过按 Tab 键导航项时 `Tab` 对象接收焦点的顺序。 此属性的默认值为 0。
- `Title`，属于 `string` 类型，表示在 UI 中的选项卡上显示的标题。

## <a name="shell-content"></a>Shell 内容

每个 `Tab` 对象的子对象都是 `ShellContent` 对象，其 `Content` 属性设置为 [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

在每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象中，可以导航到其他 `ContentPage` 对象。 有关导航的详细信息，请参阅 [Xamarin.Forms Shell 导航](navigation.md)。

> [!NOTE]
> 每个 `ShellContent` 对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 均继承自父级 `Tab` 对象。

### <a name="shellcontent-class"></a>ShellContent 类

`ShellContent` 类包含以下控制选项卡内容外观和行为的属性：

- `Content`属于 `object` 类型，表示 `ShellContent` 的内容。
- `ContentTemplate`，属于 `DataTemplate` 类型，是用于动态放大 `ShellContent` 内容的模板。
- `FlyoutIcon`，属于 `ImageSource` 类型，定义将在浮出控件中显示的图标。
- `Icon`，属于 `ImageSource` 类型，定义要在 chrome 的非浮出控件部分显示的图标。
- `IsChecked`，属于 `boolean` 类型，定义项当前是否在浮出控件中突出显示。
- `IsEnabled`，属于 `boolean` 类型，定义项是否可在 chrome 中选择。
- `IsVisible`：类型为 `bool`，指明 `ShellContent` 是否对所有 UI 结构都是隐藏的。 默认值为 `true`。
- `MenuItems`，属于 `MenuItemCollection` 类型，表示当此 `ShellContent` 为呈现页面时，要在浮出控件中显示的菜单项。
- `Title`，属于 `string` 类型，表示在 UI 中显示的标题。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

## <a name="bottom-and-top-tabs"></a>底部和顶部选项卡

当一个 `Tab` 对象中存在多个 `ShellContent` 对象时，将在底部选项卡中添加一个顶部选项卡栏，通过该选项卡栏可以导航 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

这会导致如以下屏幕截图中所示的布局：

[![iOS 和 Android 上显示了顶部和底部选项卡的 Shell 双页应用屏幕截图](tabs-images/two-page-app-top-tabs.png "显示了顶部和底部选项卡的 Shell 双页应用")](tabs-images/two-page-app-top-tabs-large.png#lightbox "显示了顶部和底部选项卡的 Shell 双页应用")

或者，可以使用 Shell 的隐式转换运算符删除前面示例中的 `ShellContent` 对象和第二个 `Tab` 对象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

此隐式转换自动将 `MonkeysPage` 包装在 `ShellContent` 对象中，然后将其包装在 `Tab` 对象中。 此外，`CatsPage` 和 `DogsPage` 隐式包装在 `ShellContent` 对象中。

## <a name="efficient-page-loading"></a>高效页面加载

在 Shell 应用程序中，`ShellContent` 对象中的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象都是在应用程序启动期间创建的，这可能会导致糟糕的启动体验。 不过，Shell 还允许根据需要创建页面，以响应导航。 这可以通过使用 `DataTemplate` 标记扩展来实现，将每个 `ContentPage` 转换为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，然后将结果设置为 `ShellContent.ContentTemplate` 属性值：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <TabBar>
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
    </TabBar>    
</Shell>
```

此 XAML 创建和显示 `CatsPage`，因为它是子类 `Shell` 对象中声明的第一个内容项。 可以通过底部选项卡导航到 `DogsPage` 和 `MonkeysPage`，只有在用户导航到它们时才会创建这些页面。 此方法的优点是避免了糟糕的启动体验，因为页面是根据响应导航需要创建的，而不是在应用程序启动时创建的。

## <a name="tab-appearance"></a>选项卡外观

`Shell` 类定义以下控制选项卡外观的附加属性：

- `TabBarBackgroundColor`，属于 `Color` 类型，用于定义选项卡栏的背景色。 如果未设置属性，则使用 `BackgroundColor` 属性值。
- `TabBarDisabledColor`，属于 `Color` 类型，用于定义选项卡栏的禁用状态颜色。 如果未设置属性，则使用 `DisabledColor` 属性值。
- `TabBarForegroundColor`，属于 `Color` 类型，用于定义选项卡栏的前景色。 如果未设置属性，则使用 `ForegroundColor` 属性值。
- `TabBarTitleColor`，属于 `Color` 类型，用于定义选项卡栏的标题颜色。 如果未设置属性，将使用 `TitleColor` 属性值。
- `TabBarUnselectedColor`，属于 `Color` 类型，用于定义选项卡栏的未选定颜色。 如果未设置属性，则使用 `UnselectedColor` 属性值。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标并设置样式。

下面的示例演示设置不同选项卡颜色属性的 XAML 样式：

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

此外，还可以使用级联样式表 (CSS) 来设置选项卡的样式。 有关详细信息，请参阅 [Xamarin.Forms Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 导航](navigation.md)
- [Xamarin.Forms CSS Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)