---
title: 在 Xamarin 中复制和粘贴
description: 本文介绍如何使用粘贴板在 Xamarin 应用程序中提供复制和粘贴。 它演示如何处理可在多个应用之间共享的标准数据类型，以及如何支持给定应用内的自定义数据。
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e85a273c75fd09672c6c75738adcdc576705af09
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433985"
---
# <a name="copy-and-paste-in-xamarinmac"></a>在 Xamarin 中复制和粘贴

_本文介绍如何使用粘贴板在 Xamarin 应用程序中提供复制和粘贴。它演示如何处理可在多个应用之间共享的标准数据类型，以及如何支持给定应用内的自定义数据。_

## <a name="overview"></a>概述

在 Xamarin 应用程序中使用 c # 和 .NET 时，您可以访问同一剪贴板 (复制和粘贴) 支持在目标-C 中工作的开发人员。

在本文中，我们将介绍两种主要方法来使用 Xamarin 应用程序中的粘贴板：

1. **标准数据类型** -由于剪贴板操作通常在两个不相关的应用之间执行，因此，这两个应用都不知道其他支持的数据类型。 为了最大限度地实现共享的可能性，剪贴板可以保存给定项的多个表示形式 (使用一组标准的通用数据) 类型，这允许使用中的应用程序选择最适合其需求的版本。
2. **自定义数据** -若要支持在 Xamarin 中复制和粘贴复杂数据，可以定义将由粘贴板处理的自定义数据类型。 例如，一个矢量绘图应用，允许用户复制和粘贴由多个数据类型和点组成的复杂形状。

[![正在运行的应用的示例](copy-paste-images/intro01.png "正在运行的应用的示例")](copy-paste-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用粘贴板以支持复制和粘贴操作的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

您可能想要了解如何向[Xamarin 内部](~/mac/internals/how-it-works.md)对象[公开 c # 类/方法](~/mac/internals/how-it-works.md)， `Register` 并说明用于将 `Export` c # 类与目标 c 对象和 UI 元素连接起来的和特性。

## <a name="getting-started-with-the-pasteboard"></a>粘贴板入门

剪贴板提供了一种标准化的机制，用于在给定的应用程序内或在应用程序之间交换数据。 Xamarin 应用程序中的粘贴板的典型用途是处理复制和粘贴操作，但也支持许多其他操作 (如拖 & Drop 和应用程序服务) 。

若要快速将您从根本上开始，我们将从一个简单、实用的介绍如何在 Xamarin pasteboards 应用程序中使用它。 稍后，我们将提供有关粘贴板工作方式和所用方法的详细说明。

在此示例中，我们将创建一个简单的基于文档的应用程序，该应用程序管理包含图像视图的窗口。 用户可以在应用中的文档之间以及与其他应用程序或同一应用内的多个窗口之间复制和粘贴图像。

### <a name="creating-the-xamarin-project"></a>创建 Xamarin 项目

首先，我们将创建一个新的基于 Xamarin 的 Xamarin 应用程序，我们将为其添加复制和粘贴支持。

执行以下操作：

1. 开始 Visual Studio for Mac 然后单击 " **新建项目 ...** " 链接。
2. 选择 " **Mac**  >  **app**  >  **Cocoa 应用**"，并单击 "**下一步**" 按钮： 

    [![创建新的 Cocoa 应用项目](copy-paste-images/sample01.png "创建新的 Cocoa 应用项目")](copy-paste-images/sample01-large.png#lightbox)
3. 输入作为 `MacCopyPaste` **项目名称** ，并将所有其他内容保留为默认值。 单击“下一步: 

    [![设置项目的名称](copy-paste-images/sample01a.png "设置项目的名称")](copy-paste-images/sample01a-large.png#lightbox)

4. 单击 " **创建** " 按钮： 

    [![确认新的项目设置](copy-paste-images/sample02.png "确认新的项目设置")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>添加 NSDocument

接下来，我们将添加自定义 `NSDocument` 类，将其用作应用程序用户界面的后台存储。 它将包含一个图像视图，并知道如何从视图中将图像复制到默认粘贴板，以及如何从默认剪贴板拍摄图像并在图像视图中显示图像。

右键单击**Solution Pad**中的 Xamarin 项目，然后选择 "**添加**  >  **新文件 ...**"：

![向项目添加 NSDocument](copy-paste-images/sample03.png "向项目添加 NSDocument")

对“名称”输入 `ImageDocument`，然后单击“新建”按钮。 编辑 **ImageDocument.cs** 类，使其类似于以下内容：

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

下面将详细介绍其中的一些代码。

下面的代码提供了一个属性，用于测试默认粘贴板上的图像数据是否存在，如果有图像可用， `true` 则返回; 否则返回 `false` ：

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

以下代码将图像从附加图像视图复制到默认粘贴板：

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

下面的代码从默认剪贴板中粘贴图像，并在附加图像视图中显示该图像 (如果粘贴板包含有效图像) ：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

在此文档准备就绪后，我们将为 Xamarin 应用创建用户界面。

### <a name="building-the-user-interface"></a>构建用户界面

双击 **Xcode 文件以** 在其中打开。 接下来，添加一个工具栏和图像，并按如下所示对其进行配置：

[![编辑工具栏](copy-paste-images/sample04.png "编辑工具栏")](copy-paste-images/sample04-large.png#lightbox)

在工具栏的左侧添加 "复制并粘贴 **图像" 工具栏项** 。 我们将使用这些作为从 "编辑" 菜单复制和粘贴的快捷方式。 接下来，将四个 " **图像工具栏" 项** 添加到工具栏的右侧。 我们将使用这些设置，用一些默认图像来填充图像。

有关使用工具栏的详细信息，请参阅 [工具栏](~/mac/user-interface/toolbar.md) 文档。

接下来，让我们向工具栏项和图像提供以下插座和操作：

[![创建输出口和操作](copy-paste-images/sample05.png "创建输出口和操作")](copy-paste-images/sample05-large.png#lightbox)

有关使用输出口和操作的详细信息，请参阅我们的[Hello，Mac](~/mac/get-started/hello-mac.md)文档中的 "[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)" 部分。

### <a name="enabling-the-user-interface"></a>启用用户界面

通过在 Xcode 中创建的用户界面和通过插座和操作公开的 UI 元素，我们需要添加代码以启用 UI。 双击**Solution Pad**中的**ImageWindow.cs**文件，使其类似于以下内容：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

下面将详细介绍以下代码。

首先，我们公开 `ImageDocument` 前面创建的类的实例：

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

通过使用 `Export` 、 `WillChangeValue` 和 `DidChangeValue` ，我们已设置了 `Document` 属性，以允许在 Xcode 中实现键/值编码和数据绑定。

此外，我们还会通过以下属性，从图像中将图像添加到 Xcode 中的用户界面：

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

当加载并显示主窗口时，我们将创建类的一个实例 `ImageDocument` ，并将 UI 的图像正确附加到下面的代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

最后，为了响应用户单击复制和粘贴工具栏项的操作，我们调用类的实例 `ImageDocument` 来执行实际工作：

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>启用文件和编辑菜单

我们需要做的最后一件事是，从 "**文件**" (菜单中启用 "**新建**" 菜单项，) 并启用 "**编辑**" 菜单中的 "**剪切**"、"**复制**" 和 "**粘贴**" 菜单项。

若要启用 " **新建** " 菜单项，请编辑 **AppDelegate.cs** 文件并添加以下代码：

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

有关详细信息，请参阅[Windows](~/mac/user-interface/window.md)文档中的[使用多个 windows](~/mac/user-interface/window.md)部分。

若要启用 **剪切**、 **复制** 和 **粘贴** 菜单项，请编辑 **AppDelegate.cs** 文件并添加以下代码：

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

对于每个菜单项，获取最新的最顶部的键窗口，并将其转换为 `ImageWindow` 类：

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

从这里，我们调用 `ImageDocument` 该窗口的类实例来处理复制和粘贴操作。 例如： 

```csharp
window.Document.CopyImage (sender);
```

仅当默认剪贴板或当前活动窗口的图像中有图像数据时，我们才需要 **剪切**、 **复制** 和 **粘贴** 菜单项。

让我们将 **EditMenuDelegate.cs** 文件添加到 Xamarin 项目，并使其类似于以下内容：

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

同样，我们获取当前的最顶层窗口，并使用其 `ImageDocument` 类实例来确定所需的图像数据是否存在。 然后，使用 `MenuWillHighlightItem` 方法根据此状态启用或禁用每个项。

编辑 **AppDelegate.cs** 文件并使该方法如下所 `DidFinishLaunching` 示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先，在 "编辑" 菜单中禁用菜单项的自动启用和禁用。 接下来，附加 `EditMenuDelegate` 前面创建的类的实例。

有关详细信息，请参阅我们的 [菜单](~/mac/user-interface/menu.md) 文档。

### <a name="testing-the-app"></a>测试应用

一切就绪后，就可以测试应用程序了。 生成并运行应用，并显示主界面：

![运行应用程序](copy-paste-images/run01.png "运行应用程序")

如果你打开 "编辑" 菜单，请注意， **剪切**、 **复制** 和 **粘贴** 操作已禁用，因为图像中没有图像或在默认的粘贴板中：

![打开 "编辑" 菜单](copy-paste-images/run02.png "打开 "编辑" 菜单")

如果将图像添加到映像，并重新打开 "编辑" 菜单，则会立即启用这些项：

![已启用 "编辑" 菜单项](copy-paste-images/run03.png "已启用 "编辑" 菜单项")

如果复制图像并从 "文件" 菜单中选择 " **新建** "，则可以将该图像粘贴到新窗口中：

![将图像粘贴到新窗口中](copy-paste-images/run04.png "将图像粘贴到新窗口中")

在以下部分中，我们将详细介绍如何在 Xamarin. Mac 应用程序中使用粘贴板。

## <a name="about-the-pasteboard"></a>关于粘贴板

在 macOS (之前称为 OS X) 粘贴板 (`NSPasteboard`) 支持对多个服务器进程（例如 Copy & Paste）提供支持，并拖动 & 删除和应用程序服务。 在以下部分中，我们将详细介绍几个关键的粘贴板概念。

### <a name="what-is-a-pasteboard"></a>什么是剪贴板？

`NSPasteboard`类提供一种标准化机制，用于在应用程序之间或在给定应用程序之间交换信息。 粘贴板的主要功能用于处理复制和粘贴操作：

1. 当用户在应用中选择某一项并使用 **剪切** 或 **复制** 菜单项时，会将所选项的一个或多个表示形式放置在粘贴板上。
2. 如果用户使用同一应用中 (" **粘贴** " 菜单项，或) 不同，则可处理的数据的版本将从粘贴板复制并添加到应用中。

不太明显的粘贴板使用包括查找、拖放、拖放和应用程序服务操作：

- 用户启动拖放操作时，会将拖动的数据复制到剪贴板。 如果拖动操作以放置到另一个应用的结尾结束，则该应用将从粘贴板复制数据。
- 对于翻译服务，请求的应用会将要转换的数据复制到剪贴板。 应用程序服务从剪贴板中检索数据，执行转换，然后将数据粘贴回粘贴板上。

最简单的形式是使用 pasteboards 在给定应用内或应用之间移动数据，因此存在于应用进程外的特殊全局内存区域。 尽管 pasteboards 的概念很容易 grasps，但还有一些必须考虑的更复杂的细节。 下面将详细介绍这些内容。

### <a name="named-pasteboards"></a>命名 pasteboards

粘贴板可以是公共或专用的，可用于应用程序中或多个应用程序内的多种目的。 macOS 提供了几个标准 pasteboards，其中每个都有一个明确定义的特定用法：

- `NSGeneralPboard` -用于 **剪切**、 **复制** 和 **粘贴** 操作的默认剪贴板。
- `NSRulerPboard`-支持对**标尺**进行**剪切**、**复制**和**粘贴**操作。
- `NSFontPboard` -支持对对象进行 **剪切**、 **复制** 和 **粘贴** 操作 `NSFont` 。
- `NSFindPboard` -支持特定于应用程序的查找面板，可以共享搜索文本。
- `NSDragPboard` -支持 **拖动 & 拖放** 操作。

大多数情况下，将使用系统定义的 pasteboards 之一。 但在某些情况下，可能需要创建自己的 pasteboards。 在这些情况下，可以使用 `FromName (string name)` 类的方法 `NSPasteboard` 来创建具有给定名称的自定义粘贴板。

或者，您可以调用 `CreateWithUniqueName` 类的方法 `NSPasteboard` 来创建一个名为的唯一粘贴板。

### <a name="pasteboard-items"></a>粘贴板项

应用程序写入剪贴板的每个数据片段都被视为一个 _粘贴板项_ ，粘贴板可以同时保存多个项。 通过这种方式，应用程序可以将多个版本的数据复制到剪贴板 (例如，纯文本和格式化文本) 并且检索应用只能读取它可以处理的数据 (如纯文本) 。

### <a name="data-representations-and-uniform-type-identifiers"></a>数据表示形式和统一类型标识符

粘贴板操作通常发生在两个 (或多个) 的应用程序之间，这些应用程序彼此不知道或每个都可以处理的数据类型。 如以上部分所述，若要最大程度地利用共享信息，粘贴板可以保存要复制和粘贴的数据的多个表示形式。

每个表示形式都通过统一的类型标识符 (UTI) 来标识，这只是一个简单的字符串，用于唯一标识要呈现的日期类型 (有关详细信息，请参阅 Apple 的 [统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) 文档) 。 

如果创建的是自定义数据类型 (例如，矢量绘图应用中的一个绘图对象) ，则可以创建自己的 UTI 以在复制和粘贴操作中唯一标识它。

当应用程序准备粘贴从粘贴板复制的数据时，它必须找到最适合其功能的表示形式 (如果存在) 。 通常，这种类型最适合用于 word 处理应用的格式文本 (例如) ，返回到最简单的窗体，可根据需要 (纯文本) 编辑器中的纯文本。

<a name="Promised_Data"></a>

### <a name="promised-data"></a>承诺的数据

一般而言，应提供尽可能多的数据表示形式，以最大限度地在应用之间共享。 但是，由于时间或内存限制，实际将每个数据类型写入粘贴板可能不切实际。

在这种情况下，您可以将第一个数据表示放置在粘贴板上，接收应用可以请求不同的表示形式，可以在粘贴操作之前即时生成。

将初始项放入剪贴板时，您将指定一个或多个可用的表示形式由符合接口的对象提供 `NSPasteboardItemDataProvider` 。 根据接收应用的要求，这些对象将按需提供额外的表示形式。

### <a name="change-count"></a>更改计数

每个粘贴板维护每次声明新所有者时递增的 _更改计数_ 。 应用可通过检查更改计数的值，来确定剪贴板上的内容自上次检查后是否已更改。

使用 `ChangeCount` 类的和 `ClearContents` 方法 `NSPasteboard` 来修改给定剪贴板的更改计数。

## <a name="copying-data-to-a-pasteboard"></a>将数据复制到粘贴板

首先要执行复制操作，只需访问剪贴板，清除任何现有内容，并根据需要写入剪贴板上所需的数据表示形式。

例如：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

通常，您只需编写到一般的粘贴板，就像我们在上面的示例中所做的那样。 发送给方法的任何对象 `WriteObjects` *都必须* 符合 `INSPasteboardWriting` 接口。 某些内置类 (例如，、、、 `NSString` 、 `NSImage` `NSURL` `NSColor` `NSAttributedString` 和 `NSPasteboardItem`) 会自动遵从此接口。

如果要将自定义数据类写入剪贴板，则它必须符合接口， `INSPasteboardWriting` 或者包装在类的实例中 `NSPasteboardItem` (参见下面) 的 " [自定义数据类型](#Custom_Data_Types) " 一节。

## <a name="reading-data-from-a-pasteboard"></a>从剪贴板读取数据

如上所述，若要最大程度地提高在应用之间共享数据的可能性，可以将复制数据的多个表示形式写入剪贴板。 如果) 存在任何 (，则应由接收应用程序选择最适合的版本，以使其功能。

### <a name="simple-paste-operation"></a>简单的粘贴操作

使用方法从剪贴板读取数据 `ReadObjectsForClasses` 。 它需要两个参数：

1. `NSObject`要从粘贴板读取的一组基于类的类型。 你应该首先按照所需的最大数据类型对此进行排序，所有剩余的类型都要降低首选项。
2. 包含其他约束的字典 (例如，限制为特定 URL 内容类型) 如果不需要进一步的约束，则为空字典。

方法返回项的数组，这些项满足我们传入的条件，因此最多包含所请求的数据类型。 还可能不存在任何请求的类型，并且将返回空数组。

例如，以下代码将检查 `NSImage` 常规粘贴板中是否存在，并在图像中将其显示为图像：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>请求多个数据类型

根据所创建的 Xamarin 的 Xamarin 应用程序的类型，它或许能够处理要粘贴的数据的多个表示形式。 在这种情况下，有两种用于从粘贴板检索数据的方案：

1. 对方法进行单一调用 `ReadObjectsForClasses` ，并提供所需的所有表示形式的数组， (按首选顺序) 。
2. 对方法进行多次调用， `ReadObjectsForClasses` 每次都需要请求不同类型的数组。

有关从粘贴板检索数据的详细信息，请参阅上面的 **简单粘贴操作** 部分。

### <a name="checking-for-existing-data-types"></a>检查现有数据类型

有时，你可能需要检查粘贴件是否包含给定的数据表示形式，而不会实际从粘贴板中读取数据 (例如，仅当存在有效数据时才启用 " **粘贴** " 菜单项) 。

调用 `CanReadObjectForClasses` 粘贴板的方法，查看其是否包含给定的类型。

例如，以下代码确定了 "常规" 粘贴板是否包含 `NSImage` 实例：

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>从剪贴板读取 url

根据给定的 Xamarin. Mac 应用程序的功能，可能需要从粘贴板中读取 Url，但前提是它们满足一组给定的条件 (例如指向特定数据类型的文件或 Url) 。 在这种情况下，可以使用或方法的第二个参数指定其他搜索条件 `CanReadObjectForClasses` `ReadObjectsForClasses` 。

<a name="Custom_Data_Types"></a>

## <a name="custom-data-types"></a>自定义数据类型

有时，你需要将自己的自定义类型保存到 Xamarin 应用程序的剪贴板中。 例如，允许用户复制和粘贴绘图对象的矢量绘图应用程序。

在这种情况下，你将需要设计数据自定义类，使其从继承 `NSObject` ，并且它符合 (`INSCoding` `INSPasteboardWriting` 和) 的几个接口 `INSPasteboardReading` 。 或者，您可以使用 `NSPasteboardItem` 来封装要复制或粘贴的数据。

下面将详细介绍这两个选项。

### <a name="using-a-custom-class"></a>使用自定义类

在本部分中，我们将在本文档开头部分创建的简单示例应用中进行扩展，并添加自定义类，以跟踪有关在窗口之间进行复制和粘贴的图像的信息。

向项目中添加一个新类，并将其称为 " **ImageInfo.cs**"。 编辑文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

在以下部分中，我们将详细介绍此类。

#### <a name="inheritance-and-interfaces"></a>继承和接口

在将自定义数据类写入或读取粘贴板之前，它必须符合 `INSPastebaordWriting` 和 `INSPasteboardReading` 接口。 此外，它还必须从继承 `NSObject` 并符合 `INSCoding` 接口：

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

还必须使用指令向目标-C 公开该类 `Register` ，并且它必须使用公开任何所需的属性或方法 `Export` 。 例如：

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我们公开此类将包含的两个数据字段-图像的名称及其类型 (jpg、png 等 ) 。 

有关详细信息，请参阅[Xamarin 内部](~/mac/internals/how-it-works.md)文档的将[c # 类/方法公开给目标-c](~/mac/internals/how-it-works.md)部分，它解释了 `Register` 用于将 `Export` c # 类与目标 c 对象和 UI 元素连接起来的和特性。

#### <a name="constructors"></a>构造函数

以下两个构造函数 (正确地公开给目标-C) ，我们的自定义数据类将需要此类构造函数，以便可以从粘贴件中读取：

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

首先，我们在的默认目标 C 方法下公开 _空_ 构造函数 `init` 。

接下来，我们将公开一个 `NSCoding` 兼容的构造函数，该构造函数在粘贴到的导出名称下时用于从粘贴板创建对象的新实例 `initWithCoder` 。

此构造函数 `NSCoder` `NSKeyedArchiver` 在写入粘贴到剪贴板) 时采用由创建的 (，提取键/值配对的数据并将其保存到数据类的属性字段。

#### <a name="writing-to-the-pasteboard"></a>写入粘贴板

通过符合 `INSPasteboardWriting` 接口，我们需要公开两个方法，还可以选择使用第三个方法，以便可以将类写入剪贴板。

首先，我们需要告诉剪贴板自定义类可以写入的数据类型表示形式：

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

每个表示形式都通过统一的类型标识符 (UTI) 来标识，这只是一个简单的字符串，用于唯一标识 (提供的数据类型。有关详细信息，请参阅 Apple 的 [统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) 文档) 。

对于我们的自定义格式，我们将创建自己的 UTI： ""， (注意，正如应用标识符) 。 我们的类还能够将标准字符串写入到粘贴板 (`public.text`) 。 

接下来，我们需要以请求的格式创建对象，该对象实际上会写入粘贴到剪贴板：

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

对于 `public.text` 类型，我们将返回一个简单的格式 `NSString` 对象。 对于自定义 `com.xamarin.image-info` 类型，我们使用 `NSKeyedArchiver` 和 `NSCoder` 接口将自定义数据类编码为键/值配对存档。 我们将需要实现以下方法来实际处理编码：

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

单个键/值对将写入编码器，并将使用上面添加的第二个构造函数对其进行解码。

（可选）可以包含以下方法，以便在将数据写入剪贴板时定义任何选项：

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

目前只有 `WritingPromised` 选项可用，并且应在给定类型仅承诺而不是实际写入剪贴板时使用。 有关详细信息，请参阅上面的 " [承诺数据](#Promised_Data) " 一节。

使用这些方法时，可以使用以下代码将自定义类写入剪贴板：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>从剪贴板读取

由于与接口一致 `INSPasteboardReading` ，因此需要公开三个方法，以便可以从剪贴板读取自定义数据类。

首先，我们需要告诉粘贴程序自定义类可以从剪贴板读取哪些数据类型表示形式：

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同样，这些都定义为简单的 Uti，它们的类型与上面 **写入粘贴板** 部分时所定义的类型相同。

接下来，我们需要使用以下方法告诉剪贴板 _如何_ 读取每个 UTI 类型：

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

对于 `com.xamarin.image-info` 类型，我们会告诉粘贴板使用向 `NSKeyedArchiver` 类中添加的构造函数将类写入剪贴板时，对使用创建的键/值对进行解码 `initWithCoder:` 。

最后，我们需要添加以下方法，以便从粘贴板读取其他 UTI 数据表示形式：

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

所有这些方法都准备就绪后，可以使用以下代码从粘贴板读取自定义数据类：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>使用 NSPasteboardItem

有时，你可能需要将自定义项写入到不保证自定义类创建的外接程序，或者你想要根据需要提供公共格式的数据。 对于这些情况，可以使用 `NSPasteboardItem` 。

`NSPasteboardItem`提供对写入到粘贴到剪贴板的数据的精细控制，并设计为进行临时访问，在将其写入剪贴板后，应将其释放。

#### <a name="writing-data"></a>写入数据

若要将自定义数据写入， `NSPasteboardItem` 需要提供自定义 `NSPasteboardItemDataProvider` 。 向项目中添加一个新类，并将其称为 " **ImageInfoDataProvider.cs**"。 编辑文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

与自定义数据类相比，我们需要使用 `Register` 和 `Export` 指令将其公开给目标-C。 类必须从继承 `NSPasteboardItemDataProvider` ，并且必须实现 `FinishedWithDataProvider` 和 `ProvideDataForType` 方法。

使用 `ProvideDataForType` 方法来提供将包装在中的数据，如下所示 `NSPasteboardItem` ：

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

在这种情况下，我们将 (名称和 ImageType) 的映像存储两部分信息，并将这些信息写入简单字符串 (`public.text`) 。

键入 "将数据写入剪贴板"，使用以下代码：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>读取数据

若要从粘贴板中读取数据，请使用以下代码：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin 应用程序中使用剪贴板，以支持复制和粘贴操作。 首先，它引入了一个简单的示例，帮助你熟悉标准 pasteboards 操作。 接下来详细了解剪贴板以及如何从其读取和写入数据。 最后，它介绍了使用自定义数据类型来支持在应用程序中复制和粘贴复杂数据类型。

## <a name="related-links"></a>相关链接

- [MacCopyPaste (示例) ](/samples/xamarin/mac-samples/maccopypaste)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [粘贴板编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)