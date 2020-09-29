---
title: 适用于 Xamarin 的 Monotouch.dialog 简介
description: 本文档介绍 (MT 的 Monotouch.dialog。D) ，一种框架，用于通过 Xamarin 进行快速的声明性 UI 开发。 本文介绍如何使用 Monotouch.dialog Api 在代码或 JSON 中创建接口，并使用请求刷新、搜索、背景图像加载等功能。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c57a6d02488272934ea77714b07c0328ac501d26
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431210"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>适用于 Xamarin 的 Monotouch.dialog 简介

Monotouch.dialog，称为 MT。D 对于 short，是一种快速 UI 开发工具包，使开发人员能够使用信息而不是创建视图控制器、表等的麻烦来构建应用程序屏幕和导航。因此，它可显著简化 UI 开发和代码缩减。 例如，请看下面的屏幕截图：

 [![例如，请看下面的屏幕快照](images/image1.png)](images/image1.png#lightbox)

以下代码用于定义此整个屏幕：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
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
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

在 iOS 中使用表时，通常会有大量重复使用的代码。
例如，每次需要表时，都需要使用数据源来填充该表。 在具有两个通过导航控制器连接的基于表的屏幕的应用程序中，每个屏幕共享大量相同的代码。

隶书.D 通过将所有这些代码封装到用于创建表的泛型 API 中来简化此过程。 然后，它在该 API 的顶层提供了一个抽象，它允许使用声明性对象绑定语法，使其变得更加简单。 在这种情况下，MT 提供两个 Api。2-d

- **低级别元素 api** –  *元素 api* 基于创建元素的层次结构树，这些元素表示屏幕及其组件。 元素 API 为开发人员提供了最大的灵活性和控制。 此外，元素 API 通过 JSON 提供对声明性定义的高级支持，这允许实现极其快速的声明，以及从服务器动态地生成 UI。 
- **高级别反射 api** （也称为  *绑定*  *api* ），其中的类用 UI 提示和 MT 进行批注。D 基于对象自动创建屏幕，并在显示的内容之间提供绑定 (，还可以选择在屏幕上编辑) 和基础对象支持。 上面的示例演示了反射 API 的用法。 此 API 不提供元素 API 执行的精细控制，但通过基于类属性自动生成元素层次结构，进一步降低了复杂性。 

隶书.D 附带了大量内置 UI 元素，用于创建屏幕，但它也能识别自定义元素和高级屏幕布局的需求。 因此，可扩展性是融入 API 的一流功能。 开发人员可以扩展现有元素，或创建新元素，然后无缝集成。

此外，MT。D 中内置了许多常见的 iOS UX 功能，如 "请求刷新" 支持、异步映像加载和搜索支持。

本文将全面介绍如何使用 MT。D. 包括：

- **MT。D 组件** –这将重点介绍构成 MT 的类。D：使快速启动速度更快。 
- **元素参考** – MT. d. 内置元素的完整列表。 
- **高级用法** –这涉及到一些高级功能，例如：请求刷新、搜索、背景图像加载、使用 LINQ 生成元素层次结构，以及创建自定义元素、单元和控制器以与 MT 一起使用。 

## <a name="setting-up-mtd"></a>设置 MT。2-d

隶书.D 与 Xamarin 一起分发。 若要使用此方法，请右键单击 Visual Studio 2017 或 Visual Studio for Mac 中的 Xamarin iOS 项目的 " **引用** " 节点，然后添加对 **monotouch.dialog** 程序集的引用。 然后， `using MonoTouch.Dialog` 根据需要在源代码中添加语句。

## <a name="understanding-the-pieces-of-mtd"></a>了解 MT 的各个部分。2-d

即使使用反射 API，MT 也是如此。D 在其下创建一个元素层次结构，就像是直接通过元素 API 创建的一样。 此外，在上一节中提到的 JSON 支持也会创建元素。 出于此原因，重要的一点是要对 MT 的构成部分有一个基本的了解。

隶书.D 使用以下四个部分生成屏幕：

- **DialogViewController**
- **RootElement**
- **节**
- **元素**

### <a name="dialogviewcontroller"></a>DialogViewController

*DialogViewController*（简称*DVC* ）继承自， `UITableViewController` 因此表示包含表的屏幕。 DVCs 可以像常规 UITableViewController 一样推送到导航控制器上。

### <a name="rootelement"></a>RootElement

*RootElement*是进入 DVC 的项的顶级容器。 它包含一些部分，然后可以包含元素。 不呈现 RootElements;它们只是容器来表示实际呈现的内容。 将 RootElement 分配给 DVC，然后 DVC 呈现其子级。

### <a name="section"></a>部分

节是表中的一组单元。 与普通的表部分一样，它可以有可能是文本的页眉和页脚，甚至是自定义视图，如以下屏幕截图所示：

 [![与普通的表部分一样，它可以选择性地具有页眉和页脚（既可以是文本，也可以是自定义视图），如以下屏幕截图所示](images/image2.png)](images/image2.png#lightbox)

### <a name="element"></a>元素

元素表示表中的实际单元。 隶书.D 打包了各种元素，这些元素表示不同的数据类型或不同的输入。 例如，以下屏幕截图演示了一些可用元素：

 [![例如，此屏幕截图演示一些可用元素](images/image3.png)](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>有关节和 RootElements 的详细信息

现在，让我们更详细地讨论 RootElements 和章节。

### <a name="rootelements"></a>RootElements

需要至少一个 RootElement 才能启动 Monotouch.dialog 进程。

如果使用节/元素值初始化 RootElement，则此值用于查找将提供配置摘要的子元素，该配置将呈现在显示内容的右侧。 例如，下面的屏幕截图在左侧显示一个表，其中包含右侧的详细信息屏幕标题 "甜品" 以及所选沙漠的值。

 [ ![ 此屏幕截图在左侧显示一个表，其中包含右甜品的详细信息屏幕的标题，其中显示了所选沙漠的值](images/image4.png)](images/image4.png#lightbox)。下面的[ ![ 屏幕截图显示了一个表，其中的单元格包含右侧的详细信息屏幕的标题甜品，以及所选沙漠的值](images/image5.png)](images/image5.png#lightbox)。

此外，还可以在部分中使用根元素来触发加载新的嵌套配置页，如上所示。 在此模式下使用时，将在部分中呈现时使用提供的标题，并将其用作子页的标题。 例如：

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

在上面的示例中，当用户点击 "甜品" 时，Monotouch.dialog 会创建一个新页面，并导航到根为 "甜品" 的单选按钮，并具有具有三个值的单选组。

在此特定示例中，单选组将在 "甜品" 部分中选择 "巧克力蛋糕"，因为我们将值 "2" 传递到 RadioGroup。 这意味着选取列表中的第三项 (零索引) 。

调用 Add 方法或使用 c # 4 初始值设定项语法添加部分。
提供插入方法以插入带有动画的部分。

如果使用 (而不是) RadioGroup 的组实例创建 RootElement，则在部分中显示的 RootElement 的汇总值将是与该组具有相同键的所有 BooleanElements 和 CheckboxElements 的累计计数。

### <a name="sections"></a>部分

节用于对屏幕中的元素进行分组，它们是 RootElement 的唯一直接子项。 节可以包含任何标准元素，包括 new RootElements。

部分中嵌入的 RootElements 用于导航到新的更深级别。

节可以包含字符串形式的页眉和页脚，也可以是 UIViews。
通常只使用字符串，但若要创建自定义 Ui，可以使用任何 UIView 作为页眉或页脚。 您可以使用字符串创建它们，如下所示：

```csharp
var section = new Section ("Header", "Footer");
```

若要使用视图，只需将视图传递到构造函数：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>收到通知

#### <a name="handling-nsaction"></a>处理 NSAction

隶书.D 将曲面 `NSAction` 作为处理回调的委托。
例如，假设您想要为由 MT 创建的表格单元处理触控事件。 使用 MT 创建元素时。D. 只需提供一个回调函数，如下所示：

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>正在检索元素值

与属性结合后 `Element.Value` ，回调可以检索其他元素中设置的值。 例如，考虑以下代码：

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

此代码将创建一个 UI，如下所示。 有关此示例的完整演练，请参阅 [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) 教程。

 [![与 Element 属性结合在一起，回调可以检索在其他元素中设置的值](images/image6.png)](images/image6.png#lightbox)

当用户按下下表单元时，将执行匿名函数中的代码，并将此实例中的值写入 `element` Visual Studio for Mac 中的 **应用程序输出** 板。

## <a name="built-in-elements"></a>内置元素

隶书.D 附带了多个称为元素的内置表单元项。
这些元素用于显示表单元格中各种不同的类型，如字符串、浮动、日期甚至图像，只需进行几次命名即可。 每个元素都负责正确显示数据类型。 例如，布尔值元素将显示开关来切换其值。 同样，float 元素会显示滑块来更改 float 值。

还有更复杂的元素来支持更丰富的数据类型，例如图像和 html。 例如，一个 html 元素，它将打开一个 UIWebView 以在选中时加载网页，并在表单元格中显示标题。

### <a name="working-with-element-values"></a>使用元素值

用于捕获用户输入的元素公开公共 `Value` 属性，该属性随时都保存元素的当前值。 当用户使用该应用程序时，它会自动更新。

这是 Monotouch.dialog 中所有元素的行为，但用户创建的元素不需要此行为。

### <a name="string-element"></a>String 元素

在 `StringElement` 表单元的左侧显示一个标题，并显示单元格右侧的字符串值。

 [![StringElement 在表格单元的左侧显示标题，在单元格右侧显示字符串值](images/image7.png)](images/image7.png#lightbox)

若要使用 `StringElement` 作为按钮，请提供委托。

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![若要将 StringElement 用作按钮，请提供委托](images/image8.png)](images/image8.png#lightbox)

### <a name="styled-string-element"></a>带样式的字符串元素

`StyledStringElement`允许使用内置表格单元样式或自定义格式来显示字符串。

 [![StyledStringElement 允许使用内置表格单元样式或自定义格式显示字符串](images/image9.png)](images/image9.png#lightbox)

`StyledStringElement`类派生自 `StringElement` ，但允许开发人员自定义一些属性，如字体、文本颜色、背景单元颜色、换行模式、要显示的行数，以及是否应显示附件。

### <a name="multiline-element"></a>多行元素

 [![多行元素](images/image10.png)](images/image10.png#lightbox)

### <a name="entry-element"></a>Entry 元素

`EntryElement`正如名称所示，用于获取用户输入。 它支持常规字符串或密码（其中隐藏了个字符）。

 [![EntryElement 用于获取用户输入](images/image11.png)](images/image11.png#lightbox)

它初始化为三个值：

- 将向用户显示的项的标题。
- 占位符文本 (这是向用户) 提供提示的灰色文本。 
- 文本的值。

占位符和值可以为 null。 但标题是必需的。

在任何时候，访问其 Value 属性都可以检索的值 `EntryElement` 。

此外，还 `KeyboardType` 可以在创建时将属性设置为数据输入所需的键盘类型样式。 此操作可用于使用如下所示的值配置键盘 `UIKeyboardType` ：

- Numeric
- 电话
- Url
- 电子邮件

### <a name="boolean-element"></a>Boolean 元素

 [![Boolean 元素](images/image12.png)](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Checkbox 元素

 [![Checkbox 元素](images/image13.png)](images/image13.png#lightbox)

### <a name="radio-element"></a>单选元素

`RadioElement`需要 `RadioGroup` 在中指定 `RootElement` 。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![RadioElement 需要在 RootElement 中指定 RadioGroup](images/image14.png)](images/image14.png#lightbox)

 `RootElements` 还用于协调收音机元素。 `RadioElement`成员可以跨多个节 (例如，实现类似于响铃音选择器的内容，并将系统铃声) 中的自定义铃声分隔开。 "摘要" 视图将显示当前选中的单选元素。 为此，请使用 `RootElement` 组构造函数创建，如下所示：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

中组的名称 `RadioGroup` 用于显示 "包含" 页中的选定值 (如果任何) 和值（在本例中为零）是第一个选定项的索引。

### <a name="badge-element"></a>徽章元素

 [![徽章元素](images/image15.png)](images/image15.png#lightbox)

### <a name="float-element"></a>Float 元素

 [![Float 元素](images/image16.png)](images/image16.png#lightbox)

### <a name="activity-element"></a>活动元素

 [![活动元素](images/image17.png)](images/image17.png#lightbox)

### <a name="date-element"></a>Date 元素

 ![Date 元素](images/image18.png)

选择对应于 DateElement 的单元格时，将显示日期选取器，如下所示：

 [![选择对应于 DateElement 的单元格时，将显示日期选取器，如下所示](images/image19.png)](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![时间元素](images/image20.png)](images/image20.png#lightbox)

如果选择对应于 TimeElement 的单元格，则会显示时间选取器，如下所示：

 [![如果选择对应于 TimeElement 的单元格，则会显示时间选取器，如下所示](images/image21.png)](images/image21.png#lightbox)

### <a name="datetime-element"></a>DateTime 元素

 [![DateTime 元素](images/image22.png)](images/image22.png#lightbox)

选择对应于 DateTimeElement 的单元格时，将显示日期时间选取器，如下所示：

 [![选择对应于 DateTimeElement 的单元格时，将显示日期时间选取器](images/image23.png)](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 元素

 [![HTML 元素](images/image24.png)](images/image24.png#lightbox)

在 `HTMLElement` `Caption` 表单元中显示其属性的值。 选择 Whe 后，将在 `Url` 控件中加载分配给该元素的，如下 `UIWebView` 所示：

 [![选择 Whe 时，将在 UIWebView 控件中加载分配给该元素的 Url，如下所示](images/image25.png)](images/image25.png#lightbox)

### <a name="message-element"></a>Message 元素

 [![Message 元素](images/image26.png)](images/image26.png#lightbox)

### <a name="load-more-element"></a>加载更多元素

使用此元素可允许用户在列表中加载更多项。 您可以自定义标准和加载标题以及字体和文本颜色。
`UIActivity`指示器开始进行动画处理，当用户点击单元格时，将显示加载标题，并 `NSAction` 执行传入构造函数的。 完成中的代码后 `NSAction` ， `UIActivity` 指示器将停止动画并再次显示正常标题。

### <a name="uiview-element"></a>UIView 元素

此外， `UIView` 可以使用来显示任何自定义 `UIViewElement` 。

### <a name="owner-drawn-element"></a>所有者描述的元素

此元素必须是子类，因为它是抽象类。 应该重写 `Height(RectangleF bounds)` 方法，应在该方法中返回元素的高度，以及 `Draw(RectangleF bounds, CGContext context, UIView view)` 在给定边界内使用上下文和视图参数执行所有自定义绘图的方法。 此元素执行的是子类 a 的繁重提升 `UIView` ，并将其放在要返回的单元中，只需实现两个简单的重写。 可以在文件的示例应用中查看更好的示例实现 `DemoOwnerDrawnElement.cs` 。

下面是实现类的一个非常简单的示例：

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
}
```

### <a name="json-element"></a>JSON 元素

`JsonElement`是的一个子类 `RootElement` ，它扩展了 `RootElement` 以便能够从本地或远程 url 加载嵌套子元素的内容。

`JsonElement`是 `RootElement` 可在两种形式中实例化的。 一个版本创建 `RootElement` 将按需加载内容的。 这些是通过使用 `JsonElement` 在末尾使用额外自变量的构造函数创建的，从其加载内容的 url：

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

另一种形式将从本地文件或 `System.Json.JsonObject` 已分析的现有创建数据：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

有关将 JSON 与 MT 一起使用的详细信息。D. 参阅 [JSON 元素演练](./json-element-walkthrough.md) 教程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>请求刷新支持

 *请求**刷新*是最初在*Tweetie2*应用中找到的视觉效果，在许多应用程序中，这会成为一种流行的效果。

若要向对话添加自动请求刷新支持，只需执行以下两项操作：挂钩事件处理程序，以便在用户提取数据时得到通知，并在加载数据后通知 `DialogViewController` 其默认状态。

将通知挂钩很简单;只需连接到 `RefreshRequested` 上的事件 `DialogViewController` ，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然后，在您的方法 `OnUserRequestedRefresh` 中，将对某些数据加载进行排队，请求从 net 请求某些数据，或旋转线程以计算数据。 加载数据后，必须通知 `DialogViewController` 新数据所在，并将视图还原到其默认状态，方法是调用 `ReloadComplete` ：

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜索支持

若要支持搜索，请 `EnableSearch` 在上设置属性 `DialogViewController` 。 还可以将属性设置 `SearchPlaceholder` 为在搜索栏中用作水印文本。

搜索将在用户键入时更改视图的内容。 它会搜索可见字段并向用户显示这些字段。 `DialogViewController`公开了三种方法，以编程方式启动、终止或触发针对结果的新筛选器操作。 下面列出了这些方法：

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

系统是可扩展的，因此，如果需要，可以更改此行为。

### <a name="background-image-loading"></a>正在加载背景图像

Monotouch.dialog 包含 [TweetStation](https://github.com/migueldeicaza/TweetStation) 应用程序的映像加载程序。 此图像加载程序可用于在后台加载图像，支持缓存并可在加载映像时通知代码。

它还会限制传出的网络连接数。

在类中实现了图像加载程序 `ImageLoader` ，你只需调用 `DefaultRequestImage` 方法，你将需要提供要加载的映像的 Uri，以及在 `IImageUpdated` 加载映像时将调用的接口的一个实例。

例如，以下代码将图像从 Url 加载到中 `BadgeElement` ：

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

ImageLoader 类公开了一个清除方法，当你想要释放当前缓存在内存中的所有图像时，你可以调用此方法。 当前代码具有50图像的缓存。 如果要使用不同的缓存大小 (例如，如果希望图像太大，以致50映像太大) ，则只需创建 ImageLoader 的实例，并传递要保存在缓存中的映像数。

## <a name="using-linq-to-create-element-hierarchy"></a>使用 LINQ 创建元素层次结构

通过使用 LINQ 和 c # 的初始化语法，LINQ 可用于创建元素层次结构。 例如，下面的代码从某些字符串数组创建一个屏幕，并通过传递到每个的匿名函数来处理单元格选择 `StringElement` ：

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

这可以轻松地与 XML 数据存储或数据库中的数据结合使用，几乎可以完全从数据创建复杂的应用程序。

## <a name="extending-mtd"></a>扩展 MT。2-d

### <a name="creating-custom-elements"></a>创建自定义元素

您可以通过从现有元素继承或通过从根类元素派生来创建自己的元素。

若要创建自己的元素，需要重写以下方法：

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

如果元素的大小可以是可变的，则需要实现 `IElementSizing` 接口，该接口包含一个方法：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果你打算 `GetCell` 通过调用 `base.GetCell(tv)` 并自定义返回的单元格来实现方法，则还需要重写 `CellKey` 属性以返回将对你的元素唯一的键，如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

这适用于大多数元素，但不适用于 `StringElement` 和， `StyledStringElement` 因为它们对各种呈现方案使用自己的键集。 必须复制这些类中的代码。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs) 

反射和元素 API 使用相同的 `DialogViewController` 。 有时你需要自定义视图的外观，或者你可能想要使用的某些功能 `UITableViewController` 超出了 ui 的基本创建功能。

`DialogViewController`只是的子类 `UITableViewController` ，你可以采用与自定义相同的方式对其进行自定义 `UITableViewController` 。

例如，如果要将列表样式更改为 `Grouped` 或 `Plain` ，则可以通过在创建控制器时更改属性来设置此值，如下所示：

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

对于更高级的自定义 `DialogViewController` 设置，如设置其背景，你会将它划分为子类并重写适当的方法，如以下示例中所示：

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

另一个自定义点是中的以下虚拟方法 `DialogViewController` ：

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

如果单元格大小相等，则此方法应返回的子类 `DialogViewController.Source` ; `DialogViewController.SizingSource` 如果单元格不相等，则返回的子类。

您可以使用此替代来捕获任何 `UITableViewSource` 方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation) 使用它来跟踪用户滚动到顶部的时间，并相应地更新未读推文的数量。

## <a name="validation"></a>验证

元素不会自行提供验证，因为这种模型非常适合于网页和桌面应用程序，也不会直接映射到 iPhone 交互模型。

如果要进行数据验证，则应在用户使用输入的数据触发操作时执行此操作。 例如，在顶部工具栏上的 " **完成** " 或 " **下一步** " 按钮，或者某些按钮 `StringElement` 用于执行下一阶段。

在这种情况下，你将执行基本输入验证，并且可能更复杂的验证，例如检查与服务器的用户/密码组合的有效性。

如何将错误通知用户是特定于应用程序的。 您可以弹出 `UIAlertView` 或显示提示。

## <a name="summary"></a>总结

本文介绍了有关 Monotouch.dialog 的许多信息。 它讨论了 MT 的基本原理。D 工作，并涵盖了包含 MT. D 的各种组件。 它还展示了 MT 支持的各种元素和表自定义。D 并讨论了 MT 的方式。可以通过自定义元素扩展 D。 此外，它还说明了 MT 中的 JSON 支持。D：允许从 JSON 动态创建元素。

## <a name="related-links"></a>相关链接

- [演练：使用元素 API 创建应用程序](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [演练：使用反射 API 创建应用程序](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [演练：使用 JSON 元素创建用户界面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Monotouch.dialog JSON 标记](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 Monotouch.dialog 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)