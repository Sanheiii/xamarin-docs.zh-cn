---
title: AndroidX
description: 如何开始使用 Xamarin.Android 开发适用于 AndroidX 的应用。
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: 05736fda26e5fd391d977481b340b3744f224c2f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454203"
---
# <a name="androidx-with-xamarin"></a>在 Xamarin 中使用 AndroidX

_如何开始使用 Xamarin.Android 开发适用于 AndroidX 的应用。_

AndroidX 是对原始 Android 支持库（不再维护）的重大改进。 AndroidX  包提供可在 Android 应用程序中使用的功能奇偶一致性和新库，可完全替代 Android 支持库。

AndroidX 包括以下功能：

- AndroidX 中的所有包现在都具有一致的命名空间（均以 `androidx` 开头）。 这表示所有 Android 支持库包都映射到相应的 `androidx.*` 包。
- 对 `androidx` 包进行单独维护和更新。 这样用户可以彼此独立地更新 AndroidX 库。
- 从 Android 支持库的 v28 开始，将不再发布任何版本。 所有开发都将在 `androidx` 中进行。

![AndroidX 徽标](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用 AndroidX 功能，需具备以下组件：

- **Visual Studio** - 在 Windows 上，请更新到 Visual Studio 2019 版本 16.4 或更高版本。 在 macOS 上，更新为 Visual Studio 2019 for Mac 版本 8.4 或更高版本。
- **Xamarin.Android** - 必须随 Visual Studio 一起安装 Xamarin.Android 10.0 或更高版本（在 Windows 上，Xamarin.Android 将作为“使用 .NET 的移动开发”  工作负载的一部分自动安装，并作为 Visual Studio for Mac 安装程序的一部分安装  ）
- **Java 开发人员工具包** - Xamarin.Android 10.0 开发需要 JDK 8。 Microsoft 的 OpenJDK 分发版是作为 Visual Studio 的一部分自动安装的。
- **Android SDK** - 必须通过 Android SDK 管理器安装 Android SDK API 28 或更高版本。

## <a name="get-started"></a>入门

开始使用 AndroidX 时，可以在 Android 项目中加入任何 [AndroidX NuGet 包](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。 详细了解如何在 [Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio) 或 [Visual Studio for Mac](/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac) 中安装和使用包

## <a name="behavior-changes"></a>行为变更

由于 AndroidX 对 Android 支持库进行了重新设计，因此，它的迁移步骤将影响使用 Android 支持库生成的 Android 应用程序。

### <a name="package-name-change"></a>包名称变更
新旧包的包名称已发生变化。 下面提供一些更改示例：

| 旧                    | 新建                    |
| ---------------------- | ---------------------- |
| android.support.**     | androidx.@             |
| android.design.**      | com.google.android.material.@ |
| android.support.test.** | androidx.test.@       |
| android.arch.**        | androidx.@             |
| android.arch.persistence.room.** | androidx.room.@ |
| android.arch.persistence.** | androidx.sqlite.@ |

有关包命名的详细信息，请[参阅以下文档](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings)。

## <a name="migration-tooling"></a>迁移工具

对于你的应用程序，你需要了解三个迁移步骤。

1. 如果你的应用程序包含 Android 支持库命名空间，并且你想要将它们迁移到 AndroidX 命名空间  ，可以使用我们的“迁移到 AndroidX”  IDE 工具来处理大多数命名空间的场景。 

在 Visual Studio 2019 中，通过导航到“工具”>“选项”>“Xamarin”>“Android 设置”启用 AndroidX 迁移程序   （在 Visual Studio for Mac 中可以跳过此步骤）。

![启用 AndroidX 迁移程序](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

右键单击项目，并选择“迁移到 AndroidX”  。

![迁移到 AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> 对于该工具不能涵盖的场景，将需要手动更改命名空间。 尽管我们会为你映射正确的包，但建议查看官方的[项目映射](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings)和[类映射](https://developer.android.com/jetpack/androidx/migrate/class-mappings)，从而帮助你迁移项目。

2. 如果你的应用程序包含尚未迁移到 AndroidX 命名空间  的任何依赖项，则必须[对 AndroidX 迁移包使用 Android 支持库](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)。
3. 如果你的应用程序不包含需要迁移 AndroidX 命名空间  的任何依赖项，则可以[立即在 NuGet 上使用 AndroidX 库](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22)。

## <a name="troubleshooting"></a>疑难解答

- AndroidX 中的某些体系结构包会与支持库版本冲突。 若要解决此问题，应使用这些包的 AndroidX 版本，并删除支持库版本。 例如，如果要在项目中引用 `Xamarin.Android.Arch.Work.Runtime`，它将与新添加的 `AndroidX.Work` 包的类型冲突。

## <a name="summary"></a>总结

本文介绍了 AndroidX，并说明了如何安装和配置最新的工具和包，以便进行 AndroidX 的 Xamarin.Android 开发。 它概要介绍了什么是 AndroidX。 它提供了指向 API 文档和 Android 开发人员主题的链接，帮助你开始使用 AndroidX 创建应用。 它还重点介绍了可能影响现有应用的最重要的 AndroidX 行为更改和疑难解答主题。

## <a name="related-links"></a>相关链接

- [AndroidX 简介 |Xamarin Show](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Xamarin AndroidX GitHub 存储库](https://github.com/xamarin/AndroidX)
- [Xamarin AndroidX 迁移 GitHub 存储库](https://github.com/xamarin/XamarinAndroidXMigration)