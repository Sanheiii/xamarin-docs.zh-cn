---
title: Xamarin 中的选项卡和制表符栏控制器
description: 本文档介绍了 iOS 选项卡栏控制器，以及如何将它们与 Xamarin 一起使用。 它演示了如何设置 UITabBarController、使用图像、设置徽章值、处理事件等。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9bd048239c404c0eb3309fdc74b26bcb94db4740
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434124"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Xamarin 中的选项卡和制表符栏控制器

在 iOS 中使用选项卡式应用程序来支持用户界面，可以在其中无特定顺序访问多个屏幕。 通过 `UITabBarController` 类，应用程序可以轻松地包括对此类多屏幕方案的支持。 `UITabBarController` 负责多屏幕管理，使应用程序开发人员可以专注于每个屏幕的详细信息。

通常，选项卡式应用程序的生成方式为 `UITabBarController` `RootViewController` 主窗口的。 但是，通过另外几个代码，选项卡式应用程序还可以连续用于其他初始屏幕，如应用程序第一次显示登录屏幕的方案，然后是选项卡式界面。

此页讨论了这两种方案：选项卡位于应用程序视图层次结构的根中，同时还讨论了非 `RootViewController` 方案。

## <a name="introducing-uitabbarcontroller"></a>UITabBarController 简介

`UITabBarController`支持通过以下方式进行选项卡式应用程序开发：

- 允许添加多个控制器。
- 通过类提供选项卡式用户界面，  `UITabBar` 使用户能够在控制器和视图之间切换。

控制器 `UITabBarController` 通过其 `ViewControllers` 属性（数组）添加到中 `UIViewController` 。 `UITabBarController`本身处理加载适当的控制器并根据所选的选项卡显示其视图。

选项卡是类的实例 `UITabBarItem` ，包含在 `UITabBar` 实例中。 每个 `UITabBar` 实例可通过 `TabBarItem` 每个选项卡中的控制器的属性进行访问。

若要了解如何使用 `UITabBarController` ，我们来演练如何构建使用一个应用程序的简单应用程序。

## <a name="tabbed-application-walkthrough"></a>选项卡式应用程序演练

在本演练中，我们将创建以下应用程序：

[![示例选项卡式应用](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

尽管 Visual Studio for Mac 中已经有了一个选项卡式应用程序模板，但在此示例中，这些说明适用于空项目，以更好地了解应用程序的构建方式。

### <a name="creating-the-application"></a>创建应用程序

首先创建一个新的应用程序。

选择该 **文件 > 新建 > 解决方案** "菜单项 Visual Studio for Mac 并选择" **IOS > 应用程序 > 空项目** 模板，将项目命名为，如下 `TabbedApplication` 所示：

[![选择空的项目模板](creating-tabbed-applications-images/newsolution1.png)](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![将项目命名为 TabbedApplication](creating-tabbed-applications-images/newsolution2.png)](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>添加 UITabBarController

接下来，通过选择 "文件" **> 新文件** 并选择 " **常规：空类** 模板" 来添加一个空类。 为文件命名 `TabController` ，如下所示：

[![添加 TabController 类](creating-tabbed-applications-images/02-newclass.png)](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController`类将包含的实现，该实现 `UITabBarController` 将管理的数组 `UIViewControllers` 。 当用户选择某个选项卡时， `UITabBarController` 将负责为相应的视图控制器呈现视图。

若要实现， `UITabBarController` 需要执行以下操作：

1. 将的基类设置  `TabController` 为  `UITabBarController` 。
1. 创建  `UIViewController` 要添加到的实例  `TabController` 。
1. 将  `UIViewController` 实例添加到分配给  `ViewControllers` 的属性的数组  `TabController` 。

将下面的代码添加到 `TabController` 类以实现以下步骤：

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

请注意，对于每个 `UIViewController` 实例，我们设置的 `Title` 属性 `UIViewController` 。 将控制器添加到时 `UITabBarController` ， `UITabBarController` 将读取 `Title` 每个控制器的，并将其显示在关联的选项卡的标签上，如下所示：

[![示例应用运行](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>将 TabController 设置为 RootViewController

控制器置于选项卡中的顺序对应于它们添加到数组中的顺序 `ViewControllers` 。

若要使 `UITabController` 作为第一个屏幕加载，需要将其设置为窗口的 `RootViewController` ，如以下代码所示 `AppDelegate` ：

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

如果现在运行应用程序， `UITabBarController` 将加载默认情况下选择的第一个选项卡。 选择任何其他选项卡会生成关联控制器的视图，如下 `UITabBarController,` 所示，最终用户已选择了第二个选项卡：

![显示的第二个选项卡](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>修改 TabBarItems

现在，我们有了一个正在运行的选项卡应用程序，接下来修改 `TabBarItem` 以更改显示的图像和文本，并将徽章添加到其中一个选项卡。

#### <a name="setting-a-system-item"></a>设置系统项

首先，将第一个选项卡设置为使用系统项。 在的构造函数中 `TabController` ，移除为实例设置控制器的行， `Title` 并将 `tab1` 其替换为以下代码以设置控制器的 `TabBarItem` 属性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

使用创建时 `UITabBarItem` `UITabBarSystemItem` ，iOS 会自动提供标题和图像，如以下屏幕截图中所示，在第一个选项卡上显示 " **收藏夹** " 图标和标题：

 ![带有星号图标的第一个选项卡](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>设置图像

除了使用系统项，可以将的标题和图像 `UITabBarItem` 设置为自定义值。 例如，更改名为的控制器的属性的代码，如下所示 `TabBarItem` `tab2` ：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上面的代码假定名为的映像已 `second.png` 添加到项目的根 (或 **资源** 目录) 。 若要支持所有屏幕密度，需要三个图像，如下所示：

![添加到项目中的图像](creating-tabbed-applications-images/tabbedimages7new.png)

有关正确维度的指导，请参阅[Apple 自定义图标页面](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/)的**选项卡栏图标大小**部分。 建议的大小根据图像样式 (圆形、方形、宽或高) 。

`Image`仅需要将属性设置为**second.png**文件名，而在需要时，iOS 将自动加载较高分辨率的文件。 有关详细信息，请参阅使用 [图像](~/ios/app-fundamentals/images-icons/index.md) 指南。 默认情况下，选项卡栏项为灰色，选中时为蓝色。

#### <a name="overriding-the-title"></a>覆盖标题

当 `Title` 直接在上设置属性时 `TabBarItem` ，它将覆盖 `Title` 在控制器本身上为设置的任何值。

此屏幕截图中的第二个 (中间) "选项卡显示自定义标题和图像：

![第二个选项卡带有正方形图标](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>设置徽章值

选项卡还可以显示徽章。 例如，添加以下代码行以在第三个选项卡上设置徽章：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

运行此操作会在选项卡的左上角出现一个红色标签，其字符串为 "Hi"，如下所示：

![带有徽标的第二个选项卡](creating-tabbed-applications-images/06-badge-sml.png)

徽章通常用于显示数字指示 "未读"、"新项"。 若要删除徽章，请将设置 `BadgeValue` 为 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>非 RootViewController 方案中的选项卡

在上面的示例中，我们演示了 `UITabBarController` 当它是窗口的时如何使用 `RootViewController` 。 在此示例中，我们将检查如何在 `UITabBarController` 不是的情况下使用 `RootViewController` ，并说明如何使用情节提要来创建此项。

### <a name="initial-screen-example"></a>初始屏幕示例

对于此方案，初始屏幕从不是的控制器加载 `UITabBarController` 。 如果用户通过点击按钮与屏幕进行交互，则会将同一个视图控制器加载到 `UITabBarController` 中，然后将其呈现给用户。 以下屏幕截图显示了应用程序流：

[![此屏幕截图显示了应用程序流](creating-tabbed-applications-images/inital-screen-application.png)](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

在此示例中，我们将启动新应用程序。 同样，我们将使用 **iPhone > 应用 > 空项目 (c # ) ** 模板，这次将项目命名为 `InitialScreenDemo` 。

在此示例中，演示图板用于设计视图控制器。 添加情节提要：

- 右键单击项目名称，然后选择 " **添加 > 新文件**"。

- 出现 "新建文件" 对话框时，导航到 " **iOS > 空 IPhone 情节提要**"。

接下来，我们将这个新的情节提要 **mainstoryboard.storyboard** ，如下所示：

[![将 Mainstoryboard.storyboard 文件添加到项目](creating-tabbed-applications-images/new-file-dialog.png)](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

将情节提要添加到 [之前的非](~/ios/user-interface/storyboards/index.md) 情节提要文件时，有几个需要注意的重要步骤。 它们是：

1. 将情节提要名称添加到**Main Interface** `Info.plist` ：

    [![将主界面设置为 Mainstoryboard.storyboard](creating-tabbed-applications-images/project-options.png)](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在中 `App Delegate` ，用以下代码替代窗口方法：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

在此示例中，我们将需要三个视图控制器。 一个名为 `ViewController1` 的，将用作初始视图控制器和第一个选项卡。 `ViewController2` `ViewController3` 将分别在第二个和第三个选项卡中使用的名为和的其他两个。

双击 "Mainstoryboard.storyboard" 文件，打开设计器，并将三个视图控制器拖动到设计图面上。 我们希望其中每个视图控制器都具有与上述名称对应的类，因此，在 " **标识 > 类**" 下键入其名称，如下面的屏幕截图中所示：

[![将类设置为 ViewController1](creating-tabbed-applications-images/class-name.png)](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio for Mac 将自动生成所需的类和设计器文件，可在 Solution Pad 中查看此内容，如下所示：

[![项目中自动生成的文件](creating-tabbed-applications-images/solution-pad2.png)](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>创建 UI

接下来，我们将使用 Xamarin iOS 设计器为 ViewController 的每个视图创建一个简单的用户界面。

我们想要将 `Label` 和 `Button` 从右侧的 **工具箱** 拖到 ViewController1 上。 接下来，我们将使用 Properties Pad 编辑控件的名称和文本，如下所示：

- **标签**： `Text`  =  **一**
- **Button** ： `Title`  =  **用户执行一些初始操作**

我们将在事件中控制按钮的可见性 `TouchUpInside` ，我们需要在代码隐藏中引用它。 让我们用 Properties Pad 中的 **名称**标识它 `aButton` ，如以下屏幕截图中所示：

[![在 Properties Pad 中将 "名称" 设置为 "aButton"](creating-tabbed-applications-images/abutton-properties.png)](creating-tabbed-applications-images/abutton-properties.png#lightbox)

你的 Design Surface 现在应类似于下面的屏幕截图：

[![你的 Design Surface 现在应类似于此屏幕截图](creating-tabbed-applications-images/design-surface1.png)](creating-tabbed-applications-images/design-surface1.png#lightbox)

让我们 `ViewController2` `ViewController3` 通过向每个标签添加一个标签，并分别将文本更改为 "2" 和 "3"，向和添加更多详细信息。 这会向用户显示我们正在查看的选项卡/视图。

#### <a name="wiring-up-the-button"></a>设置按钮的连线

`ViewController1`当应用程序第一次启动时，将加载。 当用户点击按钮时，将隐藏按钮，并 `UITabBarController` `ViewController1` 在第一个选项卡中使用实例加载。

当用户发布时 `aButton` ，我们希望触发 TouchUpInside 事件。 选择 "" 按钮，然后在 "属性" 面板的 " **事件" 选项卡** 中，声明事件处理程序– `InitialActionCompleted` 以便可以在代码中引用它。 下面的屏幕截图中阐释了这一点：

[![当用户释放 aButton 时，触发 TouchUpInside 事件](creating-tabbed-applications-images/event-handler.png)](creating-tabbed-applications-images/event-handler.png#lightbox)

现在，我们需要告知视图控制器在事件触发时隐藏按钮 `InitialActionCompleted` 。 在中 `ViewController1` ，添加以下分部方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

保存该文件，然后运行该应用程序。 屏幕上会显示 "屏幕显示" 按钮，该按钮将在触摸时消失。

#### <a name="adding-the-tab-bar-controller"></a>添加选项卡栏控制器

现在，我们的初始视图按预期方式工作。 接下来，我们想要将其添加到，并将其添加到 `UITabBarController` 视图2和3。 让我们在设计器中打开情节提要。

在 " **工具箱**" 中，搜索 "控制器 & 对象" 下的 **选项卡** ，并将其拖到 "Design Surface"。 正如你在下面的屏幕截图中所示，选项卡栏控制器是无 UI 的，因此在默认情况下会引入两个视图控制器：

[![将选项卡栏控制器添加到布局](creating-tabbed-applications-images/tabbarcontroller.png)](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

通过选择底部的黑色条并按 delete 来删除这些新的视图控制器。

在我们的情节提要中，可以使用 Segue 来处理 TabBarController 和我们的视图控制器之间的转换。 与初始视图交互后，我们希望将其加载到向用户显示的 TabBarController 中。 让我们在设计器中设置此项。

**按住 Ctrl** 并从按钮 **拖动** 到 TabBarController。 鼠标悬停时，将显示一个上下文菜单。 我们想要使用模式 segue。

若要设置每个选项 **卡，请按下从 TabBarController** 到每个视图控制器的顺序，从1到3，然后从上下文菜单中选择 "关系 **" 选项卡** ，如下所示：

[![选择选项卡关系](creating-tabbed-applications-images/context-menu.png)](creating-tabbed-applications-images/context-menu.png#lightbox)

情节提要应类似于以下屏幕截图：

[![演示图板应类似于此屏幕截图](creating-tabbed-applications-images/segue-layout.png)](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果单击其中一个选项卡项，并浏览 "属性" 面板，可以看到许多不同的选项，如下图所示：

[![设置 "属性资源管理器" 中的选项卡选项](creating-tabbed-applications-images/properties-panel.png)](creating-tabbed-applications-images/properties-panel.png#lightbox)

使用此工具可以编辑某些属性，如徽章、标题和 iOS 标识符，等等。

如果现在保存并运行应用程序，我们会发现在 ViewController1 实例加载到 TabBarController 时，按钮会重新出现。 让我们通过检查当前视图是否有父视图控制器来解决此问题。 如果是这样，我们知道我们在 TabBarController 内，因此应隐藏该按钮。 让我们将以下代码添加到 ViewController1 类：

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

当应用程序运行并且用户点击第一个屏幕上的按钮时，将加载 UITabBarController，并将第一个屏幕上的视图放置在第一个选项卡中，如下所示：

[![示例应用程序输出](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>总结

本文介绍了如何 `UITabBarController` 在应用程序中使用。 我们逐步介绍了如何将控制器加载到每个选项卡，以及如何在标题、图像和徽章等选项卡上设置属性。 然后，使用演示图板， `UITabBarController` 当它不是窗口时，如何在运行时加载 `RootViewController` 。

## <a name="related-links"></a>相关链接

- [ (示例创建选项卡式应用程序) ](/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 类引用](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)