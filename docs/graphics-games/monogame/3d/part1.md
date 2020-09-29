---
title: 使用模型类
description: 与用于着色3D 图形的传统方法相比，模型类可以极大地简化呈现复杂三维对象。 模型对象是从内容文件创建的，这样就可以轻松地集成内容，而无需自定义代码。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 06d585e976ce41080682f531b9661ff06cedaebe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437015"
---
# <a name="using-the-model-class"></a>使用模型类

_与用于着色3D 图形的传统方法相比，模型类可以极大地简化呈现复杂三维对象。模型对象是从内容文件创建的，这样就可以轻松地集成内容，而无需自定义代码。_

MonoGame API 包括一个 `Model` 类，该类可用于存储从内容文件加载的数据，以及用于执行呈现。 模型文件可能是非常简单的 (例如，实心三角形) ，也可能包含复杂渲染（包括纹理和照明）的信息。

本演练使用 [自动装置的3d 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) 并涵盖以下内容：

- 启动新的游戏项目
- 为模型及其纹理创建 XNBs
- 将 XNBs 包含在游戏项目中
- 绘制3D 模型
- 绘制多个模型

完成后，项目将如下所示：

![已完成示例，显示六个机器人](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>创建空游戏项目

我们需要设置一个名为 MonoGame3D 的游戏项目。 有关创建新的 MonoGame 项目的信息，请参阅 [有关创建跨平台 MonoGame 项目的此演练](~/graphics-games/monogame/introduction/part1.md)。

在继续进行之前，应验证项目是否已正确打开和部署。 部署完成后，我们会看到一个空的蓝色屏幕：

![黑屏蓝游戏屏幕](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>将 XNBs 包含在游戏项目中

Xnb 文件格式是 (内容的标准扩展，已通过 [MonoGame 管道工具](http://www.monogame.net/documentation/?page=Pipeline)) 创建。 所有生成的内容都有 (的源文件，在我们的) 模型中，该文件是一个 fbx 文件， (xnb 文件) 的目标文件。 Fbx 格式是可由应用程序（如 [Maya](https://www.autodesk.com/products/maya/overview) 和 [Blender](https://www.blender.org/)）创建的常见三维模型格式。 

`Model`可以通过从包含3d 几何数据的磁盘加载 xnb 文件来构造此类。   此 xnb 文件是通过内容项目创建的。 Monogame 模板会自动包含内容项目 (，其 mgcp) 在内容文件夹中。 有关 MonoGame 管道工具的详细讨论，请参阅 [内容管道指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

在本指南中，我们将跳过使用 MonoGame 管道工具，并使用。此处包含 XNB 文件。 请注意，。XNB 文件在每个平台上都不同，因此，请务必为你使用的任何平台使用正确的 XNB 文件集。

我们将解压缩 [Content.zip 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) ，以便我们可以在游戏中使用包含的 xnb 文件。 如果使用的是 Android 项目，请在**WalkingGame**项目中右键单击 "**资产**" 文件夹。 如果使用的是 iOS 项目，请右键单击 **WalkingGame** 项目。 选择 " **添加->添加文件 ...** "，然后在要处理的平台的文件夹中选择 xnb 文件。

这两个文件现在应是项目的一部分：

![包含 xnb 文件的解决方案资源管理器内容文件夹](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不会自动为新添加的 XNBs 设置生成操作。 对于 iOS，右键单击每个文件并选择 " **生成操作->BundleResource**"。 对于 Android，右键单击每个文件并选择 " **生成操作->AndroidAsset**"。

## <a name="rendering-a-3d-model"></a>呈现3D 模型

在屏幕上查看模型所需的最后一步是添加加载和绘制代码。 具体而言，我们将执行以下操作：

- `Model`在类中定义实例 `Game1`
- 加载 `Model` 实例 `Game1.LoadContent`
- 绘制 `Model` 实例 `Game1.Draw`

将 `Game1.cs` **WalkingGame** PCL)  (的代码文件替换为以下内容：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

如果运行此代码，我们将在屏幕上看到模型：

![屏幕上显示的模型](part1-images/image8.png "如果运行此代码，模型将显示在屏幕上")

### <a name="model-class"></a>模型类

`Model`类是用于从内容文件 (进行三维呈现的核心类，如 fbx 文件) 。 它包含呈现所需的所有信息，包括3D 几何、纹理引用以及 `BasicEffect` 控制定位、照明和照相机值的实例。

`Model`类本身不会直接具有用于定位的变量，因为可以在多个位置呈现单个模型实例，因为我们将在本指南的后面部分进行介绍。

每个 `Model` 实例都由一个或多个 `ModelMesh` 实例构成，这些实例通过 `Meshes` 属性公开。 尽管我们可能会将 `Model` 作为单个游戏对象 (如机器人或汽车) ，但 `ModelMesh` 可以使用不同的值绘制每个对象 `BasicEffect` 。 例如，单个网格部分可以表示机器人的腿或汽车上的滚轮，我们可能会分配 `BasicEffect` 值以使轮旋转或支线移动。 

### <a name="basiceffect-class"></a>BasicEffect 类

`BasicEffect`类提供用于控制呈现选项的属性。 我们对进行的第一次修改 `BasicEffect` 是调用 `EnableDefaultLighting` 方法。 顾名思义，这会启用默认照明，这非常适合用于验证是否 `Model` 按预期方式出现在游戏中。 如果注释掉 `EnableDefaultLighting` 调用，就会看到模型只是其纹理，但没有着色或反光发光：

```csharp
//effect.EnableDefaultLighting ();
```

![仅以其纹理呈现的模型，但不带明暗度或镜面光亮](part1-images/image9.png "仅以其纹理呈现的模型，但不带明暗度或镜面光亮")

`World`属性可用于调整模型的位置、旋转和缩放。 上面的代码使用 `Matrix.Identity` 值，这意味着 `Model` 将与 fbx 文件中指定的内容完全匹配。 在 [第3部分](~/graphics-games/monogame/3d/part3.md)中将更详细地介绍矩阵和3d 坐标，但作为示例，我们可以通过更改属性来更改的位置， `Model` `World` 如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此代码会导致对象向上移动3个单元：

![此代码会导致对象被3个世界单位向上移动](part1-images/image10.png "此代码会导致对象被3个世界单位向上移动")

为指定的最后两个属性 `BasicEffect` 为 `View` 和 `Projection` 。 我们将在 [第3部分](~/graphics-games/monogame/3d/part3.md)介绍3d 相机，但作为示例，我们可以通过更改本地变量来修改相机的位置 `cameraPosition` ：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我们可以看到，照相机已经进一步移回， `Model` 因为透视导致显示的内容更小：

![照相机已进一步移回，导致模型由于透视而显示得更小](part1-images/image11.png "照相机已进一步移回，导致模型由于透视而显示得更小")

## <a name="rendering-multiple-models"></a>呈现多个模型

如上所述，可以多次绘制单个 `Model` 。 为了使此过程更容易，我们将把 `Model` 绘图代码移动到其自己的方法中，将所需 `Model` 位置作为参数。 完成后，我们 `Draw` 的和 `DrawModel` 方法将如下所示：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

这会导致机器人模型绘制六次：

![这会导致机器人模型绘制六次](part1-images/image1.png "这会导致机器人模型绘制六次")

## <a name="summary"></a>总结

本演练介绍了 MonoGame 的 `Model` 类。 它介绍了如何将 fbx 文件转换为 xnb，然后将其加载到 `Model` 类中。 它还显示对实例的修改如何 `BasicEffect` 影响 `Model` 绘图。

## <a name="related-links"></a>相关链接

- [MonoGame 模型参考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [已完成的项目 (示例) ](/samples/xamarin/mobile-samples/modelrenderingmg/)