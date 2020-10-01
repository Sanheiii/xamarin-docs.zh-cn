---
title: 如何自动化 Android NUnit 测试项目？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: c8c9e721bc46d9071bb2af479a5e1d37b93fce27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458194"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何自动化 Android NUnit 测试项目？

> [!NOTE]
> 本指南介绍了如何自动运行 Android NUnit 测试项目（而非 Xamarin.UITest 项目）。 若要获取 Xamarin.UITest 指南，可以单击[此处](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)。

在 Visual Studio 中创建“单元测试应用(Android)”  项目（或在 Visual Studio for Mac 中创建“Android 单元测试”  项目）时，此项目默认不会自动运行测试。
若要在目标设备上运行 NUnit 测试，可以创建使用以下命令启动的 [Android.App.Instrumentation](xref:Android.App.Instrumentation) 子类： 

```shell
adb shell am instrument 
```

此过程的步骤如下：

1. 新建名为 TestInstrumentation.cs  的文件： 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;

    namespace App.Tests {

        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {

            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }

            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```

    在此文件中，子类化 `Xamarin.Android.NUnitLite.TestSuiteInstrumentation`（来自 Xamarin.Android.NUnitLite.dll  ），以创建 `TestInstrumentation`。

2. 实现 `TestInstrumentation` 构造函数和 `AddTests` 方法。 `AddTests` 方法控制实际执行哪些测试。

3. 修改 `.csproj` 文件，以添加 TestInstrumentation.cs  。 例如：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

4. 使用以下命令来运行单元测试。 将 `PACKAGE_NAME` 替换为应用的包名称（包名称位于应用在 AndroidManifest.xml  中的 `/manifest/@package` 特性内）：

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. 也可以修改 `.csproj` 文件，以添加 `RunTests` MSBuild 目标。 这样就可以使用如下命令来调用单元测试：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    （请注意，不需要使用此新目标；可以使用前面的 `adb` 命令来替代 `msbuild`。）

若要详细了解如何使用 `adb shell am instrument` 命令来运行单元测试，请参阅 Android 开发人员主题[使用 ADB 运行测试](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)。

> [!NOTE]
> 在 [Xamarin.Android 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) 版中，Android 可调用包装器的默认包名称基于，要导出的类型的程序集限定名称的 MD5SUM。 这样，可以从两个不同的程序集提供相同的完全限定名称，而不会出现打包错误。 因此，请务必对 `Instrumentation` 特性使用 `Name` 属性，以生成可读的 ACW/类名。

ACW 名称必须用于上面的 `adb` 命令  。
因此，重命名/重构 C# 类时，必须修改 `RunTests` 命令，才能使用正确的 ACW 名称。