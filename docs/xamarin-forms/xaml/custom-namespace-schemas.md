---
title: XAML 自定义命名空间架构 Xamarin.Forms
description: 可以使用 System.windows.markup.xmlnsdefinitionattribute> 类定义 XAML 自定义命名空间架构，该类指定自定义 URL 和一个或多个 CLR 命名空间之间的映射。 然后，可以在 XAML 命名空间声明中使用自定义命名空间架构。
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5c7e0d90358ec230436276070ea2792539c446e5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561477"
---
# <a name="xaml-custom-namespace-schemas-in-no-locxamarinforms"></a>XAML 自定义命名空间架构 Xamarin.Forms

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

可以通过以下方式在 XAML 中引用库中的类型：使用命名空间声明指定公共语言运行时 (CLR) 命名空间名称和程序集名称：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

但是，在定义中指定 CLR 命名空间和程序集名称 `xmlns` 可能会很繁琐，而且容易出错。 此外，如果库包含多个命名空间中的类型，则可能需要多个 XAML 命名空间声明。

另一种方法是定义 `http://mycompany.com/schemas/controls` 映射到一个或多个 CLR 命名空间的自定义命名空间架构（例如）。 这使得单个 XAML 命名空间声明可以引用程序集中的所有类型，即使它们在不同的命名空间中。 它还允许单个 XAML 命名空间声明引用多个程序集中的类型。

有关 XAML 命名空间的详细信息，请参阅[中 Xamarin.Forms 的 Xaml 命名空间](namespaces.md)。

## <a name="defining-a-custom-namespace-schema"></a>定义自定义命名空间架构

示例应用程序包含一个公开一些简单控件的库，例如 `CircleButton` ：

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

库中的所有控件都位于 `MyCompany.Controls` 命名空间中。 这些控件可通过自定义命名空间架构公开给调用程序集。

自定义命名空间架构使用类定义 `XmlnsDefinitionAttribute` ，该类指定 XAML 命名空间与一个或多个 CLR 命名空间之间的映射。 `XmlnsDefinitionAttribute`采用两个参数： XAML 命名空间名称和 CLR 命名空间名称。 XAML 命名空间名称存储在属性中 `XmlnsDefinitionAttribute.XmlNamespace` ，CLR 命名空间名称存储在 `XmlnsDefinitionAttribute.ClrNamespace` 属性中。

> [!NOTE]
> `XmlnsDefinitionAttribute`该类还具有一个名为的属性 `AssemblyName` ，可以选择将其设置为程序集的名称。 仅当从引用的 CLR 命名空间 `XmlnsDefinitionAttribute` 在外部程序集中时，才需要此项。

`XmlnsDefinitionAttribute`应在项目中的程序集级别定义，其中包含将在自定义命名空间架构中映射的 CLR 命名空间。 下面的示例显示示例应用程序中的 **AssemblyInfo.cs** 文件：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

此代码创建一个将 `http://mycompany.com/schemas/controls` URL 映射到 CLR 命名空间的自定义命名空间架构 `MyCompany.Controls` 。 此外，还在 `Preserve` 程序集上指定了属性，以确保链接器保留程序集中的所有类型。

> [!IMPORTANT]
> `Preserve`特性应应用于程序集中的类，这些类通过自定义命名空间架构进行映射或应用于整个程序集。

然后，可以将自定义命名空间架构用于 XAML 文件中的类型解析。

## <a name="consuming-a-custom-namespace-schema"></a>使用自定义命名空间架构

若要使用自定义命名空间架构中的类型，XAML 编译器要求使用类型的程序集有一个代码引用，该程序集用于定义类型的程序集。 这可以通过将包含方法的类添加 `Init` 到定义将通过 XAML 使用的类型的程序集来实现：

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

`Init`然后，可以从使用自定义命名空间架构中的类型的程序集调用方法：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> 如果无法包括此类代码引用，将导致 XAML 编译器无法找到包含自定义命名空间架构类型的程序集。

若要使用 `CircleButton` 控件，请声明一个 XAML 命名空间，并在命名空间声明中指定自定义命名空间架构 URL：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` 然后，可以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 通过使用命名空间前缀来声明实例，将其添加到中 `controls` 。

若要查找自定义命名空间架构类型， Xamarin.Forms 将为实例搜索引用的程序集 `XmlnsDefinitionAttribute` 。 如果 `xmlns` XAML 文件中元素的属性与 `XmlNamespace` 中的属性值相匹配 `XmlnsDefinitionAttribute` ， Xamarin.Forms 则将尝试使用 `XmlnsDefinitionAttribute.ClrNamespace` 属性值来解析类型。 如果类型解析失败， Xamarin.Forms 将继续根据任何其他匹配实例尝试类型解析 `XmlnsDefinitionAttribute` 。

结果就是显示了两个 `CircleButton` 实例：

![圆圈按钮](custom-namespace-schemas-images/circle-buttons.png "圆圈按钮")

## <a name="related-links"></a>相关链接

- [自定义命名空间架构 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [建议的 XAML 命名空间前缀](custom-prefix.md)
- [XAML 命名空间 Xamarin.Forms](namespaces.md)