---
title: Xamarin 中的 iOS 游戏 Api
description: 本文介绍 iOS 9 提供的新游戏增强功能，这些增强功能可用于改进 Xamarin iOS 游戏的图形和音频功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 20efcf1af10b7c1d3d36e570bc838e396241ffee
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436249"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>Xamarin 中的 iOS 游戏 Api

_本文介绍 iOS 9 提供的新游戏增强功能，这些增强功能可用于改进 Xamarin iOS 游戏的图形和音频功能。_

Apple 在 iOS 9 中对游戏 Api 做出了多项技术改进，使 Xamarin iOS 应用中的游戏图形和音频更容易实现。
其中包括通过高级框架的轻松开发，并利用 iOS 设备 GPU 的强大功能提高速度和图形性能。

[![运行群的应用的示例](images/flocking01.png)](images/flocking01.png#lightbox)

这包括 GameplayKit、ReplayKit、Model i/o、MetalKit 和金属绩效着色器，以及金属、SceneKit 和 SpriteKit 的新增功能和增强功能。

本文介绍了通过 iOS 9 的新游戏增强功能提高 Xamarin iOS 游戏的所有方法：

## <a name="introducing-gameplaykit"></a>GameplayKit 简介

Apple 的 new GameplayKit framework 提供了一组技术，使你可以通过减少实现所需的重复的通用代码量来轻松创建适用于 iOS 设备的游戏。 GameplayKit 提供了用于开发游戏机制的工具，然后可以轻松地将这些工具与图形 (引擎（如 SceneKit 或 SpriteKit) ）结合使用，以便快速交付已完成的游戏。

GameplayKit 包括几个常见的游戏播放算法，例如：

- 基于行为的代理模拟，可用于定义 AI 将自动实现的移动和目标。
- 用于基于车的游戏的 minmax 人工智能。
- 用于提供紧急行为的数据驱动游戏逻辑规则系统，具有模糊推理。

此外，GameplayKit 通过使用提供以下功能的模块化体系结构，对游戏开发采用了构造块方法：

- 用于处理游戏中基于过程的复杂系统代码的状态机。
- 用于提供随机游戏游戏和不可预测性的工具，而不会引起调试问题。
- 基于组件的可重复使用的体系结构。

若要了解有关 GameplayKit 的详细信息，请参阅 Apple 的 [GameplayKit 编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) 和 [GameplayKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 示例

让我们快速了解如何使用游戏游戏工具包在 Xamarin iOS 应用程序中实现一些简单的游戏游戏机制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是游戏的 AI 元素在游戏板上查找其方式的功能。
例如，一位2D 敌人通过射击地形的一个迷宫或3D 字符来寻找其方式。

请看下面的地图：

[![Pathfinding 映射示例](images/gkpathfindpath.png)](images/gkpathfindpath.png#lightbox)

使用 pathfinding 此 c # 代码可通过以下方式查找：

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>经典专家系统

以下 c # 代码片段演示了如何使用 GameplayKit 来实现传统专家系统：

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

基于一组给定的规则 (`GKRule`) 和一组已知输入，专家系统 (`GKRuleSystem`) 将 `fizzbuzz` 为上面 (的示例创建可预测的输出) 。

### <a name="flocking"></a>群

群允许一组 AI 控制的游戏实体表现为 flock，其中，该组响应潜在顾客实体的运动和动作，如航班或鱼游泳的一 flock。

以下 c # 代码片段使用 GameplayKit 和 SpriteKit 来实现图形显示的群行为：

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;

        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }

        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

接下来，在视图控制器中实现此场景：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

运行时，"Boids" 这一小动画的 _""_ 将围绕指尖 flock：

[![轻微的动画 Boids 将围绕指尖 flock](images/flocking01.png)](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 示例

除了上面提供的示例，Apple 还提供了以下可转码到 c # 和 Xamarin 的示例应用：

- [FourInARow：对对手 AI 使用 GameplayKit Minmax 战略家](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog：在 GameplayKit 中使用代理系统](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots：使用 SpriteKit 和 GameplayKit 构建跨平台游戏](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>金属

在 iOS 9 中，Apple 已对金属进行了几项更改和添加，以提供对 GPU 的低开销访问。 使用金属可以最大程度地提高 iOS 应用的图形和计算能力。

该金属框架包含以下新功能：

- 适用于 OS X 的新专用和深度模具纹理。
- 通过深度钳位和独立的前端和后端子模具值提高了阴影质量。
- 金属着色语言和金属标准库改进。
- 计算着色器支持更大范围的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit 框架

MetalKit 框架提供了一组实用工具类和功能，可减少在 iOS 应用中使用金属所需的工作量。 MetalKit 提供三个关键领域的支持：

1. 从各种源（包括 PNG、JPEG、KTX 和 PVR 等常用格式）加载异步纹理。
2. 可以轻松访问基于特定于金属模型处理的基于模型 i/o 的资产。 已对这些功能进行了高度优化，可在模型 i/o 网格和金属缓冲之间提供高效的数据传输。
3. 预定义的金属视图和视图管理，大大减少了在 iOS 应用中显示图形呈现所需的代码量。

若要了解有关 MetalKit 的详细信息，请参阅 Apple 的 [MetalKit 框架参考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)、 [金属编程指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)、 [金属框架参考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) 和 [金属着色语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>金属绩效着色器框架

金属性能着色器框架提供了一组高度优化的图形和基于计算的着色器，以便在基于金属的 iOS 应用中使用。 金属性能着色器框架中的每个着色器已经过专门调整，以便在金属支持的 iOS Gpu 上提供高性能。

通过使用金属性能着色器类，您可以实现每个特定 iOS GPU 上的最高性能，而无需面向和维护单独的基本代码。 金属绩效着色器可用于任何金属资源，如纹理和缓冲区。

金属性能着色器框架提供一组常见着色器，例如：

- **高斯模糊** (`MPSImageGaussianBlur`) 
- **Sobel Edge 检测** (`MPSImageSobel`) 
- **图像直方图** (`MPSImageHistogram`) 

有关详细信息，请参阅 Apple 的 [金属着色语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>模型 i/o 简介

Apple 的模型 i/o 框架可深入了解3D 资产 (如模型及其相关资源) 。 模型 i/o 为你的 iOS 游戏提供基于物理的材料、型号和照明，可与 GameplayKit、金属和 SceneKit 一起使用。

对于模型 i/o，可以支持以下类型的任务：

- 从各种流行软件和游戏引擎格式导入光源、材料、网格数据、照相机设置和其他基于场景的信息。
- 处理或生成基于场景的信息，例如创建过程纹理的天空 domes 或制作照明入网格。
- 与 MetalKit、SceneKit 和 GLKit 一起使用，可有效地将游戏资产加载到 GPU 缓冲区以供渲染。
- 将基于场景的信息导出为各种常用的软件和游戏引擎格式。

若要了解有关模型 i/o 的详细信息，请参阅 Apple 的 [模型 I/o 框架参考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>ReplayKit 简介

Apple 的 new ReplayKit framework 使你可以轻松地将游戏录制的记录添加到 iOS 游戏，并允许用户在应用中快速轻松地编辑和共享此视频。

有关详细信息，请参阅 Apple [使用 ReplayKit 和 Game Center 视频](https://developer.apple.com/videos/wwdc/2015/?id=605) 及其 [DemoBots：使用 SpriteKit 和 GameplayKit 示例应用构建跨平台游戏](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) 。

## <a name="scenekit"></a>SceneKit

场景工具包是一个3D 场景图形 API，可简化使用三维图形的操作。 它是在 OS X 10.8 中首次引入的，现已推出 iOS 8。 利用场景工具包创建沉浸式三维可视化效果和休闲三维游戏，不需要在 OpenGL 中提供专业知识。 场景工具包是在常见场景图概念上构建的，它可以简化 OpenGL 和 OpenGL ES 的复杂性，使你可以轻松地将3D 内容添加到应用程序。 但是，如果您是 OpenGL 专家，则场景工具包也有很大的支持直接与 OpenGL 结合使用。 它还包括许多补充3D 图形的功能，如物理学，并与多个其他 Apple 框架（如核心动画、核心图像和 Sprite 工具包）紧密集成。

有关详细信息，请参阅我们的 [SceneKit](~/ios/platform/gaming/scenekit.md) 文档。

### <a name="scenekit-changes"></a>SceneKit 更改

Apple 向 SceneKit for iOS 9 添加了以下新功能：

- Xcode 现在提供了一个场景编辑器，通过它可以直接从 Xcode 内编辑场景来快速生成游戏和交互式3D 应用。
- `SCNView`和 `SCNSceneRenderer` 类可用于在支持的 iOS 设备上启用金属渲染 () 。
- `SCNAudioPlayer`和 `SCNNode` 类可用于添加可自动跟踪到 iOS 应用程序的播放机位置的空间音频效果。

有关详细信息，请参阅 [SceneKit 文档](~/ios/platform/introduction-to-ios8.md#scenekit) 和 Apple [SceneKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) 和 [Fox：使用 Xcode 场景编辑器示例项目构建 SceneKit 游戏](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) 。

## <a name="spritekit"></a>SpriteKit

与 Apple 的2D 游戏框架一样，动画工具箱在 iOS 8 和 OS X Yosemite 中提供了一些有趣的新功能。 其中包括与场景工具包、着色器支持、照明、阴影、约束、正常地图生成和物理增强功能的集成。 特别是，新的物理学功能使向游戏添加逼真效果变得非常简单。

有关详细信息，请参阅我们的 [SpriteKit](~/ios/platform/gaming/spritekit.md) 文档。

### <a name="spritekit-changes"></a>SpriteKit 更改

Apple 向 SpriteKit for iOS 9 添加了以下新功能：

- 空间音频效果，可自动跟踪播放机与类的位置 `SKAudioNode` 。
- Xcode 现在使用场景编辑器和操作编辑器来实现2D 游戏和应用创建。
- 利用新的相机节点 () 对象的简单滚动游戏支持 `SKCameraNode` 。
- 在支持金属的 iOS 设备上，SpriteKit 会自动将其用于渲染，即使您已使用自定义 OpenGL ES 着色器也是如此。

有关详细信息，请参阅 [SpriteKit 文档](~/ios/platform/introduction-to-ios8.md#spritekit) Apple [SpriteKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) 及其 [DemoBots：使用 SpriteKit 和 GameplayKit 示例应用构建跨平台游戏](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) 。

## <a name="summary"></a>总结

本文介绍了 iOS 9 为你的 Xamarin iOS 应用提供的新游戏功能。
它引入了 GameplayKit 和模型 i/o;金属的主要增强功能;和 SceneKit 和 SpriteKit 的新功能。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)