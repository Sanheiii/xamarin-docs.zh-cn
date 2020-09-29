---
title: 在 Xamarin 中自定义表的外观
description: 本文档介绍如何在 Xamarin 中自定义表的外观。 它讨论了单元样式、附件、单元分隔符和自定义单元布局。
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: dd79c2059d53702d947a966327bf22b2043d36c5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429944"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>在 Xamarin 中自定义表的外观

若要更改表的外观，最简单的方法是使用不同的单元格样式。 在的方法中创建每个单元格时，可以更改所使用的单元格样式 `UITableViewSource` `GetCell` 。

## <a name="cell-styles"></a>单元格样式

有四种内置样式：

- **默认** –支持 `UIImageView` 。
- **副标题** –支持 `UIImageView` 和副标题。
- **Value1** -右对齐副标题，支持 `UIImageView` 。
- **Value2** –标题为右对齐，副标题为左对齐 (但不) 图像。

这些屏幕截图显示每种样式的显示方式：

 [![这些屏幕截图显示每种样式的显示方式](customizing-table-appearance-images/image7.png)](customizing-table-appearance-images/image7.png#lightbox)

示例 **CellDefaultTable** 包含用于生成这些屏幕的代码。 单元格样式在 `UITableViewCell` 构造函数中设置，如下所示：

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

然后，可以设置单元格样式的[支持属性](xref:UIKit.UITableViewCell)：

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accessories

单元格可以将以下附件添加到视图右侧：

- **复选标记** -可用于指示表中的多个选择。
- **DetailButton** –独立于单元格的其余部分响应触摸，使其能够执行不同的功能来触摸单元本身 (例如，打开不属于堆栈) 的弹出窗口或新窗口 `UINavigationController` 。
- **DisclosureIndicator** –通常用于指示触摸单元会打开另一个视图。
- **DetailDisclosureButton** –和的组合 `DetailButton` `DisclosureIndicator` 。

如下所示：

 [![示例附件](customizing-table-appearance-images/image8.png)](customizing-table-appearance-images/image8.png#lightbox)

若要显示这些附件之一，可以 `Accessory` 在方法中设置属性 `GetCell` ：

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

`DetailButton`显示或时 `DetailDisclosureButton` ，还应重写， `AccessoryButtonTapped` 以便在接触时执行某种操作。

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

示例 **CellAccessoryTable** 演示了一个使用附件的示例。

## <a name="cell-separators"></a>单元分隔符

单元分隔符是用于分隔表的表单元。 在表上设置属性。

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

还可以将模糊或活力效果添加到分隔符：

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

分隔符还可以包含内陷：

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>创建自定义单元布局

若要更改表的视觉样式，需要提供自定义单元格以使其显示。 自定义单元格可以具有不同的颜色和控件布局。

CellCustomTable 示例实现了一个 `UITableViewCell` 子类，该子类 `UILabel` `UIImage` 使用不同的字体和颜色来定义的自定义布局。 生成的单元格如下所示：

 [![自定义单元布局](customizing-table-appearance-images/image9.png)](customizing-table-appearance-images/image9.png#lightbox)

自定义单元格类只包含三种方法：

- **构造函数** –创建 UI 控件并设置自定义样式属性， (例如。 字体、字号和颜色) 。
- **UpdateCell** –用于  `UITableView.GetCell` 设置单元格的属性的方法。
- **LayoutSubviews** –设置 UI 控件的位置。 在示例中，每个单元格都具有相同的布局，但更复杂的单元格 (尤其是那些具有不同) 大小的单元格，具体取决于所显示的内容。

**CellCustomTable > CustomVegeCell.cs**中的完整示例代码如下：

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

`GetCell`需要修改的方法 `UITableViewSource` 以创建自定义单元格：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```

## <a name="related-links"></a>相关链接

- [WorkingWithTables (示例) ](/samples/xamarin/ios-samples/workingwithtables)