---
title: 使用数据填充 Xamarin ListView ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: 6c22905e576b37d892d83a7b22799e4afdc2262f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457154"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>使用数据填充 Xamarin ListView ListView

若要向中添加行， `ListView` 需要将行添加到布局，并 `IListAdapter` 使用 `ListView` 调用来填充自己的方法来实现。 Android 包含 `ListActivity` `ArrayAdapter` 无需定义任何自定义布局 XML 或代码即可使用的内置和类。 `ListActivity`类会自动创建 `ListView` ，并公开一个 `ListAdapter` 属性，以便通过适配器提供要显示的行视图。

内置适配器采用视图资源 ID 作为参数，用于每行。 你可以使用中的内置资源（如中的资源），而 `Android.Resource.Layout` 无需编写你自己的资源。

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter &lt; 字符串&gt;

示例 **BasicTable/HomeScreen** 演示如何使用这些类 `ListView` 只在几行代码中显示：

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
}
```

### <a name="handling-row-clicks"></a>处理行单击

通常情况下， `ListView` 还会允许用户触摸一行来执行某些操作 (如播放歌曲、调用联系人或显示另一个屏幕) 。 若要对用户进行响应，需要在中实现另一个方法， `ListActivity` &ndash; `OnListItemClick` &ndash; 如下所示：

[![SimpleListItem 的屏幕截图](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

现在用户可以触摸一行，此时 `Toast` 将显示一个警报：

[![触摸行时显示的 Toast 的屏幕截图](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>实现 ListAdapter

`ArrayAdapter<string>` 非常简单，因为它很简单，但非常有限。 不过，通常会有一系列业务实体，而不只是要绑定的字符串。
例如，如果您的数据包含 Employee 类的集合，则您可能希望该列表只显示每个雇员的姓名。 若要自定义的行为 `ListView` 来控制要显示的数据，您必须实现一个子类来 `BaseAdapter` 重写以下四项：

- **计数** &ndash; 通知控件数据中的行数。

- **GetView** &ndash; 为每行返回一个视图，并用数据填充。
    此方法有一个参数，该参数用于 `ListView` 传入未使用的现有行以供重用。

- **GetItemId** &ndash; 返回行标识符 (通常为行号，尽管它可以是你想要) 的任意长值。

- **此 [int]** 索引器 &ndash; 返回与特定行号关联的数据。

**BasicTableAdapter/HomeScreenAdapter**中的示例代码演示如何为子类 `BaseAdapter` ：

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```

### <a name="using-a-custom-adapter"></a>使用自定义适配器

使用自定义适配器与内置类似 `ArrayAdapter` ， `context` 并传入和 `string[]` 值以显示：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因为此示例使用相同的行布局 (`SimpleListItem1`) 则生成的应用程序将与前面的示例类似。

### <a name="row-view-re-use"></a>行视图重复使用

在此示例中，只有六个项。 由于屏幕可以容纳8个，因此不需要任何行重复使用。 但是，在显示几百行或数千行时， `View` 如果一次只在屏幕上显示八个或数千个对象，则可能会浪费内存。 若要避免这种情况，当行从屏幕上消失时，其视图将放置在队列中供重复使用。 当用户滚动时，将 `ListView` 调用 `GetView` 以请求显示新视图（ &ndash; 如果可用）通过参数传递未使用的视图 `convertView` 。 如果此值为 null，你的代码应创建新的视图实例，否则你可以重新设置该对象的属性并重新使用它。

`GetView`方法应遵循此模式以重复使用行视图：

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

自定义适配器实现应 *始终* 在 `convertView` 创建新视图之前重复使用对象，以确保它们在显示长列表时不会用尽内存。

某些适配器实现 (如) 没有 `CursorAdapter` `GetView` 方法，而是需要使用两种不同的方法， `NewView` `BindView` 从而强制执行行重用，方法是将的责任 `GetView` 划分为两个方法。 文档的后面有一个 `CursorAdapter` 示例。

## <a name="enabling-fast-scrolling"></a>启用快速滚动

快速滚动可提供一个额外的 "句柄" 来帮助用户滚动长列表，以直接访问列表的一部分。 此屏幕截图显示快速滚动手柄：

[![使用滚动图柄快速滚动的屏幕截图](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

使快速滚动图柄显示起来非常简单，只需要将 `FastScrollEnabled` 属性设置为 `true` ：

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>添加节索引

节索引在用户通过长列表快速滚动时为用户提供其他反馈 &ndash; ，其中显示了他们滚动到的 "部分"。 若要使节索引显示，适配器子类必须实现 `ISectionIndexer` 接口，以便根据要显示的行来提供索引文本：

[![以 H 开头的第一节中显示的 H 的屏幕截图](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要实现 `ISectionIndexer` ，需要将三个方法添加到适配器：

- **GetSections** &ndash; 提供可以显示的节索引标题的完整列表。 此方法需要 Java 对象的数组，因此代码需要 `Java.Lang.Object[]` 从 .net 集合创建。 在本示例中，它将列表中的初始字符列表返回为 `Java.Lang.String` 。

- **GetPositionForSection** &ndash; 返回给定节索引的第一个行位置。

- **GetSectionForPosition** &ndash; 返回要为给定行显示的节索引。

示例 `SectionIndex/HomeScreenAdapter.cs` 文件实现了这些方法，并在构造函数中实现了一些其他代码。 构造函数通过遍历每一行并提取标题的第一个字符来生成节索引 (项必须已排序才能) 。

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

创建数据结构 `ISectionIndexer` 后，方法非常简单：

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

部分索引标题不需要将1:1 映射到实际部分。 这就是此 `GetPositionForSection` 方法的原因。
`GetPositionForSection` 使您有机会将索引列表中的任何索引映射到列表视图中的任何部分。 例如，在索引中可能有一个 "z"，但对于每个字母，可能没有表节，因此，它可能会映射到25或24，或者应映射到的任何节索引 "z"。

## <a name="related-links"></a>相关链接

- [BasicTableAndroid (示例) ](/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (示例) ](/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (示例) ](/samples/xamarin/monodroid-samples/fastscroll)