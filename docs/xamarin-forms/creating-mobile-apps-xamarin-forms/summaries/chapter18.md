---
title: “第 18 章： MVVM
description: 使用 Xamarin.Forms 创建移动应用：“第 18 章： MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b2309d6ec6712be32f6e972fab130a542fbec857
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557421"
---
# <a name="summary-of-chapter-18-mvvm"></a>“第 18 章： MVVM

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

架构应用程序的最佳方式之一是，将用户界面与基础代码（有时称为“业务逻辑”**）分离开来。 虽然有多种技术，但为基于 XAML 的环境定制的技术称为“模型-视图-视图模型 (MVVM)”。

## <a name="mvvm-interrelationships"></a>MVVM 相互关系

MVVM 应用程序有三层：

- 模型：提供基础数据（有时通过文件或 Web 访问来提供）
- 视图：用户界面或表示层，通常在 XAML 中实现
- 视图模型：连接模型和视图

模型对视图模型未知，视图模型对视图未知。 这三层通常使用以下机制相互连接：

![视图、视图模型和视图](images/ch18fg03.png "MVVM")

在许多较小的程序（甚至更大的程序）中，模型通常是不存在的，或它的功能集成到视图模型中。

## <a name="viewmodels-and-data-binding"></a>视图模型和数据绑定

视图模型必须能够在视图模型的属性更改时通知视图，才能进行数据绑定。 为此，视图模型在 `System.ComponentModel` 命名空间中实现 [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 接口。 这是属于 .NET（而不是 Xamarin.Forms）的范畴。 （视图模型通常会尝试维持平台独立性。）

`INotifyPropertyChanged` 接口声明一个名为 [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) 的事件，此事件指明属性已更改。

### <a name="a-viewmodel-clock"></a>视图模型时钟

[Xamarin.FormsBook.Toolkit](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) 库中的 [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) 定义了类型为 `DateTime` 的属性，此属性以计时器为依据更改。 此类实现 `INotifyPropertyChanged`，每当 `DateTime` 属性更改时都会触发 `PropertyChanged` 事件。

[MvvmClock****](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) 示例实例化此视图模型，并使用与视图模型的数据绑定来显示更新后的日期和时间信息。

### <a name="interactive-properties-in-a-viewmodel"></a>视图模型中的交互式属性

视图模型中的属性可以更具交互性，如 [SimpleMultiplier****](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) 示例中的 [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) 类所示。 数据绑定从两个 `Slider` 元素提供被乘数和乘数值，并使用 `Label` 显示产品。 不过，可以在 XAML 中对此用户界面进行大量更改，而不需要对视图模型或代码隐藏文件进行后续更改。

### <a name="a-color-viewmodel"></a>颜色视图模型

[Xamarin.FormsBook.Toolkit](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) 库中的 [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) 集成了 RGB 和 HSL 颜色模型。 如 [HslSliders****](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) 示例所示：

[![TK 的三倍屏幕截图](images/ch18fg08-small.png "HSL 颜色模型")](images/ch18fg08-large.png#lightbox "HSL 颜色模型")

### <a name="streamlining-the-viewmodel"></a>简化视图模型

可以使用自动获取调用属性名称的 [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) 特性来定义 `OnPropertyChanged` 方法，从而简化视图模型中的代码。 [Xamarin.FormsBook.Toolkit](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) 库中的 [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) 类就是这样做的，并为视图模型提供了基类。

## <a name="the-command-interface"></a>命令接口

MVVM 与数据绑定结合使用，数据绑定又与属性结合使用，因此 MVVM 似乎不足以处理 `Button` 的 `Clicked` 事件或 `TapGestureRecognizer` 的 `Tapped` 事件。 为允许视图模型处理此类事件，Xamarin.Forms 支持命令接口**。

命令接口在包含两个公共属性的 `Button` 中显示：

- [`ICommand`](xref:System.Windows.Input.ICommand) 类型的 [`Command`](xref:Xamarin.Forms.Button.Command)（在 `System.Windows.Input` 命名空间中定义）
- `Object` 类型的 [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)

为了支持命令接口，视图模型必须定义类型为 `ICommand` 的属性，此属性随后数据绑定到 `Button` 的 `Command` 属性。 `ICommand` 接口声明了两种方法和一个事件：

- 包含 `object` 类型参数的 [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) 方法
- 包含返回 `bool` 的 `object` 类型参数的 [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) 方法
- [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) 事件

在内部，视图模型将类型为 `ICommand` 的每个属性设置为实现 `ICommand` 接口的类实例。 通过数据绑定，`Button` 最初调用 `CanExecute` 方法，并在此方法返回 `false` 时禁用自身。 它还设置 `CanExecuteChanged` 事件处理程序，并在相应事件触发时调用 `CanExecute`。 如果 `Button` 已启用，它在用户单击 `Button` 时调用 `Execute` 方法。

你可能有一些早于 Xamarin.Forms 的视图模型，这些视图模型可能已支持命令接口。 对于旨在仅与 Xamarin.Forms 配合使用的新视图模型，Xamarin.Forms 提供了实现 `ICommand` 接口的 [`Command`](xref:Xamarin.Forms.Command) 类和 [`Command<T>`](xref:Xamarin.Forms.Command`1) 类。 泛型类型是 `Execute` 和 `CanExecute` 方法的参数类型。

### <a name="simple-method-executions"></a>简单方法执行

[PowersOfThree****](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) 示例展示了如何在视图模型中使用命令接口。 [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) 类定义了两个类型为 `ICommand` 的属性，以及它传递给最简单 [`Command` 构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action))的两个私有属性。 此程序包含从这一视图模型到两个 `Button` 元素的 `Command` 属性的数据绑定。

在 XAML 中，可以将 `Button` 元素轻松替换为 `TapGestureRecognizer` 对象，无需更改任何代码。

### <a name="a-calculator-almost"></a>接近真实的计算器

[AddingMachine****](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) 示例同时使用 `ICommand` 的 `Execute` 和 `CanExecute` 方法。 它使用 [Xamarin.FormsBook.Toolkit](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) 库中的 [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) 类。 视图模型包含六个类型为 `ICommand` 的属性。 这些是从 `Command` 的 [`Command` 构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action))和 [`Command` 构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))，以及 `Command<T>` 的 [`Command<T>` 构造函数](/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__)初始化。 添加计算机的数字键全部绑定到使用 `Command<T>` 初始化的属性，`Execute` 和 `CanExecute` 的 `string` 参数标识特定键。

## <a name="viewmodels-and-the-application-lifecycle"></a>视图模型和应用程序生命周期

AddingMachine**** 示例中使用的 `AdderViewModel` 还定义了两个名为 `SaveState` 和 `RestoreState` 的方法。 这两种方法分别在应用程序休眠和再次启动时从应用程序中调用。

## <a name="related-links"></a>相关链接

- [第 18 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第 18 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- 《[使用 Xamarin.Forms企业应用程序模式》电子书](~/xamarin-forms/enterprise-application-patterns/index.md)