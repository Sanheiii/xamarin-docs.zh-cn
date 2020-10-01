---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 结合使用 C/C++ 库和 Xamarin
description: 借助 Xamarin 和 C#，可以使用 Visual Studio for Mac 生成跨平台 C/C++ 代码并将其集成到适用于 Android 和 iOS 的移动应用中。 本文介绍如何在 Xamarin 应用中设置和调试 C++ 项目。
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 80878ec36eb319ff6c798788493469793efdcf56
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457986"
---
# <a name="use-cc-libraries-with-xamarin"></a>结合使用 C/C++ 库和 Xamarin

## <a name="overview"></a>概述

借助 Xamarin，开发人员能够通过 Visual Studio 创建跨平台的本机移动应用。 通常，C# 绑定用于向开发人员公开现有的平台组件。 但是，在某些情况下，Xamarin 应用需要使用现有的基本代码。 有时，团队没有足够的时间、预算或资源，无法将经过充分测试且高度优化的大型基本代码移植到 C#。

[用于跨平台移动开发的 Visual C++](/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) 支持将 C/C++ 和 C# 代码生成为同一解决方案的一部分，并提供许多优势，包括统一的调试体验。 Microsoft 已经以这种方式使用 C/C++ 和 Xamarin 交付了 [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) 和 [Pix Camera](https://www.microsoft.com/microsoftpix) 等应用。

但是，在某些情况下，需要（或要求）保留现有的 C/C++ 工具和进程，使库代码与应用程序不具有相关性，从而将库视为类似于第三方组件。 在这些情况下，挑战就不仅仅是向 C# 公开相关成员，而是将库作为依赖项进行管理。 当然，还要使这个过程尽可能地实现自动化。  

本文概述了这种情况的高级方法，并演练了一个简单的示例。

## <a name="background"></a>背景

C/C++ 被视为一种跨平台语言，但必须格外小心，确保源代码确实具有跨平台的属性，只使用所有目标编译器都支持的 C/C++，并包含很少或不包含有条件包含的平台或特定于编译器的代码。

最终，代码必须在所有目标平台上都能编译并成功运行，因此，这可以归结为在所有目标平台（和编译器）之间具有通用性。 各种编译器之间的细微差别仍然可能引起问题，因此在每个目标平台上进行全面测试（最好是自动化测试）变得越来越重要。  

## <a name="high-level-approach"></a>高级方法

下图说明了将 C/C++ 源代码转换为跨平台 Xamarin 库（该库通过 NuGet 共享，然后开发人员在 Xamarin.Forms 应用中使用）的方法的四个阶段。

![结合使用 C/C++ 和 Xamarin 的高级方法](images/cpp-steps.jpg)

四个阶段分别为：

1. 将 C/C++ 源代码编译为特定于平台的本机库。
2. 使用 Visual Studio 解决方案包装本机库。
3. 为 .NET 包装器打包并推送 NuGet 包。
4. 从 Xamarin 应用中使用 NuGet 包。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>阶段 1：将 C/C++ 源代码编译为特定于平台的本机库

此阶段的目标是创建可由 C# 包装器调用的本机库。 具体相关性视开发人员的情况而定。 对于在这种常见情况下可以使用的许多工具和过程，本文将不详述。 要考虑的关键点是使 C/C++ 基本代码与任何本地包装器代码保持同步、进行足够的单元测试以及自动生成。 

本演练中的库是使用 Visual Studio Code 和附带的 shell 脚本创建的。 [Mobile CAT GitHub 存储库](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)中提供了此演练的扩展版本，它更深入地讨论了示例的这一部分。 在这种情况下，本机库被视为第三方依赖项，但是此阶段是根据上下文进行说明的。

为简单起见，本演练只针对一小部分的体系结构。 对于 iOS，它使用 lipo 实用工具从特定于体系结构的单个二进制文件创建单个胖二进制文件。 Android 将使用带有 .so 扩展名的动态二进制文件，而 iOS 将使用带有 .a 扩展名的静态胖二进制文件。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>阶段 2：使用 Visual Studio 解决方案包装本机库

下一步是包装本机库，以便可以从 .NET 轻松使用它们。 这是通过包含四个项目的 Visual Studio 解决方案完成的。 共享项目包含通用代码。 面向 Xamarin.Android、Xamarin.iOS 和 .NET Standard 的每个项目都允许以与平台无关的方式引用库。

包装器使用 Paul Betts 介绍的[诱饵和切换技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)。 这并不是唯一的方法，但使用它可以轻松地引用库，并且无需在使用的应用程序中显式管理特定于平台的实现。 技巧本质上是确保目标（.NET Standard、Android、iOS）共享相同的命名空间、程序集名称和类结构。 由于 NuGet 将始终偏爱特定于平台的库，因此在运行时从不使用 .NET Standard 版本。

此步骤中的大部分工作将集中于使用 P/Invoke 调用本机库方法以及管理对基础对象的引用。 目标是向用户公开库的功能，同时抽象出任何复杂性。 Xamarin.Forms 开发人员不需要具备非托管库的内部运作相关知识。 它的使用方式应该类似于任何其他托管 C# 库。

最终，此阶段的输出是一组 .NET 库（每个目标一个）以及一个 nuspec 文档，该文档包含了在下一步中生成包所需的信息。

**阶段 3：为 .NET 包装器打包并推送 NuGet 包**

第三个阶段是使用上一步中的生成项目创建 NuGet 包。 此步骤创建的 NuGet 包可以从 Xamarin 应用中使用。 本演练使用本地目录作为 NuGet 源。 在生产环境中，此步骤应将包发布到公共或专用 NuGet 源，并且应完全自动完成。

**阶段 4：从 Xamarin.Forms 应用中使用 NuGet 包**

最后一步是从 Xamarin.Forms 应用中引用并使用 NuGet 包。 这需要在 Visual Studio 中配置 NuGet 源以使用上一步中定义的源。

配置源后，需要在跨平台 Xamarin.Forms 应用中的每个项目中引用该包。 “偷梁换柱的技巧”提供了相同的接口，因此可以使用在单个位置定义的代码来调用本机库功能。

源代码存储库包含一个[延伸阅读列表](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)，其中包含的文章介绍如何在 Azure DevOps 上设置专用 NuGet 源，以及如何将包推送到该源。 与本地目录相比，尽管需要的设置时间要多一些，但这种类型的源更适合用于团队开发。

## <a name="walk-through"></a>演练

提供的步骤特定于 Visual Studio for Mac，但该结构也适用于 Visual Studio 2017。

### <a name="prerequisites"></a>先决条件

为执行这些步骤，开发人员需要准备：

- [NuGet 命令行 (CLI)](/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [Visual Studio for Mac ](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 若要将应用部署到 iPhone，需要具有有效的 [Apple 开发者帐户](https://developer.apple.com/)。

## <a name="creating-the-native-libraries-stage-1"></a>创建本机库（阶段 1）

本机库功能基于[演练：创建和使用静态库 (C++)](/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017) 中的示例。

本演练将跳过生成本机库的第一个阶段，因为在此场景中，该库是作为第三方依赖项提供的。 预编译的本机库随[示例代码](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)一起提供，或者可以直接[下载](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="working-with-the-native-library"></a>使用本机库

原始 MathFuncsLib 示例包含一个名为 `MyMathFuncs` 的类，其定义如下：

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

附加类定义包装器函数，使 .NET 使用者能够创建、释放基础本机 `MyMathFuncs` 类并与之进行交互。

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

[Xamarin](https://visualstudio.microsoft.com/xamarin/) 端将使用这些包装器函数。

## <a name="wrapping-the-native-library-stage-2"></a>包装本机库（阶段 2）

此阶段需要[上一节](#creating-the-native-libraries-stage-1)中所述的[预编译库](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="creating-the-visual-studio-solution"></a>创建 Visual Studio 解决方案

1. 在 Visual Studio for Mac 中，单击“新建项目”（从“欢迎页面”）或“新建解决方案”（从“文件”菜单） 。
2. 在“新建项目”窗口中选择“共享项目”（位于“多平台”>“库”中），然后单击“下一步”。
3. 更新以下字段，然后单击“创建”：

    - **项目名称：** MathFuncs.Shared  
    - **解决方案名称：** MathFuncs  
    - **位置：** 使用默认保存位置（或选择一个备用位置）   
    - **在解决方案目录中创建项目：** 将此选项设置为选中状态
4. 在“解决方案资源管理器”中，双击“MathFuncs.Shared”项目，然后导航到“主要设置”。
5. 从“默认命名空间”中删除“.Shared”， 将其设置为仅“MathFuncs”，然后单击“确定”。
6. 打开 MyClass.cs（通过模板创建），然后将类和文件名重命名为“MyMathFuncsWrapper”，并将命名空间更改为“MathFuncs”。
7. 按住 CONTROL 键并单击解决方案“MathFuncs”，然后从“添加”菜单中选择“添加新项目...”   。
8. 在“新建项目”窗口中选择“.NET Standard 库”（位于“多平台”>“库”中），然后单击“下一步”。
9. 选择“.NET Standard 2.0”，然后单击“下一步” 。
10. 更新以下字段，然后单击“创建”：

    - **项目名称：** MathFuncs.Standard  
    - **位置：** 使用与共享项目相同的保存位置   

11. 在“解决方案资源管理器”中，双击“MathFuncs.Standard”项目。
12. 导航到“主要设置”，然后将“默认命名空间”更新为“MathFuncs”。
13. 导航到“输出”设置，然后将“程序集名称”更新为“MathFuncs”。
14. 导航到“编译器”设置，将“配置”更改为“版本”，将“调试信息”设置为“仅符号”，然后单击“确定”。
15. 从项目中删除“Class1.cs/Getting Started”（如果其中一项已包含在模板中）。
16. 按住 CONTROL 键并单击项目的“依赖项/引用”文件夹，然后选择“编辑引用”。
17. 从“项目”选项卡中选择“MathFuncs.Shared”，然后单击“确定”。
18. 使用以下配置重复执行步骤 7-17（忽略步骤 9）：

    | **项目名称**  | **模板名称**   | **新建项目菜单**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | 类库       | “Android”>“库”      |
    | MathFuncs.iOS     | 绑定库     | “iOS”>“库”          |

19. 在“解决方案资源管理器”中，双击“MathFuncs.Android”项目，然后导航到“编译器”设置。

20. 在“配置”设置为“调试”的情况下，编辑“定义符号”以包括 Android;   。

21. 将“配置”更改为“版本”，然后编辑“定义符号”以同样包括 Android;   。

22. 对“MathFuncs.iOS”重复执行步骤 19-20，编辑“定义符号”以包括 iOS; 而不是 Android;（在两个步骤中都是如此）  。

23. 在“版本”配置中生成解决方案 (CONTROL + COMMAND + B)，并确认所有三个输出程序集（Android、iOS、.NET Standard）（位于各自的项目 bin 文件夹中）都共享相同的名称“MathFuncs.dll”。

在此阶段，解决方案应有三个目标，分别适用于 Android、iOS 和 .NET Standard，以及一个由三个目标中的每个目标引用的共享项目。 应将这些内容配置为使用相同的默认命名空间和具有相同名称的输出程序集。 对于前面提到的“偷梁换柱”方法，这是必需的。

### <a name="adding-the-native-libraries"></a>添加本机库

对于 Android 和 iOS，将本机库添加到包装器解决方案的过程略有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>MathFuncs.Android 的本机引用

1. 按住 CONTROL 键并单击“MathFuncs.Android”项目 ，然后从“添加”菜单中选择“新建文件夹”，将其命名为“libs”  。

2. 对于每个 ABI（应用程序二进制接口），按住 CONTROL 键并单击“libs”文件夹，然后从“添加”菜单中选择“新建文件夹”，并在相应的 ABI 之后命名     。 在这种情况下：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 有关更详细的概述，请参阅 [NDK 开发人员指南](https://developer.android.com/ndk/guides/)中的[体系结构和 CPU](https://developer.android.com/ndk/guides/arch) 主题，具体请参阅有关处理[应用包中的本机代码](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages)部分。

3. 确认文件夹结构：  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 根据以下映射，将相应的 .so 库添加到每个 ABI 文件夹中 ：

    **arm64-v8a：** libs/Android/arm64

    **armeabi-v7a：** libs/Android/arm  

    **x86：** libs/Android/x86

    **x86_64：** libs/Android/x86_64

    > [!NOTE]
    > 要添加文件，请按住 CONTROL 键并单击代表相应 ABI 的文件夹，然后从“添加”菜单中选择“添加文件...”   。 （从“PrecompiledLibs”目录中）选择相应的库，然后依次单击“打开”、“确定”，保留默认选项“将文件复制到目录”  。

5. 对于每个 .so 文件，请按住 CONTROL 键并单击，然后从“生成操作”菜单中选择“EmbeddedNativeLibrary”选项   。

现在“libs”文件夹应如下所示：

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>MathFuncs.iOS 的本机引用

1. 按住 CONTROL 键并单击“MathFuncs.iOS”项目，然后从“添加”菜单选择“添加本机引用”   。 
2. 选择“libMathFuncs.a”库（位于“PrecompiledLibs”目录下的 libs/ios 中），然后单击“打开”   
3. 按住 CONTROL 键并单击“libMathFuncs”文件（位于“本机引用”文件夹中），然后从菜单中选择“属性”选项     
4. 配置“本机引用”属性，使其在“属性”Pad 中处于选中状态（显示勾选标记图标）：

    - 强制加载
    - 是 C++
    - 智能链接

    > [!NOTE]
    > 将绑定库项目类型与[本机引用](../macios/native-references.md)一起使用会嵌入静态库，并使其与引用它的 Xamarin.iOS 应用自动链接（即使它是通过 NuGet 包提供的）。

5. 打开“ApiDefinition.cs”，删除模板化注释的代码（仅保留 `MathFuncs` 命名空间），然后对 Structs.cs 执行相同的步骤  

    > [!NOTE]
    > 绑定库项目需要这些文件（使用 ObjCBindingApiDefinition 和 ObjCBindingCoreSource 生成操作）才能生成。 但是，我们将编写代码，以便以使用标准 P/Invoke 在 Android 和 iOS 库目标之间共享的方式在这些文件之外调用我们的本机库。

### <a name="writing-the-managed-library-code"></a>编写托管库代码

现在，编写 C# 代码，调用本机库。 目标是隐藏所有底层复杂性。 使用者不需要具备任何有关本机库内部知识或 P/Invoke 概念的知识。  

#### <a name="creating-a-safehandle"></a>创建 SafeHandle

1. 按住 CONTROL 键并单击“MathFuncs.Shared”项目，然后从“添加”菜单选择“添加文件...”   。 
2. 从“新建文件”窗口中选择“空类”，将其命名为“MyMathFuncsSafeHandle”，然后单击“新建”   
3. 实现 MyMathFuncsSafeHandle 类：

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > [SafeHandle](/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) 是在托管代码中使用非托管资源的首选方法。 这样可以抽象出许多与临界终止和对象生命周期相关的 Boilerplate 代码。 此句柄的所有者随后可以像对待其他任何受管理资源一样对待它，而不必实现完整的 [Disposable 模式](/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>创建内部包装类

1. 打开 MyMathFuncsWrapper.cs，将其更改为内部静态类

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. 在同一文件中，将以下条件语句添加到类中：

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > 这会根据是针对 Android 还是 iOS 生成库来设置 DllName 常量值  。 这是为了处理各自平台使用的不同命名约定，以及在此情况下使用的库类型。 Android 使用动态库，因此需要包含扩展名的文件名。 对于 iOS，必须使用“__Internal”，因为我们使用的是静态库。

3. 在 MyMathFuncsWrapper.cs 文件顶部添加对 System.Runtime.InteropServices 的引用

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 添加包装器方法以处理 MyMathFuncs 类的创建和处置：

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我们将常量 DllName 与 EntryPoint 一起传递给 DllImport 属性，EntryPoint 将明确告诉 .NET 运行时在该库中调用的函数的名称  。 从技术上讲，如果托管方法的名称与非托管方法的名称相同，则无需提供 EntryPoint 值。 如果未提供，则改为使用托管方法名称作为 EntryPoint。 不过，最好是明确提供该值。  

5. 添加包装器方法，使我们能够使用以下代码处理 MyMathFuncs 类：

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > 在本示例中，我们使用简单类型作为参数。 由于在这种情况下，封送是按位复制的，因此我们不需要执行任何其他工作。 另请注意，使用 MyMathFuncsSafeHandle 类替代标准 IntPtr。 在封送过程中，IntPtr 会自动映射到 SafeHandle。

6. 确认已完成的 MyMathFuncsWrapper 类如下所示：

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>完成 MyMathFuncsSafeHandle 类

1. 打开 MyMathFuncsSafeHandle 类，导航到 ReleaseHandle 方法中的 TODO 注释占位符：

    ```csharp
    // TODO: Release the handle here
    ```

1. 替换 TODO 行：

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>编写 MyMathFuncs 类

现在包装器已经完成，创建一个 MyMathFuncs 类，该类将管理对非托管 C++ MyMathFuncs 对象的引用。  

1. 按住 CONTROL 键并单击“MathFuncs.Shared”项目，然后从“添加”菜单选择“添加文件...”   。 
2. 从“新建文件”窗口中选择“空类”，将其命名为“MyMathFuncs”，然后单击“新建”   
3. 将以下成员添加到 MyMathFuncs  类中：

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 实现类的构造函数，以便在实例化该类时创建并存储本机 MyMathFuncs 对象的句柄：

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 使用以下代码实现 IDisposable 接口：

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. 使用 MyMathFuncsWrapper 类实现 MyMathFuncs 方法，通过传入存储到基础非托管对象的指针，在后台执行实际工作。 代码应如以下所示：

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>创建 nuspec

为了通过 NuGet 打包和分发库，解决方案需要 nuspec 文件。 这将标识每个支持的平台将包括哪些结果程序集。

1. 按住 CONTROL 键并单击解决方案“MathFuncs”，然后从“添加”菜单中选择“添加解决方案文件夹”，将其命名为“SolutionItems”    。
2. 按住 CONTROL 键并单击“SolutionItems”文件夹，然后从“添加”菜单中选择“新建文件...”   。
3. 从“新建文件”窗口中选择“空 XML 文件”，将其命名为“MathFuncs.nuspec”，然后单击“新建”   。
4. 使用基本包元数据更新 MathFuncs.nuspec，以向 NuGet 使用者显示 。 例如：

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > 有关用于此清单的架构的更多详细信息，请参阅 [nuspec 引用](/nuget/reference/nuspec)文档。

5. 添加 `<files>` 元素作为 `<package>` 元素（`<metadata>` 下方）的子元素，并使用单独的 `<file>` 元素标识每个文件：

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 将包安装到项目后，如果有多个程序集由同一名称指定，NuGet 将有效地选择最特定于给定平台的程序集。

6. 为 Android 程序集添加 `<file>` 元素：

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 为 iOS 程序集添加 `<file>` 元素：

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 为 netstandard2.0 程序集添加 `<file>` 元素：

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 验证 nuspec 清单：

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > 此文件指定了 Release 版本的程序集输出路径，因此请确保使用该配置来生成解决方案。

至此，该解决方案包含 3 个 .NET 程序集和一个支持的 nuspec 清单。

## <a name="distributing-the-net-wrapper-with-nuget"></a>通过 NuGet 分发 .NET 包装器

下一步是打包和分发 NuGet 包，以便应用可以轻松地使用它，并将其作为依赖项进行管理。 包装和使用都可以在单个解决方案中完成，但是通过 NuGet 分发库有助于分离，并使我们能够独立管理这些基本代码。

### <a name="preparing-a-local-packages-directory"></a>准备本地包目录

NuGet 源的最简单形式是本地目录：

1. 在 Finder 中，导航到一个方便的目录。 例如，/Users。
2. 从“文件”菜单中选择“新建文件夹”，提供一个有意义的名称，例如“local-nuget-feed”  。

### <a name="creating-the-package"></a>创建包

1. 将“生成配置”设置为“版本” ，并使用 COMMAND + B 执行生成。
2. 打开“终端”，并将目录更改为包含  nuspec 文件的文件夹。
3. 在“终端”中，使用[上一步](#preparing-a-local-packages-directory)中创建的文件夹（即“local-nuget-feed”）执行 nuget pack 命令，以指定 nuspec 文件、“版本”（例如 1.0.0）以及 OutputDirectory。 例如：

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. 确认该 MathFuncs.1.0.0.nupkg 已在 local-nuget-feed 目录中创建  。

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[可选] 将专用 NuGet 源与 Azure DevOps 结合使用

[Azure DevOps 中的 NuGet 包入门](/azure/devops/artifacts/get-started-nuget?tabs=new-nav&view=vsts#publish-a-package)中介绍了一种更可靠的技术，该文章介绍了如何创建专用源并将包（在上一步中生成）推送到该源。

最好是让此工作流完全自动化，例如，使用 [Azure Pipelines](/azure/devops/pipelines/index?view=vsts)。 有关详细信息，请参阅 [Azure Pipelines 入门](/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>从 Xamarin.Forms 应用中使用 .NET 包装器

要完成本演练，请创建一个 Xamarin.Forms 应用以使用刚刚发布到本地 NuGet 源的包 。

### <a name="creating-the-xamarinforms-project"></a>创建 Xamarin.Forms 项目

1. 打开 Visual Studio for Mac 的新实例。 此操作可以在“终端”中完成：

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在 Visual Studio for Mac 中，单击“新建项目”（从“欢迎页面”）或“新建解决方案”（从“文件”菜单） 。
3. 在“新建项目”窗口中选择“空白窗体应用”（位于“多平台”>“应用”中），然后单击“下一步”。
4. 更新以下字段，然后单击“下一步”：

    - **应用名称：** MathFuncsApp。
    - **组织标识符：** 使用反向命名空间，例如 com.{your_org}。
    - **目标平台：** 使用默认平台（Android 和 iOS 目标）。
    - **共享代码：** 将其设置为 .NET Standard（可以使用“共享库”解决方案，但不在本演练范围之内）。

5. 更新以下字段，然后单击“创建”：

    - **项目名称：** MathFuncsApp。
    - **解决方案名称：** MathFuncsApp。  
    - **位置：** 使用默认保存位置（或选取一个备用位置）。

6. 在“解决方案资源管理器”中，按住 CONTROL 键并单击目标（MathFuncsApp.Android 或 MathFuncs.iOS）进行初始测试，然后选择“设置为启动项目”    。
7. 选择首选的设备或模拟器/仿真器。 
8. 运行解决方案（**COMMAND + RETURN**）来验证模板化 Xamarin.Forms 项目是否正常生成并运行。 

    > [!NOTE]
    > iOS（特别是模拟器）的生成/部署时间往往最短。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>将本地 NuGet 源添加到 NuGet 配置中

1. 在 Visual Studio 中，选择“首选项”（位于“Visual Studio”菜单中）。
2. 从“NuGet”部分下选择“源”，然后单击“添加”  。
3. 更新以下字段，然后单击“添加源”：

    - **Name：** 提供一个有意义的名称，例如 Local-Packages。  
    - **位置：** 指定在[上一步](#preparing-a-local-packages-directory)中创建的 local-nuget-feed 文件夹。

    > [!NOTE]
    > 在这种情况下，无需指定“用户名”和“密码” 。 

4. 单击“确定”。

### <a name="referencing-the-package"></a>引用包

为每个项目（MathFuncsApp、MathFuncsApp.Android  和 MathFuncsApp.iOS）重复以下步骤。

1. 按住 CONTROL 键并单击项目，然后从“添加”菜单选择“添加 NuGet 包...”  。
2. 搜索 MathFuncs。 
3. 确认包的“版本”为“1.0.0”，并且其他详细信息均按预期显示，如“标题”和“说明”分别为“MathFuncs”和“示例 C++ 包装器库”。 
4. 选择“MathFuncs”包，然后单击“添加包”。

### <a name="using-the-library-functions"></a>使用库函数

现在，在每个项目中引用 MathFuncs 包，这些函数可用于 C# 代码。

1. 从 MathFuncsApp  常见的 Xamarin.Forms 项目（由 MathFuncsApp.Android 和 MathFuncsApp.iOS 引用）中打开 MainPage.xaml.cs。
2. 在文件顶部为 System.Diagnostics 和 MathFuncs 添加 using 语句  ：

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 在 `MainPage` 类的顶部声明 `MyMathFuncs` 类的实例：

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. 覆盖 `ContentPage` 基类中的 `OnAppearing` 和 `OnDisappearing` 方法：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. 更新 `OnAppearing` 方法以初始化先前声明的 `myMathFuncs` 变量：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 将 `OnDisappearing` 方法更新为对 `myMathFuncs` 调用 `Dispose` 方法：

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 实现一个名为 TestMathFuncs 的专用方法，如下所示：

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. 最后，在 `OnAppearing` 方法末尾调用 `TestMathFuncs`：

    ```csharp
    TestMathFuncs();
    ```

9. 在每个目标平台上运行应用并确认“应用程序输出”Pad 中的输出如下所示：

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果在 Android 上进行测试时遇到“DLLNotFoundException”，或 iOS 上出现生成错误，请务必检查正在使用的设备/仿真器/模拟器的 CPU 体系结构是否与我们选择支持的子集兼容。 

## <a name="summary"></a>总结

本文介绍了如何创建 Xamarin.Forms 应用，该应用通过 NuGet 包分发的通用 .NET 包装器使用本机库。 本演练中特意提供非常简单的示例，以便更轻松地演示该方法。 实际的应用程序必须处理复杂性，如异常处理、回调、更复杂类型的封送处理以及与其他依赖项库的链接。 需要考虑一个关键因素：C++ 代码的演变过程是与包装器和客户端应用程序进行协调和同步的过程。 根据其中一个或两个问题是否由单个团队负责，此过程可能会有所不同。 无论采用哪种方式，自动化都是最佳做法。 下面提供了一些资源，供你进一步了解一些关键概念，并获取相关下载内容。 

### <a name="downloads"></a>下载

- [NuGet 命令行 (CLI) 工具](/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>示例

- [使用 C++ 的 Hyperlapse 跨平台移动开发](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix（C++ 和 Xamarin）](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Angeles 示例端口](/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>深入阅读

[与本文内容相关的文章](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)