---
title: 在 Xamarin 中使用 tvOS 页面控件
description: 本文档介绍如何在使用 Xamarin 生成的应用程序中使用 tvOS 页面控件。 它提供页控件的高级说明，讨论如何在情节提要中对其进行设置，并检查如何响应页面更改事件。
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 47e9c1ed4a043f7d11b5dfd52dca903e0a613b33
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436425"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 页面控件

有时，可能需要在 tvOS 应用中显示一系列页面或图像。 页面控件旨在清楚地显示用户在最多页的最大数目。 页面控件显示一系列的点，这些点针对的是深色 oval 形背景。 当前页将显示一个实心点，所有其他页显示为空心点。 如果背景区域内容纳太多无法容纳，则页面控件将剪裁外最多点。

[![示例页控件](page-controls-images/page01.png)](page-controls-images/page01.png#lightbox)

非交互式元素中的页面控件，旨在仅向用户提供反馈。 您将需要添加其他控件以更改当前页码 (例如) 的手势或按钮。

Apple 在使用页面控件时具有以下建议：

- **仅对完全集合使用** -页面控件在全屏环境中最适合用于显示存在于单个集合中的多个页面。
- **限制页面数** -页控件最适合10个 (10 个) 或更少的页，最多可以有20个 (20) 页。 对于20多页，请考虑使用 [集合视图](~/ios/tvos/user-interface/collection-views.md) 并在网格中显示页。

<a name="Page-Controls-and-Storyboards"></a>

## <a name="page-controls-and-storyboards"></a>页面控件和情节提要

在 tvOS 应用程序中使用页面控件的最简单方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 从 "**工具箱**" 中将**页面控件**拖放到视图上：

    [![页面控件](page-controls-images/page02.png)](page-controls-images/page02.png#lightbox)
1. 在**Properties Pad**的 "**小组件" 选项卡**中，可以调整页控件的多个属性，如其**当前页**和**页数**：

    [!["小组件" 选项卡](page-controls-images/page03.png)](page-controls-images/page03.png#lightbox)
1. 接下来，向视图添加控件或笔势，以便在页的集合中向后移动。
1. 最后，为控件分配 **名称** ，以便可以在 c # 代码中对其进行响应。 例如：

    [![命名控件](page-controls-images/page04.png)](page-controls-images/page04.png#lightbox)
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 从 "**工具箱**" 中将**页面控件**拖放到视图上：

    [![页面控件](page-controls-images/page02-vs.png)](page-controls-images/page02-vs.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中，可以调整页控件的多个属性，如其**当前页**和**页数**：

    [!["小组件" 选项卡](page-controls-images/page03-vs.png)](page-controls-images/page03-vs.png#lightbox)
1. 接下来，向视图添加控件或笔势，以便在页的集合中向后移动。
1. 最后，为控件分配 **名称** ，以便可以在 c # 代码中对其进行响应。 例如：

    [![命名控件](page-controls-images/page04-vs.png)](page-controls-images/page04-vs.png#lightbox)
1. 保存所做更改。

-----

> [!IMPORTANT]
> 尽管可以 `TouchUpInside` 在 IOS 设计器中将事件（例如 UIButton) ）分配给 UI (元素，但绝不会调用此类事件，因为 APPLE TV 没有触摸屏或支持触控事件。 `Primary Action`创建 tvOS 用户界面元素的事件处理程序时，应始终使用事件。

编辑视图控制器 (示例 `ViewController.cs`) 文件，并添加代码以处理所更改的页面。 例如：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

让我们进一步了解一下页面控件的两个属性。 首先，若要指定最大页数，请使用以下内容：

```csharp
PageView.Pages = 6;
```

若要更改当前页码，请使用以下代码：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`属性为零 (0) ，因此第一页为零，最后一页为零，最大页数为减一。

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用页面控件。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)