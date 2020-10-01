---
title: 在 Android 上链接
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2018
ms.openlocfilehash: 3c42743a26ab4ec5388c1d3458d88ef4d161a1f3
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454840"
---
# <a name="linking-on-android"></a>在 Android 上链接

Xamarin.Android 应用程序使用链接器** 缩减应用程序大小。 链接器使用应用程序的静态分析来确定实际使用的程序集、类型以及成员。 然后，链接器像垃圾回收器** 一样运行，不断寻找被引用的程序集、类型和成员，直到找到引用的程序集、类型和成员的完整闭包。 然后，放弃** 此闭包之外的所有内容。

例如，[Hello，Android](/samples/xamarin/monodroid-samples/hellom4a) 示例：

|Configuration|1.2.0 大小|4.0.1 大小|
|---|---|---|
|在不链接的情况下进行发布：|14.0 MB|16.0 MB|
|在链接的情况下进行发布：|4.2 MB|2.9 MB|

链接导致程序包的大小是 1.2.0 中原始（未链接）程序包大小的 30%，是 4.0.1 中未链接程序包大小的 18%。

## <a name="control"></a>控制

链接基于静态分析**。 因此，不会检测到任何依赖于运行时环境的内容：

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```

### <a name="linker-behavior"></a>链接器行为

用于控制链接器的主要机制为“项目选项”**** 对话框中的“链接器行为”****（在 Visual Studio 中为“链接”**）下拉列表。 有三个选项：

1. **不链接**（在 Visual Studio 中为“无”**）
1. **链接 SDK 程序集**（仅 SDK 程序集**）
1. **链接所有程序集**（SDK 和用户程序集**）

“不链接”**** 选项会关闭链接器；上述“在不链接的情况下进行发布”应用程序大小示例使用了此行为。 这对排除运行时故障很有用，可了解链接器是否负责。 通常不建议将此设置用于生产版本。

“链接 SDK 程序集”**** 选项仅链接 [Xamarin.Android 附带的程序集](~/cross-platform/internals/available-assemblies.md)。
不会链接所有其他程序集（如你的代码）。

“链接所有程序集”**** 选项将链接所有程序集，这意味着如果没有静态引用，你的代码也可能会被删除。

上述示例将使用“不链接”** 和“链接 SDK 程序集”** 选项，并且使用“链接所有程序集”** 行为将会失败，并生成以下错误：

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```

### <a name="preserving-code"></a>保留代码

链接器有时会删除你想要保留的代码。 例如：

- 你可能拥有通过 `System.Reflection.MemberInfo.Invoke` 动态调用的代码。

- 如果动态实例化类型，则需要保留类型的默认构造函数。

- 如果使用 XML 序列化，则需要保留类型的属性。

在这些情况下，你可以使用 [Android.Runtime.Preserve](xref:Android.Runtime.PreserveAttribute) 属性。 应用程序未静态链接的每个成员都可能被删除，因此可以使用此属性标记未被静态引用但应用程序仍然需要的成员。 你可以将此属性应用于某种类型的每个成员或类型本身。

在以下示例中，此属性用于保留 `Example` 类的构造函数：

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

如果要保留整个类型，可以使用以下属性语法：

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

例如，在以下代码段中，整个 `Example` 类保留用于 XML 序列化：

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

有时你需要保留某些成员，但只有在保留了包含类型的情况下才可以操作。 在这些种况下，请使用以下属性语法：

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

如果不想采用 Xamarin 库上的依赖项（例如，生成一个跨平台可移植类库 (PCL)），你仍然可以使用 `Android.Runtime.Preserve` 属性。 为此，请在 `Android.Runtime` 命名空间内声明一个 `PreserveAttribute` 类，如下所示：

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```

在以上示例中，`Preserve` 属性在 `Android.Runtime` 命名空间进行了声明；但是，你可以在任何命名空间使用 `Preserve` 属性，因为链接器会按类型名称查找此属性。

### <a name="falseflag"></a>falseflag

如果不能使用 [Preserve] 属性，提供一段代码以便链接器相信该类型被使用通常很有用，但同时需防止代码块在运行时被执行。 若要利用此技术，我们可以执行以下操作：

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```

### <a name="linkskip"></a>linkskip

可以指定根本不应链接一组用户提供的程序集，同时允许使用 [AndroidLinkSkip MSBuild 属性](~/android/deploy-test/building-apps/build-process.md)通过“链接 SDK 程序集”** 行为跳过其他用户程序集：

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```

### <a name="linkdescription"></a>LinkDescription

可以在包含[自定义链接器配置文件](~/cross-platform/deploy-test/linker.md)的文件上使用 [`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
**生成操作**
。 要保留需要保留的 `internal` 或 `private` 成员，可能需要自定义链接器配置文件。

### <a name="custom-attributes"></a>自定义特性

链接程序集时，将从所有成员中删除以下自定义属性类型：

- System.ObsoleteAttribute
- System.MonoDocumentationNoteAttribute
- System.MonoExtensionAttribute
- System.MonoInternalNoteAttribute
- System.MonoLimitationAttribute
- System.MonoNotSupportedAttribute
- System.MonoTODOAttribute
- System.Xml.MonoFIXAttribute

链接程序集时，将从发行版本的所有成员中删除以下自定义属性类型：

- System.Diagnostics.DebuggableAttribute
- System.Diagnostics.DebuggerBrowsableAttribute
- System.Diagnostics.DebuggerDisplayAttribute
- System.Diagnostics.DebuggerHiddenAttribute
- System.Diagnostics.DebuggerNonUserCodeAttribute
- System.Diagnostics.DebuggerStepperBoundaryAttribute
- System.Diagnostics.DebuggerStepThroughAttribute
- System.Diagnostics.DebuggerTypeProxyAttribute
- System.Diagnostics.DebuggerVisualizerAttribute

## <a name="related-links"></a>相关链接

- [自定义链接器配置](~/cross-platform/deploy-test/linker.md)
- [在 iOS 上链接](~/ios/deploy-test/linker.md)