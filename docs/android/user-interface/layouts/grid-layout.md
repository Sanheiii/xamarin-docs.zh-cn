---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 13271b50353d95ecd2db40e25d549788111530f7
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725001"
---
# <a name="xamarinandroid-gridlayout"></a>Xamarin GridLayout

`GridLayout` 是一种新的 `ViewGroup` 子类，它支持在二维网格中布局视图，类似于 HTML 表，如下所示：

 [显示四个单元格 ![剪裁 GridLayout](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 适用于平面视图层次结构，其中，子视图通过指定它们应位于的行和列来设置其在网格中的位置。 这样一来， *GridLayout*就可以在网格中定位视图，而无需任何中间视图提供表结构，如在 TableLayout 中使用的表行中所示。 通过维护平面层次结构， *GridLayout*能够更快地布局其子视图。 我们来看一个示例，说明此概念在代码中的实际含义。

## <a name="creating-a-grid-layout"></a>创建网格布局

下面的 XML 将多个 `TextView` 控件添加到*GridLayout*中。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

布局将调整行和列的大小，以便单元格可以容纳其内容，如下图所示：

 [显示左侧两个单元格的布局 ![关系图](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

在应用程序中运行时，这会导致以下用户界面：

 [显示四个单元格的 GridLayoutDemo 应用程序的 ![屏幕截图](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>指定方向

请注意，在上面的 XML 中，每个 `TextView` 都不指定行或列。 如果未指定这些项，`GridLayout` 会根据方向按顺序分配每个子视图。 例如，让我们将 GridLayout 的方向从水平方向更改为垂直方向，如下所示：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

现在，`GridLayout` 会将每列中的单元格从上到下，而不是从左到右定位，如下所示：

 [阐释单元格如何在垂直方向上定位的 ![关系图](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

这会在运行时生成以下用户界面：

 [![屏幕截图，其中单元格位于垂直方向](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>指定显式位置

如果要在 `GridLayout`中显式控制子视图的位置，则可以设置其 `layout_row` 和 `layout_column` 属性。 例如，以下 XML 将导致第一个屏幕截图中显示的布局（如上所示），而不考虑方向。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```

### <a name="specifying-spacing"></a>指定间距

我们提供了几个选项，这些选项将在 `GridLayout`的子视图之间提供间距。 可以使用 `layout_margin` 特性直接在每个子视图上设置边距，如下所示

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，现在提供了一个名为 `Space` 的新的通用间距视图。 若要使用它，只需将其添加为子视图即可。
例如，下面的 XML 通过将其 `rowcount` 设置为3来向 `GridLayout` 中添加了一个附加行，并添加了一个 `Space` 视图，它在 `TextViews`之间提供间距。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

此 XML 在 `GridLayout` 中创建间距，如下所示：

 [GridLayoutDemo 的 ![屏幕截图，用间距演示更大的单元格](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新 `Space` 视图的好处在于，它允许间距，而不需要我们在每个子视图上设置属性。

### <a name="spanning-columns-and-rows"></a>跨列和行

`GridLayout` 还支持跨多个列和行的单元格。 例如，假设我们向 `GridLayout` 添加另一个包含按钮的行，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

这将导致 `GridLayout` 的第一列拉伸以容纳按钮的大小，如以下所示：

[仅包含第一列的按钮 ![GridLayoutDemo 屏幕截图](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

若要保留延伸的第一列，可以通过设置按钮来跨两个列，如下所示：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

执行此操作会生成与之前所述布局类似的 `TextViews` 布局，并将按钮添加到 `GridLayout` 底部，如下所示：

 [通过按钮跨越两列 ![GridLayoutDemo 屏幕快照](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>관련 링크

- [GridLayoutDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/gridlayoutdemo)
