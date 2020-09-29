---
title: 演练：绑定 iOS Objective-C 库
description: 本文介绍了如何为现有的目标-C 库（InfColorPicker）创建 Xamarin iOS 绑定。 它介绍了一些主题，如编译静态目标-C 库、绑定和在 Xamarin iOS 应用程序中使用绑定。
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 82367d63d1cc7b52d69de52ee635e380cd68c382
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435659"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>演练：绑定 iOS Objective-C 库

> [!IMPORTANT]
> 我们当前正在调查 Xamarin 平台上的自定义绑定使用情况。 请参与[此调查](https://www.surveymonkey.com/r/KKBHNLT)，告诉我们将来应该进行哪些开发工作。

_本文介绍了如何为现有的目标-C 库（InfColorPicker）创建 Xamarin iOS 绑定。它介绍了一些主题，如编译静态目标-C 库、绑定和在 Xamarin iOS 应用程序中使用绑定。_

使用 iOS 时，可能会遇到需要使用第三方目标-C 库的情况。 在这些情况下，你可以使用 Xamarin _绑定项目_ 来创建 [c # 绑定](~/cross-platform/macios/binding/overview.md) ，该绑定将允许你在 Xamarin iOS 应用程序中使用库。

通常在 iOS 生态系统中，可以找到3种风格的库：

- 作为带有扩展名的预编译静态库文件及其 `.a` 标题 (s)  ( .h 文件) 。 例如， [Google 的分析库](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- 作为预编译框架。 这只是包含静态库和标头的文件夹，有时还包含扩展的其他资源 `.framework` 。 例如， [Google 的 Admob 是库](https://developers.google.com/admob/ios/download)。
- 作为源代码文件。 例如，包含仅包含 `.m` `.h` 目标 C 文件的库。

在第一个和第二个方案中，已有一个预编译的 CocoaTouch 静态库，因此在本文中，我们将重点介绍第三个方案。 请记住，在开始创建绑定之前，请始终检查随库一起提供的许可，以确保可随意绑定。

本文提供了使用开源 [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) 目标-c 项目作为示例创建绑定项目的分步演练，不过，本指南中的所有信息都可以改编为与任何第三方目标-c 库一起使用。 InfColorPicker 库提供了一个可重用的视图控制器，该控制器允许用户根据其 HSB 表示形式选择颜色，使颜色选择更易于用户使用。

[![IOS 上运行的 InfColorPicker 库示例](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

我们将介绍在 Xamarin 中使用此特定目标-C API 的所有必要步骤：

- 首先，我们将使用 Xcode 创建目标为 C 的静态库。
- 然后，将此静态库与 Xamarin 绑定。
- 接下来，通过自动生成某些 (，而不是由 Xamarin 绑定所需的所有必要 API 定义) ，来显示目标 Sharpie 如何减小工作负荷。
- 最后，我们将创建一个使用绑定的 Xamarin iOS 应用程序。

示例应用程序将演示如何使用强委托在 InfColorPicker API 和我们的 c # 代码之间进行通信。 了解如何使用强委托后，我们将介绍如何使用弱委托执行相同的任务。

## <a name="requirements"></a>要求

本文假设你已熟悉 Xcode 和目标 C 语言，并已阅读我们的 [绑定目标-c](~/cross-platform/macios/binding/index.md) 文档。 此外，必须满足以下要求才能完成显示的步骤：

- 在开发人员的计算机上，需要安装和配置**Xcode 和 IOS SDK** -Apple 的 Xcode 和最新的 ios API。
- **[Xcode 命令行工具](#Installing_the_Xcode_Command_Line_Tools)** -必须为当前安装的 Xcode 版本安装 Xcode 命令行工具 (参阅下面的安装详细信息) 。
- **Visual Studio for Mac 或 Visual studio** -应在开发计算机上安装和配置 Visual Studio for Mac 或 visual studio 的最新版本。 需要 Apple Mac 才能开发 Xamarin iOS 应用程序，并且在使用 Visual Studio 时，必须连接到 [Xamarin build 主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **最新版本的客观 Sharpie** -从 [此处](~/cross-platform/macios/binding/objective-sharpie/get-started.md)下载目标 Sharpie 工具的当前副本。 如果你已安装了客观 Sharpie，则可使用 `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"></a>

## <a name="installing-the-xcode-command-line-tools"></a>安装 Xcode 命令行工具

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

如上所述，我们将使用 Xcode 命令行工具 (`make` `lipo` 在本演练中专门和) 。 `make`命令是一个非常常见的 Unix 实用程序，它通过使用指定如何生成程序的_生成文件_来自动编译可执行程序和库。 `lipo`命令是 OS X 命令行实用程序，用于创建多体系结构文件; 它会将多个 `.a` 文件合并到一个文件中，该文件可由所有硬件体系结构使用。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

如上所述，在此演练中，我们将使用 Xcode 命令行工具在 **Mac 生成主机** 上 (专门 `make` 和 `lipo`) 。 `make`命令是一个非常常见的 Unix 实用工具，它将通过使用_生成文件_指定如何生成程序来自动编译可执行程序和库。 `lipo`命令是 OS X 命令行实用程序，用于创建多体系结构文件; 它会将多个 `.a` 文件合并到一个文件中，该文件可由所有硬件体系结构使用。

-----

根据 Apple 在[命令行中通过 XCODE FAQ](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)编写文档的内容，在 OS X 10.9 及更高版本中，"Xcode**首选项**" 对话框的 "**下载**" 窗格不支持下载命令行工具。

你需要使用以下方法之一来安装这些工具：

- **安装 Xcode** -安装 Xcode 时，它与所有命令行工具捆绑在一起。 在) 中安装 (OS X 10.9 填充程序中 `/usr/bin` ，可以将包含在 Xcode 中的任何工具映射 `/usr/bin` 到相应的工具。 例如， `xcrun` 命令允许您从命令行在 Xcode 内查找或运行任何工具。
- **终端应用程序** -从终端应用程序中，可以通过运行命令来安装命令行工具 `xcode-select --install` ：
  - 启动终端应用程序。
  - 键入， `xcode-select --install` 然后按 **enter**，例如：

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - 系统会要求安装命令行工具，请单击 "**安装**" 按钮： [ ![ 安装命令行工具](walkthrough-images/xcode01.png)](walkthrough-images/xcode01.png#lightbox)

  - 将从 Apple 的服务器下载和安装这些工具： [ ![ 下载工具](walkthrough-images/xcode02.png)](walkthrough-images/xcode02.png#lightbox)

- **适用于 Apple 开发人员的下载** -命令行工具包适用于 [Apple 开发人员下载](https://developer.apple.com/downloads/index.action) 网页。 用你的 Apple ID 登录，然后搜索并下载命令行工具： [ ![ 查找命令行工具](walkthrough-images/xcode03.png)](walkthrough-images/xcode03.png#lightbox)

安装命令行工具后，可以继续执行演练。

## <a name="walkthrough"></a>演练

在本演练中，我们将介绍以下步骤：

- **[创建静态库](#Creating_A_Static_Library)** -此步骤涉及创建 **InfColorPicker** 目标 C 代码的静态库。 静态库将具有 `.a` 文件扩展名，并将嵌入到库项目的 .net 程序集中。
- **[创建 Xamarin Ios 绑定项目](#Create_a_Xamarin.iOS_Binding_Project)** -一旦有了静态库，我们将使用它来创建 Xamarin iOS 绑定项目。 绑定项目由刚才创建的静态库和 c # 代码的形式组成，这些代码说明了如何使用目标 C API。 此元数据通常称为 API 定义。 我们将使用 **[客观 Sharpie](#Using_Objective_Sharpie)** 来帮助我们创建 API 定义。
- **[规范化 API 定义](#Normalize_the_API_Definitions)** -目标 Sharpie 执行帮助我们的工作非常出色，但它不能执行任何操作。 我们将讨论一些需要在 API 定义中进行的更改，然后才能使用这些更改。
- **[使用绑定库](#Using_the_Binding)** -最后，我们将创建一个 Xamarin iOS 应用程序，用于演示如何使用新创建的绑定项目。

现在，我们了解了涉及到的步骤，接下来，让我们继续执行本演练的其余部分。

<a name="Creating_A_Static_Library"></a>

## <a name="creating-a-static-library"></a>创建静态库

如果我们在 Github 中检查 InfColorPicker 的代码：

[![在 Github 中检查 InfColorPicker 的代码](walkthrough-images/image02.png)](walkthrough-images/image02.png#lightbox)

可以在项目中看到以下三个目录：

- **InfColorPicker** -该目录包含项目的目标 C 代码。
- **PickerSamplePad** -此目录包含一个示例 iPad 项目。
- **PickerSamplePhone** -此目录包含一个示例 iPhone 项目。

让我们从 [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) 下载 InfColorPicker 项目，并将其解压缩到所选的目录中。 正在打开项目的 Xcode 目标 `PickerSamplePhone` ，将在 Xcode 导航器中看到以下项目结构：

[![Xcode 导航器中的项目结构](walkthrough-images/image03.png)](walkthrough-images/image03.png#lightbox)

此项目通过直接将)  (在红色框中的 InfColorPicker 源代码添加到每个示例项目，从而实现代码重用。 示例项目的代码位于蓝色框中。 由于此特定项目不会向我们提供静态库，因此，我们需要创建一个 Xcode 项目来编译静态库。

第一步是将 InfoColorPicker 源代码添加到静态库中。 若要实现此目的，请执行以下操作：

1. 启动 Xcode。
2. 从 "**文件**" 菜单中选择 "**新建**  >  **项目 ...**"：

    [![开始新项目](walkthrough-images/image04.png)](walkthrough-images/image04.png#lightbox)
3. 选择 " **框架 & 库**"、 **Cocoa "触摸静态库** " 模板，然后单击 " **下一步** " 按钮：

    [![选择 Cocoa Touch 静态库模板](walkthrough-images/image05.png)](walkthrough-images/image05.png#lightbox)

4. 输入 `InfColorPicker` 作为 **项目名称** ，然后单击 " **下一步** " 按钮：

    [![输入项目名称的 InfColorPicker](walkthrough-images/image06.png)](walkthrough-images/image06.png#lightbox)
5. 选择保存项目的位置，然后单击 **"确定"** 按钮。
6. 现在，我们需要将 InfColorPicker 项目中的源添加到静态库项目。 由于静态库中已存在 **InfColorPicker** 文件 (默认情况下) ，因此 Xcode 将不允许我们覆盖它。 从 **查找**器中，导航到从 GitHub 解压缩的原始项目中的 InfColorPicker 源代码，复制所有 InfColorPicker 文件并将其粘贴到新的静态库项目中：

    [![复制所有 InfColorPicker 文件](walkthrough-images/image12.png)](walkthrough-images/image12.png#lightbox)

7. 返回到 Xcode，右键单击 **InfColorPicker** 文件夹，然后选择 **"将文件添加到" InfColorPicker ... "**：

    [![添加文件](walkthrough-images/image08.png)](walkthrough-images/image08.png#lightbox)

8. 在 "添加文件" 对话框中，导航到刚刚复制的 InfColorPicker 源代码文件，选择所有这些文件并单击 " **添加** " 按钮：

    [![选择 "全部"，然后单击 "添加" 按钮](walkthrough-images/image09.png)](walkthrough-images/image09.png#lightbox)

9. 源代码将复制到项目中：

    [![源代码将被复制到项目中](walkthrough-images/image10.png)](walkthrough-images/image10.png#lightbox)

10. 从 Xcode 项目导航器中，选择 **InfColorPicker** 文件，并注释掉最后两行 (由于此库的写入方式，不) 使用此文件：

    [![编辑 InfColorPicker 文件](walkthrough-images/image14.png)](walkthrough-images/image14.png#lightbox)

11. 我们现在需要检查库是否需要任何框架。 可以在自述文件或打开提供的示例项目之一中找到此信息。 此示例使用 `Foundation.framework` 、 `UIKit.framework` ，并 `CoreGraphics.framework` 添加它们。

12. 选择 **InfColorPicker 目标 > 生成阶段** ，并展开 "将 **二进制文件链接到库** " 部分：

    [![展开 "将二进制文件链接到库" 部分](walkthrough-images/image16b.png)](walkthrough-images/image16b.png#lightbox)

13. 使用 "" **+** 按钮打开对话框，以便添加上面列出的所需框架框架：

    [![添加上面列出的所需框架框架](walkthrough-images/image16c.png)](walkthrough-images/image16c.png#lightbox)

14. "将 **二进制文件链接到库** " 部分现在应如下图所示：

    [!["将二进制文件链接到库" 部分](walkthrough-images/image16d.png)](walkthrough-images/image16d.png#lightbox)

此时，我们已关闭，但我们并未完成此操作。 已创建静态库，但我们需要生成它以创建包含适用于 iOS 设备和 iOS 模拟器的所有所需体系结构的 Fat 二进制文件。

### <a name="creating-a-fat-binary"></a>创建 Fat 二进制文件

所有 iOS 设备都具有由一段时间内开发的 ARM 体系结构提供支持的处理器。 每个新的体系结构都添加了新指令和其他改进，同时仍保持向后兼容性。 iOS 设备具有 armv6、armv7、armv7s、arm64 指令集–尽管 [armv6 没有更多的使用](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模拟器不支持 ARM，而是 x86 和 x86_64 支持的模拟器。 这意味着必须为每个指令集提供库。

Fat 库是 `.a` 包含所有受支持的体系结构的文件。

创建 fat 二进制文件的过程分为三个步骤：

- 编译 ARM 7 & ARM64 的静态库版本。
- 编译静态库的 x86 和 x84_64 版本。
- 使用 `lipo` 命令行工具将两个静态库合并为一个。

虽然这三个步骤非常简单，但将来可能需要重复这些步骤，前提是目标-C 库收到更新或需要修复 bug。 如果决定自动执行这些步骤，它将简化 iOS 绑定项目的将来维护和支持。

有许多工具可用于自动执行此类任务-shell 脚本、 [rake](https://rake.rubyforge.org/)、 [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)和进行操作。 安装 Xcode 命令行工具时， `make` 还会安装，因此，这是将用于本演练的生成系统。 以下是可用于创建可在 iOS 设备上工作的多体系结构共享库和适用于任何库的模拟器的 **生成文件** ：

<!--markdownlint-disable MD010 -->
```makefile
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

在所选的纯文本编辑器中输入 **生成文件** 命令， **并通过项目名称更新** 部分。 还必须确保严格粘贴上述说明，并保留指令中的选项卡。

将具有名称 **生成** 文件的文件保存到与上面创建的 InfColorPicker Xcode 静态库相同的位置：

[![用名称生成文件保存文件](walkthrough-images/lib00.png)](walkthrough-images/lib00.png#lightbox)

打开你的 Mac 上的终端应用程序并导航到生成文件所在的位置。 键入 `make` 终端，按 **enter** ，将执行 **生成文件** ：

[![示例生成文件输出](walkthrough-images/lib01.png)](walkthrough-images/lib01.png#lightbox)

运行 "生成" 时，会看到大量文本滚动。 如果一切正常，你将看到 " **生成成功** " 字样，并且 `libInfColorPicker-armv7.a` `libInfColorPicker-i386.a` `libInfColorPickerSDK.a` 文件将复制到 **生成**文件所在的同一位置：

[![Armv7、libInfColorPicker 和 libInfColorPickerSDK 生成的 libInfColorPicker 文件的文件的文件。](walkthrough-images/lib02.png)](walkthrough-images/lib02.png#lightbox)

可以使用以下命令确认 Fat 二进制文件中的体系结构：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

这应显示以下内容：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

此时，我们已完成了 iOS 绑定的第一步，方法是使用 Xcode 和 Xcode 命令行工具和创建静态库 `make` `lipo` 。 让我们转到下一步，并使用 **Sharpie** 自动创建 API 绑定。

<a name="Create_a_Xamarin.iOS_Binding_Project"></a>

## <a name="create-a-xamarinios-binding-project"></a>创建 Xamarin iOS 绑定项目

在我们可以使用 **Sharpie** 实现绑定过程的自动化之前，我们需要创建一个 Xamarin IOS 绑定项目来容纳 API 定义 (我们将使用 **Sharpie** 来帮助我们构建) 并为我们创建 c # 绑定。

让我们执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for Mac。
1. 从 "**文件**" 菜单中，选择 "**新建**  >  **解决方案 ...**"：

    ![启动新解决方案](walkthrough-images/bind01.png)

1. 在 "新建解决方案" 对话框中，选择 "**库**" "  >  **iOS 绑定项目**"：

    ![选择 iOS 绑定项目](walkthrough-images/bind02.png)

1. 单击“下一步”按钮。

1. 输入 "InfColorPickerBinding" 作为 **项目名称** ，然后单击 " **创建** " 按钮以创建解决方案：

    ![输入 InfColorPickerBinding 作为项目名称](walkthrough-images/bind02a.png)

将创建该解决方案，并将包括两个默认文件：

![解决方案资源管理器中的解决方案结构](walkthrough-images/bind03.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 启动 Visual Studio。

1. 从 "**文件**" 菜单中，选择 "**新建**  >  **项目 ...**"：

    ![开始新项目](walkthrough-images/bind01vs.png "开始新项目")

1. 在 "新建项目" 对话框中，选择 " **Visual c # > iPhone & iPad > IOS 绑定库 (Xamarin) **：

    [![选择 iOS 绑定库](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. 输入 "InfColorPickerBinding" 作为 **名称** ，然后单击 **"确定** " 按钮以创建解决方案。

将创建该解决方案，并将包括两个默认文件：

![解决方案资源管理器中的解决方案结构](walkthrough-images/bind03vs.png)

-----

- **ApiDefinition.cs** -此文件将包含定义如何在 c # 中包装目标 C API 的协定。
- **Structs.cs** -此文件将包含接口和委托所需的任何结构或枚举值。

稍后将在本演练中使用这两个文件。 首先，需要将 InfColorPicker 库添加到绑定项目。

### <a name="including-the-static-library-in-the-binding-project"></a>将静态库包含在绑定项目中

现在我们已准备好基本绑定项目，需要为 **InfColorPicker** 库添加上面创建的 Fat 二进制库。

按照以下步骤添加库：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击 "Solution Pad 中的" **本机引用** "文件夹，然后选择" **添加本机引用**"：

    ![添加本机引用](walkthrough-images/bind04a.png)

1. 导航到前面 () 的 Fat 二进制文件 `libInfColorPickerSDK.a` ，然后按 " **打开** " 按钮：

    ![选择 libInfColorPickerSDK 文件](walkthrough-images/bind05.png)
1. 此文件将包含在项目中：

    ![包含文件](walkthrough-images/bind04.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 将 `libInfColorPickerSDK.a` 从 **Mac 生成主机** 复制并粘贴到绑定项目。

1. 右键单击该项目，然后选择 " **添加 > 现有项 ...**"：

    ![添加现有文件](walkthrough-images/bind04vs.png)

1. 导航到 `libInfColorPickerSDK.a` 并按 " **添加** " 按钮：

    ![添加 libInfColorPickerSDK](walkthrough-images/bind05vs.png)

1. 此文件将包含在项目中。

-----

将 **.** 文件添加到项目中时，Xamarin 会自动将该文件的 **生成操作** 设置为 **ObjcBindingNativeLibrary**，并创建一个名为的特殊文件 `libInfColorPickerSDK.linkwith.cs` 。

此文件包含 `LinkWith` 属性，该属性告诉 Xamarin iOS 如何处理刚刚添加的静态库。 以下代码片段显示了此文件的内容：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`特性标识项目的静态库和一些重要的链接器标志。

接下来需要为 InfColorPicker 项目创建 API 定义。 出于本演练的目的，我们将使用客观 Sharpie 生成文件 **ApiDefinition.cs**。

<a name="Using_Objective_Sharpie"></a>

## <a name="using-objective-sharpie"></a>使用目标 Sharpie

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

客观 Sharpie 是 (Xamarin) 提供的命令行工具，可帮助创建将第三方目标 C 库绑定到 c # 所需的定义。 在本部分中，我们将使用客观 Sharpie 为 InfColorPicker 项目创建初始 **ApiDefinition.cs** 。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

客观 Sharpie 是 (Xamarin) 提供的命令行工具，可帮助创建将第三方目标 C 库绑定到 c # 所需的定义。 在本部分中，我们将在 **Mac 生成主机** 上使用客观 Sharpie 为 InfColorPicker 项目创建初始 **ApiDefinition.cs** 。

-----

若要开始，请下载目标 Sharpie 安装程序文件，如本 [指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)中所述。 运行安装程序并按照安装向导中的所有屏幕提示操作，在开发计算机上安装目标 Sharpie。

成功安装目标 Sharpie 后，请启动终端应用并输入以下命令，获取有关它提供的所有工具的帮助，以帮助进行绑定：

```bash
sharpie -help
```

如果执行上面的命令，则将生成以下输出：

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

出于本演练的目的，我们将使用以下目标 Sharpie 工具：

- **xcode** -此工具提供我们当前 xcode 安装的相关信息，以及我们安装的 IOS 和 Mac api 版本。 稍后我们将在生成绑定时使用此信息。
- **bind** -我们将使用此工具将 InfColorPicker 项目中的 **.h** 文件分析为初始 **ApiDefinition.cs** 和 **StructsAndEnums.cs** 文件。

若要获取特定目标 Sharpie 工具的帮助，请输入工具的名称和 `-help` 选项。 例如， `sharpie xcode -help` 返回以下输出：

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

在可以开始绑定过程之前，需要通过在终端中输入以下命令来获取有关当前已安装的 Sdk 的信息 `sharpie xcode -sdks` ：

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

在上面，我们可以看到我们的 `iphoneos9.3` 计算机上已安装了 SDK。 利用此信息，我们可以将 InfColorPicker 项目 `.h` 文件分析到初始 **ApiDefinition.cs** 和 `StructsAndEnums.cs` InfColorPicker 项目中。

在终端应用中输入以下命令：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中 `[full-path-to-project]` 是 **InfColorPicker** Xcode 项目文件位于计算机上的目录的完整路径，[iphone-os] 是我们安装的 iOS SDK，如命令所述 `sharpie xcode -sdks` 。 请注意，在此示例中，我们已将** \* .h**作为参数传递，其中包括此目录中的*所有*头文件-通常不应执行此操作，而是仔细阅读标头文件以查找引用所有其他相关文件的顶级 **.h**文件，只需将该文件传递给目标 Sharpie。 

> [!TIP] 
> 对于 `-scope` 参数，传递包含要绑定的标头的文件夹。 如果没有 `-scope` 参数，客观 Sharpie 将尝试为导入的任何 IOS SDK 标头生成绑定，例如 `#import <UIKit.h>` ，生成一个大型定义文件，在编译绑定项目时可能会生成错误。 对于 `-scope` 参数集，客观 Sharpie 不会为作用域内文件夹之外的任何标头生成绑定。 

在终端中将生成以下 [输出](walkthrough-images/os05.png) ：

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

并且将在目录中创建 **InfColorPicker.enums.cs** 和 **InfColorPicker.cs** 文件：

[![InfColorPicker.enums.cs 和 InfColorPicker.cs 文件](walkthrough-images/os06.png)](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在上面创建的绑定项目中打开这两个文件。 复制 **InfColorPicker.cs** 文件的内容并将其粘贴到 **ApiDefinition.cs** 文件中，将现有 `namespace ...` 代码块替换为 **InfColorPicker.cs** 文件的内容， (保持 `using` 语句原样) ：

![InfColorPickerControllerDelegate 文件](walkthrough-images/os07.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在上面创建的绑定项目中打开这两个文件。 将 **InfColorPicker.cs** 文件的内容复制 (从 **Mac 生成主机**) ，并将其粘贴到 **ApiDefinition.cs** 文件中，将现有 `namespace ...` 代码块替换为 **InfColorPicker.cs** 文件的内容 (保持 `using` 语句原样) 。

-----

<a name="Normalize_the_API_Definitions"></a>

## <a name="normalize-the-api-definitions"></a>规范化 API 定义

目标 Sharpie 有时会转换问题 `Delegates` ，因此我们需要修改接口的定义 `InfColorPickerControllerDelegate` ，并将 `[Protocol, Model]` 行替换为以下内容：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

这样定义如下所示：

[![定义](walkthrough-images/os11.png)](walkthrough-images/os11.png#lightbox)

接下来，我们对文件的内容执行相同的操作 `InfColorPicker.enums.cs` ，将这些内容复制并粘贴到 `StructsAndEnums.cs` 文件中，使 `using` 语句保持不变：

[![StructsAndEnums.cs 文件的内容 ](walkthrough-images/os09.png)](walkthrough-images/os09.png#lightbox)

你还可能会发现，目标 Sharpie 已将绑定注释为 `[Verify]` 属性。 这些属性指示您应该通过将绑定与原始 C/目标-C 声明进行比较来验证目标 Sharpie 是否执行了正确的操作， (将在绑定声明) 的注释中提供。 验证绑定后，应删除 "验证" 属性。 有关详细信息，请参阅 [验证](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) 指南。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

此时，我们的绑定项目应已完成，可以开始生成。 让我们来构建绑定项目，并确保最终没有错误：

[生成绑定项目并确保没有错误](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

此时，我们的绑定项目应已完成，可以开始生成。 让我们来构建绑定项目，并确保最终不会有任何错误。

-----

<a name="Using_the_Binding"></a>

## <a name="using-the-binding"></a>使用绑定

按照以下步骤创建一个示例 iPhone 应用程序，以使用上面创建的 iOS 绑定库：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. **创建 Xamarin Ios 项目** -向解决方案添加名为 **InfColorPickerSample** 的新的 Xamarin iOS 项目，如以下屏幕截图所示：

    ![添加单一视图应用](walkthrough-images/use01.png)

    ![设置标识符](walkthrough-images/use01a.png)

1. **将引用添加到绑定项目** -更新 **InfColorPickerSample** 项目，使其具有对 **InfColorPickerBinding** 项目的引用：

    ![添加对绑定项目的引用](walkthrough-images/use02.png)

1. **创建 IPhone 用户界面**-双击**InfColorPickerSample**项目中的**Mainstoryboard.storyboard**文件可在 iOS 设计器中对其进行编辑。 将一个 **按钮** 添加到视图并调用它 `ChangeColorButton` ，如下所示：

    ![向视图添加按钮](walkthrough-images/use03.png)

1. **添加 xib** -InfColorPicker InfColorPickerView 库包含 **xib** 文件。 Xamarin 不会在绑定项目中包含 **xib** ，这将导致示例应用程序中出现运行时错误。 解决此问题的方法是将 **xib** 文件添加到 Xamarin iOS 项目。 选择 "Xamarin" 项目，右键单击并选择 " **添加" >**"添加文件"，并添加 **xib** 文件，如以下屏幕截图所示：

    ![添加 InfColorPickerView. xib](walkthrough-images/use04.png)

1. 系统提示时，将 **xib** 文件复制到项目中。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **创建 Xamarin Ios 项目**-使用 "**单一视图应用**" 模板添加名为**InfColorPickerSample**的新 Xamarin 项目：

    [![iOS 应用 (Xamarin) 项目](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![选择模板](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **将引用添加到绑定项目** -更新 **InfColorPickerSample** 项目，使其具有对 **InfColorPickerBinding** 项目的引用：

    ![将引用添加到绑定项目](walkthrough-images/use02vs.png)

1. **创建 IPhone 用户界面**-双击**InfColorPickerSample**项目中的**Mainstoryboard.storyboard**文件可在 iOS 设计器中对其进行编辑。 将一个 **按钮** 添加到视图并调用它 `ChangeColorButton` ，如下所示：

    ![创建 iPhone 用户界面](walkthrough-images/use03vs.png)

1. **添加 xib** -InfColorPicker InfColorPickerView 库包含 **xib** 文件。 Xamarin 不会在绑定项目中包含 **xib** ，这将导致示例应用程序中出现运行时错误。 解决此问题的方法是将 **xib** 文件添加到 **Mac 生成主机**的 Xamarin iOS 项目。 选择 "Xamarin" 项目，右键单击并选择 "**添加**  >  **现有项 ...**"，然后添加**xib**文件。

-----

接下来，让我们快速了解目标-C 中的协议以及如何在绑定和 c # 代码中处理它们。

### <a name="protocols-and-xamarinios"></a>协议和 Xamarin

在目标-C 中，协议定义 (或消息) 的方法，这些方法可在某些情况下使用。 从概念上讲，它们与 c # 中的接口非常类似。 目标 C 协议和 c # 接口之间的一个主要区别是，协议可以具有可选的方法-类不需要实现的方法。 目标-C 使用 @optional 关键字来指示哪些方法是可选的。 有关协议的详细信息，请参阅 [事件、协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController** 有一个此类协议，如以下代码片段所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

**InfColorPickerController**使用此协议通知客户端用户已选择了新颜色并且**InfColorPickerController**已完成。 目标 Sharpie 映射了此协议，如以下代码片段所示：

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

编译绑定库时，Xamarin 会创建一个名为的抽象基类 `InfColorPickerControllerDelegate` ，该基类用虚方法实现此接口。

可以通过两种方法在 Xamarin iOS 应用程序中实现此接口：

- **强委托** -使用强委托涉及到创建一个类，该类用于子类 `InfColorPickerControllerDelegate` 并重写适当的方法。 **InfColorPickerController** 将使用此类的实例与客户端通信。
- **弱委托** -弱委托是一种略有不同的技术，它涉及在某个类 (上创建公共方法，如 `InfColorPickerSampleViewController`) 然后通过属性将该方法公开给 `InfColorPickerDelegate` 协议 `Export` 。

强委托提供 Intellisense、类型安全性和更好的封装。 出于这些原因，应该使用强委托，而不是弱委托。

在本演练中，我们将讨论这两种方法：首先实现强委托，然后说明如何实现弱委托。

### <a name="implementing-a-strong-delegate"></a>实现强委托

使用强委托完成 Xamarin iOS 应用程序以响应 `colorPickerControllerDidFinish:` 消息：

**子类 InfColorPickerControllerDelegate** -将新类添加到名为的项目 `ColorSelectedDelegate` 。 编辑类，使其具有以下代码：

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
  public class ColorSelectedDelegate:InfColorPickerControllerDelegate
  {
    readonly UIViewController parent;

    public ColorSelectedDelegate (UIViewController parent)
    {
      this.parent = parent;
    }

    public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
    {
      parent.View.BackgroundColor = controller.ResultColor;
      parent.DismissViewController (false, null);
    }
  }
}
```

Xamarin 将通过创建一个名为的抽象基类来绑定目标 C 委托 `InfColorPickerControllerDelegate` 。 将此类型划分为子类并重写 `ColorPickerControllerDidFinish` 方法以访问的 `ResultColor` 属性的值 `InfColorPickerController` 。

**创建 ColorSelectedDelegate 的实例** -我们的事件处理程序将需要 `ColorSelectedDelegate` 在上一步中创建的类型的实例。 编辑类 `InfColorPickerSampleViewController` ，并将以下实例变量添加到类：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 变量** -若要确保 `selector` 是有效的实例，请更新中的 `ViewDidLoad` 方法 `ViewController` 以匹配以下代码片段：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**实现方法 HandleTouchUpInsideWithStrongDelegate** -接下来，在用户接触 **ColorChangeButton**时实现事件处理程序。 编辑 `ViewController` 并添加以下方法：

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

首先 `InfColorPickerController` ，我们通过静态方法获取实例，并通过属性使该实例知道我们的强委托 `InfColorPickerController.Delegate` 。 此属性是由目标 Sharpie 自动生成的。 最后，我们调用 `PresentModallyOverViewController` 来显示视图， `InfColorPickerSampleViewController.xib` 以便用户可以选择颜色。

**运行应用程序** ，此时我们已经完成了所有代码。 如果运行应用程序，你应该能够更改的背景色， `InfColorColorPickerSampleView` 如以下屏幕截图所示：

[![运行应用程序](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

恭喜！ 此时，您已成功创建并绑定了用于 Xamarin iOS 应用程序的目标-C 库。 接下来，让我们了解如何使用弱委托。

### <a name="implementing-a-weak-delegate"></a>实现弱委托

Xamarin 还允许在派生自的任何类中实现协议方法， `NSObject` 并使用修饰方法 `ExportAttribute` ，然后提供相应的选择器，而不是将绑定到特定委托的目标-C 协议的类。 采用此方法时，请将类的实例分配给 `WeakDelegate` 属性，而不是分配给 `Delegate` 属性。 弱委托使你可以灵活地将委托类向下移动到不同的继承层次结构。 让我们看看如何在我们的 Xamarin iOS 应用程序中实现和使用弱委托。

**创建 TouchUpInside 的事件处理程序** -让我们为 " `TouchUpInside` 更改背景色" 按钮的事件创建新的事件处理程序。 此处理程序将填充与 `HandleTouchUpInsideWithStrongDelegate` 我们在上一节中创建的处理程序相同的角色，但将使用弱委托而不是强委托。 编辑类 `ViewController` ，并添加以下方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Update ViewDidLoad** -必须更改， `ViewDidLoad` 使其使用刚刚创建的事件处理程序。 编辑 `ViewController` 和更改 `ViewDidLoad` 为类似于以下代码片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**处理 colorPickerControllerDidFinish： Message** - `ViewController` 完成后，iOS 会将消息发送 `colorPickerControllerDidFinish:` 到 `WeakDelegate` 。 我们需要创建一个可处理此消息的 c # 方法。 为此，我们将创建一个 c # 方法，然后使用将其修饰 `ExportAttribute` 。 编辑 `ViewController` ，并将以下方法添加到类：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

运行应用程序。 它现在应完全按照之前的方式运行，但它使用弱委托，而不是强委托。 此时，你已成功完成本演练。 你现在应该已经了解了如何创建和使用 Xamarin iOS 绑定项目。

## <a name="summary"></a>总结

本文逐步介绍了创建和使用 Xamarin iOS 绑定项目的过程。 首先，我们介绍了如何将现有的目标 C 库编译为静态库。 然后我们介绍了如何创建 Xamarin iOS 绑定项目，以及如何使用客观 Sharpie 为目标-C 库生成 API 定义。 我们讨论了如何更新和调整生成的 API 定义，使其适用于公共使用。 完成 Xamarin iOS 绑定项目后，我们将在 Xamarin iOS 应用程序中使用该绑定，重点介绍使用强委托和弱委托。

## <a name="related-links"></a>相关链接

- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [适用于目标的 Xamarin-C 开发人员](~/ios/get-started/objective-c-developers/index.md)
- [框架设计准则](/dotnet/standard/design-guidelines/)