---
title: Android 中的触控
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 154c33944e48f1409b52f14c297befa960b84f39
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455126"
---
# <a name="touch-in-android"></a>Android 中的触控

与 iOS 非常类似，Android 会创建一个对象，该对象保存有关用户与屏幕上的用户物理交互的数据 &ndash; `Android.View.MotionEvent` 。 此对象保存数据，如执行的操作、触控发生的位置、应用的压力等。 `MotionEvent` 对象将移动细分为以下值：

- 描述运动类型的操作代码，如初次触摸、触摸屏上移动或触摸结束。

- 一组轴值，用于描述 `MotionEvent` 和其他移动属性（例如触摸正在发生的位置、触控发生的时间以及使用的压力）。
   轴值可能因设备而异，因此上一个列表不描述所有轴值。

`MotionEvent`对象将被传递到应用程序中的适当方法。 Xamarin Android 应用程序响应触控事件有三种方式：

- *将事件处理程序分配 `View.Touch` 到*- `Android.Views.View` 类有一个 `EventHandler<View.TouchEventArgs>` 应用程序可以为其分配处理程序。 这是典型的 .NET 行为。

- *实现 `View.IOnTouchListener` *-可以使用视图将此接口的实例分配给视图对象。 `SetOnListener` 付款方式.这在功能上等效于向事件分配事件处理程序 `View.Touch` 。 如果有一些公共或共享的逻辑在触摸时可能需要许多不同的视图，则创建类并实现此方法比为每个视图分配自己的事件处理程序更有效。

- *替代 `View.OnTouchEvent` *-Android 子类中的所有视图 `Android.Views.View` 。 查看视图时，Android 将调用，并将 `OnTouchEvent` `MotionEvent` 对象作为参数传递。

> [!NOTE]
> 并非所有 Android 设备都支持触摸屏。 

将以下标记添加到清单文件会导致 Google Play 仅向已启用触控的设备显示应用程序：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>笔势

手势是触摸屏上的手绘形状。 该笔势可以有一个或多个笔划，每个笔划都由与屏幕不同的点联系所创建的点序列组成。 Android 可以支持多种不同类型的手势，从屏幕上的简单 fling 到涉及多点触控的复杂手势。

Android 提供 `Android.Gestures` 专用于管理和响应手势的命名空间。 所有手势的核心都是一个名为的特殊类 `Android.Gestures.GestureDetector` 。 顾名思义，此类将侦听操作系统所提供的笔势和事件 `MotionEvents` 。

若要实现笔势检测程序，活动必须实例化 `GestureDetector` 类并提供的实例 `IOnGestureListener` ，如以下代码片段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活动还必须实现 OnTouchEvent，并将 MotionEvent 传递到笔势检测程序。 下面的代码片段演示了一个示例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

当的实例 `GestureDetector` 标识感兴趣的手势时，它将通过引发事件或通过提供的回调通知活动或应用程序 `GestureDetector.IOnGestureListener` 。
此接口为各种手势提供六种方法：

- *OnDown* -当出现点击但未释放时调用。

- *OnFling* -当发生 fling 时调用，并在触发事件的开始和结束触控上提供数据。

- *OnLongPress* -在长按下时调用。

- *OnScroll* -发生滚动事件时调用。

- *OnShowPress* -在 OnDown 发生之后调用，并且尚未执行移动或释放事件。

- *OnSingleTapUp* -在出现单次点击时调用。

在许多情况下，应用程序可能只对笔势的子集感兴趣。 在这种情况下，应用程序应扩展类 GestureDetector，并覆盖与它们感兴趣的事件相对应的方法。

## <a name="custom-gestures"></a>自定义笔势

手势是用户与应用程序进行交互的好方法。 到目前为止，我们已了解到的 Api 足以满足简单的手势需求，但对于更复杂的手势，它们可能有点繁重。 为了帮助实现更复杂的手势，Android 提供了一组在 Android. 手势命名空间中的另一组 API，这将减轻与自定义手势关联的某些负担。

### <a name="creating-custom-gestures"></a>创建自定义手势

自 Android 1.6 起，Android SDK 附带了一个预装在模拟器上的应用程序（称为笔势生成器）。 此应用程序允许开发人员创建可嵌入到应用程序中的预定义手势。 以下屏幕截图显示了笔势生成器的示例：

[![笔势生成器的屏幕截图示例](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

可以 Google Play 找到此应用程序的改进版本。 手势工具非常类似于笔势生成器，只是它允许你在创建笔势后对其进行测试。 下面的屏幕截图显示了笔势生成器：

[![笔势工具和示例笔势的屏幕截图](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

手势工具更适用于创建自定义手势，因为它允许在创建笔势时测试笔势，并可通过 Google Play 轻松获得。

手势工具允许您通过在屏幕上绘制和分配名称来创建手势。 创建手势后，它们将保存在设备 SD 卡上的二进制文件中。 此文件需要从设备检索，然后将应用程序打包到文件夹/Resources/raw. 中。 可使用 Android Debug Bridge 从模拟器检索此文件。 下面的示例演示如何将文件从 Galaxy 结点复制到应用程序的资源目录：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

检索到文件后，必须将应用程序与目录/Resources/raw. 中的应用程序一起打包。 若要使用此手势文件，最简单的方法是将文件加载到 GestureLibrary 中，如以下代码片段所示：

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自定义手势

若要识别活动中的自定义手势，它必须将 GestureOverlay 对象添加到其布局。 下面的代码段演示如何以编程方式向活动添加 GestureOverlayView：

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

以下 XML 代码片段演示了如何以声明方式添加 GestureOverlayView：

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`具有多个事件，这些事件将在绘制笔势的过程中引发。 最感兴趣的事件是 `GesturePerformed` 。 当用户完成绘制其手势时，将引发此事件。

引发此事件时，活动会询问， `GestureLibrary` 尝试使用笔势工具创建的笔势之一来匹配笔势。 `GestureLibrary` 将返回预测对象的列表。

每个预测对象均包含中某个手势的分数和名称 `GestureLibrary` 。 分数越高，预测中指定的笔势与用户绘制的手势就越有可能。
一般而言，小于1.0 的分数被视为不太匹配。

下面的代码显示了一个匹配笔势的示例：

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

完成此操作后，你应该了解如何在 Xamarin Android 应用程序中使用触控和手势。 现在，让我们转到演练，查看正在运行的示例应用程序中的所有概念。

## <a name="related-links"></a>相关链接

- [Android Touch 开始 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最终 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)