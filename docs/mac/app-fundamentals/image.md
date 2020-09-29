---
title: Xamarin 中的映像
description: '本文介绍如何在 Xamarin. Mac 应用程序中使用图像和图标。 本文介绍如何创建和维护在 c # 代码和 Xcode 的 Interface Builder 中创建应用程序图标和使用图像所需的映像。'
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 53a3b43347e51cfbc99b2b798f0ad5c787f04847
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435555"
---
# <a name="images-in-xamarinmac"></a>Xamarin 中的映像

_本文介绍如何在 Xamarin. Mac 应用程序中使用图像和图标。本文介绍如何创建和维护在 c # 代码和 Xcode 的 Interface Builder 中创建应用程序图标和使用图像所需的映像。_

## <a name="overview"></a>概述

在 Xamarin 应用*程序中使用*c # 和 .net 时，可以访问与在 Xcode 和*Xcode*中工作的开发人员相同的图像和图标工具。

有多种方法可在 (macOS 中使用映像资产（以前称为 Mac OS X) 应用程序）。 从简单地将图像作为应用程序的一部分进行显示，并将其分配给 UI 控件（如工具栏或源列表项）以提供图标，Xamarin 使你可以使用以下方法轻松地将精彩图稿添加到 macOS 应用程序： 

- **UI 元素** -图像可以显示为背景，也可以作为应用程序的一部分显示在图像视图中 (`NSImageView`) 。
- **按钮** -图像可以在按钮中显示 (`NSButton`) 。
- **图像单元格** -作为基于表的控件 (`NSTableView` 或) 的一部分 `NSOutlineView` ，可以在图像单元 () 中使用图像 `NSImageCell` 。
- **工具栏项** -可以将图像添加到工具栏 (`NSToolbar`) 作为图像工具栏项 (`NSToolbarItem`) 。
- **源列表图标** -作为源列表的一部分 (特殊格式的 `NSOutlineView`) 。
- **应用图标** -可将一系列图像组合到一组中 `.icns` ，并将其用作应用程序的图标。 有关详细信息，请参阅我们的 [应用程序图标](~/mac/deploy-test/app-icon.md) 文档。

此外，macOS 还提供了一组可在整个应用程序中使用的预定义映像。

[![应用的示例运行](image-images/intro01.png "应用的示例运行")](image-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用图像和图标的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

## <a name="adding-images-to-a-xamarinmac-project"></a>向 Xamarin Mac 项目添加图像

在添加要在 Xamarin Mac 应用程序中使用的映像时，开发人员可以使用几个位置和方法将图像文件包含到项目的源：

- **主项目树 [弃用]** -可以直接将图像添加到项目树中。 在从代码中调用存储在主项目树中的图像时，未指定文件夹位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **Resources 文件夹 [弃用]** -"特殊 **资源** " 文件夹适用于将成为应用程序捆绑包的一部分的任何文件，例如图标、启动屏幕或常规图像 (或开发人员希望添加) 的任何其他图像或文件。 当从代码调用 **资源** 文件夹中存储的图像时，就像存储在主项目树中的图像一样，未指定文件夹位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自定义文件夹或子文件夹 [弃用]** -开发人员可以向项目源树添加自定义文件夹并将图像存储在此处。 添加文件的位置可以嵌套在子文件夹中，以便进一步帮助组织项目。 例如，如果开发人员将文件夹添加 `Card` 到项目，并将的子文件夹添加 `Hearts` 到该文件夹中，则在该文件夹中存储图像 **Jack.png** `Hearts` `NSImage.ImageNamed("Card/Hearts/Jack.png")` 会在运行时加载该图像。
- **资产目录图像集 [首选]** -在 OS X El Capitan 中添加， **资产目录映像集** 包含为应用程序提供支持各种设备和规模因素所需的映像的所有版本或表示形式。 ) ，而不是依赖于映像资产文件名 (**@1x** **@2x** 。

<a name="asset-catalogs"></a>

### <a name="adding-images-to-an-asset-catalog-image-set"></a>将图像添加到资产目录图像集

如上所述， **资产目录图像集** 包含为应用程序提供支持各种设备和规模因素所需的映像的所有版本或表示形式。 **映像集**使用资产编辑器来指定属于哪个设备和/或分辨率，而不是依赖于映像资产文件名 (查看) 上的独立于分辨率的图像和图像命名法。

1. 在 **Solution Pad**中，双击 **assets.xcassets** 文件以将其打开以供编辑： 

    ![选择资产。 assets.xcassets](image-images/imageset01.png "选择资产。 assets.xcassets")
2. 右键单击 "资产" **列表** ，然后选择 " **新建映像集**"： 

    [![添加新图像集](image-images/imageset02.png "添加新图像集")](image-images/imageset02-large.png#lightbox)
3. 选择新图像集，将显示编辑器： 

    [![选择新图像集](image-images/imageset03.png "选择新图像集")](image-images/imageset03-large.png#lightbox)
4. 从这里，我们可以为所需的每个不同设备和分辨率拖动图像。 
5. 在 "**资产" 列表**中双击新图像集的**名称**以对其进行编辑： 

    [![编辑映像集名称](image-images/imageset04.png "编辑映像集名称")](image-images/imageset04-large.png#lightbox)
    
添加到**图像集**的特殊**Vector**类，使我们能够在 casset 中包含_PDF_格式的矢量图像，而不是在不同的分辨率下包含单独的位图文件。 使用此方法，你可以为分辨率提供单个矢量文件 **@1x** (格式为矢量 PDF 文件) 并且 **@2x** 文件的和 **@3x** 版本将在编译时生成并包含在应用程序捆绑包中。

[![图像集编辑器接口](image-images/imageset05.png "图像集编辑器接口")](image-images/imageset05-large.png#lightbox)

例如，如果将某个 `MonkeyIcon.pdf` 文件作为资产目录的矢量包含在分辨率为 150 x 150 的资产目录中，则编译后，最终应用捆绑包中将包含以下位图资产：

1. **MonkeyIcon@1x.png** -150 x 150 解决方案。
2. **MonkeyIcon@2x.png** -300 像素 x 300 像素解决方案。
3. **MonkeyIcon@3x.png** -450px x 450px 解决方案。

在资产目录中使用 PDF 矢量图像时，应注意以下事项：

- 这不是完整的矢量支持，因为 PDF 将在编译时将栅格化到位图，并在最终应用程序中附带位图。
- 在资产目录中设置图像后，不能调整图像的大小。 如果尝试 (在代码中或通过使用 "自动布局" 和 "大小" 类调整图像大小) 图像将像任何其他位图一样扭曲。

使用在 Xcode 的 Interface Builder 中 **设置的图像** 时，只需从 **属性检查器**的下拉列表中选择集的名称即可：

![选择 Xcode 的 Interface Builder 中的映像集](image-images/imageset06.png "选择 Xcode 的 Interface Builder 中的映像集")

<a name="Adding-new-Assets-Collections"></a>

### <a name="adding-new-assets-collections"></a>添加新资产集合

使用资产目录中的图像时，有时可能需要创建新的集合，而不是将所有映像添加到 **assets.xcassets** 集合。 例如，在设计按需资源时。

向项目中添加新的资产目录：

1. 右键单击 " **Solution Pad**中的项目，然后选择"**添加**  >  **新文件 ...** "
2. 选择 " **Mac**  >  **资产目录**"，输入集合的**名称**，然后单击 "**新建**" 按钮： 

    ![添加新的资产目录](image-images/asset01.png "添加新的资产目录")

从这里，你可以使用与默认 **assets.xcassets** 集合相同的方式来处理该集合。

### <a name="adding-images-to-resources"></a>向资源添加图像

> [!IMPORTANT]
> Apple 不推荐使用此方法在 macOS 应用中处理图像。 应改用 [资产目录图像集](#asset-catalogs) 来管理应用程序的映像。

在 (在 c # 代码中或从) Interface Builder 中使用 Xamarin 应用程序中的图像文件之前，需要将该文件作为**绑定资源**包含在项目的**Resources**文件夹中。 若要将文件添加到项目中，请执行以下操作：

1. 在**Solution Pad**中右键单击项目中的 "**资源**" 文件夹，然后选择 "**添加**" "  >  **添加文件 ...**"： 

    ![添加文件](image-images/add01.png "添加文件")
2. 从 " **添加文件** " 对话框中，选择要添加到项目中的图像文件，选择 `BundleResource` " **替代生成操作** "，并单击 " **打开** " 按钮：

    [![选择要添加的文件](image-images/add02.png "选择要添加的文件")](image-images/add02-large.png#lightbox)
3. 如果文件不在 **资源** 文件夹中，系统会询问你是否要 **复制**、 **移动** 或 **链接** 这些文件。 选择每个满足您的需求，通常是 **复制**：

    ![选择 "添加" 操作](image-images/add04.png "选择 "添加" 操作")
4. 新文件将包含在项目中，并可供使用： 

    ![添加到 Solution Pad 的新图像文件](image-images/add03.png "添加到 Solution Pad 的新图像文件")
5. 对所需的任何图像文件重复此过程。

你可以在 Xamarin Mac 应用程序中使用任何 png、jpg 或 pdf 文件作为源映像。 在下一部分中，我们将介绍如何添加图像和图标的高分辨率版本，以支持基于 Retina 的 Mac。

> [!IMPORTANT]
> 如果要将图像添加到 **Resources** 文件夹，可以将 " **替代生成操作** " 设置为 " **默认**"。 此文件夹的默认生成操作是 `BundleResource` 。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有应用图形资源的高分辨率版本

添加到 Xamarin 应用程序的任何图形资产 (图标、自定义控件、自定义光标、自定义图稿等，) 除了标准解决方案版本之外，还需要具有高分辨率版本。 这是必需的，这样，在 Retina 显示已配备的 Mac 计算机上运行应用程序时，应用程序的外观就会很好。

### <a name="adopt-the-2x-naming-convention"></a>采用 @2x 命名约定

> [!IMPORTANT]
> Apple 不推荐使用此方法在 macOS 应用中处理图像。 应改用 [资产目录图像集](#asset-catalogs) 来管理应用程序的映像。

当你创建映像的标准和高分辨率版本时，请在你的 Xamarin Mac 项目中包含图像对时遵循此命名约定：

- **标准-分辨率**   - **ImageName** (示例： **tags.png**) 
- **高分辨率**   -  **ImageName@2x.filename-extension** (示例： **tags@2x.png**) 

添加到项目时，它们会如下所示：

![Solution Pad 中的图像文件](image-images/add03.png "Solution Pad 中的图像文件")

将图像分配到 Interface Builder 中的 UI 元素时，只需在_ImageName_中选取文件 **。**_文件名扩展_格式 (例如： **tags.png**) 。 对于在 c # 代码中使用图像，将在_ImageName_中选取文件 **。**_文件名扩展_格式。

当你在 Mac 上运行 Xamarin 的 Mac 应用程序时， _ImageName_**。**_文件名扩展_ 格式图像将在标准分辨率显示时使用，将在 **ImageName@2x.filename-extension** Retina 显示基 mac 上自动选取该图像。

## <a name="using-images-in-interface-builder"></a>在 Interface Builder 中使用图像

已添加到 Xamarin 项目中的 " **资源** " 文件夹并将 "生成操作" 设置为 " **BundleResource** " 的任何图像资源将自动显示在 Interface Builder 中，并可以选择作为 UI 元素的一部分 (如果) 处理图像。

若要在 interface builder 中使用图像，请执行以下操作：

1. 将一个图像添加到 " **资源** " 文件夹，其 **生成操作** 为 `BundleResource` ： 

     ![Solution Pad 中的图像资源](image-images/ib00.png "Solution Pad 中的图像资源")
2. 双击 **主情节提要** 文件以将其打开，以便在 Interface Builder 中进行编辑： 

     [![编辑主情节提要](image-images/ib01.png "编辑主情节提要")](image-images/ib01-large.png#lightbox)
3. 将拍摄图像的 UI 元素拖动到设计图面上 (例如，) **图像工具栏项** ： 

     ![编辑工具栏项](image-images/ib02.png "编辑工具栏项")
4. 在 "**映像名称**" 下拉列表中选择已添加到 "**资源**" 文件夹的映像： 

     [![选择工具栏项的图像](image-images/ib03.png "选择工具栏项的图像")](image-images/ib03-large.png#lightbox)
5. 选定的图像将显示在设计图面中： 

     ![工具栏编辑器中显示的图像](image-images/ib04.png "工具栏编辑器中显示的图像")
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

以上步骤适用于任何允许在 **属性检查器**中设置其 image 属性的 UI 元素。 同样，如果已包含某个 **@2x** 版本的映像文件，则会自动在基于 Retina 的 mac 上使用该文件。

> [!IMPORTANT]
> 如果图像在 " **映像名称** " 下拉列表中不可用，请在 Xcode 中关闭您的 storyboard 项目，然后将其从 Visual Studio for Mac 重新打开。 如果映像仍不可用，请确保其 **生成操作** 为 `BundleResource` ，并且该映像已添加到 **Resources** 文件夹中。

## <a name="using-images-in-c-code"></a>在 c # 代码中使用图像

当使用 Xamarin 应用程序中的 c # 代码将图像加载到内存中时，该映像将存储在 `NSImage` 对象中。 如果文件已包含在 Xamarin 应用程序捆绑包中 () 包含在资源中，请使用以下代码加载映像：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上面的代码使用类的静态 `ImageNamed("...")` 方法将 `NSImage` 给定的图像从 **资源** 文件夹加载到内存中（如果找不到图像，则 `null` 将返回）。 与 Interface Builder 中分配的图像一样，如果已包含某个 **@2x** 版本的映像文件，则该文件将自动在基于 Retina 的 mac 上使用。

若要在应用程序的捆绑包外加载图像 (Mac 文件系统) ，请使用以下代码：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"></a>

## <a name="working-with-template-images"></a>使用模板映像

根据你的 macOS 应用程序的设计，有时你可能需要自定义用户界面内的图标或图像以匹配配色方案 (例如，基于用户首选项) 。

若要实现此效果，请将图像资产的 _呈现模式_ 切换到 **模板映像**：

[![设置模板映像](image-images/templateimage01.png "设置模板映像")](image-images/templateimage01-large.png#lightbox)

从 Xcode 的 Interface Builder 中，将图像资产分配给 UI 控件：

![在 Xcode 的 Interface Builder 中选择图像](image-images/templateimage02.png "在 Xcode 的 Interface Builder 中选择图像")

或者根据需要在代码中设置图像源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

将以下公共函数添加到视图控制器：

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> 特别是在 macOS Mojave 中出现暗色模式的情况下， `LockFocus` 在 reating 自定义对象时避免 API 是非常重要的 `NSImage` 。 此类图像变为静态图像，不会自动更新为考虑外观或显示密度的变化。
>
> 通过使用上述基于处理程序的机制，可以在承载时自动重新呈现动态条件， `NSImage` 例如，在中 `NSImageView` 。

最后，若要为模板映像着色，请对图像调用此函数以着色：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views"></a>

## <a name="using-images-with-table-views"></a>使用带有表视图的图像

若要在中包含图像作为单元的一部分 `NSTableView` ，您需要更改表视图的方法返回数据的方式 `NSTableViewDelegate's` `GetViewForItem` 以使用 `NSTableCellView` 而非典型的 `NSTextField` 。 例如：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

此处有几行需要关注的内容。 首先，对于需要包含图像的列，我们将创建一个新 `NSImageView` 的所需大小和位置，还 `NSTextField` 会根据是否使用映像，创建新的并放置默认位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，需要在父级中包含新的 "图像" 视图和 "文本" 字段 `NSTableCellView` ：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段它可以通过表视图单元进行收缩和增长：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![在应用程序中显示图像的示例](image-images/tables01.png "在应用程序中显示图像的示例")](image-images/tables01-large.png#lightbox)

有关使用表视图的详细信息，请参阅我们的 [表视图](~/mac/user-interface/table-view.md) 文档。

<a name="Using_Images_with_Outline_Views"></a>

## <a name="using-images-with-outline-views"></a>使用带有大纲视图的图像

若要在中包含图像作为单元的一部分 `NSOutlineView` ，需要更改大纲视图的方法返回数据的方式， `NSTableViewDelegate's` `GetView` 以使用 `NSTableCellView` 而不是典型的 `NSTextField` 。 例如：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

此处有几行需要关注的内容。 首先，对于需要包含图像的列，我们将创建一个新 `NSImageView` 的所需大小和位置，还 `NSTextField` 会根据是否使用映像，创建新的并放置默认位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，需要在父级中包含新的 "图像" 视图和 "文本" 字段 `NSTableCellView` ：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段它可以通过表视图单元进行收缩和增长：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![大纲视图中显示的图像示例](image-images/outline01.png "大纲视图中显示的图像示例")](image-images/outline01-large.png#lightbox)

有关使用大纲视图的详细信息，请参阅 [大纲视图](~/mac/user-interface/outline-view.md) 文档。

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用图像和图标。 我们看到了不同的图像类型和用途，如何使用 Xcode 的 Interface Builder 中的图像和图标，以及如何使用 c # 代码中的图像和图标。

## <a name="related-links"></a>相关链接

- [MacImages（示例）](/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [macOS X 人体学接口准则](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [有关适用于 OS X 的高分辨率](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)