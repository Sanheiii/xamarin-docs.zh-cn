---
title: Xamarin.Forms Shell 导航
description: Xamarin.Forms Shell 应用程序可以利用基于 URI 的导航体验：可以导航到应用程序中的任何页面，而无需遵循设置的导航层次结构。
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6cf4932c3265d1d66200ae12ba448a758586f11c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563141"
---
# <a name="no-locxamarinforms-shell-navigation"></a>Xamarin.Forms Shell 导航

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包括基于 URI 的导航体验：使用路由导航到应用程序中的任何页面，而无需遵循设置的导航层次结构。 此外，它还能够向后导航，不必访问导航堆栈上的所有页面。

`Shell` 定义了以下导航相关的属性：

- `BackButtonBehavior`，属于 `BackButtonBehavior` 类型，是用于定义“后退”按钮行为的附加属性。
- `CurrentItem`，属于 `FlyoutItem` 类型，是当前选定的 `FlyoutItem`。
- `CurrentState`，属于 `ShellNavigationState` 类型，是当前 `Shell` 的导航状态。
- `Current`，属于 `Shell` 类型，是 `Application.Current.MainPage` 的类型转换别名。

`BackButtonBehavior`、`CurrentItem` 和 `CurrentState` 属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

导航是通过从 `Shell` 类调用 `GoToAsync` 方法来执行的。 当要执行导航时，将触发 `Navigating` 事件，并在导航完成时触发 `Navigated` 事件。

> [!NOTE]
> 仍可在 Xamarin.Forms Shell 应用程序中通过使用 [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation) 属性执行导航。 有关详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="routes"></a>路由

通过指定要导航到的 URI，可以在 Shell 应用程序中执行导航。 导航 URI 可以有三个组件：

- 一个路由，它定义了作为 Shell 视觉层次结构的一部分存在的内容的路径。
- 一个页。 Shell 视觉层次结构中不存在的页可以从 Shell 应用程序中的任何位置推送到导航堆栈。 例如，不会在 Shell 视觉层次结构中定义项详细信息页，但可以根据需要将其推送到导航堆栈。
- 一个或多个查询参数。 查询参数是可以在导航时传递到目标页的参数。

当导航 URI 包含所有三个组件时，结构为：//route/page?queryParameters

### <a name="register-routes"></a>注册路由

可以通过 `FlyoutItem`、`Tab` 和 `ShellContent` 对象的 `Route` 属性在这些对象上定义路由：

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> Shell 层次结构中的所有项都有一个与之关联的路由。 如果开发人员未设置路由，则在运行时生成路由。 但是，不能保证生成的路由在不同应用程序会话之间都是一致的。

该示例（来自示例应用程序）创建了以下路由层次结构，可用于编程导航：

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

要导航到 `dogs` 路由的 `ShellContent` 对象，绝对路由 URI 为 `//animals/domestic/dogs`。 同样，要导航到 `about` 路由的 `ShellContent` 对象，绝对路由 URI 为 `//about`。

> [!IMPORTANT]
> 如果检测到重复路由，则将在应用程序启动时引发 `ArgumentException`。 如果层次结构中同一级别的两个或更多路由共享一个路由名称，也会引发此异常。

#### <a name="register-page-routes"></a>注册页面路由

在 Shell 子类构造函数或者在调用路由前运行的任何其他位置中，可为未在 Shell 视觉层次结构中表示的任何页面显式地注册其他路由：

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

此示例将 Shell 子类中未定义的项详细信息页注册为路由。 然后可以使用基于 URI 的导航从应用程序中的任何位置导航到这些页面。 这些页面的路由被称为“全局路由”。

> [!NOTE]
> 如果需要，已经使用 `Routing.RegisterRoute` 方法注册其路由的页面可以通过 `Routing.UnRegisterRoute` 方法注销。

此外，如果需要，页面可在不同的路由层次结构上注册：

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

此示例启用上下文页面导航，其中从 `monkeys` 路由的页面导航到 `details` 路由将显示 `MonkeyDetailPage`。 同样，从 `elephants` 路由的页面导航到 `details` 路由将显示 `ElephantDetailPage`。

> [!IMPORTANT]
> 如果 `Routing.RegisterRoute` 方法尝试将同一路由注册到两个或更多不同类型，将引发 `ArgumentException`。

## <a name="perform-navigation"></a>执行导航

要执行导航，必须首先获得对 `Shell` 子类的引用。 通过将 `App.Current.MainPage` 属性转换为 `Shell` 对象，或者通过 `Shell.Current` 属性，可以获得此引用。 然后，可以通过调用 `Shell` 对象上的 `GoToAsync` 方法来执行导航。 该方法导航到 `ShellNavigationState` 并返回 `Task`，后者将在导航动画完成后完成。 `ShellNavigationState` 对象是通过 `GoToAsync` 方法从 `string` 或 `Uri` 构造的，并将其 `Location` 属性设置为 `string` 或 `Uri` 参数。

当导航到 Shell 视觉层次结构中的路由时，不会创建导航堆栈。 但是，当导航到不在 Shell 视觉层次结构中的页面时，将创建一个导航堆栈。

> [!NOTE]
> 可以通过 `Shell.Current.CurrentState` 属性检索 `Shell` 的当前导航状态，该属性包括 `Location` 属性中显示的路由的 URI。

### <a name="absolute-routes"></a>绝对路由

导航可以通过指定一个有效的绝对 URI 作为 `GoToAsync` 方法的参数来执行：

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

本例导航到 `monkeys` 路由的页面，该路由在 `ShellContent` 对象上定义。 表示 `monkeys` 路由的 `ShellContent` 对象是其路由为 `animals` 的 `FlyoutItem` 对象的子对象。

### <a name="relative-routes"></a>相对路由

导航还可以通过指定一个有效的相对 URI 作为 `GoToAsync` 方法的参数来执行。 路由系统会尝试将 URI 匹配到 `ShellContent` 对象。 因此，如果应用程序中的所有路由都是唯一的，那么导航只能通过将唯一路由名称指定为相对 URI 来执行：

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

下面的示例导航到 `monkeydetails` 路由的页面。

此外，还支持以下相对路由格式：

| 格式 | 描述 |
| --- | --- |
| //route | 将从当前显示的路由向上搜索路由层次结构来获取指定的路由。 |
| ///route | 将从当前显示的路由向下搜索路由层次结构来获取指定的路由。 |

#### <a name="contextual-navigation"></a>上下文导航

相对路由支持上下文导航。 以下列路由层次结构为例：

```
monkeys
  details
bears
  details
```

当显示 `monkeys` 路由的注册页时，导航到 `details` 路由将显示 `monkeys/details` 路由的注册页。 同样，当显示 `bears` 路由的注册页时，导航到 `details` 路由将显示 `bears/details` 路由的注册页。 有关如何注册本示例中的路由的信息，请参阅[注册页面路由](#register-page-routes)。

### <a name="backwards-navigation"></a>向后导航

向后导航可以通过将“..”指定为 `GotoAsync` 方法的参数来执行：

```csharp
await Shell.Current.GoToAsync("..");
```

通过“..”执行的向后导航还可与路由结合使用，如下所示：

```csharp
await Shell.Current.GoToAsync("../route");
```

在此示例中，总体效果是向后导航，然后导航到指定路由。

> [!IMPORTANT]
> 仅当向后导航将你置于路由层次结构中的当前位置以导航到指定路由时，才可在向后导航后导航到指定路由。

同样，可以向后导航多次，然后导航到指定路由：

```csharp
await Shell.Current.GoToAsync("../../route");
```

在此示例中，总体效果是向后导航两次，然后导航到指定路由。

> [!NOTE]
> 使用“..”导航时也可以传递数据。 有关详细信息，请参阅[传递数据](#pass-data)。

### <a name="invalid-routes"></a>无效路由

以下路由格式无效：

| 格式 | 说明 |
| --- | --- |
| route 或 /route | 不能将视觉层次结构中的路由推送到导航堆栈。 |
| //page 或 ///page | 全局路由当前不能是导航堆栈上的唯一页面。 因此，不支持绝对路由到全局路由。 |

使用任何这些路由格式都会导致引发 `Exception`。

> [!IMPORTANT]
> 尝试导航到不存在的路由会导致引发 `ArgumentException` 异常。

### <a name="debugging-navigation"></a>调试导航

一些 Shell 类通过 `DebuggerDisplayAttribute` 修饰，它指定调试程序如何显示类或字段。 这可以通过显示与导航请求相关的数据来帮助调试导航请求。 例如，下面的屏幕截图显示了 `Shell.Current` 对象的 `CurrentItem` 和 `CurrentState` 属性：

![调试程序的屏幕截图](navigation-images/debugger.png "调试器")

在本例中，类型为 `FlyoutItem` 的 `CurrentItem` 属性显示了 `FlyoutItem` 对象的标题和路由。 同样，类型为 `ShellNavigationState` 的 `CurrentState` 属性显示了 Shell 应用程序中显示的路由的 URI。

### <a name="tab-class"></a>Tab 类

`Tab` 类定义了一个类型为 `IReadOnlyList<Page>` 的 `Stack` 属性，它表示 `Tab` 中当前推送的导航堆栈。 该类还提供了以下可重写的导航方法：

- `GetNavigationStack` 返回 `IReadOnlyList<Page`>，表示当前导航堆栈。
- `OnInsertPageBefore`，调用 `INavigation.InsertPageBefore` 时会对其进行调用。
- `OnPopAsync`，返回 `Task<Page>`，调用 `INavigation.PopAsync` 时会对其进行调用。
- `OnPopToRootAsync`，返回 `Task`，调用 `INavigation.OnPopToRootAsync` 时会对其进行调用。
- `OnPushAsync`，返回 `Task`，调用 `INavigation.PushAsync` 时会对其进行调用。
- `OnRemovePage`，调用 `INavigation.RemovePage` 时会对其进行调用。

## <a name="navigation-events"></a>导航事件

`Shell` 类定义 `Navigating` 事件，该事件在即将执行导航时触发，原因可能是编程导航或用户交互。 随附 `Navigating` 事件的 `ShellNavigatingEventArgs` 对象提供以下属性：

| Property | 类型 | 描述 |
|---|---|---|
| `Current` | `ShellNavigationState` | 当前页的 URI。 |
| `Source` | `ShellNavigationSource` | 发生的导航类型。 |
| `Target` | `ShellNavigationState`  | 表示导航目标位置的 URI。 |
| `CanCancel`  | `bool` | 指示是否可以取消导航的值。 |
| `Cancelled`  | `bool` | 指示是否已取消导航的值。 |

此外，`ShellNavigatingEventArgs` 类提供可用来取消导航的 `Cancel` 方法。

> [!NOTE]
> `Navigated` 事件由 `Shell` 类中的可重写 `OnNavigating` 方法触发。

`Shell` 类还定义 `Navigated` 事件，该事件在导航完成时触发。 随附 `Navigating` 事件的 `ShellNavigatedEventArgs` 对象提供以下属性：

| Property | 类型 | 描述 |
|---|---|---|
| `Current` | `ShellNavigationState` | 当前页的 URI。 |
| `Previous`| `ShellNavigationState` | 上一页的 URI。 |
| `Source`  | `ShellNavigationSource` | 发生的导航类型。 |

> [!NOTE]
> `Navigating` 事件由 `Shell` 类中的可重写 `OnNavigated` 方法触发。

`ShellNavigatedEventArgs` 和 `ShellNavigatingEventArgs` 类均具有 `Source` 属性，类型为 `ShellNavigationSource`。 此枚举提供下列值：

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

因此，在 `Navigating` 事件处理程序中，可以拦截导航并基于导航源执行操作。 例如，下面的代码显示页面数据未保存时如何取消向后导航：

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="pass-data"></a>传递数据

执行编程导航时，可以将数据作为查询参数传递。 例如，当用户在 `ElephantsPage` 上选择大象时，将在示例应用程序中执行以下代码：

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

此代码示例在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中检索当前选中的大象，并导航到 `elephantdetails` 路由，将 `elephantName` 作为查询参数传递。 注意，查询参数将是针对导航编码的 URL，因此“Indian Elephant”将变为“Indian%20Elephant”。

要接收数据，必须为每个查询参数对表示导航到的页面的类或页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的类修饰一个 `QueryPropertyAttribute`：

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

`QueryPropertyAttribute` 的第一个参数指定将接收数据的属性的名称，第二个参数指定查询参数 ID。因此，上述示例中的 `QueryPropertyAttribute` 指定 `Name` 属性将接收从 `GoToAsync` 方法调用中的 URI 传入 `name` 查询参数的数据。 然后，`Name` 属性对查询参数值进行 URL 解码，并使用它将页面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为将显示的对象。

> [!NOTE]
> 可以用多个 `QueryPropertyAttribute` 对象修饰一个类。

## <a name="back-button-behavior"></a>“后退”按钮行为

`BackButtonBehavior` 类定义以下控制“后退”按钮外观和行为的属性：

- `Command`属于 `ICommand` 类型，在按下“后退”按钮时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。
- `IconOverride`，属于 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 类型，是用于“后退”按钮的图标。
- `IsEnabled`，属于 `boolean` 类型，指示是否已启用“后退”按钮。 默认值为 `true`。
- `TextOverride`，属于 `string` 类型，是用于“后退”按钮的文本。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

通过将 `Shell.BackButtonBehavior` 附加属性设置为 `BackButtonBehavior` 对象，可以使用 `BackButtonBehavior` 类：

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

将 `Command` 属性设置为按下“后退”按钮时执行的 `ICommand`，将 `IconOverride` 属性设置为用于“后退”按钮的图标：

[![iOS 和 Android 上 Shell“后退”按钮图标重写的屏幕截图](navigation-images/back-button.png "Shell 后退按钮图标重写")](navigation-images/back-button-large.png#lightbox "Shell 后退按钮图标重写")

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)