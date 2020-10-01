---
title: Xamarin.Forms 模式页面
description: Xamarin.Forms 支持模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。 本文演示如何导航到模式页面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ba5224c23eaed6b7c37312f8a765556d0263d9c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563024"
---
# <a name="no-locxamarinforms-modal-pages"></a>Xamarin.Forms 模式页面

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_ 支持模式页面。模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。本文演示如何导航到模式页面。

本文讨论以下主题：

- [执行导航](#performing-navigation) - 将页面推送到模式堆栈、从模式堆栈中弹出页面、禁用“后退”按钮并对页面过渡效果进行动画处理。
- [导航时传递数据](#passing-data-when-navigating) - 通过页面构造函数和 `BindingContext` 传递数据。

## <a name="overview"></a>概述

模式页可以是 Xamarin.Forms 支持的任何[页面](~/xamarin-forms/user-interface/controls/pages.md)类型。 若要显示模式页面，应用程序会将页面推送到模式堆栈中，在堆栈中，该页面会变为活动页面，如下图所示：

![将页面推送到模式堆栈](modal-images/pushing.png)

若要返到回前一页，应用程序会从模式堆栈弹出当前页面，而使最顶层的新页面成为活动页面，如下图所示：

![从模式堆栈中弹出页面](modal-images/popping.png)

## <a name="performing-navigation"></a>执行导航

可以由任何 [`Page`](xref:Xamarin.Forms.Page) 派生类型上的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性公开模式导航方法。 可通过这些方法将[模式页面推送](#pushing-pages-to-the-modal-stack)到模式堆栈中，还可从模式堆栈中[弹出模式页面](#popping-pages-from-the-modal-stack)。

[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性也可公开 [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) 属性，并从中获得模式堆栈中的模式页面。 但是，在模式导航中没有执行模式堆栈操作或弹出到根页的概念。 这是因为基础平台普遍都不支持这些操作。

> [!NOTE]
> 执行模式页面导航无需具有 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例。

### <a name="pushing-pages-to-the-modal-stack"></a>将页面推送到模式堆栈

若要导航到 `ModalPage`，必须调用当前页的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性上的 [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) 方法，如以下代码示例所示：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

这会导致 `ModalPage` 实例被推送到模式堆栈中，在该堆栈中，该实例将变为活动页面，前提是已在 `MainPage` 实例上的 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了一个项目。 `ModalPage` 实例如以下屏幕截图中所示：

![模式页面示例](modal-images/modalpage.png)

调用 [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) 时，会发生以下事件：

- 如果基础平台不是 Android，则调用 `PushModalAsync` 的页面会调用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 替代。
- 要导航到的页面会调用其 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代。
- `PushAsync` 任务完成。

但是，这些事件发生的确切顺序取决于平台。 有关详细信息，请参阅 Charles Petzold 所著的 Xamarin.Forms 书籍的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

> [!NOTE]
> 不能将对 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 和 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代的调用视为绝对的页面导航指示。 例如，在 iOS 上，应用程序终止后，将对活动页面调用 `OnDisappearing` 替代。

### <a name="popping-pages-from-the-modal-stack"></a>从模式堆栈中弹出页面

通过设备上的“后退”按钮（无论是设备上的物理按钮还是屏幕按钮），可以从模式堆栈中弹出活动页面。

若要以编程方式返回原始页，`ModalPage` 实例必须调用 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) 方法，如以下代码示例所示：

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

这会从模式堆栈中删除 `ModalPage` 实例，而使最顶层的新页面成为活动页面。 调用 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) 时，会发生以下事件：

- 调用 `PopModalAsync` 的页面会调用其 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 替代。
- 如果基础平台不是 Android，则要返回 [ 的页面会调用其 `OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 替代。
- `PopModalAsync` 任务返回。

但是，这些事件发生的确切顺序取决于平台。 有关详细信息，请参阅 Charles Petzold 所著的 Xamarin.Forms 书籍的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

### <a name="disabling-the-back-button"></a>禁用“后退”按钮

在 Android 上，只要按设备上的标准“后退”按钮，用户就可返回到上一页。 如果模式页面要求用户在离开页面之前完成独立任务，则应用程序必须禁用“后退”按钮。 可通过替代模式页面上的 [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) 方法来完成此操作。 有关详细信息，请参阅 Charles Petzold 所著的 Xamarin.Forms 书籍的[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)。

### <a name="animating-page-transitions"></a>对页面过渡效果进行动画处理

每个页面的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性还提供已替代的 push 和 pop 方法，这些方法包含一个 `boolean` 参数，用于控制是否在导航期间显示页面动画，如以下代码示例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

将 `boolean` 参数设置为 `false` 会禁用页面过渡动画，而将参数设置为 `true` 则会启用页面过渡动画，前提是基础平台支持该动画。 但是，缺少此参数的 push 和 pop 方法默认启用该动画。

## <a name="passing-data-when-navigating"></a>导航时传递数据

有时，页面必须在导航期间将数据传递到另一个页面。 实现此操作的两种方法是：通过页面构造函数传递数据，和通过将新页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为该数据。 下面将依次讨论每种方法。

### <a name="passing-data-through-a-page-constructor"></a>通过页面构造函数传递数据

在导航期间，通过页面构造函数参数将数据传递到另一个页面是最简单的方法，如以下代码示例所示：

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

此代码创建一个 `MainPage` 实例，以 ISO8601 格式传递当前日期和时间。

`MainPage` 实例通过构造函数参数接收数据，如以下代码示例所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

然后，通过设置 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性，在页面上显示数据。

### <a name="passing-data-through-a-bindingcontext"></a>通过 BindingContext 传递数据

在导航期间，另一种将数据传递到另一个页面的方法是将新页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为该数据，如以下代码示例所示：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

此代码将 `DetailPage` 实例的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `Contact` 实例，然后导航到 `DetailPage`。

然后，`DetailPage` 使用数据绑定来显示 `Contact` 实例数据，如以下 XAML 代码示例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

以下代码示例显示了如何在 C# 中完成数据绑定：

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

然后，数据会通过一系列 [`Label`](xref:Xamarin.Forms.Label) 控件显示在页面上。

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>总结

本文演示了如何导航到模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。

## <a name="related-links"></a>相关链接

- [页面导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [模式（示例）](/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData（示例）](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)