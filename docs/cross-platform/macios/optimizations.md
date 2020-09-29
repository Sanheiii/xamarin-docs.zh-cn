---
title: 生成优化
description: 本文档说明了在生成时为 Xamarin 和 Xamarin 应用程序应用的各种优化。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: d3076e35d6e9b7e44ec2f9537f250295eaff7e2e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453553"
---
# <a name="build-optimizations"></a>生成优化

本文档说明了在生成时为 Xamarin 和 Xamarin 应用程序应用的各种优化。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>删除 UIApplication/EnsureUIThread/NSApplication。 EnsureUIThread

删除对 Xamarin) 的 [UIApplication][1] (调用，或删除 `NSApplication.EnsureUIThread` xamarin) 的 (。

此优化将更改以下类型的代码：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

以下内容：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，它对发布版本启用。

通过传递 `--optimize=[+|-]remove-uithread-checks` 到 mtouch/mmp，可以重写默认行为。

[1]: /dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>内联 IntPtr. Size

根据目标平台 Inlines 的常量值 `IntPtr.Size` 。

此优化将更改以下类型的代码：

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

为64位平台构建时的以下 () ：

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，如果针对的是单个体系结构或平台程序集，则启用该功能 (**Xamarin.iOS.dll**、 **Xamarin.TVOS.dll**、 **Xamarin.WatchOS.dll** 或 **Xamarin.Mac.dll**) 。

如果面向多个体系结构，此优化将为32位版本和应用程序的64位版本创建不同的程序集，并且这两个版本都必须包含在应用程序中，而不是降低最终的应用程序大小。

通过传递 `--optimize=[+|-]inline-intptr-size` 到 mtouch/mmp，可以重写默认行为。

## <a name="inline-nsobjectisdirectbinding"></a>Inline NSObject. IsDirectBinding

`NSObject.IsDirectBinding` 是一个实例属性，该属性确定某一特定实例是否为包装类型 (包装类型是否为映射到本机类型的托管类型;例如，托管 `UIKit.UIView` 类型映射到本机 `UIView` 类型-相反，是用户类型，在本例中为 `class MyUIView : UIKit.UIView` 用户类型) 。

`IsDirectBinding`在调用目标-C 时，必须知道的值，因为值确定要使用哪个版本的 `objc_msgSend` 。

仅给定以下代码：

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

我们可以确定中的 `UIView.SomeProperty` 值不是 `IsDirectBinding` 常量，并且不能内联：

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

但是，可以查看应用中的所有类型，并确定没有从继承的类型 `NSUrl` ，这样就可以安全地将 `IsDirectBinding` 值内联到常量中 `true` ：

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

具体而言，此优化将更改以下类型的代码 (这是) 的绑定代码 `NSUrl.AbsoluteUrl` ：

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

在以下 (可以确定 `NSUrl` 应用程序) 中没有子类：

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，对于 Xamarin 始终处于启用状态，但默认情况下，对于 Xamarin. Mac (始终处于禁用状态，因为可以在 Xamarin 中动态加载程序集，因此无法确定特定类绝不会) 子类。

通过传递 `--optimize=[+|-]inline-isdirectbinding` 到 mtouch/mmp，可以重写默认行为。

## <a name="inline-runtimearch"></a>内联运行时

此优化将更改以下类型的代码：

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

为设备) 生成时，以下 (：

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，对于 Xamarin iOS (始终启用此功能。不能用于 Xamarin) 。

通过传递到 mtouch，可以重写默认行为 `--optimize=[+|-]inline-runtime-arch` 。

## <a name="dead-code-elimination"></a>死代码消除

此优化将更改以下类型的代码：

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

为

```csharp
Console.WriteLine ("Doing this");
```

它还将评估常数比较，如下所示：

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

并确定表达式始终为 `8 == 8` true，并将其减少为：

```csharp
Console.WriteLine ("Doing this");
```

这是一种功能强大的优化，与内联优化一起使用，因为它可以转换以下类型的代码 (这是) 的绑定代码 `NFCIso15693ReadMultipleBlocksConfiguration.Range` ：

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

在为64位设备生成时，以及在应用) 中没有子类的情况下，在此 (`NFCIso15693ReadMultipleBlocksConfiguration` ：

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 编译器已经能够消除类似于这样的代码，但这种优化是在链接器中完成的，这意味着，链接器可以看到有多个方法不会再使用，因此可以 (删除，除非使用) ：

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，默认情况下， (启用链接器时，它将始终启用) 。

通过传递 `--optimize=[+|-]dead-code-elimination` 到 mtouch/mmp，可以重写默认行为。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>优化对 BlockLiteral 的调用。 SetupBlock

在为托管委托创建目标为 C 的块时，Xamarin/Mac 运行时需要知道块签名。 这可能是相当昂贵的操作。 此优化将在生成时计算块签名，并修改 IL 以调用 `SetupBlock` 采用签名作为参数的方法。 这样做可以避免需要在运行时计算签名。

基准测试表明，这会加快调用块的速度，使其系数为10到15。

它将转换以下 [代码](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211)：

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

为

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，当在 Xamarin 中使用静态注册器 (时，将默认启用此功能。 iOS 默认情况下，为设备生成启用静态注册注册，在 Xamarin 中为发布) 版本启用静态注册注册。

通过传递 `--optimize=[+|-]blockliteral-setupblock` 到 mtouch/mmp，可以重写默认行为。

## <a name="optimize-support-for-protocols"></a>优化对协议的支持

Xamarin/Mac 运行时需要有关托管类型如何实现目标 C 协议的信息。 此信息存储在这些接口 (和特性) 中，这种格式不是一种非常有效的格式，也不能识别链接器。

其中一个示例是，这些接口将有关属性中所有协议成员的信息存储在 `[ProtocolMember]` 一起，其中其他内容包含对这些成员的参数类型的引用。 这意味着，只需实现此类接口，链接器就会保留在该接口中使用的所有类型，甚至应用程序永远不会调用或实现的可选成员。

此优化会使静态注册机构以有效的格式存储任何所需的信息，这种格式使用的内存非常简单，并且在运行时可快速查找。

它还会告诉链接器，不一定需要保留这些接口，也不需要保留任何相关的属性。

此优化需要同时启用链接器和静态注册器。

在 Xamarin 上，默认情况下，当同时启用链接器和静态注册器时，将启用此优化。

在 Xamarin 上，此优化默认情况下不启用，因为 Xamarin 支持动态加载程序集，这些程序集在生成 (时可能没有已知，因此未) 优化。

通过传递 `--optimize=-register-protocols` 到 mtouch/mmp，可以重写默认行为。

## <a name="remove-the-dynamic-registrar"></a>删除动态注册器

Xamarin 和 Xamarin 运行时都支持向目标 C 运行时 [注册托管类型](~/ios/internals/registrar.md) 。 它可以在生成时或运行时 (运行，也可以在运行时) 部分完成，但如果在生成时完全完成，则可以删除运行时用于执行此操作的支持代码。 这会显著降低应用程序的大小，尤其是适用于扩展或 watchOS 应用程序的小型应用程序。

此优化需要同时启用静态注册注册器和链接器。

链接器将尝试确定是否可以安全地删除动态注册程序，如果是，则将尝试删除它。

由于 Xamarin 支持在运行时动态加载程序集 (在生成时) 未知，因此，无论这是否为安全优化，都无法确定生成时。 这意味着，默认情况下不会为 Xamarin Mac 应用启用此优化。

通过传递 `--optimize=[+|-]remove-dynamic-registrar` 到 mtouch/mmp，可以重写默认行为。

如果重写默认值以删除动态注册器，则链接器将发出警告，如果它检测到这是不安全的 (但是动态注册器仍将) 删除。

## <a name="inline-runtimedynamicregistrationsupported"></a>内联运行时。 DynamicRegistrationSupported

Inlines 的值在 `Runtime.DynamicRegistrationSupported` 生成时确定。

如果删除动态注册器 (参阅 [删除动态注册](#remove-the-dynamic-registrar) 器优化) ，这是一个常数值 `false` ，否则它是一个常 `true` 数值。

此优化将更改以下类型的代码：

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

删除动态注册机构时的以下内容：

```csharp
throw new Exception ("dynamic registration is not supported");
```

在未删除动态注册器的情况下，转换为以下内容：

```csharp
Console.WriteLine ("do something");
```

此优化需要启用链接器，且仅应用于具有属性的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

默认情况下，默认情况下， (启用链接器时，它将始终启用) 。

通过传递 `--optimize=[+|-]inline-dynamic-registration-supported` 到 mtouch/mmp，可以重写默认行为。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>用于为目标-C 块创建托管委托的预计算方法

当目标 C 调用选择器以块作为参数，然后托管代码重写该方法时，Xamarin/Xamarin 运行时需要为该块创建委托。

绑定生成器生成的绑定代码将包含一个 `[BlockProxy]` 属性，该属性使用 `Create` 可执行此操作的方法指定类型。

给定以下目标-C 代码：

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

以下绑定代码：

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

生成器将生成：

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }

    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...

    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;

        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }

    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

当目标-C 调用时 `[ObjCBlockTester callClassCallback]` ，Xamarin/xamarin 运行时将查看 `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` 参数上的属性。 然后，它将 `Create` 在该类型上查找方法，并调用该方法来创建委托。

此优化将 `Create` 在生成时找到方法，并使用元数据标记生成在运行时查找方法的代码，而不是使用属性和反射 (这种方法要快得多，并且还允许链接器删除相应的运行时代码，使应用更小) 。

如果 mmp/mtouch 找不到 `Create` 方法，则将显示 MT4174/MM4174 警告，并改为在运行时执行查找。
最可能的原因是，手动编写了绑定代码，但没有所需的 `[BlockProxy]` 属性。

此优化要求启用静态注册器。

默认情况下，只要启用静态注册器) ，就总是默认启用 (。

通过传递 `--optimize=[+|-]static-delegate-to-block-lookup` 到 mtouch/mmp，可以重写默认行为。