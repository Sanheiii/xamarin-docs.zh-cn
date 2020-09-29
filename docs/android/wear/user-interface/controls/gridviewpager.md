---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 9c80535dfddd2a279fac66ab159c2a600681bb71
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456998"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)示例演示如何实现适用于 Android 磨损的2d 选取器导航模式。

![正方形显示屏上 GridViewPager 的示例屏幕截图](gridviewpager-images/gridviewpager.png)

首先将 [Xamarin Android 磨损支持](https://www.nuget.org/packages/Xamarin.Android.Wear/) NuGet 包添加到你的项目。

布局 XML 如下所示：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

创建一个 [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
 (或子类，如 [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
提供要在用户导航时显示的视图。

[示例适配器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)演示如何实现所需的方法，包括 `RowCount` 、、 `GetColumnCount` `GetBackground` 和的替代`GetFragment`

按如下所示绑定适配器：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```

## <a name="related-links"></a>相关链接

- [Google 的2D 选取器文档](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [可穿戴文档](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (示例) ](/samples/xamarin/monodroid-samples/wear-gridviewpager)