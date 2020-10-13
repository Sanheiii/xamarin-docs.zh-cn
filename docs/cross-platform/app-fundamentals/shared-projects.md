---
title: 使用共享项目共享代码
description: 使用共享项目，可以编写由多个不同的应用程序项目引用的通用代码。 此代码作为每个引用项目的一部分进行编译，并且可以包含编译器指令以帮助将特定于平台的功能合并到共享代码库中。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: bd8314e0f84dd74477782a1cfdcb6b91953ecdcd
ms.sourcegitcommit: 827daa78c090bf79a1b55da45bb8012a1723b720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997527"
---
# <a name="shared-projects-code-sharing"></a>共享项目代码共享

_使用共享项目，可以编写由多个不同的应用程序项目引用的通用代码。此代码编译为每个引用项目的一部分，并且可以包含编译器指令以帮助将平台特定的功能合并到共享代码库中。_

共享项目 (有时也称为共享资产项目) 使你可以编写在多个目标项目之间共享的代码，包括 Xamarin 应用程序。

它们支持编译器指令，以便您可以有条件地包含要编译到引用共享项目的项目子集的平台特定代码。 还提供了 IDE 支持，可帮助管理编译器指令并直观显示代码在每个应用程序中的外观。

如果过去曾经使用文件链接来共享项目中的代码，则共享项目的工作方式与此类似，但改进的 IDE 支持更多。

## <a name="what-is-a-shared-project"></a>什么是共享项目？

与大多数其他项目类型不同的是，共享项目中的任何输出 (都不) DLL 形式，而是编译到每个引用该项目的项目。 下图对此进行了说明：从概念上讲，将共享项目的整个内容 "复制到" 每个引用项目，并将其编译为，就像它是这些内容的一部分一样。

![共享项目体系结构](shared-projects-images/sharedassetproject.png)

共享项目中的代码可以包含编译器指令，这些指令可用于启用或禁用代码段（具体取决于使用代码的应用程序项目），这取决于关系图中的彩色平台框。

共享项目不会自行编译，它只是一组可以包含在其他项目中的源代码文件。 当被另一个项目引用时，该代码将被有效地编译为该项目的 *一部分* 。 共享的项目不能引用任何其他项目类型 (包括) 的其他共享项目。

请注意，Android 应用程序项目无法引用其他 Android 应用程序项目，例如，Android 单元测试项目无法引用 Android 应用程序项目。 有关此限制的详细信息，请参阅此 [论坛讨论](https://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练

本部分介绍如何使用 Visual Studio for Mac 创建和使用共享项目。 有关完整示例，请参阅 "到 [共享项目的示例](#Shared_Project_Example) " 部分。

## <a name="creating-a-shared-project"></a>创建共享项目

若要创建新的共享项目，请导航到 " **文件" > "新建解决方案 ...** " (或右键单击现有解决方案，然后选择 " **添加 > 添加新项目 ...**) ：

[![新建共享项目](shared-projects-images/xs-newsolution-sml.png "新建解决方案")](shared-projects-images/xs-newsolution.png#lightbox)

在下一个屏幕上，选择项目名称，然后单击 " **创建**"。

下面显示了一个新的共享项目-注意没有引用或组件节点;共享项目不支持这些。

![空的共享项目](shared-projects-images/xs-empty.png "空的共享项目")

要使共享项目有用，需要至少由一个可生成的项目 (（例如 iOS 或 Android 应用程序或库）或 PCL 项目) 引用。 当共享项目没有引用它时，不会对其进行编译，因此语法 (或任何其他) 错误在被其他内容引用之前将不会突出显示。

添加对共享项目的引用的方式与引用常规库项目的方式相同。 此屏幕截图显示了引用共享项目的 Xamarin iOS 项目。

![对共享项目的项目引用](shared-projects-images/xs-reference.png)

共享项目被另一个库或应用程序引用后，您可以生成解决方案并查看代码中的任何错误。 如果共享项目被 _两个或更多_ 其他项目引用，则会在源代码编辑器的左上角显示一个菜单，其中显示了哪些项目引用此文件。

## <a name="shared-project-options"></a>共享项目选项

右键单击某个共享项目，然后选择 " **选项** "，设置的设置比其他项目类型少。 由于共享项目不会在其自身)  (编译，因此不能设置输出或编译器选项、项目配置、程序集签名或自定义命令。 共享项目中的代码将有效地继承这些值。

" **选项** " 屏幕如下所示-"项目 **名称** " 和 " **默认命名空间** " 是通常会更改的两个设置。

![共享项目选项](shared-projects-images/xs-sharedprojectoptions.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 演练

本部分介绍如何使用 Visual Studio 创建和使用共享项目。 有关完整实现，请参阅 "到 [共享项目的示例](#Shared_Project_Example) " 部分。

### <a name="creating-a-shared-project"></a>创建共享项目

若要创建新的共享项目，请导航到 "**文件**" "  >  **新建**  >  **项目**"。

在 Visual Studio 2019 中，在 "**创建新项目**" 页面上的搜索框中输入 "**共享**"。 选择 **共享项目** 模板，然后选择 " **下一步**"。 输入项目的名称，然后选择 " **创建**"。

在 Visual Studio 2017 中，选择 " **共享项目** " 模板，然后选择项目的名称。

![Visual Studio 2017 中的共享项目模板](shared-projects-images/vs-newsolution.png)

还可以通过右键单击解决方案文件，然后选择 " **添加 > 新项目**"，将新的共享项目添加到现有解决方案。 ) 添加类文件后，新的共享项目如下所示 (。 请注意，没有引用或组件节点;共享项目不支持这些。

![空的共享项目](shared-projects-images/vs-empty.png)

要使共享项目有用，需要至少由一个可生成的项目 (（例如 iOS 或 Android 应用程序或库）或 PCL 项目) 引用。 当共享项目没有引用它时，不会对其进行编译，因此语法 (或任何其他) 错误在被其他内容引用之前将不会突出显示。

添加对共享项目的引用的方式与引用常规库项目的方式相同。 此屏幕截图显示了引用共享项目的 Xamarin iOS 项目。

![对共享项目的项目引用](shared-projects-images/vs-reference.png)

共享项目被另一个库或应用程序引用后，您可以生成解决方案并查看代码中的任何错误。 如果共享项目被 _两个或更多_ 其他项目引用，则源代码编辑器的左上角会出现一个菜单，查看哪些项目引用了当前代码文件。

### <a name="shared-project-properties"></a>共享项目属性

选择共享项目时，"属性" 面板中的设置比其他项目类型少一些。 由于共享项目不会在其自身)  (编译，因此不能设置输出或编译器选项、项目配置、程序集签名或自定义命令。 共享项目中的代码将有效地继承这些值。

" **属性** " 面板显示在下方， **根命名空间** 是唯一可以更改的设置。

![共享项目属性](shared-projects-images/vs-sharedprojectproperties.png)

-----

<a name="Shared_Project_Example"></a>

## <a name="shared-project-example"></a>共享项目示例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)示例使用共享项目来包含 IOS、Android 和 Windows Phone 应用程序使用的通用代码。 `SQLite.cs`和 `TaskRepository.cs` 源代码文件利用编译器指令 (例如。 `#if __ANDROID__`) 为每个引用它们的应用程序生成不同的输出。

完整的解决方案结构如下所示 (Visual Studio for Mac 和 Visual) Studio：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio for Mac 解决方案](shared-projects-images/xs-examplesolution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Visual Studio 解决方案](shared-projects-images/vs-examplesolution.png)

-----

可以从 Visual Studio for Mac 中导航 Windows Phone 项目，即使该项目类型不支持 Visual Studio for Mac 中的编译。

正在运行的应用程序如下所示：

![iOS、Android、Windows Phone 示例](shared-projects-images/example.png)

## <a name="summary"></a>总结

本文档介绍了共享项目的工作原理、如何在 Visual Studio for Mac 和 Visual Studio 中创建和使用它们，并引入了一个简单的示例应用程序，该应用程序演示了操作中的共享项目。

## <a name="related-links"></a>相关链接

- [Tasky 示例应用](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可移植类库 (示例) ](~/cross-platform/app-fundamentals/pcl.md)
- [共享代码选项 (示例) ](~/cross-platform/app-fundamentals/code-sharing.md)
