---
title: Xamarin.iOS API 设计
description: 用于构建 Xamarin.iOS API 以及这些 API 如何关联到 Objective-C 的指导原则。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4ba1daeb94706efc9d27136c6bd4b4d13e774689
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437209"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API 设计

除了属于 Mono 的核心基类库以外，[Xamarin.iOS](~/ios/index.yml) 还附带了各种 iOS API 的绑定，以便开发人员能够通过 Monoto 创建本机 iOS 应用程序。

Xamarin.iOS 的核心有一个互操作引擎，它将 C# 环境与 Objective-C 环境桥接起来，并且还有基于 C 的 iOS API 的绑定，例如 CoreGraphics 和 [OpenGL ES](#opengles)。

与 Objective-C 代码通信的低级别运行时位于 [MonoTouch.ObjCRuntime](#objcruntime) 中。 在此基础上，提供了 [Foundation](#foundation)、CoreFoundation 和 [UIKit](#uikit) 的绑定。

## <a name="design-principles"></a>设计原理

下面介绍我们对 Xamarin.iOS 绑定的一些设计原理（也适用于 Xamarin.Mac，即 macOS 上 Objective-C 的 Mono 绑定）：

- 遵循[框架设计准则](/dotnet/standard/design-guidelines)
- 允许开发人员对 Objective-C 类设置子类：

  - 从现有类派生
  - 调用要链接的基础构造函数
  - 替代方法应使用 C# 的替代系统完成
  - 子类应与 C# 标准构造一起使用

- 不要向开发人员公开 Objective-C 选择器
- 提供一种机制来调用任意 Objective-C 库
- 轻松完成常见的 Objective-C 任务，并使困难的 Objective-C 任务变为可能
- 将 Objective-C 属性公开为 C# 属性
- 公开强类型 API：

  - 提高类型安全性
  - 最大限度地减少运行时错误
  - 获取有关返回类型的 IDE IntelliSense
  - 允许用于 IDE 弹出文档

- 建议对 API 进行 IDE 浏览：

  - 例如，无需公开弱类型数组，如下所示：

    ```objc
    NSArray *getViews
    ```

    公开强类型，如下所示：

    ```csharp
    NSView [] Views { get; set; }
    ```

    这为 Visual Studio for Mac 提供了在浏览 API 时执行自动完成的功能，使所有 `System.Array` 操作都可用于返回值，并允许返回值参与 LINQ。

- 本机 C# 类型：

  - [`NSString` 成为 `string`](~/ios/internals/api-design/nsstring.md)
  - 将应该已经枚举的 `int` 和 `uint` 参数转化为 C# 枚举和具有 `[Flags]` 属性的 C# 枚举
  - 将数组公开为强类型数组，而不是中性类型的 `NSArray` 对象。
  - 对于事件和通知，请为用户提供以下选项之一：

    - 默认情况下为强类型版本
    - 高级用例的弱类型版本

- 支持 Objective-C 委托模式：

  - C# 事件系统
  - 将 C# 委托（lambda、匿名方法和 `System.Delegate`）向 Objective-C API 公开为块

### <a name="assemblies"></a>程序集

Xamarin.iOS 包含一些构成 Xamarin.iOS 配置文件的程序集。 [程序集](~/cross-platform/internals/available-assemblies.md)页面包含更多信息。

### <a name="major-namespaces"></a>主要命名空间

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](xref:ObjCRuntime) 命名空间允许开发人员将 C# 环境与 Objective-C 环境桥接起来。
这是一种新的绑定，基于 Cocoa# 和 Gtk# 的体验专为 iOS 设计。

#### <a name="foundation"></a>Foundation

[Foundation](xref:Foundation) 命名空间提供了基本数据类型，这些数据类型旨在与作为 iOS 的一部分的 Objective-C Foundation 框架进行互操作，Foundation 命名空间是 Objective-C 中面向对象编程的基础。

Xamarin.iOS 使用 C# 镜像 Objective-C 中类的层次结构。 例如，可通过 [Foundation.NSObject](xref:Foundation.NSObject) 从 C# 使用 Objective-C 基类 NSObject。

虽然此命名空间提供基础 Objective-C Foundation 类型的绑定，但在少数情况下，我们已将基础类型映射到 .NET 类型。 例如：

- 运行时不处理 NSString 和 [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，而是在整个 API 中将它们公开为 C# [字符串](xref:System.String)和强类型[数组](xref:System.Array)。

- 此处公开了各种帮助程序 API，使开发人员能够绑定第三方 Objective-C API、其他 iOS API 或当前未由 Xamarin.iOS 绑定的 API。

有关绑定 API 的更多详细信息，请参阅 [Xamarin.iOS 绑定生成器](~/cross-platform/macios/binding/binding-types-reference.md)部分。

##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject) 类型是所有 Objective-C 绑定的基础。 Xamarin.iOS 类型从 iOS CocoaTouch API 镜像两个类型的类：C 类型（通常称为 CoreFoundation 类型）和 Objective-C 类型（这些类型都派生自 NSObject 类）。

对于每个镜像非托管类型的类型，可以通过 [Handle](xref:Foundation.NSObject.Handle) 属性获取本机对象。

虽然 Mono 将为所有对象提供垃圾回收，但 `Foundation.NSObject` 会实现 [System.IDisposable](xref:System.IDisposable) 接口。 这意味着，你可以显式释放任意给定 NSObject 的资源，而无需等待垃圾回收器启动。 当使用繁重的 NSObject（例如，可能包含指向大型数据块的指针的 UIImages）时，这一点非常重要。

如果你的类型需要执行确定性终止，请重写 [NSObject.Dispose(bool) 方法](xref:Foundation.NSObject.Dispose(System.Boolean))。要释放的参数为“bool disposing”，如果设置为 true，则表示正在调用 Dispose 方法，因为用户在对象上显式调用了 Dispose ()。 如果该值为 false，则表示正在从终结器线程上的终结器调用 Dispose(bool disposing) 方法。

##### <a name="categories"></a>类别

从 Xamarin.iOS 8.10 开始，可以从 C# 创建 Objective-C 类别。

这是使用 `Category` 属性完成的，指定要作为参数扩展到属性的类型。 例如，下面的示例将扩展 NSString。

```csharp
[Category (typeof (NSString))]
```

每个 category 方法使用的是使用 `Export` 属性将方法导出到 Objective-C 的正常机制：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有托管扩展方法都必须是静态的，但可以在 C# 中使用扩展方法的标准语法来创建 Objective-C 实例方法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

并且扩展方法的第一个参数将是在其上调用方法的实例。

完整示例：

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

此示例将在 NSString 类中添加一个本机 toUpper 实例方法，该方法可从 Objective-C 中调用。

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

这在某些情况下很有用，比如将方法添加到基本代码中的一组完整的类，例如，这会使所有 `UIViewController` 实例报告它们可以旋转：

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute 是一个自定义属性，用于在处理应用程序以减小其大小时，通知 Xamarin.iOS 部署工具 mtouch 保留某个类型或类型的成员。

每个未被应用程序静态链接的成员都可能被删除。 因此，此属性用于标记未静态引用但应用程序仍然需要的成员。

例如，如果你动态实例化类型，则需要保留类型的默认构造函数。 如果你使用 XML 序列化，则需要保留类型的属性。

你可以在类型的每个成员上或类型本身应用此属性。 如果想要保留整个类型，则可以在类型上使用语法 [Preserve (AllMembers = true)]。

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit) 命名空间包含到所有以 C# 类形式构成 CocoaTouch 的 UI 组件的一对一映射。 已将 API 修改为遵循 C# 语言中使用的约定。

对常见操作提供 C# 委托。 有关详细信息，请参阅[委托](#delegates)部分。

#### <a name="opengles"></a>OpenGLES

对于 OpenGLES，我们将分发 [OpenTK](https://opentk.net/) API 的[修改版本](xref:OpenTK)，这是一个面向对象的 OpenGL 绑定，已修改为使用 CoreGraphics 数据类型和结构，并且只公开在 iOS 上可用的功能。

OpenGLES 1.1 功能可通过 [ES11.GL 类型](xref:OpenTK.Graphics.ES11.GL)提供。

OpenGLES 2.0 功能可通过 [ES20.GL 类型](xref:OpenTK.Graphics.ES20.GL)提供。

OpenGLES 3.0 功能可通过 [ES30.GL 类型](xref:OpenTK.Graphics.ES30.GL)提供。

### <a name="binding-design"></a>绑定设计

Xamarin.iOS 并非仅绑定到基础 Objective-C 平台。 它扩展了 .NET 类型系统和调度系统，以更好地融合 C# 和 Objective-C。

正如 P/Invoke 是一种用于在 Windows 和 Linux 上调用本机库的有用工具，或如 IJW 支持可用于 Windows 上的 COM 互操作一样，Xamarin.iOS  将扩展运行时以支持将 C# 对象绑定到 Objective-C 对象。

对于创建 Xamarin.iOS 应用程序的用户来说，以下几节中的讨论并不是必需的，但它将帮助开发人员了解如何完成操作并在创建更复杂的应用程序时提供帮助。

#### <a name="types"></a>类型

如果合理，将向通用 C# 公开 C# 类型，而不是低级别的 Foundation 类型。  这意味着 [API 使用 C#“string”类型而不是 NSString](~/ios/internals/api-design/nsstring.md)，并使用强类型 C# 数组而不是公开 NSArray。

通常，在 Xamarin.iOS 和 Xamarin.Mac 设计中，不会公开基础 `NSArray` 对象。 相反，运行时会自动将 `NSArray` 转换为某些 `NSObject` 类的强类型数组。 因此，Xamarin.iOS 不会公开弱类型方法（如 GetViews）来返回 NSArray：

```csharp
NSArray GetViews ();
```

相反，绑定会公开强类型返回值，如下所示：

```csharp
UIView [] GetViews ();
```

`NSArray` 中公开了几种方法，在个别情况下，你可能想要直接使用 `NSArray`，但不建议在 API 绑定中使用。

此外，在 Classic API 中，不从 CoreGraphics API 公开 `CGRect`、`CGPoint` 和 `CGSize`，而是将它们替换为 `System.Drawing` 实现 `RectangleF`、`PointF` 和 `SizeF`，因为它们可帮助开发人员保留使用 OpenTK 的现有 OpenGL 代码。 使用新的 64 位 Unified API 时，应该使用 CoreGraphics API。

#### <a name="inheritance"></a>继承

Xamarin.iOS API 设计允许开发人员使用派生类上的“override”关键字来扩展本机 Objective-C 类型（这与扩展 C# 类型的方式相同），并使用“base”C# 关键字链接到基础实现。

此设计允许开发人员避免在开发过程中处理 Objective-C 选择器，因为整个 Objective-C 系统都已包装在 Xamarin.iOS 库中。

#### <a name="types-and-interface-builder"></a>类型和 Interface Builder

创建作为 Interface Builder 创建的类型的实例的 .NET 类时，需要提供采用单个 `IntPtr` 参数的构造函数。
这对于将托管对象实例与非托管对象绑定在一起是必需的。
此代码由一行组成，如下所示：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>委托

对于每种语言中的字词委托，Objective-C 和 C# 具有不同的含义。

在 Objective-C 环境中，以及你将在线找到的有关 CocoaTouch 的文档中，委托通常是将响应一组方法的类的实例。 这与 C# 接口非常类似，不同之处在于方法并非始终是必需的。

这些委托在 UIKit 和其他 CocoaTouch API 中发挥重要作用。 它们用于完成各种任务：

- 向代码提供通知（类似于 C# 或 Gtk+ 中的事件交付）。
- 实现数据可视化效果控件的模型。
- 驱动控件的行为。

编程模式旨在最大程度地减少创建派生类以改变控件的行为。 此解决方案在本质上与其他 GUI 工具包多年来所做的操作非常相似：Gtk 的信号、Qt 槽、Winforms 事件、WPF/Silverlight 事件等。 为了避免具有数百个接口（每个操作一个），或者要求开发人员实现太多不需要的方法，Objective-C 支持可选的方法定义。 这不同于需要实现所有方法的 C# 接口。

在 Objective-C 类中，你将看到使用此编程模式的类公开了一个属性（通常称为 `delegate`），这是实现接口的必需部分和零个或更多可选部分所必需的。

在 Xamarin.iOS 中，提供了绑定到这些委托的三个互相排斥的机制：

1. [通过事件](#via-events)。
2. [通过 `Delegate` 属性进行强类型化](#strongly-typed-via-a-delegate-property)
3. [通过 `WeakDelegate` 属性进行松散类型化](#loosely-typed-via-the-weakdelegate-property)

例如，假设使用 UIWebView 类。 这会调度到分配给委托属性的 UIWebViewDelegate 实例。

##### <a name="via-events"></a>通过事件

对于许多类型，Xamarin.iOS 会自动创建相应的委托，该委托将 `UIWebViewDelegate` 调用转发到 C# 事件。 对于 `UIWebView`：

- webViewDidStartLoad 方法映射到 [UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted) 事件。
- webViewDidFinishLoad 方法映射到 [UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished) 事件。
- webView:didFailLoadWithError 方法映射到 [UIWebView.LoadError](xref:UIKit.UIWebView.LoadError) 事件。

例如，在加载 Web 视图时，这个简单的程序会记录开始时间和结束时间：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>通过属性

如果事件可能有多个订阅服务器，则事件很有用。 而且，事件仅限于代码没有返回值的情况。

如果代码应返回值，则我们选择改为属性。 这意味着，只能在对象的给定时间设置一个方法。

例如，可以使用此机制在 `UITextField` 的处理程序上关闭屏幕上的键盘：

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

在此情况下，`UITextField` 的 `ShouldReturn` 属性采用委托作为参数，该委托返回一个 bool 值，并确定 TextField 是否应在按下“返回”按钮的情况下执行操作。 在我们的方法中，将 true 返回给调用方，但我们也会从屏幕中删除键盘（当 textfield 调用 `ResignFirstResponder` 时，会发生这种情况）。

##### <a name="strongly-typed-via-a-delegate-property"></a>通过 Delegate 属性进行强类型化

如果不想使用事件，则可以提供自己的 [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) 子类，并将其分配给 [UIWebView.Delegate](xref:UIKit.UIWebView.Delegate) 属性。 分配 UIWebView.Delegate 后，UIWebView 事件调度机制将不再起作用，并将在发生相应的事件时调用 UIWebViewDelegate 方法。

例如，此简单类型会记录加载 Web 视图所用的时间：

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

上述内容在代码中使用，如下所示：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述命令将创建一个 UIWebViewer，并指示它将消息发送到通知程序的实例，这是我们为响应消息而创建的类。

此模式还可用于控制某些控件的行为，例如，在 UIWebView 情况下，[UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) 属性允许 `UIWebView` 实例控制 `UIWebView` 是否将加载页。

此模式还用于按需为几个控件提供数据。 例如，[UITableView](xref:UIKit.UITableView) 控件是一个功能强大的表呈现控件，而外观和内容则由 [UITableViewDataSource](xref:UIKit.UITableViewDataSource) 的实例驱动

### <a name="loosely-typed-via-the-weakdelegate-property"></a>通过 WeakDelegate 属性进行松散类型化

除了强类型属性以外，还有一个弱类型委托，使开发人员可以根据需要以不同的方式进行绑定。
在 Xamarin.iOS 的绑定中，只要公开了强类型 `Delegate` 属性，就还会公开相应的 `WeakDelegate` 属性。

使用 `WeakDelegate` 时，你需要负责使用 [Export](xref:Foundation.ExportAttribute) 属性来指定选择器，从而正确地修饰类。 例如：

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

请注意，分配 `WeakDelegate` 属性后，将不使用 `Delegate` 属性。 此外，如果在要执行 [Export] 的继承基类中实现此方法，则必须将其设为公共方法。

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>将 Objective-C 委托模式映射到 C\#

当看到如下所示的 Objective-C 示例时：

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

这会指示语言创建并构造类“SomethingDelegate”的实例，并将值分配给 foo 变量上的委托属性。 Xamarin.iOS 支持此机制，C# 语法如下：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 中，我们提供了映射到 Objective-C 委托类的强类型类。 若要使用这些方法，你将进行子类化，并覆盖 Xamarin.iOS 的实现所定义的方法。 有关其工作原理的详细信息，请参阅下面的“模型”一节。

### <a name="mapping-delegates-to-c"></a>将委托映射到 C\#

UIKit 一般使用两种形式的 Objective-C 委托。

第一种形式提供组件模型的接口。 例如，作为一种为视图按需提供数据的机制，如列表视图的数据存储工具。  在这些情况下，应始终创建适当类的实例并分配变量。

在下面的示例中，我们提供了一个使用字符串的模型的实现 `UIPickerView`：

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

第二种形式是提供事件的通知。 在这些情况下，虽然我们仍按上面概述的形式公开 API，但我们还提供了 C# 事件，这些事件对于快速操作更为简单，并且与 C# 中的匿名委托和 lambda 表达式相集成。

例如，可以订阅 `UIAccelerometer` 事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

这两个选项在适用时提供，但作为编程人员，你必须选择其中一个选项。 如果创建自己的强类型响应程序/委托实例并分配它，C# 事件将无法正常运行。 如果使用 C# 事件，则永远不会调用响应程序/委托类中的方法。

使用 `UIWebView` 的先前示例可以使用 C# 3.0 lambda 编写，如下所示：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>对事件作出响应

在 Objective-C 代码中，有时多个控件的事件处理程序和多个控件的信息提供程序将托管在同一个类中。 这是可能的，因为类响应消息，并且只要类响应消息，就可以将对象链接在一起。

如前所述，Xamarin.iOS 支持基于事件的 C# 编程模型和 Objective-C 委托模式，在该模式下，可以创建一个新类来实现委托并覆盖所需的方法。

还可以支持 Objective-C 的模式，其中多个不同操作的响应程序全部托管在类的同一个实例中。 若要执行此操作，必须使用 Xamarin.iOS 绑定的低级别功能。

例如，如果你希望类在类的同一实例中同时响应 `UITextFieldDelegate.textFieldShouldClear`: 消息和 `UIWebViewDelegate.webViewDidStartLoad`:，则必须使用 [Export] 属性声明：

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

方法的 C# 名称并不重要；重要的是传递到 [Export] 属性的字符串。

使用这种类型的编程时，请确保 C# 参数与运行时引擎将通过的实际类型匹配。

#### <a name="models"></a>模型

在 UIKit 存储设施中，或在使用帮助程序类实现的响应程序中，这些在 Objective-C 代码中通常称为委托，它们作为协议实现。

Objective-C 协议与接口类似，但它们支持可选方法，也就是说，并非所有方法都需要实现才能使协议工作。

有两种方法来实现模型。 可以手动实现，也可以使用现有的强类型定义来实现。

当你尝试实现未由 Xamarin.iOS 绑定的类时，必须使用手动机制。 这非常简单：

- 标记类以在运行时注册
- 在要重写的每个方法上应用具有实际选择器名称的 [Export] 属性
- 实例化类，然后传递它。

例如，以下仅实现 UIApplicationDelegate 协议定义中的一种可选方法：

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Objective-C 选择器名称（“applicationDidFinishLaunching:”）通过 Export 属性进行声明，并向 `[Register]` 属性注册该类。

Xamarin.iOS 提供强类型声明，可供使用，无需手动绑定。 为了支持此编程模型，Xamarin.iOS 运行时支持类声明上的 [Model] 属性。 这会通知运行时它不应连接类中的所有方法，除非显式实现方法。

这意味着，在 UIKit 中，可按如下方法编写表示具有可选方法的协议的类：

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

如果要实现仅实现某些方法的模型，则只需替代你感兴趣的方法，并忽略其他方法。 运行时将只挂钩覆盖的方法，而不是 Objective-C 环境的原始方法。

与上一个手动示例等效的是：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

优点在于，无需深入了解 Objective-C 头文件来查找选择器、参数的类型或映射到 C#，并且可以从 Visual Studio for Mac 获取 Intellisense 以及强类型

#### <a name="xib-outlets-and-c"></a>XIB 输出口和 C\#

> [!IMPORTANT]
> 本部分说明了使用 XIB 文件时与输出口的 IDE 集成。 使用 Xamarin Designer for iOS 时，将通过在 IDE“属性”部分中的“标识”>“名称”下输入名称来替换，如下所示：
>
> [![在 iOS 设计器中输入项名称](images/designeroutlet.png)](images/designeroutlet.png#lightbox)
>
>有关 iOS 设计器的详细信息，请参阅 [iOS 设计器简介](~/ios/user-interface/designer/introduction.md#how-it-works)文档。

这是有关如何将输出口与 C# 进行集成以及如何为 Xamarin.iOS 的高级用户提供的简要说明。 使用 Visual Studio for Mac 时，会使用为你实时生成的代码在后台自动执行映射。

使用 Interface Builder 设计用户界面时，只会设计应用程序的外观，并将建立一些默认连接。 如果要以编程方式提取信息、在运行时更改控件的行为或在运行时修改控件，则必须将某些控件绑定到托管代码。

这只需几个步骤即可完成：

1. 将输出口声明添加到文件的所有者。
1. 将控件连接到文件的所有者。
1. 将 UI 和连接存储到 XIB/NIB 文件中。
1. 在运行时加载 NIB 文件。
1. 访问输出口变量。

有关使用 Interface Builder 生成接口的 Apple 文档中介绍了步骤 (1) 到 (3)。

使用 Xamarin.iOS 时，应用程序需要创建一个派生自 UIViewController 的类。 它的实现如下所示：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然后，若要从 NIB 文件加载 ViewController，请执行以下操作：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

这将从 NIB 加载用户界面。 现在，若要访问输出口，必须通知运行时我们要访问它们。 为此，`UIViewController` 子类需要声明属性并通过 [Connect] 属性对这些属性进行批注。 如：

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

属性实现是实际提取并存储实际本机类型的值的实现。

使用 Visual Studio for Mac 和 InterfaceBuilder 时，无需考虑这一点。 Visual Studio for Mac 使用作为项目的一部分编译的部分类中的代码来自动镜像所有声明的输出口。

#### <a name="selectors"></a>选择器

Objective-C 编程的核心概念是选择器。 你经常会遇到需要你传递选择器的 API，或者期望你的代码响应选择器。

在 C# 中创建新的选择器非常简单 - 只需创建 `ObjCRuntime.Selector` 类的新实例，并在需要它的 API 中的任何位置使用结果即可。 例如：

```csharp
var selector_add = new Selector ("add:plus:");
```

对于响应选择器调用的 C# 方法，它必须继承自 `NSObject` 类型，并且该 C# 方法必须使用 `[Export]` 属性通过选择器名称进行修饰。 例如：

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

请注意，选择器名称必须完全匹配，包括中间和结尾的所有冒号 (:)（如果存在）。

#### <a name="nsobject-constructors"></a>NSObject 构造函数

Xamarin.iOS 中派生自 `NSObject` 的大多数类将公开特定于对象功能的构造函数，但它们还将公开各种不会立即显而易见的构造函数。

构造函数的使用方式如下：

```csharp
public Foo (IntPtr handle)
```

当运行时需要将你的类映射到非托管类时，此构造函数用于实例化你的类。 在加载 XIB/NIB 文件时，会发生这种情况。  此时，Objective-C 运行时将在非托管环境中创建一个对象，并且将调用此构造函数来初始化托管端。

通常情况下，只需调用带有 handle 参数的基构造函数，然后在正文中执行任何必要的初始化。

```csharp
public Foo ()
```

这是类的默认构造函数，在 Xamarin.iOS 提供的类中，这会初始化 Foundation.NSObject 类及其之间的所有类，并在结束时将其链接到类上的 Objective-C `init` 方法。

```csharp
public Foo (NSObjectFlag x)
```

此构造函数用于初始化实例，但阻止代码在末尾调用 Objective-C“init”方法。 如果已注册初始化（在构造函数中使用 `[Export]`）或已通过另一种方式完成初始化，则通常使用此操作。

```csharp
public Foo (NSCoder coder)
```

此构造函数适用于从 NSCoding 实例初始化对象的情况。

#### <a name="exceptions"></a>异常

Xamarin.iOS API 设计不会将 Objective-C 异常作为 C# 异常引发。 该设计首先强制不会将任何垃圾邮件发送到 Objective-C 环境，并且必须生成的任何异常在无效数据传递到 Objective-C 环境之前由绑定本身生成。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中，开发人员可以订阅由基础层平台广播的通知。 这通过使用 `NSNotificationCenter.DefaultCenter.AddObserver` 方法来完成。 `AddObserver` 方法采用两个参数，一个是要订阅的通知，另一个是在引发通知时要调用的方法。

在 Xamarin.iOS 和 Xamarin.Mac 中，各种通知的键都托管在触发通知的类上。 例如，由 `UIMenuController` 引发的通知在 `UIMenuController` 类中作为 `static NSString` 属性进行托管，并以名称“Notification”结尾。

### <a name="memory-management"></a>内存管理

Xamarin.iOS 具有垃圾回收器，当不再使用资源时，它将负责释放资源。 除了垃圾回收器外，派生自 `NSObject` 的所有对象都实现 `System.IDisposable` 接口。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公开 `IDisposable` 接口是帮助开发人员释放可能封装大内存块的对象的一种便捷方式（例如，`UIImage` 可能只是一个无恶意的指针，但可能会指向 2 MB 的图像）以及其他重要和有限的资源（如视频解码缓冲区）。

NSObject 实现 IDisposable 接口，同时还实现 [.NET Dispose 模式](/dotnet/standard/garbage-collection/implementing-dispose)。 这使得子类化 NSObject 的开发人员可以替代 Dispose 行为，并按需释放自己的资源。 例如，假设有如下视图控制器，它保留一组图像：

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

当托管对象被释放时，它将不再有用。 你可能仍具有对对象的引用，但对象用于此时无效的所有意图和目的。 如果尝试访问已释放对象上的任何方法，某些 .NET API 会通过引发 ObjectDisposedException 来确保这一点，例如：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使仍可以访问变量“image”，它确实是无效的引用，并且不再指向保存该图像的 Objective-C 对象。

但在 C# 中释放对象并不意味着对象一定将被销毁。 你所做的只是释放 C# 对该对象的引用。 Cocoa 环境可能会保留引用以供自己使用。 例如，如果将 UIImageView 的 Image 属性设置为一个图像，然后释放该图像，则基础 UIImageView 已获取其自己的引用，并且将保留对此对象的引用，直到它完成使用。

#### <a name="when-to-call-dispose"></a>何时调用 Dispose

当需要 Mono 删除对象时，应调用 Dispose。 一种可能的用例是：Mono 不知道 NSObject 实际上具有对重要资源（如内存）或信息池的引用。 在这些情况下，应调用 Dispose 立即释放对内存的引用，而不是等待 Mono 执行垃圾回收周期。

在内部，当 Mono [从 C# 字符串创建 NSString 引用](~/ios/internals/api-design/nsstring.md)时，它会立即释放它们，以减少垃圾回收器必须执行的工作量。 要处理的对象越少，GC 运行的速度就越快。

#### <a name="when-to-keep-references-to-objects"></a>何时保留对对象的引用

自动内存管理的一个副作用是，只要任何未使用的对象没有被引用，GC 就会将其删除。 这有时可能会产生意外的副作用，例如，如果你创建了一个本地变量来保存顶级视图控制器或顶级窗口，然后让这些程序消失。

如果不在静态或实例变量中保留对对象的引用，Mono 将会对其调用 Dispose() 方法，并释放对对象的引用。 由于这可能是唯一未完成的引用，因此，Objective-C 运行时将销毁对象。

## <a name="related-links"></a>相关链接

- [绑定字段](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)