---
title: 用 Xamarin 显示映像
description: 本文档介绍如何在 Xamarin 中显示图像。 它涉及以编程方式或通过 iOS 设计器将图像添加到应用。
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 900a4bf3407b4a3d37f6102ff4b10292edf4bd25
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434041"
---
# <a name="displaying-images-with-xamarinios"></a>用 Xamarin 显示映像

向应用程序添加图像需要两个步骤：首先，将图像添加到项目中;然后，添加控件和代码，将其显示在屏幕上。 有关 Xamarin 中的映像处理的详细信息，请参阅使用 [图像](~/ios/app-fundamentals/images-icons/index.md) 一文。

## <a name="adding-images-to-your-app"></a>向应用程序添加图像

可以将图像添加到 Visual Studio for Mac 解决方案中的任何文件夹，如果 " **生成" 操作** 设置为 " **内容** "，则该文件将包含在应用中并可显示。

Visual Studio for Mac 还支持名为 **资源** 的特殊目录，该目录还可以包含图像文件。 Resources 文件夹中的文件应将 **生成操作** 设置为 **BundleResource**。

此屏幕截图显示在右键单击文件时显示的 **生成操作** 选项：

 [!["生成操作" 菜单](image-images/image30a.png)](image-images/image30a.png#lightbox)

Visual Studio for Mac 通常会自动选择正确的 **生成操作** ，但是你应该知道这些设置，尤其是在你的项目中移动文件时。

### <a name="adding-an-image-file"></a>添加图像文件

若要将图像文件添加到项目中，请先右键单击该项目，然后选择 "**添加文件 ...** "

 [![添加文件 .。。下拉菜单](image-images/image31a.png)](image-images/image31a.png#lightbox)

选择要包含在标准文件对话框中的图像 (或图像) 。 将 **BundleResource** 映像的默认生成操作–不要覆盖此值，除非有特定的原因。

 [!["添加文件" 对话框](image-images/image32a.png)](image-images/image32a.png#lightbox)

该图像将添加到项目中，并可在代码中加载和显示。 此屏幕截图显示了添加到 iOS 应用程序项目的图像：

 [![项目中的图像](image-images/image33a.png)](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什么是资源目录？

放置在 **Resources** 目录中的文件的处理方式与常规文件不同– **资源** 文件夹的内容将被复制到应用程序的根目录，并且可以在代码中引用。 这可能会很多原因：

- 存储在应用程序的属性中配置的映像，例如，默认启动映像和应用程序图标。
- 将其他图像和文件与代码分开存储，以便在将资源目录内容复制) 时，更易于管理 (子目录。

**资源**目录在库项目中特别有用，因为代码可以假设这些图像将复制到使用应用程序的根目录中，这样就可以更轻松地编写需要图像、声音、视频、XML 或其他文件的共享代码库。

**Resources**目录必须为，因此，所有文件都应将生成操作设置为**BundleResource**。

## <a name="displaying-the-image"></a>显示图像

在 iOS 设计器中，使用 **图像视图** 显示图像或动画序列的图像。 工具箱中的 **图像视图** 图标如下所示：

 [![工具箱中的 ImageView](image-images/image35a.png)](image-images/image35.png#lightbox)

将 " **图像" 视图** 从 " **工具箱** " 拖到视图控制器上。 然后，在 " **图像视图 > 图像** " 下，下拉列表将提供项目中所有可用图像文件的列表。 选择其中任何一个，以将其添加到图像视图。

 [![工具箱中的 ImageView](image-images/image36a.png)](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以编程方式显示图像

由于 **SF Monkey.jpg** 位于 **Resources** 目录的根目录中，因此它将在应用程序捆绑包的根中的运行时可用。 若要在图像视图控件中显示此图像，请使用以下代码：

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

如果将图像置于 **/Resources/Pics/SF Monkey.jpg**中，则代码会在路径中包括 " **图片** " 文件夹：

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

资源文件引用决不需要包含 **Resources** 文件夹。

## <a name="related-links"></a>相关链接

- [控制 (示例) ](/samples/xamarin/ios-samples/controls)