---
title: Xamarin.iOS 性能
description: 本文档介绍的技术可用于提升 Xamarin.iOS 应用程序的性能和内存使用情况。
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/29/2016
ms.openlocfilehash: 06d413b2bf07df38f78e93af027de2c7dc0badcc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436774"
---
# <a name="xamarinios-performance"></a>Xamarin.iOS 性能

应用程序性能差表现在许多方面。 这会造成应用程序看起来无响应，导致滚动缓慢，还可降低电池寿命。 但是，优化性能不止需要实现高效的代码。 还必须考虑用户对应用程序性能的体验。 例如，确保操作执行不会妨碍用户执行其他活动，这有助于改进用户的体验。

本文档介绍的技术可用于提升 Xamarin.iOS 应用程序的性能和内存使用情况。

> [!NOTE]
> 阅读本文之前，首先应阅读[跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中讨论了非平台特定方法，可用于改善使用 Xamarin 平台生成的应用程序的内存使用情况和性能。

## <a name="avoid-strong-circular-references"></a>避免强循环引用

在某些情况下，可创建强引用循环以防止垃圾回收器回收对象的内存。 例如，考虑这种情况：[`NSObject`](xref:Foundation.NSObject) 派生的子类（如继承自 [`UIView`](xref:UIKit.UIView) 的类）添加到了 `NSObject` 派生的容器中，并从 Objective-C 进行了强引用，如以下代码示例所示：

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

此代码创建 `Container` 实例时，C# 对象具有对 Objective-C 对象的强引用。 同样，`MyView` 示例也将具有对 Objective-C 对象的强引用。

此外，对 `container.AddSubview` 的调用将增加未托管 `MyView` 实例上的引用数。 出现此情况时，Xamarin.iOS 运行时会创建一个 `GCHandle` 实例以使 `MyView` 对象在托管代码中处于活动状态，因为无法保证所有托管对象保留对该对象的引用。 从托管代码的角度看，如果 `MyView` 对象不适用于 `GCHandle`，则将在 [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) 调用后将该对象回收。

未托管的 `MyView` 对象具有指向托管对象的 `GCHandle`，称为强链接**。 托管对象包含一个对 `Container` 实例的引用。 反之，`Container` 实例则有一个对 `MyView` 对象的托管引用。

在所含对象保留对其容器的链接的情况下，可通过多种选项处理循环引用：

- 通过将容器的链接设置为 `null` 来手动中断循环。
- 从容器中手动删除所含对象。
- 调用对象上的 `Dispose`。
- 避免循环引用保持对容器的弱引用。 详细了解弱引用。

### <a name="using-weakreferences"></a>使用弱引用

防止循环的一种方法是从子级到父级使用弱引用。 例如，前述的代码可以编写成：

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

在这里，包含的对象不会使父级处于活动状态。 但是，父级通过对 `container.AddSubView` 的调用使子级处于活动状态。

使用委托或数据源模式的 iOS API 也采用这种做法。其中，对等类包含实现代码（例如，在 [`Delegate`](xref:UIKit.UITableView.Delegate*) 类中
设置 [`DataSource`](xref:UIKit.UITableView.DataSource*) 属性
或 [`UITableView`](xref:UIKit.UITableView) 时）。

对于纯粹为了实现协议而创建的类（例如，[`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource)），可以直接在类中实现接口并替代方法，再向 `this` 分配 `DataSource` 属性，而不是创建子类。

#### <a name="weak-attribute"></a>弱特性

[Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) 采用了 `[Weak]` 特性。 与 `WeakReference <T>` 类似，`[Weak]` 可用于中断[强循环引用](#avoid-strong-circular-references)，但使用的代码数量更少。

请看以下使用 `WeakReference <T>` 的代码：

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

使用 `[Weak]` 的等效代码更加简洁：

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

以下是在[委托](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html)模式上下文中使用 `[Weak]` 的另一个示例：

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>释放具有强引用的对象

如果存在强引用且难以删除依赖项，请使用 `Dispose` 方法清除父指针。

对于容器，请替代 `Dispose` 方法以删除所含对象，如以下代码示例所示：

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

对于保留对其父级的强引用的子对象，请在 `Dispose` 实现中清除对父级的引用：

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

有关释放强引用的详细信息，请参阅[释放 IDisposable 资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable)。
另外，此博客文章也对其进行了适当探讨：[Xamarin.iOS, the garbage collector and me](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me)（Xamarin.iOS、垃圾回收器和我）。

### <a name="more-information"></a>详细信息

有关详细信息，请参阅 Cocoa With Love 上的 [Rules to Avoid Retain Cycles](https://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html)（避免保留周期的规则）、StackOverflow 上的 [Is this a bug in MonoTouch GC](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc)（这是不是 MonoTouch GC 中的一个 bug）以及 StackOverflow 上的 [Why can't MonoTouch GC kill managed objects with refcount > 1?](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1)（MonoTouch GC 为何不能将托管对象的引用计数缩减到大于 1？）。

## <a name="optimize-table-views"></a>优化表视图

用户期望平滑滚动并快速加载 [`UITableView`](xref:UIKit.UITableView) 实例。 但是，当单元格包含深度嵌套的视图层次结构或包含复杂布局时，滚动性能会降低。 但是，可以使用一些方法避免出现不佳的 `UITableView` 性能：

- 重用单元格。 有关详细信息，请参阅[重用单元格](#reuse-cells)。
- 减少子视图数。
- 缓存检索自 Web 服务的单元格内容。
- 如果这些行不尽相同，则缓存任何行的高度。
- 使单元格和任何其他视图处于不透明状态。
- 避免图像缩放和渐变。

结合使用这些方法有助于保持 [`UITableView`](xref:UIKit.UITableView) 实例的平滑滚动。

### <a name="reuse-cells"></a>重用单元格

在 [`UITableView`](xref:UIKit.UITableView) 中显示数百个行时，若一次仅在屏幕上显示其中一小部分，创建数百个 [`UITableViewCell`](xref:UIKit.UITableViewCell) 对象则是在浪费内存。 相反，应仅将屏幕上显示单元格加载到内存中，同时将**内容**加载到这些重用单元格中。 这可以防止实例化数百个其他对象，从而节省时间和内存。

因此，当某一单元格从屏幕上消失后，可将其视图放到队列中以供重复使用，如以下代码示例所示：

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

用户滚动鼠标时，[`UITableView`](xref:UIKit.UITableView) 会调用 `GetCell` 替代以请求显示新视图。 此替代随后调用 [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) 方法，如果单元格可供重复使用，则将返回该方法。

有关详细信息，请参阅[用数据填充表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)中的[单元格重用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。

## <a name="use-opaque-views"></a>使用不透明视图

确保未定义透明度的所有视图均具有其 [`Opaque`](xref:UIKit.UIView.Opaque) 属性集。 这可确保视图通过绘图系统以最佳方式呈现。 当视图嵌入 [`UIScrollView`](xref:UIKit.UIScrollView) 中，或者视图是复杂动画的一部分时，这一点尤为重要。 否则，绘图系统会将视图和其他内容合并，这可能严重影响性能。

## <a name="avoid-fat-xibs"></a>避免 fat XIB

尽管 XIB 在大多数情况下已替换为情节提要，但某些情况下仍在使用 XIB。 XIB 加载到内存中时，其所有内容也会加载到内存中，包括任何图像。 如果 XIB 包含不会立即使用的视图，则是在浪费内存。 因此，使用 XIB 时，请确保每个视图控制器中仅有一个 XIB，如果可能，请将视图控制器的视图层次结构划分到单独的 XIB 中。

## <a name="optimize-image-resources"></a>优化图像资源

图像是应用程序使用的一些最昂贵的资源，通常以高分辨率捕获。 因此，在 [`UIImageView`](xref:UIKit.UIImageView) 中显示来自应用程序包的图像时，请确保图像和 `UIImageView` 的大小相同。 在运行时缩放图像可能是一项开销巨大的操作，将 `UIImageView` 嵌入 [`UIScrollView`](xref:UIKit.UIScrollView) 中时尤为明显。

有关详细信息，请参阅[跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南中的[优化图像资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

## <a name="test-on-devices"></a>在设备上进行测试

尽早开始在物理设备上部署和测试应用程序。 模拟器与设备的行为和限制不完全匹配，因此务必尽早在实际设备方案中测试应用。

当模拟器不以任何方式模拟物理设备的内存或 CPU 限制时尤其如此。

## <a name="synchronize-animations-with-the-display-refresh"></a>通过显示刷新同步动画

游戏往往会紧凑循环地运行游戏逻辑和更新屏幕。 典型的帧速率介于每秒 30 到 60 帧之间。 一些开发人员认为每秒应尽可能多次更新屏幕，以便组合游戏模拟和屏幕更新，并且可能倾向于每秒超出 60 帧。

但是，显示服务器每秒最多执行 60 次屏幕更新。 因此，尝试以超出此限制的速度更新屏幕可能出现屏幕断层和轻微抖动现象。 最好的办法是结构化代码，以使屏幕更新与显示更新同步进行。 为此，可使用 [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink) 类，该类是一个适用于可视化效果和以每秒 60 帧速率运行的游戏的计时器。

## <a name="avoid-core-animation-transparency"></a>避免核心动画透明度

避免核心动画透明度可提高位图合成性能。 通常，请避免使用透明层和经过模糊处理的边框（如果可以）。

## <a name="avoid-code-generation"></a>避免代码生成

必须避免使用 `System.Reflection.Emit` 或动态语言运行时** 动态生成代码，因为 iOS 内核会阻止动态代码执行。

## <a name="summary"></a>总结

本文介绍和讨论了用于提高使用 Xamarin.iOS 所生成应用程序的性能的方法。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。

## <a name="related-links"></a>相关链接

- [跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)