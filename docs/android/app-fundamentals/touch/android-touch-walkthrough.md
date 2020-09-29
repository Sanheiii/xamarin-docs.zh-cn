---
title: 演练-在 Android 中使用 Touch
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: b7db20c2d51e96de8fcadaf5d50627132946177c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455282"
---
# <a name="walkthrough---using-touch-in-android"></a>演练-在 Android 中使用 Touch

让我们看看如何使用工作应用程序的上一节中的概念。 我们会创建一个包含四个活动的应用程序。 第一个活动将是一个菜单或切换面板，将启动其他活动以演示各种 Api。 以下屏幕截图显示了主要活动：

[!["触摸我" 按钮的示例屏幕截图](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一个活动（Touch 示例）将演示如何使用事件处理程序来触摸视图。 手势识别器活动将演示如何对事件进行子类 `Android.View.Views` 处理并处理事件，并演示如何处理挤压手势。 第三个和最后一个活动（ **自定义笔势**）将显示如何使用自定义手势。 为了使内容更易于跟踪，我们将本演练分成几个部分，每个部分重点介绍其中一项活动。

## <a name="touch-sample-activity"></a>触摸示例活动

- 打开 **TouchWalkthrough \_ **项目。 **MainActivity**全部设置为，以 &ndash; 在活动中实现触摸行为。 如果运行应用程序，并单击 " **触摸示例**"，则以下活动将启动：

  [![具有触控的活动屏幕截图开始显示](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

- 现在，我们已确认活动已启动，打开文件 **TouchActivity.cs** 并添加的事件处理程序 `Touch` `ImageView` ：

  ```csharp
  _touchMeImageView.Touch += TouchMeImageViewOnTouch;
  ```

- 接下来，将以下方法添加到 **TouchActivity.cs**：

  ```csharp
  private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
  {
      string message;
      switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
      {
          case MotionEventActions.Down:
          case MotionEventActions.Move:
          message = "Touch Begins";
          break;

          case MotionEventActions.Up:
          message = "Touch Ends";
          break;

          default:
          message = string.Empty;
          break;
      }

      _touchInfoTextView.Text = message;
  }
  ```

请注意，在上面的代码中，我们将 `Move` 和 `Down` 操作视为相同。 这是因为，即使用户可能不会将其指尖抬起 `ImageView` ，它也可能会四处移动，或者用户可能会发生显然的压力。 这些类型的更改将生成一个 `Move` 操作。

用户每次触及时 `ImageView` ， `Touch` 都会引发事件，处理程序将在屏幕上显示消息 " **触摸** "，如以下屏幕截图所示：

[![具有触控的活动屏幕截图开始](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要用户接触 `ImageView` ， **Touch** 就会显示在中 `TextView` 。 当用户不再接触时 `ImageView` ，将在中显示消息 " **Touch 结束** " `TextView` ，如以下屏幕截图所示：

[![具有触控端的活动屏幕截图](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)

## <a name="gesture-recognizer-activity"></a>手势识别器活动

现在，让我们实现手势识别器活动。 此活动将演示如何在屏幕上拖动视图，并演示实现缩小到缩放的一种方法。

- 向应用程序中添加一个名为的新活动 `GestureRecognizer` 。
  编辑此活动的代码，使其类似于以下代码：

  ```csharp
  public class GestureRecognizerActivity : Activity
  {
      protected override void OnCreate(Bundle bundle)
      {
          base.OnCreate(bundle);
          View v = new GestureRecognizerView(this);
          SetContentView(v);
      }
  }
  ```

- 向项目中添加一个新的 Android 视图，并将其命名为 `GestureRecognizerView` 。 将以下变量添加到此类：

  ```csharp
  private static readonly int InvalidPointerId = -1;

  private readonly Drawable _icon;
  private readonly ScaleGestureDetector _scaleDetector;

  private int _activePointerId = InvalidPointerId;
  private float _lastTouchX;
  private float _lastTouchY;
  private float _posX;
  private float _posY;
  private float _scaleFactor = 1.0f;
  ```

- 将以下构造函数添加到 `GestureRecognizerView` 。 此构造函数将在 `ImageView` 活动中添加。 此时，代码仍将无法编译， &ndash; 我们需要创建 `MyScaleListener` 一个类，以便在 `ImageView` 用户 pinches 它时帮助调整大小：

  ```csharp
  public GestureRecognizerView(Context context): base(context, null, 0)
  {
      _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
      _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
      _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
  }
  ```

- 若要在活动中绘制图像，需要重写 `OnDraw` View 类的方法，如以下代码片段所示。 此代码会将移动 `ImageView` 到由和指定的位置， `_posX` 并 `_posY` 根据缩放系数调整图像的大小：

  ```csharp
  protected override void OnDraw(Canvas canvas)
  {
      base.OnDraw(canvas);
      canvas.Save();
      canvas.Translate(_posX, _posY);
      canvas.Scale(_scaleFactor, _scaleFactor);
      _icon.Draw(canvas);
      canvas.Restore();
  }
  ```

- 接下来，需要更新实例变量， `_scaleFactor` 因为用户 pinches `ImageView` 。 我们将添加一个名为的类 `MyScaleListener` 。 此类将侦听当用户 pinches 时将由 Android 引发的缩放事件 `ImageView` 。
  将以下内部类添加到 `GestureRecognizerView` 。 此类是一个 `ScaleGesture.SimpleOnScaleGestureListener` 。 此类是一个便利类，当你对笔势子集感兴趣时，侦听器可以为其划分子类：

  ```csharp
  private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
  {
      private readonly GestureRecognizerView _view;

      public MyScaleListener(GestureRecognizerView view)
      {
          _view = view;
      }

      public override bool OnScale(ScaleGestureDetector detector)
      {
          _view._scaleFactor *= detector.ScaleFactor;

          // put a limit on how small or big the image can get.
          if (_view._scaleFactor > 5.0f)
          {
              _view._scaleFactor = 5.0f;
          }
          if (_view._scaleFactor < 0.1f)
          {
              _view._scaleFactor = 0.1f;
          }

          _view.Invalidate();
          return true;
      }
  }
  ```

- 在中，我们需要替代的下一个方法 `GestureRecognizerView` 是 `OnTouchEvent` 。 下面的代码列出了此方法的完全实现。 这里有很多代码，让我们花点时间看一下这里的内容。 此方法所做的第一件事是，如有必要，可 &ndash; 通过调用来处理此操作 `_scaleDetector.OnTouchEvent` 。 接下来，我们将尝试确定调用此方法的操作：

  - 如果用户与屏幕接触，则会记录 X 和 Y 位置以及与屏幕接触的第一个指针的 ID。

  - 如果用户在屏幕上移动了触摸屏，则会确定用户移动指针的距离。

  - 如果用户已从屏幕上提起了手指，则会停止跟踪手势。

  ```csharp
  public override bool OnTouchEvent(MotionEvent ev)
  {
      _scaleDetector.OnTouchEvent(ev);

      MotionEventActions action = ev.Action & MotionEventActions.Mask;
      int pointerIndex;

      switch (action)
      {
          case MotionEventActions.Down:
          _lastTouchX = ev.GetX();
          _lastTouchY = ev.GetY();
          _activePointerId = ev.GetPointerId(0);
          break;

          case MotionEventActions.Move:
          pointerIndex = ev.FindPointerIndex(_activePointerId);
          float x = ev.GetX(pointerIndex);
          float y = ev.GetY(pointerIndex);
          if (!_scaleDetector.IsInProgress)
          {
              // Only move the ScaleGestureDetector isn't already processing a gesture.
              float deltaX = x - _lastTouchX;
              float deltaY = y - _lastTouchY;
              _posX += deltaX;
              _posY += deltaY;
              Invalidate();
          }

          _lastTouchX = x;
          _lastTouchY = y;
          break;

          case MotionEventActions.Up:
          case MotionEventActions.Cancel:
          // We no longer need to keep track of the active pointer.
          _activePointerId = InvalidPointerId;
          break;

          case MotionEventActions.PointerUp:
          // check to make sure that the pointer that went up is for the gesture we're tracking.
          pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
          int pointerId = ev.GetPointerId(pointerIndex);
          if (pointerId == _activePointerId)
          {
              // This was our active pointer going up. Choose a new
              // action pointer and adjust accordingly
              int newPointerIndex = pointerIndex == 0 ? 1 : 0;
              _lastTouchX = ev.GetX(newPointerIndex);
              _lastTouchY = ev.GetY(newPointerIndex);
              _activePointerId = ev.GetPointerId(newPointerIndex);
          }
          break;

      }
      return true;
  }
  ```

- 现在，运行该应用程序，并启动手势识别器活动。
  当它启动时，屏幕应类似于下面的屏幕截图：

  [![带有 Android 图标的笔势识别器启动屏幕](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

- 现在触摸图标，并将其拖动到屏幕上。 尝试 "缩小到缩放" 手势。 某些时候，屏幕可能类似于以下屏幕截图所示：

  [![屏幕周围的手势移动图标](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此时，你应该为自己的平台创建一个 pat：你刚在 Android 应用程序中实现了大规模缩放！ 进行快速中断，并 &ndash; 使用自定义手势在本演练中继续学习第三个和最后一个活动。

## <a name="custom-gesture-activity"></a>自定义手势活动

本演练中的最后一个屏幕将使用自定义手势。

出于本演练的目的，已使用手势工具创建了手势库，并将其添加到了文件 **资源/原始/笔势**中。 通过这种方式，让我们在演练中获得最后一个活动。

- 将名为 " **自定义 \_ 笔势 \_ main.axml** " 的布局文件添加到项目中，其内容如下。 该项目已包含 **Resources** 文件夹中的所有映像：

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="vertical"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
      <LinearLayout
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <ImageView
          android:src="@drawable/check_me"
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="3"
          android:id="@+id/imageView1"
          android:layout_gravity="center_vertical" />
      <LinearLayout
          android:layout_width="match_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
  </LinearLayout>
  ```

- 接下来，将新活动添加到项目并将其命名为 `CustomGestureRecognizerActivity.cs` 。 将两个实例变量添加到类，如以下两行代码所示：

  ```csharp
  private GestureLibrary _gestureLibrary;
  private ImageView _imageView;
  ```

- 编辑 `OnCreate` 此活动的方法，使其类似于以下代码。 在此代码中，我们需要一分钟的时间来解释。 我们要做的第一件事是实例化 `GestureOverlayView` 并将其设置为活动的根视图。
  还会为的事件分配事件处理程序 `GesturePerformed` `GestureOverlayView` 。 接下来，将显示先前创建的布局文件，并将其添加为的子视图 `GestureOverlayView` 。 最后一步是初始化变量 `_gestureLibrary` ，并从应用程序资源加载笔势文件。 如果由于某种原因无法加载笔势文件，则此活动没有太多可执行的操作，因此它会关闭：

  ```csharp
  protected override void OnCreate(Bundle bundle)
  {
      base.OnCreate(bundle);

      GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
      SetContentView(gestureOverlayView);
      gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

      View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
      _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
      gestureOverlayView.AddView(view);

      _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
      if (!_gestureLibrary.Load())
      {
          Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
          Finish();
      }
  }
  ```

- 首先，我们需要实现方法， `GestureOverlayViewOnGesturePerformed` 如以下代码片段所示。 当 `GestureOverlayView` 检测到手势时，它将回调到此方法。 首先，我们尝试通过调用来获取 `IList<Prediction>` 与该笔势匹配的对象 `_gestureLibrary.Recognize()` 。 我们使用一些 LINQ 获取 `Prediction` 具有该笔势的最高分的。

  如果没有足够多的匹配笔势，则不执行任何操作，事件处理程序将退出。 否则，检查预测的名称，并根据该笔势的名称更改要显示的图像：

  ```csharp
  private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
  {
      IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
      orderby p.Score descending
      where p.Score > 1.0
      select p;
      Prediction prediction = predictions.FirstOrDefault();

      if (prediction == null)
      {
          Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
          return;
      }

      Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

      if (prediction.Name.StartsWith("checkmark"))
      {
          _imageView.SetImageResource(Resource.Drawable.checked_me);
      }
      else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
      {
          // Match one of our "erase" gestures
          _imageView.SetImageResource(Resource.Drawable.check_me);
      }
  }
  ```

- 运行应用程序，并启动自定义手势识别器活动。 它应类似于以下屏幕截图：

  [![带有签入图像的屏幕截图](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

  现在，在屏幕上绘制一个复选标记，显示的位图应类似于下一屏幕截图所示：

  [![已绘制选中标记，已识别标记](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

  最后，在屏幕上绘制一个自由曲线。 此复选框应改回其原始图像，如以下屏幕截图所示：

  [![屏幕上的自由曲线，显示原始图像](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

现在，你已了解如何使用 Xamarin 在 Android 应用程序中集成触控和手势。

## <a name="related-links"></a>相关链接

- [Android Touch 开始 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最终 (示例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)