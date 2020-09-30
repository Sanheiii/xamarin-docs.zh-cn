---
title: 在运行时加载 XAML Xamarin.Forms
description: 可以在运行时通过 LoadFromXaml 扩展方法加载和分析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 071a7de630d7d7b7582e3c9fa8906919e92d1fb5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561997"
---
# <a name="loading-xaml-at-runtime-in-no-locxamarinforms"></a>在运行时加载 XAML Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

[`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml)命名空间包括两个 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 扩展方法，可用于在运行时加载和分析 XAML。

## <a name="background"></a>背景

Xamarin.Forms构造 XAML 类时，将 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 间接调用方法。 之所以发生这种情况，是因为 XAML 类的代码隐藏文件 `InitializeComponent` 从其构造函数中调用了方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

当 Visual Studio 生成包含 XAML 文件的项目时，它会分析 XAML 文件以生成 c # 代码文件 (例如，包含方法定义的 **MainPage.xaml.g.cs**) `InitializeComponent` ：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法调用 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法，以从 .NET Standard 库中提取 XAML 文件 (或其已编译的二进制) 。 提取后，它将初始化在 XAML 文件中定义的所有对象，将它们一起连接到父-子关系，将代码中定义的事件处理程序附加到 XAML 文件中设置的事件，并将对象的结果树设置为页面的内容。

## <a name="loading-xaml-at-runtime"></a>在运行时加载 XAML

这些 [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 方法是 `public` ，因此可以从 Xamarin.Forms 要加载的应用程序中调用，并在运行时分析 XAML。 这将允许诸如应用程序从 web 服务下载 XAML、从 XAML 创建所需的视图，以及在应用程序中显示该视图之类的方案。

> [!WARNING]
> 在运行时加载 XAML 会产生极高的性能开销，通常应避免这样做。

下面的代码示例演示了一个简单的用法：

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此示例中，将 [`Button`](xref:Xamarin.Forms.Button) 创建一个实例，并 [`Text`](xref:Xamarin.Forms.Button.Text) 从中定义的 XAML 设置其属性值 `string` 。 然后，将 `Button` 添加到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 在该页面的 XAML 中定义的。

> [!NOTE]
> [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法允许指定泛型类型参数。 但是，很少需要指定类型参数，因为它将从它所操作的实例的类型推断。

[`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可用于将任何 XAML 放在一起，下面的示例因为这样做， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 然后导航到该方法：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>访问元素

使用方法在运行时加载 XAML [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) 不允许强类型访问使用)  (指定运行时对象名称的 xaml 元素 `x:Name` 。 但是，可以使用方法检索这些 XAML 元素， [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) 然后根据需要进行访问：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此示例中，的 XAML [`ContentPage`](xref:Xamarin.Forms.ContentPage) 被放大。 此 XAML 包含一个名为的，它在 [`Label`](xref:Xamarin.Forms.Label) `monkeyName` [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) 设置其属性之前使用方法进行检索 [`Text`](xref:Xamarin.Forms.Label.Text) 。

## <a name="related-links"></a>相关链接

- [LoadRuntimeXAML (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)