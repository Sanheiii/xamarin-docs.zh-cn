---
title: 面向 Java 开发者的 Xamarin
description: 如果你是 Java 开发人员，则可以在 Xamarin 平台上充分利用你的技能和现有代码，同时获得 C# 的代码重用优势。 你会发现 C# 语法与 Java 语法非常相似，这两种语言提供非常类似的功能。 此外，你会发现 C# 的特有功能，这些功能将帮助你轻松进行开发工作。
ms.prod: xamarin
ms.assetid: A3B6C041-4052-4E7D-999C-C4FA10BE3D67
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: d32d30419550c9b5f9a612589d12eba7c74eef26
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454320"
---
# <a name="xamarin-for-java-developers"></a>面向 Java 开发者的 Xamarin

_如果你是 Java 开发人员，则可以在 Xamarin 平台上充分利用你的技能和现有代码，同时获得 C# 的代码重用优势。你会发现 C# 语法与 Java 语法非常相似，这两种语言提供非常类似的功能。此外，你会发现 C# 的特有功能，这些功能将帮助你轻松进行开发工作。_

## <a name="overview"></a>概述

本文介绍面向 Java 开发人员的 C# 编程，主要侧重于在开发 Xamarin.Android 应用程序时会遇到的 C# 语言功能。 此外，本文说明了这些功能与其 Java 对应项的区别所在，并介绍了在 Java 中不可用的重要 C# 功能（与 Xamarin.Android 相关）。 包含指向附加参考资料的链接，因此你可以将本文用作一个“起点”以进一步研究 C# 和 .NET。

如果你熟悉 Java，那么对于 C# 语法的使用便可以轻松上手。 C# 语法与 Java 语法非常相似 &ndash; C# 是“大括号”语言，如 Java、C 和 C++。 在许多方面，C# 语法读起来像是 Java 语法的超集，但是有一些重命名和新增的关键字。

可以在 C# 中找到 Java 的许多主要特征：

- 面向对象的基于类的编程

- 强类型化

- 支持接口

- 泛型

- 垃圾回收

- 运行时编译

Java 和 C# 都被编译为中间语言，在托管执行环境中运行。 C# 和 Java 都是静态类型，这两种语言将字符串视为不可变类型。
这两种语言使用单根类层次结构。 和 Java 一样，C# 仅支持单一继承，不支持全局方法。
在这两种语言中，对象使用 `new` 关键字在堆上创建而成，当不再使用对象时，它们会被作为垃圾回收处理。 这两种语言提供带 `try`/`catch` 语义的正式异常处理支持。 两者都提供线程管理和同步支持。

但是，Java 和 C# 之间存在很多不同。 例如：

- Java（在 Android 上使用）不支持隐式类型的局部变量（C# 支持 `var` 关键字）。

- 在 Java 中，可以仅按值传递参数，而在 C# 中，你可以按引用以及值进行传递。 （C# 提供 `ref` 和 `out` 关键字，用于按引用传递参数；Java 中无此类等效项）。

- Java 不支持预处理器指令，如 `#define`。

- Java 不支持无符号整数类型，而 C# 提供无符号整数类型，如 `ulong`、`uint`、`ushort` 和 `byte`。

- Java 不支持运算符重载；在 C# 中，可以重载运算符和转换。

- 在 Java `switch` 语句中，代码可以贯穿到下一个 switch 部分，但在 C# 中，每个 `switch` 部分的结尾必须终止 swith（每个部分的结尾必须以 `break` 语句结束）。

- 在 Java 中，指定由带 `throws` 关键字的方法引发的异常，但 C# 没有检查异常的概念 &ndash;`throws` 关键字在 C# 中不受支持。

- C# 支持语言集成查询 (LINQ)，这样就可以使用保留的字 `from`、`select` 和 `where`，以便以类似于数据库查询的方式编写针对集合的查询。

当然，与本文所讨论的相比，C# 和 Java 之间还有更多的区别。 此外，Java 和 C# 将继续演进（例如，Android 工具链中尚不存在的 Java 8，它支持 C# 样式的 lambda 表达式），因此这些差异会随时间而变化。 本文仅概述了首次使用 Xamarin.Android 的 Java 开发人员当前遇到的最重要的差异。

- [从 Java 到 C# 开发](#fundamentals)介绍了 C# 和 Java 之间的基本区别。

- [面向对象的编程功能](#oopfeatures)概述了两种语言之间最重要的面向对象的功能区别。

- [关键字差异](#keywords)提供了一个表格，其中包含有效的关键字等效项、仅限 C# 的关键字以及指向 C# 关键字定义的链接。

C# 为 Xamarin.Android 提供了许多主要功能，Java 开发人员当前尚不可在 Android 上使用这些功能。 这些功能可帮助你在更短的时间内编写出更好的代码：

- [属性](#properties) &ndash; 可通过 C# 属性系统直接安全地访问成员变量，而无需编写 setter 和 getter 方法。

- [Lambda 表达式](#lambdas) &ndash; 在 C# 中，你可以使用匿名方法（也称为“lambda”  ）更简洁、更高效地表示你的功能。 你可以避免编写一次性对象的开销，并且可以向某个方法传递本地状态而无需添加参数。

- [事件处理](#events) &ndash; C# 为事件驱动的编程  提供语言级别支持，对象可以进行注册，以便在出现感兴趣的事件时接收通知。 `event` 关键字定义发布服务器类可用于通知事件订阅者的多播广播机制。

- [异步编程](#async) &ndash; C# 的异步编程功能 (`async`/`await`) 使应用保持响应状态。
    此功能的语言级别支持使异步编程轻松实现，且不容易出错。

最后，Xamarin 允许你通过已知的绑定  技术来[利用现有的 Java 资产](#interop)。 你可以通过使用 Xamarin 的自动绑定生成器，从 C# 调用现有的 Java 代码、框架和库。 若要执行此操作，只需在 Java 中创建静态库，并通过绑定将其公开到 C#。

> [!NOTE]
> Android 编程使用支持所有 Java 7 功能[和一部分 Java 8 功能](https://developer.android.com/studio/write/java8-support.html)的 Java 语言的特定版本。
>
> 此页上提到的某些功能（例如，C# 中的 `var` 关键字）在较新版本的 Java（例如，[Java 10 中的 `var`](https://developer.oracle.com/java/jdk-10-local-variable-type-inference.html)）中提供，但仍不可用于 Android 开发人员。

<a name="fundamentals"></a>

## <a name="going-from-java-to-c-development"></a>从 Java 到 C# 开发

以下各节概述了 C# 和 Java; 之间的基本“入门”差异；后面的部分将介绍这些语言之间面向对象的差异。

### <a name="libraries-vs-assemblies"></a>库与程序集

Java 通常将相关类打包到 .jar  文件中。 然而，在 C# 和 .NET 中，预编译代码的可重用位将打包到程序集  ，通常打包为 .dll  文件。 程序集是部署 C#/.NET 代码的单位，每个程序集通常与 C# 项目相关联。 程序集包含在运行时实时编译的中间代码 (IL)。

有关程序集的详细信息，请参阅[程序集和全局程序集缓存](/dotnet/csharp/programming-guide/concepts/assemblies-gac/)主题。

### <a name="packages-vs-namespaces"></a>包与命名空间

C# 使用 `namespace` 关键字对相关类型进行分组；这类似于 Java 的 `package` 关键字。 通常情况下，Xamarin.Android 应用将驻留在为该应用创建的命名空间中。 例如，下面的 C# 代码声明天气报告应用的 `WeatherApp` 命名空间包装器：

```csharp
namespace WeatherApp
{
    ...
```

### <a name="importing-types"></a>导入类型

在使用外部命名空间中定义的类型时，使用 `using` 语句导入这些类型（近似于 Java `import` 语句）。 在 Java 中，你可能使用以下语句导入单个类型：

```java
import javax.swing.JButton
```

可能会使用类似于下面的语句导入整个 Java 包：

```java
import javax.swing.*
```

C# `using` 语句的工作方式非常类似，但它允许导入整个包而无需指定通配符。 例如，在 Xamarin.Android 源文件的开头，通常会看到一系列 `using` 语句，如以下示例所示：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Net;
using System.IO;
using System.Json;
using System.Threading.Tasks;
```

这些语句从 `System`、`Android.App`、`Android.Content` 等命名空间导入功能。

### <a name="generics"></a>泛型

Java 和 C# 支持泛型  ，这是允许你在编译时插入不同类型中的占位符。 但是，泛型在 C# 中的工作方式略有不同。 在 Java 中，[类型擦除](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html)仅在编译时提供类型信息，而不是在运行时。 与此相反，.NET 公共语言运行时 (CLR) 对泛型类型提供显式支持，这意味着 C# 有权在运行时访问类型信息。 在日常 Xamarin.Android 开发中，这一区别的重要性通常不明显，但如果使用的是[反射](/dotnet/csharp/programming-guide/concepts/reflection)，你将依赖此功能在运行时访问类型信息。

在 Xamarin.Android 中，通常会看到泛型方法 `FindViewById` 用于获取对布局控件的引用。 此方法接受一个泛型类型参数，指定要查找的控件类型。 例如：

```csharp
TextView label = FindViewById<TextView> (Resource.Id.Label);
```

在此代码示例中，`FindViewById` 将布局中定义的 `TextView` 控件作为标签  引用，然后将其作为 `TextView` 类型返回。

有关泛型的更多信息，请参阅[泛型](/dotnet/csharp/programming-guide/generics/index)主题。
请注意，Xamarin.Android 对泛型 C# 类的支持存在一些限制；有关详细信息，请参阅[限制](~/android/internals/limitations.md)。

<a name="oopfeatures"></a>

## <a name="object-oriented-programming-features"></a>面向对象的编程功能

Java 和 C# 使用非常相似的面向对象的编程惯用语：

- 所有类最终都派生自单个根对象 &ndash; 所有 Java 对象均派生自 `java.lang.Object`，而所有 C# 对象均派生自 `System.Object`。

- 类实例为引用类型。

- 访问某个实例的属性和方法时，可以使用“`.`”运算符。

- 所有类实例均通过 `new` 运算符在堆上创建。

- 由于这两种语言都使用垃圾回收，因此无法显式释放未使用的对象（即，不像 C++ 一样存在 `delete` 关键字）。

- 可以通过继承来扩展类，并且这两种语言只允许每种类型有单个基类。

- 可以定义接口，并且一个类可以继承自（即，实现）多个接口定义。

但是，也有一些重要的区别：

- Java 有两个 C# 不支持的强大功能：匿名类和内部类。 （但是，C# 允许嵌套类定义 &ndash; C# 的嵌套类就像 Java 的静态嵌套类。）

- C# 支持 C 样式结构类型 (`struct`)，而 Java 不支持。

- 在 C# 中，可以在单独的源文件中通过使用 `partial` 关键字实现类定义。

- C# 接口不能声明字段。

- C# 使用 C++ 样式析构函数语法来表示终结器。 该语法不同于 Java 的 `finalize` 方法，但语义大致相同。 （请注意，在 C# 中，析构函数自动调用基类析构函数 &ndash;而在 Java 中则使用对 `super.finalize` 的显式调用。）

### <a name="class-inheritance"></a>类继承

要扩展 Java 中的类，可以使用 `extends` 关键字。 要扩展 C# 中的类，可以使用冒号 (`:`) 以指示派生。 例如，在 Xamarin.Android 应用中，通常会看到类似于以下代码段的类派生：

```csharp
public class MainActivity : Activity
{
    ...
```

在此示例中，`MainActivity` 继承自 `Activity` 类。

要在 Java 中声明对接口的支持，可以使用 `implements` 关键字。 但是，在 C# 中，只需将接口名称添加到从中继承的类列表，如以下代码段所示：

```csharp
public class SensorsActivity : Activity, ISensorEventListener
{
    ...
```

在此示例中，`SensorsActivity` 继承自 `Activity`，并实现 `ISensorEventListener` 接口中声明的功能。 请注意，接口列表必须晚于基类（否则将收到一个编译时错误）。 按照约定，C# 接口名称前有一个大写“I”；这样便能确定哪些类是接口，而无需 `implements` 关键字。

如果想要阻止类在 C# 中进一步子类化，请在类名前添加 `sealed` &ndash; 在 Java 中，在类名前添加 `final`。

有关 C# 类定义的更多信息，请参阅[类](/dotnet/csharp/programming-guide/classes-and-structs/classes)和[继承](/dotnet/csharp/programming-guide/classes-and-structs/inheritance)主题。

### <a name="properties"></a>属性

在 Java 中，赋值函数方法 (setter) 和检查器方法 (getter) 通常用于控制在隐藏和保护类成员不受外部代码影响时如何对这些成员进行更改。 例如，Android `TextView` 类提供 `getText` 和 `setText` 方法。 C# 提供相似但更直接的机制，称为“属性”  。
C# 类用户可以使用与访问字段相同的方式来访问属性，但是每次访问实际上会导致对调用方透明的方法调用。 此“隐秘”方法会导致一些副作用，如设置其他值、执行转换，或更改对象状态。

属性通常用于访问和修改 UI（用户界面）对象成员。 例如：

```csharp
int width = rulerView.MeasuredWidth;
int height = rulerView.MeasuredHeight;
...
rulerView.DrawingCacheEnabled = true;
```

在此示例中，可通过访问 `rulerView` 对象的 `MeasuredWidth` 和 `MeasuredHeight` 属性从中读取宽度和高度值。 在读取这些属性时，其关联（但隐藏）字段值中的值会在后台提取，并返回给调用方。 `rulerView` 对象可以在一个度量单位（如像素）中存储宽度和高度的值，并在访问 `MeasuredWidth` 和 `MeasuredHeight` 属性时将这些值实时转换为不同的度量单位（如毫米）。

`rulerView` 对象还有一个名为 `DrawingCacheEnabled` 的属性 &ndash; 示例代码将此属性设置为 `true` 以启用 `rulerView` 中的绘制缓存。 在后台，关联的隐藏字段已更新为新值，`rulerView` 状态的可能其他方面已修改。 例如，当 `DrawingCacheEnabled` 设置为 `false` 时，`rulerView` 还可能清除对象中累积的任何绘制缓存信息。

对属性的访问权限可以是读/写、只读或只写。 此外，可以使用不同的访问修饰符进行读取和写入。 例如，可以定义一个具有公共读取访问权限和专有写入访问权限的属性。

有关 C# 属性的详细信息，请参阅[属性](/dotnet/csharp/programming-guide/classes-and-structs/properties)主题。

### <a name="calling-base-class-methods"></a>调用基类方法

若要在 C# 中调用基类构造函数，可以使用后跟关键字 `base` 的冒号 (`:`) 和初始化表达式列表；此 `base` 构造函数调用会被立即置于派生的构造函数参数列表后。 在进入派生构造函数时，将调用基类构造函数；编译器会在方法主体开头将此调用插入到基构造函数。 下面的代码段演示了从 Xamarin.Android 应用中的派生构造函数调用的基构造函数：

```csharp
public class PictureLayout : ViewGroup
{
    ...
    public PictureLayout (Context context)
           : base (context)
    {
        ...
    }
    ...
}

```

在此示例中，`PictureLayout` 类派生自 `ViewGroup` 类。 此示例中所示的 `PictureLayout` 构造函数接受 `context` 参数，并通过 `base(context)` 调用将其传递给 `ViewGroup` 构造函数。

若要在 C# 中调用基类方法，请使用 `base` 关键字。 例如，Xamarin.Android 应用通常调用基方法，如下所示：

```csharp
public class MainActivity : Activity
{
    ...
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
```

在这种情况下，由派生类 (`MainActivity`) 定义的 `OnCreate` 方法调用基类 (`Activity`) 的 `OnCreate` 方法。

### <a name="access-modifiers"></a>访问修饰符

Java 和 C# 都支持 `public`、`private` 和 `protected` 访问修饰符。 但是，C# 支持以下两个其他访问修饰符：

- `internal` &ndash; 只能在当前程序集内访问类成员  。

- `protected internal` &ndash; 可在定义程序集、定义类和派生类中访问类成员（程序集内部和外部的派生类都具有访问权限）  。

有关 C# 访问修饰符的详细信息，请参阅[访问修饰符](/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)主题。

### <a name="virtual-and-override-methods"></a>虚拟和重写方法

Java 和 C# 均支持多形性  ，即能够以相同方式处理相关对象。 在这两种语言中，可以使用基类引用来引用派生类对象，且派生类的方法可以重写基类的方法。 两种语言都具有虚拟  方法的概念，此基类方法旨在替换为派生类中的方法。
和 Java 一样，C# 支持 `abstract` 类和方法。

但是，Java 和 C# 之间在如何声明和重写虚拟方法方面存在一些差异：

- 默认情况下，在 C# 中，方法是非虚拟的。 父类必须显式标记要使用 `virtual` 关键字重写的方法。 与此相反，Java 中的所有方法都默认为虚拟方法。

- 要防止在 C# 中重写方法，只需删除 `virtual` 关键字。 与此相反，Java 使用 `final` 关键字将方法标记为“不允许重写”。

- C# 派生类必须使用 `override` 关键字显式指示正在重写虚拟基类方法。

有关 C# 对多形性支持的详细信息，请参阅[多形性](/dotnet/csharp/programming-guide/classes-and-structs/polymorphism)主题。

<a name="lambdas"></a>

## <a name="lambda-expressions"></a>Lambda 表达式

可以通过 C# 创建一个闭包  ：内联匿名方法，可以访问它们包含在其中的方法的状态。
使用 lambda 表达式，你可以编写较少的代码行来实现相同功能，你可能已使用更多的代码行在 Java 中实现该功能。

Lambda 表达式使你可以跳过像在 Java 中创建一次性类或匿名类时会涉及到的其他繁琐操作 &ndash; 而只需编写方法代码内联的业务逻辑即可。 此外，由于 lambda 对周围方法中的变量具备访问权限，因此不必创建用于将状态传递到方法代码的长参数列表。

在 C# 中，lambda 表达式通过 `=>` 运算符创建而成，如下所示：

```csharp
(arg1, arg2, ...) => {
    // implementation code
};
```

在 Xamarin.Android 中，lambda 表达式通常用于定义事件处理程序。 例如：

```csharp
button.Click += (sender, args) => {
    clickCount += 1;    // access variable in surrounding code
    button.Text = string.Format ("Clicked {0} times.", clickCount);
};
```

在此示例中，lambda 表达式代码（在大括号内的代码）递增点击数，并更新 `button` 文本以显示点击数。 此 lambda 表达式在 `button` 对象中注册为点击按钮时要调用的点击事件处理程序。 （下文对事件处理程序进行了更详细的说明。）在此简单示例中，lambda 表达式代码不使用 `sender` 和 `args` 参数，但 lambda 表达式需要这些参数来满足事件注册的方法签名要求。 实质上，C# 编译器将 lambda 表达式转换为一个匿名方法，在发生按钮点击事件时会调用该方法。

有关 C# 和 lambda 表达式的详细信息，请参阅[Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)主题。

<a name="events"></a>

## <a name="event-handling"></a>事件处理

事件  是在对象出现一些有趣的内容时，该对象通知注册订阅者所采用的一种方式。 与 Java 不同，订阅者通常在 Java 中实现 `Listener` 接口，其中包含一个回调方法，而 C# 则通过委托  为事件处理提供语言级别支持。 委托  类似于面向对象的类型安全函数指针 &ndash; 它封装对象引用和方法令牌。 如果客户端对象想要订阅事件，它将创建委托并将该委托传递给通知对象。
发生事件时，通知对象将调用委托对象所表示的方法，以通知订阅事件的客户端对象。 在 C# 中，事件处理程序实际上就是通过委托调用的方法。

有关委托的详细信息，请参阅[委托](/dotnet/csharp/programming-guide/delegates/index)主题。

在 C# 中，事件为多播  ；也就是说，发生某事件时可以通知多个侦听器。 当你考虑 Java 和 C# 事件注册之间的语法差异时，将观察到这种差异。 在 Java 中，可以调用 `SetXXXListener` 来注册事件通知；在 C# 中，可以使用 `+=` 运算符来注册事件通知，方法是将委托“添加”到事件侦听器列表。
在 Java 中，可以调用 `SetXXXListener` 以取消注册，而在 C# 中，则使用 `-=`将委托从侦听器列表中“去除”。

在 Xamarin.Android 中，事件通常用于在用户对 UI 控件执行某操作时通知对象。 通常，UI 控件将具有使用 `event` 关键字定义的成员；将委托附加到这些成员，以便从该 UI 控件中订阅事件。

订阅事件：

1. 创建一个委托对象，在事件发生时引用要调用的方法。

2. 使用 `+=` 运算符将委托附加到你订阅的事件。

下面的示例定义了一个委托（显式使用 `delegate` 关键字）以订阅按钮点击事件。
此按钮点击处理程序将启动新的活动：

```csharp
startActivityButton.Click += delegate {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

但是，你还可以使用 lambda 表达式来注册事件，一起跳过 `delegate` 关键字。 例如：

```csharp
startActivityButton.Click += (sender, e) => {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

在此示例中，`startActivityButton` 对象具有一个事件，该事件期望带有特定方法签名的委托：该事件接受发件人和事件参数并返回 void。 但是，因为我们不希望在显式定义此类委托或其方法时遇到问题，我们声明具有 `(sender, e)` 的方法签名并使用 lambda 表达式来实现事件处理程序的主体。
请注意，必须声明此参数列表，即使不使用 `sender` 和 `e` 参数。

务必记住，你可以取消订阅委托（通过 `-=` 运算符），但不能取消订阅 lambda 表达式 &ndash; 试图执行此操作可能会导致内存泄漏。 只有当处理程序不从事件中取消订阅时，才能使用 lambda 形式的事件注册。

通常，lambda 表达式用于声明 Xamarin.Android 代码中的事件处理程序。 此用于声明事件处理程序的速记方法乍一看可能比较晦涩，但在你写入和读取代码时它将帮你节省大量的时间。 随着熟悉程度的增加，你将习惯于识别此模式（这在 Xamarin.Android 代码中经常出现），可以花更多的时间思考应用程序的业务逻辑，而不需要花太多时间在语法开销上。

<a name="async"></a>

## <a name="asynchronous-programming"></a>异步编程

异步编程  是改进应用程序总体响应能力的一种方法。 当应用程序的某些部分被冗长的操作所阻塞时，异步编程功能使应用代码的其余部分得以继续运行。
如果应用程序未以异步方式编写，访问 Web、处理图像和读取/写入文件等操作示例可能会导致整个应用看上去像冻结了一样。

C# 包含对通过 `async` 和 `await` 关键字进行异步编程的语言级别支持。 通过这些语言功能，可以非常方便地编写代码来执行长时间运行的任务，而不会阻止应用的主线程。 简而言之，可以在某个方法上使用 `async` 关键字来指示方法中以异步方式运行，且不会阻止调用方线程的代码。 在调用标记有 `async` 的方法时使用 `await` 关键字。 编译器会将 `await` 解释为一个点，其中方法执行将移至后台线程（会向调用方返回一个任务）。 完成此任务后，将在代码中 `await` 点处的调用方线程上恢复代码执行，从而返回 `async` 调用的结果。 按照约定，异步运行的方法的名称带有 `Async` 后缀。

在 Xamarin.Android 应用中，`async` 和 `await` 通常用于释放 UI 线程，以便在后台任务中发生长时间运行的操作时，它可以响应用户输入（如点击“取消”  按钮）。

在以下示例中，按钮点击事件处理程序将导致异步操作从 Web 下载映像：

```csharp
downloadButton.Click += downloadAsync;
...
async void downloadAsync(object sender, System.EventArgs e)
{
    webClient = new WebClient ();
    var url = new Uri ("http://photojournal.jpl.nasa.gov/jpeg/PIA15416.jpg");
    byte[] bytes = null;

    bytes = await webClient.DownloadDataTaskAsync(url);

    // display the downloaded image ...
```

在此示例中，当用户单击 `downloadButton` 控件时，`downloadAsync` 事件处理程序将创建 `WebClient` 对象和 `Uri` 对象，以便从指定 URL 中提取映像。 接下来，它使用此 URL 调用 `WebClient` 对象的 `DownloadDataTaskAsync` 方法来检索映像。

请注意，`downloadAsync` 的方法声明以 `async` 关键字开头，指示它将以异步方式运行，并返回一个任务。 另请注意，对 `DownloadDataTaskAsync` 的调用前面为 `await` 关键字。 应用将事件处理程序执行（从显示 `await` 的点开始）移至后台线程，直到 `DownloadDataTaskAsync` 完成并返回。
同时，应用的 UI 线程仍可以响应用户输入，并激发其他控件的事件处理程序。 当 `DownloadDataTaskAsync` 完成时（这可能需要几秒钟），执行将恢复，其中 `bytes` 变量设置为调用 `DownloadDataTaskAsync` 的结果，事件处理程序代码的其余部分在调用方的 (UI) 线程上显示下载的映像。

有关 C# 中 `async`/`await` 的说明，请参阅[使用 Async 和 Await 的异步编程](/dotnet/csharp/async)主题。
有关 Xamarin 对异步编程功能的支持的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

<a name="keywords"></a>

## <a name="keyword-differences"></a>关键字差异

Java 中使用的很多语言关键字也在 C# 中使用。 还有大量的 Java 关键字在 C# 中具有以不同方式命名的等效对应项，如下表所示：

|Java|C#|描述|
|---|---|---|
|`boolean`|[bool](/dotnet/csharp/language-reference/keywords/bool)|用于声明布尔值 true 和 false。|
|`extends`|`:`|先于要从中继承的类和接口。|
|`implements`|`:`|先于要从中继承的类和接口。|
|`import`|[using](/dotnet/csharp/language-reference/keywords/using)|从命名空间导入类型，还可用于创建命名空间别名。|
|`final`|[sealed](/dotnet/csharp/language-reference/keywords/sealed)|防止类派生；防止方法和属性在派生类中被重写。|
|`instanceof`|[is](/dotnet/csharp/language-reference/keywords/is)|评估对象与给定类型是否兼容。|
|`native`|[extern](/dotnet/csharp/language-reference/keywords/extern)|声明外部实现的方法。|
|`package`|[namespace](/dotnet/csharp/language-reference/keywords/namespace)|声明一组相关对象的作用域。|
|`T...`|[params T](/dotnet/csharp/language-reference/keywords/params)|指定采用可变数目的参数的方法参数。|
|`super`|[base](/dotnet/csharp/language-reference/keywords/base)|用于从派生类中访问父类的成员。|
|`synchronized`|[lock](/dotnet/csharp/language-reference/keywords/lock-statement)|使用锁获取和发布包装代码的关键部分。|

此外，还有很多关键字是 C# 所特有的，且在 Android 上使用的 Java 中没有对应项。 Xamarin.Android 代码通常使用下面的 C# 关键字（读取 Xamarin.Android [示例代码](/samples/browse/?products=xamarin&term=Xamarin.Android)时，可使用此表作为参考）:

|C#|描述|
|---|---|
|[as](/dotnet/csharp/language-reference/keywords/as)|在兼容的引用类型或可以为 null 的类型之间执行转换。|
|[async](/dotnet/csharp/language-reference/keywords/async)|指定方法或 lambda 表达式为异步。|
|[await](/dotnet/csharp/language-reference/keywords/await)|挂起方法执行，直到任务完成。|
|[byte](/dotnet/csharp/language-reference/keywords/byte)|无符号的 8 位整数类型。|
|[delegate](/dotnet/csharp/language-reference/keywords/delegate)|用于封装方法或匿名方法。|
|[enum](/dotnet/csharp/language-reference/keywords/enum)|声明枚举，这是一组命名的常数。|
|[event](/dotnet/csharp/language-reference/keywords/event)|声明发布者类中的一个事件。|
|[fixed](/dotnet/csharp/language-reference/keywords/fixed-statement)|防止变量被重定位。|
|`get`|定义检索属性值的访问器方法。|
|[in](/dotnet/csharp/language-reference/keywords/in-generic-modifier)|使一个参数接受泛型接口中派生程度更小的类型。|
|[object](/dotnet/csharp/language-reference/keywords/object)|.NET Framework 中 Object 类型的别名。|
|[out](/dotnet/csharp/language-reference/keywords/out)|参数修饰符或泛型类型参数声明。|
|[override](/dotnet/csharp/language-reference/keywords/override)|扩展或修改继承成员的实现。|
|[partial](/dotnet/csharp/language-reference/keywords/partial-method)|声明要拆分为多个文件的定义，或者将一个方法定义从其实现中分离出来。|
|[readonly](/dotnet/csharp/language-reference/keywords/readonly)|声明只能在声明时分配或由类构造函数分配的类成员。|
|[ref](/dotnet/csharp/language-reference/keywords/ref)|通过引用（而非值）来传递参数。|
|[set](/dotnet/csharp/language-reference/keywords/set)|定义设置属性值的访问器方法。|
|[string](/dotnet/csharp/language-reference/keywords/string)|.NET Framework 中 String 类型的别名。|
|[struct](/dotnet/csharp/language-reference/keywords/struct)|封装一组相关变量的值类型。|
|[typeof](/dotnet/csharp/language-reference/keywords/typeof)|获取对象类型。|
|[var](/dotnet/csharp/language-reference/keywords/var)|声明一个隐式类型局部变量。|
|[value](/dotnet/csharp/language-reference/keywords/value)|引用客户端代码想要分配到属性的值。|
|[virtual](/dotnet/csharp/language-reference/keywords/virtual)|允许在派生类中重写方法。|

<a name="interop"></a>

## <a name="interoperating-with-existing-java-code"></a>与现有的 Java 代码交互操作

如果你有不希望转换为 C# 的现有 Java 功能，可以在 Xamarin.Android 应用程序中通过两种技术重复使用现有的 Java 库：

- 创建 Java 绑定库  &ndash; 通过此方法，你可以使用 Xamarin 工具生成围绕 Java 类型的 C# 包装器。 这些包装器称为“绑定”  。 因此，Xamarin.Android 应用程序可以通过调用这些包装器来使用 .jar  文件。

- Java 本机接口 &ndash; Java 本机接口 (JNI) 是一个框架，使 C# 应用可以调用 Java 代码或者由 Java 代码调用   。

有关这些技术的详细信息，请参阅 [Java 集成概述](~/android/platform/java-integration/index.md)。

## <a name="further-reading"></a>其他阅读材料

MSDN [C# 编程指南](/dotnet/csharp/programming-guide/)是开始学习 C# 编程语言的绝佳方式，你可以使用 [C# 参考](/dotnet/csharp/language-reference/)查找特定的 C# 语言功能。

同样，Java 知识要求至少像了解 Java 语言一样了解 Java 类库，C# 实践知识则要求熟悉 .NET framework。 Microsoft 的[移动到面向 Java 开发人员的 C# 和 .NET Framework](https://www.microsoft.com/download/details.aspx?id=6073) 学习数据包是从 Java 角度了解有关 .NET Framework 详细信息的非常好的途径（同时可以更加深入地了解 C#）。

当你准备好在 C# 中处理第一个 Xamarin.Android 项目时，[Hello，Android](~/android/get-started/hello-android/index.md) 系列可以帮助你生成第一个 Xamarin.Android 应用程序，并进一步加强你对通过 Xamarin 开发 Android 应用程序的基础知识的了解。

## <a name="summary"></a>总结

本文从 Java 开发人员的角度提供有关 Xamarin.Android C# 编程环境的简介。 它指出 C# 和 Java 之间的相似之处，同时解释了它们的实际差异。 它介绍了程序集和命名空间，说明如何导入外部类型，并概述了访问修饰符、泛型、类派生、调用基类方法、方法重写以及事件处理方面的区别。 它介绍了在 Java 中不可用的 C# 功能，例如属性、`async`/`await` 异步编程、lambda、C# 委托和 C# 事件处理系统。 它包含一个涵盖重要 C# 关键字的表格，说明如何与现有的 Java 库进行交互操作，并提供用于进一步研究的相关文档链接。

## <a name="related-links"></a>相关链接

- [Java 集成概述](~/android/platform/java-integration/index.md)
- [C# 编程指南](/dotnet/csharp/programming-guide/)
- [C# 参考](/dotnet/csharp/language-reference/index)
- [移动到面向 Java 开发人员的 C# 和 .NET Framework](https://www.microsoft.com/download/details.aspx?id=6073)