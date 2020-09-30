---
title: 第 1 部分。 XAML 入门
description: 在 Xamarin.Forms 应用程序中，XAML 主要用于定义页面的视觉内容，并与代码隐藏文件一起工作。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 319afff4572c02e363fef6296aca06ba290923c2
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562829"
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部分。 XAML 入门

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_在 Xamarin.Forms 应用程序中，XAML 主要用于定义页面的视觉内容，并与 c # 代码隐藏文件一起工作。_

代码隐藏文件提供标记的代码支持。 这两个文件共同构成了一个新的类定义，其中包括子视图和属性初始化。 在 XAML 文件中，类和属性通过 XML 元素和属性进行引用，并建立标记和代码之间的链接。

## <a name="creating-the-solution"></a>创建解决方案

若要开始编辑第一个 XAML 文件，请使用 Visual Studio 或 Visual Studio for Mac 创建新的 Xamarin.Forms 解决方案。  (选择下面与您的环境相对应的选项卡。 ) 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Windows 中，启动 Visual Studio 2019，然后在 "开始" 窗口中单击 " **创建新项目** " 以创建新项目：

![新建解决方案窗口](get-started-with-xaml-images/win/new-solution-2019.png)

在“创建新项目”窗口中，在“项目类型”下拉菜单中选择“移动”，选择“移动应用(Xamarin.Forms)”，然后单击“下一步”按钮：

!["新建项目" 窗口](get-started-with-xaml-images/win/new-project-2019.png)

在 " **配置新项目** " 窗口中，将 " **项目名称** " 设置为 " **XamlSamples** (或任何偏好) ，并单击" **创建** "按钮。

在 " **新建跨平台应用** " 对话框中，单击 " **空白**"，然后单击 **"确定"** 按钮：

![新建应用程序对话框](get-started-with-xaml-images/win/new-cross-platform-app.png)

解决方案中创建了四个项目： **XamlSamples** .NET Standard 库、 **XamlSamples**、 **XamlSamples**和通用 Windows 平台解决方案 **XamlSamples**。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，从菜单中选择 " **文件" > "新建解决方案** "。 在 " **新建项目** " 对话框中，从 "模板" 列表中选择 "多 **平台" > 应用** ，并选择 " **空白窗体" 应用** (*不*) **窗体应用程序** ：

![新建项目对话框1](get-started-with-xaml-images/mac/newprojectdialog1.png)

按 " **下一步**"。

在下一个对话框中，为项目指定名称 **XamlSamples** (或任何喜欢的) 。 请确保选中 " **使用 .NET Standard** " 单选按钮：

![新建项目对话框2](get-started-with-xaml-images/mac/newprojectdialog2.png)

按 " **下一步**"。

在下面的对话框中，可以选择项目的位置：

![新项目对话框3](get-started-with-xaml-images/mac/newprojectdialog3.png)

按 "**创建**"

在解决方案中创建三个项目： " **XamlSamples** .NET Standard 库"、" **XamlSamples**" 和 " **XamlSamples**"。

-----

在创建 **XamlSamples** 解决方案后，你可能需要通过选择各种平台项目作为解决方案启动项目，并在手机模拟器或实际设备上生成和部署由项目模板创建的简单应用程序，来测试你的开发环境。

除非你需要编写特定于平台的代码，否则共享的 **XamlSamples** .NET Standard 库项目是你将在其中花费几乎所有编程时间的位置。 这些文章不能在该项目外冒险。

### <a name="anatomy-of-a-xaml-file"></a>XAML 文件的解析

在 **XamlSamples** .NET Standard 库中是一对具有以下名称的文件：

- **.Xaml，xaml**文件;与
- **App.xaml.cs**，它是与 xaml 文件关联的 c # *代码隐藏* 文件。

需要单击 **app.xaml** 旁边的箭头才能查看代码隐藏文件。

App.xaml.cs**和**从派生的**App.xaml.cs**名为 `App` 的类 `Application` 。 大多数包含 XAML 文件的类都涉及派生自的类 `ContentPage` ; 这些文件使用 XAML 来定义整页的可视内容。 这适用于 **XamlSamples** 项目中的其他两个文件：

- **MAINPAGE**xaml 文件;与
- **MainPage.xaml.cs**，c # 代码隐藏文件。

尽管格式可能稍有) 不同，但 **MainPage** 文件看起来像此 (：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

这两个 XML 命名空间 (`xmlns`) 声明引用 uri、第一个看起来是 Xamarin 网站上的第二个和第二个。 不需要检查这些 Uri 所指向的内容。 这里没有任何内容。 它们只是 Xamarin 和 Microsoft 拥有的 Uri，它们基本上充当版本标识符。

第一个 XML 命名空间声明意味着 XAML 文件中没有前缀定义的标记将引用中的类 Xamarin.Forms ，例如 `ContentPage` 。 第二个命名空间声明定义的前缀 `x` 。 这适用于 XAML 自身固有的几个元素和属性，以及其他 XAML 实现所支持的属性。 但是，根据在 URI 中嵌入的年份，这些元素和属性略有不同。 Xamarin.Forms 支持 2009 XAML 规范，但并不支持所有。

`local`命名空间声明允许访问 .NET Standard 库项目中的其他类。

在第一个标记的末尾， `x` 前缀用于名为的属性 `Class` 。 由于使用此 `x` 前缀对于 XAML 命名空间几乎是通用的，因此 xaml 特性（例如） `Class` 几乎始终称为 `x:Class` 。

`x:Class`特性指定完全限定的 .net 类名称： `MainPage` 命名空间中的类 `XamlSamples` 。 这意味着，此 XAML 文件定义一个名为的命名空间中的一个名为的新类 `MainPage` `XamlSamples` ，该命名空间显示在该命名空间中 `ContentPage` `x:Class` 。

`x:Class`特性只能出现在 XAML 文件的根元素中，以定义派生的 c # 类。 这是 XAML 文件中定义的唯一一个新类。 XAML 文件中显示的所有其他内容只是从现有的类进行实例化并进行了初始化。

除了未使用的指令) 之外， **MainPage.xaml.cs** 文件与此 (类似 `using` ：

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

`MainPage`类派生自 `ContentPage` ，但注意 `partial` 类定义。 这表明应有其他分部类定义 `MainPage` ，但在何处？ 该方法是什么 `InitializeComponent` ？

当 Visual Studio 生成项目时，它会分析 XAML 文件以生成 c # 代码文件。 如果你查找 **XamlSamples\XamlSamples\obj\Debug** 目录，你会找到名为 **XamlSamples.MainPage.xaml.g.cs**的文件。 "G" 代表生成的。 这是的其他分部类定义 `MainPage` ，其中包含 `InitializeComponent` 从构造函数中调用的方法的定义 `MainPage` 。 `MainPage`然后，可以将这两个分部类定义组合在一起。 Xaml 文件或二进制形式的 XAML 文件将嵌入到可执行文件中，具体取决于是否编译了 XAML。

在运行时，特定平台项目中的代码 `LoadApplication` 会调用方法，并 `App` 在 .NET Standard 库中向其传递类的新实例。 `App`类构造函数实例化 `MainPage` 。 该类的构造函数调用 `InitializeComponent` ，然后调用 `LoadFromXaml` 从 .NET Standard 库中提取 XAML 文件 (或其已编译的二进制) 的方法。 `LoadFromXaml` 初始化在 XAML 文件中定义的所有对象，将它们一起连接到父-子关系，将代码中定义的事件处理程序附加到 XAML 文件中设置的事件，并将对象的结果树设置为页面的内容。

尽管通常不需要花费大量时间来生成代码文件，但有时会在生成的文件中的代码上引发运行时异常，因此你应该熟悉它们。

编译和运行此程序时， `Label` 元素会显示在页面的中心，如 XAML 所示：

[![Default：：： no (Xamarin) ：：： display](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

对于更有趣的视觉对象，你只需了解更有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>添加新的 XAML 页

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要将其他基于 XAML 的 `ContentPage` 类添加到项目中，请选择 " **XamlSamples** " .NET Standard 库项目，右键单击，然后选择 " **添加 > 新项 ...**"。在 " **添加新项** " 对话框中，选择 " **Visual c # 项" > Xamarin.Forms > 内容页** ("内容" 页 ** (c # ) **，这将创建一个仅限页面的代码页或 **内容视图**。 为页面提供名称，例如， **HelloXamlPage**：

!["添加新项" 对话框](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要将其他基于 XAML 的 `ContentPage` 类添加到项目中，请选择 " **XamlSamples** " .NET Standard 库项目，然后 > " **新建文件** " 菜单项中调用文件。 在 " **新建文件** " 对话框的左侧，选择 " **窗体** "，在左侧选择 "窗体"， **窗体 ContentPage Xaml** (不是 **窗体 ContentPage**，它创建仅限代码的页或 **内容视图**，而不是) 页面。 为页面提供名称，例如， **HelloXamlPage**：

![“新建文件”对话框](get-started-with-xaml-images/mac/newfiledialog.png)

-----

将两个文件添加到项目 **HelloXamlPage** 和代码隐藏文件 **HelloXamlPage.xaml.cs**。

## <a name="setting-page-content"></a>设置页面内容

编辑 **HelloXamlPage** 文件，以便仅标记为和的标记 `ContentPage` `ContentPage.Content` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`标记是 XAML 的唯一语法的一部分。 最初，它们可能看起来是无效的 XML，但它们是合法的。 句点不是 XML 中的特殊字符。

`ContentPage.Content`标记称为 "*属性元素*标记"。 `Content` 是的属性 `ContentPage` ，通常设置为单一视图或具有子视图的布局。 通常，属性将成为 XAML 中的属性，但难以将属性设置 `Content` 为复杂对象。 出于此原因，该属性表示为一个 XML 元素，该元素由类名和以句点分隔的属性名称组成。 现在， `Content` 可以在标记之间设置属性 `ContentPage.Content` ，如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

另请注意，已 `Title` 在根标记上设置属性。

此时，类、属性和 XML 之间的关系应该是显而易见的： Xamarin.Forms 类 (如 `ContentPage` 或 `Label`) 作为 XML 元素出现在 XAML 文件中。 该类的属性（包括的 `Title` `ContentPage` 属性 `Label` ）通常显示为 XML 特性。

有很多快捷方式可用于设置这些属性的值。 某些属性是基本数据类型：例如， `Title` 和 `Text` 属性的类型为，类型 `String` `Rotation` 为 `Double` ， `IsVisible` `true` 默认情况下，默认情况下在此处设置 (为，) 的类型为 `Boolean` 。

`HorizontalTextAlignment`属性的类型为 `TextAlignment` ，它是一个枚举。 对于任何枚举类型的属性，只需提供一个成员名称。

但对于更复杂类型的属性，转换器用于分析 XAML。 这些是 Xamarin.Forms 派生自的类 `TypeConverter` 。 很多是公共类，但某些不是公共类。 对于此特定的 XAML 文件，其中几个类在幕后扮演角色：

- `LayoutOptionsConverter` 对于  `VerticalOptions` 属性
- `FontSizeConverter` 对于  `FontSize` 属性
- `ColorTypeConverter` 对于  `TextColor` 属性

这些转换器控制属性设置允许的语法。

`ThicknessTypeConverter`可以处理用逗号分隔的一个、两个或四个数字。 如果提供一个数字，则应用于所有四个边。 如果有两个数字，则第一个是左对齐和右填充，第二个数字为顶部和底部。 四个数字按左、上、右和下顺序排列。

`LayoutOptionsConverter`可以将结构的公共静态字段的名称转换 `LayoutOptions` 为类型的值 `LayoutOptions` 。

`FontSizeConverter`可以处理 `NamedSize` 成员或数字字体大小。

`ColorTypeConverter`接受结构或十六进制 RGB 值的公共静态字段的名称 `Color` ，带或不带 alpha 通道（前面带有数字符号） ( # ) 。 下面是没有 alpha 通道的语法：

 `TextColor="#rrggbb"`

其中的每个小字母都是一个十六进制数字。 下面介绍如何包括 alpha 通道：

 `TextColor="#aarrggbb">`

对于 alpha 通道，请记住，FF 是完全不透明的，00是完全透明的。

另外两种格式允许您为每个通道仅指定一个十六进制数字：

 `TextColor="#rgb"` `TextColor="#argb"`

在这些情况下，将重复该数字以形成值。 例如，#CF3 是 RGB 颜色 "CC-FF-33"。

## <a name="page-navigation"></a>页面导航

运行 **XamlSamples** 程序时， `MainPage` 会显示。 若要查看新的， `HelloXamlPage` 可以将其设置为 **App.xaml.cs** 文件中的新启动页面，或导航到中的新页面 `MainPage` 。

若要实现导航，请先更改 **App.xaml.cs** 构造函数中的代码，以便 `NavigationPage` 创建对象：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在 **MainPage.xaml.cs** 构造函数中，可以创建一个简单的 `Button` ，并使用事件处理程序导航到 `HelloXamlPage` ：

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

设置 `Content` 页的属性将替换 `Content` XAML 文件中属性的设置。 在编译和部署该程序的新版本时，屏幕上会出现一个按钮。 按下它将导航到 `HelloXamlPage` 。 下面是 iPhone、Android 和 UWP 上的结果页：

[![旋转标签文本](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

您可以使用 `MainPage` iOS 上的 " **< 后退** " 按钮导航回来，使用页面顶部的左箭头或 Android 上电话的底部，或使用 Windows 10 上页面顶部的向左键。

请随意对 XAML 进行试验，以提供不同的呈现方式 `Label` 。 如果需要将任何 Unicode 字符嵌入到文本中，则可以使用标准 XML 语法。 例如，若要将问候语置于智能引号中，请使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

如下所示：

[![带有 Unicode 字符的旋转标签文本](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML 和代码交互

**HelloXamlPage**示例只包含 `Label` 页上的一个，但这种情况非常罕见。 大多数 `ContentPage` 派生类型将 `Content` 属性设置为某种形式的布局，例如 `StackLayout` 。 `Children`的属性 `StackLayout` 定义为类型 `IList<View>` ，但实际上是类型的对象 `ElementCollection<View>` ，并且可以使用多个视图或其他布局填充该集合。 在 XAML 中，这些父子关系与普通 XML 层次结构建立在一起。 下面是一个名为 **XamlPlusCodePage**的新页的 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

此 XAML 文件在语法上完成，如下所示：

[![页面上的多个控件](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

但是，你可能会认为此计划的功能缺少。 可能会 `Slider` 导致 `Label` 显示当前值，并且可能会在 `Button` 程序内执行一些操作。

如 [第4部分所示。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) `Slider` 使用来显示值的工作 `Label` 可以完全在 XAML 中使用数据绑定来处理。 但首先查看代码解决方案非常有用。 尽管如此，处理 `Button` 单击无疑也需要代码。 这意味着中的代码隐藏文件 `XamlPlusCodePage` 必须包含的事件的处理程序 `ValueChanged` `Slider` ，以及的事件的处理程序 `Clicked` `Button` 。 我们来添加它们：

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

这些事件处理程序不需要是公共的。

返回到 XAML 文件， `Slider` 和 `Button` 标记需要包括 `ValueChanged` `Clicked` 引用这些处理程序的和事件的特性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

请注意，为事件分配处理程序的语法与为属性赋值的语法相同。

如果的事件的处理程序 `ValueChanged` `Slider` 将使用 `Label` 显示当前值，则该处理程序需要从代码中引用该对象。 `Label`需要一个使用特性指定的名称 `x:Name` 。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`属性的前缀 `x:Name` 指示此属性是 XAML 的内部属性。

分配给该属性的名称 `x:Name` 具有与 c # 变量名称相同的规则。 例如，它必须以字母或下划线开头并且不包含嵌入的空格。

现在， `ValueChanged` 事件处理程序可以将设置 `Label` 为显示新 `Slider` 值。 新值可从事件参数获取：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或者，处理程序可以获取 `Slider` 从参数生成此事件的对象 `sender` ，并从该对象获取 `Value` 属性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

首次运行该程序时， `Label` 不会显示 `Slider` 该值，因为尚未 `ValueChanged` 触发事件。 但的任何操作 `Slider` 都会导致显示值：

[![显示的滑块值](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

现在为提供 `Button` 。 让我们 `Clicked` 通过使用按钮的显示警报来模拟对事件的响应 `Text` 。 事件处理程序可以安全地将 `sender` 参数强制转换为 `Button` ，然后访问其属性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

此方法定义为， `async` 因为该 `DisplayAlert` 方法是异步的，并且应以 `await` 运算符开头，后者将在方法完成时返回。 由于此方法 `Button` 从参数中获取触发事件 `sender` ，因此同一处理程序可用于多个按钮。

你已看到，在 XAML 中定义的对象可能会激发在代码隐藏文件中进行处理的事件，并且代码隐藏文件可以使用属性指定的名称访问在 XAML 中定义的对象 `x:Name` 。 这是代码和 XAML 交互的两种基本方法。

可以通过检查新生成的 **XamlPlusCode.xaml.g.cs 文件**来搜集 XAML 工作方式的其他深入了解，其中现在包含分配给任何 `x:Name` 属性作为私有字段的任何名称。 下面是该文件的简化版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此字段的声明允许在 `XamlPlusCodePage` 区域下的分部类文件中的任意位置自由使用该变量。 在运行时，将在分析 XAML 后分配该字段。 这意味着， `valueLabel` `null` 当 `XamlPlusCodePage` 构造函数在调用后开始但有效时，该字段是 `InitializeComponent` 。

在将 `InitializeComponent` 控制权返回给构造函数后，该页面的视觉对象的构造方式与在代码中实例化和初始化相同。 XAML 文件不再扮演类中的任何角色。 您可以使用任何所需的方式在页面上操作这些对象，例如，通过将视图添加到 `StackLayout` ，或将页面的 `Content` 属性设置为其他内容。 您可以通过检查 `Content` 页的属性和布局集合中的项来 "遍历树" `Children` 。 您可以为以这种方式访问的视图设置属性，或以动态方式向它们分配事件处理程序。

欢迎。 这是您的页面，而 XAML 只是生成其内容的工具。

## <a name="summary"></a>总结

本文介绍了 XAML 文件和代码文件如何影响类定义以及 XAML 和代码文件如何交互。 但 XAML 还具有自己独特的语法功能，使其能够以非常灵活的方式使用。 你可以在第2部分中开始浏览这些项 [。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第2部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)