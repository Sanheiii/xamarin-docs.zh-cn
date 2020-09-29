---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: 81ce3a31a151c8783e09bc42f2dcc2c64168a2e4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457570"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>将 CursorAdapters 与 Xamarin 配合使用

Android 提供专用于显示 SQLite 数据库查询数据的适配器类：

 **SimpleCursorAdapter** –类似于， `ArrayAdapter` 因为它可以在没有子类的情况下使用。 只需在构造函数中提供所需的参数 (如光标和布局信息) ，然后将分配给 `ListView` 。

 **CursorAdapter** –当需要更好地控制数据值到布局控件的绑定时，可以从继承的基类 (例如，隐藏/显示控件或) 更改其属性。

游标适配器提供一种高性能的方法来滚动查看 SQLite 中存储的长数据列表。 使用代码必须在对象中定义一个 SQL 查询 `Cursor` ，然后说明如何创建和填充每个行的视图。

## <a name="creating-an-sqlite-database"></a>创建 SQLite 数据库

为了演示游标适配器，需要简单的 SQLite 数据库实现。 **SimpleCursorTableAdapter/VegetableDatabase**中的代码包含用于创建表的代码和 SQL，并为其填充一些数据。
完整的 `VegetableDatabase` 类如下所示：

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

`VegetableDatabase`类将在活动的方法中实例化 `OnCreate` `HomeScreen` 。 `SQLiteOpenHelper`基类管理数据库文件的设置，并确保其方法中的 SQL `OnCreate` 只运行一次。 此类用于和的以下两个示例中 `SimpleCursorAdapter` `CursorAdapter` 。

游标查询 *必须* 具有整数列， `_id` `CursorAdapter` 才能正常工作。 如果基础表不具有名为的整数列 `_id` ，则为中的另一个唯一整数使用列别名 `RawQuery` ，后者构成了游标。 有关详细信息，请参阅 [Android 文档](xref:Android.Widget.CursorAdapter) 。

### <a name="creating-the-cursor"></a>创建游标

这些示例使用将 `RawQuery` SQL 查询转换为 `Cursor` 对象。 从游标返回的列列表定义了可在游标适配器中显示的数据列。 在 **SimpleCursorTableAdapter/HomeScreen**方法中创建数据库的代码 `OnCreate` 如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

调用的任何代码 `StartManagingCursor` 还应调用 `StopManagingCursor` 。 这些示例使用 `OnCreate` 开始和 `OnDestroy` 关闭游标。 `OnDestroy`此方法包含以下代码：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

当应用程序有一个 SQLite 数据库可用并创建了如下所示的 cursor 对象后，它可以利用的 `SimpleCursorAdapter` 或子类 `CusorAdapter` 来显示中的行 `ListView` 。

## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 类似于 `ArrayAdapter` ，但专用于 SQLite。 它不需要创建子类–只需在创建对象时设置一些简单参数，然后将其分配给 `ListView` 的 `Adapter` 属性即可。

SimpleCursorAdapter 构造函数的参数是：

 **Context** –对包含活动的引用。

 **Layout** -要使用的行视图的资源 ID。

 **ICursor** –包含要显示的数据的 SQLite 查询的游标。

 **From** string array-与游标中列的名称相对应的字符串数组。

 **到** 整数数组–与行布局中的控件相对应的布局 id 的数组。 数组中指定的列的值 `from` 将绑定到在此数组中指定索引处的 ControlID。

`from`和 `to` 数组必须具有相同的项数，因为它们构成了从数据源到视图中布局控件的映射。

**SimpleCursorTableAdapter/HomeScreen**示例代码将 `SimpleCursorAdapter` 类似于：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是在中显示 SQLite 数据的快速而简单的方法 `ListView` 。 主要的限制是，它只能将列值绑定到显示控件，它不允许您更改行布局的其他方面 (例如，显示/隐藏控件或) 更改属性。

## <a name="subclassing-cursoradapter"></a>子类 CursorAdapter

`CursorAdapter`子类具有与 `SimpleCursorAdapter` 从 SQLite 显示数据相同的性能优势，但它还可让你完全控制每个行视图的创建和布局。 `CursorAdapter`实现与子类的实现非常不同 `BaseAdapter` ，因为它不重 `GetView` 写 `GetItemId` 、 `Count` 或 `this[]` 索引器。

给定工作的 SQLite 数据库后，只需重写两种方法即可创建 `CursorAdapter` 子类：

- **BindView** –如果提供了一个视图，请更新它以显示所提供的游标中的数据。

- **NewView** –当 `ListView` 需要显示新视图时调用。 `CursorAdapter` (与常规适配器上的方法) 的回收视图的处理 `GetView` 方式不同。

前面的示例中的适配器子类具有返回行数和检索当前项的方法–不 `CursorAdapter` 需要这些方法，因为该信息可从游标本身搜集。 通过将每个视图的创建和填充拆分为这两个方法， `CursorAdapter` 强制使用视图。 这与常规适配器相反，在这种情况下，可以忽略 `convertView` 方法的参数 `BaseAdapter.GetView` 。

### <a name="implementing-the-cursoradapter"></a>实现 CursorAdapter

**CursorTableAdapter/HomeScreenCursorAdapter**中的代码包含 `CursorAdapter` 子类。 它将传递的上下文引用存储在构造函数中，以便它可以 `LayoutInflater` 在方法中访问 `NewView` 。 完整的类如下所示：

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```

### <a name="assigning-the-cursoradapter"></a>分配 CursorAdapter

在 `Activity` 将显示的中 `ListView` ，创建光标， `CursorAdapter` 然后将其分配到列表视图。

在 **CursorTableAdapter/HomeScreen**方法中执行此操作的代码 `OnCreate` 如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy`方法包含 `StopManagingCursor` 前面所述的方法调用。

## <a name="related-links"></a>相关链接

- [SimpleCursorTableAdapter (示例) ](/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (示例) ](/samples/xamarin/monodroid-samples/cursortableadapter)