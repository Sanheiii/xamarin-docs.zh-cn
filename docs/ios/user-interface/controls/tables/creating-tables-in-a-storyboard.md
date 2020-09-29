---
title: 在 iOS 设计器中使用表
description: 在前面的部分中，我们探讨了如何使用表进行开发。 在此第五部分和最后一节中，我们将聚合迄今为止了解到的内容，并使用情节提要创建基本的工作列表应用程序。
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 963151c8f5f88373fd6d71a2bb74bd2dbe5d6ab5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430076"
---
# <a name="working-with-tables-in-the-ios-designer"></a>在 iOS 设计器中使用表

情节提要是创建 iOS 应用程序的 WYSIWYG 方法，在 Mac 和 Windows 上的 Visual Studio 中受支持。 有关情节提要的详细信息，请参阅 [演示图板简介](~/ios/user-interface/storyboards/index.md) 文档。 利用情节提要，还可以编辑表 *中* 的单元格布局，这简化了表和单元格的开发

在 iOS 设计器中配置表视图的属性时，可以选择以下两种类型的单元格内容： **动态** 或 **静态** 原型内容。

<a name="Prototype_Content"></a>

## <a name="dynamic-prototype-content"></a>动态原型内容

`UITableView`具有原型内容的通常用于显示原型单元 (或单元的数据列表，因为可以为列表中的每个项定义多个) 。 单元不需要实例化，而是 `GetView` 通过调用其的方法在方法中获取 `DequeueReusableCell` `UITableViewSource` 。

 <a name="Static_Content"></a>

## <a name="static-content"></a>静态内容

`UITableView`具有静态内容的可以在设计图面上直接设计表。 可以通过更改属性并添加控件，将单元拖到表中并进行自定义。

 <a name="Creating_a_Storyboard-driven_app"></a>

## <a name="creating-a-storyboard-driven-app"></a>创建情节提要驱动的应用

StoryboardTable 示例包含一个简单的主-从应用，它使用情节提要中的这两种类型的 UITableView。 本部分的其余部分介绍如何生成一个小型的待办事项列表示例，此示例在完成时将如下所示：

 [![示例屏幕](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

用户界面将使用情节提要生成，并且这两个屏幕将使用 UITableView。 主屏幕使用 *原型内容* 来布局行，详细信息屏幕使用 *静态内容* 创建使用自定义单元布局的数据输入窗体。

## <a name="walkthrough"></a>演练

在 Visual Studio 中创建一个新的解决方案，_方法是使用_ ** (创建) "新建项目" )  (>。 **

 [![“创建新项目”对话框](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

解决方案将打开，其中包含某些 c # 文件和 `Main.storyboard` 已创建的文件。 双击该 `Main.storyboard` 文件以在 IOS 设计器中将其打开。

<a name="Modifying_the_Storyboard"></a>

## <a name="modifying-the-storyboard"></a>修改情节提要

将通过三个步骤编辑情节提要：

- 首先，设置所需的视图控制器布局并设置其属性。
- 其次，通过将对象拖放到视图上来创建 UI
- 最后，将所需的 UIKit 类添加到每个视图中，并为各个控件提供一个名称，以便可以在代码中引用它们。

情节提要完成后，可以添加代码以使一切正常工作。

<a name="Layout_The_View_Controllers"></a>

### <a name="layout-the-view-controllers"></a>布局视图控制器

首次更改情节提要时，会删除现有的详细信息视图，并将其替换为 UITableViewController。 执行以下步骤：

1. 选择视图控制器底部的栏并将其删除。
2. 将 **导航控制器** 和 **表视图控制器** 从工具箱拖至情节提要。 
3. 创建从根视图控制器到刚刚添加的第二个表视图控制器的 segue。 若要创建 segue，请控制并将 *详细信息单元* 拖到新添加的 UITableViewController 中。 选择选项 "在**Segue**选项下**显示**"。 
4. 选择创建的新 segue，并为其指定一个标识符以在代码中引用此 segue。 单击 "segue"，并在 `TaskSegue` **Properties Pad**中输入**标识符**，如下所示：    
  [![属性面板中的命名 segue](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下来，通过选择两个表视图并使用 Properties Pad 进行配置。 请确保选择 "查看"，而不是 "查看控制器" –您可以使用文档大纲来帮助选择。

6. 将根视图控制器改为内容： Design Surface 上视图 (的  **动态原型** 将为  **原型内容** ) 的标签：

    [![将 Content 属性设置为动态原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. 将新的 **UITableViewController** 更改为  **Content： Static Cells**。 

8. 新的 UITableViewController 必须具有其类名称和标识符集。 在**Properties Pad**中为**类**选择视图控制器并键入_TaskDetailViewController_ –这会 `TaskDetailViewController.cs` 在 Solution Pad 中创建新的文件。 输入 **StoryboardID** 作为 _详细信息_，如以下示例中所示。 稍后将使用它在 c # 代码中加载此视图：  

    [![设置情节提要 ID](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 情节提要设计图面现在应如下所示 (根视图控制器的导航项标题已更改为 "任务板" ) ：

    [![设计图面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  

<a name="Create_the_UI"></a>

### <a name="create-the-ui"></a>创建 UI

既然已配置视图和 segue，则需要添加用户界面元素。

#### <a name="root-view-controller"></a>根视图控制器

首先，选择母版视图控制器中的原型单元，并将 **标识符** 设置为 _taskcell_，如下图所示。 稍后在代码中将使用它来检索此 UITableViewCell 的实例：

 [![设置单元标识符](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下来，你将需要创建一个将添加新任务的按钮，如下所示：

[![导航栏中的 "条形" 按钮项](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

执行以下操作： 

- 将 " **条形" 按钮项** 从工具箱拖动到 _导航栏的右侧_。
- 在 **Properties Pad**的 " **条形按钮项** " 下，选择 "  **标识符：添加** (以使其 *+*) 。 
- 为其指定一个名称，以便可以在以后的代码中识别该名称。 请注意，您需要为根视图控制器提供类名 (例如 **ItemViewController**) ，以允许您设置条形按钮项的名称。

#### <a name="taskdetail-view-controller"></a>TaskDetail 视图控制器

详细信息视图需要更多的工作。 需要将表视图单元拖到视图上，然后用标签、文本视图和按钮填充。 下面的屏幕截图显示完成的 UI，其中包含两个部分。 一个部分包含三个单元格、三个标签、两个文本字段和一个开关，而第二部分包含一个包含两个按钮的单元格：

 [![详细信息视图布局](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

生成完整布局的步骤如下：

选择表视图并打开 **属性板**。 更新以下属性：

- **部分**： _2_ 
- **样式**： _分组_
- **分隔符**： _无_
- **选定内容**： _无选择_

选择顶部部分，然后在 " **属性 > 表视图部分** 将 **行** 更改为 _3_，如下所示：

 [![将顶部部分设置为三行](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

对于每个单元，打开 **Properties Pad** 并设置：

- **样式**：  _自定义_
- **标识符**：为每个单元格选择唯一标识符 (例如。 "_标题_"、"_说明_"、"_完成_" ) 。
- 拖动所需的控件以生成屏幕截图中显示的布局 (将 **UILabel**、 **UITextField** 和 **UISwitch** 置于正确的单元格上，并适当地设置标签，即 ie。标题、说明和完成) 。

在第二部分中，将 " **行** " 设置为 " _1_ "，并抓住单元格的底部调整大小控点，使其更高。

- **将标识符：设置**为唯一值 (例如。 "保存" ) 。 
- **设置 "背景**：  _清除颜色_ "。
- 将两个按钮拖到单元格上，并适当地设置其标题 (例如， _保存_ 并 _删除_) ，如下所示：

   [![设置下一部分中的两个按钮](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

此时，您可能还需要对单元和控件设置约束以确保自适应布局。

### <a name="adding-uikit-class-and-naming-controls"></a>添加 UIKit 类和命名控件

创建情节提要的最后几个步骤。 首先，我们必须在 " **标识" > 名称** "下为每个控件提供一个名称，以便以后可以在代码中使用它们。 将这些名称命名如下：

- **Title UITextField** ： _TitleText_
- **说明 UITextField** ： _NotesText_
- **UISwitch** ： _DoneSwitch_
- **Delete UIButton** ： _DeleteButton_
- **Save UIButton** ： _SaveButton_

<a name="Adding_Code"></a>

## <a name="adding-code"></a>添加代码

剩余的工作将在使用 c # 的 Mac 或 Windows 上的 Visual Studio 中完成。 请注意，代码中使用的属性名称反映了上述演练中设置的属性名称。

首先，我们要创建一个 `Chores` 类，它将提供一种方法来获取和设置 ID、名称、注释和完成的布尔值，以便我们可以在整个应用程序中使用这些值。

在 `Chores` 类中添加以下代码：

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接下来，创建一个 `RootTableSource` 继承自的类 `UITableViewSource` 。 

此表和非情节提要表视图的区别在于： `GetView` 方法不需要实例化任何单元– `theDequeueReusableCell` 方法将始终返回具有匹配标识符) 的原型单元 (的实例。

以下代码来自 `RootTableSource.cs` 文件：

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

若要使用 `RootTableSource` 类，请在的构造函数中创建新的集合 `ItemViewController` ：

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在中 `ViewWillAppear` ，将集合传递到源并分配给表视图：

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果现在运行应用程序，主屏幕现在将加载并显示两个任务的列表。 当涉及到任务时，由情节提要定义的 segue 将导致显示 "详细信息" 屏幕，但此时不会显示任何数据。

若要在 segue 中 "发送参数"，请重写 `PrepareForSegue` 方法，并在 `DestinationViewController` 此示例中的 (上设置属性 `TaskDetailViewController`) 。 目标视图控制器类将已经实例化但尚未向用户显示-这意味着你可以设置类的属性，但不能修改任何 UI 控件：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

在 `TaskDetailViewController` 方法中，将 `SetTask` 其参数分配给属性，以便在 ViewWillAppear 中引用它们。 调用时不能修改控件属性， `SetTask` 因为在调用时可能不存在 `PrepareForSegue` ：

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

此时，segue 将打开 "详细信息" 屏幕，并显示所选的任务信息。 遗憾的是，" **保存** " 和 " **删除** " 按钮没有实现。 在实现按钮之前，请将以下方法添加到 **ItemViewController.cs** ，以更新基础数据并关闭详细信息屏幕：

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

接下来，需要将该按钮的 `TouchUpInside` 事件处理程序添加到 `ViewDidLoad` **TaskDetailViewController.cs**的方法。 `Delegate`对的属性引用是 `ItemViewController` 专门创建的，因此我们可以调用 `SaveTask` 和 `DeleteTask` ，后者将此视图作为其操作的一部分进行关闭：

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

要生成的最后一项功能是创建新任务。 在 **ItemViewController.cs** 中，添加一个方法，用于创建新任务并打开详细信息视图。 若要从情节提要实例化视图，请将方法用于该 `InstantiateViewController` `Identifier` 视图的; 在此示例中，将为 "详细信息"：

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

最后，将导航栏中的按钮连接到 **ItemViewController.cs**的 `ViewDidLoad` 方法，以调用它：

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

完成情节提要示例-完成的应用如下所示：

[![完成的应用](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

该示例演示：

- 使用原型内容创建一个表，其中单元定义用于显示数据列表。 
- 创建具有静态内容的表以生成输入窗体。 这包括更改表样式并添加部分、单元格和 UI 控件。 
- 如何创建 segue 并重写  `PrepareForSegue` 方法，以通知目标视图其所需的任何参数。 
- 直接用方法加载情节提要视图  `Storyboard.InstantiateViewController` 。

## <a name="related-links"></a>相关链接

- [StoryboardTable (示例) ](/samples/xamarin/ios-samples/storyboardtable)
- [情节提要简介](~/ios/user-interface/storyboards/index.md)