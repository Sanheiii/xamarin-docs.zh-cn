---
title: 基本 RecyclerView 示例
description: 演示如何使用 RecyclerView 的示例应用。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: d4a775d1e96fd6650623c2a151ae74b8b68ce0ac
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457076"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 示例

为了了解 `RecyclerView` 典型应用程序中的工作原理，本主题介绍了 [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) 示例应用，这是一个用于 `RecyclerView` 显示大量照片的简单代码示例： 

[![使用 CardViews 显示照片的 RecyclerView 应用程序的两个屏幕截图](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** 使用 [CardView](~/android/user-interface/controls/card-view.md) 实现布局中的每个照片项 `RecyclerView` 。 由于性能方面的 `RecyclerView` 优势，此示例应用程序能够快速滚动大量照片，而不会出现明显的延迟。

### <a name="an-example-data-source"></a>示例数据源

在此示例应用程序中，"唱片集" 数据源 (类表示， `PhotoAlbum`) 提供 `RecyclerView` 项内容。
`PhotoAlbum` 带有字幕的照片集合;当你对其进行实例化时，将获得一个现成的32照片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

中的每个照片实例都 `PhotoAlbum` 公开了属性，这些属性允许您读取其图像资源 ID 及其 `PhotoID` 标题字符串 `Caption` 。 照片集合经过组织，可以通过索引器访问每张照片。 例如，以下代码行访问集合中第十张照片的图像资源 ID 和标题：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 还提供了一个 `RandomSwap` 方法，可以调用该方法，将集合中的第一个照片替换为集合中其他位置随机选择的照片：

```csharp
mPhotoAlbum.RandomSwap ();
```

由于的实现细节 `PhotoAlbum` 与理解无关 `RecyclerView` ，因此 `PhotoAlbum` 此处不会显示源代码。 `PhotoAlbum` [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer)示例应用程序的[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中提供了源代码。

### <a name="layout-and-initialization"></a>布局和初始化

布局文件 **main.axml**包含中的单个 `RecyclerView` `LinearLayout` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

请注意，必须使用完全限定的名称 **v7** ，因为 `RecyclerView` 会打包到支持库中。 的 `OnCreate` 方法 `MainActivity` 初始化此布局，实例化适配器，并准备基础数据源：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

此代码将执行以下操作：

1. 实例化 `PhotoAlbum` 数据源。

2. 将唱片集数据源传递到适配器的构造函数， `PhotoAlbumAdapter` (本指南稍后部分中定义的) 。 
   请注意，将数据源作为参数传递给适配器的构造函数是一种最佳做法。 

3. `RecyclerView`从布局中获取。

4. 通过调用方法将适配器插入到实例中， `RecyclerView` `RecyclerView` `SetAdapter` 如上面所示。

### <a name="layout-manager"></a>布局管理器

中的每一项 `RecyclerView` 都由一个 `CardView` 包含照片图像和照片标题的组成， (详细信息将在下面) 的 " [查看容器](#view-holder) " 部分中介绍。 预定义 `LinearLayoutManager` 用于 `CardView` 在垂直滚动排列中布局每个：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此代码位于主活动的方法中 `OnCreate` 。 布局管理器的构造函数需要 *上下文*，因此 `MainActivity` 使用传递的是， `this` 如上所述。

可以不使用预定义的，而是 `LinearLayoutManager` 插入一个自定义的布局管理器，该管理器将并排显示两个 `CardView` 项，实现页面翻转动画效果以遍历照片集。 稍后在本指南中，你将看到一个示例，说明如何通过在不同的布局管理器中进行交换来修改布局。

<a name="view-holder"></a>

### <a name="view-holder"></a>查看刀柄

调用视图持有者类 `PhotoViewHolder` 。 每个 `PhotoViewHolder` 实例都包含对 `ImageView` 关联行项的和的引用 `TextView` ，这些行项在中 `CardView` 按图解的方式进行布局：

[![包含 ImageView 和 TextView 的 CardView 关系图](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 派生自 `RecyclerView.ViewHolder` ，其中包含用于存储对的引用 `ImageView` 并 `TextView` 显示在上述布局中的属性。
`PhotoViewHolder` 包含两个属性和一个构造函数：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

在此代码示例中， `PhotoViewHolder` 构造函数向包装的)  (对父项视图的引用 `CardView` `PhotoViewHolder` 。 请注意，始终将父项视图转发到基构造函数。 `PhotoViewHolder`构造函数对 `FindViewById` 父项视图调用以定位其每个子视图引用， `ImageView` 并分别将 `TextView` 结果存储在 `Image` 和属性中 `Caption` 。 以后，适配器将在用新数据更新此子视图时从这些属性中检索视图引用 `CardView` 。

有关的详细信息 `RecyclerView.ViewHolder` ，请参阅 [RecyclerView. ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

### <a name="adapter"></a>适配器

适配器 `RecyclerView` 使用特定照片的数据加载每一行。 例如，对于位于第 *p*行的给定照片，适配器会在数据源中的位置 *p* 找到关联的数据，并将此数据复制到集合中位于 *p* 位置的行项 `RecyclerView` 。 适配器使用视图持有者在该位置查找和的引用 `ImageView` ， `TextView` 这样 `FindViewById` 当用户滚动照片集合并重复使用视图时，无需重复调用这些视图。

在 **RecyclerViewer**中，适配器类从派生， `RecyclerView.Adapter` 以创建 `PhotoAlbumAdapter` ：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

`mPhotoAlbum`成员包含 (传入构造函数的相册) 的数据源; 构造函数将相册复制到此成员变量中。 实现以下必需 `RecyclerView.Adapter` 方法：

- **`OnCreateViewHolder`**&ndash;实例化项布局文件和视图持有者。

- **`OnBindViewHolder`** 将 &ndash; 位于指定位置的数据加载到其引用存储在给定视图持有者中的视图中。

- **`ItemCount`**&ndash;返回数据源中的项数。

布局管理器在定位中的项时调用这些方法 `RecyclerView` 。 以下各节将对这些方法的实现进行了检查。

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

`OnCreateViewHolder`当 `RecyclerView` 需要新的视图持有者来表示项时，布局管理器会调用。 `OnCreateViewHolder` 从视图的布局文件中增加项视图，并在新的实例中包装视图 `PhotoViewHolder` 。 `PhotoViewHolder`构造函数在布局中查找并存储对子视图的引用，如前面的 "[查看容器](#view-holder)" 中所述。

每个行项由一个 `CardView` (表示，其中包含 `ImageView` 照片) 的和 `TextView` 标题) 的 (。 此布局位于 PhotoCardView 文件中 **。 main.axml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

此布局表示中的单个行项 `RecyclerView` 。 `OnBindViewHolder`下面 (的方法) 将数据从数据源复制到 `ImageView` 此布局的和中 `TextView` 。
`OnCreateViewHolder` 为中的给定照片位置增加此布局 `RecyclerView` ，并实例化一个新的 `PhotoViewHolder` 实例， (该实例 `ImageView` `TextView` 在关联布局) 中查找并缓存对和子视图的引用 `CardView` 。

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

生成的视图所有者实例 `vh` 返回到 (布局管理器) 的调用方。

#### <a name="onbindviewholder"></a>OnBindViewHolder

当布局管理器准备好在的可视屏幕区域中显示特定视图时 `RecyclerView` ，它会调用适配器的 `OnBindViewHolder` 方法，以使用来自数据源的内容来填充位于指定行位置的项。 `OnBindViewHolder` 获取特定行位置的照片信息 (照片的图像资源和照片标题) 的字符串，并将此数据复制到关联的视图。 视图通过通过参数) 传入的引用存储在视图容器对象 (中 `holder` 。

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

在这种情况下，必须首先将传入的视图持有者对象强制转换为派生的视图持有者类型 (， `PhotoViewHolder`) 。
适配器将图像资源加载到视图持有者的属性所引用的视图 `Image` 中，并将标题文本复制到视图持有者的属性所引用的视图中 `Caption` 。 这会将关联视图与其数据 *绑定* 在一起。

请注意， `OnBindViewHolder` 是直接处理数据结构的代码。 在这种情况下， `OnBindViewHolder` 了解如何将 `RecyclerView` 项位置映射到数据源中其关联数据项。 在这种情况下，映射很简单，因为位置可用作相册中的数组索引;但是，更复杂的数据源可能需要额外的代码来建立此类映射。

#### <a name="itemcount"></a>ItemCount

`ItemCount`方法返回数据集合中的项数。 在示例照片查看器应用程序中，项计数是相册中照片的数目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

有关的详细信息 `RecyclerView.Adapter` ，请参阅 [RecyclerView 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

### <a name="putting-it-all-together"></a>将其全部放在一起

`RecyclerView`示例照片应用程序的结果实现包括 `MainActivity` 创建数据源、布局管理器和适配器的代码。 `MainActivity` 创建 `mRecyclerView` 实例，实例化数据源和适配器，并插入布局管理器和适配器：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` 查找并缓存视图引用：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` 实现三个所需的方法重写：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

编译并运行此代码时，它将创建基本照片查看应用，如以下屏幕截图所示：

[![具有垂直滚动照片卡的照片查看应用的两个屏幕截图](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果未绘制阴影 (如以上屏幕截图所示) 中，编辑 **Properties/AndroidManifest.xml** ，并将以下属性设置添加到 `<application>` 元素：

```xml
android:hardwareAccelerated="true"
```

此基本应用仅支持浏览相册。 它不响应项触控事件，也不处理基础数据中的更改。 此功能是在 [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)中添加的。

### <a name="changing-the-layoutmanager"></a>更改 LayoutManager

由于 `RecyclerView` 非常灵活，因此可轻松修改应用以使用不同的布局管理器。 在下面的示例中，将对其进行修改，以显示带有水平滚动而不是垂直线性布局的网格布局的相册。 为此，布局管理器实例化将被修改为使用，如下所示 `GridLayoutManager` ：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此代码更改将垂直替换为 `LinearLayoutManager` 一个 `GridLayoutManager` ，它表示由两行组成的网格，该网格沿水平方向滚动。 再次编译并运行应用程序时，将看到照片显示在网格中，并且滚动是水平而不是垂直的：

[![网格中水平滚动照片的应用的示例屏幕截图](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

通过只更改一行代码，可以修改照片查看应用程序，以使用具有不同行为的不同布局。
请注意，适配器代码和布局 XML 都必须修改以更改布局样式。 

在下一主题中， [扩展了 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，扩展了此基本示例应用程序以处理项单击事件，并在 `RecyclerView` 基础数据源发生更改时进行更新。

## <a name="related-links"></a>相关链接

- [RecyclerViewer (示例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)