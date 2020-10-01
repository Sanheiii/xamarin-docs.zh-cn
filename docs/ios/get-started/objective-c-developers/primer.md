---
title: 面向 Objective-C 开发人员的 C# 入门
description: 本文档为 Objective-C 开发人员介绍 C#。 通过检查协议和接口、类别和扩展方法、框架和程序集、选择器和命名参数等来比较和对比这两种语言。
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: acde83ee88ab17884cadd375f83efee8afb0385e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433383"
---
# <a name="c-primer-for-objective-c-developers"></a>面向 Objective-C 开发人员的 C# 入门

_Xamarin.iOS 允许用 C# 编写的平台无关的代码跨平台共享。但是，现有 iOS 应用程序可能想要利用已创建的 Objective-C 代码。本文面向希望转移到 Xamarin 和 C# 语言的 Objective-C 开发人员提供一个快速入门。_

用 Objective-C 开发的 iOS 和 macOS 应用程序可在无需平台专属代码的位置利用 C# 从 Xamarin 中受益，从而允许在非 Apple 设备上使用此类代码。 然后，可通过跨平台的方式使用诸如 Web 服务、JSON 和 XML 解析，以及自定义算法之类的内容。

若要在维护现有 Objective-C 资产的同时利用 Xamarin，前者可以用 Xamarin 中的技术（称为绑定）向 C# 公开，该技术可将 Objective-C 代码展示给托管的 C# 环境。 另外，如果需要，代码也可以逐行移植到 C# 中。 但是，不管采用何种方法，无论是绑定还是移植，均需要对 Objective-C 和 C# 有所了解，才能使用 Xamarin.iOS 有效地利用现有 Objective-C 代码。

## <a name="objective-c-interop"></a>Objective-C 互操作

目前没有任何支持的机制来在 C# 中使用可从 Objective-C 调用的 Xamarin.iOS 创建库。 其主要原因是除了绑定之外，还需要 Mono 运行时。 但是，你仍然可以在 Objective-C 中创建大部分逻辑，包括用户界面。 为此，将 Objective-C 代码包装在库中，并创建一个到该库的绑定。 需要 Xamarin.iOS 来启动应用程序（这意味着它必须创建 `Main` 入口点）。 之后，Objective-C 中的任何其他逻辑可以通过绑定（或通过 P/Invoke）公开给 C#。 这样，你可以在 Objective-C 中保留平台特定的逻辑，并在 C# 中开发平台无关的部分。

本文重点介绍了这两种语言的一些关键相似之处，并对两者之间的若干差异进行了对比，以便在使用 Xamarin.iOS 移至 C# 时，无论是绑定到现有的 Objective-C 代码，还是将其移植到 C# 都可作为入门知识加以了解。

有关创建绑定的详细信息，请参阅[绑定 Objective-C](~/ios/platform/binding-objective-c/index.md) 中的其他文档。

## <a name="language-comparison"></a>语言比较

从语法和运行时角度来看，Objective-C 和 C# 是非常不同的两种语言。 Objective-C 是动态语言，使用消息传递方案，而 C# 为静态型的语言。 在语法智能方面，Objective-C 就像 Smalltalk，而 C# 的大部分基本语法派生自 Java，尽管其最近几年已经成熟到可以包含 Java 之外的许多功能。

也就是说，Objective-C 和 C# 均有的几种语言特性在功能上是相似的。 在从 C# 创建到 Objective-C 代码的绑定时或将 Objective-C 移植到 C# 时，了解这些相似之处是非常有用的。

### <a name="protocols-vs-interfaces"></a>协议与接口

Objective C 和 C# 均为单继承语言。 但是，这两种语言都支持在给定类中实现多个接口。 在 Objective-C 中，这些逻辑接口称为“协议”  ，而在 C# 中，它们称为“接口”  。 在实现智能方面，C# 接口和 Objective-C 协议之间的主要区别是后者可以有可选的方法。 有关详细信息，请参阅[事件、委托和协议](~/ios/app-fundamentals/delegates-protocols-and-events.md)文章。

### <a name="categories-vs-extension-methods"></a>类别与扩展方法

Objective-C 允许将方法添加到你可能没有让实现代码对其使用“类别”  的类。 在 C# 中，类似的概念可以通过所谓的*扩展方法*获得。

扩展方法可使你将静态方法添加到类，其中 C# 中的静态方法类似于 Objective-C 中的类方法。 例如，下面的代码将名为 `ScrollToBottom` 的方法添加到 `UITextView` 类，该类又是一个从 UIKit 绑定到 Objective-C `UITextView` 类的托管类：

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

然后，当在代码中创建 `UITextView` 的实例时，该方法将出现在自动完成列表中，如下所示：

 ![自动完成列表中可用的方法](primer-images/01-extensionmethodintellisense.png)

当调用扩展方法时，实例将传递给参数，如本示例中的 `textView`。

### <a name="frameworks-vs-assemblies"></a>框架与程序集

Objective-C 将相关类打包在称为框架的特定目录中。 但是，在 C# 和 .NET 中，程序集用于提供预编译代码的可重用位。 在 iOS 以外的环境中，程序集将包含在运行时实时 (JIT) 编译的中间语言代码 (IL)。 但是，Apple 不允许在 App Store 上发布的 iOS 应用程序中执行 JIT 编译的代码。 因此，针对使用 Xamarin 的 iOS 的 C# 代码是提前编译 (AOT) 代码，生成单个 Unix 可执行文件以及包含在应用程序捆绑包中的元数据文件。

### <a name="selectors-vs-named-parameters"></a>选择器与命名的参数

就其本质而言，Objective-C 方法固有包含选择器中的参数名称。 例如，诸如 `AddCrayon:WithColor:` 之类的选择器明确了代码中所用每个参数的含义。 C# 也可以选择支持命名的参数。

例如，C# 中使用命名参数的类似代码将是：

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

虽然 C# 在语言版本 4.0 中添加了此支持，但实际上并不经常使用。 但是，如果你想要在代码中明确，则可以随时使用此支持。

### <a name="headers-and-namespaces"></a>标头和命名空间

作为 C 的超集，Objective-C 使用与实现文件分开的公共声明的标头。 C# 不使用标头文件。 与 Objective-C 不同，C# 代码包含在命名空间中。 如果你想要包含某些名称空间中可用的代码，可以在实现文件的顶部添加 using 指令，或者使用完整的名称空间来限定该类型。

例如，下面的代码包括 `UIKit` 命名空间，使该命名空间中的每个类均可用于实现：

```csharp
using UIKit;
namespace MyAppNamespace
{
    // implementation of classes
}
```

此外，上述代码中的命名空间关键字将设置用于实现文件本身的命名空间。 如果多个实现文件共享相同的命名空间，也无需将该命名空间包括在 using 指令中，因为它是隐含的。

### <a name="properties"></a>属性

Objective-C 和 C# 都具有属性的概念，以提供关于访问器方法的高级抽象。 在 Objective-C 中，@property 编译器指令用于有效地生成访问器方法。 与此相反，C# 中包含对语言本身中的属性的支持。 可以使用访问支持字段的较长样式或使用较短的自动属性语法来实现 C# 属性，如以下示例中所示：

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Static 关键字

static 关键字在 Objective-C 和 C# 之间具有截然不同的含义。 在 Objective-C 中，静态函数用于将函数的作用域限制为当前文件。 但是，在 C# 中，作用域通过 public、private 和 internal 关键字进行维护。

当 static 关键字应用于 Objective-C 中的变量时，变量会在函数调用之间维护其值。

C# 也具有 static 关键字。 当应用于某个方法时，它会有效地执行与 `+` 修饰符在 Objective-C 中执行的相同操作。 也就是说，它会创建一个类方法。 同样，当应用于字段、属性和事件等的其他构造时，它将创建已声明的部分类型，而不是该类型的任何实例。 你还可以创建静态类，其中在类中定义的所有方法也必须是静态的。

### <a name="nsarray-vs-list-initialization"></a>NSArray 与列表初始化

Objective-C 现在包含用于 `NSArray` 的文本语法，使初始化更加容易。 但是，C# 具有称为 `List` 的更丰富类型，即“泛型”，这意味着列表所包含的类型可以由创建列表的代码提供（如 C++ 中的模板）。 此外，列表还支持自动初始化语法，如下所示：

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>块与Lambda 表达式

Objective-C 使用块创建闭包，可以在其中创建函数内联，以便使用其中包含的状态。 通过使用 lambda 表达式，C# 具有类似的概念。 在 C# 中，lambda 表达式通过 `=>` 运算符创建而成，如下所示：

```csharp
(args) => {
    //  implementation code
};
```

有关 lambda 表达式的详细信息，请参阅 Microsoft 的 [C# 编程指南](/dotnet/csharp/language-reference/operators/lambda-expressions)。

## <a name="summary"></a>总结

在本文中，将 Objective-C 和 C# 之间的各种语言特征进行了对比。 在某些情况下，它会调用两种语言之间存在的类似特征，例如块到 lambda 表达式，以及类别到扩展方法。 此外，还对比了两种语言存在的不同之处，例如 C# 中的命名空间和 static 关键字的含义。