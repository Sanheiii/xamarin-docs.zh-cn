---
title: Xamarin.Forms Shell 搜索
description: Xamarin.Forms Shell 应用程序可以使用搜索框提供的集成搜索功能，并且可将搜索框添加到各个页面的顶部。
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f0d8766420f876898a3f10c2f23a35a0b0afeb43
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563466"
---
# <a name="no-locxamarinforms-shell-search"></a>Xamarin.Forms Shell 搜索

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包含 `SearchHandler` 类提供的集成搜索功能。 将 `Shell.SearchHandler` 附加属性设置为子类 `SearchHandler` 对象，即可将搜索功能添加到页面。 这会将搜索框添加到页面顶部：

[![iOS 和 Android 上的 Shell SearchHandler 的屏幕截图](search-images/searchhandler.png "Shell SearchHandler")](search-images/searchhandler-large.png#lightbox "Shell SearchHandler")

将查询输入到搜索框时，`Query` 属性将更新，每次更新时均将执行 `OnQueryChanged` 方法。 可以重写此方法来使用数据填充搜索建议区：

[![iOS 和 Android 上的 Shell SearchHandler 中搜索结果的屏幕截图](search-images/search-suggestions.png "Shell SearchHandler 搜索结果")](search-images/search-suggestions-large.png#lightbox "Shell SearchHandler 搜索结果")

然后，从搜索建议区选择一个结果后，将执行 `OnItemSelected` 方法。 可以重写此方法来相应地进行响应，例如通过导航到详细信息页。

## <a name="create-a-searchhandler"></a>创建 SearchHandler

可按照以下步骤将搜索功能添加到 Shell 应用程序：创建 `SearchHandler` 类的子类，然后重写 `OnQueryChanged` 和 `OnItemSelected` 方法：

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

`OnQueryChanged` 重写包含两个参数：包含旧搜索查询的 `oldValue`，以及包含当前搜索查询的 `newValue`。 将 `SearchHandler.ItemsSource` 属性设置为 `IEnumerable` 集合（其中包含与当前搜索查询匹配的项），即可更新搜索建议区。

用户选择一个搜索结果后，将执行 `OnItemSelected` 重写，并设置 `SelectedItem` 属性。 在此示例中，此方法导航到另一个显示选定的 `Animal` 相关数据的页面。 有关导航的详细信息，请参阅 [Xamarin.Forms Shell 导航](navigation.md)。

> [!NOTE]
> 可设置其他的 `SearchHandler` 属性以控制搜索框的外观。

## <a name="consume-a-searchhandler"></a>使用 SearchHandler

将 `Shell.SearchHandler` 附加属性设置为子类类型对象，可以使用子类 `SearchHandler`：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

`MonkeySearchHandler.OnQueryChanged` 方法返回 `Animal` 对象的 `List`。 将 `DisplayMemberName` 属性设置为各个 `Animal` 对象的 `Name` 属性，这样建议区所显示的数据将为各个动物名称。

将 `ShowsResults` 属性设置为 `true`，这样当用户输入搜索查询时即可显示搜索建议：

[![iOS 和 Android 上的 Shell SearchHandler 中搜索结果的屏幕截图](search-images/search-results.png "Shell SearchHandler 搜索结果")](search-images/search-results-large.png#lightbox "Shell SearchHandler 搜索结果")

搜索查询更改时，将更新搜索建议区：

[![iOS 和 Android 上的 Shell SearchHandler 中搜索结果的屏幕截图](search-images/search-results-change.png "Shell SearchHandler 搜索结果")](search-images/search-results-change-large.png#lightbox "Shell SearchHandler 搜索结果")

选择某个搜索结果后，将导航到 `MonkeyDetailPage`，并显示已选定猴子的相关数据：

[![iOS 和 Android 上的猴子详情的屏幕截图](search-images/detailpage.png "猴子详情")](search-images/detailpage-large.png#lightbox "猴子详情")

## <a name="define-search-results-item-appearance"></a>定义搜索结果项外观

除了在搜索结果中显示 `string` 数据外，可以通过将 `SearchHandler.ItemTemplate` 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 来定义每个搜索结果项的外观：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中指定的元素定义建议区各个项的外观。 在此示例中，由 [`Grid`](xref:Xamarin.Forms.Grid) 管理 `DataTemplate` 内的布局。 `Grid` 包含 [`Image`](xref:Xamarin.Forms.Image) 对象和 [`Label`](xref:Xamarin.Forms.Label) 对象，两者均绑定到各个 `Monkey` 对象的属性。

下面的屏幕截图显示建议区内各个项模板化后的结果：

[![iOS 和 Android 上的 Shell SearchHandler 中模板化搜索结果的屏幕截图](search-images/search-results-template.png "Shell SearchHandler 模板化搜索结果")](search-images/search-results-template-large.png#lightbox "Shell SearchHandler 模板化搜索结果")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="search-box-visibility"></a>搜索框可见性

将 `SearchHandler` 添加到页面顶部后，默认情况下将显示并完全展开该搜索框。 但，将 `SearchHandler.SearchBoxVisibility` 属性设置为 `SearchBoxVisibility` 枚举成员之一可以更改此行为：

- `Hidden` – 搜索框不可见或不可访问。
- `Collapsible` – 隐藏搜索框，直到用户执行操作来显示搜索框。 在 iOS 上，通过纵向弹跳页面内容来显示搜索框；在 Android 上，通过点击问号图标显示搜索框。
- `Expanded` – 搜索框可见，并且完全展开。 这是 `SearchHandler.SearchBoxVisibility` 属性的默认值。

> [!IMPORTANT]
> 在 iOS 上，可折叠搜索框需要 iOS 11 或更高版本。

下面的示例演示了如何隐藏搜索框：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>搜索框焦点

点击搜索框可调用屏幕键盘，使搜索框获得输入焦点。 也可以通过调用 `Focus` 方法（尝试在搜索框上设置输入焦点，并在成功时返回 `true`）来以编程方式实现。 当搜索框获得焦点时，会触发 `Focus` 事件并调用可重写的 `OnFocused` 方法。

当搜索框具有输入焦点时，点击屏幕上的其他位置可关闭屏幕键盘，并且搜索框失去输入焦点。 也可以通过调用 `Unfocus` 方法来以编程方式实现。 当搜索框失去焦点时，会触发 `Unfocused` 事件并调用可重写的 `OnUnfocus` 方法。

可以通过 `IsFocused` 属性检索搜索框的焦点状态，如果 `SearchHandler` 当前具有输入焦点，则会返回 `true`。

## <a name="searchhandler-appearance"></a>SearchHandler 外观

`SearchHandler` 类定义以下影响其外观的属性：

- `BackgroundColor`，属于 `Color` 类型，是搜索框文本的背景颜色。
- `CancelButtonColor`，属于 `Color` 类型，是取消按钮的颜色。
- `CharacterSpacing`，属于 `double` 类型，是 `SearchHandler` 文本字符之间的间距。
- `FontAttributes`，属于 `FontAttributes` 类型，用于指示搜索框文本是斜体还是粗体。
- `FontFamily`，属于 `string` 类型，是用于搜索框文本的字体系列。
- `FontSize`，属于 `double` 类型，是搜索框文本的大小。
- `HorizontalTextAlignment`，属于 `TextAlignment` 类型，是搜索框文本的水平对齐方式。
- `PlaceholderColor`，属于 `Color` 类型，是搜索框文本的占位符颜色。
- `TextColor`，属于 `Color` 类型，是搜索框文本的颜色。
- `TextTransform`：类型为 `TextTransform`，确定搜索框文本的大小写。
- `VerticalTextAlignment`，属于 `TextAlignment` 类型，是搜索框文本的垂直对齐方式。

## <a name="searchhandler-keyboard"></a>SearchHandler 键盘

用户与 `SearchHandler` 交互时显示的键盘可以通过 `Keyboard` 属性以编程方式设置为 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 类中的以下属性之一：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – 用于短信和表情符号有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – 默认键盘。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – 输入电子邮件地址时使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – 输入数字时使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – 输入文本时使用，而无需指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – 输入电话号码时使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – 输入文本时使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – 用于输入文件路径和 Web 地址。

这可以通过以下操作在 XAML 中实现：

```xaml
<SearchHandler Keyboard="Email" />
```

等效 C# 代码如下：

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

[`Keyboard`](xref:Xamarin.Forms.Keyboard) 类还具有 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) 工厂方法，可用于通过指定大小写、拼写检查和建议行为来自定义键盘。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 枚举值指定为方法的参数，并返回自定义的 `Keyboard`。 `KeyboardFlags` 枚举包含以下值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – 没有功能添加到键盘。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – 指示输入的每句话的第一个词的首字母将自动大写。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – 指示将对输入的文本执行拼写检查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – 指示将对输入的文本执行单词自动完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – 指示每个词的首字母将自动大写。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – 指示每个字符将自动大写。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – 指示不会发生自动大写。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – 指示将对输入的文本执行拼写检查、单词自动完成和句子首字母大写。

以下 XAML 代码示例演示如何自定义默认 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 来执行单词自动完成并将输入的每个字符的首字母大写：

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

等效 C# 代码如下：

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="searchhandler-reference"></a>SearchHandler 引用

`SearchHandler` 类定义以下控制其外观和行为的属性：

- `BackgroundColor`，属于 `Color` 类型，是搜索框文本的背景颜色。
- `CancelButtonColor`，属于 `Color` 类型，是取消按钮的颜色。
- `ClearIcon`，属于 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 类型，是 用于清除搜索框内容的显示图标。
- `ClearIconHelpText`，属于 `string` 类型，是清除图标的可访问帮助文本。
- `ClearIconName`，属于 `string` 类型，是与屏幕阅读器配合使用的清除图标的名称。
- `ClearPlaceholderCommand`，属于 `ICommand` 类型，点击 `ClearPlaceholderIcon` 时就会执行它。
- `ClearPlaceholderCommandParameter`，属于 `object` 类型，是传递给 `ClearPlaceholderCommand` 的参数。
- `ClearPlaceholderEnabled`，属于 `bool` 类型，用于确定是否能执行 `ClearPlaceholderCommand`。 默认值为 `true`。
- `ClearPlaceholderHelpText`，属于 `string` 类型，是清除占位符图标的可访问帮助文本。
- `ClearPlaceholderIcon`，属于 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 类型，是搜索框为空时所显示的清除占位符图标。
- `ClearPlaceholderName`，属于 `string` 类型，是与屏幕阅读器配合使用的清除占位符图标的名称。
- `Command`，属于 `ICommand` 类型，确认搜索查询时就会执行它。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。
- `string`，属于 `ItemsSource` 类型，表示所显示的 `DisplayMemberName` 集合各个数据项的属性的名称或路径。
- `FontAttributes`，属于 `FontAttributes` 类型，用于指示搜索框文本是斜体还是粗体。
- `FontFamily`，属于 `string` 类型，是用于搜索框文本的字体系列。
- `FontSize`，属于 `double` 类型，是搜索框文本的大小。
- `HorizontalTextAlignment`，属于 `TextAlignment` 类型，是搜索框文本的水平对齐方式。
- `IsFocused`，属于 `bool`类型，表示 `SearchHandler` 当前是否具有输入焦点。
- `IsSearchEnabled`，属于 `bool` 类型，表示搜索框的已启用状态。 默认值为 `true`。
- `ItemsSource`，属于 `IEnumerable` 类型，用于指定建议区要显示的项目集，其默认值为 `null`。
- `ItemTemplate`，属于 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 类型，用于指定模板，以应用到建议区要显示的项目集中的各个项。
- `Keyboard`，属于 `Keyboard` 类型，是 `SearchHandler` 的键盘。
- `Placeholder`，属于 `string` 类型，是搜索框为空时要显示的文本。
- `PlaceholderColor`，属于 `Color` 类型，是搜索框文本的占位符颜色。
- `Query`，属于 `string` 类型，是用户输入到搜索框的文本。
- `QueryIcon`，属于 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 类型，是用于指示用户搜索可用的图标。
- `QueryIconHelpText`，属于 `string` 类型，是查询图标的可访问帮助文本。
- `QueryIconName`，属于 `string` 类型，是与屏幕阅读器配合使用的查询图标的名称。
- `SearchBoxVisibility`，属于 `SearchBoxVisibility` 类型，是搜索框的可见性。 默认情况下，搜索框可见，并且完全展开。
- `SelectedItem`，属于 `object` 类型，是从搜索结果选定的项。 此属性为只读属性，其默认值为 `null`。
- `ShowsResults`，属于 `bool` 类型，用于指示输入文本后建议区是否应显示搜索结果。 默认值为 `false`。
- `TextColor`，属于 `Color` 类型，是搜索框文本的颜色。
- `TextTransform`：类型为 `TextTransform`，确定搜索框文本的大小写。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

此外，`SearchHandler` 类提供下列可重写的方法：

- `OnClearPlaceholderClicked`，只要点击 `ClearPlaceholderIcon` 就会调用它。
- `OnItemSelected`，只要用户选择一个搜索结果就会调用它。
- `OnFocused`，当 `SearchHandler` 获得输入焦点时将调用它。
- `OnQueryChanged`，当 `Query` 属性更改时将调用它。
- `OnQueryConfirmed`，只要用户按 Enter 键或确认搜索框中的查询，就会调用它。
- `OnUnfocus`，当 `SearchHandler` 失去输入焦点时将调用它。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 导航](navigation.md)