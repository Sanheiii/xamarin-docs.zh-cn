---
title: 自定义 ListView 的外观
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 41b4bb25aa170abeb58a92058f4bfb8b78af35ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457232"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>使用 Xamarin 自定义 ListView 的外观

ListView 的外观由正在显示的行的布局决定。 若要更改的外观 `ListView` ，请使用不同的行布局。

## <a name="built-in-row-views"></a>内置行视图

有十二个可使用 **Android**引用的内置视图：

- **TestListItem** &ndash; 具有最小格式的单行文本。

- **SimpleListItem1** &ndash; 单行文本。

- **SimpleListItem2** &ndash; 两行文本。

- **SimpleSelectableListItem** &ndash; 支持单个或多个项选择 (添加到 API 级别 11) 中的单行文本。

- **SimpleListItemActivated1** &ndash; 类似于 SimpleListItem1，但背景色指示何时选择行 (在 API 级别 11) 中添加。

- **SimpleListItemActivated2** &ndash; 类似于 SimpleListItem2，但背景色指示何时选择行 (在 API 级别 11) 中添加。

- **SimpleListItemChecked** &ndash; 显示复选标记以指示选择。

- **SimpleListItemMultipleChoice** &ndash; 显示复选框以指示多选选择。

- **SimpleListItemSingleChoice** &ndash; 显示单选按钮以指示互斥的选择。

- **TwoLineListItem** &ndash; 两行文本。

- **ActivityListItem** &ndash; 带图像的单行文本。

- **SimpleExpandableListItem** &ndash; 按类别对行进行分组，并且每个组都可以展开或折叠。

每个内置行视图都具有与之关联的内置样式。 这些屏幕截图显示每个视图的显示方式：

[![TestListItem、SimpleSelectableListItem、SimpleListitem1 和 SimpleListItem2 的屏幕截图](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![SimpleListItemActivated1、SimpleListItemActivated2、SimpleListItemChecked 和 SimpleListItemMultipleChecked 的屏幕截图](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![SimpleListItemSingleChoice、TwoLineListItem、ActivityListItem 和 SimpleExpandableListItem 的屏幕截图](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

**BuiltInViews**解决方案中的**BuiltInViews/HomeScreenAdapter**示例文件 () 包含用于生成不可展开列表项屏幕的代码。 视图在方法中设置， `GetView` 如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

然后，可以通过引用标准控件标识符来设置视图的属性 `Text1` ， `Text2` 并 `Icon` 在 (不 `Android.Resource.Id` 设置视图不包含的属性，否则将引发异常) ：

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInViews**解决方案中的**BuiltInExpandableViews/ExpandableScreenAdapter**示例文件 () 包含用于生成 SimpleExpandableListItem 屏幕的代码。 在方法中设置组视图， `GetGroupView` 如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

在方法中设置子视图， `GetChildView` 如下所示：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

然后，可以通过引用标准和控制标识符来设置组视图和子视图的属性，如上 `Text1` `Text2` 所示。 上面显示的 SimpleExpandableListItem 屏幕 (截图) 提供一个单行组视图 (SimpleExpandableListItem1) 和两行子视图 (SimpleExpandableListItem2) 的示例。 另外，可以将组视图配置为 (SimpleExpandableListItem2 的两行) 并且可以将子视图配置为一行 (SimpleExpandableListItem1) ，或者两个组视图和子视图的行数可以相同。 

## <a name="accessories"></a>Accessories

行可以在视图右侧添加附件，以指示选择状态：

- **SimpleListItemChecked** &ndash; 创建一个选择列表，其中包含一个检查作为指示器。

- **SimpleListItemSingleChoice** &ndash; 创建单选按钮类型列表，其中只能有一个选择。

- **SimpleListItemMultipleChoice** &ndash; 创建 checkbox 类型列表，其中有多个选项可供选择。

以下屏幕以各自的顺序说明上述附件：

[![带有附件的 SimpleListItemChecked、SimpleListItemSingleChoice 和 SimpleListItemMultipleChoice 屏幕截图](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

若要显示其中一个附件，请将所需的布局资源 ID 传递到适配器，然后手动设置所需行的选择状态。 下面这行代码演示了如何 `Adapter` 使用下列布局之一创建和分配：

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView`本身支持不同的选择模式，而不考虑显示的附件。 若要避免混淆，请将 `Single` 选择模式与 `SingleChoice` 附件 `Checked` 以及 `Multiple` 样式的或模式 `MultipleChoice` 结合使用。 选择模式由 `ChoiceMode` 的属性控制 `ListView` 。

### <a name="handling-api-level"></a>处理 API 级别

早期版本的 Xamarin 已将枚举实现为整数属性。 最新版本引入了正确的 .NET 枚举类型，这使得发现可能的选项变得更加容易。

根据目标的 API 级别， `ChoiceMode` 是整数或枚举。 如果要以 Gingerbread API 为目标，示例文件 **AccessoryViews/HomeScreen 中** 有一个块注释掉：

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```

### <a name="selecting-items-programmatically"></a>以编程方式选择项

手动设置 "选定的项" 是通过方法完成的 `SetItemChecked` (可以多次为多个选择) 调用它：

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

此代码还需要检测不同于多个选择的单项选择。 若要确定在 "模式" 中选择了哪个行， `Single` 请使用 `CheckedItemPosition` integer 属性：

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

若要确定在模式中选择了哪些行 `Multiple` ，需要遍历 `CheckedItemPositions` `SparseBooleanArray` 。 稀疏数组类似于只包含值已更改的项的字典，因此你必须遍历整个数组，查找 `true` 值以了解列表中所选的内容，如以下代码段所示：

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

## <a name="creating-custom-row-layouts"></a>创建自定义行布局

这四个内置行视图非常简单。 若要显示更复杂的布局 (如电子邮件列表、推文或联系人信息) 需要自定义视图。 自定义视图通常声明为 **资源/布局** 目录中的 main.axml 文件，然后通过自定义适配器使用其资源 Id 进行加载。 视图可以包含任意数量的显示类 (例如，TextViews、ImageViews 和其他控件) 具有自定义颜色、字体和布局。

此示例与前面的示例在多种方面有所不同：

- 继承自 `Activity` ，而不是 `ListActivity` 。 你可以为任何自定义行 `ListView` ，但其他控件也可以包含在 `Activity` 布局 (如标题、按钮或其他用户界面元素) 。 此示例在上添加一个标题 `ListView` ，以说明。

- 需要屏幕的 MAIN.AXML 布局文件;在前面的示例中，不 `ListActivity` 需要布局文件。 此 MAIN.AXML 包含一个 `ListView` 控件声明。

- 需要使用 MAIN.AXML 布局文件来呈现每一行。 此 MAIN.AXML 文件包含具有自定义字体和颜色设置的文本和图像控件。

- 使用可选的自定义选择器 XML 文件来设置选定行的外观。

- `Adapter`实现从重写中返回自定义布局 `GetView` 。

- `ItemClick` 在将事件处理程序附加到 `ListView.ItemClick` （而不是) 中的重写） (，必须以不同的方式声明  `OnListItemClick` `ListActivity` 。

下面将详细介绍这些更改，从创建活动的视图和自定义行视图开始，然后覆盖对适配器和活动的修改以呈现这些更改。

### <a name="adding-a-listview-to-an-activity-layout"></a>向活动布局添加 ListView

由于不能 `HomeScreen` 再从 `ListActivity` 其继承默认视图，因此必须为 HomeScreen 的视图创建布局 main.axml 文件。 在此示例中，视图将具有一个标题 (使用 `TextView`) ，并 `ListView` 显示数据。 此布局在 **资源/layout/HomeScreen** 文件中定义，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

使用 `Activity` 自定义布局 (而不是) 的好处 `ListActivity` 在于，能够将其他控件添加到屏幕，如 `TextView` 本示例中的标题。

### <a name="creating-a-custom-row-layout"></a>创建自定义行布局

需要另一个 MAIN.AXML 布局文件，以包含将显示在列表视图中的每一行的自定义布局。 在此示例中，该行将具有绿色背景、棕色文本和右对齐图像。 **Resources/layout/CustomView. main.axml**中描述了用于声明此布局的 Android XML 标记：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

尽管自定义行布局可以包含许多不同的控件，但对于复杂的设计和使用映像，滚动性能可能会受到影响 (尤其是在必须通过网络) 进行加载时。 有关解决滚动性能问题的详细信息，请参阅 Google 文章。

### <a name="referencing-a-custom-row-view"></a>引用自定义行视图

自定义适配器示例的实现在中 `HomeScreenAdapter.cs` 。 密钥方法是 `GetView` 使用资源 ID 加载自定义 main.axml 的位置 `Resource.Layout.CustomView` ，然后在视图中的每个控件上设置属性，然后再将其返回。 将显示完整的适配器类：

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```

### <a name="referencing-the-custom-listview-in-the-activity"></a>引用活动中的自定义 ListView

由于 `HomeScreen` 该类现在继承自 `Activity` ，因此 `ListView` 在类中声明字段以保存对 main.axml 中声明的控件的引用：

```csharp
ListView listView;
```

然后，类必须使用方法加载活动的自定义布局 MAIN.AXML `SetContentView` 。 然后，它可以 `ListView` 在布局中找到该控件，然后创建并分配适配器并分配单击处理程序。 OnCreate 方法的代码如下所示：

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

最后 `ItemClick` 必须定义该处理程序; 在这种情况下，它只显示一 `Toast` 条消息：

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

生成的屏幕如下所示：

[![生成的 CustomRowView 的屏幕截图](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)

### <a name="customizing-the-row-selector-color"></a>自定义行选择器颜色

当涉及某个行时，应该突出显示该行以供用户反馈。 当自定义视图将背景色指定为 **CustomView** 时，它还会替代选择的突出显示。 **CustomView**中的这行代码会将背景设置为浅绿色，但这也意味着在接触行时没有视觉指示器：

```xml
android:background="#FFDAFF7F"
```

若要重新启用突出显示行为，并同时自定义使用的颜色，请改为将背景属性设置为自定义选择器。 选择器将同时声明默认背景色和突出显示颜色。 文件 **资源/绘制/CustomSelector.xml** 包含以下声明：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

若要引用自定义选择器，请将 **CustomView** 中的背景属性更改为：

```xml
android:background="@drawable/CustomSelector"
```

选定的行和相应的 `Toast` 消息现在如下所示：

[![选定行显示为橙色，并显示 Toast 消息显示所选行的名称](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)

### <a name="preventing-flickering-on-custom-layouts"></a>防止自定义布局闪烁

Android 尝试 `ListView` 通过缓存布局信息来提高滚动性能。 如果有数据的长滚动列表，还应将 `android:cacheColorHint` `ListView` 活动的 main.axml 定义中的声明上的属性设置 (为与自定义行布局的背景) 相同的颜色值。 如果无法包括此提示，则可能会导致 "闪烁"，因为用户滚动使用自定义行背景色的列表。

## <a name="related-links"></a>相关链接

- [BuiltInViews (示例) ](/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (示例) ](/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (示例) ](/samples/xamarin/monodroid-samples/customrowview)