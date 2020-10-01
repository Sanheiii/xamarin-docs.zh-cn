---
title: 使用本机库
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1b24648f70041abb092c93f41e999b68e8f26a26
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453254"
---
# <a name="using-native-libraries"></a>使用本机库

Xamarin.Android 支持通过标准 PInvoke 机制使用本机库。 你还可以将不属于 OS 的其他本机库绑定到你的 .apk。

若要在 Xamarin.Android 应用程序中部署本机库，请将库二进制文件添加到项目中，并将其**生成操作**设置为 **AndroidNativeLibrary**。

若要在 Xamarin.Android 库项目中部署本机库，请将库二进制文件添加到项目中，并将其**生成操作**设置为 **EmbeddedNativeLibrary**。

请注意，由于 Android 支持多个应用程序二进制接口 (ABI)，因此 Xamarin.Android 必须知道本机库是为哪个 ABI 生成的。
可以通过两种方法完成：

1. 路径“探查”
1. 通过使用项目文件中的 `AndroidNativeLibrary/Abi` 元素

通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果将 `lib/armeabi/libfoo.so` 添加到项目中，则 ABI 将被“探查”为 `armeabi`。

或者，你可以编辑项目文件以显式指定要使用的 ABI：

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

有关使用本机库的详细信息，请参阅[本机库互操作](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>在 Visual Studio 中调试本机代码

如果使用 *Visual Studio 2019* 或 *Visual Studio 2017*，则不必按上文所述修改项目文件。
可以通过将项目引用添加到 C++ **动态共享库 (Android)** 项目，在 Xamarin.Android 解决方案内部生成和调试 C++。

若要在项目中调试本机 C++ 代码，请执行以下步骤：

1. 双击项目**属性**，然后选择 **Android 选项**页。
2. 向下滚动到“调试”选项  。
3. 在“调试器”  下拉菜单中，选择“C++”  （而不是默认的“.NET (Xamarin)”  ）。

Visual Studio C++ 开发人员可以查看 [SanAngeles_NativeDebug](/samples/xamarin/monodroid-samples/sanangeles-ndk) 示例，尝试从 Visual Studio 2019 或带 Xamarin 的 Visual Studio 2017 调试 C++。有关详细信息，请参阅我们的[博客文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)。

## <a name="related-links"></a>相关链接

- [SanAngeles_NativeDebug（示例）](/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [开发 Xamarin Android 本机应用程序](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)