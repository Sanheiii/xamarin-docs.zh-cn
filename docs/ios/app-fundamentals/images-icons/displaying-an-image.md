---
title: 在 Xamarin 中显示映像
description: '本文介绍如何在 Xamarin iOS 应用中包括图像资产，并使用 c # 代码或通过将其分配给 iOS 设计器中的控件来显示该图像。'
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/24/2018
ms.openlocfilehash: a7bc2b87e267e78d841217b7860e5d54c046b788
ms.sourcegitcommit: d7c09c6cc2f479b8f14910ad2d20ec76800cd9c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91248134"
---
# <a name="displaying-an-image-in-xamarinios"></a>在 Xamarin 中显示映像

_本文介绍如何在 Xamarin iOS 应用中包括图像资产，并使用 c # 代码或通过将其分配给 iOS 设计器中的控件来显示该图像。_

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>在 Xamarin iOS 应用中添加和组织映像

添加要在 Xamarin iOS 应用中使用的映像时，开发人员将使用 _资产目录_ 来支持应用所需的每个 iOS 设备和分辨率。

已在 iOS 7 中添加， **资产目录映像集** 包含支持各种设备和应用的规模因素所需的映像的所有版本或表示形式。 **图像集**使用 Json 文件来指定属于哪个设备和/或分辨率，而不是依赖于映像资产文件名。 这是在 ios 9 或更高) 版本中管理和支持 iOS (中的映像的首选方法。

## <a name="adding-images-to-an-asset-catalog-image-set"></a>将图像添加到资产目录图像集

如上所述， **资产目录图像集** 包含支持各种设备所需的映像的所有版本或表示形式，以及应用的规模因素。 **图像集**使用 Json 文件来指定属于哪个设备和/或分辨率，而不是依赖于映像资产文件名。

若要创建新的映像集并向其中添加映像，请执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **解决方案资源管理器**中，双击该 `Assets.xcassets` 文件以将其打开进行编辑：

    ![解决方案资源管理器中的 assets.xcassets](displaying-an-image-images/imageset01.png)
2. 右键单击 "资产" **列表** ，然后选择 " **新建映像集**"：

    ![添加新图像集](displaying-an-image-images/imageset02.png)
3. 选择新图像集，将显示编辑器：

    ![图像集编辑器](displaying-an-image-images/imageset03.png)
4. 在此处，为每个所需的不同设备和分辨率拖动图像。
5. 双击 "**资产" 列表**中的新图像集**名称**进行编辑： ![ 编辑新映像集的名称](displaying-an-image-images/imageset04.png)

使用 iOS 设计器中的 **图像集** 时，只需在属性编辑器的下拉列表中选择集的名称即可：

![从下拉列表中选择图像集的名称](displaying-an-image-images/imageset06.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 从 " **解决方案资源管理器**打开" 资产目录 "，然后在左上角，单击 **加号** 按钮：

    ![单击加号按钮](displaying-an-image-images/asset5.png)

2. 选择 " **添加图像集** "，将显示新图像集的 "图像集编辑器"。 在此处，为每个所需的不同设备和分辨率拖动图像。

    ![图像集编辑器](displaying-an-image-images/asset7.png)

### <a name="renaming-an-image-set"></a>重命名图像集

若要重命名映像集，请执行以下操作：

1. 在 **解决方案资源管理器**中，双击 **资产目录** 文件以将其打开进行编辑：

    ![解决方案资源管理器中的资产目录](displaying-an-image-images/rename01.png)
2. 选择要重命名的 **图像集** ：

    ![选择要重命名的图像集](displaying-an-image-images/rename02.png)
3. 在 "**属性资源管理器**" 中，滚动到底部，选择 "**杂项**" 部分下的 "**名称**" () ：

    ![在 "杂项" 部分下选择名称](displaying-an-image-images/rename03.png)
4. 为**映像集**输入新**名称**，并保存所做的更改。

-----

在代码中使用 **图像集** 时，通过调用类的方法按名称引用它 `FromBundle` `UIImage` 。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> 如果分配给图像集的图像未正确显示，请确保将正确的文件名与该方法一起使用 `FromBundle` (**图像集** ，而不是父 **资产目录** 名称) 。 对于 PNG 图像， `.png` 可以省略扩展。 对于其他图像格式，需要扩展 (例如。 `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>在资产目录中使用矢量图像

从 iOS 8 开始，特殊 **Vector** 类已添加到 **图像集** ，使开发人员能够在卡带中包含 **PDF** 格式的矢量图像，而不是在不同的分辨率下包含单独的位图文件。 使用此方法可为分辨率提供单个矢量文件 `@1x` (格式为矢量 PDF 文件) 并且 `@2x` 文件的和 `@3x` 版本将在编译时生成并包含在应用程序捆绑包中。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![资产目录编辑器中的矢量图像](displaying-an-image-images/imageset05.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![资产目录编辑器中的矢量图像](displaying-an-image-images/asset8.png)

-----

例如，如果开发人员包括一个 `MonkeyIcon.pdf` 文件作为资产目录的矢量，并且分辨率为 150 x 150，则在编译后，最终的应用程序捆绑包中将包含以下位图资产：

- `MonkeyIcon@1x.png` -150 x 150 解决方案。
- `MonkeyIcon@2x.png` -300 像素 x 300 像素解决方案。
- `MonkeyIcon@3x.png` -450px x 450px 解决方案。

在资产目录中使用 PDF 矢量图像时，应注意以下事项：

- 这不是完整的矢量支持，因为 PDF 将在编译时将栅格化到位图，并在最终应用程序中附带位图。
- 在资产目录中设置图像后，无法调整图像大小。 如果开发人员试图在代码中或者通过使用自动布局和大小类来调整图像 (大小) 图像将像任何其他位图一样扭曲。
- 资产目录仅兼容 iOS 7 及更高版本，如果应用需要支持 iOS 6 或更低版本，则它不能使用资产目录。

## <a name="working-with-template-images"></a>使用模板映像

根据 iOS 应用的设计，有时，开发人员可能需要自定义用户界面内的图标或图像，以便与配色方案中的更改相匹配 (例如，基于用户首选项) 。

若要轻松实现此效果，请将图像资产的 _呈现模式_ 切换到 **模板映像**：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![呈现模式设置为模板映像](displaying-an-image-images/templateimage01.png)](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![呈现模式设置为模板](displaying-an-image-images/templateimage01vs.png)](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

在 iOS 设计器中，将图像资产分配给 UI 控件，并将 **色调** 设置为着色图像：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![设置色调以为图像着色](displaying-an-image-images/templateimage03.png)](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![设置色调以为图像着色](displaying-an-image-images/templateimage03vs.png)](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

也可以在代码中直接设置图像资产和淡色：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要完全从代码中使用模板映像，请执行以下操作：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因为 `RenderMode` 的属性 `UIImage` 是只读的，所以请使用 `ImageWithRenderingMode` 方法，通过所需的呈现模式设置创建映像的新实例。

有三种可能的 `UIImage.RenderMode` 通过枚举的设置 `UIImageRenderingMode` ：

- `AlwaysOriginal` -强制将图像呈现为原始源映像文件，而不进行任何更改。
- `AlwaysTemplate` -通过使用指定颜色着色像素来强制将图像呈现为模板图像 `Tint` 。
- `Automatic` -将图像作为模板呈现，或者根据它所使用的环境呈现原始映像。 例如，如果在、或中使用图像，则会将 `UIToolBar` `UINavigationBar` `UITabBar` `UISegmentControl` 其视为模板。

## <a name="adding-new-assets-collections"></a>添加新资产集合

使用资产目录中的图像时，有时需要新的集合，而不是将所有应用程序的映像添加到 `Assets.xcassets` 集合中。 例如，在设计按需资源时。

向项目中添加新的资产目录：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击 "**解决方案资源管理器**中的**项目名称**，然后选择"**添加**  >  **新文件 ...** "
2. 选择 " **iOS**  >  **资产目录**"，输入集合的**名称**，然后单击 "**新建**" 按钮：

    ![创建新的资产目录](displaying-an-image-images/asset01.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在解决方案资源管理器中，右键单击 " **资产目录** " 文件夹，然后选择 " **添加 > 新资产目录**"。
2. 为其命名，然后单击 " **添加**"：

    ![创建新的资产目录](displaying-an-image-images/asset1.png)

-----

在此处，集合的工作方式与 `Assets.xcassets` 自动包含在项目中的默认集合的方式相同。

## <a name="using-images-with-controls"></a>在控件中使用图像

除了使用映像支持应用外，iOS 还会使用包含应用控件类型（如选项卡栏、工具栏、导航栏、表格和按钮）的图像。 要使图像显示在控件上，一种简单的方法是将 `UIImage` 实例分配给控件的 `Image` 属性。

### <a name="frombundle"></a>FromBundle

`FromBundle`方法调用是一个同步 (阻止) 调用，该调用具有许多内置的映像加载和管理功能，例如缓存支持并自动处理各种分辨率的图像文件。

下面的示例演示如何在上设置的图像 `UITabBarItem` `UITabBar` ：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假设 `MyImage` 是添加到资产目录中的图像资产的名称。 工作资产目录映像时，只需在 `FromBundle` 方法中为 **PNG** 格式的图像指定图像集的名称即可：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

对于任何其他图像格式，请将扩展名包含在该名称中。 例如：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

有关图标和图像的详细信息，请参阅有关 [自定义图标和映像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)的 Apple 文档。

## <a name="displaying-an-image-in-a-storyboard"></a>在情节提要中显示图像

使用资产目录将图像添加到 Xamarin iOS 项目后，可以使用 `UIImageView` IOS 设计器中的来轻松地在情节提要上显示该图像。 例如，如果已添加以下图像资产：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![添加了示例图像资产](displaying-an-image-images/display01.png)

执行以下操作以在情节提要中显示它：

1. 双击 `Main.storyboard` " **解决方案资源管理器** 中的文件以将其打开，以便在 iOS 设计器中进行编辑。
2. 从**工具箱**中选择一个**图像视图**：

     ![从工具箱中选择一个图像视图](displaying-an-image-images/display02.png)
3. 将图像视图拖动到设计图面上，并根据需要调整其位置和大小：

    ![Design Surface 上的新图像视图](displaying-an-image-images/display03.png)
4. 在**属性资源管理器**的 "**小组件**" 部分中，选择要显示的所需**图像**资产：

    ![选择要显示的所需图像资产](displaying-an-image-images/display04.png)
5. 在 " **视图** " 部分中，使用 **模式** 来控制调整 **图像视图** 大小时如何调整图像的大小。
6. 选中 " **图像" 视图** 后，再次单击以添加 **约束**：

    ![添加约束](displaying-an-image-images/display05.png)
7. 将 **图像视图** 的每个边缘上的 "T" 形控点拖到屏幕的相应侧，以将图像 "固定" 到侧面。 这样，当调整屏幕大小时， **图像视图** 就会缩小和增大。
8. 保存对情节提要所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![添加了示例图像资产](displaying-an-image-images/display01vs.png)

执行以下操作以在情节提要中显示它：

1. 双击 `Main.storyboard` " **解决方案资源管理器** 中的文件以将其打开，以便在 iOS 设计器中进行编辑。
2. 从**工具箱**中选择一个**图像视图**：

     ![从工具箱中选择一个图像视图](displaying-an-image-images/display02vs.png)
3. 将图像视图拖动到设计图面上，并根据需要调整其位置和大小：

    ![Design Surface 上的新图像视图](displaying-an-image-images/display03vs.png)
4. 在**属性资源管理器**的 "**小组件**" 部分中，选择要显示的所需**图像**资产：

    ![选择要显示的所需图像资产](displaying-an-image-images/display04vs.png)
5. 在 " **视图** " 部分中，使用 **模式** 来控制调整 **图像视图** 大小时如何调整图像的大小。
6. 选中 " **图像" 视图** 后，再次单击以添加 **约束**：

    ![添加约束](displaying-an-image-images/display05vs.png)
7. 将 **图像视图** 的每个边缘上的 "T" 形控点拖到屏幕的相应侧，以将图像 "固定" 到侧面。 这样，当调整屏幕大小时， **图像视图** 就会缩小和增大。
8. 保存对情节提要所做的更改。

-----

## <a name="displaying-an-image-in-code"></a>在代码中显示图像

就像在情节提要中显示图像一样，将图像添加到使用资产目录的 Xamarin iOS 项目后，可以使用 c # 代码轻松地显示它。

执行以下示例：

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

此代码将创建一个新的 `UIImageView` ，并为其提供初始大小和位置。 然后，它从添加到项目的图像资产中加载图像，并将添加到 `UIImageView` 父项 `UIView` 以显示它。

## <a name="related-links"></a>相关链接

- [使用图像 (示例) ](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [ (Apple) 的图像大小和分辨率 ](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
