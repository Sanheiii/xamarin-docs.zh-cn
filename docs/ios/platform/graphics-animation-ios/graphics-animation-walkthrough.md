---
title: 在 Xamarin 中使用核心图形和核心动画
description: 本文分步介绍如何创建使用核心图形和核心动画的应用程序。 它演示了如何在屏幕上绘制响应用户触摸，以及如何对图像进行动画处理以便沿着路径移动。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1245531d68e43c833e892fe888830decc9d07c85
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430599"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>在 Xamarin 中使用核心图形和核心动画

对于本演练，我们将使用核心图形绘制路径，以响应触控输入。 接下来，我们将添加一个 `CALayer` 包含将沿着路径进行动画处理的图像的。

以下屏幕截图显示已完成的应用程序：

![已完成的应用程序](graphics-animation-walkthrough-images/00-final-app.png)

在开始之前，请下载本指南附带的 *GraphicsDemo* 示例。 可在 [此处](/samples/xamarin/ios-samples/graphicsandanimation) 下载该程序，它位于 **GraphicsWalkthrough** 目录中，通过双击来启动名为 **GraphicsDemo_starter** 的项目，然后打开 `DemoView` 该类。

## <a name="drawing-a-path"></a>绘制路径

1. 在中 `DemoView` `CGPath` ，将变量添加到类，并在构造函数中对其进行实例化。 还声明了两个 `CGPoint` 变量， `initialPoint` 和 `latestPoint` ，我们将使用这些变量来捕获构造路径时使用的触摸点：

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. 添加以下 using 指令：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下来，重写 `TouchesBegan` 并 `TouchesMoved,` 添加以下实现以便分别捕获初始 touch 点和每个后续触摸点：

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` 每次触控移动时都将调用，以便 `Draw` 在下一次运行循环时调用。

4. 我们将在方法中向路径添加行 `Draw` 并使用一条红虚线来绘制。 [实现 `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md)，代码如下所示：

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

如果现在运行应用程序，我们可以触摸屏幕上的绘图，如以下屏幕截图所示：

![在屏幕上绘制](graphics-animation-walkthrough-images/01-path.png)

## <a name="animating-along-a-path"></a>沿着路径进行动画处理

现在，我们已经实现了代码以允许用户绘制路径，接下来，让我们添加代码，将层沿绘制路径进行动画处理。

1. 首先，将一个 [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) 变量添加到类，并在构造函数中创建它：

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. 接下来，当用户从屏幕上提起手指时，会将该层添加为视图层的子层。 接下来，我们将使用路径创建一个关键帧动画，并对层的进行动画处理 `Position` 。

    若要实现此目的，我们需要重写 `TouchesEnded` 并添加以下代码：

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. 立即运行该应用程序，在绘图后，将添加包含图像的层，并沿绘制路径进行传播：

![添加了具有图像的层，并沿绘图路径进行传播](graphics-animation-walkthrough-images/00-final-app.png)

## <a name="summary"></a>总结

本文逐步介绍如何将图形和动画概念相互关联起来。 首先，我们介绍了如何使用核心图形在中绘制路径，以 `UIView` 响应用户触摸。 然后，我们介绍了如何使用核心动画使图像沿该路径行进。

## <a name="related-links"></a>相关链接

- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心动画食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)