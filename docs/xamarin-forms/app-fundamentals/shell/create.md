---
title: 创建 Xamarin.Forms Shell 应用程序
description: 创建 Xamarin.Forms Shell 应用程序的过程包括创建可对 Shell 类进行子类化的 XAML 文件，将应用程序的 App 类的 MainPage 属性设置为子类化的 Shell 对象，然后描述子类化的 Shell 类中的应用程序的视觉层次结构。
ms.prod: xamarin
ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9ce36af6b8b2a70a7993ff9605c6a7b7735fd974
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563271"
---
# <a name="create-a-no-locxamarinforms-shell-application"></a>创建 Xamarin.Forms Shell 应用程序

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

创建 Xamarin.Forms Shell 应用程序的过程如下所示：

1. 创建新的 Xamarin.Forms 应用程序，或加载想要转换为 Shell 应用程序的现有应用程序。
1. 将 XAML 文件添加到可创建 `Shell` 类的子类的共享代码项目。 有关详细信息，请参阅[创建 Shell 类的子类](#subclass-the-shell-class)。
1. 将应用程序的 `App` 类的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性设置为子类 `Shell` 对象。 有关详细信息，请参阅[启动 Shell 应用程序](#bootstrap-the-shell-application)。
1. 描述子类 `Shell` 类中的应用程序的视觉层次结构。 有关详细信息，请参阅[描述应用程序的视觉层次结构](#describe-the-visual-hierarchy-of-the-application)。

## <a name="subclass-the-shell-class"></a>创建 Shell 类的子类

创建 Xamarin.Forms Shell 应用程序的第一步是将 XAML 文件添加到可创建 `Shell` 类的子类的共享代码项目。 此文件可以命名为任何名称，但建议使用 AppShell****。 以下代码示例显示了新创建的 AppShell.xaml**** 文件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

以下示例显示了代码隐藏文件 AppShell.xaml.cs****：

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>启动 Shell 应用程序

创建可创建 `Shell` 对象的子类的 XAML 文件后，`App` 类的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性应设置为子类 `Shell` 对象：

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

在此示例中，`AppShell` 类是派生自 `Shell` 类的 XAML 文件。

> [!NOTE]
> 虽然生成空白的 Shell 应用程序，但运行该应用程序将导致引发 `InvalidOperationException`。

## <a name="describe-the-visual-hierarchy-of-the-application"></a>描述应用程序的视觉层次结构

创建 Xamarin.Forms Shell 应用程序的最后一步是描述子类 `Shell` 类中的应用程序的视觉层次结构。 子类 `Shell` 类包含三个主要层次结构对象：

- `FlyoutItem` 或 `TabBar`。 `FlyoutItem` 表示浮出控件中的一个或多个项，并且应在应用程序的导航模式包括浮出控件时使用。 `TabBar` 表示底部选项卡栏，并且应在应用程序的导航模式以底部选项卡开头时使用。 每个 `FlyoutItem` 对象或 `TabBar` 对象都是 `Shell` 对象的子对象。
- `Tab`，表示分组内容，可通过底部选项卡导航。 每个 `Tab` 对象都是 `FlyoutItem` 对象或 `TabBar` 对象的子对象。
- `ShellContent`，表示应用程序中的 `ContentPage` 对象。 每个 `ShellContent` 对象都是 `Tab` 对象的子对象。 当 `Tab` 中存在多个 `ShellContent` 对象时，这些对象将可通过顶部选项卡导航。

上述对象均不表示任何用户界面，而表示应用程序视觉层次结构的组织。 Shell 使用这些对象，生成内容的导航用户界面。

以下 XAML 演示子类 `Shell` 类的一个示例：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

运行时，此 XAML 显示 `CatsPage`，因为它是子类 `Shell` 类中声明的第一个内容项：

[![iOS 和 Android 上的 Shell 应用的屏幕截图](create-images/cats.png "Shell 应用")](create-images/cats-large.png#lightbox "Shell 应用")

按汉堡图标或从左侧轻扫会显示以下浮出控件：

[![iOS 和 Android 上的 Shell 浮出控件的屏幕截图](create-images/flyout-reduced.png "Shell 浮出控件")](create-images/flyout-reduced-large.png#lightbox "Shell 浮出控件")

> [!IMPORTANT]
> 在 Shell 应用程序中，作为 `ShellContent` 对象子对象的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在应用程序启动期间创建的。 使用该方法添加其他 `ShellContent` 对象将导致在应用程序启动期间创建额外页面，这可能导致糟糕的启动体验。 不过，Shell 还能够根据需要创建页面，以响应导航。 有关详细信息，请参阅 [Xamarin.Forms Shell 选项卡](tabs.md)指南中的[高效页面加载](tabs.md#efficient-page-loading)。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)