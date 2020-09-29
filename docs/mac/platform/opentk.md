---
title: Xamarin 中的 OpenTK 简介
description: 本文介绍了如何在 Xamarin 应用程序中使用 OpenTK。 它介绍了如何创建和维护游戏窗口、呈现简单对象并向用户显示该对象。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7f600593d7a3bb180ef8daca6639dbbea4bf07aa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430072"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Xamarin 中的 OpenTK 简介

OpenTK (打开的工具包) 是一种高级的低级别 c # 库，可让你更轻松地使用 OpenGL、OpenCL 和 OpenAL。 OpenTK 可用于需要3D 图形、音频或计算功能的游戏、科学应用程序或其他项目。 本文简要介绍了如何在 Xamarin 应用程序中使用 OpenTK。

[![示例应用运行](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

本文介绍了 Xamarin OpenTK 应用程序中的基础知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

<a name="About_OpenTK"></a>

## <a name="about-opentk"></a>关于 OpenTK

如上所述，OpenTK (打开的工具包) 是一种高级的低级别 c # 库，可让你更轻松地使用 OpenGL、OpenCL 和 OpenAL。 在 Xamarin 应用程序中使用 OpenTK 提供以下功能：

- **快速开发** -OpenTK 提供了强大的数据类型和内联文档，可提高编码工作流并更快地捕获错误。
- **轻松集成** -OpenTK 旨在与 .net 应用程序轻松集成。
- 许可的**许可**-OPENTK 在 MIT/X11 许可证下分发，完全免费。
- **丰富的类型安全绑定** -OpenTK 支持最新版本的 OpenGL、OPENGL | ES、OpenAL 和 OpenCL 和自动扩展加载、错误检查和内联文档。
- **灵活的 GUI 选项** -OpenTK 提供专为游戏和 Xamarin 设计的本机、高性能游戏窗口。
- **完全托管的符合 CLS 的代码** OpenTK 支持32位和64位版本的 macOS，不包含非托管库。
- **3D 数学工具包** OpenTK `Vector` `Matrix` `Quaternion` `Bezier` 通过其3d 数学工具包提供、和结构。

OpenTK 可用于需要3D 图形、音频或计算功能的游戏、科学应用程序或其他项目。

有关详细信息，请参阅 [开放工具包](https://opentk.net) 网站。

<a name="OpenTK_Quickstart"></a>

## <a name="opentk-quickstart"></a>OpenTK 快速入门

作为在 Xamarin 应用程序中使用 OpenTK 的快速介绍，我们将创建一个简单的应用程序，用于打开游戏视图，在该视图中呈现简单的三角形，并将游戏视图 attachs 到 Mac 应用程序的主窗口，以向用户显示三角形。

<a name="Starting_a_New_Project"></a>

### <a name="starting-a-new-project"></a>启动新项目

开始 Visual Studio for Mac 并创建新的 Xamarin Mac 解决方案。 选择**Mac**  >  **应用**  >  **常规**  >  **Cocoa 应用**：

[![添加新的 Cocoa 应用](opentk-images/sample01.png)](opentk-images/sample01.png#lightbox)

输入 `MacOpenTK` 作为 **项目名称**：

[![设置项目名称](opentk-images/sample02.png)](opentk-images/sample02.png#lightbox)

单击 " **创建** " 按钮以生成新项目。

<a name="Including_OpenTK"></a>

### <a name="including-opentk"></a>包括 OpenTK

你需要包含对 OpenTK 程序集的引用，然后才能在 Xamarin Mac 应用程序中使用 Open TK。 在 **解决方案资源管理器**中，右键单击 " **引用** " 文件夹，然后选择 " **编辑引用 ...**"。

勾选 `OpenTK` 并单击 **"确定"** 按钮：

[![编辑项目引用](opentk-images/sample03.png)](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK"></a>

### <a name="using-opentk"></a>使用 OpenTK

创建新项目 `MainWindow.cs` 后，在 **解决方案资源管理器** 中双击该文件以将其打开以进行编辑。 使 `MainWindow` 类看起来如下所示：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

接下来，我们将详细介绍以下代码。

<a name="Required_APIs"></a>

### <a name="required-apis"></a>必需的 Api

若要在 Xamarin 类中使用 OpenTK，需要多个引用。 在定义开始时，我们包括以下 `using` 语句：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```

使用 OpenTK 的任何类都需要这一最小集。

<a name="Adding_the_Game_View"></a>

### <a name="adding-the-game-view"></a>添加游戏视图

接下来，我们需要创建一个游戏视图来包含与 OpenTK 的所有交互，并显示结果。 我们使用了以下代码：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

此处，我们已使游戏视图与主 Mac 窗口的大小相同，并已将窗口的内容视图替换为新的 `MonoMacGameView` 。 由于我们替换了现有的窗口内容，因此当调整主窗口大小时，将自动调整我们的视图大小。

<a name="Responding_to_Events"></a>

### <a name="responding-to-events"></a>对事件作出响应

每个游戏视图都应响应多个默认事件。 本部分将介绍所需的主要事件。

<a name="The_Load_Event"></a>

### <a name="the-load-event"></a>Load 事件

`Load`事件是从磁盘（如图像、纹理或音乐）加载资源的位置。 对于简单的测试应用程序，我们没有使用该 `Load` 事件，但包含它以供参考：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event"></a>

### <a name="the-resize-event"></a>Resize 事件

`Resize`每次调整游戏视图大小时，都应调用事件。 对于我们的示例应用程序，我们会将总帐视区的大小设为与游戏视图相同， (通过 Mac 主窗口) 自动调整大小，使用以下代码：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event"></a>

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame`事件用于处理用户输入、更新对象位置、运行物理学或 AI 计算。 对于简单的测试应用程序，我们没有使用该 `UpdateFrame` 事件，但包含它以供参考：

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> OpenTK 的 Xamarin 实现不包括 `Input API` ，因此，你将需要使用 Apple 提供的 api 来添加键盘和鼠标支持。 （可选）可以创建的自定义实例 `MonoMacGameView` ，并重写 `KeyDown` 和 `KeyUp` 方法。

<a name="The_RenderFrame_Event"></a>

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame`事件包含用于呈现图形)  (绘图的代码。 对于我们的示例应用，我们使用简单的三角形填充游戏视图：

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

通常，呈现代码将调用， `GL.Clear` 以在绘制新元素之前删除任何现有元素。

> [!IMPORTANT]
> 对于 **OpenTK 版本** 的， `SwapBuffers` `MonoMacGameView` 在呈现代码末尾不要调用实例的方法。 这样做将导致游戏视图快速看起来，而不是显示呈现的视图。

<a name="Running_the_Game_View"></a>

### <a name="running-the-game-view"></a>运行游戏视图

将所有必需的事件定义为，并将游戏视图附加到应用程序的主 Mac 窗口，我们将阅读运行游戏视图并显示图形。 使用以下代码：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我们会按所需的帧速率来更新游戏视图，对于我们的示例，我们已选择 `60` "每秒帧数" (与普通电视) 相同的刷新频率。

运行应用程序并查看输出：

[![应用输出示例](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

如果调整窗口的大小，则游戏视图也会随之驻留，同时也会调整三角形的大小并进行实时更新。

<a name="Where_to_Next"></a>

### <a name="where-to-next"></a>下一步是什么？

在 Xamarin 应用程序中使用 OpenTk 的基础知识完成后，以下是有关下一步操作的一些建议：

- 尝试更改和事件中的三角形颜色和游戏视图的背景色 `Load` `RenderFrame` 。
- 当用户在和事件中按下某个键 `UpdateFrame` `RenderFrame` ，或者创建您自己的自定义 `MonoMacGameView` 类并重写 `KeyUp` 和 `KeyDown` 方法时，使三角形更改颜色。
- 使用事件中的识别密钥使三角形在屏幕上移动 `UpdateFrame` 。 提示：使用 `Matrix4.CreateTranslation` 方法创建平移矩阵并调用 `GL.LoadMatrix` 方法以在事件中加载它 `RenderFrame` 。
- 使用 `for` 循环来呈现事件中的几个三角形 `RenderFrame` 。
- 旋转相机，为3D 空间中的三角形指定不同的视图。 提示：使用 `Matrix4.CreateTranslation` 方法创建平移矩阵并调用 `GL.LoadMatrix` 方法将其加载。 你还可以使用 `Vector2` 、 `Vector3` `Vector4` 和 `Matrix4` 类进行照相机操作。

有关更多示例，请参阅 [OpenTK 示例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) 存储库。 它包含使用 OpenTK 的官方示例列表。 必须修改这些示例，以便将与 OpenTK 的 Xamarin 版本结合使用。

有关 OpenTK 实现的更复杂的 Xamarin 示例，请参阅我们的 [MonoMacGameView](/samples/xamarin/mac-samples/monomacgamewindow) 示例。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文大致介绍了如何在 Xamarin. Mac 应用程序中使用 OpenTK。 我们了解了如何创建游戏窗口，如何将游戏窗口附加到 Mac 窗口，以及如何在游戏窗口中呈现简单的形状。

## <a name="related-links"></a>相关链接

- [MacOpenTK (示例) ](/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (示例) ](/samples/xamarin/mac-samples/monomacgamewindow)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [开放工具包](https://opentk.net)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)