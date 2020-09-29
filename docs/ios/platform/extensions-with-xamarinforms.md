---
title: 在 iOS 扩展中重复使用 Xamarin 页
description: 本文档介绍 iOS 扩展以及如何使用中的 Xamarin 页。 它包含有关如何创建扩展、集成 Xamarin 和在 iOS 扩展项目中以本机方式呈现简单 ContentPage 的演练。
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: b9bbbc784fa9932d087e1f1c2a575eb7848d5a80
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437118"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>在 iOS 扩展中重复使用 Xamarin 页

iOS 扩展使你可以通过将额外功能添加到 [ios 和 MacOS 扩展点](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2)（如自定义上下文操作、密码自动填充、传入调用筛选器、通知内容修饰符等）来自定义现有系统行为。 Xamarin 支持扩展， [本指南](./extensions.md) 将指导你使用 Xamarin 工具创建 iOS 扩展。

扩展作为容器应用的一部分进行分发，并从主机应用程序中的特定扩展点激活。 容器应用通常是一个简单的 iOS 应用程序，它为用户提供有关扩展的信息，以及如何激活和使用该扩展。 有三种主要方法可用于在扩展和容器应用之间共享代码：

1. 常见的 iOS 项目。

    可以将容器和扩展之间的所有共享代码放入共享的 iOS 库，并从这两个项目中引用该库。 通常，共享库包含 native UIViewControllers，它必须是一个 Xamarin 类库。

1. 文件链接。

    在某些情况下，容器应用程序提供大部分功能，而扩展需要呈现单个 `UIViewController` 。 对于共享的文件，通常会将文件链接从容器应用中的文件添加到扩展应用程序。

1. 常见的 Xamarin 窗体项目。

    如果应用页面已与其他平台（如 Android）共享，使用 Xamarin. Forms framework，则常见方法是在扩展项目中以本机方式重新实现所需页面，因为 iOS 扩展适用于本机 UIViewControllers 而不是 Xamarin。 Forms 页面。 您必须执行其他步骤才能使用 iOS 扩展中的 Xamarin，如下所述。

## <a name="xamarinforms-in-an-ios-extension-project"></a>IOS 扩展项目中的 Xamarin

在本机项目中使用 Xamarin 的能力是通过 [本机形式](~/xamarin-forms/platform/native-forms.md)提供的。 它允许 `ContentPage` 派生页面直接添加到本机 Xamarin iOS 项目。 `CreateViewController`扩展方法将 Xamarin 页的实例转换为本机 `UIViewController` ，可以使用或将其作为常规控制器进行修改。 由于 iOS 扩展是一种特殊类型的本机 iOS 项目，因此也可以在此处使用 **本机窗体** 。

> [!IMPORTANT]
> IOS 扩展有很多 [已知限制](./extensions.md#limitations) 。 虽然你可以在 iOS 扩展中使用 Xamarin，但你应该非常小心，监视内存使用情况和启动时间。 否则，该扩展可能会被 iOS 终止，而不会对此进行适当的处理。

## <a name="walkthrough"></a>演练

在本演练中，你将创建一个 Xamarin. Forms 应用程序、一个 Xamarin iOS 扩展，并在扩展项目中重用共享代码：

1. 打开 Visual Studio for Mac，使用 **空白窗体应用程序** 模板创建新的 Xamarin. Forms 项目，并将其命名为 **FormsShareExtension**：

    ![创建项目](extensions-xf-images/1.walkthrough-createproject.png)

1. 在 **FormsShareExtension/MainPage**中，将内容替换为以下布局：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. 将名为 **MainPageViewMode** 的新类添加到 **FormsShareExtension** 项目中，并将类的内容替换为以下代码：

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    代码在所有平台之间共享，并且也将由 iOS 扩展使用。

1. 在 "解决方案板" 中，右键单击解决方案，选择 " **添加 > 新建项目 > iOS > 扩展 > 操作扩展，将**其命名为 **MyAction** ，然后按" **创建**"：

    ![创建扩展](extensions-xf-images/2.walkthrough-createextension.png)

1. 若要在 iOS 扩展和共享代码中使用 Xamarin，需要添加所需的引用：

    - 右键单击 iOS 扩展，选择 " **引用" > > 项目添加引用 ">" FormsShareExtension** "，然后按 **" 确定 "**。

    - 右键单击 iOS 扩展，选择 " **包" > "管理 NuGet 包 ..." > Xamarin**  并按 " **添加包**"。

1. 展开扩展项目，然后修改入口点以初始化 Xamarin. Forms 和 create pages。 根据 iOS 要求，扩展必须定义 **info.plist** 中的入口点作为 `NSExtensionMainStoryboard` 或 `NSExtensionPrincipalClass` 。 激活入口点后，在这种情况下，它是 `ActionViewController.ViewDidLoad` 方法，你可以创建 Xamarin 页面的实例并将其显示给用户。 因此，请打开入口点并将 `ViewDidLoad` 方法替换为以下实现：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    `MainPage`是使用标准构造函数进行实例化的，在扩展中使用该构造函数之前，可以使用扩展方法将其转换为本机 `UIViewController` `CreateViewController` 。 
    
    生成并运行应用程序：

    ![创建扩展](extensions-xf-images/3.walkthrough-runapp.png)

    若要激活该扩展，请导航到 Safari 浏览器，键入任意 web 地址（例如 [microsoft.com](https://microsoft.com)），按 "导航"，然后按页面底部的 **共享** 图标查看可用的操作扩展。 在可用扩展列表中，通过点击来选择 **MyAction** 扩展：

    ![创建扩展](extensions-xf-images/4.walkthrough-run1.png) ![创建扩展](extensions-xf-images/5.walkthrough-run2.png) ![创建扩展](extensions-xf-images/6.walkthrough-run3.png)

    扩展随即激活，并向用户显示 Xamarin 页。 所有绑定和命令在容器应用中都起作用。

1. 原始入口点视图控制器可见，因为它是由 iOS 创建和激活的。 若要解决此问题，请在 `UIModalPresentationStyle.FullScreen` 调用前添加以下代码，以将新控制器的模式表示形式更改为 `PresentModalViewController` ：

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    在 iOS 模拟器或设备中生成并运行：

    ![IOS 扩展中的 Xamarin](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > 对于设备生成，请确保使用正确的生成设置和 **发布** 配置，如 [此处所述](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions)。

## <a name="related-links"></a>相关链接

- [Xamarin.iOS 中的 iOS 扩展](~/iOS/platform/extensions.md)
- [Xamarin 本机项目中的 xamarin 窗体](~/xamarin-forms/platform/native-forms.md)
- [优化 iOS 应用扩展的效率和性能](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [示例源代码](https://github.com/xamcat/xamarin-forms-ios-extension)