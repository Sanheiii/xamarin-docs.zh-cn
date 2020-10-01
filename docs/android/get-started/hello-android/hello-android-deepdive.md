---
title: Hello, Android：深入了解
description: 本指南由两部分构成，在本指南中，用户将生成第一个 Xamarin.Android 应用程序，并了解使用 Xamarin 进行 Android 应用程序开发的基础知识。 在此过程中，会向你介绍生成和部署 Xamarin.Android 应用程序所需的工具、概念和步骤。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: e07a7e6ea11cbf717db22dd46eaa5e28d907bafa
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454918"
---
# <a name="hello-android-deep-dive"></a>Hello, Android：深入了解

_本指南由两部分构成，在本指南中，用户将生成第一个 Xamarin.Android 应用程序，并了解使用 Xamarin 进行 Android 应用程序开发的基础知识。在此过程中，会向你介绍生成和部署 Xamarin.Android 应用程序所需的工具、概念和步骤。_

在[了解 Android 快速入门](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md)中，生成并运行第一个 Xamarin.Android 应用程序。 现在可以更深入地了解 Android 应用程序的工作原理，以便可以生成更复杂的程序。 本指南回顾在《了解 Android》演练中执行的步骤，使用户了解所执行的操作并开始了解 Android 应用程序开发的基础知识。

本指南涉及以下主题：

::: zone pivot="windows"

- Visual Studio 简介  &ndash; Visual Studio 以及创建新 Xamarin.Android 应用程序的简介。

- **Xamarin.Android 应用程序剖析** - Xamarin.Android 应用程序基本部分的教程。

- 应用基础知识和体系结构基础知识  &ndash; 活动、Android 清单以及常规 Android 开发风格简介。

- 用户界面 (UI)  &ndash; 使用 Android Designer 创建用户界面。

- 活动和活动生命周期  &ndash; 活动生命周期和使用代码连接用户界面的简介。

- 测试、部署和完成收尾工作  &ndash; 完成应用程序（提供了有关测试、部署、生成图稿等方面的建议）。

::: zone-end
::: zone pivot="macos"

- Visual Studio for Mac 简介  &ndash; Visual Studio for Mac 以及创建新 Xamarin.Android 应用程序的简介。

- Xamarin.Android 应用程序剖析  &ndash; Xamarin.Android 应用程序基本部分的教程。

- 应用基础知识和体系结构基础知识  &ndash; 活动、Android 清单，以及常规 Android 开发风格简介。

- 用户界面 (UI)  &ndash; 使用 Android Designer 创建用户界面。

- 活动和活动生命周期  &ndash; 活动生命周期和使用代码连接用户界面的简介。

- 测试、部署和完成收尾工作  &ndash; 完成应用程序（提供了有关测试、部署、生成图稿等方面的建议）。

::: zone-end

本指南可帮助你掌握生成单屏幕 Android 应用程序所需的技能和知识。 完成本指南之后，应了解 Xamarin.Android 应用程序的不同部分以及它们如何组合在一起。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 简介

Visual Studio 是 Microsoft 提供的强大 IDE。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 在本指南中，将了解如何使用 Visual Studio 的一些基本功能以及 Xamarin 插件。

Visual Studio 将代码组织为解决方案  和项目  。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序（如 iOS 或 Android）、支持库、测试应用程序等。 在 **Phoneword** 应用中，你已使用了“Android 应用程序”  模板将新的 Android 项目添加到了在[了解 Android](~/android/get-started/hello-android/hello-android-quickstart.md)指南中所创建的 **Phoneword** 解决方案中。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 简介

Visual Studio for Mac 是免费的开源 IDE，类似于 Visual Studio。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 本指南介绍如何使用 Visual Studio for Mac 中的一些基本功能。 如果是初次接触 Visual Studio for Mac，可能需阅读更深入的 [Visual Studio for Mac 简介](/visualstudio/mac/)。

Visual Studio for Mac 遵循将代码组织为解决方案和项目的 Visual Studio 做法   。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序（如 iOS 或 Android）、支持库、测试应用程序等。 在 **Phoneword** 应用中，你已使用了“Android 应用程序”  模板将新的 Android 项目添加到了在[了解 Android](~/android/get-started/hello-android/hello-android-quickstart.md)指南中所创建的 **Phoneword** 解决方案中。

::: zone-end

<a name="anatomy"></a>

## <a name="anatomy-of-a-xamarinandroid-application"></a>Xamarin.Android 应用程序剖析

::: zone pivot="windows"

以下屏幕截图列出了解决方案的内容。 下面是一个解决方案资源管理器，它包含与解决方案关联的目录结构和所有文件：

[![“解决方案资源管理器”](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

以下屏幕截图列出了解决方案的内容。 下面是一个 Solution Pad，它包含与解决方案关联的目录结构和所有文件：

[![Solution Pad](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

创建了一个名为 **Phoneword** 的解决方案，并在其中放入了 Android 项目 **Phoneword**。

查看项目中的项，查看每个文件夹及其用途：

- 属性  &ndash; 包含 [AndroidManifest.xml](~/android/platform/android-manifest.md) 文件，该文件描述了对 Xamarin.Android 应用程序的所有要求（包括名称、版本号和权限）。 **Properties** 文件夹还包括.NET 程序集元数据文件 [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo)。 最好在此文件中填写一些应用程序相关的基本信息。

- 引用  &ndash; 包含生成和运行应用程序所需的程序集。 如果展开“引用”目录，可查看对 .NET 程序集（如 [System](xref:System)、System.Core 和 [System.Xml](xref:System.Xml)）的引用以及对 Xamarin 的 Mono.Android 程序集的引用。

- 资产  &ndash; 包含应用程序需要运行的文件（包括字体、本地数据文件和文本文件）。 此处包括的文件可通过生成的 `Assets` 类访问。 有关 Android 资产的详细信息，请参阅 Xamarin [使用 Android 资产](~/android/app-fundamentals/resources-in-android/android-assets.md)指南。

- 资源  &ndash; 包含应用程序资源，例如字符串、图像和布局。 可以通过生成的 `Resource` 类访问代码中的这些资源。 [Android 资源](~/android/app-fundamentals/resources-in-android/index.md)指南提供有关“资源”  目录的更多详细信息。 应用程序模板在 **AboutResources.txt** 文件中还包含有“资源”的简明指南。

### <a name="resources"></a>资源

“资源”目录包含 4 个文件夹（drawable、layout、mipmap 和 values），还有一个名为 Resource.designer.cs 的文件       。

下表总结了这些项：

- drawable  &ndash; drawable 目录包含[可绘制资源](https://developer.android.com/guide/topics/resources/drawable-resource.html)，如图像和位图。

- mipmap  &ndash; mipmap 目录包含适用于不同启动器图标密度的可绘制文件。 在默认模板中，drawable 目录包含应用程序图标文件“Icon.png”  。

::: zone pivot="windows"

- layout  &ndash; layout 目录包含 _Android 设计器文件_ (.axml)，该文件定义每个屏幕或活动的用户界面。 该模板创建名为 activity_main.axml 的默认布局  。

::: zone-end
::: zone pivot="macos"

- layout  &ndash; layout 目录包含 _Android 设计器文件_ (.axml)，该文件定义每个屏幕或活动的用户界面。 该模板创建名为 **Main.axml** 的默认布局。

::: zone-end

- values  &ndash; 此目录包含存储简单值（如字符串、整数和颜色）的 XML 文件。 该模板创建名为 **Strings.xml** 的文件，用于存储字符串值。

- Resource.designer.cs  &ndash; 也称为 `Resource` 类，此文件是一个分部类，存放分配给每个资源的唯一 ID。 它由 Xamarin.Android 工具自动创建，并在必要时重新生成。 不应手动编辑此文件，因为 Xamarin.Android 将覆盖对其进行的任何手动更改。

## <a name="app-fundamentals-and-architecture-basics"></a>应用基础知识和体系结构基础知识

Android 应用程序不具有单一入口点；也就是说，应用程序中没有操作系统可调用来启动该应用程序的任何代码行。 相反，当 Android 实例化应用程序的一个类时，会启动该应用程序，在此期间 Android 将整个应用程序的进程加载到内存中。

设计复杂应用程序或与 Android 操作系统交互时，Android 的这一特有功能极其有用。 但是，这些选项也使 Android 在处理 **Phoneword** 应用程序等基本方案时变得复杂。 出于此原因，分两种情况来探索 Android 体系结构。 本指南剖析使用 Android 应用最常见入口点（第一个屏幕）的应用程序。 在[了解 Android 多屏幕](~/android/get-started/hello-android-multiscreen/index.md)中，讨论了以不同方式启动应用程序，全面探讨了 Android 体系结构的复杂性。

### <a name="phoneword-scenario---starting-with-an-activity"></a>Phoneword 解决方案 - 以活动开始

在仿真器或设备中首次打开 **Phoneword** 应用程序时，操作系统会创建第一个活动  。 活动是特殊的 Android 类，对应于单个应用程序屏幕，负责绘制和支持用户界面。 Android 创建应用程序的第一个活动时，会加载整个应用程序：

[![活动负载](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

由于 Android 应用程序中没有线性发展（可以通过多个点启动应用程序），Android 采用一种独特方式来跟踪哪些类和文件组成应用程序。 在 **Phoneword** 示例中，将向名为“Android 清单”  的特殊 XML 文件注册组成应用程序的所有部分。 “Android 清单”  的作用是跟踪应用程序的内容、属性和权限，并将这些信息告知 Android 操作系统。 可以将 **Phoneword** 应用程序当作单一活动（屏幕）和由 Android 清单文件捆绑在一起的资源文件和帮助程序文件的集合，如以下关系图所示：

[![资源帮助程序](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

以下几个部分将探索 **Phoneword** 应用程序各部分的关系；使你能更好地理解上面的关系图。 此探索先从用户界面开始，会讨论 Android 设计器和布局文件。

## <a name="user-interface"></a>用户界面

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

::: zone pivot="windows"

activity_main.axml 是应用程序中第一个屏幕的用户界面布局文件  。 .axml 指示这是 Android 设计器文件（AXML 表示 *Android XML*。 名称 Main  对 Android 而言是任意的 &ndash; 可将布局文件命名为其他名称。 在 IDE 中打开 activity_main.axml 时，会显示名为“Android Designer”的 Android 布局文件的可视编辑器   ：

[![Android Designer](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android 设计器")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

在 **Phoneword** 应用中，**TranslateButton** 的 ID 设置为 `@+id/TranslateButton`：

[![TranslateButton ID 设置](hello-android-deepdive-images/vs/04-translatebutton-sml.png "TranslateButton ID 设置")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

Main.axml 是应用程序中第一个屏幕的用户界面布局文件  。 .axml 指示这是 Android 设计器文件（AXML 表示 *Android XML*。 名称 Main  对 Android 而言是任意的 &ndash; 可将布局文件命名为其他名称。 在 IDE 中打开 **Main.axml** 时，会显示名为“Android 设计器”  的 Android 布局文件的可视编辑器：

[![Android Designer](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

在 **Phoneword** 应用中，**TranslateButton** 的 ID 设置为 `@+id/TranslateButton`：

[![TranslateButton ID 设置](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

设置 **TranslateButton** 的 `id` 属性时，Android Designer 会将 **TranslateButton** 控件映射到 `Resource` 类，并为其分配 `TranslateButton` 的资源 ID  。 通过将可视控件映射到类，可以找到并使用 **TranslateButton** 和应用代码中的其他控件。 当你剖析为控件提供支持的代码时，会更详细地了解这一内容。 此时，只需知道控件的代码表示形式是通过 `id` 属性链接到设计器中控件的可视表示形式即可。

### <a name="source-view"></a>源视图

在设计界面上定义的所有内容都会转换成 XML，以供 Xamarin.Android 使用。 Android 设计器提供源视图，此源视图包含从可视化设计器生成的 XML。 可以切换到设计器视图左下角的“源”  面板以查看此 XML，如以下屏幕截图所示：

::: zone pivot="windows"

[![设计器源视图](hello-android-deepdive-images/vs/05-source-view-sml.png "设计器源视图")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![设计器源视图](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

此 XML 源代码应包含四个控件元素：两个 TextView、一个 EditText 和一个“按钮”元素    。 有关 Android 设计器的更深入教程，请参阅 Xamarin Android [设计器概述](~/android/user-interface/android-designer/index.md)指南。

现在已讨论了用户界面可视部分背后的工具和概念。 接下来，通过探索活动和活动生命周期介绍为用户界面提供支持的代码。

## <a name="activities-and-the-activity-lifecycle"></a>活动和活动生命周期

`Activity` 类包含为用户界面提供支持的代码。
活动负责响应用户交互，并创建动态用户体验。
本部分将介绍 `Activity` 类，讨论活动生命周期，并剖析为 **Phoneword** 应用程序中的用户界面提供支持的代码。

### <a name="activity-class"></a>Activity 类

**Phoneword** 应用程序只有一个屏幕（活动）。 为屏幕提供支持的类称为 `MainActivity`，位于 **MainActivity.cs** 文件中。 名称 `MainActivity` 在 Android 中没有特别的意义 &ndash; 虽然约定是命名应用程序 `MainActivity` 中的第一个活动，但 Android 并不在意将其命名为其他名称。

打开 **MainActivity.cs** 时，可以看到，`MainActivity` 类是 `Activity` 类的子类  并且活动标有 [Activity](xref:Android.App.ActivityAttribute) 属性：

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

`Activity` 属性向 Android 清单注册活动；这能让 Android 知道此类是该清单所管理的 **Phoneword** 应用程序的一部分。 `Label` 属性设置将显示在屏幕顶部的文本。

`MainLauncher` 属性告知 Android 在启动应用程序时显示此活动。 如[了解 Android 多屏幕](~/android/get-started/hello-android-multiscreen/index.md)指南中所述，当你向应用程序添加更多活动（屏幕）时，此属性会变得很重要。

在了解了 `MainActivity` 的基础知识，现在将通过介绍_活动生命周期_来深入研究活动代码。

### <a name="activity-lifecycle"></a>活动生命周期

在 Android 中，活动会根据与用户的交互经历生命周期的不同阶段。 可以对活动进行创建、启动和暂停、恢复和销毁等操作。 `Activity` 类包含方法，系统会在屏幕生命周期的特定时间点调用这些方法。 下图说明了活动的典型生命周期以及一些相应的生命周期方法：

[![活动生命周期](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

通过重写 `Activity` 生命周期方法，可以控制活动的加载方式和与用户的互动方式，甚至还可以控制活动从设备屏幕消失后会发生的情况。 例如，可以重写上图中的生命周期方法，以执行以下重要任务：

- OnCreate  &ndash; 创建视图、初始化变量，并执行在用户能看到活动之前必须完成的其他准备工作。 只有将活动加载到内存时，才会调用此方法一次。

- OnResume  &ndash; 执行每当活动返回到设备屏幕时必须发生的任何任务。

- OnPause  &ndash; 执行每当活动离开设备屏幕时必须发生的任何任务。

向 `Activity` 中的生命周期方法添加自定义代码时，重写  该生命周期方法的基实现  。 可以利用现有的生命周期方法（已在其中附加了一些代码）并使用自己的代码来扩展该方法。 从方法内调用基实现，确保原始代码在新代码之前运行。 下一部分对此提供示例说明。

活动生命周期是 Android 中一个重要且复杂的部分。 完成_入门_系列后，如果想要了解有关活动的详细信息，请阅读[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。 本指南下一步的重点是活动生命周期的第一个阶段 - `OnCreate`。

### <a name="oncreate"></a>OnCreate

Android 创建活动时会调用 `Activity` 的 `OnCreate` 方法（向用户显示屏幕之前）。 可以重写 `OnCreate` 生命周期方法，以创建视图并准备向用户显示活动：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

在 **Phoneword** 应用中，首先在 `OnCreate` 中加载在 Android Designer 中创建的用户界面。 若要加载 UI，请调用 `SetContentView` 并向其传递布局文件的资源布局名称：activity_main.axml   。 布局位于 `Resource.Layout.activity_main`：

```csharp
SetContentView (Resource.Layout.activity_main);
```

当 `MainActivity` 开始运行后，会基于 activity_main.axml 文件的内容创建一个视图  。

::: zone-end
::: zone pivot="macos"

在 **Phoneword** 应用中，首先在 `OnCreate` 中加载在 Android Designer 中创建的用户界面。 若要加载 UI，请调用 `SetContentView` 并向其传递布局文件的资源布局名称  ：Main.axml  。 布局位于 `Resource.Layout.Main`：

```csharp
SetContentView (Resource.Layout.Main);
```

当 `MainActivity` 开始运行后，会基于 **Main.axml** 文件中内容创建一个视图。 请注意，布局文件名应与活动名称匹配 &ndash;  Main.axml 是 Main  活动的布局。 这不是 Android 要求的，但当你开始向应用程序添加更多屏幕时，会发现此命名约定便于匹配布局文件和代码文件。

::: zone-end

准备好布局文件后，可以开始查找控件。
若要查找控件，请调用 `FindViewById`，并传入控件的资源 ID：

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

现在布局文件中已具有对控件的引用，可以开始对其进行编程，以响应用户交互。

### <a name="responding-to-user-interaction"></a>响应用户交互

在 Android 中， `Click` 事件侦听用户的触控。 在此应用中，`Click` 事件将由 lambda 处理，不过也可改用委托或命名事件处理程序。 最终的 **TranslateButton** 代码如下所示：

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>测试、部署和完成收尾工作

Visual Studio for Mac 和 Visual Studio 均提供许多用于测试和部署应用程序的选项。 此部分介绍调试选项，演示如何在设备上测试应用程序，并介绍用于针对不同的屏幕密度创建自定义应用图标的工具。

### <a name="debugging-tools"></a>调试工具

应用程序代码中的问题可能难以进行诊断。 为了帮助诊断复杂的代码问题，可以[设置断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐行执行代码](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)或[将信息输出到日志窗口](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

### <a name="deploy-to-a-device"></a>部署到设备

仿真器是用于部署和测试应用程序的良好开端，但用户不会在仿真器中使用最终应用程序。 最好尽早并经常在实际设备上测试应用程序。

使用 Android 设备测试应用程序之前，需要对其进行开发配置。 [设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)指南提供了有关准备设备进行开发的详尽说明。

::: zone pivot="windows"

配置设备后，可通过插入设备，从“选择设备”  对话框中将其选中，然后启动应用程序，对设备进行部署：

![选择调试设备](hello-android-deepdive-images/vs/06-select-device.png "选择调试设备")

::: zone-end
::: zone pivot="macos"

配置设备后，可通过插入设备，按“开始(播放)”  ，从“选择设备”  对话框中将其选中，然后按“确定”  ，对设备进行部署：

[![选择调试设备](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

这将启动设备上的应用程序：

[![输入 Phoneword](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>为不同的屏幕密度设置图标

Android 设备具有不同的屏幕大小和分辨率，不是所有图像都能清晰显示在屏幕上。 例如，下面是一张高密度 Nexus 5 上低密度图标的屏幕截图。 可以看到，与周边的图标相比，它很模糊：

[![模糊的图标](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

考虑到这一点，最好将不同分辨率的图标添加到 **Resources** 文件夹。 Android 提供了不同版本的 mipmap 文件夹来处理不同密度的启动器图标，包括针对中等密度屏幕的 mdpi、针对高密度屏幕的 hdpi，以及针对超高密度屏幕的 xhdpi、xxhdpi 和 xxxhdpi       。 不同大小的图标存储在相应的 mipmap- 文件夹中  ：

::: zone pivot="windows"

![Mipmap 文件夹](hello-android-deepdive-images/vs/07-mipmap-folders.png "Mipmap 文件夹")

::: zone-end
::: zone pivot="windows"

[![Mipmap 文件夹](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

Android 根据合适的密度选择图标：

[![合适密度下的图标](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>生成自定义图标

并非每个人都具有可用于创建自定义图标和启动图像（让应用与众不同）的设计器。下面是几种生成自定义应用图像的备选方法：

::: zone pivot="windows"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; 是一个基于 Web 的浏览器内生成器，针对所有类型 Android 图标，带有指向其他有用社区工具的链接。 在 Google Chrome 中性能最佳。

- Visual Studio &ndash; 可以用于直接在 IDE 中为应用创建简单图标集。

- [Fiverr](https://www.fiverr.com/) &ndash; 从各种设计器中进行选择以便创建图标集（最低 5 美元）。 可以漫无目标，不过如果需要动态设计的图标，这是很好的资源。

::: zone-end
::: zone pivot="macos"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; 是一个基于 Web 的浏览器内生成器，针对所有类型 Android 图标，带有指向其他有用社区工具的链接。 在 Google Chrome 中性能最佳。

- [Pixelmator](https://www.pixelmator.com/) &ndash; 适用于 Mac 的通用图像编辑应用（价格约 30 美元）。

- [Fiverr](https://www.fiverr.com/) &ndash; 从各种设计器中进行选择以便创建图标集（最低 5 美元）。 可以漫无目标，不过如果需要动态设计的图标，这是很好的资源。

::: zone-end

有关图标大小和要求的详细信息，请参阅 [Android 资源](~/android/app-fundamentals/resources-in-android/index.md)指南。

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>添加 Google Play Services 包

 Google Play Services 是一套外接程序库，让 Android 开发人员可以利用 Google 的最新功能，如 Google Maps、Google Cloud Messaging 和 In-App Billing。
以前，由 Xamarin 以单一包的形式提供对所有 Google Play Services 库的绑定 &ndash; 从 Visual Studio for Mac 开始，提供了一个新的项目对话框，用于选择要包括在应用中的 Google Play Services 包。

若要添加一个或多个 Google Play Service 库，右键单击项目树中的“包”  节点，并单击“添加 Google Play Service...”  ：

[![添加 Google Play 服务](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

出现“添加 Google Play Services”  对话框时，选择想要添加到项目的包 (nuget)：

[![选择包](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

选择服务并单击“添加包”时，Visual Studio for Mac 会下载并安装所选包及其所需的任何 Google Play Services 依赖包  。 在某些情况下，可能会看到“接受许可”  对话框，要求在安装包之前单击“接受”  ：

[![许可证接受](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>总结

祝贺你！ 你现在应已充分了解了 Xamarin.Android 应用程序的组件以及创建它们所需的工具。

在入门  系列的另一个教程中，你将扩展应用程序以处理多个屏幕，同时探索更高级的 Android 体系结构和概念。