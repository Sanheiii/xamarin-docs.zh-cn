---
title: 扩展 RecyclerView 示例
description: 向 RecyclerView 示例应用添加项-click 事件处理程序。
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 80b7dc5554e0db525de7fcea274e82cdf3e8c7f4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457119"
---
# <a name="extending-the-recyclerview-example"></a>扩展 RecyclerView 示例

[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中所述的基本应用实际上 &ndash; 只是滚动并显示一个固定的照片项列表以便于浏览。 在实际应用程序中，用户希望能够通过点击显示中的项目与应用进行交互。 此外，基础数据源可以更改 (或由应用更改) ，并且显示内容必须与这些更改保持一致。 在下面的部分中，你将了解如何处理项单击事件，并在 `RecyclerView` 基础数据源发生更改时进行更新。

### <a name="handling-item-click-events"></a>处理项单击事件

当用户在中触及某个项时 `RecyclerView` ，将生成一个项单击事件，以通知应用程序所涉及的项目。 此事件不是由生成的 `RecyclerView` &ndash; ， (中包装在视图刀柄内的项视图) 检测触控并报告这些触控为 click 事件。

为了说明如何处理项单击事件，以下步骤说明了如何修改基本照片查看应用程序，以报告用户已接触到的照片。 在示例应用中发生项单击事件时，将发生以下序列：

1. 照片会 `CardView` 检测项目单击事件，并通知适配器。

2. 适配器将事件 (与项位置信息) 转发到活动的项单击处理程序。

3. 活动的项单击处理程序响应项单击事件。

首先，将名为的事件处理程序成员 `ItemClick` 添加到 `PhotoAlbumAdapter` 类定义中：

```csharp
public event EventHandler<int> ItemClick;
```

接下来，将向添加一个项 click 事件处理程序方法 `MainActivity` 。
此处理程序会短暂显示一个 toast，指出涉及了哪个照片项：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下来，需要一行代码才能向注册 `OnItemClick` 处理程序 `PhotoAlbumAdapter` 。 创建后，可以立即执行此 `PhotoAlbumAdapter` 操作： 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

在此基本示例中，处理程序注册发生在主活动的 `OnCreate` 方法中，但生产应用程序可能在中注册该处理程序， `OnResume` 并在中注销该处理程序 `OnPause` &ndash; 。有关详细信息，请参阅 [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md) 。

`PhotoAlbumAdapter` 现在会 `OnItemClick` 在收到项单击事件时调用。 下一步是在适配器中创建引发此事件的处理程序 `ItemClick` 。 下面的方法 `OnClick` 立即添加到适配器的 `ItemCount` 方法之后：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

此 `OnClick` 方法是项视图中项单击事件的适配器 *侦听器* 。 `PhotoViewHolder`必须先修改构造函数以将此方法作为附加参数接受，并 `OnClick` 使用 item view 事件进行注册，然后才能将此侦听器注册到项视图)  (`Click` 。
下面是修改后的 `PhotoViewHolder` 构造函数：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`参数包含对用户所接触的的引用 `CardView` 。 请注意，视图持有者基类 `CardView` 通过属性) 了解它所 (表示的项 () 的布局位置 `LayoutPosition` ，此位置将 `OnClick` 在发生项单击事件时传递到适配器的方法。 修改适配器的 `OnCreateViewHolder` 方法以将适配器的 `OnClick` 方法传递给视图持有者的构造函数：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

现在，当你生成并运行示例照片查看应用时，点击显示器中的照片将会出现 toast，指出涉及了哪些照片：

[![点击照片卡时显示的示例 Toast](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

此示例演示了一种用于实现事件处理程序的方法 `RecyclerView` 。 此处可以使用的另一种方法是将事件放在视图容器上，并让适配器订阅这些事件。 如果示例照片应用程序提供了一种照片编辑功能，则需要单独的事件，其中每个事件都是必需的。在中，将 `ImageView` `TextView` `CardView` `TextView` 启动一个 `EditView` 对话框，使用户能够编辑标题，而在上接触时， `ImageView` 将启动允许用户裁剪或旋转照片的照片 touchup 工具。 根据应用程序的需要，必须设计最佳方法来处理和响应触控事件。

若要演示在 `RecyclerView` 数据集更改时可以更新的方式，可以将照片查看应用程序修改为随机选择数据源中的照片，并将其替换为第一个照片。 首先，将 **随机选取** 按钮添加到示例照片应用的 **main.axml** 布局中：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

接下来，将代码添加到主活动的方法末尾， `OnCreate` 以查找 `Random Pick` 布局中的按钮，并为其附加一个处理程序：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

`RandomSwap`当点击**随机选择**按钮时，此处理程序将调用 photo 唱片集的方法。 `RandomSwap`方法使用数据源中的第一张照片随机交换照片，然后返回随机交换照片的索引。 使用此代码编译和运行示例应用时，点击 **随机选取** 按钮不会导致显示变化，因为 `RecyclerView` 无法识别对数据源所做的更改。

若要在 `RecyclerView` 数据源更改后保持更新，则必须修改 **随机 Pick** 单击处理程序，以便 `NotifyItemChanged` 为集合中已更改的每个项调用适配器的方法 (在此示例中，两个项已更改：第一张照片和交换的照片) 。 这将导致 `RecyclerView` 更新其显示，使其与数据源的新状态保持一致：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

现在，当点击 **随机 Pick** 按钮时，将 `RecyclerView` 更新显示内容，以显示集合中更深层的照片已与集合中的第一张照片交换：

[![交换前的第一个屏幕截图，交换后的第二个屏幕截图](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

当然， `NotifyDataSetChanged` 可以调用，而不是进行两次调用 `NotifyItemChanged` ，但这样做会强制 `RecyclerView` 刷新整个集合，即使集合中只有两个项发生了更改。 调用 `NotifyItemChanged` 比调用的效率要高得多 `NotifyDataSetChanged` 。

## <a name="related-links"></a>相关链接

- [RecyclerViewer (示例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)