---
title: Xamarin.Forms 母版页详细信息页
description: Xamarin.Forms MasterDetailPage 页面用于管理两个相关信息页，其中一个为显示项的母版页，另一个为详细信息页，显示母版页上各项的详细信息。 本文介绍如何使用 MasterDetailPage 并在其信息页面之间导航。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d29dafd5bcdf991f275580850d77a4923d37286e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563050"
---
# <a name="no-locxamarinforms-master-detail-page"></a>Xamarin.Forms 母版页详细信息页

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Xamarin.Forms MasterDetailPage 页面用于管理两个相关信息页，其中一个为显示项的母版页，另一个为详细信息页，显示母版页上各项的详细信息。本文介绍如何使用 MasterDetailPage 并在其信息页面之间导航。_

## <a name="overview"></a>概述

母版页通常会显示项列表，如以下屏幕截图中所示：

[![母版页组件](master-detail-page-images/masterpage-components.png)](master-detail-page-images/masterpage-components-large.png#lightbox "母版页组件")

每个平台上的项列表的位置是相同的，选择某一项将导航到相关的详细信息页。 此外，母版页还提导航栏功能，包含一个可用于导航到活动详细信息页的按钮：

- 在 iOS 中，导航栏位于页面顶部，并有一个导航到详细信息页的按钮。 此外，可通过向左滑动母版页来导航到活动详细信息页。
- 在 Android 中，导航栏位于页面顶部，显示标题、图标和导航到详细信息页的按钮。 在 `[Activity]` 属性中定义图标，该属性修饰特定于 Android 平台的项目中的 `MainActivity` 类。 此外，可以通过向左滑动母版页、点击最右侧的详细信息页或点击屏幕底部的“返回”按钮来导航到活动详细信息页。
- 在通用 Windows 平台 (UWP) 中，导航栏位于页面顶部，并有一个可以导航到详细信息页的按钮。

详细信息页显示和母版页上选择的项相关的数据，且以下屏幕截图中展示了详细信息页的主要组件：

![详细信息页组件](master-detail-page-images/detailpage-components.png)

详细信息页包含内容依赖于平台的导航栏：

- 在 iOS 中，导航栏位于页面顶部并显示标题，并且有一个返回母版页的按钮，前提是详细信息页实例包含在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例中。 此外，还可以通过向右滑动详细信息页返回母版页。
- 在 Android 中，导航栏位于页面顶部，显示标题、图标和返回母版页的按钮。 在 `[Activity]` 属性中定义图标，该属性修饰特定于 Android 平台的项目中的 `MainActivity` 类。
- 在 UWP 中，导航栏位于页面顶部，显示标题，并有一个返回母版页的按钮。

### <a name="navigation-behavior"></a>导航行为

母版页和详细信息页之间的导航体验行为取决于平台：

- 在 iOS 中，当母版页从左侧滑动时，详细信息页向右滑动，并且详细信息页的左侧部分仍然可见。
- 在 Android 中，详细信息和母版页相互叠加。
- 在 UWP 上，如果 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 属性设置为 `Popover`，母版页会从左侧滑动到部分详细信息页上方。 有关更多信息，请参阅[控制详细信息页显示行为](#controlling-the-detail-page-display-behavior)。

在横向模式下将观察到类似的行为，除了 iOS 和 Android 中的母版页在纵向模式下具有与母版页类似的宽度，因此将显示更多详细信息页。

有关如何控制导航行为的信息，请参阅[控制详细信息页显示行为](#controlling-the-detail-page-display-behavior)。

## <a name="creating-a-masterdetailpage"></a>创建 MasterDetailPage

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 包含同为类型 [`Page`](xref:Xamarin.Forms.Page) 的 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)，用于分别获取和设置母版页和详细信息页。

> [!IMPORTANT]
> [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 旨在作为根页，且在其他页面类型中将其作为子页使用时可能导致意外和不一致行为。 此外，我们建议 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的母版页应始终为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例，且详细信息页应只使用 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 `ContentPage` 实例填充。 这将有助于确保在所有平台上都有一致的用户体验。

以下 XAML 代码示例显示了设置 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 属性的 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

以下代码示例演示了在 C# 中创建的等效 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 属性设置为 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例。 [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 属性设置为包含 `ContentPage` 实例的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。

### <a name="creating-the-master-page"></a>创建母版页

以下 XAML 代码示例演示了 `MasterPage` 对象的声明，通过 [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 属性引用该声明：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

该页面由 [`ListView`](xref:Xamarin.Forms.ListView) 组成且通过将其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 属性设置为 `MasterPageItem` 实例数组来填充 XAML 中的数据。 每个 `MasterPageItem` 定义 `Title`、`IconSource` 和 `TargetType` 属性。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指定为 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 属性以显示每个 `MasterPageItem`。 `DataTemplate` 包含由 [`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 组成的 [`ViewCell`](xref:Xamarin.Forms.ViewCell)。 [`Image`](xref:Xamarin.Forms.Image) 显示 `IconSource` 属性值，[`Label`](xref:Xamarin.Forms.Label) 显示每个 `MasterPageItem` 的 `Title` 属性值。

该页面有 [`Title`](xref:Xamarin.Forms.Page.Title) 和 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 属性集。 如果详细信息页有标题栏，则图标将显示在详细信息页上。 必须在 iOS 上通过将详细信息页面实例包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例中来启用此功能。

> [!NOTE]
> [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 页必须要有其 [`Title`](xref:Xamarin.Forms.Page.Title) 属性集，否则会出现异常。

以下代码示例演示了在 C# 中创建的等效页：

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

以下屏幕截图显示每个平台上的母版页：

![母版页示例](master-detail-page-images/masterpage.png)

### <a name="creating-and-displaying-the-detail-page"></a>创建并显示详细信息页

`MasterPage` 实例包含公开其 [`ListView`](xref:Xamarin.Forms.ListView) 实例的 `ListView` 属性，以便 `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 实例可以注册事件处理程序以处理 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 这使 `MainPage` 实例能够将 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 属性设置为表示所选 `ListView` 项的页面。 以下代码示例演示事件处理程序：

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected` 方法执行以下操作：

- 它检索来自 [`ListView`](xref:Xamarin.Forms.ListView) 实例的 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)，并假设其不是 `null`，则将详细信息页设置为存储在 `MasterPageItem` 的 `TargetType` 属性中的页面类型的新实例。 页面类型包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例中以确保通过 `MasterPage` 上的 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 属性引用的图标能够显示在 iOS 中的详细信息页上。
- [`ListView`](xref:Xamarin.Forms.ListView) 中所选的项设置为 `null`，以确保下次显示 `MasterPage` 时不会选择任何 `ListView` 中的项。
- 通过将 [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) 属性设置为 `false` 以将详细信息页显示给用户。 此属性控制是显示母版页还是详细信息页。 应将其设置为 `true` 以显示母版页，设置为 `false` 以显示详细信息页。

以下屏幕截图显示了 `ContactPage` 详细信息页，在母版页上选择它后将显示：

![详细信息页示例](master-detail-page-images/detailpage.png)

### <a name="controlling-the-detail-page-display-behavior"></a>控制详细信息页显示行为

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理母版页和详细信息页的方式取决于应用程序是在手机还是平板电脑上运行、设备的方向以及 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 属性的值。 此属性决定详细信息页的显示方式。 可能的值为：

- **默认** - 使用平台默认值显示页面。
- **弹出框** - 详细信息页覆盖或部分覆盖母版页。
- **拆分** - 母版页显示在左侧，详细信息页显示在右侧。
- **SplitOnLandscape** - 当设备处于横向时使用分屏。
- **SplitOnPortrait** - 当设备处于纵向时使用分屏。

以下 XAML 代码示例演示如何在 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 上设置 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 属性：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

以下代码示例演示了在 C# 中创建的等效 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

但是，[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 属性的值只影响在平板电脑或桌面上运行的应用程序。 在手机上运行的应用程序始终具有弹出框行为。

## <a name="summary"></a>总结

本文介绍如何使用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 并在其信息页面之间导航。 Xamarin.Forms `MasterDetailPage` 页面用于管理两个相关信息页，其中一个为显示项的母版页，另一个为详细信息页，显示母版页上各项的详细信息。

## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage（示例）](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)