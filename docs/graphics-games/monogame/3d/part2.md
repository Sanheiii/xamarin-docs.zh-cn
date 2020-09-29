---
title: 在 MonoGame 中绘制带有顶点的3D 图形
description: MonoGame 支持使用顶点数组定义三维对象如何基于每个点进行呈现。 用户可以利用顶点数组创建动态几何、实现特殊效果，并通过精选提高其渲染效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: ccec89253c58d7d43cb09f33777c253a2f0da56d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431117"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>在 MonoGame 中绘制带有顶点的3D 图形

_MonoGame 支持使用顶点数组定义三维对象如何基于每个点进行呈现。用户可以利用顶点数组创建动态几何、实现特殊效果，并通过精选提高其渲染效率。_

已阅读 [有关呈现模型指南](~/graphics-games/monogame/3d/part1.md) 的用户将熟悉如何在 MonoGame 中呈现3d 模型。 `Model`当处理在文件中定义的数据时，类是一种有效的方式， (例如 fbx) 和处理静态数据。 有些游戏要求在运行时动态定义或操作三维几何图形。 在这些情况下，我们可以使用 *顶点* 数组定义和呈现几何。 顶点是三维空间中的点的常规术语，它是用于定义几何图形的排序列表的一部分。 通常，顶点的排序方式类似于定义一系列三角形。

为了帮助直观显示用于创建3D 对象的顶点，让我们考虑以下球：

![若要直观地显示如何使用顶点创建3D 对象，请考虑此球](part2-images/image1.png)

如上所示，球体明确地包含多个三角形。 可以查看球的线框，看看顶点如何连接到三角形：

![查看球体的线框，查看顶点如何连接到形成三角形](part2-images/image2.png)

本演练将介绍以下主题：

- 创建项目
- 创建顶点
- 添加绘制代码
- 使用纹理呈现
- 修改纹理坐标
- 用模型呈现顶点

已完成的项目将包含将使用顶点数组绘制的棋盘楼层：

![已完成的项目将包含将使用顶点数组绘制的棋盘楼层](part2-images/image3.png)

## <a name="creating-a-project"></a>创建项目

首先，我们将下载一个项目，该项目将作为起点。 我们将使用模型项目， [可在此处找到](/samples/xamarin/mobile-samples/modelrenderingmg/)。

下载并解压缩后，打开并运行该项目。 我们期望看到六个在屏幕上绘制的机器人模型：

![在屏幕上绘制六个机器人模型](part2-images/image4.png)

在此项目结束时，我们会将自己的自定义顶点呈现与机器人组合在一起 `Model` ，因此，我们不会删除机器人呈现代码。 相反，我们只需清除 `Game1.Draw` 立即删除6个机器人的绘图。 为此，请打开 **Game1.cs** 文件并找到 `Draw` 方法。 对其进行修改，使其包含以下代码：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

这将导致游戏显示空的蓝屏：

![这将导致游戏显示空的蓝屏](part2-images/image5.png)

## <a name="creating-the-vertices"></a>创建顶点

我们将创建一个顶点数组来定义几何。 在本演练中，我们将创建三维空间 (方形的3D 平面，而不是飞机) 。 虽然我们的平面具有四个边和四个角，但它会由两个三角形组成，每个三角形都需要三个顶点。 因此，我们将定义总共六个点。

到目前为止，我们一直在谈论顶点，但 MonoGame 提供了一些可用于顶点的标准结构：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每个类型的名称都指示它包含的组件。 例如， `VertexPositionColor` 包含位置和颜色的值。 让我们看看每个组件：

- Position –所有顶点类型都包含一个 `Position` 组件。 这些 `Position` 值定义顶点在 (X、Y 和 Z) 的3d 空间中的位置。
- Color –顶点可以选择指定 `Color` 值来执行自定义色调。
- 法线–法线定义对象图面的方向。 如果使用光源渲染对象，则必须具有法线，因为图面的方向会影响其收到的光线。 通常将法线指定为 *单位矢量* –长度为1的3d 矢量。
- 纹理–纹理指纹理坐标–即，纹理的哪个部分应显示在给定顶点上。 如果使用纹理呈现3D 对象，则必须使用纹理值。 纹理坐标为规范化坐标，这意味着值将介于0和1之间。 本指南稍后将更详细地介绍纹理坐标。

我们的平面将作为楼层，并在执行渲染时要应用纹理，因此我们将使用该 `VertexPositionTexture` 类型来定义顶点。

首先，将成员添加到我们的 Game1 类：

```csharp
VertexPositionTexture[] floorVerts;
```

接下来，在中定义顶点 `Game1.Initialize` 。 请注意，本文前面提到的提供的模板不包含 `Game1.Initialize` 方法，因此，我们需要将整个方法添加到 `Game1` ：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

若要帮助可视化顶点的外观，请参阅下图：

![为了帮助可视化顶点的外观，请考虑此示意图](part2-images/image6.png)

我们需要依赖于我们的关系图来可视化顶点，直到完成呈现代码。

## <a name="adding-drawing-code"></a>添加绘制代码

现在我们已定义了几何的位置，接下来我们可以编写呈现代码。

首先，我们需要定义一个实例， `BasicEffect` 该实例将保存用于呈现的参数，如位置和照明。 为此，请将 `BasicEffect` 成员添加到 `Game1` 下面定义了字段的类 `floorVerts` ：

```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

接下来，修改 `Initialize` 方法以定义效果：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

现在，我们可以添加代码来执行绘制操作：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

我们需要 `DrawGround` 在以下项中调用 `Game1.Draw` ：

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

执行时，应用会显示以下内容：

![应用将在执行时显示此](part2-images/image7.png)

让我们看看上面代码中的一些详细信息。

### <a name="view-and-projection-properties"></a>视图和投影属性

`View`和 `Projection` 属性控制我们如何查看场景。 稍后我们将在我们重新添加模型呈现代码时修改此代码。 具体来说， `View` 就是控制照相机的位置和方向，并 `Projection` 控制可用于缩放相机)  (视图的 *字段* 。

### <a name="techniques-and-passes"></a>技术和通过

为我们的效果分配了属性后，我们可以执行实际渲染。

`CurrentTechnique`在本演练中，我们不会更改属性，但更高级的游戏可能会有单个效果，该效果可以采用不同的方式执行绘制 (如) 的应用方式。 其中的每种呈现模式可以表示为在呈现之前可以分配的一种技术。 此外，每种方法都可能要求多个传递正确呈现。 如果呈现复杂的视觉对象（如发光表面或毛皮），则效果可能需要多个刀路。

需要记住的重要一点是， `foreach` 循环启用相同的 c # 代码来呈现任何效果，而不考虑基础的复杂性 `BasicEffect` 。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 用于呈现顶点的位置。 第一个参数告诉方法我们如何组织顶点。 我们已经对它们进行了结构化，以便每个三角形都由三个有序顶点定义，因此我们使用 `PrimitiveType.TriangleList` 该值。

第二个参数是我们前面定义的顶点的数组。

第三个参数指定要绘制的第一个索引。 由于我们希望呈现整个顶点数组，因此，我们将传递值0。

最后，我们指定要呈现多少三角形。 顶点数组包含两个三角形，因此传递值2。

## <a name="rendering-with-a-texture"></a>使用纹理呈现

此时，应用程序在透视)  (呈现白面。 接下来，我们将向项目添加一个要在渲染平面时使用的纹理。

为简单起见，我们会将 .png 直接添加到项目中，而不是使用 MonoGame 管道工具。 为此，请将 [此 .png 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) 下载到你的计算机。 下载完成后，在 "解决方案板" 中右键单击 " **内容** " 文件夹，然后选择 " **添加>添加文件 ...** "。 如果在 Android 上工作，则此文件夹将位于特定于 Android 的项目中的 " **资产** " 文件夹下。 如果在 iOS 上，则此文件夹将位于 iOS 项目的根目录中。 导航到 **checkerboard.png** 的保存位置，然后选择此文件。 选择此可将文件复制到目录。

接下来，我们将添加代码来创建 `Texture2D` 实例。 首先，将 `Texture2D` 作为的成员添加到 `Game1` 实例下面 `BasicEffect` ：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改 `Game1.LoadContent` ，如下所示：

```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

接下来，修改 `DrawGround` 方法。 唯一要做的修改是将分配给 `effect.TextureEnabled` `true` ，并将设置为 `effect.Texture` `checkerboardTexture` ：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

最后，我们还需要修改 `Game1.Initialize` 方法，以便在顶点上分配纹理坐标：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

如果我们运行代码，我们可以看到，我们的平面现在显示的是棋盘模式：

![平面现在显示棋盘模式](part2-images/image8.png)

## <a name="modifying-texture-coordinates"></a>修改纹理坐标

MonoGame 使用标准化纹理坐标，这些坐标是0到1之间的坐标，而不是介于0和纹理的宽度或高度之间。 下图可帮助可视化规范化坐标：

![此关系图可帮助可视化规范化坐标](part2-images/image9.png)

规范化纹理坐标允许调整纹理大小，而无需重写代码或重新创建模型 (如 fbx 文件) 。 这是可能的，因为标准化的坐标表示比率而不是特定的像素。 例如， (1，1) 将始终表示右下角，而与纹理大小无关。

对于重复次数，我们可以将纹理坐标赋值更改为使用单个变量：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

这将导致纹理重复20次：

![这将导致纹理重复20次](part2-images/image10.png)

## <a name="rendering-vertices-with-models"></a>用模型呈现顶点

现在，我们的平面呈现正确，我们可以重新添加模型，以同时查看所有内容。 首先，我们会将模型代码重新添加到 `Game1.Draw` (修改了位置) 的方法中：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
}
```

我们还将 `Vector3` 在中创建一个 `Game1` 来表示照相机的位置。 我们将在声明下面添加一个字段 `checkerboardTexture` ：

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10);
```

接下来， `cameraPosition` 从方法中删除局部变量 `DrawModel` ：

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            ...
```

同样， `cameraPosition` 从方法中删除局部变量 `DrawGround` ：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ...
```

现在，如果我们运行代码，我们可以同时查看模型和基础：

![同时同时显示模型和地面](part2-images/image11.png)

如果修改照相机位置 (例如，通过增加其 X 值，这种情况下会将相机移到左侧) ，我们可以看到该值会影响地面和模型：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

此代码将产生以下结果：

![此代码会导致此视图](part2-images/image3.png)

## <a name="summary"></a>总结

本演练演示了如何使用顶点数组来执行自定义呈现。 在这种情况下，我们通过将基于顶点的着色与纹理和组合在一起来创建一个棋盘层 `BasicEffect` 此外，我们还介绍了基于顶点的呈现可以与同一场景中的模型混合。

## <a name="related-links"></a>相关链接

- [棋盘文件 (示例) ](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [已完成的项目 (示例) ](/samples/xamarin/mobile-samples/modelsandvertsmg/)