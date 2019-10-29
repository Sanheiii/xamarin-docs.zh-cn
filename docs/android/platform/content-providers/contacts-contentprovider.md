---
title: 使用联系人 ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020547"
---
# <a name="using-the-contacts-contentprovider"></a>使用联系人 ContentProvider

使用 `ContentProvider` 公开的访问数据的代码不需要对 `ContentProvider` 类的引用。 相反，Uri 用于在 `ContentProvider`公开的数据上创建游标。 Android 使用 Uri 在系统中搜索使用该标识符公开 `ContentProvider` 的应用程序。 Uri 是一个字符串，通常采用反向 DNS 格式，如 `com.android.contacts/data`。

Android*联系人*提供程序在 `android.provider.ContactsContract` 类中公开其元数据，而不是让开发人员记住此字符串。 此类用于确定 `ContentProvider` 的 Uri，以及可查询的表和列的名称。

某些数据类型还需要特殊的访问权限。 内置联系人列表需要**androidmanifest.xml**文件中的 `android.permission.READ_CONTACTS` 权限。

有三种方法可从 Uri 创建游标：

1. **ManagedQuery （）** &ndash; Android 2.3 （API 级别10）和更早版本中的首选方法，`ManagedQuery` 返回游标，还自动管理刷新数据和关闭游标的方法。 此方法已在 Android 3.0 （API 级别11）中弃用。

1. **ContentResolver （）** &ndash; 返回非托管游标，这意味着必须在代码中显式刷新和关闭它。

1. **CursorLoader （）。LoadInBackground （）** &ndash; 是在 Android 3.0 （API 级别11）中引入的，`CursorLoader` 现在是使用 `ContentProvider` 的首选方式。 `CursorLoader` 在后台线程上查询 `ContentResolver`，因此不会阻止 UI。
   可以使用 v4 兼容性库在较早版本的 Android 中访问此类。

其中每个方法都具有相同的基本输入集：

- **Uri** &ndash; `ContentProvider` 的完全限定名称。
- **投影**&ndash; 指定要为游标选择的列。
- **选择**&ndash; 类似于 SQL `WHERE` 子句。
- 要在选定内容中替换 &ndash; 参数的**SelectionArgs** 。
- 排序**依据 &ndash; 要**排序的列。

## <a name="creating-inputs-for-a-query"></a>为查询创建输入

`ContactsProvider` 的示例代码对 Android 的内置联系人提供商执行了非常简单的查询。 你不需要知道实际的 Uri 或列名称-查询联系人 `ContentProvider` 所需的所有信息都可用作 `ContactsContract` 类公开的常数。

无论使用哪种方法来检索游标，都将使用这些相同的对象作为参数，如*ContactsProvider/ContactsAdapter*文件中所示：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

在此示例中，通过将 `selection`、`selectionArgs` 和 `sortOrder` 设置为 `null`将忽略该。

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>通过内容提供程序 Uri 创建游标

创建参数对象后，可以使用以下三种方法之一：

### <a name="using-a-managed-query"></a>使用托管查询

面向 Android 2.3 （API 级别10）或更早版本的应用程序应使用此方法：

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

此游标将由 Android 管理，因此你无需关闭它。

### <a name="using-contentresolver"></a>使用 ContentResolver

直接访问 `ContentResolver` 以获取针对 `ContentProvider` 的游标，如下所示：

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

此游标是不受管理的，因此必须在不再需要时将其关闭。
确保代码关闭打开的游标，否则将发生错误。

```csharp
cursor.Close();
```

或者，可以调用 `StartManagingCursor()`，并 `StopManagingCursor()` "管理" 光标。 当活动停止并重新启动时，将自动停用和重新查询托管游标。

### <a name="using-cursorloader"></a>使用 CursorLoader

为 Android 3.0 （API 级别11）或更高版本生成的应用程序应使用此方法：

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader` 确保所有游标操作都是在后台线程上完成的，并且可以在活动重新启动时（例如配置更改），在活动实例间智能地重复使用现有游标，而不是再次重新加载数据。

早期 Android 版本还可以通过使用[v4 支持库](https://developer.android.com/tools/support-library/index.html)来使用 `CursorLoader` 类。

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>使用自定义适配器显示游标数据

若要显示联系人图像，我们将使用自定义适配器，以便我们可以手动解析对图像文件路径 `PhotoId` 引用。

若要使用自定义适配器显示数据，该示例使用 `CursorLoader` 将所有联系人数据从**ContactsProvider/ContactsAdapter**的**FillContacts**方法中的本地集合中检索到本地集合中：

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

然后，使用 `contactList` 集合实现 BaseAdapter 的方法。 该适配器的实现方式与任何其他集合的实现方式相同 &ndash; 在此处没有特殊处理，因为数据源于 `ContentProvider`：

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

使用设备上的图像文件的 Uri 显示映像（如果存在）。 应用程序如下所示：

[显示 ListView 中的联系人的应用程序![屏幕截图;一个图像显示在一个条目的左侧](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

使用类似的代码模式，应用程序可以访问各种系统数据，包括用户的照片、视频和音乐。
某些数据类型需要在项目的**androidmanifest.xml**中请求特殊权限。

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>使用 SimpleCursorAdapter 显示游标数据

还可以使用 `SimpleCursorAdapter` 显示光标（尽管只显示名称，而不显示照片）。 此代码演示如何对 `SimpleCursorAdapter` 使用 `ContentProvider` （此代码不显示在示例中）：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

有关实施 `SimpleCursorAdapter`的详细信息，请参阅[listview 和适配器](~/android/user-interface/layouts/list-view/index.md)。

## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
