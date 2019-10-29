---
title: Xamarin 中的 SpriteKit
description: 本文档介绍了与 SceneKit 集成的 SpriteKit 和 Apple 2D 图形框架，其中包含了物理学和动画，其中包括对照明和着色等的支持。 SpriteKit 可用于创建2D 游戏。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: e8829211ebf06eea224eade3f1b9d836207cdd64
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032470"
---
# <a name="spritekit-in-xamarinios"></a>Xamarin 中的 SpriteKit

SpriteKit 是 Apple 的2D 图形框架，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 其中包括与 SceneKit、着色器支持、照明、阴影、约束、正常地图生成和物理增强功能的集成。 特别是，新的物理学功能使向游戏添加逼真效果变得非常简单。

## <a name="physics-bodies"></a>物理学主体

SpriteKit 包括一个二维的硬正文物理 API。 每个 sprite 都有一个关联的物理主体（`SKPhysicsBody`），用于定义物理属性（如质量和摩擦）以及物理世界中主体的几何。

## <a name="creating-a-physics-body-from-a-texture"></a>基于纹理创建物理主体
SpriteKit 现在支持从其纹理中派生子画面的物理主体。 这样就可以轻松地实现外观更自然的冲突。

例如，在以下冲突中，请注意香蕉和猴子如何在每个图像的图面上发生冲突：

![](spritekit-images/image13.png "The banana and monkey collide nearly at the surface of each image")

SpriteKit 使使用一行代码就能创建这样的物理体。 只需调用带有纹理和大小的 `SKPhysicsBody.Create`：子画面。PhysicsBody = SKPhysicsBody （sprite。纹理、sprite。大小）;

## <a name="alpha-threshold"></a>Alpha 阈值

除了直接将 `PhysicsBody` 属性直接设置到从纹理派生的几何以外，应用程序还可以设置和 alpha 阈值来控制如何派生几何。 

Alpha 阈值定义要在生成的物理主体中包含的像素必须包含的最小 alpha 值。 例如，以下代码会产生略微不同的物理主体：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

调整 alpha 阈值（如这种方式）的效果会微调上一个冲突，以便在与香蕉发生冲突时，猴子会下降：

![](spritekit-images/image14.png "The monkey falls over when colliding with the banana")

## <a name="physics-fields"></a>物理字段

SpriteKit 的另一个极佳补充是新的物理领域支持。 利用这些功能，您可以将 vortex 字段、径向重力字段和弹簧字段等一些东西命名为几个。

物理学字段是使用 SKFieldNode 类创建的，它与任何其他 `SKNode`一样添加到场景中。 `SKFieldNode` 上有多种用于创建不同物理字段的工厂方法。 可以通过调用 `SKFieldNode.CreateRadialGravityField()`等 `SKFieldNode.CreateSpringField()`、径向重力字段来创建弹簧字段。

`SKFieldNode` 还具有属性来控制字段属性，例如字段强度、字段区域和字段强制的衰减。

## <a name="spring-field"></a>弹簧字段

例如，下面的代码创建一个弹簧字段并将其添加到场景中：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

然后，可以添加子画面并设置其 `PhysicsBody` 属性，使物理学字段影响 sprite，如以下代码在用户触摸屏幕时执行操作：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

这会导致 bananas 有，如字段节点周围的弹簧：

![](spritekit-images/image15.png "The bananas oscillate like a spring around the field node")

## <a name="radial-gravity-field"></a>径向重力字段

添加不同的字段类似。 例如，以下代码将创建一个径向重力字段：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

这会产生一个不同的 "强制" 字段，其中 bananas 将 radially 有关字段的请求：

![](spritekit-images/image16.png "The bananas are pulled radially around the field")
