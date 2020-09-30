---
title: Xamarin.Forms 项
description: MenuItem 类用于为菜单项（如 ListView 项上下文菜单和 Shell 应用程序飞出菜单）创建菜单项。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 749e569e36896441c5b7cebbc4425d9c0e860e0b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559631"
---
# <a name="no-locxamarinforms-menuitem"></a>Xamarin.Forms 项

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) 类定义菜单的菜单项 `ListView` ，如项上下文菜单和 Shell 应用程序飞出菜单。

以下屏幕截图显示 `MenuItem` `ListView` IOS 和 Android 上上下文菜单中的对象：

[!["IOS 和 Android 上的菜单项"](menuitem-images/menuitem-demo-cropped.png "IOS 和 Android 上的菜单项")](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 完整映像上的菜单项")

`MenuItem` 类定义了以下属性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) 是一个 `ICommand` ，它允许将用户操作（如指指单击或单击）绑定到在 viewmodel 上定义的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)`object`指定应传递到的参数的 `Command` 。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource`定义显示图标的值。
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) 一个 `bool` 值，该值指示是否 `MenuItem` 从列表中移除其关联的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled) 一个 `bool` 值，该值指示此对象是否响应用户输入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string`指定显示文本的值。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，因此， `MenuItem` 实例可以是数据绑定的目标。

## <a name="create-a-menuitem"></a>创建 MenuItem

`MenuItem` 对象可用于对象项的上下文菜单中 `ListView` 。 最常见的模式是 `MenuItem` 在实例中创建对象 `ViewCell` ，该对象用作的 `DataTemplate` 对象 `ListView` `ItemTemplate` 。 `ListView`填充对象时，它将使用创建每个项，并在为 `DataTemplate` `MenuItem` 项激活上下文菜单时公开选项。

下面的示例显示了 `MenuItem` 对象在对象上下文中的实例化 `ListView` ：

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

`MenuItem`也可以在代码中创建：

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>定义包含事件的 MenuItem 行为

`MenuItem` 类会公开 `Clicked` 事件。 可以将事件处理程序附加到此事件，以响应在 XAML 中的点击或单击 `MenuItem` 实例：

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件处理程序也可以在代码中附加：

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

前面的示例引用了 `OnItemClicked` 事件处理程序。 下面的代码演示实现示例：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>通过 MVVM 定义 MenuItem 行为

`MenuItem`类通过 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象和接口，支持模型-视图-VIEWMODEL (MVVM) 模式 `ICommand` 。 以下 XAML 显示了 `MenuItem` 绑定到 viewmodel 上定义的命令的实例：

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

在前面的示例中，定义了两个 `MenuItem` 对象，并将其 `Command` 和 `CommandParameter` 属性绑定到 viewmodel 上的命令。 Viewmodel 包含 XAML 中引用的命令：

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

示例应用程序包含一个 `DataService` 类，该类用于获取用于填充对象的项的列表 `ListView` 。 使用类中的项来实例化 viewmodel， `DataService` 并将其设置为 `BindingContext` 代码隐藏中的：

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 图标

> [!WARNING]
> `MenuItem` 对象只显示 Android 上的图标。 在其他平台上，仅显示由属性指定的文本 `Text` 。

 图标是使用属性指定的 `IconImageSource` 。 如果指定了图标，则不会显示由属性指定的文本 `Text` 。 以下屏幕截图显示 `MenuItem` Android 上带有图标的：

!["Android 上的 MenuItem 图标屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上菜单菜单的屏幕截图")

有关使用中的图像的详细信息 Xamarin.Forms ，请参阅[中的 Xamarin.Forms 图像](~/xamarin-forms/user-interface/images.md)。

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>在运行时启用或禁用 MenuItem

若要 `MenuItem` 在运行时启用，请将其 `Command` 属性绑定到 `ICommand` 实现，并确保 `canExecute` 委托启用和禁用 `ICommand` 相应的。

> [!IMPORTANT]
> `IsEnabled`使用 `Command` 属性启用或禁用时，不要将属性绑定到另一个属性 `MenuItem` 。

下面的示例演示了 `MenuItem` 一个 `Command` 属性绑定到一个 `ICommand` 命名的 `MyCommand` ：

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

`ICommand`实现需要一个 `canExecute` 委托，该委托返回属性的值 `bool` 以启用和禁用 `MenuItem` ：

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public MyViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsMenuItemEnabled);
    }
}
```

在此示例中，在 `MenuItem` 设置属性之前，将禁用 `IsMenuItemEnabled` 。 发生这种情况时，将 `Command.ChangeCanExecute` 调用方法，这将导致 `canExecute` `MyCommand` 重新计算的委托。

## <a name="cross-platform-context-menu-behavior"></a>跨平台上下文菜单行为

上下文菜单在每个平台上以不同的方式进行访问和显示。

在 Android 上，通过长时间按列表项激活上下文菜单。 上下文菜单替换标题和导航栏区域， `MenuItem` 选项显示为水平按钮。

!["Android 上上下文菜单的屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上上下文菜单的屏幕截图")

在 iOS 上，通过在列表项上轻扫激活上下文菜单。 上下文菜单显示在列表项上，并 `MenuItems` 显示为水平按钮。

!["IOS 上上下文菜单的屏幕截图"](menuitem-images/menuitem-ios-contextmenu.png "IOS 上上下文菜单的屏幕截图")

在 UWP 上，右键单击列表项即可激活上下文菜单。 上下文菜单将作为垂直列表显示在光标附近。

!["UWP 上的上下文菜单的屏幕截图"](menuitem-images/menuitem-uwp.png "UWP 上的上下文菜单的屏幕截图")

## <a name="related-links"></a>相关链接

* [MenuItem 演示](/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [中的映像 Xamarin.Forms](~/xamarin-forms/user-interface/images.md)