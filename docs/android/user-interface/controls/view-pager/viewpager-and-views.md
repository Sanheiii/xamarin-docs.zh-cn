---
title: 带视图的 ViewPager
description: ViewPager 是一个布局管理器，可用于实现 gestural 导航。 Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。 本指南说明了如何使用 ViewPager 和 PagerTabStrip 实现 swipeable UI，使用视图作为数据页 (后面的指南介绍如何使用页面的片段) 。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e4f4243c06d98eac6f3501c41b48508f260d4633
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456985"
---
# <a name="viewpager-with-views"></a>带视图的 ViewPager

_ViewPager 是一个布局管理器，可用于实现 gestural 导航。Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。本指南说明了如何使用 ViewPager 和 PagerTabStrip 实现 swipeable UI，使用视图作为数据页 (后面的指南介绍如何使用页面的片段) 。_

## <a name="overview"></a>概述

本指南提供了分步演示如何使用 `ViewPager` 来实现落叶和长时间树的图像库的演练。 在此应用中，用户通过 "树目录" 向左和向右 swipes 查看树图像。 在目录的每一页的顶部，树的名称列在中 `PagerTabStrip` ，树的图像显示在中 `ImageView` 。 适配器用于将接口接口 `ViewPager` 到基础数据模型。 此应用实现从派生的适配器 `PagerAdapter` 。 

尽管 `ViewPager` 基于的应用通常是使用 s 实现的 `Fragment` ，但也有一些相对简单的用例，这种情况下不需要的额外复杂性 `Fragment` 。 例如，本演练中所示的基本映像库应用不需要使用 `Fragment` 。 因为内容是静态的，并且用户仅在不同的图像之间来回 swipes，所以可以通过使用标准 Android 视图和布局来使实现更加简单。 

## <a name="start-an-app-project"></a>启动应用项目

创建名为 **TreePager** 的新 android 项目 (参阅 [Hello、android](~/android/get-started/hello-android/hello-android-quickstart.md) 以获取有关新建 Android 项目) 的详细信息。 接下来，启动 NuGet 包管理器。  (有关安装 NuGet 包的详细信息，请参阅 [演练：在项目中包括 NuGet](/visualstudio/mac/nuget-walkthrough)) 。 查找并安装 **Android 支持库 v4**： 

[![在 NuGet 包管理器中选择的支持 v4 NuGet 的屏幕截图](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

这也会安装 **Android 支持库 v4**reaquired 的任何其他包。

## <a name="add-an-example-data-source"></a>添加示例数据源

在此示例中，树目录数据源 (`TreeCatalog` 类表示) 提供 `ViewPager` 有项内容。 
`TreeCatalog` 包含一组现成的树映像和树标题，适配器将使用该集合来创建 `View` 。 `TreeCatalog`构造函数不需要任何参数：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

中的图像集合 `TreeCatalog` 进行了组织，以便索引器可以访问每个图像。 例如，以下代码行检索集合中第三个图像的图像资源 ID： 

```csharp
int imageId = treeCatalog[2].imageId;
```

由于的实现细节 `TreeCatalog` 与理解无关 `ViewPager` ， `TreeCatalog` 此处未列出代码。 `TreeCatalog` [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)上提供的源代码。 下载此源文件 (或将代码复制并粘贴到新的 **TreeCatalog.cs** 文件中) 并将其添加到你的项目。 同时，将 [图像文件](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) 下载并解压缩到 **资源/可绘制** 文件夹中，并将其包含在项目中。 

## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开 **Resources/layout/main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>
```

此 XML 定义 `ViewPager` 占据整个屏幕的。 请注意，必须使用完全限定的名称 **ViewPager** ，因为 `ViewPager` 会打包到支持库中。 `ViewPager` 仅适用于 [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);它在 Android SDK 中不可用。 

## <a name="set-up-viewpager"></a>设置 ViewPager

编辑 **MainActivity.cs** 并添加以下 `using` 语句：

```csharp
using Android.Support.V4.View;
```

将 `OnCreate` 方法替换为以下代码：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

此代码将执行以下操作：

1. 设置 **main.axml** 布局资源中的视图。

2. 从布局中检索对的引用 `ViewPager` 。

3. 实例化一个新的 `TreeCatalog` 作为数据源。

生成并运行此代码时，应会看到类似于以下屏幕截图的显示： 

[![显示空 ViewPager 应用程序的屏幕截图](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此时， `ViewPager` 是空的，因为它缺少用于访问 **TreeCatalog**中的内容的适配器。 在下一部分中，将创建一个 **PagerAdapter** 以连接 `ViewPager` 到 **TreeCatalog**。 

## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用位于和数据源之间的适配器控制器对象 `ViewPager` (参阅 [适配器](~/android/user-interface/controls/view-pager/index.md#adapter)) 中的插图。 若要访问此数据， `ViewPager` 需要提供一个派生自的自定义适配器 `PagerAdapter` 。 此适配器 `ViewPager` 用数据源中的内容填充每个页面。 由于此数据源是特定于应用的，因此自定义适配器是了解如何访问数据的代码。 当用户 swipes 页面时 `ViewPager` ，适配器将从数据源中提取信息，并将其加载到页面中， `ViewPager` 以便显示。 

实现时 `PagerAdapter` ，必须重写以下项：

- **InstantiateItem** &ndash;`View`为给定位置创建)  (页面，并将其添加到 `ViewPager` 视图的集合。 

- **DestroyItem** &ndash; 从给定位置中删除页。

- **计数** &ndash; 只读属性，可返回 (页面) 可用的视图数。 

- **IsViewFromObject** &ndash; 确定页是否与特定的键对象相关联。  (此对象由 `InstantiateItem` 方法创建。 ) 在此示例中，密钥对象为 `TreeCatalog` 数据对象。

添加一个名为 **TreePagerAdapter.cs** 的新文件，并将其内容替换为以下代码： 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

此代码会将这一重要的 `PagerAdapter` 实现存根。 在以下部分中，这些方法中的每一个都被替换为工作代码。 

### <a name="implement-the-constructor"></a>实现构造函数

当应用程序实例化时 `TreePagerAdapter` ，它将 () 提供上下文， `MainActivity` 并将其实例化 `TreeCatalog` 。 `TreePagerAdapter`在**TreePagerAdapter.cs**中，将以下成员变量和构造函数添加到类的顶部： 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此构造函数的用途是存储将使用的上下文和 `TreeCatalog` 实例 `TreePagerAdapter` 。 

### <a name="implement-count"></a>实现计数

`Count`实现相对简单：返回树目录中的树数。 将 `Count` 替换为以下代码：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

的 `NumTrees` 属性 `TreeCatalog` 返回数据集中) 的页数 (页数。

### <a name="implement-instantiateitem"></a>实现 InstantiateItem

`InstantiateItem`方法为给定位置创建页面。 它还必须向的视图集合中添加新创建的视图 `ViewPager` 。 若要实现此过程，请将 `ViewPager` 自身作为容器参数进行传递。 

将 `InstantiateItem` 方法替换为以下代码：

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

此代码将执行以下操作：

1. 实例化一个新的 `ImageView` ，以显示指定位置的树图像。 应用的 `MainActivity` 是将传递到 `ImageView` 构造函数的上下文。

2. 将 `ImageView` 资源设置为 `TreeCatalog` 指定位置的图像资源 ID。

3. 将传递的容器强制转换 `View` 为 `ViewPager` 引用。
    请注意，必须使用 `JavaCast<ViewPager>()` 正确执行此强制转换 (这是必需的，以便 Android 执行运行时检查的类型转换) 。

4. 将实例化的添加 `ImageView` 到 `ViewPager` ，并将返回 `ImageView` 到调用方。

当在 `ViewPager` 上显示图像时 `position` ，会显示此 `ImageView` 。 最初， `InstantiateItem` 调用两次，用视图填充前两页。 当用户滚动时，将再次调用它来维护当前显示项的紧靠后的视图。 

### <a name="implement-destroyitem"></a>实现 DestroyItem

`DestroyItem`方法从给定位置中删除页。 在任何给定位置的视图都可以更改的应用程序中， `ViewPager` 必须在将旧视图替换为新视图之前，将其移到该位置。 在此 `TreeCatalog` 示例中，每个位置的视图不会更改，因此，在 `DestroyItem` `InstantiateItem` 针对该位置调用时，将只重新添加被删除的视图。  (为提高效率，可以实现一个 `View` 将重新显示在同一位置的池。 )  

将 `DestroyItem` 方法替换为以下代码： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此代码将执行以下操作：

1. 将传递的容器强制转换 `View` 为 `ViewPager` 引用。

2. 将传递的 Java 对象 (`view`) 转换为 c # `View` (`view as View`) ;

3. 从中移除视图 `ViewPager` 。 

### <a name="implement-isviewfromobject"></a>实现 IsViewFromObject

当用户在内容页中向左和向右滑动时， `ViewPager` `IsViewFromObject` 将调用以验证 `View` 给定位置处的子级是否与该位置的适配器的对象相关联 (因此，适配器的对象称为 *对象键*) 。 对于相对简单的应用程序，关联是标识 &ndash; 适配器的对象密钥的标识，该实例是之前通过返回到的视图 `ViewPager` `InstantiateItem` 。 但对于其他应用，对象键可能是与 (关联的其他特定于适配器的类实例，但与 `ViewPager` 在该位置显示的子视图) 不同。 只有适配器知道传递的视图和对象键是否关联。 

`IsViewFromObject` 必须实现 `PagerAdapter` 才能正常工作。 如果 `IsViewFromObject` `false` 对给定位置返回， `ViewPager` 则不会在该位置显示视图。 在 `TreePager` 应用程序中，由返回的对象键 `InstantiateItem` *是* 树的页面 `View` ，因此，该代码只需要检查标识 (即，对象键和视图是同一) 。 将 `IsViewFromObject` 替换为以下代码： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

现在 `TreePagerAdapter` 已经实现了，现在可以将其添加到中 `ViewPager` 。 在 **MainActivity.cs**中，将以下代码行添加到方法的末尾 `OnCreate` ：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此代码将实例化 `TreePagerAdapter` ，并传入 `MainActivity` 作为上下文 (`this`) 。 实例化 `TreeCatalog` 会传入构造函数的第二个参数。 将 `ViewPager` 的 `Adapter` 属性设置为实例化 `TreePagerAdapter` 对象; 这会将 `TreePagerAdapter` 插入 `ViewPager` 。 

核心实现现已完成 &ndash; 生成并运行应用。 屏幕上会显示树目录的第一个图像，如接下来的屏幕截图中所示。 向左轻扫可查看更多树视图，并向右轻扫可在树目录中向后移动： 

[![TreePager 应用通过树图像的屏幕截图](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>添加寻呼指示器

这一最小 `ViewPager` 实现显示树目录的映像，但不提供用户在目录中的位置的指示。 下一步是添加 `PagerTabStrip` 。 `PagerTabStrip`通知用户显示的是哪个页面，并通过显示上一页和下一页的提示来提供导航上下文。 `PagerTabStrip` 用于作为的当前页的指示器 `ViewPager` ; 当用户 swipes 每个页面时，它将滚动更新。 

打开 **Resources/layout/main.axml** 并将添加 `PagerTabStrip` 到布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` 和 `PagerTabStrip` 设计为协同工作。 在 `PagerTabStrip` 布局中声明时 `ViewPager` ， `ViewPager` 将自动查找 `PagerTabStrip` 并将其连接到适配器。 当你生成并运行应用时，你应看到在 `PagerTabStrip` 每个屏幕顶部显示空： 

[![空 PagerTabStrip 的特写屏幕快照](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>显示标题

若要将标题添加到每个页选项卡，请 `GetPageTitleFormatted` 在派生的类中实现方法 `PagerAdapter` 。 `ViewPager``GetPageTitleFormatted`如果实现了) 以获取描述指定位置的页的标题字符串，则调用 (。 将以下方法添加到 `TreePagerAdapter` **TreePagerAdapter.cs**中的类： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此代码检索树目录中指定页 (位置) 的树标题字符串，并将其转换为 Java `String` ，并将其返回到 `ViewPager` 。 当你通过此新方法运行应用时，每个页面将在中显示树标题 `PagerTabStrip` 。 你应在屏幕顶部看到树名称时不带下划线： 

[![带有文本填充的 PagerTabStrip 选项卡的页面屏幕截图](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以来回切换，以查看目录中的每个带标题的树图像。 

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 变体

`PagerTitleStrip` 类似于 `PagerTabStrip` ，只不过为 `PagerTabStrip` 当前选定的选项卡添加下划线。您可以 `PagerTabStrip` 将替换为 `PagerTitleStrip` 以上布局中的，并再次运行应用程序以查看其外观 `PagerTitleStrip` ： 

[![从文本中删除下划线的 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

请注意，当您将转换为时，下划线会被删除 `PagerTitleStrip` 。 

## <a name="summary"></a>总结

本演练提供了如何在 `ViewPager` 不使用的情况下生成基于基本的应用的分步示例 `Fragment` 。 其中显示了一个示例数据源，其中包含图像和标题字符串、 `ViewPager` 用于显示图像的布局以及一个将 `PagerAdapter` 连接 `ViewPager` 到数据源的子类。 为了帮助用户浏览数据集，包含了说明如何添加 `PagerTabStrip` 或 `PagerTitleStrip` 显示每页顶部的图像标题的说明。 

## <a name="related-links"></a>相关链接

- [TreePager (示例) ](/samples/xamarin/monodroid-samples/userinterface-treepager)