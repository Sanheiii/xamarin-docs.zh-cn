---
title: Xamarin. Android Designer 基础知识
description: 本主题介绍了 Xamarin. Android Designer 功能，介绍了如何启动设计器，介绍了 Design Surface，并详细说明了如何使用 "属性" 窗格编辑小组件属性。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/05/2018
ms.openlocfilehash: e4d4ffe70db8f79ddf5e8f7a8ca1bda0fdc5aa11
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458303"
---
# <a name="xamarinandroid-designer-basics"></a>Xamarin. Android Designer 基础知识

_本主题介绍了 Xamarin. Android Designer 功能，介绍了如何启动设计器，介绍了 Design Surface，并详细说明了如何使用 "属性" 窗格编辑小组件属性。_

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>启动设计器

创建布局后，设计器会自动启动，也可以通过双击现有布局文件来启动设计器。 例如，在**资源 > 布局**文件夹中双击**Activity_main**将加载设计器，如以下屏幕截图所示：

[![Visual Studio 中的设计器屏幕](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

同样，您也可以通过右键单击 "**解决方案资源管理器**中的**布局**文件夹，然后选择"**添加 > 新项 ... > Android 布局**"来添加新的布局：

[![“添加新项”对话框](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

这将创建一个新的 **main.axml** 布局文件，并将其加载到设计器中。

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

## <a name="designer-features"></a>设计器功能

设计器由几个部分组成，它们支持其各种功能，如以下屏幕截图所示：

[![设计器窗格示意图](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

在设计器中编辑布局时，可以使用以下功能来创建和设计设计：

- **Design Surface** &ndash; 通过提供布局在设备上的显示方式的可编辑表示形式，简化用户界面的可视化构造。 **Design Surface**显示在 "设计"**窗格**内 (并将**设计器工具栏**置于其上方) "。

- **源窗格** &ndash; 提供基础 XML 源的视图，该视图对应于在 **Design Surface**上显示的设计。

- **设计器工具栏** &ndash; 显示选择器列表： " **设备**"、" **版本**"、" **主题**"、"布局配置" 和 "操作栏设置"。 **设计器工具栏**还包含用于启动主题编辑器和启用材料设计网格的图标。

- **工具箱** &ndash; 提供可拖放到 **Design Surface**上的小组件和布局的列表。

- **属性窗口** &ndash; 列出所选小组件的属性以供查看和编辑。

- **文档大纲** &ndash; 显示构成布局的小组件树。 您可以单击树中的某一项，使其在 **Design Surface**上处于选中状态。 另外，单击树中的某一项会将该项的属性加载到 " **属性** " 窗口中。

## <a name="design-surface"></a>设计图面

设计器使您能够将小组件从 "工具箱" 拖放到 **Design Surface**上。 当你在设计器中与小组件进行交互 (通过添加新小组件或重新定位现有小组件) ，将显示垂直和水平线条以标记可用插入点。 在下面的示例中，将一个新 `Button` 小组件拖动到 **Design Surface**：

[![Design Surface 上的插入行示例](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

此外，可以复制小组件：可以使用 "复制" 和 "粘贴" 复制小组件，还可以在按住 <kbd>CTRL</kbd> 键的同时拖放现有小组件。

### <a name="designer-toolbar"></a>设计器工具栏

**设计器工具栏** (放置在**Design Surface**的上方) 提供配置选择器和工具菜单：

[![设计器工具栏的关系图](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

**设计器工具栏**提供了以下功能的访问权限：

- **替代布局选择器** &ndash; 允许您从不同的布局版本中进行选择。

- **设备选择器** &ndash; 定义一组限定符 (例如屏幕大小、分辨率和键盘可用性) 与特定设备关联。 你还可以添加和删除新设备。

- **Android 版本选择器** &ndash; 布局的目标 Android 版本。 设计器将根据所选 Android 版本呈现布局。

- **主题选择器** &ndash; 选择布局的 UI 主题。

- **配置选择器** &ndash; 选择设备配置，如 " *纵向* " 或 " *横向*"。

- **资源限定符选项** &ndash; 打开一个对话框，其中显示了用于选择 *语言*、 *UI 模式*、 *夜景模式*和 *圆形屏幕* 选项的下拉菜单。

- **操作栏设置** &ndash; 配置布局的操作栏设置。

- **主题编辑器** &ndash; 打开 *主题编辑器*，使您可以自定义选定主题的元素。

- **材料设计网格** &ndash; 启用或禁用 *材料设计网格*。 材料设计网格旁边的下拉菜单项将打开一个对话框，您可以在其中自定义网格。

其中的每个功能将在以下主题中更详细地说明：

- [资源限定符和可视化选项](~/android/user-interface/android-designer/resource-qualifiers.md) 提供有关 **设备选择器**、   **Android 版本选择器**、 **主题选择器**、 **配置选择器**、 **资源限定选项**和 **操作栏设置**的详细信息。

- [替代布局视图](~/android/user-interface/android-designer/alternative-layout-views.md) 说明了如何使用 **替代布局选择器**。

- [Xamarin. Android Designer 材料设计功能](~/android/user-interface/android-designer/material-design-features.md) 提供了 **主题编辑器** 和   **材料设计网格**的全面概述。

### <a name="context-menu-commands"></a>上下文菜单命令

上下文菜单在 " **Design Surface** " 和 " **文档大纲**" 中都可用。 此菜单显示可用于所选小组件及其容器的命令，从而使你可以更轻松地在容器上执行操作， (在 **Design Surface**) 上并不一定容易选择。 下面是上下文菜单的示例：

[![右键单击 Design Surface 时的示例上下文菜单](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此示例中，右键单击将 `TextView` 打开一个上下文菜单，其中提供了多个选项：

- **LinearLayout** &ndash; 打开用于编辑的 `LinearLayout` 父项的子菜单 `TextView` 。

- 应用于**Copy**右键单击的操作的**删除**、复制和**剪切** &ndash; 操作 `TextView` 。

### <a name="zoom-controls"></a>缩放控件

**Design Surface**支持通过多个控件进行缩放，如下所示：

[![Design Surface 缩放控件的图示](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

通过这些控件，可以更轻松地在设计器中查看用户界面的某些区域：

- **突出显示容器** &ndash; 突出显示   **Design Surface** 上的容器，以便在放大和缩小时可以更轻松地找到它们。

- **正常大小** &ndash; 呈现像素布局，以便您可以看到布局将如何查看选定设备的分辨率。

- **适应窗口大小** &ndash; 设置缩放级别，使整个布局在 Design Surface 上可见。

- **放大** &ndash; 通过每次单击放大，放大布局。

- **缩小** &ndash; 每次单击时增量缩小，使布局在 Design Surface 上显示得更小。

请注意，所选的缩放设置在运行时不会影响应用程序的用户界面。

## <a name="switching-between-design-and-source-panes"></a>在 "设计" 和 "源" 窗格之间切换

在 " **设计** " 和 " **源** " 窗格之间的中心条带中，有多个按钮用于修改 " **设计** " 和 " **源** " 窗格的显示方式：

[![窗格显示按钮位置](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

这些按钮执行以下操作：

- **设计** &ndash; 此最顶部的按钮 " **设计**" 选择 " **设计** " 窗格。 单击此按钮时，将启用 " **工具箱** " 和 " **属性** " 窗格，并且不显示 " **文本编辑器" 工具栏** 。 单击 " **折叠** " 按钮后 (参见下面) 中的 " **设计** " 窗格，而不会显示 " **源** " 窗格。

- **交换窗格** &ndash; 此按钮 (类似两个反向箭头) 交换 " **设计** " 和 " **源** " 窗格，以便 " **源** " 窗格位于左侧，而 " **设计** " 窗格位于右侧。 再次单击它会将这些窗格移回其原始位置。

- **源** &ndash; 此按钮 (类似于两个反向尖括号) 选择 **源** 窗格。 当单击此按钮时，" **工具箱** " 和 " **属性** " 窗格将被禁用，并且 " **文本编辑器" 工具栏** 将显示在 Visual Studio 的顶部。 单击 " **折叠** " 按钮后 (参阅下面的 ") 下，单击" **源** "按钮将显示 **源** 窗格，而不是" **设计** "窗格。

- **垂直拆分** &ndash; 此按钮 (类似于竖线) ，可并排显示 " **设计** " 和 " **源** " 窗格。 这是默认的排列方式。

- **水平拆分** &ndash;此按钮 (类似于水平条) ，用于在**源**窗格上方显示 "**设计**" 窗格。 可以单击**交换窗格**，将**源**窗格置于**设计**窗格上方。

- **折叠窗格** &ndash; 此按钮 (类似于两个右尖括号) "将 **设计** 和 **源** 的双窗格显示" 折叠 "到其中一个窗格的单一视图中。
    此按钮将成为 **展开窗格** 按钮 (类似两个左指尖括号) ，单击该按钮可将视图返回到双窗格 (**设计** 和 **源**) 显示模式。

单击 " **折叠" 窗格** 时，只显示 " **设计** " 窗格。 但是，您可以单击 " **源** " 按钮，而只查看 " **源** " 窗格。 再次单击 " **设计** " 按钮返回到 " **设计** " 窗格。

## <a name="source-pane"></a>源窗格

**源**窗格显示**Design Surface**上显示的设计的 XML 源。 因为这两个视图同时可用，所以可以通过在设计的可视化表示形式和设计的基础 XML 源之间来回切换来创建 UI 设计：

[![源窗格中的 XML 源示例](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

对 XML 源所做的更改将立即呈现在 **Design Surface**上;对 **Design Surface** 所做的更改会导致在 " **源** " 窗格中显示的 XML 源会相应地进行更新。 在 **源** 窗格中对 XML 进行更改时，可以使用自动完成功能和 IntelliSense 功能来加速基于 XML 的 UI 开发，如下面所述。

为了更方便地在处理长 XML 文件时便于导航，" **源** " 窗格支持 Visual Studio 滚动条 (如前面屏幕截图) 中所示。 有关滚动条的详细信息，请参阅 [如何通过自定义滚动条来跟踪代码](/visualstudio/ide/how-to-track-your-code-by-customizing-the-scrollbar)。

### <a name="autocompletion"></a>自动完成

当你开始键入小组件的属性名称时，可以按 <kbd>CTRL + SPACE</kbd> 来查看可能的完成的列表。 例如， `android:lay` 在下面的示例中输入后 (后面键入 <kbd>CTRL + SPACE</kbd>) ，将显示以下列表：

[![布局特性的自动完成](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

按 <kbd>enter</kbd> 以接受第一个列出的完成，或使用箭头键滚动到所需的完成，然后按 <kbd>enter</kbd>。 或者，您可以使用鼠标滚动到并单击所需的完成。

### <a name="intellisense"></a>IntelliSense

输入小组件的新属性并开始为其分配值后，IntelliSense 将在键入触发器字符后弹出，并提供要用于该属性的有效值的列表。 例如，在下面的示例中为输入第一个双引号后 `android:layout_width` ，自动完成选择器将弹出以提供此宽度的有效选项列表：

[![布局宽度的 IntelliSense 示例](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

此弹出框的底部有两个按钮 (如以上屏幕截图) 中所示。 单击左侧的 " **项目资源** " 按钮会将列表限制为应用项目中的资源，而单击右侧的 " **框架资源** " 按钮会限制列表显示框架中可用的资源。
这些按钮开启或关闭：可以再次单击它们来禁用每个按钮提供的筛选操作。

## <a name="properties-pane"></a>属性窗格

设计器支持通过 " **属性** " 窗格编辑小组件属性： 

![属性窗口的屏幕截图](designer-basics-images/vs/08-property-pad.png)

" **属性** " 窗格中列出的属性会根据 **Design Surface**上所选的小组件发生变化。

### <a name="default-values"></a>默认值

大多数小组件的属性在 " **属性** " 窗口中将为空白，因为其值从所选的 Android 主题继承。
" **属性** " 窗口将仅显示为所选小组件显式设置的值;它不会显示从主题继承的值。

### <a name="referencing-resources"></a>引用资源

某些属性可以引用 **main.axml** 文件以外的文件中定义的资源。 此类型最常见的情况是 `string` 和 `drawable` 资源。 不过，引用也可用于其他资源，如 `Boolean` 值和维度。 当某个属性支持资源引用时，将在该属性的文本项旁显示一个) 的浏览图标 (。 单击此按钮将打开资源选择器。

例如，以下屏幕截图显示了在单击 `Text` " **属性** " 窗口中小组件文本字段右侧的暗暗方块时可用的选项：

[![文本选项的示例列表](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

单击 " **资源 ...** " 时，将显示 " **选择资源** " 对话框：

[![列出了多个资源的示例资源屏幕截图](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

从此列表中，可以选择要用于该小组件的文本资源，而不是对 " **属性** " 窗格中的文本进行硬编码。 下一个示例说明的属性的资源选择器 `Src` `ImageView` ：

[![资源选择器列出 ImageView 的图标资源](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

单击属性右侧的空白正方形 `Src` 将打开 " **选择资源** " 对话框，其中包含一个 (资源列表，其中包含一个资源列表，如上面) 到绘图所示。

### <a name="boolean-property-references"></a>布尔值属性引用

通常在属性窗口中的属性旁将*布尔*属性选作检查标记。 您可以 `true` `false` 通过选中或取消选中此复选框来指定或值，也可以通过单击属性右侧的黑色实心的方块来选择属性引用。 在下面的示例中，通过单击 **文本 "全部大写** "，将文本更改为 "全部大写" `TextView` 。

![设置布尔属性的示例](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>以内联方式编辑属性

Android Designer 支持直接编辑 **Design Surface** (上的某些属性，因此您无需在属性列表) 中搜索这些属性。 可以直接编辑的属性包括文本、边距和大小。

### <a name="text"></a>文本

某些小组件的文本属性 (如 `Button` 和 `TextView`) ，可以直接在 **Design Surface**上进行编辑。 双击小组件会将其置于编辑模式下，如下所示：

![Hello 字符串的文本资源](designer-basics-images/vs/12-text-resource.png "文本资源")

您可以输入新的文本值，也可以输入新的资源字符串。 在下面的示例中， `@string/hello` 资源被替换为文本 `CLICK THIS BUTTON` ：

![按 Shift + Enter 可自动将文本链接到新资源](designer-basics-images/vs/13-shift-enter-resource.png)

此更改存储在小组件的 `text` 属性中; 它不会修改分配给资源的值 `@string/hello` 。
当你在新文本字符串中输入密钥时，可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd>自动将输入的文本链接到新资源。

### <a name="margin"></a>Margin

选择小组件时，设计器将显示允许您以交互方式更改小组件的大小或边距的图柄。 选择小组件时单击它会在边距编辑模式和大小编辑模式之间切换。

第一次单击小组件时，将显示边距控点。 如果将鼠标移动到其中一个控点，设计器将显示该句柄将更改 (的属性，如下所示 `layout_marginLeft`) 属性：

![在设计器中显示边距控点的屏幕截图](designer-basics-images/vs/15-margin-handles.png)

如果已经设置了边距，则会显示虚线，指示边距占用的空间：

![标记按钮周围空间的虚线示例](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>大小

如前文所述，你可以通过单击小组件，然后在其处于选中状态时切换到大小编辑模式。 单击三角控点，将指定维度的大小设置为 `wrap_content` ：

![环绕内容和调整控点](designer-basics-images/vs/17-wrap-content.png)

单击 " **环绕内容** " 控点会收缩该维度中的小组件，使其不会超出所需内容的包装。 在此示例中，按钮文本在水平方向上缩小，如接下来的屏幕截图所示。

当 "大小" 值设置为 " **包装内容**" 时，设计器将显示一个三角控点，使其相对于将大小更改为 `match_parent` ：

![匹配父句柄](designer-basics-images/vs/18-match-parent.png)

单击 " **匹配" 父** 控点将还原该维度的大小，使其与父小组件相同。

此外，还可以 (如上面的屏幕截图中所示拖动圆形大小调整控点) 将小组件调整为任意 `dp` 值。 当你执行此操作时，将为该维度显示 " **环绕内容** " 和 " **匹配" 父** 句柄：

![循环大小调整控点](designer-basics-images/vs/19-resize-dp.png)

并非所有容器都允许编辑 `Size` 小组件的。 例如，请注意，在下面的屏幕截图中 `LinearLayout` ，如果选择了，则不会显示调整大小控点：

![无调整大小控点](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>文档大纲

**文档大纲**显示布局的小组件层次结构。
在下面的示例中，选择了包含 `LinearLayout` 小组件：

![文档大纲示例](designer-basics-images/vs/21-document-outline.png)

在此示例中，所选小组件的轮廓 (在 `LinearLayout` **Design Surface**上还突出显示了) 。 文档大纲中选定的小组件与 **Design Surface**上的对应小组件保持同步。 这对于选择视图组非常有用，但在 **Design Surface**上的选择并不总是如此。

**文档大纲**支持复制和粘贴，或者您可以使用拖放。 支持从 **文档大纲** 到 **Design Surface** 以及从 **Design Surface** 到 **文档大纲**的拖放。 此外，右键单击 " **文档大纲** " 中的项将显示该项目的上下文菜单， (在 **Design Surface**) 上右键单击相同小组件时显示的相同上下文菜单。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="launching-the-designer"></a>启动设计器

创建布局后，设计器会自动启动，也可以通过双击现有的 main.axml 文件来启动设计器。 例如，在**资源 > 布局**文件夹中双击 " **main.axml** " 将加载设计器，如下所示：

[![Visual Studio for Mac 中的设计器屏幕](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

同样，您也可以通过右键单击 " **Solution Pad**中的"**布局**"文件夹，然后选择"**添加 > 新文件 > Android > 布局**"来添加新的布局：

[!["添加新文件" 对话框](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

这将创建一个新的 main.axml 文件，并将其加载到 Design Surface 上。

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

## <a name="designer-features"></a>设计器功能

设计器由几个部分组成，它们支持其各种功能，如以下屏幕截图所示：

[![设计器窗格示意图](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

在设计器中编辑布局时，可以使用以下功能来创建和设计设计：

- **Design Surface** &ndash; 通过提供布局在设备上的显示方式的可编辑表示形式，简化用户界面的可视化构造。

- **工具栏** &ndash; 显示选择器列表： " **设备**"、" **版本**"、" **主题**"、"布局配置" 和 "操作栏设置"。 工具栏还包含用于启动主题编辑器和启用材料设计网格的图标。

- **工具箱** &ndash; 提供可拖放到 Design Surface 上的小组件和布局的列表。

- **属性板** &ndash; 列出所选小组件的属性以供查看和编辑。

- **文档大纲** &ndash; 显示构成布局的小组件树。 您可以单击树中的某一项，使其在设计器中处于选中状态。 另外，单击树中的某一项会将该项的属性加载到属性板中。

## <a name="toolbar"></a>工具栏

工具栏 (放置在 Design Surface 的上方) 提供配置选择器和工具菜单：

[![设计器工具栏的关系图](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

工具栏提供以下功能的访问权限：

- **替代布局选择器** &ndash; 允许您从不同的布局版本中进行选择。

- **设备选择器** &ndash; 定义一组与特定设备关联的限定符，如屏幕大小、分辨率和键盘可用性。 你还可以添加和删除新设备。

- **Android 版本选择器** &ndash; 布局的目标 Android 版本。 设计器将根据所选 Android 版本呈现布局。

- **主题选择器** &ndash; 选择布局的 UI 主题。

- **配置选择器** &ndash; 选择设备配置，如 " *纵向* " 或 " *横向*"。

- **资源限定符选项** &ndash; 打开一个对话框，其中显示了用于选择 *语言*、 *UI 模式*、 *夜景模式*和 *圆形屏幕* 选项的下拉菜单。

- **操作栏设置** &ndash; 配置布局的操作栏设置。

- **主题编辑器** &ndash; 打开 *主题编辑器*，使您可以自定义选定主题的元素。

- **材料设计网格** &ndash; 启用或禁用 *材料设计网格*。 材料设计网格旁边的下拉菜单项将打开一个对话框，您可以在其中自定义网格。

其中的每个功能将在以下主题中更详细地说明：

[资源限定符和可视化选项](~/android/user-interface/android-designer/resource-qualifiers.md) 提供有关 **设备选择器**、 **Android 版本选择器**、 **主题选择器**、 **配置选择器**、 **资源限定选项**和 **操作栏设置**的详细信息。

[替代布局视图](~/android/user-interface/android-designer/alternative-layout-views.md) 说明了如何使用 **替代布局选择器**。

[材料设计功能](~/android/user-interface/android-designer/material-design-features.md) 提供了 **主题编辑器** 和 **材料设计网格**的全面概述。

## <a name="design-surface"></a>设计图面

设计器使您能够将小组件从 "工具箱" 拖放到 Design Surface 上。 当你在设计器中与小组件进行交互 (通过添加新小组件或重新定位现有小组件) ，将显示垂直和水平线条以标记可用插入点。 在下面的示例中，将一个新 `Button` 小组件拖动到 Design Surface：

[![Design Surface 上的插入行示例](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

此外，可以复制小组件：可以使用 "复制" 和 "粘贴" 复制小组件，还可以在按住 <kbd>Ctrl</kbd> 键的同时拖放现有小组件。

### <a name="context-menu-commands"></a>上下文菜单命令

上下文菜单在 "Design Surface" 和 "文档大纲" 中都可用。 此菜单显示可用于所选小组件及其容器的命令，从而使你可以更轻松地在容器上执行操作， (在 Design Surface) 上并不一定容易选择。 下面是上下文菜单的示例：

[![右键单击 Design Surface 时的示例上下文菜单](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此示例中，右键单击将 `Button` 打开一个上下文菜单，其中提供了多个选项：

- **LinearLayout** &ndash; 打开用于编辑的 `LinearLayout` 父项的子菜单 `Button` 。

- **剪切**、 **复制**和 **删除** &ndash; 应用于右键单击的操作 `Button` 。

### <a name="zoom-controls"></a>缩放控件

Design Surface 支持通过多个控件进行缩放，如下所示：

[![Design Surface 缩放控件的图示](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

通过这些控件，可以更轻松地在设计器中查看用户界面的某些区域：

- **突出显示容器** &ndash; 突出显示 Design Surface 上的容器，以便在放大和缩小时可以更轻松地找到它们。

- **正常大小** &ndash; 呈现像素布局，以便您可以看到布局将如何查看选定设备的分辨率。

- **适应窗口大小** &ndash; 设置缩放级别，使整个布局在 Design Surface 上可见。

- **放大** &ndash; 通过每次单击放大，放大布局。

- **缩小** &ndash; 每次单击时增量缩小，使布局在 Design Surface 上显示得更小。

请注意，所选的缩放设置在运行时不会影响应用程序的用户界面。

## <a name="property-pad"></a>属性板

设计器支持通过 **属性板**编辑小组件属性。 属性板中列出的属性会根据设计器图面中选择的小组件而更改。 如果 `Button` 选择了上一示例中的，则 `Button` 会显示该小组件的属性：

[![属性板的屏幕截图](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>属性板部分

属性板划分为多个部分，这些部分将相似的属性组合在一起，这样就可以 &ndash; 更轻松地找到相关属性：

- **小组件** &ndash; 小组件的主要属性，如 `id` 、   `visibility` 、等 `text` 。通常在此处放置用于管理小组件内容的属性。

- **样式** &ndash; 更改小组件可视外观的属性，例如、、等 `font` `text color` `background` 。

- **布局** &ndash; 设置小组件的位置和大小的属性。

- **滚动** &ndash; 滚动属性。

- **行为** &ndash; 设置小组件行为方式的标志。

### <a name="default-values"></a>默认值

大多数小组件的属性将在 **属性板** 中为空，因为它们的值从所选的 Android 主题继承。 **属性板**只显示为所选小组件显式设置的值;它不会显示从主题继承的值。

### <a name="referencing-resources"></a>引用资源

某些属性可以引用 **main.axml** 文件以外的文件中定义的资源。 此类型最常见的情况是 `string` 和 `drawable` 资源。 不过，引用也可用于其他资源，如 `Boolean` 值和维度。
当某个属性支持资源引用时，"浏览" 图标 (省略号， &hellip;) 将显示在该属性的文本项旁边。
单击此按钮将打开资源选择器。

例如，以下屏幕截图显示了在单击 `Button` **属性板**中小组件文本字段右侧的省略号时可用的资源：

[![列出了两个资源的示例资源屏幕截图](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

下一个示例说明的属性的资源选择器 `Src` `ImageView` ：

[![资源选择器列出 ImageView 的图标资源](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>布尔值属性引用

*布尔* 属性通常在属性板中显示为复选框。 当 `Boolean` 属性支持资源引用时，属性旁边会出现一个小的复选框。 选中的复选框表示 `true` ，空框表示 `false` 。 还可以直接输入值，如 `true` 或 `false` 。 将鼠标悬停在输入上会显示一个小文本字段图标。 如果要手动输入值，可以单击它。

[![设置布尔属性的示例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>分组属性

某些小组件具有多值属性，这些属性组合在一起 (例如 `Padding`) 。 这些属性值在一个可扩展行中的 **属性板** 中列出。 其中一些属性可以直接在分组行中编辑，如 `Padding` 下面所示的属性：

[![填充属性的示例设置](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>以内联方式编辑属性

Android Designer 支持直接编辑 Design Surface (上的某些属性，因此您无需在属性列表) 中搜索这些属性。 可以直接编辑的属性包括文本、边距和大小。

### <a name="text"></a>文本

某些小组件的文本属性 (如 `Button` 和 `TextView`) ，可以直接在 Design Surface 上进行编辑。 双击小组件会将其置于编辑模式下，如下所示：

[![Hello 字符串的文本资源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

您可以输入新的文本值，也可以输入新的资源字符串。 在下面的示例中， `@string/hello` 资源被替换为文本 `CLICK THIS BUTTON` ：

[![按 Shift + Enter 可自动将文本链接到新资源](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

此更改存储在小组件的 `text` 属性中; 它不会修改分配给资源的值 `@string/hello` 。
当你在新文本字符串中输入密钥时，可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd>自动将输入的文本链接到新资源。

### <a name="margin"></a>Margin

选择小组件时，设计器将显示允许您以交互方式更改小组件的大小或边距的图柄。 选择小组件时单击它会在边距编辑模式和大小编辑模式之间切换。

第一次单击小组件时，将显示边距控点。 如果将鼠标移动到其中一个控点，设计器将显示该句柄将更改 (的属性，如下所示 `layout_marginLeft`) 属性：

[![在设计器中显示边距控点的屏幕截图](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

如果已经设置了边距，则会显示虚线，指示边距占用的空间：

[![标记按钮周围空间的虚线示例](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>大小

如前文所述，你可以通过单击小组件，然后在其处于选中状态时切换到大小编辑模式。 单击三角控点，将指定维度的大小设置为 `wrap_content` ：

[![环绕内容和调整控点](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

单击 " **环绕内容** " 控点会收缩该维度中的小组件，这样就不会换行所包含的内容。 在此示例中，按钮文本在水平方向上缩小，如接下来的屏幕截图所示。

当 "大小" 值设置为 " **包装内容**" 时，设计器将显示一个三角控点，使其相对于将大小更改为 `match_parent` ：

[![匹配父句柄](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

单击 " **匹配" 父** 控点将还原该维度的大小，使其与父小组件相同。

此外，还可以 (如上面的屏幕截图中所示拖动圆形大小调整控点) 将小组件调整为任意 `dp` 值。 当你执行此操作时，将为该维度显示 " **环绕内容** " 和 " **匹配" 父** 句柄：

[![循环大小调整控点](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

并非所有容器都允许编辑 `Size` 小组件的。 例如，请注意，在下面的屏幕截图中 `LinearLayout` ，如果选择了，则不会显示调整大小控点：

[![无调整大小控点](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>文档大纲

**文档大纲**显示布局的小组件层次结构。
在下面的示例中，选择了包含 `LinearLayout` 小组件：

[![文档大纲](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

在此示例中，所选小组件的轮廓 (在 `LinearLayout` Design Surface 上还突出显示了) 。 文档大纲中选定的小组件与 Design Surface 上的对应小组件保持同步。 这对于选择视图组非常有用，但在 Design Surface 上的选择并不总是如此。

文档大纲支持复制和粘贴，或者您可以使用拖放。 支持从文档大纲到 Design Surface 以及从 Design Surface 到文档大纲的拖放。 此外，右键单击 "文档大纲" 中的项将显示该项目的上下文菜单， (在 Design Surface) 上右键单击相同小组件时显示的相同上下文菜单。

-----