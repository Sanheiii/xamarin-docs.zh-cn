---
title: 针对目标的 .NET 嵌入最佳做法-C
description: 本文档介绍了在目标 C 中使用 .NET 嵌入的各种最佳实践。 它讨论了公开托管代码子集、公开 chunkier API、命名等。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 7eccc83a28d0bac7b9ff15a46022942c7238c714
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457388"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>针对目标的 .NET 嵌入最佳做法-C

这是一个草稿，可能不与该工具当前支持的功能同步。 我们希望本文档单独发展，并最终与最终工具匹配，也就是说，我们将建议长期的最佳方法，而不是即时解决方法。

本文档中的大部分内容也适用于其他受支持的语言。 但提供的所有示例都是 c # 和目标-C。

## <a name="exposing-a-subset-of-the-managed-code"></a>公开托管代码的子集

生成的本机库/框架包含目标-C 代码，以调用公开的每个托管 Api。 您 (的 API 越多，使公共) 越大，本机 _粘连_ 库就会变得更大。

最好创建一个不同的小型程序集，以便仅向本机开发人员公开所需的 Api。 此外观还允许您更好地控制可见性、命名、错误检查 .。。生成的代码的。

## <a name="exposing-a-chunkier-api"></a>公开 chunkier API

需要支付从本机到托管 (和后) 的过渡价格。 因此，更好的做法是将 _块式（而不是_ 向本机开发人员）公开的 api，例如

**聊天**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**块式**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

由于转换的数量较小，因此性能将更好。 它还需要生成较少的代码，因此，这也会生成较小的本机库。

## <a name="naming"></a>命名

命名功能是计算机科学中的两个最难解决的问题之一，另一个则是缓存失效，而不是由1个错误引发。 但愿 .NET 嵌入会使你免受除命名以外的一切的防御。

### <a name="types"></a>类型

目标-C 不支持命名空间。 一般情况下，其类型以一个用于 Apple) 的 2 (或第三方的3个 (为第三) 方（例如 `UIView` 用于 UIKit 的视图，它表示框架）。

对于 .NET 类型，不能跳过命名空间，因为它会引入重复或混乱的名称。 这使得现有的 .NET 类型非常长，例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

使用方式类似于：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

但是，可以将类型重新公开为：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

使其更易于使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

即使是良好的 .NET 名称也可能不适合目标-C API。

目标-C 中的命名约定不同于 .NET (camel 大小写格式，而不是 pascal 大小写，更详细) 。
请参阅 [Cocoa 的编码指导原则](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

从目标-C 开发人员的角度来看，带有前缀的方法 `Get` 隐含您不拥有该实例，即 [get 规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名规则在 .NET GC 世界中没有匹配项;带有前缀的 .NET 方法 `Create` 在 .net 中的行为相同。 但对于目标为 C 的开发人员，这通常意味着拥有返回的实例，即 [创建规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>异常

.NET 中很常见的情况是经常使用异常来报告错误。 但是，它们速度慢，但在目标-C 中并不完全相同。 应尽可能将它们从目标-C 开发人员隐藏。

例如，.NET `Try` 模式将更易于从目标-C 代码使用：

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

对比

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>内部异常 `init*`

在 .NET 中，构造函数必须成功并返回 (_希望_) 有效的实例或引发异常。

与此相反， `init*` `nil` 当无法创建实例时，客观-C 允许返回。 这是许多 Apple 框架中使用的常见（而非一般）模式。 在某些其他情况下 `assert` ，可能会 (，并) 终止当前进程。

生成器对于生成的方法遵循相同的 `return nil` 模式 `init*` 。 如果引发了托管异常，则会使用) 打印 (， `NSLog` 并将其 `nil` 返回给调用方。

## <a name="operators"></a>运算符

目标-C 不允许在 c # 中重载运算符，因此这些运算符转换为类选择器。

在找到运算符重载时，将按照优先顺序生成["友好"](/dotnet/standard/design-guidelines/operator-overloads)命名方法，从而可以更轻松地使用 API。

重写运算符和/或的 `==` 类 `!=` 应同时重写标准等于 (对象) 方法。