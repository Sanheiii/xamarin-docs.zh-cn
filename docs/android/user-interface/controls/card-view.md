---
title: CardView
description: Cardview 小组件是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。 本指南说明如何在 Xamarin Android 应用程序中使用和自定义 CardView，同时保持与早期版本的 Android 的向后兼容性。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d378b48741b6640f342d51e6eaa16aaf1a2f74a5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457453"
---
# <a name="xamarinandroid-cardview"></a>Xamarin CardView

_Cardview 小组件是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。本指南说明如何在 Xamarin Android 应用程序中使用和自定义 CardView，同时保持与早期版本的 Android 的向后兼容性。_

## <a name="overview"></a>概述

`Cardview`Android 5.0 中引入的小组件 (棒糖形) ，它是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。 `CardView` 作为 `FrameLayout` 带有圆角和阴影的小组件实现。 通常， `CardView` 用于在 `ListView` 或视图组中显示单个行项 `GridView` 。 例如，以下屏幕截图是 `CardView` 在可滚动的中实现基于的旅游目标卡的旅游预订应用的示例 `ListView` ：

![为每个旅行目标使用 CardView 的示例应用](card-view-images/01-cardview-example.png)

本指南说明如何将包添加 `CardView` 到你的 Xamarin Android 项目，如何添加 `CardView` 到你的布局中，以及如何 `CardView` 在你的应用程序中自定义外观。 此外，本指南还提供了 `CardView` 您可以更改的属性的详细列表，其中包括可帮助您 `CardView` 在早于 Android 5.0 棒糖上的 android 版本上使用的属性。

<a name="requirements"></a>

## <a name="requirements"></a>要求

需要使用新的 Android 5.0 及更高版本的功能， (包括 `CardView` 基于 Xamarin 的应用中的) ：

- **Xamarin.Android** &ndash; 必须安装和配置 Xamarin xamarin 4.20 或更高版本，并且必须安装 Visual Studio 或 Visual Studio for Mac。

- **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须通过 Android SDK 管理器进行安装。

- **JAVA JDK 1.8** &ndash; 如果特别面向 API 级别23和更早版本，可以使用 JDK 1.7。 JDK 1.8 可从 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)获得。

应用还必须包含 `Xamarin.Android.Support.v7.CardView` 包。 若要 `Xamarin.Android.Support.v7.CardView` 在 Visual Studio for Mac 中添加包：

1. 打开项目，右键单击 " **包** "，然后选择 " **添加包**"。

2. 在 " **添加包** " 对话框中，搜索 " **CardView**"。

3. 选择 " **Xamarin 支持库 V7 CardView**"，并单击 " **添加包**"。

`Xamarin.Android.Support.v7.CardView`在 Visual Studio 中添加包：

1. 打开项目，右键单击 "**解决方案资源管理器**" 窗格中的 "**引用**" 节点 () 并选择 "**管理 NuGet 包 ...**"。

2. 显示 " **管理 NuGet 包** " 对话框时，请在 "搜索" 框中输入 **CardView** 。

3. 出现 " **Xamarin 支持库 V7 CardView** " 时，单击 " **安装**"。

若要了解如何配置 Android 5.0 应用项目，请参阅 [设置 android 5.0 项目](~/android/platform/lollipop.md)。
有关安装 NuGet 包的详细信息，请参阅 [演练：在项目中包括 NuGet](/visualstudio/mac/nuget-walkthrough)。

## <a name="introducing-cardview"></a>CardView 简介

默认值 `CardView` 类似于具有最小圆角和轻微阴影的白色卡片。 下面的示例 **main.axml** 布局显示单个 `CardView` 小组件，其中包含 `TextView` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

如果你使用此 XML 来替换 **main.axml**的现有内容，请确保注释掉引用前面 XML 中的资源的 **MainActivity.cs** 中的任何代码。

此布局示例创建一个具有单行文本的默认值 `CardView` ，如以下屏幕截图所示：

[![带有白色背景和文本行的 CardView 屏幕截图](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此示例中，应用样式设置为 () 的亮材料主题， `Theme.Material.Light` 使 `CardView` 阴影和边缘更易于查看。 有关主题 Android 5.0 应用的详细信息，请参阅 [材料主题](~/android/user-interface/material-theme.md)。 在下一部分中，我们将了解如何 `CardView` 为应用程序自定义。

## <a name="customizing-cardview"></a>自定义 CardView

您可以修改基本 `CardView` 属性，以便 `CardView` 在应用程序中自定义的外观。 例如，的提升 `CardView` 增加为转换更大的阴影 (，这使得卡片看起来更大，使其在后台) 上更高。 另外，还可以增加角半径，使纸牌的拐角更大一些。

在下一个布局示例中，自定义 `CardView` 用于创建 ("快照" ) 的打印照片模拟。 `ImageView`将添加到 `CardView` 用于显示图像的，并 `TextView` 定位在 `ImageView` 用于显示图像标题的下方。
在此示例布局中， `CardView` 具有以下自定义：

- `cardElevation`增大到4dp 以转换更大的阴影。
- `cardCornerRadius`增大到5dp，以使拐角显得更大。

由于 `CardView` Android v7 支持库提供了它的属性，因此它的特性在命名空间中不可用 `android:` 。 因此，你必须定义自己的 XML 命名空间，并使用该命名空间作为 `CardView` 属性前缀。 在下面的布局示例中，我们将使用此行定义一个名为的命名空间 `cardview` ：

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以调用此命名空间， `card_view` 甚至在 `myapp` 您选择 (只能在此文件) 的范围内对其进行访问。 无论选择哪种方法来调用此命名空间，都必须使用该命名空间作为 `CardView` 要修改的属性的前缀。 在此布局示例中， `cardview` 命名空间为和的 `cardElevation` 前缀 `cardCornerRadius` ：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

当此布局示例用于显示照片查看应用中的图像时， `CardView` 具有照片快照的外观，如以下屏幕截图所示：

[![图像下方带有图像和标题的 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此屏幕截图是从 [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) 示例应用程序中获取的，它使用 `RecyclerView` 小组件提供 `CardView` 用于查看照片的图像滚动列表。 有关的详细信息 `RecyclerView` ，请参阅 [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) 指南。

请注意， `CardView` 可以在其内容区域中显示一个以上的子视图。 例如，在上述照片查看应用程序示例中，内容区域由包含和的组成 `ListView` `ImageView` `TextView` 。 尽管 `CardView` 实例通常是垂直排列的，但您也可以水平排列它们 (参阅为示例屏幕截图) [创建自定义视图样式](~/android/user-interface/material-theme.md#customview) 。

### <a name="cardview-layout-options"></a>CardView 布局选项

`CardView` 可以通过设置一个或多个影响其填充、仰角、角半径和背景色的属性，自定义布局：

[![CardView 属性图示](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

还可以通过调用对应方法，动态更改每个特性 `CardView` (有关方法的详细信息 `CardView` ，请参阅 [CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)) 。
请注意，这些属性 (除了背景色) 接受维度值，该值是一个十进制数，后跟单位。 例如， `11.5dp` 指定11.5 与密度无关的像素。

#### <a name="padding"></a>填充

`CardView` 提供五个填充属性来定位卡片内的内容。 可以在布局 XML 中进行设置，也可以在代码中调用类似的方法：

[![CardView 填充属性的图示](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填充属性如下所述：

- `contentPadding`&ndash;的子视图和卡片的所有边缘之间的内部填充 `CardView` 。

- `contentPaddingBottom`&ndash;的子视图与卡片的下边缘之间的内部填充 `CardView` 。

- `contentPaddingLeft`&ndash;的子视图和卡片的左边缘之间的内部填充 `CardView` 。

- `contentPaddingRight`&ndash;的子视图与卡片的右边缘之间的内部填充 `CardView` 。

- `contentPaddingTop`&ndash;的子视图 `CardView` 与卡片上边缘之间的内部填充。

内容填充属性相对于内容区域的边界，而不是相对于内容区域内的任何给定小组件。
例如，如果 `contentPadding` 在照片查看应用中得到了足够的增加，则 `CardView` 会裁剪图像和卡片上显示的文本。

#### <a name="elevation"></a>Elevation

`CardView` 提供了两个提升特性来控制其提升，因此，其阴影的大小为：

[![CardView 提升特性示意图](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提升属性如下所述：

- `cardElevation`&ndash; (的提升 `CardView` 表示其 Z 轴) 。

- `cardMaxElevation`的 &ndash; 最大仰角值 `CardView` 。

更大的值会 `cardElevation` 增加阴影大小，使其 `CardView` 看起来更大的背景。 `cardElevation`属性还确定了重叠视图的绘制顺序; 即，将在另一个具有较高提升设置的重叠 `CardView` 视图下绘制，并在具有较低提升设置的任何重叠视图上绘制。
此 `cardMaxElevation` 设置对应用动态更改提升非常有用， &ndash; 它可以防止阴影超出你用此设置定义的限制。

#### <a name="corner-radius-and-background-color"></a>角半径和背景色

`CardView` 提供可用于控制其角半径及其背景色的特性。 这两个属性允许您更改的总体样式 `CardView` ：

[![CardView 角 radious 和背景色属性图示](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

这些属性的解释如下：

- `cardCornerRadius`的 &ndash; 所有角的角半径 `CardView` 。

- `cardBackgroundColor`的 &ndash; 背景色 `CardView` 。

在此图中，将 `cardCornerRadius` 设置为更大的10dp，并将 `cardBackgroundColor` 设置为 `"#FFFFCC"` (浅黄色) 。

## <a name="compatibility"></a>兼容性

你可以使用 `CardView` 早于 android 5.0 棒糖形的 android 版本。 由于 `CardView` 属于 android v7 支持库，因此你可以将 `CardView` 与 android 2.1 (API 级别 7) 和更高版本结合使用。
但是，必须 `Xamarin.Android.Support.v7.CardView` 按照上述 [要求](#requirements)安装包。

`CardView` 在棒糖 (API 级别 21) 之前，设备上出现略微不同的行为：

- `CardView` 使用添加附加填充的编程阴影实现。

- `CardView` 不剪裁与的圆角相交的子视图  `CardView` 。

为了帮助管理这些兼容性差异， `CardView` 提供了可在布局中配置的几个附加属性：

- `cardPreventCornerOverlap`&ndash;将此属性设置为 `true` ，以在应用程序运行在早期 Android 版本 (API 级别20和更早版本) 时添加填充。 此设置可防止 `CardView` 内容与 `CardView` 的圆角相交。

- `cardUseCompatPadding`&ndash;将此特性设置为 `true` ，以在应用程序运行于或高于 API 级别21的 Android 版本中时添加填充。 如果要 `CardView` 在前棒糖设备上使用，并使其在棒糖形 (或更高) 版本上看起来相同，请将此属性设置为 `true` 。 启用此属性后，会 `CardView` 添加额外的填充，以在棒糖形设备上运行时绘制阴影。 这有助于克服当棒式编程影子实现生效时所引入的填充的差别。

有关保持与早期版本的 Android 兼容性的详细信息，请参阅 [维护兼容性](https://developer.android.com/training/material/compatibility.html)。

## <a name="summary"></a>总结

本指南介绍 `CardView` Android 5.0 (棒糖形) 中随附的新小组件。 它演示了默认 `CardView` 外观并说明了如何 `CardView` 通过更改其提升、角圆度、内容填充和背景色进行自定义。 它列出了 `CardView`)  (的布局属性，并介绍了如何 `CardView` 在 Android 5.0 棒糖之前的 android 设备上使用。 有关的详细信息 `CardView` ，请参阅 [CardView 类参考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。

## <a name="related-links"></a>相关链接

- [RecyclerView (示例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)