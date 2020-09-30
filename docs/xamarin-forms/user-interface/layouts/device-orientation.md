---
title: 设备方向
description: 本文介绍如何布局 Xamarin.Forms 在纵向和横向方向上非常美观的应用程序。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01e439812c3935e890acf0b9b83579b5813d8e21
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563947"
---
# <a name="device-orientation"></a>设备方向

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

请务必考虑应用程序的使用方式以及如何合并横向方向以改善用户体验。 可以设计各个布局来容纳多个方向并最好地使用可用空间。 在应用程序级别，可以禁用或启用旋转。

## <a name="controlling-orientation"></a>控制方向

使用时 Xamarin.Forms ，支持的设备方向控制方法是使用每个单独项目的设置。

### <a name="ios"></a>iOS

在 iOS 上，使用 **info.plist** 文件为应用程序配置设备方向。 使用本文档顶部的 IDE 选项选择要查看的说明：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，打开 iOS 项目并打开 **info.plist**。 文件将从 "iPhone 部署信息" 选项卡开始打开配置面板：

![Visual Studio 中的 iPhone 部署信息](device-orientation-images/orientation-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，打开 iOS 项目并打开 **info.plist**。 在 " **应用程序** " 选项卡下，可以使用以下各节设置方向：

![Visual Studio for Mac 中的 iPhone 部署信息](device-orientation-images/orientation-vsmac.png)

如果希望使用键值编辑器界面编辑值，请选择屏幕底部的 " **源**>" 选项卡：

![Visual Studio for Mac 中支持的设备方向](device-orientation-images/orientation-source-vsmac.png)

-----

### <a name="android"></a>Android

若要控制 Android 方向，请打开 **MainActivity.cs** ，并使用修饰类的属性设置方向 `MainActivity` ：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin 支持指定方向的几个选项：

- **横向** &ndash; 强制应用程序方向为横向，而不考虑传感器数据。
- **纵向** &ndash; 强制应用程序方向为纵向，而不考虑传感器数据。
- **用户** &ndash; 使应用程序使用用户的首选方向进行呈现。
- **后面** &ndash; 使应用程序的方向与其后 [活动](xref:Android.App.Activity) 的方向相同。
- **传感器** &ndash; 使应用程序的方向由传感器确定，即使用户已禁用自动旋转。
- **SensorLandscape** &ndash; 导致应用程序在使用传感器数据时使用横向方向，以更改屏幕所面临的方向 (使屏幕不会) 倒置。
- **SensorPortrait** &ndash; 导致应用程序在使用传感器数据时使用纵向方向，以更改屏幕所面临的方向 (使屏幕不会) 倒置。
- **ReverseLandscape** &ndash; 使应用程序使用横向方向，使其在正常方向上朝向相反方向，以便显示 "倒置"。
- **ReversePortrait** &ndash; 使应用程序使用纵向方向，使其在正常方向上朝向相反方向，因此显示 "倒置"。
- **FullSensor** &ndash; 使应用程序依赖传感器数据来选择正确的方向 (超出可能的 4) 。
- **FullUser** &ndash; 使应用程序使用用户的方向首选项。 如果启用了自动旋转，则可以使用所有4个方向。
- **UserLandscape** &ndash;_ \[ 不支持 \] _导致应用程序使用横向方向，除非用户启用了自动旋转，在这种情况下，它将使用传感器来确定方向。 此选项将中断编译。
- **UserPortrait** &ndash;_ \[ 不支持 \] _导致应用程序使用纵向，除非用户启用了自动旋转，在这种情况下，它将使用传感器来确定方向。 此选项将中断编译。
- 已**锁定** &ndash;_ \[ 不受 \] 支持_会导致应用程序在启动时使用屏幕方向，而不会响应设备物理方向的变化。 此选项将中断编译。

请注意，本机 Android Api 提供了很多控制方向的管理方式，其中包括显式矛盾用户表达首选项的选项。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 上，支持的方向是在 **appxmanifest.xml** 文件中设置的。 打开清单将显示一个配置面板，可以在其中选择支持的方向。

## <a name="reacting-to-changes-in-orientation"></a>对方向变化做出反应

Xamarin.Forms 不提供用于向应用程序通知共享代码中的方向更改的任何本机事件。 但是， [Xamarin.Essentials](~/essentials/index.md) 包含一个 `DeviceDisplay` 提供方向更改通知的 [] 类。

若要在没有的情况下检测方向，请 Xamarin.Essentials 监视的 `SizeChanged` 事件，该事件在的 `Page` 宽度或高度 `Page` 发生更改时激发。 当的宽度 `Page` 大于高度时，设备处于横向模式。 有关详细信息，请参阅 [基于屏幕方向显示图像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

或者，可以重写 [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) 中的方法，并在 `Page` 其中插入任何布局更改逻辑。 `OnSizeAllocated`只要 `Page` 分配新大小，就会调用方法，只要设备旋转，就会发生这种情况。 请注意，的基实现 `OnSizeAllocated` 执行重要的布局函数，因此在重写中调用基实现非常重要：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

如果无法执行该步骤，将导致无法正常工作的页面。

请注意，在 `OnSizeAllocated` 旋转设备时，可能会多次调用此方法。 每次更改布局会浪费资源，并可能导致闪烁。 请考虑使用页面中的实例变量来跟踪横向还是纵向方向，并在发生更改时仅重绘：

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

检测到设备方向发生变化后，你可能需要在用户界面中添加或删除其他视图，以响应可用空间的更改。 例如，考虑每个纵向平台上的内置计算器：

![纵向计算器应用程序](device-orientation-images/calculator-portrait.png)

和横向：

![横向计算器应用程序](device-orientation-images/calculator-landscape.png)

请注意，应用会通过在横向添加更多功能来利用可用空间。

## <a name="responsive-layout"></a>响应式布局

可以使用内置布局设计接口，使其在设备旋转时正常转换。 设计在响应方向变化时仍有吸引力的接口时，请考虑以下一般规则：

- **注意比率** &ndash; 当某些假设与比率有关时，方向变化可能会导致问题。 例如，在纵向屏幕垂直空间的1/3 中具有大量空间的视图可能无法适应横向垂直空间的1/3。
- **请注意绝对值** &ndash; 绝对 (像素) 值在横向上非常有意义。 如果需要绝对值，请使用嵌套布局来找出它们的影响。 例如， `TableView` `ItemTemplate` 如果项模板具有有保证的统一高度，则在中使用绝对值是合理的。

在为多个屏幕大小实现接口时，上述规则也适用，通常被视为最佳做法。 本指南的其余部分将介绍使用中的每个主要布局的响应式布局的特定示例 Xamarin.Forms 。

> [!NOTE]
> 为清楚起见，以下部分演示了如何一次只使用一种类型实现响应式布局 `Layout` 。 在实践中， `Layout` 使用 `Layout` 每个组件的简单或最直观地混合来实现所需布局通常更为简单。

### <a name="stacklayout"></a>StackLayout

请考虑以下应用程序，纵向显示：

![纵向照片应用程序](device-orientation-images/photo-stack-portrait.png)

和横向：

![横向照片应用程序](device-orientation-images/photo-stack-landscape.png)

这是通过以下 XAML 实现的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

某些 c # 用于根据设备方向更改的方向 `outerStack` ：

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

注意以下事项：

- `outerStack` 进行调整后，根据方向，将图像和控件呈现为水平或垂直堆栈，以最好地利用可用空间。

### <a name="absolutelayout"></a>AbsoluteLayout

请考虑以下应用程序，纵向显示：

![纵向照片应用程序](device-orientation-images/photo-abs-portrait.png)

和横向：

![横向照片应用程序](device-orientation-images/photo-abs-landscape.png)

这是通过以下 XAML 实现的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

注意以下事项：

- 由于页面布局的方式，无需执行过程代码来引入响应能力。
- 将 `ScrollView` 用于允许标签可见，即使屏幕的高度小于按钮和图像的固定高度之和也是如此。

### <a name="relativelayout"></a>RelativeLayout

请考虑以下应用程序，纵向显示：

![纵向照片应用程序](device-orientation-images/photo-rel-portrait.png)

和横向：

![横向照片应用程序](device-orientation-images/photo-rel-landscape.png)

这是通过以下 XAML 实现的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

注意以下事项：

- 由于页面布局的方式，无需执行过程代码来引入响应能力。
- 将 `ScrollView` 用于允许标签可见，即使屏幕的高度小于按钮和图像的固定高度之和也是如此。

### <a name="grid"></a>Grid

请考虑以下应用程序，纵向显示：

![纵向照片应用程序](device-orientation-images/photo-grid-portrait.png)

和横向：

![横向照片应用程序](device-orientation-images/photo-grid-landscape.png)

这是通过以下 XAML 实现的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以及以下过程代码来处理旋转变化：

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

注意以下事项：

- 由于页面布局的方式，有一种方法可以更改控件的网格位置。

## <a name="related-links"></a>相关链接

- [布局 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [响应式布局 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [基于屏幕方向显示图像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)