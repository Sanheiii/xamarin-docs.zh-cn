---
title: Xamarin 中的标准控件
description: 本文介绍如何使用 AppKit 应用程序中的标准控件，例如按钮、标签、文本字段、复选框和分段控件。 它介绍如何将它们添加到 Interface Builder 的接口中，并在代码中与它们进行交互。
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fe887d837930ebc75fed0fb7c163a3f30ad83af9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008367"
---
# <a name="standard-controls-in-xamarinmac"></a>Xamarin 中的标准控件

_本文介绍如何使用 AppKit 应用程序中的标准控件，例如按钮、标签、文本字段、复选框和分段控件。它介绍如何将它们添加到 Interface Builder 的接口中，并在代码中与它们进行交互。_

在 Xamarin 应用C# *程序中使用*和 .net 时，可以访问 AppKit 和*Xcode*中的开发人员所使用的相同的控件。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的_Interface Builder_来创建和维护 Appkit 控件（或者在代码中C#直接创建）。

AppKit 控件是用于创建 Xamarin Mac 应用程序的用户界面的 UI 元素。 它们由元素组成，如按钮、标签、文本字段、复选框和分段控件，并在用户处理即时操作或可见结果时引发。

[![](standard-controls-images/intro01.png "The example app main screen")](standard-controls-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用 AppKit 控件的基本知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了我们将在本文。

你可能想要查看[Xamarin](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，并说明用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>控件和视图简介

macOS （以前称为 Mac OS X）通过 AppKit 框架提供一组标准用户界面控件。 它们由元素组成，如按钮、标签、文本字段、复选框和分段控件，并在用户处理即时操作或可见结果时引发。

所有 AppKit 控件都具有适用于大多数使用的标准内置外观，某些控件指定在窗口框架区域或_Vibrance 效果_上下文中使用的替代外观，例如，在侧栏区域或通知中心小组件.

当使用 AppKit 控件时，Apple 建议使用以下准则：

- 避免在同一视图中混合控制大小。
- 通常，应避免垂直调整控件大小。
- 在控件内使用系统字体和适当的文本大小。
- 在控件之间使用适当的间距。

有关详细信息，请参阅[pleas 的 "](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)[关于控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)" 部分。

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>在窗口框架中使用控件

有一个 AppKit 控件子集，其中包含一个显示样式，使其可以包含在窗口的框架区域中。 有关示例，请参阅邮件应用的工具栏：

[![](standard-controls-images/mailapp.png "A Mac Window frame")](standard-controls-images/mailapp.png#lightbox)

- 带**圆圈的圆形按钮**-样式为 `NSTexturedRoundedBezelStyle`的 `NSButton`。
- 带**纹理舍入的控件**-具有 `NSSegmentStyleTexturedRounded`样式的 `NSSegmentedControl`。
- 带**纹理舍入的控件**-具有 `NSSegmentStyleSeparated`样式的 `NSSegmentedControl`。
- **圆形纹理的弹出菜单**-样式为 `NSTexturedRoundedBezelStyle`的 `NSPopUpButton`。
- **圆形纹理下拉菜单**-样式为 `NSTexturedRoundedBezelStyle`的 `NSPopUpButton`。
- **搜索栏**-A `NSSearchField`。

在窗口框架中使用 AppKit 控件时，Apple 建议使用以下准则：

- 不要在窗口主体中使用特定于窗口框架的控件样式。
- 不要在窗口框架中使用窗口正文控件或样式。

有关详细信息，请参阅[pleas 的 "](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)[关于控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)" 部分。

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>在 Interface Builder 中创建用户界面

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在项目中自动包含的 `.storyboard` 文件中定义。 若要编辑 windows 设计，请在 "**解决方案资源管理器**中，双击 `Main.storyboard` 文件：

[![](standard-controls-images/edit01.png "Selecting the Main Storyboard in the Solution Explorer")](standard-controls-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![](standard-controls-images/edit02.png "Editing the storyboard in Xcode")](standard-controls-images/edit02.png#lightbox)

若要创建用户界面，请将 UI 元素（AppKit 控件）从 "**库" 检查器**拖动到 Interface Builder 中的 "**界面编辑器**"。 在下面的示例中，**垂直拆分视图**控件已从**库检查器**中获得药品，并将其放置在 "**界面编辑器**" 中的窗口中：

[![](standard-controls-images/edit03.png "Selecting a Split View from the Library")](standard-controls-images/edit03.png#lightbox)

有关在 Interface Builder 中创建用户界面的详细信息，请参阅[Xcode 和 Interface Builder 文档简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)。

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>调整大小和定位

在用户界面中包含控件后 **，可以通过**手动输入值来设置其位置和大小，并控制在父窗口或视图调整大小时，控件自动定位和调整大小的方式：

[![](standard-controls-images/edit04.png "Setting the constraints")](standard-controls-images/edit04.png#lightbox)

使用 " **Autoresizing** " 框外的**光标** _，将控件_控制到给定的（x，y）位置。 例如: 

[![](standard-controls-images/edit05.png "Editing a constraint")](standard-controls-images/edit05.png#lightbox)

指定选定的控件（在 "**层次结构" 视图**中 & **界面编辑器**）在调整大小或移动时将会停滞到窗口或视图的顶部和右侧位置。 

编辑器控件属性的其他元素，如 Height 和 Width：

[![](standard-controls-images/edit06.png "Setting the height")](standard-controls-images/edit06.png#lightbox)

你还可以使用**对齐编辑器**来控制元素与约束的对齐方式：

[![](standard-controls-images/edit07.png "The Alignment Editor")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> 不同于 iOS，其中（0，0）是屏幕的左上角，在 macOS （0，0）中是左下角。 这是因为 macOS 使用一个数学坐标系统，其数字值向上和向右增大。 在用户界面上放置 AppKit 控件时，需要考虑这一点。

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>设置自定义类

有时，使用 AppKit 控件时，需要为子类和现有控件创建子类，并创建该类的自定义版本。 例如，定义源列表的自定义版本：

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

其中 `[Register("SourceListView")]` 指令向目标-C 公开 `SourceListView` 类，以便可以在 Interface Builder 中使用。 有关详细信息，请参阅[Xamarin](~/mac/internals/how-it-works.md)示例文档的C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md)部分，它解释了用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

在上面的代码准备就绪后，可以将要扩展的基类型的 AppKit 控件拖到设计图面上（在以下示例中为**源列表**），切换到**标识检查器**并将**自定义类**设置为你向目标系统（例如 `SourceListView`）公开：

[![](standard-controls-images/edit10.png "Setting a custom class in Xcode")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>公开插座和操作

在代码中C#访问 AppKit 控件之前，需要将它公开为**插座**或和**操作**。 若要执行此操作，请在 "**界面层次结构**" 或 "**界面编辑器**" 中选择给定的控件，并切换到 "**助手" 视图**（确保你已选择要编辑的窗口 `.h`）：

[![](standard-controls-images/edit11.png "Selecting the correct file to edit")](standard-controls-images/edit11.png#lightbox)

Control-从 "AppKit" 控件拖动到 "为 `.h` 文件，以开始创建**插座**或**操作**：

[![](standard-controls-images/edit12.png "Dragging to create an Outlet or Action")](standard-controls-images/edit12.png#lightbox)

选择要创建的公开类型，并为**插座**或**操作**指定**名称**： 

[![](standard-controls-images/edit13.png "Configuring the Outlet or Action")](standard-controls-images/edit13.png#lightbox)

有关使用**输出口**和**操作**的详细信息，请参阅[Xcode 和 Interface Builder 文档简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)中的 "[插座和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)" 部分。

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

切换回 Xcode 的 Visual Studio for Mac 时，在 Xcode 中所做的任何更改都将自动与 Xamarin 项目同步。

如果在**解决方案资源管理器**中选择 `SplitViewController.designer.cs`，你将能够看到你的**输出口**和**操作**在我们C#的代码中的连接方式：

[![](standard-controls-images/sync01.png "Synchronizing Changes with Xcode")](standard-controls-images/sync01.png#lightbox)

请注意 `SplitViewController.designer.cs` 文件中的定义：

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

在 Xcode 中的 `MainWindow.h` 文件中，与定义对齐：

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

正如您所看到的，Visual Studio for Mac 侦听 `.h` 文件的更改，然后在相应的 `.designer.cs` 文件中自动同步这些更改，以将这些更改公开给您的应用程序。 你可能还注意到 `SplitViewController.designer.cs` 是分部类，因此 Visual Studio for Mac 无需修改将覆盖对类所做的任何更改的 `SplitViewController.cs`。

通常，您不需要自行打开 `SplitViewController.designer.cs`，此处只是出于教育目的提供此内容。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac 会自动查看在 Xcode 中所做的任何更改，并将其同步到 Xamarin Mac 项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>使用按钮

AppKit 提供了几种可在用户界面设计中使用的按钮类型。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)" 部分。 

[![](standard-controls-images/buttons01.png "An example of the different button types")](standard-controls-images/buttons01.png#lightbox)

如果已通过**输出口**公开了某个按钮，以下代码将对其进行响应：

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

对于通过**操作**公开的按钮，会自动为你创建一个 `public partial` 方法，该方法具有你在 Xcode 中选择的名称。 若要响应**操作**，请完成在其上定义**操作**的类中的分部方法。 例如:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

对于状态为 "状态" 的按钮（如 **"打开" 和 "** **关闭**"），可以使用 "`State`" 属性针对 `NSCellStateValue` 枚举来检查或设置状态。 例如:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

其中 `NSCellStateValue` 可以是：

- **"打开**" 按钮或选中控件（如复选框中的复选框）。
- **关闭**-未按下按钮或未选择控件。
- **混合模式**：**开启**和**关闭**状态。

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>将按钮标记为默认值和设置密钥等效项

对于已添加到用户界面设计的任何按钮，你可以将该按钮标记为_默认_按钮，该按钮将在用户按下键盘上的**Return/Enter**键时被激活。 在 macOS 中，默认情况下，此按钮将收到蓝色背景色。

若要将按钮设置为默认值，请在 Xcode 的 Interface Builder 中选择它。 接下来，在 "**属性检查器**" 中，选择 "**等效关键字**" 字段并按**Return/enter**键：

[![](standard-controls-images/buttons03.png "Editing the Key Equivalent")](standard-controls-images/buttons03.png#lightbox)

同样，可以使用键盘而不是鼠标来分配可用于激活按钮的任何键序列。 例如，在上面的图像中按命令 C 键。

当应用程序运行时，如果用户按下了命令-C，则该按钮的操作将被激活（如果用户单击了该按钮）。

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>使用复选框和单选按钮

AppKit 提供了几种类型的复选框和单选按钮组，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)" 部分。 

[![](standard-controls-images/buttons02.png "An example of the available checkbox types")](standard-controls-images/buttons02.png#lightbox)

复选框和单选按钮（通过**插座**公开）具有状态（如**打开**和**关闭**），可以使用 "`State`" 属性针对 `NSCellStateValue` 枚举来检查或设置状态。 例如:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

其中 `NSCellStateValue` 可以是：

- **"打开**" 按钮或选中控件（如复选框中的复选框）。
- **关闭**-未按下按钮或未选择控件。
- **混合模式**：**开启**和**关闭**状态。

若要选择单选按钮组中的按钮，请公开单选按钮以选择作为**插座**，并设置其 `State` 属性。 例如：

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

若要获取单选按钮的集合以作为组并自动处理选定状态，请创建新**操作**并将组中的每个按钮附加到其中：

![](standard-controls-images/buttons04.png "Creating a new Action")

接下来，在**属性检查器**中为每个单选按钮指定唯一 `Tag`：

![](standard-controls-images/buttons05.png "Editing a radio button tag")

保存更改并返回到 Visual Studio for Mac，添加代码以处理所有单选按钮附加到的**操作**：

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

您可以使用 "`Tag`" 属性来查看选定的单选按钮。

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>使用菜单控件

AppKit 提供了几种类型的菜单控件，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[菜单控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1)" 部分。 

[![](standard-controls-images/menu01.png "Example menu controls")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>提供菜单控件数据

可以将可用于 macOS 的菜单控件设置为从内部列表（可通过代码在 Interface Builder 中预定义）或通过提供自己的自定义外部数据源填充下拉列表。

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>使用内部数据

除了在 Interface Builder 中定义项以外，菜单控件（如 `NSComboBox`）还提供了一组完整的方法，使您可以在其维护的内部列表中添加、编辑或删除项：

- `Add`-将新项添加到列表的末尾。
- `GetItem`-返回给定索引处的项。
- `Insert`-在列表中的给定位置插入一个新项。
- `IndexOf`-返回给定项的索引。
- `Remove`-从列表中删除给定的项。
- `RemoveAll`-从列表中删除所有项。
- `RemoveAt`-删除给定索引处的项。
- `Count`-返回列表中的项数。

> [!IMPORTANT]
> 如果使用的是外部数据源（`UsesDataSource = true`），调用上述任何方法将引发异常。

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>使用外部数据源

您可以选择使用外部数据源并为项提供自己的后备存储（如 SQLite 数据库），而不是使用内置的内部数据提供菜单控件的行。

若要使用外部数据源，你将创建 Menu 控件数据源的实例（例如`NSComboBoxDataSource`），并重写几个方法来提供所需的数据：

- `ItemCount`-返回列表中的项数。
- `ObjectValueForItem`-返回给定索引的项的值。
- `IndexOfItem`-返回给定项值的索引。
- `CompletedString`-返回部分类型的项值的第一个匹配项值。 仅当启用了 "自动完成" （`Completes = true`）时，才会调用此方法。

有关更多详细信息，请参阅使用[数据库](~/mac/app-fundamentals/databases.md)文档中的[数据库和组合框](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes)部分。

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>调整列表的外观

以下方法可用于调整菜单控件的外观：

- `HasVerticalScroller`-如果 `true`，则控件将显示垂直滚动条。 
- `VisibleItems`-调整在打开控件时显示的项数。 默认值为5（5）。
- `IntercellSpacing`-通过提供 `NSSize` （其中 `Width` 指定左边距和右边距）来调整给定项周围的空间量，`Height` 指定项前后的间距。
- `ItemHeight`-指定列表中每一项的高度。

对于 `NSPopupButtons`的下拉类型，第一个菜单项提供控件的标题。 例如： 

[![](standard-controls-images/menu02.png "An example menu control")](standard-controls-images/menu02.png#lightbox)

若要更改标题，请将此项公开为**插座**，并使用如下所示的代码：

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>操作选定项

以下方法和属性允许您操作菜单控件列表中的选定项：

- `SelectItem`-选择给定索引处的项。
- `Select`-选择给定的项值。
- `DeselectItem`-取消选择给定索引处的项。
- `SelectedIndex`-返回当前选定项的索引。
- `SelectedValue`-返回当前选定项的值。

使用 `ScrollItemAtIndexToTop` 在列表顶部显示给定索引处的项，并将 `ScrollItemAtIndexToVisible` 滚动到列表，直到给定索引处的项可见。

<a name="Responding to Events" />

### <a name="responding-to-events"></a>对事件作出响应

菜单控件提供以下事件来响应用户交互：

- `SelectionChanged`-当用户从列表中选择一个值时调用。
- `SelectionIsChanging`-在新用户选择的项成为活动选定项之前调用。
- `WillPopup`-在显示项的下拉列表之前调用。
- `WillDismiss`-在关闭项下拉列表之前调用。

对于 `NSComboBox` 控件，它们包含与 `NSTextField`相同的所有事件，例如在用户编辑组合框中的文本值时调用的 `Changed` 事件。

或者，您可以通过将项目附加到**操作**并使用如下代码来响应用户触发的**操作**，来响应 Interface Builder 所选的内部数据菜单项。

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

有关使用菜单和菜单控件的详细信息，请参阅[菜单](~/mac/user-interface/menu.md)和[弹出按钮以及下拉列表](~/mac/user-interface/menu.md)文档。

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>使用选择控件

AppKit 提供了几种类型的选择控件，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[选择控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1)" 部分。 

[![](standard-controls-images/select01.png "Example selection controls")](standard-controls-images/select01.png#lightbox)

可以通过两种方法来跟踪选择控件是否具有用户交互，方法是将其作为**操作**公开。 例如:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

或者将**委托**附加到 `Activated` 事件。 例如:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

若要设置或读取选择控件的值，请使用 `IntValue` 属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

专业控件（如色彩良好和图像良好）具有其值类型的特定属性。 例如：

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker` 具有用于直接处理日期和时间的以下属性：

- **DateValue** -`NSDate`的当前日期和时间值。
- **本地**-用户作为 `NSLocal`的位置。
- **TimeInterval** -`Double`的时间值。
- **时区**-用户的时区，作为 `NSTimeZone`。

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>使用指示器控件

AppKit 提供了几种类型的指示器控件，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[指示器控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1)" 部分。 

[![](standard-controls-images/level01.png "Example indicator controls")](standard-controls-images/level01.png#lightbox)

可以通过两种方法来跟踪指示器控件是否具有用户交互，方法是将其公开为**操作**或**插座**，并将**委托**附加到 `Activated` 事件。 例如:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

若要读取或设置指示器控件的值，请使用 `DoubleValue` 属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

显示不确定的和异步的进度指示器。 使用 `StartAnimation` 方法在显示动画时启动动画。 例如:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

调用 `StopAnimation` 方法将停止动画。

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>使用文本控件

AppKit 提供了几种类型的文本控件，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[文本控制](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1)" 部分。 

[![](standard-controls-images/text01.png "Example text controls")](standard-controls-images/text01.png#lightbox)

对于文本字段（`NSTextField`），可以使用以下事件来跟踪用户交互：

- **已更改**-当用户更改字段的值时，将激发。 例如，在每个类型的字符上。
- **EditingBegan** -当用户选择要编辑的字段时触发。
- **EditingEnded** -用户按下 enter 键或离开字段的时间。

使用 `StringValue` 属性读取或设置字段的值。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

对于显示或编辑数值的字段，可以使用 "`IntValue`" 属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView` 提供内置格式设置的功能齐全的文本编辑和显示区域。 与 `NSTextField`一样，请使用 `StringValue` 属性读取或设置区域的值。

有关在 Xamarin 应用程序中使用文本视图的复杂示例示例，请参阅[SourceWriter 示例应用](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>使用内容视图

AppKit 提供了几种类型的内容视图，可用于用户界面设计。 有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)部分。

[![](standard-controls-images/content01.png "An example content view")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Segue 是一个瞬态 UI 元素，它提供直接与特定控件或屏幕区域相关的功能。 Segue 在包含其相关控件或区域的窗口上方浮动，其边框包含一个箭头，用于指示其出现的位置。

若要创建 segue，请执行以下操作：

1. 通过在**解决方案资源管理器**中双击要添加 segue 的窗口的 `.storyboard` 文件，将其打开。
2. 将 "**视图控制器**" 从 "**库" 检查器**拖到 "**界面编辑器**"： 

    [![](standard-controls-images/content02.png "Selecting a View Controller from the Library")](standard-controls-images/content02.png#lightbox)
3. 定义**自定义视图**的大小和布局： 

    [![](standard-controls-images/content04.png "Editing the layout")](standard-controls-images/content04.png#lightbox)
4. 按住 ctrl 的同时单击并拖动到**视图控制器**： 

    [![](standard-controls-images/content05.png "Dragging to create a segue")](standard-controls-images/content05.png#lightbox)
5. 从弹出菜单中选择 " **segue** "： 

    [![](standard-controls-images/content06.png "Setting the segue type")](standard-controls-images/content06.png#lightbox)
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

<a name="Tab_Views" />

### <a name="tab-views"></a>选项卡视图

选项卡视图由选项卡列表（类似于分段控件）组成，并与一组称为 "_窗格_" 的视图结合使用。 当用户选择新选项卡时，将显示附加到该选项卡的窗格。 每个窗格都包含自己的一组控件。

使用 Xcode 的 Interface Builder 中的选项卡视图时，请使用 "**属性检查器**" 设置选项卡的数目：

[![](standard-controls-images/content08.png "Editing the number of tabs")](standard-controls-images/content08.png#lightbox)

选择**接口层次结构**中的每个选项卡，以设置其**标题**并向其**窗格**添加 UI 元素：

[![](standard-controls-images/content09.png "Editing the tabs in Xcode")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>数据绑定 AppKit 控件

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 您还可以从您的前端用户界面（_模型-视图-控制器_）进一步分离您的备份数据（_数据模型_），从而更易于维护，更灵活的应用程序设计。

键-值编码（KVC）是一种用于间接访问对象属性的机制，使用键（特殊格式的字符串）来标识属性，而不是通过实例变量或访问器方法（`get/set`）来访问这些属性。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS 的功能，例如键-值观察（KVO）、数据绑定、核心数据、Cocoa 绑定和 scriptability。

有关详细信息，请参阅[数据绑定和键-值编码](~/mac/app-fundamentals/databinding.md)文档的[简单数据绑定](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding)部分。

<a name="Summary" />

## <a name="summary"></a>总结

本文详细介绍了如何使用标准的 AppKit 控件，如 Xamarin. Mac 应用程序中的按钮、标签、文本字段、复选框和分段控件。 其中介绍了如何将它们添加到 Xcode 的 Interface Builder 中的用户界面设计中，通过插座和操作向代码公开这些代码， C#以及如何在代码中使用 AppKit 控件。

## <a name="related-links"></a>相关链接

- [MacControls （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/maccontrols)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [关于控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
