---
title: 为 Xamarin iOS 应用启动屏幕
description: 本文介绍如何使用单个统一的情节提要，以任何分辨率和方向为所有 iOS 设备创建应用启动屏幕。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: f4f24ee8bfc6bdde0becb9539ff9e2f532d06381
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432029"
---
# <a name="launch-screens-for-xamarinios-apps"></a>为 Xamarin iOS 应用启动屏幕

_本文介绍如何使用单个统一的情节提要，以任何分辨率和方向为所有 iOS 设备创建应用启动屏幕。_

在 iOS 8 之前，为 iOS 应用程序创建启动屏幕要求开发人员提供应用程序运行时所用的各种设备外形规格和分辨率的图像资产。 由于 iOS 8 的发布，因此可以使用单个统一的情节提要来创建在所有情况下均正确显示的启动屏幕。

此 brief 演练介绍了如何使用默认情况下在新项目中提供的情节提要或手动添加到现有项目中的情节提要创建启动屏幕。 然后，它演示了如何使用 iOS 设计器将图像视图和标签添加到情节提要，为这些视图设置约束，并验证情节提要对于各种设备和方向是否正确显示。

<a name="storyboard"></a>

## <a name="managing-launch-screens-with-storyboards"></a>用情节提要管理启动屏幕

在 iOS 8 (及更高版本) 中，开发人员可以创建一个特殊的统一情节提要来提供启动屏幕，而不是使用一个或多个静态启动图像。 在 iOS 设计器中创建启动情节提要时，可使用大小类和自动布局来定义不同显示环境的不同布局。 通过使用 Size 类和自动布局，开发人员可以创建一个在所有设备上都能正常运行并显示环境的启动屏幕。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，选择 " **文件" > "新建解决方案** "，然后选择 " **单视图应用**"，创建一个新项目： 

    ![已选择 "单视图应用" 的 "新建项目" 窗口](launch-screens-images/launch01.png)

    - 默认情况下，新项目包含定义启动屏幕界面的 **LaunchScreen** 文件。 
    - 若要改为将启动屏幕情节提要添加到现有项目，请在 **Solution Pad** 中右键单击项目名称，然后选择 " **添加 > 新文件 ...** "，然后选择 " **启动屏幕**"：

    ![已选择 iOS 启动屏幕的 "新建文件" 窗口](launch-screens-images/launch01b.png)

    - 将该文件命名为 **LaunchScreen** 或您选择的其他名称。

2. 将项目配置为使用合适的情节提要启动屏幕：

    - 双击**Solution Pad**中的**info.plist**文件，将其打开以进行编辑。
    - 在 " **启动映像** " 部分中，确保 " **启动屏幕** " 设置为相应情节提要的名称：

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch02.png)

    - 默认情况下，新项目配置为使用 **LaunchScreen** 作为其启动屏幕。

3. 将图像添加到 **Assets.xcassets** 资产目录，使其可用于启动屏幕。 有关详细信息，请参阅[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南的将[图像添加到资产目录图像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)部分。

4. 打开 **LaunchScreen** 进行编辑，方法是在 **Solution Pad**中双击它。

5. 选择要在 iOS 设计器中预览启动屏幕情节提要的设备和方向。 打开底部工具栏上的 "设备选择" 面板，然后选择 " **IPhone 4S** " 和 " **纵向**"。

    ![设备选择工具栏](launch-screens-images/launch05.png)

    - 请注意，选择设备和方向仅会改变 iOS 设计器预览设计的方式。 无论在什么情况下进行选择，新添加的约束都将应用于所有设备和方向，除非已使用 " **编辑特征** " 按钮进行指定。 

6. 设置视图控制器主视图的 **背景** 色。 通过单击视图控制器的中间，并使用 **Properties Pad**调整背景色来选择视图：

    ![具有紫色背景色的单个视图](launch-screens-images/launch06.png)

7. 向启动屏幕添加一个 **图像视图** ，并设置其源 **映像**：

    - 将 " **图像" 视图** 从 " **工具箱** " 拖到视图的中心。
    - 选中 "**图像" 视图**后，在**Properties Pad**的 "**小组件**" 部分中，将 " **image** " 属性设置为已添加到**assets.xcassets**资产目录中的映像集。 根据需要重新定位 **图像视图** 并调整其大小：
    
    ![具有图像属性集的图像视图](launch-screens-images/launch07.png)

8. 在**图像视图**下面添加一个**标签**，并使用**Properties Pad**设置其属性： 

    ![带有文本和颜色集的标签](launch-screens-images/launch08.png)

9. 使用 " **约束" 工具栏**中的右侧按钮切换到 "约束编辑模式"：
    
    !["约束编辑模式" 按钮](launch-screens-images/launch09.png)

10. 向 **图像视图**添加约束，设置其高度和宽度并水平和垂直居中对齐：

    ![带有布局约束的图像视图](launch-screens-images/launch10.png)

    - 有关如何添加约束的详细信息，请参阅 [与 Xamarin Designer for iOS 自动布局](~/ios/user-interface/designer/designer-auto-layout.md)。

11. 将约束添加到 **标签**，将其水平居中，使其成为高度和宽度，并将其垂直放置在 **图像视图**的固定距离上：

    ![带有布局约束的标签](launch-screens-images/launch11.png)

12. 测试其他设备和方向，以验证设计在所有方案中是否按预期方式显示。 如果需要对特定设备或方向进行调整，请使用 " **编辑特征** " 按钮添加特定大小类的约束：

    ![使用横向方向呈现为 iPhone X 的启动屏幕](launch-screens-images/launch12.png)

13. 保存对情节提要所做的更改。 在模拟器或设备上运行应用，启动屏幕将在应用启动时可见。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 创建新项目。 在 Visual Studio 中，选择 "文件" " **> 新建 > 项目" > Visual c # > iPhone & iPad **> "

    !["新建项目" 窗口，其中包含 iOS 应用 (Xamarin) 选择](launch-screens-images/launch01.w157.png)

    选择 " **单一视图应用程序** " 模板，然后单击 **"确定"**：

    ![单一视图应用模板](launch-screens-images/launch01-2.w157.png)

2. 如果**解决方案资源管理器**中存在 **> LaunchScreen 的资源**，请通过右键单击该文件并选择 "**删除**" 来删除它。 此文件将替换为下一步中的情节提要。

3. 创建要用作启动屏幕的情节提要。 在 **解决方案资源管理器**中，右键单击项目，然后选择 " **添加 > 新项 ...** "，后跟 **空情节提要**。 将此情节提要命名为 **LaunchScreen** ，然后单击 " **添加**"：

    ![已选中空情节提要的 "添加新项" 窗口](launch-screens-images/launch03.w157.png)

4. 将项目配置为使用 **LaunchScreen** 作为其启动屏幕情节提要：

    - 在“解决方案资源管理器”  中，双击 **Info.plist** 文件，打开它进行编辑。 
    - 在 " **视觉对象资产** " 选项卡上，将 " **启动屏幕** " 设置为 **LaunchScreen**。

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch04-vs.png)

5. 将图像添加到项目中的资产目录，以使其可用于 "启动" 屏幕：

    - 在 **解决方案资源管理器**中，右键单击 " **资产目录** "，然后选择 " **添加资产目录**"。 为此新资产目录命名 **资产**：

    !["添加新项" 窗口，已选择资产目录](launch-screens-images/launch05.w157.png)

    - 如[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南的将[图像添加到资产目录图像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)部分中所述，将新图像集添加到**资产**资产目录中。

6. 打开 **LaunchScreen** 进行编辑，方法是在 **解决方案资源管理器**中双击它。

    - 若要编辑情节提要文件，Visual Studio 需要与 Mac 生成主机建立活动连接。 有关详细信息，请参阅 [连接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 指南。

7. 选择要在 iOS 设计器中预览启动屏幕情节提要的设备和方向。 打开底部工具栏上的 "设备选择" 面板，然后选择 " **IPhone 4S** 和 **纵向**"： 

    ![设备选择工具栏](launch-screens-images/launch07-vs.png)

    - 请注意，选择设备和方向仅会改变 iOS 设计器预览设计的方式。 无论在什么情况下进行选择，新添加的约束都将应用于所有设备和方向，除非已使用 " **编辑特征** " 按钮进行指定。 

8. 将 **视图控制器** 从 **工具箱** 拖到设计图面，将其添加到情节提要： 

    ![添加到设计图面的空视图控制器](launch-screens-images/launch08-vs.png)

9. 设置视图控制器主视图的 **背景** 色。 通过单击视图控制器的中间点并使用 " **属性" 窗口**调整背景色来选择视图：
    
    ![具有紫色背景色的单个视图](launch-screens-images/launch09-vs.png)

10. 向启动屏幕添加一个 **图像视图** ，并设置其源 **映像**：

    - 将 " **图像" 视图** 从 " **工具箱** " 拖至视图的中心。
    - 如果仍选中 "**图像" 视图**，则在 "属性"**窗口**的 "**小组件**" 部分中，将 "**图像**" 属性设置为已添加到**资产**资产目录中的映像集。 根据需要重新定位 **图像视图** 并调整其大小：
    
    ![具有图像属性集的图像视图](launch-screens-images/launch10-vs.png)

11. 在**图像视图**下面添加一个**标签**：

    - 将 **标签** 从 " **工具箱** " 拖到设计图面上，将其放置在 **图像视图**的下方。
    - 使用 "**属性" 窗口**设置**标签**的属性：

    ![带有文本和颜色集的标签](launch-screens-images/launch11-vs.png) 

12. 使用 " **约束" 工具栏**中的右侧按钮切换到 "约束编辑模式"：
    
    !["约束编辑模式" 按钮](launch-screens-images/launch12-vs.png) 

13. 向 **图像视图**添加约束，设置其高度和宽度并水平和垂直居中对齐：

    ![带有布局约束的图像视图](launch-screens-images/launch13-vs.png) 

    - 有关添加约束的信息，请参阅 [与 Xamarin Designer for iOS 自动布局](~/ios/user-interface/designer/designer-auto-layout.md)。

14. 将约束添加到 **标签**，将其水平居中，使其成为高度和宽度，并将其垂直放置在 **图像视图**的固定距离上：
    
    ![带有布局约束的标签](launch-screens-images/launch14-vs.png) 

15. 测试其他设备和方向，以验证设计在所有方案中是否按预期方式显示。 如果需要对特定设备或方向进行调整，请使用 " **编辑特征** " 按钮添加特定大小类的约束：

    ![使用横向方向呈现为 iPhone X 的启动屏幕](launch-screens-images/launch15-vs.png) 

16. 保存对情节提要所做的更改。 在模拟器或设备上运行应用，启动屏幕将在应用启动时可见。

-----

> [!NOTE]
> 用作 _启动屏幕的情节提要只能包含_ 简单的内置 UI 元素， **不能** 进行任何计算或从自定义类派生。

有关使用统一情节提要创建启动屏幕的详细信息，请参阅[统一](~/ios/user-interface/storyboards/unified-storyboards.md)情节提要指南的[动态启动](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)屏幕部分。

## <a name="migrating-to-launch-screen-storyboards"></a>迁移到启动屏幕情节提要

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

更新现有应用程序以使用演示图板启动屏幕时，右键单击 "**解决方案资源管理器**中的**项目名称**，然后选择"**添加**  >  **新文件 ...**"。选择 " **iOS**  >  " "**启动" 屏幕**，然后单击 "**新建**" 按钮：

![选择 iOS 启动屏幕](launch-screens-images/storyboard02.png)

接下来， `Info.plist` 在 **解决方案资源管理器** 中双击该文件以将其打开以进行编辑。 在 " **启动" 屏幕**下，选择上面创建的新情节提要文件。

![选择上面创建的新情节提要文件](launch-screens-images/storyboard09.png)

若要使用新的情节提要作为启动屏幕，请执行以下操作：

1. 双击 `Info.plist` " **解决方案资源管理器** 中的文件以将其打开以进行编辑。
2. 滚动到编辑器的 " **通用启动映像** " 部分，打开 " **启动屏幕** " 下拉列表，选择上面创建的情节提要的名称： 

    ![将启动屏幕设置为情节提要](launch-screens-images/storyboard08.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 右键单击 "**解决方案资源管理器**中的项目名称，然后选择"**添加**  >  **新文件 ...**"： 

    ![添加新文件](launch-screens-images/image012.png)
2. 输入启动屏幕的名称，然后单击 " **添加** " 按钮： 

    ![输入启动屏幕的名称](launch-screens-images/image013.png)
3. 在 **解决方案资源管理器**中，双击新创建的情节提要文件，将其打开以进行编辑。
4. 确保 **Size 类** 设置为 **any： any** ，并将 **视图** 设置为 **泛型**： 

    ![确保 Size 类设置为 any： any，并将视图设置为泛型](launch-screens-images/image016.png)
5. 从大小类、简单 UI 元素 (（例如， `UIImageView` 应用程序捆绑包中包括) 和图像）的启动屏幕。 

    ![在 iOS 设计器中组装启动屏幕](launch-screens-images/image017.png)
6. 保存对情节提要所做的更改。

-----

## <a name="related-links"></a>相关链接

- [动态启动屏幕 (示例) ](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [统一的情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 设计器基本知识](~/ios/user-interface/designer/index.md)
- [将图像添加到资产目录图像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [用 Xamarin Designer for iOS 自动布局](~/ios/user-interface/designer/designer-auto-layout.md)
- [人体学接口准则：启动屏幕](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)