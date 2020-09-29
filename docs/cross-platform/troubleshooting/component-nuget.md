---
title: 将组件引用更新到 NuGet
description: 本文档介绍如何将组件引用替换为 NuGet 包，以便在将来对应用进行校对，因为 Xamarin 组件存储已停止使用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 08713afa4e781f0712ffccbbcf80533dda8e4bfc
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457219"
---
# <a name="updating-component-references-to-nuget"></a>将组件引用更新到 NuGet

> [!IMPORTANT]
> 从2018年5月15日起，组件存储已被停用， (此节假日最初于11月 2017) [公布](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) 。
>
> Visual Studio 中不再支持 Xamarin 组件，并应将其替换为 NuGet 包。 按照以下说明手动删除项目中的组件引用。

有关在 [Windows](/nuget/quickstart/use-a-package) 或 [Mac](/visualstudio/mac/nuget-walkthrough)上添加 NuGet 包的说明，请参阅这些说明。

常用 Xamarin [插件和库](https://github.com/xamarin/XamarinComponents/blob/master/README.md) 的列表可用于帮助查找作为 NuGet 包不可用的组件替代项。

## <a name="manually-removing-component-references"></a>手动删除组件引用

15.6 版本的 Visual Studio 和7.4 版本的 Visual Studio for Mac 不再支持项目中的组件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

如果将项目加载到 Visual Studio 中，则会显示以下对话框，说明必须手动从项目中删除任何组件：

![说明已在项目中找到组件并且必须将其删除的警报对话框](component-nuget-images/component-alert-vs.png)

从项目中删除组件：

1. 打开 **.csproj** 文件。 为此，请右键单击项目名称，然后选择 " **卸载项目**"。

2. 再次右键单击卸载的项目，然后选择 " **编辑 {你的项目名称}. .csproj**"。

3. 查找文件中的任何引用 `XamarinComponentReference` 。 其外观应类似于以下示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. 删除对的引用 `XamarinComponentReference` ，并保存该文件。 在上面的示例中，可以安全地删除整个 `ItemGroup` 。

5. 保存该文件后，右键单击项目名称，然后选择 " **重新加载项目**"。

6. 为解决方案中的每个项目重复上述步骤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

如果将项目加载到 Visual Studio for Mac 中，将显示以下对话框，说明必须手动从项目中删除任何组件：

![说明已在项目中找到组件并且必须将其删除的警报对话框](component-nuget-images/component-alert.png)

从项目中删除组件：

1. 打开 .csproj 文件。 为此，请右键单击项目名称，然后选择 " **工具" > "编辑文件**"。

2. 查找文件中的任何引用 `XamarinComponentReference` 。 其外观应类似于以下示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. 删除对的引用 `XamarinComponentReference` ，并保存该文件。 在上面的示例中，可以安全地删除整个 `ItemGroup`

4. 为解决方案中的每个项目重复上述步骤。

-----

> [!WARNING]
> 以下说明仅适用于早期版本的 Visual Studio。
> Visual Studio 2017 或 Visual Studio for Mac 的当前版本中不再提供 " **组件** " 节点。

以下部分介绍如何更新现有 Xamarin 解决方案，以更改对 NuGet 包的组件引用。

- [包含 NuGet 包的组件](#contain)
- [带有 NuGet 替换的组件](#replace)

大多数组件属于上述类别之一。
如果你使用的组件看起来没有等效的 NuGet 包，请阅读下面的 " [不包含 nuget 迁移路径的组件](#require-update) " 一节。

<a name="contain"></a>

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 包的组件

许多组件已经包含 NuGet 包，迁移路径只是删除组件引用。

可以通过双击解决方案中的组件来确定组件是否已包含 NuGet 包：

![组件节点已展开](component-nuget-images/solution-sml.png)

" **包** " 选项卡将列出组件中包含的任何 NuGet 包：

![包选项卡包含 NuGet](component-nuget-images/packages-tab-sml.png)

请注意，" **程序集** " 选项卡将为空：

![程序集选项卡为空](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>更新解决方案

若要更新解决方案，请从解决方案中删除 **组件** 条目：

![删除组件](component-nuget-images/delete-component-sml.png)

NuGet 包将继续在 " **包** " 节点中列出，并且你的应用程序将照常编译和运行。 将来，将通过 **NuGet** 更新功能执行对此包的更新：

![更新 NuGet 包](component-nuget-images/nuget-update-sml.png)

<a name="replace"></a>

## <a name="components-with-nuget-replacements"></a>带有 NuGet 替换的组件

如果 "组件信息" 页的 " **程序集** " 选项卡包含如下所示的条目，则需要手动查找等效的 NuGet 包。

![包含程序集](component-nuget-images/assemblies-tab-sml.png)

请注意，" **包** " 选项卡可能为空：

![包选项卡](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 依赖项，但可以忽略这些依赖项。_

若要确认替换 NuGet 包存在，请使用组件名称或作者，搜索 [NuGet.org](https://www.nuget.org/packages)。

例如，你可以通过搜索来查找常见的 **sqlite 网络-pcl** 包：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) –产品名称。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) –作者的个人资料。

### <a name="updating-the-solution"></a>更新解决方案

确认组件在 NuGet 中可用之后，请执行以下步骤：

#### <a name="delete-the-component"></a>删除组件

右键单击解决方案中的组件，然后选择 " **删除**"：

![删除组件](component-nuget-images/remove-component-sml.png)

这将删除该组件和任何引用。 这会中断您的生成，直到您添加了等效的 NuGet 包来替换它。

#### <a name="add-the-nuget-package"></a>添加 NuGet 包

1. 右键单击 " **包** " 节点，然后选择 " **添加包 ...**"。
2. 按名称或作者搜索 NuGet 替换：

    ![NuGet 搜索](component-nuget-images/nuget-search-sml.png)

3. 按 " **添加包**"。

NuGet 包将添加到你的项目，以及任何依赖项。
这会修复生成。 如果生成继续失败，请调查每个错误，查看组件和 NuGet 包之间是否存在 API 差异。

<a name="require-update"></a>

## <a name="components-without-a-nuget-migration-path"></a>无 NuGet 迁移路径的组件

如果你不想立即找到应用程序中使用的组件，请不要担心。 现有组件将继续在 Visual Studio 15.5 中工作，" **组件** " 节点会照常出现在解决方案中。

但未来的 Visual Studio 版本将 _不_ 会还原或更新组件。
这意味着，如果在新的计算机上打开该解决方案，则将不会下载和安装该组件;作者将无法向您提供更新。 你应计划：

- 从组件提取程序集并直接在项目中引用它们。
- 请与组件作者联系，并询问计划迁移到 NuGet。
- 调查备用 NuGet 包，或者在组件为开源的情况下查找源代码。

许多组件供应商仍在努力迁移到 NuGet，其他 (包括商用产品) 可能正在研究备选交付选项。

## <a name="related-links"></a>相关链接

- [常用 Xamarin 插件和库的列表](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [在 Windows)  (安装并使用 NuGet 包 ](/nuget/quickstart/use-a-package)
- [ (Mac 中包括 NuGet 包) ](/visualstudio/mac/nuget-walkthrough)