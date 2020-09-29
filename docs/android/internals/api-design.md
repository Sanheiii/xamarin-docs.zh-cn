---
title: Xamarin Android API 设计原则
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: bb9dadd388a514f18abdaf6e6db6d76f0be99f86
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454242"
---
# <a name="xamarinandroid-api-design-principles"></a>Xamarin Android API 设计原则

除了包含 Mono 的核心基类库外，Xamarin 还附带了各种 Android Api 的绑定，以允许开发人员通过 Mono 创建本机 Android 应用程序。

Xamarin 的核心是一个互操作引擎，它将 c # 世界与 Java 世界桥梁，并为开发人员提供了从 c # 或其他 .NET 语言访问 Java Api 的权限。

## <a name="design-principles"></a>设计原理

下面是适用于 Xamarin 的一些设计原则

- 符合  [.NET Framework 设计准则](/dotnet/standard/design-guidelines/)。

- 允许开发人员为 Java 类提供子类。

- 子类应该适用于 c # 标准构造。

- 从现有类派生。

- 调用基构造函数以进行链接。

- 重写方法应通过 c # 的重写系统来完成。

- 轻松执行常见的 Java 任务，并使其难以实现。

- 将 JavaBean 属性公开为 c # 属性。

- 公开强类型的 API：

  - 提高类型安全性。

  - 最小化运行时错误。

  - 获取有关返回类型的 IDE intellisense。

  - 允许用于 IDE 弹出窗口文档。

- 建议对 API 进行 IDE 浏览：

  - 利用框架替代方法来最大程度地减少 Java Classlib。

  - 适当时公开 (lambda、匿名方法和 System.object) 的 c # 委托，而不是单方法接口。

  - 提供一种机制，用于将任意 Java[库 () 。](xref:Android.Runtime.JNIEnv)

## <a name="assemblies"></a>程序集

Xamarin 包含若干构成 *MonoMobile 配置文件*的程序集。 [程序集](~/cross-platform/internals/available-assemblies.md)页面包含更多信息。

到 Android 平台的绑定包含在 `Mono.Android.dll` 程序集中。 此程序集包含用于使用 Android Api 和与 Android 运行时 VM 通信的整个绑定。

## <a name="binding-design"></a>绑定设计

### <a name="collections"></a>集合

Android Api 广泛地利用 util 集合来提供列表、集和映射。 我们会在绑定中使用[system.exception 接口公开这些元素。](xref:System.Collections.Generic) 基础映射包括：

- [util \<E> ](https://developer.android.com/reference/java/util/Set.html)映射到系统类型[ICollection \<T> ](xref:System.Collections.Generic.ICollection`1)、helper 类[JavaSet \<T> ](xref:Android.Runtime.JavaSet`1)。

- [util \<E> ](https://developer.android.com/reference/java/util/List.html)映射到系统类型[IList \<T> ](xref:System.Collections.Generic.IList`1)，帮助程序类[JavaList \<T> ](xref:Android.Runtime.JavaList`1)。

- [util<K，V>](https://developer.android.com/reference/java/util/Map.html) 映射到系统类型 [IDictionary<TKey、TValue>](xref:System.Collections.Generic.IDictionary`2)、Helper 类 [JavaDictionary<K、V>](xref:Android.Runtime.JavaDictionary`2)。

- [util \<E> ](https://developer.android.com/reference/java/util/Collection.html)映射到系统类型[ICollection \<T> ](xref:System.Collections.Generic.ICollection`1)、helper 类[JavaCollection \<T> ](xref:Android.Runtime.JavaCollection`1)。

我们提供了帮助程序类，有助于更快地 copyless 这些类型的封送处理。 如果可能，我们建议使用提供的集合，而不是提供的框架实现，如 [`List<T>`](xref:System.Collections.Generic.List`1) 或 [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2) 。 [Android. 运行时](xref:Android.Runtime)实现在内部利用本机 Java 集合，因此，在传递到 Android API 成员时，不需要从本机集合复制和进行复制。

可以将任何接口实现传递到接受该接口的 Android 方法，例如，将传递 `List<int>` 给 [ArrayAdapter &lt; int &gt; (Context，int，IList &lt; int &gt;) ](xref:Android.Widget.ArrayAdapter`1) 构造函数。 *但*对于 *除 Android 以外* 的所有实现，这涉及到将列表从 Mono VM *复制* 到 android 运行时 vm。 如果以后在 Android 运行时中更改了列表 (例如，通过调用 [ArrayAdapter &lt; T &gt; 。添加 (T) ](xref:Android.Widget.ArrayAdapter`1.Add*) 方法) ，这些更改在托管代码中 *将不* 可见。 如果 `JavaList<int>` 使用了，则这些更改将可见。

*不*是上述**帮助器类**es 之一的只、集合接口实现仅封送 [In]：

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```

### <a name="properties"></a>属性

Java 方法在适当时转换为属性：

- Java 方法对 `T getFoo()` 并 `void setFoo(T)` 转换为 `Foo` 属性。 示例：[活动。](xref:Android.App.Activity.Intent)

- Java 方法 `getFoo()` 转换为只读 Foo 属性。 示例： [PackageName](xref:Android.Content.Context.PackageName)。

- 不生成仅设置属性。

- 如果属性类型是数组，则 *不* 生成属性。

### <a name="events-and-listeners"></a>事件和侦听器

Android Api 构建在 Java 之上，其组件按照 Java 模式与事件侦听器挂钩。 此模式往往非常麻烦，因为它要求用户创建匿名类并声明要重写的方法，例如，在 Android 中使用 Java 完成操作：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

C # 中使用事件的等效代码是：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

请注意，这两种机制都适用于 Xamarin。 可以实现侦听器接口并将其附加到 SetOnClickListener，也可以将通过任何常用 c # 模式创建的委托附加到 Click 事件。

如果侦听器回调方法具有 void 返回，我们将基于[EventHandler &lt; TEventArgs &gt; ](xref:System.EventHandler`1)委托创建 API 元素。 对于这些侦听器类型，我们将生成类似于上述示例的事件。 但是，如果侦听器回调返回非 void 和非 **布尔** 值，则不会使用事件和 EventHandlers。 我们改为生成回调签名的特定委托并添加属性，而不是事件。 原因是处理委托调用顺序和返回处理。 此方法反映了对 Xamarin API 执行的操作。

仅当 Android 事件注册方法为时，才会自动生成 c # 事件或属性：

1. 具有 `set` 前缀，例如 [*设置*OnClickListener](xref:Android.Views.View.SetOnClickListener*)。

1. 具有 `void` 返回类型。

1. 仅接受一个参数，参数类型为接口，接口只有一个方法，接口名称以结尾 `Listener` ，如 "[查看"。 *Listener*](xref:Android.Views.View.IOnClickListener)

此外，如果侦听器接口方法的返回类型为 **布尔值** 而不是 **void**，则生成的 *EventArgs* 子类将包含一个已 *处理* 的属性。 *处理*的属性的值将用作*侦听器*方法的返回值，并且默认为 `true` 。

例如，Android [setOnKeyListener ( # B1 ](xref:Android.Views.View.SetOnKeyListener*) 方法接受 [OnKeyListener](xref:Android.Views.View.IOnKeyListener) 接口， [OnKeyListener onKey (视图，int，KeyEvent) ](xref:Android.Views.View.IOnKeyListener.OnKey*) 方法的返回类型为布尔值。 Xamarin 会生成一个对应的[视图 KeyPress](xref:Android.Views.View.KeyPress)事件，这是一个[EventHandler &lt; system.windows.forms.keyeventargs.handled &gt; ](xref:Android.Views.View.KeyEventArgs)。
*System.windows.forms.keyeventargs.handled*类又具有[system.windows.forms.keyeventargs.handled](xref:Android.Views.View.KeyEventArgs.Handled)属性，该属性用作 OnKeyListener onKey 方法的返回值。 * ( # B1*方法。

我们打算为其他方法和 ctor 添加重载，以公开基于委托的连接。 此外，具有多个回调的侦听器还需要进行一些额外检查来确定实现各个回调是否合理，因此，我们将在确定时进行转换。 如果没有对应的事件，则必须在 c # 中使用侦听器，但要注意的是，请让你认为可能会有委托使用。 我们还在不使用 "侦听器" 后缀的情况下进行了一些转换，这种转换会从委托替代项受益。

所有侦听器接口实现 [`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
接口，因为绑定的实现细节，所以侦听器类必须实现此接口。 为此，可以在 [java](xref:Java.Lang.Object) 或任何其他已包装的 java 对象（如 Android 活动）的子类上实现侦听器接口。

### <a name="runnables"></a>Runnables

Java 利用了 [.java](xref:Java.Lang.Runnable) 接口来提供委托机制。 [.Java](xref:Java.Lang.Thread)类是此接口的重要使用者。 Android 也在 API 中采用了接口。
[RunOnUiThread ( # B1 ](xref:Android.App.Activity.RunOnUiThread*) 和 [View.post ( # B3 ](xref:Android.Views.View.Post*) 是值得注意的示例。

`Runnable`接口包含一个 void 方法，[运行 ( # B1 ](xref:Java.Lang.Runnable.Run)。 因此，它将 c # 中的绑定视为一个 [系统操作](xref:System.Action) 委托。 我们在绑定中提供了一些重载，这些重载接受 `Action` 本机 API 中使用的所有 API 成员的参数 `Runnable` ，例如 [RunOnUiThread ( # B1 ](xref:Android.App.Activity.RunOnUiThread*) 和 [View.Post ( # B3 ](xref:Android.Views.View.Post*)。

我们保留了 [IRunnable](xref:Java.Lang.IRunnable) 重载，而不是将其替换，因为多个类型实现了接口，因此可以直接作为 runnables 传递。

### <a name="inner-classes"></a>内部类

Java 具有两种不同类型的 [嵌套类](https://download.oracle.com/javase/tutorial/java/javaOO/nested.html)：静态嵌套类和非静态类。

Java 静态嵌套类与 c # 嵌套类型相同。

非静态嵌套类（也称为 *内部类*）的差别很大。 它们包含对其封闭类型的实例的隐式引用，不能包含静态成员 (在此概述) 范围之外的其他差异中。

当涉及到绑定和 c # 使用时，静态嵌套类被视为普通嵌套类型。 同时，内部类有两个重要的区别：

1. 对包含类型的隐式引用必须作为构造函数参数显式提供。

1. 从内部类继承时，内部类 *必须* 嵌套在继承自基内部类的包含类型的类型中，并且派生类型必须提供与包含类型的 c # 类型相同的构造函数。

例如，请考虑 [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService.Engine) 的内部类。 由于它是一个内部类， [WallpaperService ( # A1 构造函数](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) 获取对 [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) 实例的引用， (比较和对比 Java WallpaperService ( # A4 构造函数，该构造函数不采用任何参数) 。

内部类的示例派生为 CubeWallpaper. CubeEngine：

```csharp
class CubeWallpaper : WallpaperService {
    public override WallpaperService.Engine OnCreateEngine ()
    {
        return new CubeEngine (this);
    }

    class CubeEngine : WallpaperService.Engine {
        public CubeEngine (CubeWallpaper s)
                : base (s)
        {
        }
    }
}
```

请注意 `CubeWallpaper.CubeEngine` ，如何嵌套在中 `CubeWallpaper` ， `CubeWallpaper` 继承自的包含类 `WallpaperService.Engine` ，并且 `CubeWallpaper.CubeEngine` 具有一个采用声明类型的构造函数（ `CubeWallpaper` 在本例中为--以上指定的全部）。

### <a name="interfaces"></a>接口

Java 接口可包含三组成员，其中两个成员将导致来自 c # 的问题：

1. 方法

1. 类型

1. 字段

Java 接口被转换为两种类型：

1.  (可选的包含方法声明的) 接口。 此接口与 Java 接口具有相同的名称， *但* 它也具有 " *I* " 前缀。

1. 一个 (可选) 静态类，其中包含在 Java 接口中声明的任何字段。

嵌套类型被 "重定位" 为封闭接口的同级，而不是嵌套类型，并将封闭接口名称作为前缀。

例如，请考虑 [Parcelable](xref:Android.OS.Parcelable) 接口。
*Parcelable*接口包含方法、嵌套类型和常数。 *Parcelable*接口方法放置在[IParcelable](xref:Android.OS.IParcelable)接口中。
*Parcelable*接口常量将被放入[ParcelableConsts](xref:Android.OS.ParcelableConsts)类型。 由于泛型支持中的限制，嵌套的[android. Parcelable. \<T> ClassLoaderCreator](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)和[ \<T> Parcelable](https://developer.android.com/reference/android/os/Parcelable.Creator.html)类型当前未绑定; 如果支持，则它们将显示为*android. IParcelableClassLoaderCreator*和 android.. *IParcelableCreator*接口。 例如，嵌套的 DeathRecipient 接口被绑定为 [IBinder](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) 接口的 [IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) 接口。

> [!NOTE]
> 从 Xamarin 1.9 开始，Java 接口常量 _重复_ ，以简化移植 Java 代码。 这有助于改进依赖于 [android 提供程序](https://developer.android.com/reference/android/provider/package-summary.html) 接口常量的移植 Java 代码。

除了以上类型之外，还有四个进一步的更改：

1. 生成与 Java 接口具有相同名称的类型以包含常量。

1. 包含接口常量的类型还包含实现的 Java 接口中的所有常量。

1. 实现包含常量的 Java 接口的所有类都将获取一个新的嵌套 InterfaceConsts 类型，该类型包含所有已实现接口中的常量。

1. *常量*类型现已过时。

对于 *Parcelable* 接口，这意味着现在会有一个 [*Parcelable*](xref:Android.OS.Parcelable) 类型来包含常量的类型。 例如， [CONTENTS_FILE_DESCRIPTOR Parcelable](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) 常数将绑定为 [*Parcelable*](xref:Android.OS.Parcelable.ContentsFileDescriptor) 常量，而不是 *ParcelableConsts. ContentsFileDescriptor* 常数。

对于包含用于实现其他接口（还包含更多常量）的常量的接口，现在会生成所有常量的并集。 例如，MediaStore 接口实现了 [VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) 接口，该接口用于实现 [MediaStore. MediaColumns](xref:Android.Provider.MediaStore.MediaColumns) 接口。 但是，在1.9 之前，MediaStore 类型无法访问在 [VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) 上声明的常量。 [MediaStore. MediaColumnsConsts.](xref:Android.Provider.MediaStore.MediaColumnsConsts)。
因此，需要将 Java 表达式 *MediaStore* 绑定到 c # 表达式 MediaStore，而无需阅读大量的 Java 文档就难以发现 *MediaColumnsConsts.* 。 在1.9 中，等效的 c # 表达式将为 [*MediaStore. VideoColumns*](xref:Android.Provider.MediaStore.Video.VideoColumns.Title)。

此外，请考虑采用 " [android](xref:Android.OS.Bundle) " 类型的类型，它实现了 Java *Parcelable* 接口。 由于它实现了接口，该接口上的所有常量都可通过捆绑类型进行访问，例如， *CONTENTS_FILE_DESCRIPTOR* 是一个完全有效的 Java 表达式。
以前，若要将此表达式移植到 c #，需要查看实现的所有接口，以查看 *CONTENTS_FILE_DESCRIPTOR* 来自哪个类型。 从 Xamarin 1.9 开始，实现包含常量的 Java 接口的类将具有嵌套的 *InterfaceConsts* 类型，该类型包含所有继承的接口常量。 这将允许将 InterfaceConsts *CONTENTS_FILE_DESCRIPTOR* 转换为 [*ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor)。

最后， *常量* 后缀（如 *ParcelableConsts* ）的类型现已过时，而不是新引入的 InterfaceConsts 嵌套类型。 它们将在 Xamarin 3.0 中被删除。

## <a name="resources"></a>资源

图像、布局说明、二进制 blob 和字符串字典可以作为 [资源文件](https://developer.android.com/guide/topics/resources/providing-resources.html)包含在你的应用程序中。
各种 Android Api 旨在对 [资源 id 进行操作](https://developer.android.com/guide/topics/resources/accessing-resources.html) ，而不是直接处理图像、字符串或二进制 blob。

例如，包含用户界面布局的示例 Android 应用程序 ( `main.axml`) ，国际化表字符串 ( `strings.xml`) ，某些图标 ( `drawable-*/icon.png`) 会将其资源保留在应用程序的 "资源" 目录中：

```
Resources/
    drawable-hdpi/
        icon.png

    drawable-ldpi/
        icon.png

    drawable-mdpi/
        icon.png

    layout/
        main.axml

    values/
        strings.xml
```

本机 Android Api 不会直接与文件名一起操作，而是对资源 Id 进行操作。 在编译使用资源的 Android 应用程序时，生成系统将打包用于分发的资源，并生成一个名 `Resource` 为的类，其中包含每个所包含资源的标记。 例如，对于上述资源布局，R 类将公开以下内容：

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

然后，您可以使用 `Resource.Drawable.icon` 来引用 `drawable/icon.png` 文件或引用 `Resource.Layout.main` `layout/main.xml` 文件，或 `Resource.String.first_string` 引用字典文件中的第一个字符串 `values/strings.xml` 。

## <a name="constants-and-enumerations"></a>常量和枚举

本机 Android Api 具有多个方法，这些方法采用或返回必须映射到常量字段的 int，以确定 int 的含义。 若要使用这些方法，用户需要查阅文档，以查看哪些常量为适当的值，这些值小于理想值。

例如，请考虑 [requestWindowFeature (Int featureID) ](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在这些情况下，我们努力将相关常量组合到 .NET 枚举中，并重新映射方法以改用枚举。
通过执行此操作，我们能够为 IntelliSense 选择可能的值。

上面的示例变为： [RequestWindowFeature (WindowFeatures featureId) ](xref:Android.App.Activity.RequestWindowFeature*)。

请注意，这是一个非常手动的过程，用于确定哪些常量属于一起，哪些 Api 使用这些常量。 请为 API 中使用的任何常量处理错误，这将更好地表示为枚举。