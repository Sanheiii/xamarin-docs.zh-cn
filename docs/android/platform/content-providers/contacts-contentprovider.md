---
title: 使用联系人 ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: 8c3608837132e221bec12b2c00fb2dd0b1730e76
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454073"
---
# <a name="using-the-contacts-contentprovider"></a>使用联系人 ContentProvider

使用 `ContentProvider` 公开的访问数据的代码根本不需要引用 `ContentProvider` 类。 而是改用 URI 在 `ContentProvider` 公开的数据上创建游标。 Android 使用 URI 在系统中搜索公开包含相应标识符的 `ContentProvider` 的应用程序。 URI 是字符串，通常采用反向 DNS 格式（如 `com.android.contacts/data`）。

Android 联系人  提供程序在 `android.provider.ContactsContract` 类中公开了它的元数据，而不是让开发人员记住此字符串。 此类用于确定 `ContentProvider` 的 URI，以及可查询的表和列的名称。

一些数据类型还需要特殊权限才能访问。 内置联系人列表需要 AndroidManifest.xml  文件中的 `android.permission.READ_CONTACTS` 权限。

通过 URI 创建游标的方法有三种：

1. **ManagedQuery()** &ndash; Android 2.3（API 级别 10）及更低版本中的首选方法，`ManagedQuery` 返回游标，并还自动管理数据刷新和游标关闭。 此方法在 Android 3.0（API 级别 11）中是弃用的。

1. **ContentResolver.Query()** &ndash; 返回非托管游标；也就是说，必须在代码中显式刷新和关闭它。

1. **CursorLoader().LoadInBackground()** &ndash; 在 Android 3.0（API 级别 11）中引入，`CursorLoader` 现在是使用 `ContentProvider` 的首选方式。 `CursorLoader` 在后台线程查询 `ContentResolver`，这样 UI 不会受阻。
   使用 v4 兼容性库，可以在旧版 Android 中访问此类。

这些方法都有相同的基本输入集：

- **URI** &ndash; `ContentProvider` 的完全限定名称。
- **Projection** &ndash; 指定为游标选择哪些列。
- **Selection** &ndash; 类似于 SQL `WHERE` 子句。
- **SelectionArgs** &ndash; 要在 Selection 中替换的参数。
- **SortOrder** &ndash; 列的排序依据。

## <a name="creating-inputs-for-a-query"></a>创建查询输入

`ContactsProvider` 示例代码对 Android 内置联系人提供程序执行非常简单的查询。 无需知道实际 URI 或列名，查询联系人 `ContentProvider` 所需的全部信息都可以作为 `ContactsContract` 类公开的常量使用。

无论使用哪种方法来检索游标，这些相同对象都可用作参数，如 ContactsProvider/ContactsAdapter.cs  文件所示：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

对于此示例，通过将 `selection`、`selectionArgs` 和 `sortOrder` 设置为 `null`，可忽略它们。

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>通过内容提供程序 URI 创建游标

在参数对象创建完毕后，可以通过下面三种方式之一来使用它们：

### <a name="using-a-managed-query"></a>使用托管查询

定目标到 Android 2.3（API 级别 10）或更低版本的应用程序应使用此方法：

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

此游标由 Android 托管，因此你无需关闭它。

### <a name="using-contentresolver"></a>使用 ContentResolver

可以像下面这样通过直接访问 `ContentResolver` 来获取针对 `ContentProvider` 的游标：

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

此游标为非托管，因此必须在不再需要时关闭它。
请确保代码关闭打开的游标，否则会出错。

```csharp
cursor.Close();
```

另外，可以调用 `StartManagingCursor()` 和 `StopManagingCursor()` 来“托管”游标。 当活动停止并重启时，托管游标会自动停用并重新查询。

### <a name="using-cursorloader"></a>使用 CursorLoader

定目标到 Android 3.0（API 级别 11）或更高版本的应用程序应使用此方法：

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader` 可确保所有游标操作都是在后台线程完成，并能在活动重启（例如由于配置更改）时跨活动实例智能地重用现有游标，而不是再次重载数据。

旧版 Android 还可以通过使用 [v4 支持库](https://developer.android.com/tools/support-library/index.html)来使用 `CursorLoader` 类。

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>使用自定义适配器显示游标数据

为了显示联系人图像，我们将使用自定义适配器，这样就能手动将 `PhotoId` 引用解析为图像文件路径。

为了使用自定义适配器显示数据，此示例在 ContactsProvider/ContactsAdapter.cs  的 FillContacts  方法中使用 `CursorLoader` 将所有联系人数据检索到本地集合中：

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

然后，使用 `contactList` 集合来实现 BaseAdapter 的方法。 适配器的实现与在其他任何集合中的实现一样 &ndash; 此处没有进行特殊处理，因为数据源于 `ContentProvider`：

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

图像（若有）是使用设备上的图像文件的 URI 来显示。 应用程序如下所示：

[![在 ListView 中显示联系人、在一个条目左侧显示图像的应用的屏幕截图](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

使用类似的代码模式，应用程序可以访问各种系统数据，包括用户的照片、视频和音乐。
一些数据类型需要在项目的 AndroidManifest.xml  中请求特殊权限才能访问。

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>使用 SimpleCursorAdapter 显示游标数据

还可以使用 `SimpleCursorAdapter` 显示游标（尽管只显示名称，而不显示照片）。 下面的代码展示了如何结合使用 `ContentProvider` 与 `SimpleCursorAdapter`（示例中没有此代码）：

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

若要详细了解如何实现 `SimpleCursorAdapter`，请参阅 [ListView 和适配器](~/android/user-interface/layouts/list-view/index.md)。

## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示（示例）](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)