---
title: 在 Xamarin 中拖放
description: 本文档介绍如何使用 iOS 11 中引入的 Api 实现 Xamarin iOS 应用中的拖放。 具体而言，它讨论了如何在 UITableView 中启用拖放。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: bbdfe9682474352fab7c0ae3b1cd7d949cbca0b4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437161"
---
# <a name="drag-and-drop-in-xamarinios"></a>在 Xamarin 中拖放

_为 iOS 11 实现拖放_

iOS 11 包含拖放支持，可在 iPad 上的应用程序之间复制数据。 用户可以选择并拖放来自并行的应用程序的所有类型的内容，也可以通过在应用程序图标上拖动，将触发应用程序打开并允许删除数据：

![将示例从自定义应用拖放到 Notes 应用](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 只能在 iPhone 上的同一应用中使用拖放功能。

请考虑支持在可创建或编辑内容的任何位置执行拖放操作：

- 对于基于 iOS 11 构建的所有应用，文本控件支持拖放，无需任何其他工作。
- 表视图和集合视图包括 iOS 11 中的增强功能，可简化拖放行为。
- 可以进行其他任何视图以支持通过其他自定义进行拖放。

向应用程序添加拖放支持时，可以提供不同级别的内容保真;例如，你可以提供数据的格式化文本和纯文本版本，以便接收应用可以选择最适合拖动目标的数据。 还可以自定义拖动可视化效果，同时允许同时拖动多个项。

## <a name="drag-and-drop-with-text-controls"></a>拖放和文本控件

`UITextView` 并 `UITextField` 自动支持将选定的文本拖出，并在中放置文本内容。

<a name="uitableview"></a>

## <a name="drag-and-drop-with-uitableview"></a>拖放 with UITableView

`UITableView` 具有与表行的拖放交互的内置处理，只需要几个方法来启用默认行为。

涉及两个接口：

- `IUITableViewDragDelegate` –当在表视图中启动拖动时，包信息。
- `IUITableViewDropDelegate` –尝试和完成删除时处理信息。

在 [DragAndDropTableView 示例](/samples/xamarin/ios-samples/ios11-draganddroptableview) 中，这两个接口都是在类上实现的 `UITableViewController` ，以及委托和数据源。 它们是在方法中指定的 `ViewDidLoad` ：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

下面说明了这两个接口所需的最少代码。

### <a name="table-view-drag-delegate"></a>表视图拖动委托

支持从表视图中拖动行的 _唯一方法是_ `GetItemsForBeginningDragSession` 。 如果用户开始拖动行，将调用此方法。

下面显示了实现。 它检索与拖动的行相关联的数据，对其进行编码，并配置一个 `NSItemProvider` 来确定应用程序将如何处理操作的 "drop" 部分 (例如，是否可以处理该操作的 "删除" 部分， `PlainText` 在此示例) ：

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

可以实现拖动委托上的许多可选方法，以自定义拖动行为，例如提供多个数据表示形式，这些数据表示形式可以利用目标应用中的 (例如，格式化文本和纯文本，或绘图) 的矢量和位图版本。 还可以提供在同一应用中拖放时要使用的自定义数据表示形式。

### <a name="table-view-drop-delegate"></a>表视图删除委托

拖动操作在表视图上发生或在其上方完成时，将调用 drop 委托上的方法。 所需的方法确定是否允许删除数据，以及在删除完成后采取的操作：

- `CanHandleDropSession` –虽然正在进行拖动并且可能会在应用程序上删除，但此方法确定是否允许删除正在拖动的数据。
- `DropSessionDidUpdate` –拖动过程中，将调用此方法来确定所需的操作。 要拖动的表视图中的信息、拖动会话和可能的索引路径都可用于确定为用户提供的行为和视觉反馈。
- `PerformDrop` –当用户通过) 抬起其手指来完成 drop (时，此方法将提取要拖动的数据并修改表视图，以将数据添加到 (或行) 的新行中。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指示表视图是否可以接受要拖动的数据。 在此代码段中， `CanLoadObjects` 用于确认此表视图可以接受字符串数据。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`拖动操作正在进行时，将重复调用方法，以向用户提供视觉提示。

在下面的代码中， `HasActiveDrag` 用于确定操作是否源自当前表视图。 如果是这样，则只允许移动单个行。
如果拖动来自其他源，则会显示复制操作：

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

删除操作可以是 `Cancel` 、 `Move` 或之一 `Copy` 。

Drop 意向可以是插入新行，或向现有行添加/追加数据。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`当用户完成操作时调用方法，并修改表视图和数据源以反映已删除的数据。

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

可以添加其他代码以异步加载大型数据对象。

### <a name="testing-drag-and-drop"></a>测试拖放

必须使用 iPad 测试 [示例](/samples/xamarin/ios-samples/ios11-draganddroptableview)。
与另一个应用程序 (，如 "注释) 并在它们之间拖动行和文本：

![正在进行拖动操作的屏幕截图](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>相关链接

- [将人体学接口准则拖放 (Apple) ](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖放表视图示例](/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [拖放集合视图示例](/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [ (视频的拖放 (WWDC) 简介) ](https://developer.apple.com/videos/play/wwdc2017/203/)
- [拖放与 "集合" 和 "表" 视图 (WWDC)  (视频 ") ](https://developer.apple.com/videos/play/wwdc2017/223/)