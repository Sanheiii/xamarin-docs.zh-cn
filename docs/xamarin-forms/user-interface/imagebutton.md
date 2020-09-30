---
title: Xamarin.Forms ImageButton
description: ImageButton 会显示一个图像，并对指示应用程序的点击或单击进行响应，以执行特定任务。
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3b27ef8ecbd5f357eabd728423b5787ea222c593
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562296"
---
# <a name="no-locxamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton 会显示一个图像，并对指示应用程序的点击或单击进行响应，以执行特定任务。_

`ImageButton`视图将视图和视图组合在一起， [`Button`](xref:Xamarin.Forms.Button) [`Image`](xref:Xamarin.Forms.Image) 以创建其内容为图像的按钮。 用户按下 `ImageButton` 手指或用鼠标单击它，以指示应用程序执行特定任务。 但与视图不同 `Button` ， `ImageButton` 视图没有文本和文本外观的概念。

> [!NOTE]
> [`Button`](xref:Xamarin.Forms.Button)如果视图定义了一个 [`Image`](xref:Xamarin.Forms.Button.Image) 属性，该属性允许你在上显示图像 `Button` ，则此属性将在文本旁边显示一个小图标时使用 `Button` 。

本指南中的代码示例摘自 [FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)。

## <a name="setting-the-image-source"></a>设置图像源

`ImageButton` 定义一个 `Source` 属性，该属性应设置为要在按钮中显示的图像，图像源是文件、URI、资源或流。 有关从不同源加载图像的详细信息，请参阅[中 Xamarin.Forms 的图像](images.md)。

下面的示例演示如何 `ImageButton` 在 XAML 中实例化：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Source`属性指定显示在中的图像 `ImageButton` 。 在此示例中，将其设置为将从每个平台项目加载的本地文件，并生成以下屏幕截图：

[![基本 ImageButton](imagebutton-images/BasicImageButton.png "基本 ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "基本 ImageButton")

默认情况下， `ImageButton` 为矩形，但您可以通过使用属性为其指定圆角 `CornerRadius` 。 有关外观的详细信息 `ImageButton` ，请参阅 [ImageButton 外观](#imagebutton-appearance)。

> [!NOTE]
> 虽然 `ImageButton` 可以加载动态 gif，但它只显示 gif 的第一帧。

下面的示例演示如何创建一个在功能上等同于上一个 XAML 示例的页，但完全使用 c #：

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>处理 ImageButton 单击

`ImageButton` 定义在 `Clicked` 用户 `ImageButton` 使用手指或鼠标指针点击时触发的事件。 当从的图面释放手指或鼠标按钮时，会触发事件 `ImageButton` 。 `ImageButton`必须 `IsEnabled` 将其属性设置为 `true` ，以响应点击。

下面的示例演示如何 `ImageButton` 在 XAML 中实例化并处理其 `Clicked` 事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Clicked`事件设置为一个名为的事件处理程序 `OnImageButtonClicked` ，该处理程序位于代码隐藏文件中：

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

点击 `ImageButton` 时，将执行 `OnImageButtonClicked` 方法。 `sender`自变量是 `ImageButton` 此事件的责任。 您可以使用它来访问 `ImageButton` 对象，或区分 `ImageButton` 共享同一事件的多个对象 `Clicked` 。

此特定 `Clicked` 处理程序将递增计数器，并在中显示计数器值 [`Label`](xref:Xamarin.Forms.Label) ：

[![基本 ImageButton 单击](imagebutton-images/ImageButton.png "基本 ImageButton 单击")](imagebutton-images/ImageButton-Large.png#lightbox "基本 ImageButton 单击")

下面的示例演示如何创建一个在功能上等同于上一个 XAML 示例的页，但完全使用 c #：

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>禁用 ImageButton

有时，应用程序处于特定的状态，即特定的 `ImageButton` 单击不是有效的操作。 在这些情况下， `ImageButton` 应通过将其属性设置 `IsEnabled` 为来禁用 `false` 。

## <a name="using-the-command-interface"></a>使用命令界面

应用程序可以响应 `ImageButton` 点击，而不会处理 `Clicked` 事件。 `ImageButton`实现一个替代的通知机制，称为_命令_或_commanding_命令性接口。 这包括两个属性：

- `Command` 类型为的 [`ICommand`](xref:System.Windows.Input.ICommand) ，它是在命名空间中定义的接口 [`System.Windows.Input`](xref:System.Windows.Input) 。
- `CommandParameter` 类型的属性 [`Object`](xref:System.Object) 。

此方法适用于与数据绑定的连接，尤其是实现模型-视图-ViewModel (MVVM) 体系结构时。

有关使用命令界面的详细信息，请参阅在[按钮](button.md)指南中[使用命令界面](button.md#using-the-command-interface)。

## <a name="pressing-and-releasing-the-imagebutton"></a>按下并释放 ImageButton

除了 `Clicked` 事件，`ImageButton` 还定义了 `Pressed` 和 `Released` 事件。 `Pressed`当手指按下时 `ImageButton` ，或在鼠标指针位于上并按下鼠标按钮时发生该事件 `ImageButton` 。 `Released`当鼠标悬停或鼠标按钮时，发生此事件。 通常情况下，事件 `Clicked` 也会同时触发 `Released` ，但如果手指或鼠标指针在被释放之前从表面上离开 `ImageButton` ，则 `Clicked` 事件可能不会发生。

有关这些事件的详细信息，请参阅[按下并松开](button.md#pressing-and-releasing-the-button)[按钮](button.md)指南中的按钮。

## <a name="imagebutton-appearance"></a>ImageButton 外观

除了 `ImageButton` 继承自类的属性以外 [`View`](xref:Xamarin.Forms.View) ， `ImageButton` 还定义了多个影响其外观的属性：

- `Aspect` 如何对图像进行缩放以适应显示区域。
- `BorderColor` 围绕的区域的颜色 `ImageButton` 。
- `BorderWidth` 边框的宽度。
- `CornerRadius` 是的角半径 `ImageButton` 。

`Aspect`属性可以设置为枚举的成员之一 [`Aspect`](xref:Xamarin.Forms.Aspect) ：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -将图像拉伸到完全完全填充 `ImageButton` 。 这可能会导致图像扭曲。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -剪辑图像，以便在 `ImageButton` 保持纵横比的同时填充图像。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterboxes 图像 (如有必要) 以便将整个图像放入 `ImageButton` ，并根据图像的宽度或高度，将空白区域添加到顶部/底部或边。 这是枚举的默认值 [`Aspect`](xref:Xamarin.Forms.Aspect) 。

> [!NOTE]
> `ImageButton`类还具有 [`Margin`](xref:Xamarin.Forms.View.Margin) 控制的 `Padding` 布局行为的和属性 `ImageButton` 。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="imagebutton-visual-states"></a>ImageButton 视觉状态

`ImageButton` 具有 `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于在用户按下时启动对的视觉对象更改 `ImageButton` （如果已启用）。

下面的 XAML 示例演示如何为状态定义可视状态 `Pressed` ：

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定在 `ImageButton` 按下时，其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 属性将从其默认值1更改为0.8。 `Normal` `VisualState` 指定在 `ImageButton` 处于正常状态时，其 `Scale` 属性将设置为1。 因此，整体效果是在 `ImageButton` 按下时，它会略微缩小，重新缩放 `ImageButton` 释放后，它将被重新缩放为其默认大小。

有关可视状态的详细信息，请 [参阅 Xamarin.Forms 可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)