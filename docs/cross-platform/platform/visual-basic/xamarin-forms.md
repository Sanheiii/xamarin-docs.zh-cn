---
title: 使用 Visual Basic.NET 的 Xamarin 窗体
description: 可以修改 Xamarin 项目模板以将 Visual Basic 用于主程序集，从而有效地允许使用 VB.NET 构建跨平台的移动应用。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 5b042116f6d1fe2832298d09e3d47cba745973ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456868"
---
# <a name="xamarinforms-using-visual-basicnet"></a>使用 Visual Basic.NET 的 Xamarin 窗体

Xamarin 不支持直接 Visual Basic-按照本页上的说明创建 c # Xamarin. Forms 解决方案，然后将 .NET Standard c # 项目替换为 Visual Basic。

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![创建 Xamarin Forms 解决方案，然后将 .NET Standard 项目替换为 Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 你必须在 Windows 上使用 Visual Studio 来使用 Visual Basic 进行编程。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>带有 Visual Basic 演练的 Xamarin. Forms

按照以下步骤创建一个使用 Visual Basic 的简单 Xamarin. Forms 项目：

1. 从 Visual Studio 2019 中选择 " **创建新项目**"。

2. 在 "新建 **项目** " 窗口中，键入 " **xamarin** " 以筛选列表，然后选择 " **移动应用" (Xamarin. forms) **，然后按 " **下一步**"。

    [![适用于 Xamarin 的筛选器应用](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. 在下一个屏幕上，键入项目的名称，然后按 " **创建**"。

4. 选择 **空白** 模板并按 **"确定"**：

    [![空白的 Xamarin 窗体模板](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    这会在 Visual Studio 中使用 c # 创建一个 Xamarin 窗体解决方案。 后续步骤将修改解决方案以使用 Visual Basic。

5. 右键单击该解决方案，然后选择 "**添加 > 新建项目 ...** "

6. 键入 " **Visual Basic 库** " 以筛选项目选项，并选择 "类库" ** ( .NET Standard ") ** " 选项和 "Visual Basic" 图标：

    [![Visual Basic 库的筛选器](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. 在下一个屏幕上，键入项目的名称，然后按 " **创建**"。

8. 右键单击 "Visual Basic" 项目，然后选择 " **属性**"，然后更改 **默认命名空间** 以匹配现有 c # 项目：

    [![确保 Visual Basic 根命名空间与 Xamarin 应用程序匹配](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. 右键单击新的 Visual Basic 项目，然后选择 " **管理 NuGet 包**"，然后安装 " **Xamarin** " 并关闭 "包管理器" 窗口。

    [![窗体并关闭 "包管理器" 窗口](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. 将默认的 **Class1 .vb** 文件重命名为 **app.config**：

    [![将默认的 Class1 文件和类重命名为应用](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. 将以下代码粘贴到 **app.config** 文件中，该文件将成为 Xamarin 应用程序的起点：

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

12. 更新 Android 和 iOS 项目，使其引用新 Visual Basic 项目 (而不是模板) 创建的 c # 项目。
右键单击 Android 和 iOS 项目中的 " **引用** " 节点，以打开 " **引用管理器**"。 取消对 c # 库进行计时并使 Visual Basic 库 (不会忘记，同时为 Android 和 iOS 项目) 执行此操作。

    [![删除旧项目引用，添加 Visual Basic 引用](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. 删除 c # 项目。 添加新 **的 .vb** 文件以生成 Xamarin. Forms 应用程序。 Visual Basic 中的新的模板如下 `ContentPage` 所示：

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Xamarin 中 Visual Basic 的限制

如 [便携式视觉对象 Basic.NET 页](~/cross-platform/platform/visual-basic/index.md)上所述，Xamarin 不支持 Visual Basic 语言。 这意味着，在某些情况下，可以使用 Visual Basic：

- XAML 页不能包含在 Visual Basic 项目中-代码隐藏生成器只能生成 c #。 可以在另一个引用的 c # 可移植类库中包含 XAML，并使用数据绑定通过 Visual Basic 模型填充 XAML 文件 (此示例) 包含在 [示例](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB) 中。

- 自定义呈现器无法在 Visual Basic 中编写，它们必须用 c # 编写在本机平台项目中。

- 无法在 Visual Basic 中编写依赖项服务实现，它们必须用 c # 编写在本机平台项目中。

## <a name="related-links"></a>相关链接

- [XamarinFormsVB (示例) ](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [使用 .NET Framework 的跨平台开发](/dotnet/standard/cross-platform/)