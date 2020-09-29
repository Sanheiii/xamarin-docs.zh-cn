---
title: 添加第二个工具栏
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: e6104a58c35b4daf8e204b3937022103d774615c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457762"
---
# <a name="adding-a-second-toolbar"></a>添加第二个工具栏

## <a name="overview"></a>概述 

`Toolbar`除了替换操作栏以外，它还可以在 &ndash; 一个活动中多次使用，并且可以对其进行自定义以放置在屏幕上的任何位置，并且可以将其配置为仅覆盖屏幕的部分宽度。 下面的示例演示如何创建另一个 `Toolbar` ，并将其放置在屏幕底部。 这会 `Toolbar` 实现 " **复制**"、" **剪切**" 和 " **粘贴** " 菜单项。 

## <a name="define-the-second-toolbar"></a>定义第二个工具栏 

编辑布局文件 **main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

此 XML 会将第二个添加到屏幕的 `Toolbar` 底部，并 `ImageView` 在屏幕中间显示空的。 此的高度 `Toolbar` 设置为操作栏的高度： 

```xml
android:minHeight="?android:attr/actionBarSize"
```

此的背景色 `Toolbar` 设置为将在下一次定义的强调文字颜色：

```xml
android:background="?android:attr/colorAccent
```

请注意，这 `Toolbar` 是基于与在操作栏中创建的不同 (**ActionBar**) 不同的主题，而不是 `Toolbar` [Replacing the Action Bar](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; 绑定到活动的窗口 décor 或第一个主题中使用的主题 `Toolbar` 。

编辑 **资源/值/styles.xml** ，并将以下强调颜色添加到样式定义： 

```xml
<item name="android:colorAccent">#C7A935</item>
```

这为底部工具栏提供了深灰色的琥珀色。 生成和运行应用会在屏幕底部显示空白的第二个工具栏： 

[![屏幕底部具有黄色第二个工具栏的应用屏幕截图](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>添加编辑菜单项 

本部分介绍如何在底部添加 "编辑" 菜单项 `Toolbar` 。 

向辅助副本添加菜单项 `Toolbar` ： 

1. 向应用项目的文件夹中添加菜单图标 `mipmap-` (如果需要) 。

2. 通过将附加的菜单资源文件添加到 **资源/菜单**来定义菜单项的内容。 

3. 在活动的 `OnCreate` 方法中， `Toolbar` 通过调用 `FindViewById`) 并陀螺形菜单来查找 (`Toolbar` 。

4. 为新菜单项实现中的 click 处理程序 `OnCreate` 。 

以下部分详细说明了此过程： " **剪切**"、" **复制**" 和 " **粘贴** " 菜单项将添加到底部 `Toolbar` 。 

### <a name="define-the-edit-menu-resource"></a>定义编辑菜单资源

在 **资源/菜单** 子目录中，创建一个名为 **edit_menus.xml** 的新 XML 文件，并将内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

此 XML 使用添加到文件夹中的 " **剪切**"、" **复制**" 和 " **粘贴** " 菜单项 (`mipmap-` [替换操作栏](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)) 。

### <a name="inflate-the-menus"></a>菜单菜单

在 MainActivity.cs 的方法末尾 `OnCreate` ，添加**MainActivity.cs**以下代码行： 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

此代码将查找 `edit_toolbar` 在 **main.axml**中定义的视图，将其标题设置为 " **编辑**"，并增加其菜单项 (在 **edit_menus.xml**) 中定义。 它定义了一个菜单单击处理程序，该处理程序显示 toast 以指示点击了哪个编辑图标。 

构建并运行应用。 应用运行时，将显示上面添加的文本和图标，如下所示： 

[![带有剪切、复制和粘贴图标的底部工具栏示意图](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

点击 " **剪切** " 菜单图标会显示以下 toast： 

[![Toast 屏幕截图，指示点击了剪切菜单图标](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

点击任一工具栏上的菜单项将显示生成的 toast： 

[![用于 "保存"、"复制" 和 "粘贴" 菜单项的 Toast 的屏幕截图](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>向上键 

大多数 Android 应用依赖于应用导航的 " **后退** " 按钮;按 " **后退** " 按钮会使用户进入上一屏幕。
但是，您可能还需要 **提供一个按钮** ，使用户可以轻松地导航到应用程序的主屏幕。 当用户选择 " **向上** " 按钮时，用户会在应用层次结构中上移到较高的级别，即 &ndash; ，应用在后退堆栈中弹出用户多个活动，而不是弹出回以前访问的活动。 

若要在使用作为操作栏的第二个活动中启用 " **上移** " 按钮 `Toolbar` ，请调用 `SetDisplayHomeAsUpEnabled` `SetHomeButtonEnabled` 第二个活动的方法中的和方法 `OnCreate` ：

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

[Support V7 Toolbar](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)代码示例演示了操作中的**向上**键。 此示例 (使用下一个) 所述的 AppCompat 库实现第二个活动，该活动使用工具栏上的按钮 **向上** 导航到上一个活动。 在此示例中， `DetailActivity` 通过进行以下方法调用，"主页" 按钮启用 " **向上** " 按钮 `SupportActionBar` ： 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

当用户导航到时 `MainActivity` `DetailActivity` ，将 `DetailActivity` 显示一个 (左箭头) 的 **向上** 按钮，如屏幕截图中所示：

[![工具栏中的向上按钮左箭头的屏幕截图示例](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

点击此 **按钮** 会使应用返回到 `MainActivity` 。 在具有多个层次结构级别的更复杂的应用中，点击此按钮会将用户返回到应用中的下一个最高级别，而不是返回到上一屏幕。 

## <a name="related-links"></a>相关链接

- [棒糖形工具栏 (示例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏 (示例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)