---
title: iOS 7 简介
description: 本文介绍了 iOS 7 中引入的主要新 Api，包括查看控制器转换、UIView 动画的增强功能、UIKit Dynamics 和文本工具包。 还介绍了对用户界面的一些更改，并介绍了新的增强多任务功能。
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9f27fa550bb21f4261288e8a9a3d809a0ccc7de2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436502"
---
# <a name="introduction-to-ios-7"></a>iOS 7 简介

_本文介绍了 iOS 7 中引入的主要新 Api，包括查看控制器转换、UIView 动画的增强功能、UIKit Dynamics 和文本工具包。还介绍了对用户界面的一些更改，并介绍了新的增强多任务功能。_

iOS 7 是对 iOS 的重大更新。 它引入了一个全新的用户界面设计，该设计将重点放在内容上，而不是应用程序镶边上。 除了视觉变化外，iOS 7 还添加了很多的新 Api，以创建更丰富的交互和体验。 本文档调查 iOS 7 引入的新技术，并作为进一步探索的起点。

## <a name="uiview-animation-enhancements"></a>UIView 动画增强功能

iOS 7 增强了 UIKit 中的动画支持，使应用程序可以执行以前需要直接放置在核心动画框架中的操作。 例如， `UIView` 现在可以执行弹簧动画和关键帧动画，这些动画先前应用于 `CAKeyframeAnimation` `CALayer` 。

### <a name="spring-animations"></a>弹簧动画

 `UIView` 现在支持使用弹簧效果对属性更改进行动画处理。 若要添加此方法，请调用 `AnimateNotify` 或 `AnimateNotifyAsync` 方法，并传入弹簧阻尼比率和初始弹簧速度的值，如下所述：

- `springWithDampingRatio` –一个介于0和1之间的值，其中，振荡为较小的值增加。
- `initialSpringVelocity` –初始弹簧速度，以每秒的总动画距离的百分比表示。

以下代码将在图像视图的中心更改时产生弹簧效果：

```csharp
void AnimateWithSpring ()
{
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;

    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {

        imageView.Center = new CGPoint (imageView.Center.X, 400);

    }, null);
}
```

这一弹簧效果会使图像视图在向新的中心位置完成动画时出现弹跳，如下所示：

 ![这一弹簧效果会使图像视图显示为在其将动画完成到新中心位置时弹跳](images/spring-animation.png)

### <a name="keyframe-animations"></a>关键帧动画

`UIView`类现在包含 `AnimateWithKeyframes` 用于在上创建关键帧动画的方法 `UIView` 。 此方法类似于其他 `UIView` 动画方法，不同之处在于将附加 `NSAction` 作为参数传递以包含关键帧。 在中 `NSAction` ，通过调用来添加关键帧 `UIView.AddKeyframeWithRelativeStartTime` 。

例如，以下代码片段将创建一个关键帧动画来对视图中心进行动画处理，并旋转视图：

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => {
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => {
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

方法的前两个参数 `AddKeyframeWithRelativeStartTime` 分别指定关键帧的开始时间和持续时间（以占总体动画长度的百分比表示）。 上面的示例将在第一秒中将图像视图动画处理到其新中心，然后在下一秒旋转90度。 由于动画将指定 `UIViewKeyframeAnimationOptions.Autoreverse` 为一个选项，因此，这两个关键帧也会反向动画。 最后，最后一个值设置为完成处理程序中的初始状态。

下面的屏幕截图演示了通过关键帧的组合动画：

 ![此屏幕快照演示了通过关键帧的组合动画](images/keyframes.png)

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是 UIKit 中一组新的 Api，允许应用程序基于物理学创建动画交互。 UIKit Dynamics 封装了2D 物理学引擎来实现这一点。

API 本质上是声明性的。 可以通过创建对象（称为 *行为* ）来声明物理学交互的行为方式，以表示物理概念，如重力、碰撞、弹簧等。然后，将)  (的行为附加到另一个对象，该对象称为 " *动态 animator*"，该对象封装视图。 动态 animator 需要将声明的物理行为应用于 *动态项* -实现的项（ `IUIDynamicItem` 例如） `UIView` 。

有几种不同的基元行为可用于触发复杂的交互，包括：

- `UIAttachmentBehavior` –附加两个动态项，以便将它们一起移动，或将动态项附加到连接点。
- `UICollisionBehavior` –允许动态项参与冲突。
- `UIDynamicItemBehavior` –指定要应用于动态项（如弹性、密度和摩擦）的一组常规属性。
- `UIGravityBehavior` -对动态项应用重力，使项在 gravitational 方向加速。
- `UIPushBehavior` –对动态项应用强制。
- `UISnapBehavior` –允许动态项与弹簧效果对齐位置。

虽然有许多基元，但使用 UIKit Dynamics 向视图中添加基于物理的交互的一般过程在行为上保持一致：

1. 创建动态 animator。
1. 创建)  (行为。
1. 向动态 animator 中添加行为。

### <a name="dynamics-example"></a>Dynamics 示例

我们来看一个示例，该示例将重心和冲突边界添加到 `UIView` 。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

向图像视图添加重力后，请按照上面所述的3个步骤进行操作。

在此示例中，我们将在方法中工作 `ViewDidLoad` 。 首先，添加一个 `UIImageView` 实例，如下所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

这将创建一个在屏幕上边缘中间的图像视图。 若要使映像 "秋季" 使用重力，请创建的实例 `UIDynamicAnimator` ：

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`采用引用 `UIView` 或的实例 `UICollectionViewLayout` ，该实例包含将按附加行为进行动画处理 () 的项。

接下来，创建一个 `UIGravityBehavior` 实例。 可以传递一个或多个实现的对象 `IUIDynamicItem` ，如下所示 `UIView` ：

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

向传递的是一个数组 `IUIDynamicItem` ，在此示例中，该数组包含 `UIImageView` 要进行动画处理的单个实例。

最后，将行为添加到动态 animator：

```csharp
dynAnimator.AddBehavior (gravity);
```

这会使图像与重力向下动画，如下所示：

![](images/gravity2.png)
 ![ 结束图像位置的起始图像位置](images/gravity3.png)

由于屏幕边界没有任何限制，因此图像视图只是底部。 若要约束视图以便图像与屏幕边缘冲突，可以添加 `UICollisionBehavior` 。 下一节将介绍这一点。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

首先，我们将创建一个 `UICollisionBehavior` 并将其添加到动态 animator，就像我们对进行的操作一样 `UIGravityBehavior` 。

修改代码以包括 `UICollisionBehavior` ：

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`具有一个名为的属性 `TranslatesReferenceBoundsIntoBoundry` 。 如果将此选项设置为， `true` 则会将引用视图的边界用作冲突边界。

现在，当图像与重力向下动画动画时，它会在屏幕底部上弹跳，然后在屏幕上进行静止。

<!--, as shown below:

 ![Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there](images/bounce.png)-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我们可以进一步控制正在处理的图像视图的行为，并提供其他行为。 例如，我们可以添加 `UIDynamicItemBehavior` 来增加弹性，从而使图像视图在与屏幕底部冲突时更多地跳动。

添加与 `UIDynamicItemBehavior` 其他行为相同的步骤。 首先创建行为：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然后，将行为添加到动态 animator：

 `dynAnimator.AddBehavior (dynBehavior);`

此行为到位后，图像视图在与边界冲突时将会弹跳更多。

## <a name="general-user-interface-changes"></a>常规用户界面更改

除了上面所述的新的 UIKit Api （如 UIKit Dynamics、控制器转换和增强的 UIView 动画）之外，iOS 7 还引入了对 UI 的各种视觉更改，以及各种视图和控件的相关 API 更改。 有关详细信息，请参阅 [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文本工具包

文本工具包是一个提供强大文本布局和呈现功能的新 API。 它构建在低级别核心文本框架之上，但比核心文本更易于使用。

有关详细信息，请参阅我们的 [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多任务

iOS 7 会在执行后台工作时进行更改。 当任务在后台运行时，iOS 7 中的任务完成不会再使应用程序保持唤醒状态，并且应用程序将以不间断的方式唤醒后台处理。 iOS 7 还添加了三个新的 Api，用于在后台用新内容更新应用程序：

- 后台提取–允许应用程序定期更新后台内容。
- 远程通知-允许应用程序在收到推送通知时更新内容。 通知可以是静默的，也可以是在锁定屏幕上显示横幅。
- 后台传输服务–允许上传和下载数据（如大型文件），而无需经过固定的时间限制。

有关新的多任务功能的更多详细信息，请参阅 Xamarin [后台处理 guide](~/ios/app-fundamentals/backgrounding/index.md)的 iOS 部分。

## <a name="summary"></a>总结

本文介绍 iOS 的几项重大新增内容。 首先，它演示如何将自定义转换添加到视图控制器。 然后，它演示了如何在集合视图中使用从导航控制器中的转换，以及在集合视图之间交互使用转换。 接下来，它引入了对 UIView 动画的几项增强功能，显示应用程序如何使用 UIKit 来实现以前需要直接对核心动画进行编程的内容。 最后，引入了新的 UIKit Dynamics API，该 API 与 UIKit 的物理引擎一起引入，现在可在文本工具包框架中使用丰富文本支持。

## <a name="related-links"></a>相关链接

- [IOS 7 (示例) 简介 ](/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)