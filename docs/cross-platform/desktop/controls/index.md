---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: UI 控件比较
description: 本文档提供 Xamarin、Windows 窗体和 WPF 之间的 UI 控件比较。 它还链接到其他文档，该文档将 WPF 与 Xamarin 进行比较。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: b4cffd9e95f24dea9fc5fed5a6badeec624a4e25
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453085"
---
# <a name="ui-controls-comparison"></a>UI 控件比较

下面是基于 [此表](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls)的 WINDOWS 窗体和 WPF 的 Xamarin. Forms 控件比较。

阅读有关 [WPF 和 Xamarin 之间的相似之处和差异](wpf.md) 的详细信息，以帮助更新您的移动应用程序开发的桌面知识。

|Windows 窗体|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](/dotnet/api/system.windows.forms.bindingnavigator)|-|-|
|[BindingSource](/dotnet/api/system.windows.forms.bindingsource)|[CollectionViewSource](/dotnet/api/system.windows.data.collectionviewsource)|绑定属性，例如 BindingContext|
|[Button](/dotnet/api/system.windows.forms.button)|[Button](/dotnet/api/system.windows.controls.button)|Button|
|[CheckBox](/dotnet/api/system.windows.forms.checkbox)|[CheckBox](/dotnet/api/system.windows.controls.checkbox)|开关|
|[CheckedListBox](/dotnet/api/system.windows.forms.checkedlistbox)|包含组合的[ListBox](/dotnet/api/system.windows.controls.listbox) 。|包含组合的 ListView。|
|[ColorDialog](/dotnet/api/system.windows.forms.colordialog)|-|-|
|[ComboBox](/dotnet/api/system.windows.forms.combobox)|[ComboBox](/dotnet/api/system.windows.controls.combobox) (不支持自动完成) |选取器|
|[ContextMenuStrip](/dotnet/api/system.windows.forms.contextmenustrip)|[ContextMenu](/dotnet/api/system.windows.controls.contextmenu)|-|
|[DataGridView](/dotnet/api/system.windows.forms.datagridview)|[DataGrid](/dotnet/api/system.windows.controls.datagrid)|-|
|[DateTimePicker](/dotnet/api/system.windows.forms.datetimepicker)|[DatePicker](/dotnet/api/system.windows.controls.datepicker)|DatePicker & TimePicker|
|[DomainUpDown](/dotnet/api/system.windows.forms.domainupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) 和两个 [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) 控件。|步进器|
|[ErrorProvider](/dotnet/api/system.windows.forms.errorprovider)|-|-|
|[FlowLayoutPanel](/dotnet/api/system.windows.forms.flowlayoutpanel)|[WrapPanel](/dotnet/api/system.windows.controls.wrappanel) 或 [system.windows.controls.stackpanel>](/dotnet/api/system.windows.controls.stackpanel)|StackLayout 或 FlexLayout|
|[FolderBrowserDialog](/dotnet/api/system.windows.forms.folderbrowserdialog)|-|-|
|[FontDialog](/dotnet/api/system.windows.forms.fontdialog)|-|-|
|[形式](/dotnet/api/system.windows.forms.form)|[窗口](/dotnet/api/system.windows.window)|页面|
|[GroupBox](/dotnet/api/system.windows.forms.groupbox)|[GroupBox](/dotnet/api/system.windows.controls.groupbox)|-|
|[HelpProvider](/dotnet/api/system.windows.forms.helpprovider)|没有等效的控件 (使用工具提示) 。|-|
|[HScrollBar](/dotnet/api/system.windows.forms.hscrollbar)|滚动[条](/dotnet/api/system.windows.controls.primitives.scrollbar) (滚动内置到容器控件中) |使用 ScrollView|
|[ImageList](/dotnet/api/system.windows.forms.imagelist)|-|-|
|[Label](/dotnet/api/system.windows.forms.label)|[Label](/dotnet/api/system.windows.controls.label)|标签|
|[LinkLabel](/dotnet/api/system.windows.forms.linklabel)|无等效控件 (可以使用 [Hyperlink](/dotnet/api/system.windows.documents.hyperlink) 类在流内容) 中承载超链接。|-|
|[ListBox](/dotnet/api/system.windows.forms.listbox)|[ListBox](/dotnet/api/system.windows.controls.listbox)|使用 ListView|
|[ListView](/dotnet/api/system.windows.forms.listview)|[ListView](/dotnet/api/system.windows.controls.listview)|ListView|
|[MaskedTextBox](/dotnet/api/system.windows.forms.maskedtextbox)|-|-|
|[MenuStrip](/dotnet/api/system.windows.forms.menustrip)|[菜单](/dotnet/api/system.windows.controls.menu)|请考虑 MasterDetailPage 或 TabbedPage|
|[MonthCalendar](/dotnet/api/system.windows.forms.monthcalendar)|[日历](/dotnet/api/system.windows.controls.calendar)|-|
|[NotifyIcon](/dotnet/api/system.windows.forms.notifyicon)|-|-|
|[NumericUpDown](/dotnet/api/system.windows.forms.numericupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) 和两个 [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) 控件。|步进器|
|[OpenFileDialog](/dotnet/api/system.windows.forms.openfiledialog)|[OpenFileDialog](/dotnet/api/microsoft.win32.openfiledialog)|-|
|[PageSetupDialog](/dotnet/api/system.windows.forms.pagesetupdialog)|-|-|
|[Panel](/dotnet/api/system.windows.forms.panel)|[画布](/dotnet/api/system.windows.controls.canvas)|视图或 AbsoluteLayout|
|[PictureBox](/dotnet/api/system.windows.forms.picturebox)|[图像](/dotnet/api/system.windows.controls.image)|映像|
|[PrintDialog](/dotnet/api/system.windows.forms.printdialog)|[PrintDialog](/dotnet/api/system.windows.controls.printdialog)|-|
|[PrintDocument](/dotnet/api/system.drawing.printing.printdocument)|-|-|
|[PrintPreviewControl](/dotnet/api/system.windows.forms.printpreviewcontrol)|[DocumentViewer](/dotnet/api/system.windows.controls.documentviewer)|-|
|[PrintPreviewDialog](/dotnet/api/system.windows.forms.printpreviewdialog)|-|-|
|[进度栏](/dotnet/api/system.windows.forms.progressbar)|[ProgressBar](/dotnet/api/system.windows.controls.progressbar)|进度栏|
|[PropertyGrid](/dotnet/api/system.windows.forms.propertygrid)|-|-|
|[RadioButton](/dotnet/api/system.windows.forms.radiobutton)|[RadioButton](/dotnet/api/system.windows.controls.radiobutton)|-|
|[RichTextBox](/dotnet/api/system.windows.forms.richtextbox)|[RichTextBox](/dotnet/api/system.windows.controls.richtextbox)|编辑器不支持丰富格式的 () 文本、单行文本输入|
|[SaveFileDialog](/dotnet/api/system.windows.forms.savefiledialog)|[SaveFileDialog](/dotnet/api/microsoft.win32.savefiledialog)|-|
|[ScrollableControl](/dotnet/api/system.windows.forms.scrollablecontrol)|[ScrollViewer](/dotnet/api/system.windows.controls.scrollviewer)|ScrollView|
|[SoundPlayer](/dotnet/api/system.media.soundplayer)|[MediaPlayer](/dotnet/api/system.windows.media.mediaplayer)|-|
|[SplitContainer](/dotnet/api/system.windows.forms.splitcontainer)|[GridSplitter](/dotnet/api/system.windows.controls.gridsplitter)|请考虑 MasterDetailPage|
|[StatusStrip](/dotnet/api/system.windows.forms.statusstrip)|[StatusBar](/dotnet/api/system.windows.controls.primitives.statusbar)|-|
|[TabControl](/dotnet/api/system.windows.forms.tabcontrol)|[TabControl](/dotnet/api/system.windows.controls.tabcontrol)|TabbedPage|
|[TableLayoutPanel](/dotnet/api/system.windows.forms.tablelayoutpanel)|[网格](/dotnet/api/system.windows.controls.grid)|网格|
|[TextBox](/dotnet/api/system.windows.forms.textbox)|[TextBox](/dotnet/api/system.windows.controls.textbox)|编辑器不支持) 文本格式的丰富 (|
|[计时器](/dotnet/api/system.windows.forms.timer)|[DispatcherTimer](/dotnet/api/system.windows.threading.dispatchertimer)|Device. StartTime ( # A1|
|[ToolStrip](/dotnet/api/system.windows.forms.toolstrip)|[ToolBar](/dotnet/api/system.windows.controls.toolbar)|ToolbarItems 和 ToolbarItem|
|[ToolStripContainer](/dotnet/api/system.windows.forms.toolstripcontainer)、 [ToolStripDropDown](/dotnet/api/system.windows.forms.toolstripdropdown)、 [何时](/dotnet/api/system.windows.forms.toolstripdropdownmenu)、 [ToolStripPanel](/dotnet/api/system.windows.forms.toolstrippanel)|包含组合的[工具栏](/dotnet/api/system.windows.controls.toolbar)。|带组合的 ToolbarItems 和 ToolbarItem|
|[ToolTip](/dotnet/api/system.windows.forms.tooltip)|[ToolTip](/dotnet/api/system.windows.controls.tooltip)|使用辅助功能|
|[TrackBar](/dotnet/api/system.windows.forms.trackbar)|[滑块](/dotnet/api/system.windows.controls.slider)|滑块|
|[TreeView](/dotnet/api/system.windows.forms.treeview)|[TreeView](/dotnet/api/system.windows.controls.treeview)|考虑在 NavigationPage 中使用分层 ListView|
|[用户控件](/dotnet/api/system.windows.forms.usercontrol)|[用户控件](/dotnet/api/system.windows.controls.usercontrol)|视图和自定义呈现器|
|[VScrollBar](/dotnet/api/system.windows.forms.vscrollbar)|[长度](/dotnet/api/system.windows.controls.primitives.scrollbar)|使用 ScrollView|
|[WebBrowser](/dotnet/api/system.windows.forms.webbrowser)|[WebBrowser](/dotnet/api/system.windows.controls.webbrowser)|WebView|