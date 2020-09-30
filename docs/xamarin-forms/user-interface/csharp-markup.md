---
title: 'Xamarin.Forms C # 标记'
description: 'C # 标记是一组乐观的帮助器方法和类，用于简化 Xamarin.Forms 在 c # 中生成声明性用户界面的过程。'
ms.prod: xamarin
ms.assetid: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a07931bfa53a5e4d77c2755b08745b8dd962b695
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557148"
---
# <a name="no-locxamarinforms-c-markup"></a>Xamarin.Forms C # 标记

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

C # 标记是一组乐观的帮助器方法和类，用于简化 Xamarin.Forms 在 c # 中生成声明性用户界面的过程。 命名空间中提供了 c # 标记提供的 Fluent API `Xamarin.Forms.Markup` 。

与 XAML 一样，c # 标记可实现 UI 标记和 UI 逻辑之间的完全分离。 这可以通过将 UI 标记和 UI 逻辑分成不同的分部类文件来实现。 例如，对于登录页，UI 标记将位于名为 *LoginPage.cs*的文件中，而 ui 逻辑位于名为 *LoginPage.logic.cs*的文件中。

C # 标记在4.6 中提供 Xamarin.Forms 。 但是，当前正在试验，只能通过将以下代码行添加到 *App.cs* 文件中来使用：

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> C # 标记在支持的所有平台上可用 Xamarin.Forms 。

## <a name="basic-example"></a>基本示例

下面的示例演示如何 [`Grid`](xref:Xamarin.Forms.Grid) [`Label`](xref:Xamarin.Forms.Label) [`Entry`](xref:Xamarin.Forms.Entry) 在 c # 中将页面内容设置为包含和的新的：

```csharp
Grid grid = new Grid();

Label label = new Label { Text = "Code: " };
grid.Children.Add(label, 0, 1);

Entry entry = new Entry
{
    Placeholder = "Enter number",
    Keyboard = Keyboard.Numeric,
    BackgroundColor = Color.AliceBlue,
    TextColor = Color.Black,
    FontSize = 15,
    HeightRequest = 44,
    Margin = fieldMargin
};
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode"));

Content = grid;
```

此示例 [`Grid`](xref:Xamarin.Forms.Grid) 使用子对象和对象创建一个对象 [`Label`](xref:Xamarin.Forms.Label) [`Entry`](xref:Xamarin.Forms.Entry) 。 `Label`显示文本， `Entry` 数据绑定到 `RegistrationCode` viewmodel 的属性。 每个子视图都设置为显示在中的特定行 `Grid` ，并 `Entry` 跨中的所有列 `Grid` 。 此外，还设置了的高度 `Entry` ，以及键盘、颜色、文本的字号和 `Margin` 。 最后，将 `Page.Content` 属性设置为 `Grid` 对象。

C # 标记允许使用其 Fluent API 重写此代码：

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Content = new Grid
{
  Children =
  {
    new Label { Text = "Code:" }
               .Row (BodyRow.CodeHeader) .Column (BodyCol.Header),

    new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
               .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
               .Bind (nameof(vm.RegistrationCode))
  }
}};
```

此示例与前面的示例相同，但 c # 标记 Fluent API 简化了在 c # 中生成 UI 的过程。

> [!NOTE]
> C # 标记包含设置特定视图属性的扩展方法。 这些扩展方法并不用于替换所有属性资源库。 相反，它们旨在提高代码可读性，并可与属性资源库结合使用。 如果属性存在一个扩展方法，则建议始终使用该方法，但可以选择首选余额。

## <a name="data-binding"></a>数据绑定

C # 标记包含一个 `Bind` 扩展方法以及一些重载，用于在视图可绑定属性和指定属性之间创建数据绑定。 `Bind`方法为中包含的大多数控件识别默认的可绑定属性 Xamarin.Forms 。 因此，在使用此方法时，通常不需要指定目标属性。 但是，还可以为其他控件注册默认的可绑定属性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

`Bind`方法可用于绑定到任何可绑定的属性：

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

此外， `BindCommand` 扩展方法可以通过单个方法调用绑定到控件的默认值 `Command` 和 `CommandParameter` 属性：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

默认情况下， `CommandParameter` 绑定到绑定上下文。 还可以为和绑定指定绑定路径和源 `Command` `CommandParameter` ：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

在此示例中，绑定上下文是一个 `Item` 实例，因此不需要为绑定指定源 `Id` `CommandParameter` 。

如果只需要绑定到 `Command` ，则可以将传递 `null` 给方法的 `parameterPath` 参数 `BindCommand` 。 或者，使用 `Bind` 方法。

还可以注册其他控件的默认值 `Command` 和 `CommandParameter` 属性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

内联转换器代码可以通过 `Bind` 和参数传递到方法 `convert` `convertBack` ：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth),
                  convert: (int depth) => new Thickness(depth * 20, 0, 0, 0))
```

还支持类型安全转换器参数：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { }
           .Bind (nameof(viewModel.Text),
                  convert: (string text, int repeat) => string.Concat(Enumerable.Repeat(text, repeat)))
```

此外，转换器代码和实例可以与类重新使用 `FuncConverter` ：

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

`FuncConverter`类还支持 `CultureInfo` 对象：

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

还可以将数据绑定到 `Span` 用属性指定的对象 `FormattedText` ：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { } .FormattedText (
    new Span { Text = "Built with " },
    new Span { TextColor = Color.Blue, TextDecorations = TextDecorations.Underline }
              .BindTapGesture (nameof(vm.ContinueToCSharpForMarkupCommand))
              .Bind (nameof(vm.Title))
)
```

### <a name="gesture-recognizers"></a>手势识别器

`Command` 和 `CommandParameter` 属性可以是 `GestureElement` `View` 使用 `BindClickGesture` 、 `BindSwipeGesture` 和 `BindTapGesture` 扩展方法绑定到和类型的数据：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

此示例将创建指定类型的笔势识别器，并将其添加到中 [`Label`](xref:Xamarin.Forms.Label) 。 `Bind*Gesture`扩展方法提供的参数与 `BindCommand` 扩展方法相同。 但是，默认情况下不 `Bind*Gesture` 绑定 `CommandParameter` ，而是 `BindCommand` 。

若要使用参数初始化笔势识别器，请使用 `ClickGesture` 、 `PanGesture` 、 `PinchGesture` 、 `SwipeGesture` 和 `TapGesture` 扩展方法：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

由于笔势识别器是 `BindableObject` ，因此你可以在 `Bind` 初始化时使用和 `BindCommand` 扩展方法。 还可以用扩展方法初始化自定义笔势识别器类型 `Gesture<TGestureElement, TGestureRecognizer>` 。

## <a name="layout"></a>布局

C # 标记包含一系列布局扩展方法，这些方法支持布局中的定位视图和视图中的内容：

| 类型 | 扩展方法 |
|---|---|
| `FlexLayout` | `AlignSelf`, `Basis`, `Grow`, `Menu`, `Order`, `Shrink` |
| `Grid` | `Row`, `Column`, `RowSpan`, `ColumnSpan` |
| `Label` | `TextLeft`, `TextCenterHorizontal`, `TextRight` <br/> `TextTop`, `TextCenterVertical`, `TextBottom` <br/> `TextCenter` |
| `Layout` | `Padding`, `Paddings` |
| `LayoutOptions` | `Left`, `CenterHorizontal`, `FillHorizontal`, `Right` <br/> `LeftExpand`, `CenterExpandHorizontal`, `FillExpandHorizontal`, `RightExpand` <br /> `Top`, `Bottom`, `CenterVertical`, `FillVertical` <br /> `TopExpand`, `BottomExpand`, `CenterExpandVertical`, `FillExpandVertical` <br /> `Center`, `Fill`, `CenterExpand`, `FillExpand` |
| `View` | `Margin`, `Margins` |
| `VisualElement` | `Height`, `Width`, `MinHeight`, `MinWidth`, `Size`, `MinSize` |

### <a name="left-to-right-and-right-to-left-support"></a>从左到右和从右到左的支持

对于设计为支持从左向右 (LTR) 或从右到左 (RTL) flow 方向的 c # 标记，上面列出的扩展方法提供最直观的名称集： `Left` 、 `Right` `Top` 和 `Bottom` 。

若要使正确的左侧和右侧扩展方法集可用，并在进程中显式设计标记的流方向，请包含以下两个 `using` 指令之一： `using Xamarin.Forms.Markup.LeftToRight;` 或 `using Xamarin.Forms.Markup.RightToLeft;` 。

对于旨在支持从左到右和从右到左流动方向的 c # 标记，建议使用下表中的扩展方法，而不是上述任一命名空间：

| 类型 | 扩展方法 |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>布局线约定

建议的约定是将视图的所有布局扩展方法按以下顺序放置在一行上：

1. 包含视图的行和列。
1. 行和列中的对齐方式。
1. 视图周围的边距。
1. 视图大小。
1. 视图中的填充。
1. 边距内的内容对齐方式。

下面的代码演示了此约定的示例：

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

遵循约定，可以快速阅读 c # 标记，并构建一个在用户界面中显示视图内容的心理地图。

## <a name="grid-rows-and-columns"></a>Grid 行和列

枚举可用于定义 [`Grid`](xref:Xamarin.Forms.Grid) 行和列，而不是使用数字。 这提供了在添加或删除行或列时无需重新编号的优点。

> [!IMPORTANT]
> [`Grid`](xref:Xamarin.Forms.Grid)使用枚举定义行和列需要以下 `using` 指令：`using static Xamarin.Forms.Markup.GridRowsColumns;`

下面的代码演示如何使用枚举定义和使用 [`Grid`](xref:Xamarin.Forms.Grid) 行和列的示例：

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms.Markup.LeftToRight;
using static Xamarin.Forms.Markup.GridRowsColumns;
// ...

enum BodyRow
{
    Prompt,
    CodeHeader,
    CodeEntry,
    Button
}

enum BodyCol
{
    FieldLabel,
    FieldValidation
}

View Build() => new Grid
{
    RowDefinitions = Rows.Define(
        (BodyRow.Prompt    , 170 ),
        (BodyRow.CodeHeader, 75  ),
        (BodyRow.CodeEntry , Auto),
        (BodyRow.Button    , Auto)
    ),

    ColumnDefinitions = Columns.Define(
        (BodyCol.FieldLabel     , 160 ),
        (BodyCol.FieldValidation, Star)
    ),

    Children =
    {
        new Label { LineBreakMode = LineBreakMode.WordWrap } .Font (15) .Bold ()
                   .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal ()
                   .Bind (nameof(vm.RegistrationPrompt)),

        new Label { Text = "Registration code" } .Bold ()
                   .Row (BodyRow.CodeHeader) .Column(BodyCol.FieldLabel) .Bottom () .Margin (fieldNameMargin),

        new Label { } .Italic ()
                   .Row (BodyRow.CodeHeader) .Column (BodyCol.FieldValidation) .Right () .Bottom () .Margin (fieldNameMargin)
                   .Bind (nameof(vm.RegistrationCodeValidationMessage)),

        new Entry { Placeholder = "E.g. 123456", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                   .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                   .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay),

        new Button { Text = "Verify" } .Style (FilledButton)
                    .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (PageMarginSize)
                    .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode))
                    .Bind (nameof(vm.VerifyRegistrationCodeCommand)),
    }
};
```

此外，可以在不进行枚举的情况下简明地定义行和列：

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>字体

以下列表中的控件可以调用 `FontSize` 、 `Bold` 、 `Italic` 和 `Font` 扩展方法，以设置控件显示的文本的外观：

- `Button`
- `DatePicker`
- `Editor`
- `Entry`
- `Label`
- `Picker`
- `SearchBar`
- `Span`
- `TimePicker`

## <a name="effects"></a>效果

可以通过扩展方法将效果附加到控件 `Effect` ：

```csharp
using Xamarin.Forms.Markup;
// ...

new Button { Text = "Tap Me" }
            .Effects (new ButtonMixedCaps())
```

## <a name="logic-integration"></a>逻辑集成

`Invoke`扩展方法可用于在 c # 标记中执行内联代码：

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Invoke (l => l.ItemTapped += OnListViewItemTapped)
```

此外，还可以使用 `Assign` 扩展方法从 ui (的 ui 标记之外访问控件) ：

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>样式

下面的示例演示如何使用 c # 标记创建隐式和显式样式：

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms;

namespace CSharpForMarkupDemos
{
    public static class Styles
    {
        static Style<Button> buttons, filledButton;
        static Style<Label> labels;
        static Style<Span> link;

        #region Implicit styles

        public static ResourceDictionary Implicit => new ResourceDictionary { Buttons, Labels };

        public static Style<Button> Buttons => buttons ?? (buttons = new Style<Button>(
            (Button.HeightRequestProperty, 44),
            (Button.FontSizeProperty, 13),
            (Button.HorizontalOptionsProperty, LayoutOptions.Center),
            (Button.VerticalOptionsProperty, LayoutOptions.Center)
        ));

        public static Style<Label> Labels => labels ?? (labels = new Style<Label>(
            (Label.FontSizeProperty, 13),
            (Label.TextColorProperty, Color.Black)
        ));

        #endregion Implicit styles

        #region Explicit styles

        public static Style<Button> FilledButton => filledButton ?? (filledButton = new Style<Button>(
            (Button.TextColorProperty, Color.White),
            (Button.BackgroundColorProperty, Color.FromHex("#1976D2")),
            (Button.CornerRadiusProperty, 5)
        )).BasedOn(Buttons);

        public static Style<Span> Link => link ?? (link = new Style<Span>(
            (Span.TextColorProperty, Color.Blue),
            (Span.TextDecorationsProperty, TextDecorations.Underline)
        ));

        #endregion Explicit styles
    }
}
```

可以通过将隐式样式加载到应用程序资源字典中来使用它们：

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

可以使用扩展方法来使用显式样式 `Style` 。

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> 除了 `Style` 扩展方法之外，还有 `ApplyToDerivedTypes` 、、 `BasedOn` `Add` 和 `CanCascade` 扩展方法。

或者，您可以创建自己的样式扩展方法：

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

`Filled`然后，可以按如下所示使用扩展方法：

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>平台特定内容

`Invoke`扩展方法可用于应用平台细节。 但是，若要避免出现多义性错误，请不要 `using` 直接包含用于 `Xamarin.Forms.PlatformConfiguration.*Specific` 命名空间的指令。 请改为创建命名空间别名，并通过别名使用特定于平台的：

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

此外，如果经常使用特定平台细节，可以在自己的扩展类中为其创建熟知的扩展方法：

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

然后，可以按如下所示使用扩展方法：

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

有关平台细节的详细信息，请参阅 [Android 平台](~/xamarin-forms/platform/android/index.md)功能、 [iOS 平台功能](~/xamarin-forms/platform/ios/index.md)和 [Windows 平台功能](~/xamarin-forms/platform/windows/index.md)。

## <a name="recommended-convention"></a>推荐的约定

建议的属性和帮助器方法的顺序和分组如下：

- **目的**：其值标识控件用途的任何属性或 helper 方法 (例如 `Text` ， `Placeholder` `Assign`) 。
- **其他**：在同一行或多行上不是布局或绑定的所有属性或 helper 方法。
- **布局**：布局按顺序排列：行和列、布局选项、边距、大小、填充和内容对齐方式。
- **Bind**：在方法链的末尾执行数据绑定，每行有一个绑定属性。 如果绑定了 *默认* 的可绑定属性，则它应位于方法链的末尾。

下面的代码演示了遵循此约定的示例：

```csharp
new Button { Text = "Verify" /* purpose */ } .Style (FilledButton) // other
            .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (10) // layout
            .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode)) // bind
            .Bind (nameof(vm.VerifyRegistrationCodeCommand)), // bind default

new Label { }
           .Assign (out animatedMessageLabel) // purpose
           .Invoke (label => label.SizeChanged += MessageLabel_SizeChanged) // other
           .Row (BodyRow.Message) .ColumnSpan (All<BodyCol>()) // layout
           .Bind (nameof(vm.Message)), // bind default
```

通过持续应用此约定，可以快速扫描 c # 标记并生成 UI 布局的心理图像。

## <a name="related-links"></a>相关链接

- [CSharpForMarkupDemos (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Android 平台功能](~/xamarin-forms/platform/android/index.md)
- [iOS 平台功能](~/xamarin-forms/platform/ios/index.md)
- [Windows 平台功能](~/xamarin-forms/platform/windows/index.md)