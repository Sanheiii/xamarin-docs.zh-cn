---
title: Xamarin.Android 性能
description: 可以通过许多方法提高使用 Xamarin.Android 构建的应用程序的性能。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。 本文介绍并讨论这些方法。
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e49c6d92d4c6bcabf45047321e183fe36d19096b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454710"
---
# <a name="xamarinandroid-performance"></a>Xamarin.Android 性能

_可以通过很多方法来提高使用 Xamarin.Android 构建的应用程序的性能。这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。本文将介绍并讨论这些方法。_

## <a name="performance-overview"></a>性能概述

应用程序性能差表现在许多方面。 这会造成应用程序看起来无响应，导致滚动缓慢，还可降低电池寿命。 但是，优化性能不止需要实现高效的代码。 还必须考虑用户对应用程序性能的体验。 例如，确保操作执行不会妨碍用户执行其他活动，这有助于改进用户的体验。

可以通过许多方法提高使用 Xamarin.Android 构建的应用程序的性能和感知性能。 它们包括：

- [优化布局层次结构](#optimizelayout)
- [优化列表视图](#optimizelistviews)
- [在活动中删除事件处理程序](#removeeventhandlers)
- [限制服务的有效期](#limitservices)
- [收到通知时释放资源](#releaseresources)
- [隐藏用户界面时释放资源](#releaseresourcesuihidden)
- [优化图像资源](#optimizeimages)
- [释放未使用的图像资源](#disposeimages)
- [避免使用浮点运算](#avoidfloats)
- [关闭对话框](#dismissdialogs)

> [!NOTE]
> 阅读本文之前，首先应阅读[跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中讨论了非平台特定方法，可用于改善使用 Xamarin 平台生成的应用程序的内存使用情况和性能。

<a name="optimizelayout"></a>

## <a name="optimize-layout-hierarchies"></a>优化布局层次结构

添加到应用程序的每个布局都需要执行初始化、布局和绘制。 嵌套使用 `weight` 参数的 [`LinearLayout`](xref:Android.Widget.LinearLayout) 实例时，布局过程可能很昂贵，因为每个子级都将测量两次。 使用 `LinearLayout` 的嵌套实例可能产生深层视图层次结构，这可能导致将布局的较差性能加倍放大，例如在 [`ListView`](xref:Android.Widget.ListView) 中。 因此，对这种布局进行优化很重要，因为之后的性能优势会成倍增加。

例如，考虑对具有图标、标题和说明的列表视图行使用 [`LinearLayout`](xref:Android.Widget.LinearLayout) 的情况。 `LinearLayout` 将包含 [`ImageView`](xref:Android.Widget.ImageView) 以及包含两个 [`TextView`](xref:Android.Widget.TextView) 实例的垂直 `LinearLayout`：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

此布局的层级为 3 级，当放大 [`ListView`](xref:Android.Widget.ListView) 行时则是一种浪费。 但是，可以通过平展布局改善这种情况，如以下代码示例所示：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

以前的 3 级层次结构已缩减为 2 级层次结构，并且一个 [`RelativeLayout`](xref:Android.Widget.RelativeLayout) 取代了两个 [`LinearLayout`](xref:Android.Widget.LinearLayout) 实例。 放大布局的每个 [`ListView`](xref:Android.Widget.ListView) 行时，性能将显著提高。

<a name="optimizelistviews"></a>

## <a name="optimize-list-views"></a>优化列表视图

用户期望平滑滚动并快速加载 [`ListView`](xref:Android.Widget.ListView) 实例。 但是，当每个列表视图行包含深度嵌套的视图层次结构或列表视图行包含复杂布局时，滚动性能会降低。 但是，可以使用一些方法避免出现不佳的 `ListView` 性能：

- 重复使用行视图 有关详细信息，请参阅[重复使用行视图](#reuserowviews)。
- 尽量平展布局。
- 缓存从 Web 服务检索的行内容。
- 避免缩放图像。

结合使用这些方法有助于保持 [`ListView`](xref:Android.Widget.ListView) 实例的平滑滚动。

<a name="reuserowviews"></a>

### <a name="reuse-row-views"></a>重复使用行视图

在 [`ListView`](xref:Android.Widget.ListView) 中显示数百个行时，若一次仅在屏幕上显示其中一小部分，创建数百个 [`View`](xref:Android.Views.View) 对象则是在浪费内存。 相反，应仅将屏幕上行中显示的 `View` 对象加载到内存中，同时**内容**将加载到这些重复使用的对象中。 这可以防止实例化数百个其他对象，从而节省时间和内存。

因此，当某一行从屏幕上消失后，可以将其视图放到队列中以供重复使用，如下面的代码示例所示：

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

当用户滚动时，[`ListView`](xref:Android.Widget.ListView) 调用 `GetView` 重写以请求显示新视图，（如果有）它会在 `convertView` 参数中传递一个未使用的视图。 如果此值为 `null`，则代码将创建一个新的 [`View`](xref:Android.Views.View) 实例，否则可以重置和重复使用 `convertView` 属性。

有关详细信息，请参阅[用数据填充 ListView](~/android/user-interface/layouts/list-view/populating.md) 中的[重复使用行视图](~/android/user-interface/layouts/list-view/populating.md#row-view-re-use)。

<a name="removeeventhandlers"></a>

## <a name="remove-event-handlers-in-activities"></a>在活动中删除事件处理程序

当在 Android 运行时中销毁活动时，该活动仍可能在 Mono 运行时处于活动状态。 因此，请在 `Activity.OnPause` 中删除外部对象的事件处理程序，以防止运行时继续引用已销毁的活动。

在活动中，在类级别处声明事件处理程序：

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

然后在活动中实现该处理程序，如在 `OnResume` 中：

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

当活动退出运行状态时，会调用 `OnPause`。 在 `OnPause` 实现中，删除处理程序，如下所示：

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices"></a>

## <a name="limit-the-lifespan-of-services"></a>限制服务的有效期

服务启动时，Android 会持续运行该服务进程。 这使得进程很昂贵，因为其内存不能分页或在其他地方使用。 如果持续运行已不需要的服务，会增加应用程序由于内存限制而出现性能降低的风险。 还可导致应用程序切换效率降低，因为它减少了 Android 可以缓存的进程数。

使用 `IntentService` 可以限制服务的有效期，处理完最初启动的目的时服务便可自动终止。

<a name="releaseresources"></a>

## <a name="release-resources-when-notified"></a>收到通知时释放资源

在应用程序生命周期内，[`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) 回调会在设备内存较低时发出通知。 应实现此回调以侦听以下内存级别的通知：

- [`TrimMemoryRunningModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate) - 应用程序可能需要释放一些不需要的资源。
- [`TrimMemoryRunningLow`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningLow) - 应用程序应释放不需要的资源。
- [`TrimMemoryRunningCritical`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical) - 应用程序应尽可能多地释放非关键进程。

此外，缓存应用程序进程时，[`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) 回调可能会收到以下内存级别的通知：

- [`TrimMemoryBackground`](xref:Android.Content.ComponentCallbacks2.TrimMemoryBackground) - 释放如果用户返回到应用，则可以快速高效地重新生成的资源。
- [`TrimMemoryModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryModerate) - 释放资源有助于系统继续缓存其他进程以提高整体性能。
- [`TrimMemoryComplete`](xref:Android.Content.ComponentCallbacks2.TrimMemoryComplete) - 如果未能尽快恢复更多内存，将很快终止应用程序进程。

应根据接收到的级别释放资源来响应通知。

<a name="releaseresourcesuihidden"></a>

## <a name="release-resources-when-the-user-interface-is-hidden"></a>隐藏用户界面时释放资源

在用户导航到另一应用时，释放该应用的用户界面使用的所有资源，因为这样可以显著提高 Android 的缓存进程容量，这反过来会影响用户体验质量。

若要在用户退出用户界面时接收通知，需在 `Activity` 类中实现 [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) 回调，并侦听 [`TrimMemoryUiHidden`](xref:Android.Content.ComponentCallbacks2.TrimMemoryUiHidden) 级别，该值指示用户界面已从视图中隐藏。 仅当应用程序的所有用户界面组件均对用户隐藏时，才会收到此通知。 收到此通知时释放用户界面资源，可确保当用户从应用中的另一个活动导航回来后，用户界面资源仍可用于快速恢复活动。

<a name="optimizeimages"></a>

## <a name="optimize-image-resources"></a>优化图像资源

图像是应用程序使用的一些最昂贵的资源，通常以高分辨率捕获。 因此，显示图像时，请采用设备屏幕所必需的分辨率显示它。 如果图像的分辨率比屏幕高，则应降低。

有关详细信息，请参阅[跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南中的[优化图像资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

<a name="disposeimages"></a>

## <a name="dispose-of-unused-image-resources"></a>释放未使用的图像资源

为了节省内存使用，最好释放不再需要的大型图像资源。 但是，请必须确保图像被正确地释放。 可以利用 [using](/dotnet/csharp/language-reference/keywords/using-statement) 语句以确保正确使用 `.Dispose()` 对话，而不利用显式 `IDisposable` 调用。 

例如，[位图](xref:Android.Graphics.Bitmap)类可实现 `IDisposable`。 在 `using` 块中包装 `BitMap` 对象的实例化可确保从块中退出时它能被正确地释放：

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

有关释放可释放资源的详细信息，请参阅[释放 IDisposable 资源](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable)。  

<a name="avoidfloats"></a>

## <a name="avoid-floating-point-arithmetic"></a>避免使用浮点运算

在 Android 设备上，浮点运算比整数运算的速度大约慢两倍。 因此，应尽可能用整数运算取代浮点运算。 但是，`float` 和 `double` 运算在最新硬件上不存在执行时间差异。

> [!NOTE]
> 甚至对于整数运算，一些 CPU 也缺少硬件划分功能。 因此，通常在软件中执行整数除法和取模运算。

<a name="dismissdialogs"></a>

## <a name="dismiss-dialogs"></a>关闭对话框

对话框的操作目的完成后，当使用 [`ProgressDialog`](xref:Android.App.ProgressDialog) 类（或任何对话框或警报）而不是调用 [`Hide`](xref:Android.App.Dialog.Hide*) 方法时，请调用 [`Dismiss`](xref:Android.App.Dialog.Dismiss*) 方法。 否则，该对话框将仍处于活动状态，并由于继续引用活动而导致泄露活动。

## <a name="summary"></a>总结

本文介绍和讨论了提高使用 Xamarin.Android 构建的应用程序的性能的方法。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。

## <a name="related-links"></a>相关链接

- [跨平台性能](~/cross-platform/deploy-test/memory-perf-best-practices.md)