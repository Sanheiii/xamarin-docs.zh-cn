---
title: 演练：在 Xamarin 中使用触控
description: 本文档介绍如何在 Xamarin iOS 应用程序中处理触控，并讨论示例触摸交互、笔势识别器和自定义笔势识别器。
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 3d296c36febfb5671c816372aa97661494179b83
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433899"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>演练：在 Xamarin 中使用触控

本演练演示如何编写响应不同类型触控事件的代码。 每个示例都包含在单独的屏幕中：

- [触摸示例](#Touch_Samples) –如何响应触控事件。
- [手势识别器示例](#Gesture_Recognizer_Samples) –如何使用内置的笔势识别器。
- [自定义手势识别器示例](#Custom_Gesture_Recognizer) –如何生成自定义手势识别器。

每节包含用于从头开始编写代码的说明。

按照以下说明将代码添加到情节提要，并了解 iOS 中可用的不同类型的触摸事件。 或者，打开 [已完成的示例](/samples/xamarin/ios-samples/applicationfundamentals-touch-final) 以查看一切正常运行。

<a name="Touch_Samples"></a>

## <a name="touch-samples"></a>触摸示例

在此示例中，我们将演示一些触控 Api。 按照以下步骤添加实现触控事件所需的代码：

1. 打开项目 **Touch_Start**。 首先运行项目以确保一切正常，并触摸 " **触摸示例** " 按钮。 你应看到类似于以下 (的屏幕，尽管任何按钮都不能) ：

    [![使用非工作按钮运行示例应用](ios-touch-walkthrough-images/image4.png)](ios-touch-walkthrough-images/image4.png#lightbox)

1. 编辑文件 **TouchViewController.cs** 并将以下两个实例变量添加到类 `TouchViewController` ：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```

1. 实现 `TouchesBegan` 方法，如以下代码所示：

    ```csharp
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);

        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);

        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```

    此方法的工作方式是检查某个 `UITouch` 对象，如果该对象存在，则根据触控发生的位置执行某些操作：

    - In _TouchImage_ –在 `Touches Began` 标签中显示文本并更改图像。
    - _内部 DoubleTouchImage_ –如果手势是双击，则更改显示的图像。
    - _DragImage 内_ –设置指示触摸已开始的标志。 方法 `TouchesMoved` 将使用此标志来确定是否 `DragImage` 应在屏幕上移动，如我们将在下一步中看到的那样。

    上述代码只涉及单个触摸，如果用户在屏幕上移动手指，则没有任何行为。 若要响应移动，请实现， `TouchesMoved` 如以下代码所示：

    ```csharp
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    此方法获取 `UITouch` 对象，然后检查以查看触控发生的位置。 如果在中发生了触摸 `TouchImage` ，则移动的文本触及会显示在屏幕上。

    如果 `touchStartedInside` 为 true，则我们知道用户的手指位于上， `DragImage` 并且正在四处移动。 当用户将鼠标指针移动到屏幕上时，代码将移动 `DragImage` 。

1. 如果用户将其手指从屏幕上移开，则需要处理事例，否则 iOS 会取消触控事件。 为此，我们将实现 `TouchesEnded` 和， `TouchesCancelled` 如下所示：

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);

        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }

    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```

    这两种方法都会将标志重置 `touchStartedInside` 为 false。 `TouchesEnded` 还将显示 `TouchesEnded` 在屏幕上。

1. 此时，"触摸示例" 屏幕已完成。 请注意，当你与每个图像交互时，屏幕会发生变化，如以下屏幕截图所示：

    [![正在启动应用程序屏幕](ios-touch-walkthrough-images/image4.png)](ios-touch-walkthrough-images/image4.png#lightbox)

    [![用户拖动按钮后的屏幕](ios-touch-walkthrough-images/image5.png)](ios-touch-walkthrough-images/image5.png#lightbox)

<a name="Gesture_Recognizer_Samples"></a>

## <a name="gesture-recognizer-samples"></a>手势识别器示例

[上一节](#Touch_Samples)演示了如何使用触控事件围绕屏幕拖动对象。
在本部分中，我们将消除触控事件，并演示如何使用以下手势识别器：

- `UIPanGestureRecognizer`用于在屏幕周围拖动图像的。
- `UITapGestureRecognizer`响应屏幕上双击的。

按照以下步骤实现手势识别器：

1. 编辑文件 **GestureViewController.cs** 并添加以下实例变量：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    我们需要此实例变量来跟踪映像的以前位置。
平移手势识别器将使用此 `originalImageFrame` 值来计算重绘屏幕上的图像所需的偏移量。

1. 将以下方法添加到控制器：

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();

        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined

        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    此代码 `UIPanGestureRecognizer` 将实例化实例，并将其添加到视图中。
请注意，我们以方法的形式将目标分配给该笔 `HandleDrag` –此方法是在下一步中提供的。

1. 若要实现 HandleDrag，请将以下代码添加到控制器：

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }

        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    上面的代码将首先检查手势识别器的状态，然后在屏幕上移动图像。 此代码准备就绪后，控制器现在就可以支持在屏幕上拖动一个图像。

1. 添加一个 `UITapGestureRecognizer` 将更改在 DoubleTouchImage 中显示的图像的。 将以下方法添加到 `GestureViewController` 控制器：

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;

        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));

            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };

        tapGesture = new UITapGestureRecognizer(action);

        // Configure it
        tapGesture.NumberOfTapsRequired = 2;

        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    此代码与的代码非常相似，而 `UIPanGestureRecognizer` 不是使用我们使用的目标的委托 `Action` 。

1. 我们需要做的最后一件事是修改， `ViewDidLoad` 以便它调用刚才添加的方法。 更改 ViewDidLoad，使其类似于以下代码：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        Title = "Gesture Recognizers";

        // Save initial state
        originalImageFrame = DragImage.Frame;

        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    请注意，我们初始化的值 `originalImageFrame` 。

1. 运行应用程序，并与两个图像交互。
以下屏幕截图是这些交互的一个示例：

    [![此屏幕截图显示了一个拖动交互](ios-touch-walkthrough-images/image7.png)](ios-touch-walkthrough-images/image7.png#lightbox)

<a name="Custom_Gesture_Recognizer"></a>

## <a name="custom-gesture-recognizer"></a>自定义手势识别器

在本部分中，我们将应用前面几节中的概念以生成自定义手势识别器。 自定义手势识别器将为子类 `UIGestureRecognizer` ，并识别用户在屏幕上绘制 "V"，然后切换位图。 下面的屏幕截图是此屏幕的一个示例：

 [![此应用将识别用户在屏幕上绘制 "V" 的时间](ios-touch-walkthrough-images/image8.png)](ios-touch-walkthrough-images/image8.png#lightbox)

按照以下步骤创建自定义手势识别器：

1. 向项目添加一个名为的新类 `CheckmarkGestureRecognizer` ，并使其类似于以下代码：

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;

    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion

            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();

                strokeUp = false;
                midpoint = CGPoint.Empty;
            }

            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);

                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }

                Console.WriteLine(base.State.ToString());
            }

            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }

            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }

                Console.WriteLine(base.State.ToString());
            }

            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);

                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);

                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }

                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    当 `State` 属性更改为或时，将调用 Reset 方法 `Recognized` `Ended` 。 这是重置自定义手势识别器中设置的任何内部状态的时间。
现在，每当用户与应用程序交互时，类就可以开始刷新，并准备重新尝试识别手势。

1. 现在，我们已经定义了自定义手势识别器 (`CheckmarkGestureRecognizer`) 编辑 **CustomGestureViewController.cs** 文件并添加以下两个实例变量：

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. 若要实例化和配置笔势识别器，请将以下方法添加到控制器：

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();

        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });

        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. 编辑 `ViewDidLoad` 以使其调用 `WireUpCheckmarkGestureRecognizer` ，如以下代码片段所示：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. 运行应用程序，并尝试在屏幕上绘制 "V"。 应会看到图像显示更改，如以下屏幕截图所示：

    [![已选中按钮](ios-touch-walkthrough-images/image9.png)](ios-touch-walkthrough-images/image9.png#lightbox)

    [![未选中按钮](ios-touch-walkthrough-images/image10.png)](ios-touch-walkthrough-images/image10.png#lightbox)

上述三个部分演示了在 iOS 中响应触控事件的不同方法：使用触控事件、内置笔势识别器或使用自定义笔势识别器。

## <a name="related-links"></a>相关链接

- [iOS 触摸最终 (示例) ](/samples/xamarin/ios-samples/applicationfundamentals-touch-final)