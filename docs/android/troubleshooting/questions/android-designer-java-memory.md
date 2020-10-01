---
title: 调整 Android Designer 的 Java 内存参数
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 5a692a931bfcdc1e8eee534de3adfff0de688891
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457895"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>调整 Android Designer 的 Java 内存参数

为 Android 设计器启动 `java` 进程时使用的默认内存参数可能与某些系统配置不兼容。

从 Xamarin Studio 5.7.2.7（和更高版本 Visual Studio for Mac）和 Visual Studio Tools for Xamarin 3.9.344 开始，可以根据每个项目自定义这些设置。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 设计器属性和相应的 Java 选项

以下属性名称对应于指示的 Java [命令行选项](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开解决方案。

2. 在解决方案资源管理器中逐个选择每个 Android 项目，然后在每个项目上单击[“显示所有文件”](/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))两次。 可以跳过不包含任何 `.axml` 布局文件的项目。 此步骤将确保每个项目目录都包含一个 `.csproj.user` 文件。

3. 退出 Visual Studio。

4. 找到步骤 2 中每个项目的 `.csproj.user` 文件。

5. 在文本编辑器中编辑每个 `.csproj.user` 文件。

6. 在 `<PropertyGroup>` 元素内添加任何或所有新的 Android 设计器内存属性。 可以使用现有的 `<PropertyGroup>` 或新建一个。 下面是一个完整的示例 `.csproj.user` 文件，其中包括全部设置为其默认值的 3 个属性：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. 保存并关闭所有更新的 `.csproj.user` 文件。

8. 重启 Visual Studio 并重新打开解决方案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开解决方案，以确保解决方案目录包含一个 `.userprefs` 文件。

2. 退出 Visual Studio for Mac。

3. 在解决方案目录中找到 `.userprefs` 文件。

4. 在文本编辑器中编辑 `.userprefs` 文件。

5. 找到具有以下格式的现有 XML 元素。 该元素名称的最后一部分将与项目名称匹配：本例中的“AndroidApplication1”：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. 如果 `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` 元素不存在，请在封闭的 `<Properties>` 元素内的任何位置创建它。 确保用你的项目名称替换“AndroidApplication1”。

7. 将任何或所有新的 Android 设计器内存属性添加为元素的属性。 下面是一个完整的示例 `.userprefs` 文件，其中包括全部设置为其默认值的 3 个属性：

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. 对包含任何 `.axml` 布局文件的解决方案中的每个 Android 项目重复执行步骤 5-7。 （即，为每个项目添加一个 `<MonoDevelop.Ide.ItemProperties.ProjectName>` 元素。）

9. 保存并关闭 `.userprefs` 文件。

10. 重启 Visual Studio for Mac 并重新打开解决方案。

-----