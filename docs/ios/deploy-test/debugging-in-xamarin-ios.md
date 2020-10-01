---
title: 调试 Xamarin.iOS 应用
description: 本文档介绍如何在 Visual Studio for Mac 或 Visual Studio 2019 中使用调试器调试 Xamarin.iOS 应用程序，包括设置断点、无线调试等。
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 8d4b60a2e8e53b5b3f97a781b7f1888239541f1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431872"
---
# <a name="debugging-xamarinios-apps"></a>调试 Xamarin.iOS 应用

_可以使用 Visual Studio for Mac 或 Visual Studio 中的内置调试程序调试 Xamarin.iOS 应用程序。_

需要调试可能与 Xamarin.iOS 项目关联的 C、C++ 或 Objective C 代码时，使用 Visual Studio for Mac 的本机调试支持调试 C# 和其他托管语言代码，然后使用 [LLDB](https://lldb.llvm.org/tutorial.html)。

> [!NOTE]
> 在“调试”模式下编译应用时，Xamarin.iOS 生成的应用不仅速度慢，而且还很大，因为必须检测所有代码行。 发布之前，请确保创建一个“发布”版本。

Xamarin.iOS 调试器集成到了 IDE 中，它允许开发人员在模拟器或设备上调试使用 Xamarin.iOS 支持的任何托管语言生成的 Xamarin.iOS 应用程序。

Xamarin.iOS 调试器使用 [Mono 软调试器](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)，这意味着生成的代码和 Mono 运行时将与 IDE 配合提供调试体验。 这与 LLDB 或 MDB 等硬调试器不同，硬调试器不需要调试程序方面的知识或配合，即可控制程序。

## <a name="setting-breakpoints"></a>设置断点

准备好开始调试应用程序时，第一步是[设置应用程序的断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)。 单击编辑器中想要中断的代码行数旁的边缘区域，即可实现此操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![设置断点](debugging-in-xamarin-ios-images/debugging1.png)](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![设置断点](debugging-in-xamarin-ios-images/debugging1a.png)](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

可转到“Breakpoints pad”面板，查看代码中已设置的所有断点：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![断点板](debugging-in-xamarin-ios-images/image0a.png)](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 如果“Breakpoints pad”未自动显示，可以依次选择“视图”>“调试窗口”>“断点”调出它

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![断点板](debugging-in-xamarin-ios-images/image0.png)](debugging-in-xamarin-ios-images/image0.png#lightbox)

 如果“Breakpoints pad”未自动显示，可以依次选择择“调试”>“窗口”>“断点”调出它

-----

开始调试任何应用程序之前，应始终确保配置已设为“调试”，因为其中有一套有用的工具，可支持调试（比如断点），使用数据可视化工具以及查看调用堆栈：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在模拟器上调试](debugging-in-xamarin-ios-images/debugging7.png)](debugging-in-xamarin-ios-images/debugging7.png#lightbox)
[![在物理设备上调试](debugging-in-xamarin-ios-images/debugging7a.png)](debugging-in-xamarin-ios-images/debugging7a.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在模拟器上调试](debugging-in-xamarin-ios-images/debugging7c.png)](debugging-in-xamarin-ios-images/debugging7c.png#lightbox)
[![在物理设备上调试](debugging-in-xamarin-ios-images/debugging7d.png)](debugging-in-xamarin-ios-images/debugging7d.png#lightbox)

-----

## <a name="start-debugging"></a>开始调试
若要开始调试，在 IDE 中选择目标设备或类似目标：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![选择目标设备](debugging-in-xamarin-ios-images/debugging7b.png)](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![选择目标设备](debugging-in-xamarin-ios-images/debugging7e.png)](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----

然后通过按“播放”按钮部署应用程序。

命中断点时，代码会以黄色突出显示：

[![代码会以黄色突出显示](debugging-in-xamarin-ios-images/image2.png)](debugging-in-xamarin-ios-images/image2.png#lightbox)

此时，可使用调试工具（如检查对象的值）获取代码中所发生情况的相关详细信息：

[![显示颜色值](debugging-in-xamarin-ios-images/image3.png)](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>条件断点

还可以设置规则，规定应该发生断点的情况，称为添加条件断点。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要设置条件断点，请访问“断点属性”窗口。具体有以下两种操作方法：

- 若要添加新的条件断点，请右键单击编辑器边缘（即要设置断点的代码行号左侧），再选择“新建断点”：

  [![选择“新建断点”](debugging-in-xamarin-ios-images/image4.png)](debugging-in-xamarin-ios-images/image4.png#lightbox)

- 若要向现有断点添加条件，请右键单击该断点并选择“断点属性”，或如下图所示在“断点”面板中选择“属性”按钮：

  [![断点板](debugging-in-xamarin-ios-images/image5.png)](debugging-in-xamarin-ios-images/image5.png#lightbox)

然后可输入想要断点发生的条件：

[![输入断点发生的条件](debugging-in-xamarin-ios-images/image6.png)](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中设置条件断点，请先[设置常规断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)。 右键单击断点，以显示其上下文菜单：

 [![断点上下文菜单](debugging-in-xamarin-ios-images/image4vs.png)](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

选择“条件...”显示“断点设置”菜单：

 [![“断点设置”菜单](debugging-in-xamarin-ios-images/image6vs.png)](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

可在此处输入想要断点发生的条件

有关在 Visual Studio 早期版本中使用断点条件的详细信息，请参阅关于本主题的 [Visual Studio文档](/visualstudio/debugger/using-breakpoints)。

-----

## <a name="navigating-through-code"></a>通过代码导航

到达断点时，调试工具使用户能够控制该程序的执行。 IDE 将显示四个按钮，使用户能够逐行运行代码。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，它们将显示如下：

 [![开发者可通过调试工具控制程序执行](debugging-in-xamarin-ios-images/image7.png)](debugging-in-xamarin-ios-images/image7.png#lightbox)

这些是：

- “播放/停止” -此按钮开始/停止执行代码，直至下一个断点处。
- **单步跳过** - 此按钮执行下一行代码。 如果下一行是函数调用，“单步跳过”将执行该函数，并在该函数后的下一行代码停止。
- **单步执行** - 此按钮也执行下一行代码。 如果下一行是函数调用，“单步执行”将在该函数的第一行停止，允许继续进行函数的逐行调试。 如果下一行不是函数，其行为与“单步跳过”相同。
- **跳出** - 此按钮返回到调用当前函数的代码行。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，它们如下所示：

[![开发者可通过调试工具控制程序执行](debugging-in-xamarin-ios-images/image7vs.png)](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

这些是：

- “播放/停止” -此按钮开始/停止执行代码，直至下一个断点处。
- “单步跳过 (F11)” - 此按钮执行下一行代码。 如果下一行是函数调用，“单步跳过”将执行该函数，并在该函数后的下一行代码停止。
- “单步执行 (F10)” - 此按钮也执行下一行代码。 如果下一行是函数调用，“单步执行”将在该函数的第一行停止，允许继续进行函数的逐行调试。 如果下一行不是函数，其行为与“单步跳过”相同。
- “跳出 (Shift+F11)” - 此按钮返回到调用当前函数的行。

有关调试的更多详细文档，请参阅[使用 Visual Studio 调试器浏览代码](/visualstudio/debugger/navigating-through-code-with-the-debugger)。

-----

### <a name="breakpoints"></a>断点

需要指出的是，iOS 仅给应用程序几秒 (10) 时间在“应用程序”委派中启动并完成 `FinishedLaunching` 方法。 如果应用程序在 10 秒内未完成此方法，则 iOS 将终止该进程。

这意味着，在应用程序的启动代码上设置断点几乎是不可能的。 如果想要调试启动代码，应延迟其某些初始化，并将其放入已调用计时器的方法，或在 FinishedLaunching 终止后以其他形式回调方法。

## <a name="device-diagnostics"></a>设备诊断

如果设置调试器时出错，可以通过向“项目选项”中的其他 mtouch 参数添加“-v -v -v”启用详细诊断。 这将打印设备控制台详细的错误信息。

 <a name="WiFi_Debugging"></a>

## <a name="wireless-debugging"></a>无线调试

Xamarin.iOS 中的默认设置是通过 USB 连接，在设备上调试应用程序。 有时为了开发 ExternalAccessory 驱动的应用程序，可能需要 USB 设备测试电缆的插入/拔出。 在这些情况下，可以通过无线网络进行调试。

有关无线部署和调试的详细信息，请参阅[无线部署](~/ios/deploy-test/wireless-deployment.md)指南。

<a name="Technical_Details"></a>

## <a name="technical-details"></a>技术详细信息

Xamarin.iOS 使用新的 Mono 软调试器。 与标准 Mono 调试器不同，后者是使用操作系统界面控制单独进程的程序，而软调试器则是通过使 Mono 运行时通过网络协议公开调试功能来进行工作。

启动时，要进行调试的应用程序将联系调试器，然后调试器开始运行。 在 Xamarin.iOS for Visual Studio 中，Xamarin Mac Agent 充当应用程序（Visual Studio 中）与调试器之间的中间程序。

在设备上运行时，此软调试器需要协作调试方案。 这意味着调试时二进制文件生成版本将变复杂，因为需要检测代码才能在每个序列点包含额外的代码，以便支持调试。

<a name="Accessing_the_Console"></a>

## <a name="accessing-the-console"></a>访问控制台

崩溃日志和控制台类输出将发送到 iPhone 控制台。 可以通过 Xcode 使用“管理器”，然后从管理器选择设备来访问此控制台。

或者，如果不想启动 Xcode，可以使用 Apple 的 iPhone 配置实用程序直接访问控制台。 此操作有额外的好处，如果用户正在域中调试问题，则可从 Windows 计算机访问控制台日志。

对于 Visual Studio 用户，虽然输出窗口中有多个可用日志，但应切换到 Mac 更全面地了解详细日志。

-----

<a name="Debugging_Mono's_Class_Libraries"></a>

## <a name="debugging-monos-class-libraries"></a>调试 Mono 类库

Xamarin.iOS 随附用于 Mono 类库的源代码，可使用此代码在调试器中单步执行，查看内部工作原理。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

由于此功能在调试过程中会占用更多内存，因此默认禁用。

若要启用此功能，请确保在“Visual Studio for Mac”>“首选项”>“调试程序”菜单下已取消选择“仅调试项目代码；不单步执行框架代码”选项，如下图所示：

[![调试 Mono 的类库](debugging-in-xamarin-ios-images/debugging6.png)](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中调试类库，必须在“调试”>“选项”菜单下禁用“仅我的代码”。 在“调试”>“常规”节点中，取消选中“启用‘仅我的代码’”复选框：

[![调试 Mono 的类库](debugging-in-xamarin-ios-images/debugging6vs.png)](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

一旦执行此操作，便可以启动应用程序并单步执行 Mono 的任何核心类库。

## <a name="related-links"></a>相关链接

- [使用 Xamarin 进行调试](/visualstudio/mac/debugging/)
- [数据可视化效果](/visualstudio/mac/data-visualizations/)
- [设置断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)
- [逐行执行代码](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)
- [将信息输出到日志窗口](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)