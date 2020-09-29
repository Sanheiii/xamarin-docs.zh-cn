---
title: 使用反射 API 创建 Xamarin iOS 应用程序
description: 本文档介绍 Monotouch.dialog 属性基于属性的反射 API，该 API 基于用属性修饰的类创建 UI。
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2eef38791f420fd49b87b46fd131f1e6ce501892
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436580"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>使用反射 API 创建 Xamarin iOS 应用程序

MT。D 反射 API 允许用 MT 的特性修饰类。D 使用自动创建屏幕。 反射 API 在这些类和屏幕上显示的内容之间提供绑定。 尽管此 API 不提供元素 API 使用的精细控制，但通过基于类修饰自动构建元素层次结构可降低复杂性。

## <a name="setting-up-mtd"></a>设置 MT。2-d

隶书.D 与 Xamarin 一起分发。 若要使用此方法，请右键单击 Visual Studio 2017 或 Visual Studio for Mac 中的 Xamarin iOS 项目的 " **引用** " 节点，然后添加对 **monotouch.dialog** 程序集的引用。 然后， `using MonoTouch.Dialog` 根据需要在源代码中添加语句。

## <a name="getting-started-with-the-reflection-api"></a>反射 API 入门

使用反射 API 非常简单，如下所示：

1. 创建用 MT 修饰的类。D 特性。
1. 创建一个  `BindingContext` 实例，并将上面的类的一个实例传递给它。
1. 创建  `DialogViewController` ，并将其传递给  `BindingContext’s` `RootElement` 。

我们来看一个示例，说明如何使用反射 API。 在此示例中，我们将生成一个简单的数据输入屏幕，如下所示：

 [![在此示例中，我们将构建一个简单的数据输入屏幕，如下所示](reflection-api-walkthrough-images/01-expense-entry.png)](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>使用 MT 创建类。D 特性

需要使用反射 API 的第一件事是使用属性修饰的类。 这些属性将由 MT 使用。D 在内部从元素 API 创建对象。 例如，请考虑以下类定义：

```csharp
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;

    [Section("Expense Details")]

    [Caption("Description")]
    [Entry]
    public string Details;

    [Checkbox]
    public bool IsApproved = true;
}
```

`SectionAttribute`将生成 `UITableView` 要创建的部分，其中包含用于填充节标头的字符串参数。 声明节后，该节后面的每个字段都将包含在该节中，直到声明另一个节。
为该字段创建的用户界面元素类型将取决于该字段的类型和 MT。D 特性修饰它。

例如， `Name` 字段是 `string` ，并且使用进行修饰 `EntryAttribute` 。 这会导致向表中添加一行，其中包含文本输入字段和指定的标题。 同样，该 `IsApproved` 字段是一个 `bool` ，它会 `CheckboxAttribute` 生成一个表行，其中包含表单元右侧的复选框。 隶书.由于未在属性中指定标题，因此在这种情况下，D 使用字段名称（自动添加空格）创建标题。

## <a name="adding-the-bindingcontext"></a>添加 BindingContext

若要使用 `Expense` 类，需要创建一个 `BindingContext` 。 `BindingContext`是一个类，它将绑定特性化类中的数据以创建元素的层次结构。 若要创建一个实例，只需对其进行实例化，然后将特性化类的实例传递给构造函数。

例如，若要添加在类中使用属性声明的 UI `Expense` ，请在的方法中包含以下代码 `FinishedLaunching` `AppDelegate` ：

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

接下来，我们需要做的就是将添加到， `BindingContext` `DialogViewController` 并将其设置为窗口的 `RootViewController` ，如下所示：

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{   
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    var expense = new Expense ();
    var bctx = new BindingContext (null, expense, "Create a task");
    var dvc = new DialogViewController (bctx.Root);

    window.RootViewController = dvc;
    window.MakeKeyAndVisible ();

    return true;
}
```

运行应用程序现在会显示上面显示的屏幕。

### <a name="adding-a-uinavigationcontroller"></a>添加 UINavigationController

但请注意，不会显示传递到的标题 "创建任务" `BindingContext` 。 这是因为不 `DialogViewController` 是的一部分 `UINavigatonController` 。 让我们更改代码以添加 `UINavigationController` 作为窗口的 `RootViewController,` ，并将添加 `DialogViewController` 为的根目录，如下 `UINavigationController` 所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

现在，运行应用程序时，标题将显示在 `UINavigationController’s` 导航栏中，如以下屏幕截图所示：

 [![现在，运行应用程序时，标题会显示在 UINavigationControllers 导航栏中。](reflection-api-walkthrough-images/02-create-task.png)](reflection-api-walkthrough-images/02-create-task.png#lightbox)

通过包括 `UINavigationController` ，我们现在可以利用 MT 的其他功能。需要导航的 D。 例如，可以向类添加枚举 `Expense` ，以定义支出和 MT 的类别。D 将自动创建选择屏幕。 若要演示，请修改 `Expense` 类以包括 `ExpenseCategory` 字段，如下所示：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}

public class Expense
{
    …

    [Caption("Category")]
    public Category ExpenseCategory;
}
```

现在，运行应用程序会在表中显示一个新行，如下所示：

 [![现在，运行应用程序会在表中显示一个新行，如下所示](reflection-api-walkthrough-images/03-set-details.png)](reflection-api-walkthrough-images/03-set-details.png#lightbox)

选择该行后，应用程序会导航到一个新屏幕，其中包含与枚举相对应的行，如下所示：

 [![选择该行后，应用程序会导航到一个新屏幕，其中包含与枚举相对应的行](reflection-api-walkthrough-images/04-set-category.png)](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了反射 API 的演练。 我们演示了如何将属性添加到类以控制显示的内容。 还介绍了如何使用将 `BindingContext` 数据从类绑定到创建的元素层次结构，以及如何使用 MT。D 替换为 `UINavigationController` 。

## <a name="related-links"></a>相关链接

- [MTDReflectionWalkthrough (示例) ](/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Monotouch.dialog 对话框简介](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 Monotouch.dialog 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)