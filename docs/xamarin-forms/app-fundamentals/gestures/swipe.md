---
title: 添加轻扫手势识别器
description: 本文介绍如何识别视图上的轻扫手势。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cbd1bdd097bc205f82f98574df5c356b03769e29
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562647"
---
# <a name="add-a-swipe-gesture-recognizer"></a>添加轻扫手势识别器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

“手指在水平或垂直方向上滑过屏幕即产生轻扫手势，该手势通常用于启动内容导航。在本文中的代码示例摘自[轻扫手势](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)示例。

若要使 [`View`](xref:Xamarin.Forms.View) 识别轻扫手势，请创建 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 实例，将 [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) 属性设置为 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 枚举值（`Left`、`Right`、`Up` 或 `Down`），或者根据需要设置 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 属性、处理 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件，以及将新的笔势识别器添加到视图上的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 下面的代码示例显示附加到 [`BoxView`](xref:Xamarin.Forms.BoxView) 上的 `SwipeGestureRecognizer`：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

下面是等效 C# 代码：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 类还包括 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 属性，可根据需要设置为 `uint` 值，该值表示在独立于设备的单元中实现轻扫手势识别所需的最小轻扫距离。 此属性的默认值为 100，也就是说，将忽略任何小于 100 个独立于设备的单元的轻扫手势。

## <a name="recognizing-the-swipe-direction"></a>识别轻扫方向

在上面的示例中，[`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 属性设置为 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 枚举中的单个值。 但是，也可以将此属性设置为 `SwipeDirection` 枚举中的多个值，以便触发 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件以响应多个方向的轻扫手势。 但是，约束是单个 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 只能识别同一个轴上的轻扫手势。 因此，可以通过将 `Direction` 属性设置为 `Left` 和 `Right` 来识别水平轴上的轻扫手势：

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同样，可以通过将 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 属性设置为 `Up` 和 `Down` 来识别垂直轴上的轻扫手势：

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者，可以创建每个轻扫方向的 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 来识别每个方向的轻扫手势：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

下面是等效 C# 代码：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> 在上面的例子中，相同事件处理程序响应 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件触发。 但是，如果需要，每个 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 实例可以使用不同的事件处理程序。

## <a name="responding-to-the-swipe"></a>响应轻扫手势

[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件的事件处理程序如下例所示：

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

可以检查 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) 以确定轻扫方向，并根据需要使用自定义逻辑响应轻扫手势。 可以从事件参数的 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 属性获得轻扫方向，该属性将设置为 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 枚举中的某个值。 此外，事件参数还具有 [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) 属性，如果定义了该属性，则该属性将设置为 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 属性中的值。

## <a name="using-commands"></a>使用命令

[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 类还包括 [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) 和 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 属性。 这些属性通常用于使用模型-视图-视图模型 (MVVM) 模式的应用程序。 识别轻扫手势时，`Command` 属性定义要调用的 `ICommand`并且 `CommandParameter` 属性定义要传递给 `ICommand.` 的对象。下面的代码示例演示如何将 `Command` 属性绑定到视图模型中定义的 `ICommand`，该视图模型实例设置为页 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

等效 XAML 代码为：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` 是视图模型实例中定义的类型 `ICommand` 的属性，该视图模型实例设置为页 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)。 识别轻扫手势时，将执行 `SwipeCommand` 对象的 `Execute` 方法。 `Execute` 方法的参数是 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 属性的值。 有关命令的详细信息，请参阅[命令接口](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>创建轻扫手势容器

`SwipeContainer` 类（如下面的代码示例所示）是通用的轻扫识别类，包装在 [`View`](xref:Xamarin.Forms.View) 中，用于执行轻扫手势的识别：

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

`SwipeContainer` 类创建所有四个轻扫方向的 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 对象，并附加 `Swipe` 事件处理程序。 这些事件处理程序调用由 `SwipeContainer` 定义的 `Swipe` 事件。

下面的 XAML 代码示例演示包装 [`BoxView`](xref:Xamarin.Forms.BoxView) 的 `SwipeContainer` 类：

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

下面的代码示例演示 `SwipeContainer` 如何在 C# 页中包装 [`BoxView`](xref:Xamarin.Forms.BoxView)：

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

[`BoxView`](xref:Xamarin.Forms.BoxView) 接收到轻扫手势时，触发 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 中的 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件。 这是由 `SwipeContainer` 类处理，该类触发自己的 `Swipe` 事件。 此 `Swipe` 事件在页上进行处理。 然后可以检查 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) 以确定轻扫方向，并根据需要使用自定义逻辑响应轻扫手势。

## <a name="related-links"></a>相关链接

- [轻扫手势（示例）](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)