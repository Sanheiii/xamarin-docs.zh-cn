---
title: 添加拖放手势识别器
description: 本文介绍了如何使用 Xamarin.Forms 来识别拖放手势。
ms.prod: xamarin
ms.assetid: 4CB2F270-908A-4A89-B852-70BC04066E8C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d3eb7edbb24c7e28ee375e1de85f6a7597ec63ac
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561010"
---
# <a name="add-drag-and-drop-gesture-recognizers"></a>添加拖放手势识别器

![预发行版 API](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)

借助拖放手势，可以使用连续手势将项及其相关的数据包从屏幕上的一个位置拖放到另一个位置。 拖放手势可以在单个应用程序中进行，也可以在一个应用程序中开始，然后在另一个应用程序中结束。

> [!IMPORTANT]
> Xamarin.Forms 拖放手势识别器目前还处于实验性阶段，只能通过设置 `DragAndDrop_Experimental` 标志来使用。 有关详细信息，请参阅[实验性标志](~/xamarin-forms/internals/experimental-flags.md)。
>
> iOS、Android 和通用 Windows 平台 (UWP) 都支持识别拖放手势。 不过，在 iOS 上，平台版本不得低于 iOS 11。

拖动源（即启动拖动手势的元素）可以通过填充数据包对象来提供要传输的数据。 当拖放源被释放时，就会发生放置。 然后，放置目标（即拖动源下的元素）会处理数据包。

在应用程序中启用拖放的过程如下所示：

1. 启用对元素的拖动，具体方法为将 `DragGestureRecognizer` 对象添加到它的 `GestureRecognizers` 集合中，然后将 `DragGestureRecognizer.CanDrag` 属性设置为 `true`。 有关详细信息，请参阅[启用拖动](#enable-drag)。
1. [可选] 生成数据包。 Xamarin.Forms 自动为图像和文本控件填充数据包，但对于其他内容，你需要构造自己的数据包。 有关详细信息，请参阅[生成数据包](#build-a-data-package)。
1. 启用对元素的放置，具体方法为将 `DropGestureRecognizer` 对象添加到它的 `GestureRecognizers` 集合中，然后将 `DropGestureRecognizer.AllowDrop` 属性设置为 `true`。 有关详细信息，请参阅[启用放置](#enable-drop)。
1. [可选] 处理 `DropGestureRecognizer.DragOver` 事件，以指明放置目标允许执行的操作类型。 有关详细信息，请参阅[处理 DragOver 事件](#handle-the-dragover-event)。
1. [可选] 处理数据包，以接收放置的内容。 Xamarin.Forms 自动从数据包中检索图像和文本数据，但对于其他内容，你需要自行处理数据包。 有关详细信息，请参阅[处理数据包](#process-the-data-package)。

> [!NOTE]
> 暂不支持将项拖放到 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 和从中拖动项。

## <a name="enable-drag"></a>启用拖动

在 Xamarin.Forms 中，拖动手势识别是由 `DragGestureRecognizer` 类提供的。 此类定义了以下属性：

- `CanDrag`：类型为 `bool`，指明手势识别器附加到的元素能否为拖动源。 此属性的默认值为 `false`。
- `DragStartingCommand`：类型为 `ICommand`，在第一次识别拖动手势时执行。
- `DragStartingCommandParameter`，属于 `object` 类型，是传递给 `DragStartingCommand` 的参数。
- `DropCompletedCommmand`：类型为 `ICommand`，在放置拖动源时执行。
- `DropCompletedCommandParameter`，属于 `object` 类型，是传递给 `DropCompletedCommand` 的参数。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`DragGestureRecognizer` 类还定义了 `DragStarting` 和 `DropCompleted` 事件。 当检测到拖动手势时，`DragGestureRecognizer` 对象就会执行 `DragStartingCommand`，并调用 `DragStarting` 事件。 然后，当检测到放置手势完成时，`DragGestureRecognizer` 对象就会执行 `DropCompletedCommand`，并调用 `DropCompleted` 事件。

`DragStarting` 事件随附的 `DragStartingEventArgs` 对象定义了以下属性：

- `Handled`：类型为 `bool`，指明是事件处理程序已经处理了事件，还是 Xamarin.Forms 应继续执行自己的处理。
- `Cancel`：类型为 `bool`，指明是否应取消事件。
- `Data`：类型为 `DataPackage`，指明拖动源随附的数据包。 这是只读属性。

`DropCompleted` 事件随附的 `DropCompletedEventArgs` 对象有类型为 `DataPackageOperation` 的只读 `DropResult` 属性。 若要详细了解 `DataPackageOperation` 枚举，请参阅[处理 DragOver 事件](#handle-the-dragover-event)。

下面的 XAML 示例展示了附加到 [`Image`](xref:Xamarin.Forms.Image) 的 `DragGestureRecognizer`：

```xaml
<Image Source="monkeyface.png">
    <Image.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True" />
    </Image.GestureRecognizers>
</Image>
```

在此示例中，可以对 [`Image`](xref:Xamarin.Forms.Image) 启动拖动手势。

> [!TIP]
> 在 iOS、Android 和 UWP 上，拖动手势是通过先长按再拖动来启动的。

## <a name="build-a-data-package"></a>生成数据包

Xamarin.Forms（在拖动手势启动时）自动为以下控件生成数据包：

- 文本控件。 可以从 [`CheckBox`](xref:Xamarin.Forms.CheckBox)、[`DatePicker`](xref:Xamarin.Forms.DatePicker)、[`Editor`](xref:Xamarin.Forms.Editor)、[`Entry`](xref:Xamarin.Forms.Entry)、[`Label`](xref:Xamarin.Forms.Label)、[`RadioButton`](xref:Xamarin.Forms.RadioButton)、[`Switch`](xref:Xamarin.Forms.Switch) 和 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 对象中拖动文本值。
- 图像控件。 可以从 [`Button`](xref:Xamarin.Forms.Button)、[`Image`](xref:Xamarin.Forms.Image) 和 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 控件中拖动图像。

下表显示了当对文本控件启动拖动手势时读取的属性和尝试执行的任何转换：

| 控制 | 属性 | 转换 |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | 转换为 `string` 的 `bool`。 |
| `DatePicker` | `Date` | 转换为 `string` 的 `DateTime`。 |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | 转换为 `string` 的 `bool`。 |
| `Switch` | `IsToggled` | 转换为 `string` 的 `bool`。 |
| `TimePicker` | `Time` | 转换为 `string` 的 `TimeSpan`。 |

对于除文本和图像以外的内容，你需要自行生成数据包。

数据包由 `DataPackage` 类表示，此类定义了以下属性：

- `Properties`：类型为 `DataPackagePropertySet`，它是组成 `DataPackage` 中所包含数据的属性的集合。 此为只读属性。
- `Image`：类型为 [`ImageSource`](xref:Xamarin.Forms.ImageSource)，它是 `DataPackage` 中包含的图像。
- `Text`：类型为 `string`，它是 `DataPackage` 中包含的文本。
- `View`：类型为 `DataPackageView`，它是 `DataPackage` 的只读版本。

`DataPackagePropertySet` 类表示存储为 `Dictionary<string,object>` 的属性包。 若要了解 `DataPackageView` 类，请参阅[处理数据包](#process-the-data-package)。

### <a name="store-image-or-text-data"></a>存储图像或文本数据

通过在 `DataPackage.Image` 或 `DataPackage.Text` 属性中存储图像或文本数据，可以将这些数据与拖动源关联。 这可以在 `DragStarting` 事件的处理程序中完成。

下面的 XAML 示例展示了为 `DragStarting` 事件注册处理程序的 `DragGestureRecognizer`：

```xaml
<Path Stroke="Black"
      StrokeThickness="4">
    <Path.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Path.GestureRecognizers>
    <Path.Data>
        <!-- PathGeometry goes here -->
    </Path.Data>
</Path>
```

在此示例中，`DragGestureRecognizer` 附加到 `Path` 对象。 当在 `Path` 上检测到拖动手势时，就会触发 `DragStarting` 事件，此事件执行 `OnDragStarting` 事件处理程序：

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    e.Data.Text = "My text data goes here";
}
```

`DragStarting` 事件随附的 `DragStartingEventArgs` 对象有类型为 `DataPackage` 的 `Data` 属性。 在此示例中，`DataPackage` 对象的 `Text` 属性设置为 `string`。 然后，可以在放置时访问 `DataPackage` 来检索 `string`。

### <a name="store-data-in-the-property-bag"></a>将数据存储在属性包中

任何数据（包括图像和文本）都可以通过将数据存储在 `DataPackage.Properties` 集合中来与拖动源关联。 这可以在 `DragStarting` 事件的处理程序中完成。

下面的 XAML 示例展示了为 `DragStarting` 事件注册处理程序的 `DragGestureRecognizer`：

```xaml
<Rectangle Stroke="Red"
           Fill="DarkBlue"
           StrokeThickness="4"
           HeightRequest="200"
           WidthRequest="200">
    <Rectangle.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Rectangle.GestureRecognizers>
</Rectangle>
```

在此示例中，`DragGestureRecognizer` 附加到 `Rectangle` 对象。 当在 `Rectangle` 上检测到拖动手势时，就会触发 `DragStarting` 事件，此事件执行 `OnDragStarting` 事件处理程序：

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    Shape shape = (sender as Element).Parent as Shape;
    e.Data.Properties.Add("Square", new Square(shape.Width, shape.Height));
}
```

`DragStarting` 事件随附的 `DragStartingEventArgs` 对象有类型为 `DataPackage` 的 `Data` 属性。 可以通过修改 `DataPackage` 对象的 `Properties` 集合（即 `Dictionary<string, object>` 集合）来存储所需的任何数据。 在此示例中，`Properties` 字典被修改为根据“Square”键存储表示 `Rectangle` 大小的 `Square` 对象。

## <a name="enable-drop"></a>启用放置

在 Xamarin.Forms 中，放置手势识别是由 `DropGestureRecognizer` 类提供的。 此类定义了以下属性：

- `AllowDrop`：类型为 `bool`，指明手势识别器附加到的元素能否为放置目标。 此属性的默认值为 `false`。
- `DragOverCommand`：类型为 `ICommand`，在拖动源被拖动到放置目标上时执行。
- `DragOverCommandParameter`，属于 `object` 类型，是传递给 `DragOverCommand` 的参数。
- `DropCommand`：类型为 `ICommand`，在拖动源被放置到放置目标上时执行。
- `DropCommandParameter`，属于 `object` 类型，是传递给 `DropCommand` 的参数。

这些属性由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持；也就是说，它们可以作为数据绑定的目标，并能进行样式设置。

`DropGestureRecognizer` 类还定义了 `DragOver` 和 `Drop` 事件。 当在放置目标上识别拖动源时，`DropGestureRecognizer` 就会执行 `DragOverCommand`，并调用 `DragOver` 事件。 然后，当在放置目标上识别放置手势时，`DropGestureRecognizer` 就会执行 `DropCommand`，并调用 `Drop` 事件。

`DragOver` 事件随附的 `DragEventArgs` 类定义了以下属性：

- `Data`：类型为 `DataPackage`，它包含与拖动源关联的数据。 此属性为只读。
- `AcceptedOperation`：类型为 `DataPackageOperation`，指定放置目标允许执行的操作。

若要了解 `DataPackageOperation` 枚举，请参阅[处理 DragOver 事件](#handle-the-dragover-event)。

`Drop` 事件随附的 `DropEventArgs` 类定义了以下属性：

- `Data`：类型为 `DataPackageView`，它是数据包的只读版本。
- `Handled`：类型为 `bool`，指明是事件处理程序已经处理了事件，还是 Xamarin.Forms 应继续执行自己的处理。

下面的 XAML 示例展示了附加到 [`Image`](xref:Xamarin.Forms.Image) 的 `DropGestureRecognizer`：

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True" />
    </Image.GestureRecognizers>
</Image>
```

在此示例中，当拖动源被放置到 [`Image`](xref:Xamarin.Forms.Image) 放置目标上时，拖动源就会被复制到放置目标，但前提是拖动源是 [`ImageSource`](xref:Xamarin.Forms.ImageSource)。 这是因为 Xamarin.Forms 自动将拖动的图像和文本复制到兼容的放置目标。

## <a name="handle-the-dragover-event"></a>处理 DragOver 事件

可以视需要选择处理 `DropGestureRecognizer.DragOver` 事件，以指明放置目标允许执行的操作类型。 这可以通过设置 `DragOver` 事件随附的 `DragEventArgs` 对象的 `DataPackageOperation` 类型 `AcceptedOperation` 属性来完成。

`DataPackageOperation` 枚举定义下列成员：

- `None`：指明不执行任何操作。
- `Copy`：指明将拖动源内容复制到放置目标。

> [!IMPORTANT]
> 当 `DragEventArgs` 对象创建时，`AcceptedOperation` 属性默认为 `DataPackageOperation.Copy`。

下面的 XAML 示例展示了为 `DragOver` 事件注册处理程序的 `DropGestureRecognizer`：

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True"
                               DragOver="OnDragOver" />
    </Image.GestureRecognizers>
</Image>
```

在此示例中，`DropGestureRecognizer` 附加到 [`Image`](xref:Xamarin.Forms.Image) 对象。 将拖动源被拖动到放置目标上但尚未放置时，就会触发 `DragOver` 事件，此事件执行 `OnDragOver` 事件处理程序：

```csharp
void OnDragOver(object sender, DragEventArgs e)
{
    e.AcceptedOperation = DataPackageOperation.None;
}
```

在此示例中，`DragEventArgs` 对象的 `AcceptedOperation` 属性设置为 `DataPackageOperation.None`。 这样可确保在拖动源被放置到放置目标上时不执行任何操作。

## <a name="process-the-data-package"></a>处理数据包

在放置目标上释放拖动源时，就会触发 `Drop` 事件。 当发生这种情况时，如果拖动源被放置到以下控件上，Xamarin.Forms 就会自动尝试从数据包中检索数据：

- 文本控件。 可以将文本值放置到 [`CheckBox`](xref:Xamarin.Forms.CheckBox)、[`DatePicker`](xref:Xamarin.Forms.DatePicker)、[`Editor`](xref:Xamarin.Forms.Editor)、[`Entry`](xref:Xamarin.Forms.Entry)、[`Label`](xref:Xamarin.Forms.Label)、[`RadioButton`](xref:Xamarin.Forms.RadioButton)、[`Switch`](xref:Xamarin.Forms.Switch) 和 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 对象上。
- 图像控件。 可以将图像放置到 [`Button`](xref:Xamarin.Forms.Button)、[`Image`](xref:Xamarin.Forms.Image)和 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 控件上。

下表显示了当将基于文本的拖动源放置到文本控件上时设置的属性和尝试执行的任何转换：

| 控制 | 属性 | 转换 |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `string` 转换为 `bool`。 |
| `DatePicker` | `Date` | `string` 转换为 `DateTime`。 |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `string` 转换为 `bool`。 |
| `Switch` | `IsToggled` | `string` 转换为 `bool`。 |
| `TimePicker` | `Time` | `string` 转换为 `TimeSpan`。 |

对于除文本和图像以外的内容，你需要自行处理数据包。

`Drop` 事件随附的 `DropEventArgs` 类定义了类型为 `DataPackageView` 的 `Data` 属性。 此属性表示数据包的只读版本。

### <a name="retrieve-image-or-text-data"></a>检索图像或文本数据

使用 `DataPackageView` 类中定义的方法，可以在 `Drop` 事件的处理程序中从数据包中检索图像或文本数据。

`DataPackageView` 类包含 `GetImageAsync` 和 `GetTextAsync` 方法。 `GetImageAsync` 方法从数据包中检索存储在 `DataPackage.Image` 属性中的图像，并返回 `Task<ImageSource>`。 同样，`GetTextAsync` 方法从数据包中检索存储在 `DataPackage.Text` 属性中的文本，并返回 `Task<string>`。

下面的示例展示了从 `Path` 的数据包中检索文本的 `Drop` 事件处理程序：

```csharp
async void OnDrop(object sender, DropEventArgs e)
{
    string text = await e.Data.GetTextAsync();

    // Perform logic to take action based on the text value.
}
```

在此示例中，文本数据是使用 `GetTextAsync` 方法从数据包中检索的。 然后，可以执行基于文本值的操作。

### <a name="retrieve-data-from-the-property-bag"></a>从属性包中检索数据

通过访问数据包的 `Properties` 集合，可以在 `Drop` 事件的处理程序中从数据包中检索任何数据。

`DataPackageView` 类定义了类型为 `DataPackagePropertySetView` 的 `Properties` 属性。 `DataPackagePropertySetView` 类表示存储为 `Dictionary<string, object>` 的只读属性包。

下面的示例展示了从 `Rectangle` 的数据包的属性包中检索数据的 `Drop` 事件处理程序：

```csharp
void OnDrop(object sender, DropEventArgs e)
{
    Square square = (Square)e.Data.Properties["Square"];

    // Perform logic to take action based on retrieved value.
}
```

在此示例中，`Square` 对象是通过指定“Square”字典键从数据包的属性包中检索的。 然后，可以执行基于检索到的值的操作。

## <a name="related-links"></a>相关链接

- [拖放手势（示例）](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)