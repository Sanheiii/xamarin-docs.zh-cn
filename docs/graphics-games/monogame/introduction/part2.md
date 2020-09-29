---
title: 第2部分-实现 WalkingGame
description: 本演练演示如何将游戏逻辑和内容添加到空的 MonoGame 项目中，以创建使用触控输入移动动画动画动画效果的演示。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 835beca6866f5d3f82ae48d1c99557ceafe360ba
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429765"
---
# <a name="part-2--implementing-the-walkinggame"></a>第2部分-实现 WalkingGame

_本演练演示如何将游戏逻辑和内容添加到空的 MonoGame 项目中，以创建使用触控输入移动动画动画动画效果的演示。_

本演练的前面部分演示了如何创建空的 MonoGame 项目。 我们将通过创建一个简单的游戏演示来构建这些先前的部分。 本文包含以下各节：

- 解压缩游戏内容
- MonoGame 类概述
- 渲染第一个 Sprite
- 创建 CharacterEntity
- 将 CharacterEntity 添加到游戏
- 创建动画类
- 将第一个动画添加到 CharacterEntity
- 将移动添加到字符
- 匹配移动和动画

## <a name="unzipping-our-game-content"></a>解压缩游戏内容

在开始编写代码之前，我们需要解压缩游戏 *内容*。 游戏开发人员通常使用术语 " *内容* " 来指通常由视觉对象、游戏设计人员或音频设计人员创建的非代码文件。 常见内容类型包括用于显示视觉对象、播放声音或控制人工智能 (AI) 行为的文件。 从游戏开发团队的角度来看，通常由非程序员创建。

此处使用的内容可 [在 GitHub 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)找到。 我们需要将这些文件下载到我们稍后将在本演练中访问的位置。

## <a name="monogame-class-overview"></a>MonoGame 类概述

对于初学者，我们将探讨基本呈现中使用的 MonoGame 类：

- `SpriteBatch` –用于在屏幕上绘制2D 图形。 *Sprite* 是用于在屏幕上显示图像的2d 视觉对象。 `SpriteBatch`对象可以在其和方法之间一次绘制一个子 `Begin` 画面 `End` ，也可以将多个子画面组合在一起或*分批*。
- `Texture2D` –表示运行时的图像对象。 `Texture2D` 通常通过文件格式（如 .png 或 .bmp）创建实例，但也可以在运行时动态创建。 `Texture2D` 当使用实例进行呈现时，将使用实例 `SpriteBatch` 。
- `Vector2` –表示二维坐标系统中的一个位置，该位置通常用于定位视觉对象。 MonoGame 还包括 `Vector3` 和， `Vector4` 但我们将仅 `Vector2` 在本演练中使用。
- `Rectangle` –位置、宽度和高度的四面区域。 我们将使用它来定义我们 `Texture2D` 开始使用动画时要呈现的部分。

还应注意，MonoGame 不由 Microsoft 维护–无论其命名空间。 `Microsoft.Xna`命名空间用于 MonoGame，以便更轻松地将现有的未生成项目迁移到 MonoGame。

## <a name="rendering-our-first-sprite"></a>渲染第一个 Sprite

接下来，我们将在屏幕上绘制一个子画面，以演示如何在 MonoGame 中执行2D 呈现。

### <a name="creating-a-texture2d"></a>创建 Texture2D

我们需要创建一个 `Texture2D` 在呈现子画面时要使用的实例。 所有游戏内容最终都包含在一个名为 **content** 的文件夹中，该文件夹位于特定于平台的项目中。 MonoGame 共享项目不能包含内容，因为内容必须使用平台特定的生成操作。 内容文件夹可以在 iOS 项目中找到，也可以在 Android 项目的 "资产" 文件夹中找到。

若要添加游戏内容，请右键单击 " **内容** " 文件夹，然后选择 " **添加 > 添加文件 ...** "导航到提取 content.zip 文件的位置并选择 **charactersheet.png** 文件。 如果系统询问如何将文件添加到文件夹，应选择 " **复制** " 选项：

![如果系统询问如何将文件添加到文件夹，请选择 "复制" 选项](part2-images/image1.png)

Content 文件夹现在包含 charactersheet.png 文件：

![Content 文件夹现在包含 charactersheet.png 文件](part2-images/image2.png)

接下来，我们将添加代码来加载 charactersheet.png 文件并创建 `Texture2D` 。 要执行此操作，请打开 `Game1.cs` 文件，并将以下字段添加到 Game1.cs 类：

```csharp
Texture2D characterSheetTexture;
```

接下来，我们将 `characterSheetTexture` 在方法中创建 `LoadContent` 。 在任何修改 `LoadContent` 方法如下之前：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

请注意，默认项目已 `spriteBatch` 为我们实例化实例。 稍后我们将使用此代码，但我们不会添加任何其他代码来准备要 `spriteBatch` 使用的。 另一方面，我们 `spriteSheetTexture` 需要初始化，因此，我们将在创建后对其进行初始化 `spriteBatch` ：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

现在，我们有了一个 `SpriteBatch` 实例和一个 `Texture2D` 实例，接下来可以将呈现代码添加到 `Game1.Draw` 方法：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

运行游戏现在会显示一个子画面，其中显示了从 charactersheet.png 创建的纹理：

![运行游戏现在会显示一个子画面，其中显示了创建的纹理 charactersheet.png](part2-images/image3.png)

## <a name="creating-the-characterentity"></a>创建 CharacterEntity

到目前为止，我们已添加了用于将一个子画面呈现到屏幕的代码;但是，如果我们要开发完整的游戏而不创建任何其他类，就会遇到代码组织问题。

### <a name="what-is-an-entity"></a>什么是实体？

用于组织游戏代码的常见模式是为每个游戏 *实体* 类型创建一个新类。 游戏开发中的实体是一个对象，该对象可以包含以下某些特征 (并非全部都是必需的) ：

- 视觉对象，如子画面、文本或三维模型
- 物理学或每个帧行为，如单元 patrolling 设置路径或播放机字符响应输入
- 可以动态创建和销毁，如播放机显示和回收

实体组织系统可能比较复杂，许多游戏引擎提供类来帮助管理实体。 我们将实现一个非常简单的实体系统，因此，值得注意的是，完整的游戏通常需要开发人员的更多组织。

### <a name="defining-the-characterentity"></a>定义 CharacterEntity

我们将调用的实体 `CharacterEntity` 将具有以下特征：

- 能够加载自己的 `Texture2D`
- 呈现自己的功能，包括用于更新行走动画的调用方法
- `X` 和 Y 属性以控制字符的位置。
- 自行更新的能力–具体而言，是为了从触摸屏读取值并适当调整位置。

若要将添加 `CharacterEntity` 到游戏，请右键单击或单击鼠标右键，单击 " **WalkingGame** " 项目，然后选择 " **添加 > 新文件 ...**"。选择 " **空类** " 选项并将新文件命名为 **CharacterEntity**，然后单击 " **新建**"。

首先，我们将添加的功能，以便 `CharacterEntity` 加载 `Texture2D` 和自行绘制。 我们将修改新添加的 `CharacterEntity.cs` 文件，如下所示：

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;

namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

上面的代码添加了将内容定位、呈现和加载到的责任 `CharacterEntity` 。 我们花点时间来指出上述代码中的一些注意事项。

### <a name="why-are-x-and-y-floats"></a>为什么 X 和 Y 是浮动的？

不熟悉游戏编程的开发人员可能会想知道为什么 `float` 要使用该类型，而不是 `int` 或 `double` 。 原因在于，32位值最常用于在低级别呈现代码中定位。 Double 类型占用64位的精度，这对于定位对象很少需要。 尽管32位差异看起来不是重要的，但许多新式游戏包含需要处理数十个位置值的图形，每个帧 (每秒30或60次) 。 将必须通过图形管道移动的内存量减少一半可能会对游戏性能产生显著影响。

`int`数据类型可以是用于定位的适当度量单位，但它可能会对实体的定位方式施加限制。 例如，如果使用整数值，则会更难为支持放大或3D 相机 (游戏实现平滑运动，) 允许使用这些值 `SpriteBatch` 。

最后，我们会看到，在屏幕上移动人物的逻辑将使用游戏的时间值来实现。 在给定帧中所经过的时间相乘的速度很少导致产生整数，因此，我们需要 `float` 为 `X` 和使用 `Y` 。

### <a name="why-is-charactersheettexture-static"></a>为什么 characterSheetTexture 静态？

`characterSheetTexture` `Texture2D` 实例是 charactersheet.png 文件的运行时表示形式。 换句话说，它包含从源 **charactersheet.png** 文件中提取的每个像素的颜色值。 如果我们的游戏创建了两个 `CharacterEntity` 实例，则每个实例都需要 charactersheet.png 访问信息。 在这种情况下，我们不希望产生 charactersheet.png 加载两次的性能成本，也不希望在 ram 中存储重复的纹理内存。 使用 `static` 字段允许我们 `Texture2D` 在所有实例中共享相同的 `CharacterEntity` 。

将 `static` 成员用于内容的运行时表示形式是一种简化的解决方案，它不能解决在较大的游戏中遇到的问题，例如在从一个级别移到另一个级别时卸载内容。 更复杂的解决方案超出了本演练的范围，包括使用 MonoGame 的内容管道或创建自定义的内容管理系统。

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>为什么 SpriteBatch 作为参数传递，而不是由实体进行实例化？

`Draw`上面实现的方法采用 `SpriteBatch` 参数，但相比之下，由 `characterSheetTexture` 实例化 `CharacterEntity` 。

出现这种情况的原因是，当对 `SpriteBatch` 所有调用使用同一个实例时 `Draw` ，以及在一 `Draw` 组和调用之间进行所有调用时，可以使用最有效的呈现方式 `Begin` `End` 。 当然，我们的游戏只包含一个实体实例，但是，更复杂的游戏会从允许多个实体使用相同实例的模式中受益 `SpriteBatch` 。

## <a name="adding-characterentity-to-the-game"></a>将 CharacterEntity 添加到游戏

现在我们已添加 `CharacterEntity` 了包含代码来呈现自身，接下来我们可以替换中的代码 `Game1.cs` 以使用此新实体的实例。 为此，我们将 `Texture2D` 使用中的字段替换字段 `CharacterEntity` `Game1` ：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

接下来，我们将在中添加此实体的实例化 `Game1.Initialize` ：

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我们还需要 `Texture2D` 从中删除创建 `LoadContent` ，因为现在在中进行了处理 `CharacterEntity` 。 `Game1.LoadContent` 应该如下所示：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意的是，尽管它的名称 `LoadContent` 不是可加载内容的唯一位置，但在播放机到达游戏的某个点之前，可能不需要许多游戏在其 Initialize 方法中实现内容加载，甚至会将其更新代码作为内容。

最后，我们可以修改 Draw 方法，如下所示：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

如果运行该游戏，现在会看到字符。 由于 X 和 Y 默认值为0，因此字符位于屏幕的左上角：

![由于 X 和 Y 默认值为0，因此字符位于屏幕的左上角](part2-images/image4.png)

## <a name="creating-the-animation-class"></a>创建动画类

目前，我们会 `CharacterEntity` 显示完整的 **charactersheet.png** 文件。 这种在一个文件中排列多个图像的方式称为子 *画面*。 通常，sprite 仅呈现部分动画工作表。 我们将修改 `CharacterEntity` 以呈现此 **charactersheet.png**的一部分，并且此部分将随时间变化以显示遍历动画。

我们将创建 `Animation` 类以控制 CharacterEntity 动画的逻辑和状态。 动画类将是一个常规类，可用于任何实体，而不只是 `CharacterEntity` 动画。 终极 `Animation` 类将提供在 `Rectangle` `CharacterEntity` 绘制自身时将使用的。 我们还将创建一个 `AnimationFrame` 用于定义动画的类。

### <a name="defining-animationframe"></a>定义 AnimationFrame

`AnimationFrame` 不会包含任何与动画相关的逻辑。 我们将仅使用它来存储数据。 若要添加 `AnimationFrame` 类，请右键单击或单击控件，然后单击 " **WalkingGame** " 共享项目，然后选择 " **添加 > 新文件 ...** "。输入名称 **AnimationFrame** ，并单击 " **新建** " 按钮。 我们将修改该 `AnimationFrame.cs` 文件，使其包含以下代码：

```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame`类包含两个信息片段：

- `SourceRectangle` –定义 `Texture2D` 将由显示的区域 `AnimationFrame` 。 此值以像素为单位进行度量，左上方 (0，0) 。
- `Duration` –定义 `AnimationFrame` 在中使用时显示的时间 `Animation` 。

### <a name="defining-animation"></a>定义动画

`Animation`类将包含 `List<AnimationFrame>` 和逻辑，以根据已通过的时间来切换当前显示的帧。

若要添加 `Animation` 类，请右键单击或单击控件，然后单击 " **WalkingGame** " 共享项目，然后选择 " **添加 > 新文件 ...**"。输入名称 **动画** 并单击 " **新建** " 按钮。 我们将修改该 `Animation.cs` 文件，使其包含以下代码：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;

            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

让我们看看类中的一些详细信息 `Animation` 。

### <a name="frames-list"></a>帧列表

该 `frames` 成员是存储动画数据的内容。 实例化动画的代码将 `AnimationFrame` 通过方法向列表中添加实例 `frames` `AddFrame` 。 更完整的实现可能会提供 `public` 用于修改的方法或属性 `frames` ，但我们会限制为此演练添加帧的功能。

### <a name="duration"></a>持续时间

Duration 返回的总持续时间， `Animation,` 这是通过添加所有包含实例的持续时间获取的 `AnimationFrame` 。 如果是不可变对象，则可能会缓存此值 `AnimationFrame` ，但由于我们已将 AnimationFrame 作为类实现，在将其添加到动画后，我们需要在访问属性时计算此值。

### <a name="update"></a>更新

`Update`每次) 更新整个游戏时，都应将此方法称为每个帧 (。 其目的是增加 `timeIntoAnimation` 用于返回当前显示的帧的成员。 中的逻辑 `Update` 可防止 `timeIntoAnimation` 超出整个动画的持续时间。

由于我们将使用 `Animation` 类来显示遍历动画，因此我们希望在此动画结束后重复此动画。 可以通过检查是否大于值来实现此目的 `timeIntoAnimation` `Duration` 。 如果是这样，则会循环回到开头，并保留余数，导致循环动画。

### <a name="obtaining-the-current-frames-rectangle"></a>获取当前框架的矩形

类的目的 `Animation` 最终是提供在 `Rectangle` 绘制子画面时可以使用的。 此 `Animation` 类当前包含用于更改成员的逻辑 `timeIntoAnimation` ，我们将使用这些逻辑来获取 `Rectangle` 应显示的成员。 为此，我们将 `CurrentRectangle` 在类中创建一个属性 `Animation` 。 将此属性复制到 `Animation` 类：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>将第一个动画添加到 CharacterEntity

`CharacterEntity`将包含遍历和执行的动画，以及对当前正在显示的的引用 `Animation` 。

首先，我们将添加第一个， `Animation,` 我们将使用它来测试迄今为止编写的功能。 将以下成员添加到 CharacterEntity 类：

```csharp
Animation walkDown;
Animation currentAnimation;
```

接下来，让我们定义 `walkDown` 动画。 为此，请 `CharacterEntity` 按如下所示修改构造函数：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

如前文所述，我们需要调用来 `Animation.Update` 播放基于时间的动画。 我们还需要分配 `currentAnimation` 。 现在，我们将分配 `currentAnimation` 给 `walkDown` ，但我们会在以后实现移动逻辑时替换此代码。 我们会将方法添加到，如下所示 `Update` `CharacterEntity` ：

```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

现在，我们已 `currentAnimation` 分配和更新了，可以使用它来执行我们的绘图。 我们将 `CharacterEntity.Draw` 按如下所示进行修改：

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

获取动画效果的最后一步 `CharacterEntity` 是调用中新添加的 `Update` 方法 `Game1` 。 修改 `Game1.Update` ，如下所示：

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

现在， `CharacterEntity` 将播放其 `walkDown` 动画：

![现在，CharacterEntity 将播放其 walkDown 动画](part2-images/image5.gif)

## <a name="adding-movement-to-the-character"></a>将移动添加到字符

接下来，我们将使用触控控件向字符添加移动。 当用户接触到屏幕时，该字符将移到屏幕的接触点。 如果未检测到任何接触，则该字符将到位。

### <a name="defining-getdesiredvelocityfrominput"></a>定义 GetDesiredVelocityFromInput

我们将使用 MonoGame 的 `TouchPanel` 类，它提供有关触摸屏的当前状态的信息。 让我们添加一个方法，该方法将检查 `TouchPanel` 并返回字符的所需速度：

```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState`方法返回一个 `TouchCollection` ，其中包含有关用户正在触摸屏幕的位置的信息。 如果用户未接触到屏幕，则 `TouchCollection` 将为空，在这种情况下，我们不应移动字符。

如果用户正在触摸屏幕，我们会将字符移动到第一个触摸的位置，即 `TouchLocation` 索引0处。 最初，我们会将所需速度设置为等于字符位置和第一个触摸位置之间的差异：

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

下面是一些数学，这会使字符在同一速度下移动。 为了帮助解释这一点很重要，让我们考虑一下，用户在远离屏幕500像素的地方接触到该字符所在的位置。 设置的第一行的值将为 `desiredVelocity.X` 500。 但是，如果用户只是在屏幕上以100个单位的距离触摸屏幕，则会将 `desiredVelocity.X` 设置为100。 结果就是该字符的移动速度将响应触摸点距字符的距离。 由于我们希望字符始终以相同的速度移动，因此我们需要修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`语句检查速度是否为非零值，换言之，检查以确保用户不会接触到与字符当前位置相同的位置。 如果不是，则需要将字符的速度设置为常量，而不管触摸远距离。 为此，我们通过规范化速度向量来实现此目的，导致其长度为1。 如果速度向量为1，则表示该字符将每秒1个像素移动。 我们将通过将值乘以200的所需速度来提高速度。

### <a name="applying-velocity-to-position"></a>将速度应用于位置

从返回的速度 `GetDesiredVelocityFromInput` 需要应用于字符的 `X` 和 `Y` 值，以在运行时产生任何影响。 我们将修改方法，如下所示 `Update` ：

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

此处实现的内容称为 *基于时间的* 移动 (，而不是 *基于帧的* 移动) 。 基于时间的移动将速率值相乘 (在本例中，变量中存储的值是 `velocity` 自上次更新（存储在中）以来所经过的时间) `gameTime.ElapsedGameTime.TotalSeconds` 。 如果游戏每秒运行的帧数更少，则帧之间的运行时间会向上–最终结果是，使用基于时间的移动的对象将始终以相同的速度移动，而与帧速率无关。

如果我们现在运行游戏，将看到该字符正朝着触摸位置移动：

![人物向触摸位置移动](part2-images/image6.gif)

## <a name="matching-movement-and-animation"></a>匹配移动和动画

当我们将字符移动和播放一个动画后，便可以定义动画的其余部分，并使用它们来反映字符的移动。 完成后，总共会有8个动画：

- 向上、向下、向左和向右滚动的动画
- 仍有正面和正面朝上、向下、向左、向右的动画

### <a name="defining-the-rest-of-the-animations"></a>定义其余动画

首先，我们会 `Animation` 在添加的同一位置将实例添加到 `CharacterEntity` 类中 `walkDown` 。 完成此操作后， `CharacterEntity` 将具有以下 `Animation` 成员：

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

现在，我们将在构造函数中定义动画， `CharacterEntity` 如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

请注意，上面的代码已添加到 `CharacterEntity` 构造函数，以使本演练更短一些。 游戏通常会将字符动画的定义分隔到自己的类中，或从 XML 或 JSON 等数据格式加载此信息。

接下来，我们将调整逻辑，以便根据字符的移动方向或最后一个动画（如果该字符刚停止）使用动画。 为此，我们将修改 `Update` 方法：

```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

用于切换动画的代码分为两个块。 第一个检查速度是否不等于 `Vector2.Zero` –这会告诉我们该字符是否正在移动。 如果移动了字符，则可以查看 `velocity.X` 和 `velocity.Y` 值来确定要播放的动画。

如果该字符不是移动的，则我们希望将该字符设置 `currentAnimation` 为一个上移动画–但仅当 `currentAnimation` 是行走动画或尚未设置动画时才执行此操作。 如果不 `currentAnimation` 是四个行走动画中的一种，则该字符已存在，因此我们不需要进行更改 `currentAnimation` 。

此代码的结果是字符将在浏览时正确地进行动画处理，然后面对停止时的最后方向：

![此代码的结果是字符将在浏览时正确地进行动画处理，然后在停止时面对正在进行的最后方向](part2-images/image7.gif)

## <a name="summary"></a>总结

本演练演示了如何使用 MonoGame 创建一个包含子画面、移动对象、输入检测和动画的跨平台游戏。 它介绍了如何创建常规用途动画类。 还介绍了如何创建用于组织代码逻辑的字符实体。

## <a name="related-links"></a>相关链接

- [CharacterSheet 图像资源 (示例) ](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [完成游戏 (示例) ](/samples/xamarin/mobile-samples/walkinggamemg/)