---
title: 从效果调用事件
description: 一种效果可以定义和调用事件，指示基础本机视图中的更改。 本文介绍如何实现低级别多点触控手指跟踪，以及如何生成发出触摸活动信号的事件。
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3bbad7f96403cb1f386b80b2923b0cdd222f9596
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555874"
---
# <a name="invoking-events-from-effects"></a>从效果调用事件

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_一种效果可以定义和调用事件，指示基础本机视图中的更改。本文介绍如何实现低级别多点触控手指跟踪，以及如何生成发出触摸活动信号的事件。_

本文中所述的效果提供对低级别触摸事件的访问权限。 这些低级别事件不能通过现有的 `GestureRecognizer` 类获得，但它们对某些类型的应用程序至关重要。 例如，一款手指触摸应用程序需要在手指在屏幕上移动时跟踪手指。 音乐键盘需要检测各个键上的按键和松开按键，以及手指从一个键滑动到另一个键的滑动。

效果非常适合多点触控手指跟踪，因为它可以附加到任何 Xamarin.Forms 元素。

## <a name="platform-touch-events"></a>平台触摸事件

iOS、Android 和通用 Windows 平台都包括一个低级别 API，允许应用程序检测触摸活动。 这些平台都能区分三种基本类型的触摸事件：

- 已按下，当手指触摸屏幕时
- 已移动，当手指触摸屏幕并移动时
- 已松开，当手指从屏幕上松开

在多点触控环境中，多个手指可同时触摸屏幕。 各种平台都包括一个应用程序可用于区分多个手指的标识 (ID) 号。

在 iOS 中，`UIView` 类定义了三个可替代的方法，`TouchesBegan`、`TouchesMoved` 和 `TouchesEnded` 对应于这三个基本事件。 [多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)一文介绍了如何使用这些方法。 但是，iOS 程序无需替代从 `UIView` 派生的类以使用这些方法。 iOS `UIGestureRecognizer` 还定义了这三个相同的方法，可以将从 `UIGestureRecognizer` 派生的类的实例附加到任何 `UIView` 对象。

在 Android 中，`View` 类定义名为 `OnTouchEvent` 的可替代方法，以处理所有触摸活动。 触摸活动的类型由文章[多点触控手指跟踪](~/android/app-fundamentals/touch/touch-tracking.md)中所述的枚举成员 `Down`、`PointerDown`、`Move`、`Up` 和 `PointerUp` 定义。 Android `View` 还定义了名为 `Touch` 的事件，该事件允许事件处理程序附加到任何 `View` 对象。

在通用 Windows 平台 (UWP) 上，`UIElement` 类定义名为 `PointerPressed`、`PointerMoved` 和 `PointerReleased` 的事件。 这些在 MSDN 上的文章[处理指针输入](/windows/uwp/input-and-devices/handle-pointer-input/)和 [`UIElement`](/uwp/api/windows.ui.xaml.uielement/) 类的 API 文档中都有介绍。

通用 Windows 平台中的 `Pointer` API 旨在统一鼠标、触控和笔输入。 为此，即使未按下鼠标按钮，当鼠标在元素之间移动时将调用 `PointerMoved` 事件。 伴随这些事件的 `PointerRoutedEventArgs` 对象具有名为 `Pointer` 的属性，该属性具有名为 `IsInContact` 的属性，该属性指示是否按下了鼠标按钮或手指是否与屏幕接触。

此外，UWP 还定义了另外两个名为 `PointerEntered` 和 `PointerExited` 的事件。 这些事件指示鼠标或手指何时从一个元素移动到另一个元素。 例如，考虑名为 A 和 B 的两个相邻元素。两个元素都安装了指针事件的处理程序。 当手指按下 A 时，调用 `PointerPressed` 事件。 当手指移动时，A 调用 `PointerMoved` 事件。 如果手指从 A 移动到 B，A 调用 `PointerExited` 事件，B 调用 `PointerEntered` 事件。 如果松开手指，B 调用 `PointerReleased` 事件。

iOS 和 Android 平台与 UWP 不同：即使手指移动到不同视图，当手指触摸视图并继续获取所有触摸活动时，视图将首先调用 `TouchesBegan` 或 `OnTouchEvent`。 如果应用程序捕获指针，则 UWP 的行为可能类似：在 `PointerEntered` 事件处理程序中，元素调用 `CapturePointer`，然后获取来自该手指的所有触摸活动。

UWP 方法证明对某些类型的应用程序非常有用，例如音乐键盘。 每个键可以处理该键的触摸事件，并使用 `PointerEntered` 和 `PointerExited` 事件检测手指何时从一个键滑动到另一个键。

出于此原因，本文中所述的触控跟踪效果实现 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>触控跟踪效果 API

[触控跟踪效果演示](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)示例包含实现低级别触控跟踪的类（和枚举）。 这些类型属于命名空间 `TouchTracking`，以单词 `Touch` 开头。 TouchTrackingEffectDemos .NET Standard 库项目包括触摸事件类型的 `TouchActionType` 枚举：

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

所有平台还都包括一个事件以指示已取消触摸事件。

.NET Standard 库中的 `TouchEffect` 类派生自 `RoutingEffect`，并定义了一个名为 `TouchAction` 的事件和一个名为 `OnTouchAction` 的方法，该方法调用 `TouchAction` 事件：

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

此外，请注意 `Capture` 属性。 若要捕获触摸事件，应用程序必须在 `Pressed` 事件之前将此属性设置为 `true`。 否则，触摸事件的行为将和通用 Windows 平台中的行为类似。

.NET Standard 库中的 `TouchActionEventArgs` 类包含附带每个事件的所有信息：

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

应用程序可以使用 `Id` 属性跟踪单个手指。 请注意 `IsInContact` 属性。 此属性对于 `Pressed` 事件始终为 `true`，对于 `Released` 事件始终为 `false`。 此属性还对于 iOS 和 Android 中的 `Moved` 事件始终为 `true`。 当程序在桌面上运行且在未按下按钮时移动鼠标指针，`IsInContact` 属性对于通用 Windows 平台中的 `Moved` 事件可能为 `false`。

可以在解决方案的 .NET Standard 库项目中包含该文件，并在任何 Xamarin.Forms 元素的 `Effects` 集合中添加实例，从而在自己的应用程序中使用 `TouchEffect` 类。 将处理程序附加到 `TouchAction` 事件以获取触摸事件。

若要在自己的应用程序中使用 `TouchEffect`，还需要 TouchTrackingEffectDemos 解决方案中包含的平台实现。

## <a name="the-touch-tracking-effect-implementations"></a>触控跟踪效果实现

下面将介绍 `TouchEffect` 的 iOS、Android 和 UWP 实现，从最简单的实现 (UWP) 开始，到 iOS 实现结束，因为它在结构上比其他实现更复杂。

### <a name="the-uwp-implementation"></a>UWP 实现

`TouchEffect` 的 UWP 实现是最简单的。 像往常一样，类派生自 `PlatformEffect` 并包含两个程序集属性：

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

`OnAttached` 替代将一些信息保存为字段并将处理程序附加到所有指针事件：

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

`OnPointerPressed` 处理程序通过调用 `CommonHandler` 方法中的 `onTouchAction` 字段，以调用效果事件：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` 还会检查 .NET Standard 库中的效果类中的 `Capture` 属性的值，且如果值为 `true` 则调用 `CapturePointer`。

 其他 UWP 事件处理程序更简单：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>Android 实现

Android 和 iOS 实现必然是更复杂的，因为当手指从一个元素移动到另一个元素时它们必须实现 `Exited` 和 `Entered` 事件。 这两个实现结构类似。

Android `TouchEffect` 类为 `Touch` 事件安装处理程序：

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

该类还定义了两个静态字典：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

每次调用 `OnAttached` 替代时，`viewDictionary` 都会获取一个新的输入：

```csharp
viewDictionary.Add(view, this);
```

从 `OnDetached` 中的字典中删除该输入。 `TouchEffect` 的每个实例都与效果附加到的特定视图相关联。 静态字典允许任何 `TouchEffect` 实例枚举所有其他视图及其对应的 `TouchEffect` 实例。 对于将事件从一个视图传输到另一个视图，这是必需的操作。

Android 将分配一个 ID 代码到触摸事件，该代码允许应用程序跟踪单个手指。 `idToEffectDictionary` 使用 `TouchEffect` 实例来关联此 ID 代码。 当手指按下 `Touch` 处理程序时，会将一个项添加到此字典中：

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

当手指从屏幕上松开时，该项将从 `idToEffectDictionary` 中删除。 `FireEvent` 方法只是累积调用 `OnTouchAction` 方法所需的所有信息：

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

所有其他触摸类型以两种不同的方法进行处理：如果 `Capture` 属性为 `true`，则触摸事件只是简单的 `TouchEffect` 信息的转换。 当 `Capture` 为 `false` 时，它会变得更复杂，因为触摸事件可能需要从一个视图移动到另一个视图。 这由 `CheckForBoundaryHop` 方法责任，在移动事件期间将调用该方法。 此方法充分利用了这两个静态字典。 它通过 `viewDictionary` 枚举以确定手指当前正在触摸的视图，并使用 `idToEffectDictionary` 来存储与特定 ID 相关联的当前 `TouchEffect` 实例（当前视图）：

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

如果 `idToEffectDictionary` 中发生了更改，则该方法可能会为 `Exited` 和 `Entered` 调用 `FireEvent` 以从一个视图传输到另一视图。 但是，手指可能已移动到视图占用的区域而没有附加 `TouchEffect`，或者从该区域移动到附加效果的视图。

访问视图时，请注意 `try` 和 `catch` 块。 在导航到该页面然后导航回主页的页面中，未调用 `OnDetached` 方法，并且项保留在 `viewDictionary` 中，但 Android 认为它们已丢失。

### <a name="the-ios-implementation"></a>iOS 实现

iOS 实现和 Android 实现类似，不同的是 iOS `TouchEffect` 类必须实例化 `UIGestureRecognizer` 的派生。 这是 iOS 项目中名为 `TouchRecognizer` 的类。 此类维护两个存储 `TouchRecognizer` 实例的静态字典：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

`TouchRecognizer` 类的大部分结构类似于 Android `TouchEffect` 类。

> [!IMPORTANT]
> `UIKit` 中的许多视图在默认情况下都不会启动触摸。 可以通过将 `view.UserInteractionEnabled = true;` 添加到 iOS 项目中的 `TouchEffect` 类中 `OnAttached` 重写来启用触摸。 应在获得与附加到效果的元素相对应的 `UIView` 后执行此操作。

## <a name="putting-the-touch-effect-to-work"></a>使触控效果发挥作用

[TouchTrackingEffectDemos](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) 程序包含五个页面，用于测试常见任务的触控跟踪效果。

BoxView 拖动页面允许将 `BoxView` 元素添加到 `AbsoluteLayout`然后在屏幕上拖动它们。 [XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml)实例化两个 `Button` 视图，用于将 `BoxView` 元素添加到 `AbsoluteLayout` 并清除 `AbsoluteLayout`。

[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs) 中向 `AbsoluteLayout` 添加新 `BoxView` 的方法也向 `BoxView` 添加了一个 `TouchEffect` 对象，并为效果附加了一个事件处理程序：

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

`TouchAction` 事件处理程序处理所有 `BoxView` 元素的所有触摸事件，但它需要小心执行：它不允许在单个 `BoxView` 上使用两个手指，因为程序仅实现拖动，而两个手指将相互干扰。 出于此原因，页会为当前跟踪的每个手指定义一个嵌入的类：

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

`dragDictionary` 包含当前被拖动的每个 `BoxView` 的输入。

`Pressed` 触摸操作将一个项添加到字典中，`Released` 操作会将其删除。 `Pressed` 逻辑必须检查字典中是否已存在 `BoxView` 的项。 如果存在，则 `BoxView` 已被拖动，新事件是同一 `BoxView` 上的第二根手指。 对于 `Moved` 和 `Released` 操作，事件处理程序必须检查字典是否具有该 `BoxView` 的输入，并且拖动的 `BoxView` 的触摸 `Id` 属性是否与字典输入中的属性匹配：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

`Pressed` 逻辑将 `TouchEffect` 对象的 `Capture` 属性设置为 `true`。 这具有将该手指的所有后续事件传递到同一事件处理程序的效果。

`Moved` 逻辑通过更改 `LayoutBounds` 附加属性来移动 `BoxView`。 事件参数的 `Location` 属性和正在拖动的 `BoxView` 始终是相对的，并且如果以恒定速率拖动 `BoxView`，则该连续事件的 `Location` 属性将大致相同。 例如，如果手指按下其中心的 `BoxView`，则 `Pressed` 操作存储 (50, 50) 的 `PressPoint` 属性，对于后续事件，该属性保持不变。 如果以恒定速率对角拖动 `BoxView`，则 `Moved` 操作期间的后续 `Location` 属性的值可能为 (55, 55)，在这种情况下，`Moved` 逻辑会向 `BoxView` 水平和垂直位置添加 5。 这样可以移动 `BoxView` 以便其中心再次直接置于手指下。

可以使用不同手指同时移动多个 `BoxView` 元素。

[![BoxView 拖动页的三重屏幕截图](touch-tracking-images/boxviewdragging-small.png)](touch-tracking-images/boxviewdragging-large.png#lightbox "BoxView 拖动页的三重屏幕截图")

### <a name="subclassing-the-view"></a>对视图进行子类化

通常情况下，Xamarin.Forms 元素更善于处理其自身的触摸事件。 可拖动的 BoxView 拖动页与 BoxView 拖动页的功能相同，但用户拖动的元素是派生自 `BoxView` 的 [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) 类的实例 ：

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

构造函数创建并附加 `TouchEffect`，且当对象首次实例化时设置 `Capture` 属性。 因为其自身存储和每个手指关联的 `isBeingDragged`、`pressPoint` 和 `touchId` 值，因此不需要字典。 `Moved` 处理会改变 `TranslationX` 和 `TranslationY` 属性，因此即使 `DraggableBoxView` 的父级不是 `AbsoluteLayout`，逻辑也会起作用。

### <a name="integrating-with-skiasharp"></a>与 SkiaSharp 集成

接下来的两个演示需要图形，并且使用 SkiaSharp 实现此目的。 开始学习这些示例前，建议先了解有关[在 Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。 前两篇文章（“SkiaSharp 绘制基础知识”和“SkiaSharp 线和路径”）涵盖此处需要的所有内容。

椭圆绘图页允许通过在屏幕上滑动手指来绘制一个椭圆。 具体取决于如何移动手指，可以自左上到右下，或是从任何其他的对角来绘制一个椭圆。 绘制的椭圆颜色和不透明度随机。

[![椭圆绘制页的三重屏幕截图](touch-tracking-images/ellipsedrawing-small.png)](touch-tracking-images/ellipsedrawing-large.png#lightbox "椭圆绘制页的三重屏幕截图")

然后触摸其中一个椭圆，可将其拖动到其他位置。 此操作要求一种名为“命中测试”的技术，该技术涉及在特定的点搜索图形对象。 SkiaSharp 椭圆不是 Xamarin.Forms 元素，因此它们不能自己执行 `TouchEffect` 处理。 `TouchEffect` 必须应用于整个 `SKCanvasView` 对象。

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) 文件在单元格 `Grid` 中实例化 `SKCanvasView`。 `TouchEffect` 对象已附加到 `Grid`：

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

在 Android 和通用 Windows 平台中，`TouchEffect` 可以直接附加到 `SKCanvasView`，但在 iOS 上不行。 请注意，`Capture` 属性设置为 `true`。

SkiaSharp 呈现的每个椭圆都由类型 `EllipseDrawingFigure` 的对象表示：

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

当程序处理触摸输入时使用 `StartPoint` 和 `EndPoint` 属性；`Rectangle` 属性用于绘制椭圆。 当拖动椭圆时 `LastFingerLocation` 属性发挥作用，且 `IsInEllipse` 方法有助于命中测试。 如果点在椭圆内则方法返回 `true`。

[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs) 维护三个集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure` 字典包含 `completedFigures` 集合的子集。 SkiaSharp `PaintSurface` 事件处理程序只是在 `completedFigures` 和 `inProgressFigures` 集合中呈现对象：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

触摸处理中最棘手的部分是 `Pressed` 处理。 这是执行命中测试的时机，但如果代码在用户的手指下检测到椭圆，则只有当椭圆当前没有被另一个手指拖动时才能拖动该椭圆。 如果用户的手指下没有椭圆，则代码会开始绘制新椭圆：

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

其他 SkiaSharp 示例位于手指绘制页中。 可以从两个 `Picker` 视图中选择笔划颜色和宽度，然后使用一个或多个手指进行绘制：

[![手指绘制页的三重屏幕截图](touch-tracking-images/fingerpaint-small.png)](touch-tracking-images/fingerpaint-large.png#lightbox "手指绘制页的三重屏幕截图")

此示例还需要一个单独的类来表示屏幕上绘制的每一行：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

`SKPath` 对象用于呈现每行。 [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) 文件维护这些对象的两个集合，一个用于当前正在绘制的这些折线，另一个用于已完成绘制的折线：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed` 正在创建一个新的 `FingerPaintPolyline`，在路径对象上调用 `MoveTo` 来存储初始点，并将该对象添加到 `inProgressPolylines` 字典中。 `Moved` 正在使用新的手指位置调用路径对象上的 `LineTo`，`Released` 正在将已完成的折线从 `inProgressPolylines` 传输到 `completedPolylines`。 再次重申，实际的 SkiaSharp 绘制代码是相对简单的：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>跟踪视图对视图触摸

在创建 `TouchEffect` 或发生 `Pressed` 事件时，前面所有的示例都将 `TouchEffect` 的 `Capture` 属性设置为 `true`。 这可确保同一元素接收所有和首次按下视图的手指相关联的事件。 最后一个示例未将 `Capture` 设置为 `true`。 当接触屏幕的手指从一个元素移动到另一个时，这将导致不同的行为。 手指移动的元素接收 `Type` 属性设置为 `TouchActionType.Exited` 的事件，第二个元素接收 `Type` 设置为 `TouchActionType.Entered` 的事件。

此类型的触摸处理对于音乐键盘非常有用。 键应该能够检测到按下的时间，以及当手指从一个键滑动到另一个键的时间。

无提示键盘页定义派生自 [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs) 的小 [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) 和 [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs) 类，它派生自 `BoxView`。

`Key` 类已准备好在实际音乐程序中使用。 它定义了名为 `IsPressed` 和 `KeyNumber` 的公共属性，旨在将其设置为由 MIDI 标准建立的密钥代码。 `Key` 还定义了名为 `StatusChanged` 的事件，该事件将在 `IsPressed` 属性更改时调用。

单个密钥允许使用多根手指。 因此，`Key` 类维护当前触摸该键的所有手指的触摸 ID 号的 `List`：

```csharp
List<long> ids = new List<long>();
```

仅当 `Entered` 事件的 `IsInContact` 属性为 `true` 时，`TouchAction` 事件处理程序将 ID 添加到 `Pressed` 事件类型和 `Entered` 类型的 `ids` 列表中。 ID 已从 `Released` 或 `Exited` 事件的 `List` 中删除：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

`AddToList` 和 `RemoveFromList` 方法检查 `List` 是否在空和非空之间发生了更改，如果是，则调用 `StatusChanged` 事件。

`WhiteKey` 和 `BlackKey` 的各种元素在页的 [XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml)中排列，以便于当手机保持在横向模式时显示效果最佳：

[![无提示键盘页的三重屏幕截图](touch-tracking-images/silentkeyboard-small.png)](touch-tracking-images/silentkeyboard-large.png#lightbox "无提示键盘页的三重屏幕截图")

如果在键之间滑动手指，可以通过颜色的细微变化看见触摸事件从一个键传输到另一个键。

## <a name="summary"></a>总结

本文演示了如何在效果中调用事件，以及如何编写和使用能够实现低级别多点触控处理的效果。

## <a name="related-links"></a>相关链接

- [iOS 中的多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Android 中的多点触控手指跟踪](~/android/app-fundamentals/touch/touch-tracking.md)
- [触控跟踪效果（示例）](/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)