---
title: 在 Xamarin 中使用 tvOS 文本和搜索字段
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中处理文本和搜索字段。 它提供文本和搜索字段的高级概述，并讨论键盘、情节提要集成、搜索数据模型等。
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: ae54f4f4cfb9b2c604be9c55d2a47303fb22485e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435568"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>在 Xamarin 中使用 tvOS 文本和搜索字段

需要时，你的 tvOS 应用可以从用户 (（例如用户 Id 和密码）) 使用文本字段和屏幕键盘来请求少量文本：

[![示例搜索字段](text-fields-and-search-images/intro01.png)](text-fields-and-search-images/intro01.png#lightbox)

你可以选择使用搜索字段提供应用内容的关键字搜索功能：

[![示例搜索结果](text-fields-and-search-images/intro02.png)](text-fields-and-search-images/intro02.png#lightbox)

本文档将介绍在 tvOS 应用中使用文本和搜索字段的详细信息。

<a name="About-Text-and-Search-Fields"></a>

## <a name="about-text-and-search-fields"></a>关于文本和搜索字段

如上所述，如有必要，你的 tvOS 可以提供一个或多个文本字段，以根据用户已安装) 的 tvOS 版本，使用屏幕 (或可选的蓝牙键盘从用户那里收集少量的文本。

此外，如果你的应用程序向用户提供大量内容 (例如音乐、电影或图片集合) ，你可能希望包含搜索字段，该字段允许用户输入少量文本来筛选可用项的列表。

<a name="Text-Fields"></a>

## <a name="text-fields"></a>文本字段

在 tvOS 中，文本字段显示为固定高度的圆角输入框，当用户单击它时，将显示屏幕键盘：

[![TvOS 中的文本字段](text-fields-and-search-images/text01.png)](text-fields-and-search-images/text01.png#lightbox)

当用户将 [焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 移到给定的文本字段时，它将增大并显示一个深阴影。 设计用户界面时，需要记住这一点，因为文本字段在处于焦点时可能会与其他 UI 元素重叠。

Apple 对于使用文本字段具有以下建议：

- 应**慎用文本输入**-由于屏幕键盘的性质，为用户输入较长的文本部分或填写多个文本字段非常枯燥。 更好的解决方案是使用选择列表或 [按钮](~/ios/tvos/user-interface/buttons.md)限制文本输入量。
- **使用提示传达目的** -文本字段在为空时可以显示占位符 "提示"。 如果适用，请使用提示来描述文本字段的用途而不是单独的标签。
- **选择相应的默认键盘类型** -tvOS 提供可为文本字段指定的多种不同用途的内置键盘类型。 例如，通过允许用户从最近输入的地址列表中进行选择，电子邮件地址键盘可以轻松地进入。
- **如果需要，请使用安全文本字段** -安全文本字段表示输入为点 (而不是实) 的字符。 收集敏感信息（例如密码）时，请始终使用安全文本字段。

<a name="Keyboards"></a>

## <a name="keyboards"></a>键盘

只要用户在用户界面中单击某个文本字段，就会显示线性屏幕键盘。 用户使用 [Siri 遥控器](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) 上的触摸表面从键盘中选择单个字母并输入所需的信息：

[![Siri 远程键盘](text-fields-and-search-images/keyboard01.png)](text-fields-and-search-images/keyboard01.png#lightbox)

如果当前视图上存在多个文本字段，将自动显示 " **下一步** " 按钮，以使用户转到下一个文本字段。 将显示最后一个文本字段的 " **完成** " 按钮，该字段将结束文本输入并将用户返回到上一屏幕。

用户随时还可以按 Siri 遥控器上的 **菜单** 按钮结束文本输入，并再次返回到上一屏幕。

Apple 提供了以下有关使用屏幕键盘的建议：

- **选择相应的默认键盘类型** -tvOS 提供可为文本字段指定的多种不同用途的内置键盘类型。 例如，通过允许用户从最近输入的地址列表中进行选择，电子邮件地址键盘可以轻松地进入。
- **如果合适，请使用键盘附件视图** -除了始终显示的标准信息外，还可以将可选的附件视图 (如图像或标签) 可以添加到屏幕键盘，以阐明文本输入的目的或帮助用户输入所需的信息。

有关使用屏幕键盘的详细信息，请参阅 Apple 的 [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)、 [管理键盘](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1)、 [用于数据输入的自定义视图](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) 和 iOS 文档的 [文本编程指南](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) 。

<a name="Search"></a>

## <a name="search"></a>搜索

搜索字段提供了一个专用屏幕，其中提供了一个文本字段和屏幕键盘，使用户能够筛选出键盘下显示的项的集合：

[![示例搜索结果](text-fields-and-search-images/search01.png)](text-fields-and-search-images/search01.png#lightbox)

当用户在搜索字段中输入字母时，以下结果会自动反映搜索结果。 用户随时都可以将焦点移动到结果，并选择其中一项。

Apple 提供了以下有关使用搜索字段的建议：

- **提供最新的搜索** -由于使用 Siri 遥控器输入文本可能比较繁琐，用户往往会重复搜索请求，因此请考虑在键盘区域下的当前结果之前添加一节最近的搜索结果。
- **如果可能，请限制结果的数量** -由于很大的项列表可能对用户难以分析和导航，因此请考虑限制返回的结果的数目。
- **如果需要，请提供搜索结果筛选器** -如果应用提供的内容适合自己，请考虑添加范围栏以允许用户进一步筛选返回的搜索结果。

有关详细信息，请参阅 Apple 的 [UISearchController 类引用](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)。

<a name="Working-with-Text-Fields"></a>

## <a name="working-with-text-fields"></a>使用文本字段

若要在 tvOS 应用中使用文本字段，最简单的方法是使用 iOS 设计器将它们添加到用户界面设计。

执行以下操作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击该 `Main.storyboard` 文件以将其打开以进行编辑。
1. 将设计图面上的一个或多个 **文本字段** 拖至视图：

    [![文本字段](text-fields-and-search-images/text02.png)](text-fields-and-search-images/text02.png#lightbox)
1. 选择**文本字段**，并在**Properties Pad**的 "**小组件**" 选项卡中为每个名称指定唯一**名称**：

    [![Properties Pad 的 "小组件" 选项卡](text-fields-and-search-images/text03.png)](text-fields-and-search-images/text03.png#lightbox)
1. 在 " **文本字段** " 部分中，可以定义元素，例如 **占位符** 提示和默认 **值**：

    [![文本字段部分](text-fields-and-search-images/text04.png)](text-fields-and-search-images/text04.png#lightbox)
1. 向下滚动以定义 **拼写检查**、 **大写** 和默认 **键盘类型**等属性：

    [![拼写检查，大小写和默认键盘类型](text-fields-and-search-images/text05.png)](text-fields-and-search-images/text05.png#lightbox)
1. 保存对情节提要所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在“解决方案资源管理器”**** 中，双击 `Main.storyboard` 文件，将其打开进行编辑。
1. 将设计图面上的一个或多个 **文本字段** 拖至视图：

    [![文本字段](text-fields-and-search-images/text02-vs.png)](text-fields-and-search-images/text02-vs.png#lightbox)
1. 选择 "**属性资源管理器**" 的 "**小组件**" 选项卡中的**文本字段**，并为每个名称指定唯一**名称**：

    [!["小组件" 选项卡](text-fields-and-search-images/text03-vs.png)](text-fields-and-search-images/text03-vs.png#lightbox)
1. 在 " **文本字段** " 部分中，可以定义元素，例如 **占位符** 提示和默认 **值**：

    [![文本字段部分](text-fields-and-search-images/text04-vs.png)](text-fields-and-search-images/text04-vs.png#lightbox)
1. 向下滚动以定义 **拼写检查**、 **大写** 和默认 **键盘类型**等属性：

    [![拼写检查，大小写和默认键盘类型](text-fields-and-search-images/text05-vs.png)](text-fields-and-search-images/text05-vs.png#lightbox)
1. 保存对情节提要所做的更改。

-----

在代码中，可以使用文本字段的属性来获取或设置该字段的值 `Text` ：

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

您可以选择使用 `Started` 和 `Ended` 文本字段事件来响应文本输入的开始和结束。

<a name="Working-with-Search-Fields"></a>

## <a name="working-with-search-fields"></a>使用搜索字段

若要在 tvOS 应用中使用搜索字段，最简单的方法是使用接口设计器将它们添加到用户界面设计。

执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击该 `Main.storyboard` 文件以将其打开以进行编辑。
1. 将新的集合视图控制器拖动到情节提要，以显示用户的搜索结果：

    [![集合视图控制器](text-fields-and-search-images/search02.png)](text-fields-and-search-images/search02.png#lightbox)
1. 在**Properties Pad**的 "**小组件**" 选项卡中，对 `SearchResultsViewController` **类**和 `SearchResults` **情节提要 ID**使用：

    [!["小组件" 选项卡](text-fields-and-search-images/search03.png)](text-fields-and-search-images/search03.png#lightbox)
1. 选择设计图面上的 **单元原型** 。
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中， `SearchResultCell` 为**类**和 `ImageCell` **标识符**使用：

    [!["小组件" 选项卡](text-fields-and-search-images/search04.png)](text-fields-and-search-images/search04.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡**中布局****单元原型**的设计并公开每个元素：

    [![布局单元原型的设计](text-fields-and-search-images/search05.png)](text-fields-and-search-images/search05.png#lightbox)
1. 保存对情节提要所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在“解决方案资源管理器”**** 中，双击 `Main.storyboard` 文件，将其打开进行编辑。
1. 将新的集合视图控制器拖动到情节提要，以显示用户的搜索结果：

    [![集合视图控制器](text-fields-and-search-images/seach02-vs.png)](text-fields-and-search-images/seach02-vs.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中， `SearchResultsViewController` 为**类**和 `SearchResults` **情节提要 ID**使用：

    [!["小组件" 选项卡](text-fields-and-search-images/search03-vs.png)](text-fields-and-search-images/search03-vs.png#lightbox)
1. 选择设计图面上的 **单元原型** 。
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中， `SearchResultCell` 为**类**和 `ImageCell` **标识符**使用：

    [!["小组件" 选项卡](text-fields-and-search-images/search04-vs.png)](text-fields-and-search-images/search04-vs.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡**中布局****单元原型**的设计并公开每个元素：

    [![布局单元原型的设计](text-fields-and-search-images/search05-vs.png)](text-fields-and-search-images/search05-vs.png#lightbox)
1. 保存对情节提要所做的更改。

-----

<a name="Provide-a-Data-Model"></a>

### <a name="provide-a-data-model"></a>提供数据模型

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

接下来，需要提供一个类作为用户将搜索的结果的数据模型。 在**解决方案资源管理器**中，右键单击项目名称，然后选择 "**添加**" "  >  **新文件 ...**  >  "**常规**  > **空类**并提供**名称**：

[![选择空类并提供名称](text-fields-and-search-images/search06.png)](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

接下来，需要提供一个类作为用户将搜索的结果的数据模型。 在**解决方案资源管理器**中，右键单击项目名称，然后选择 "**添加**  >  **新项 ...**  >  "**Apple**  > **杂项**  > **类**并提供**名称**：

[![选择类并提供名称](text-fields-and-search-images/search06-vs.png)](text-fields-and-search-images/search06-vs.png#lightbox)

-----

例如，允许用户按标题和关键字搜索图片集合的应用可能如下所示：

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell"></a>

### <a name="the-collection-view-cell"></a>集合视图单元格

数据模型到位后，编辑 **原型单元** (`SearchResultViewCell.cs`) 并使其看起来如下所示：

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

此 `UpdateUI` 方法将用于显示 **PictureInformation** 项的各个字段 (属性 `PictureInfo` 每次更新属性时命名的 UI 元素中) 。 例如，图像和与图片关联的标题。

<a name="The-Collection-View-Controller"></a>

### <a name="the-collection-view-controller"></a>集合视图控制器

接下来，编辑搜索结果集合视图控制器 (`SearchResultsViewController.cs`) ，并使其类似于以下内容：

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

首先，将 `IUISearchResultsUpdating` 接口添加到类，以处理用户正在更新的搜索控制器筛选器：

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

还定义了一个常数，以指定与上述接口设计器中定义的 ID 相匹配的 **原型单元** (的 id) 稍后在集合控制器请求新单元时将使用该 id：

```csharp
public const string CellID = "ImageCell";
```

将为要搜索的项的完整列表创建存储区，搜索筛选器术语和与该字词匹配的项列表：

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

更改时 `SearchFilter` ，会更新匹配项的列表，并重新加载集合视图的内容。 `FindPictures`例程负责查找与新搜索词匹配的项：

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

的值 `SearchFilter` 将更新 (这会在用户更改搜索控制器中的筛选器时) 更新结果集合视图：

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

此 `PopulatePictures` 方法最初填充可用项的集合：

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

就本示例来说，当收集视图控制器加载时，将在内存中创建所有示例数据。 在实际应用中，这些数据可能会从数据库或 web 服务中读取，并且仅在需要时才能从 overrunning Apple TV 的有限内存中继续进行。

`NumberOfSections`和 `GetItemsCount` 方法提供匹配项的数目：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

方法将根据在 "集合" 视图中的 `GetCell` 每一项的情节提要) 中定义的，返回一个新的 **原型单元** (`CellID` ：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

在 `WillDisplayCell` 显示单元格之前调用方法，以便可以对其进行配置：

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

`DidUpdateFocus`当用户突出显示 "结果" 集合视图中的项时，方法向用户提供视觉反馈：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

最后，该 `ItemSelected` 方法处理用户选择项 (单击 "结果" 集合视图中 Siri 远程) 的触摸面：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

如果搜索字段作为模式对话框视图显示 (在将其) 调用的视图顶部，请使用 `DismissViewController` 方法在用户选择项时消除搜索视图。 在此示例中，"搜索" 字段显示为 "选项卡视图" 选项卡的内容，因此不会在此处解除。

有关集合视图的详细信息，请参阅 [使用集合视图](~/ios/tvos/user-interface/collection-views.md) 文档。

<a name="Presenting the Search Field"></a>

### <a name="presenting-the-search-field"></a>显示搜索字段

搜索字段 (及其关联的屏幕键盘和搜索结果) 可通过两种主要方式向用户呈现在 tvOS 中：

- **模式对话框视图** -在当前视图和视图控制器上，搜索字段可以显示为全屏模式对话框视图。 这通常是为了响应用户单击按钮或其他 UI 元素而完成的。 当用户从搜索结果中选择某项时，将关闭该对话框。
- **查看内容** -搜索字段是给定视图的直接组成部分。 例如，作为选项卡视图控制器中的 "搜索" 选项卡的内容。

对于上面给出的可搜索图片列表，搜索字段在 "搜索" 选项卡中显示为 "查看内容"，而 "搜索" 选项卡视图控制器如下所示：

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

首先，定义了与在接口设计器中分配给搜索结果集合视图控制器的 **情节提要标识符** 相匹配的常数：

```csharp
public const string SearchResultsID = "SearchResults";
```

接下来，该 `ShowSearchController` 方法创建一个新的搜索视图集合控制器并显示它需要的内容：

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

在上述方法中，一旦 `SearchResultsViewController` 从情节提要中实例化，就会创建一个新的， `UISearchController` 用于向用户显示搜索字段和屏幕键盘。 ) 定义的搜索结果集合 (`SearchResultsViewController` 将显示在此键盘上。

接下来， `SearchBar` 将配置为包含 **占位符** 提示等信息。 这为用户提供了要进行的搜索类型的相关信息。

然后，通过以下两种方式之一向用户显示搜索字段：

- **模式对话框视图** - `PresentViewController` 调用方法以在现有视图上显示搜索全屏。
- **查看内容** - `UISearchContainerViewController` 创建包含搜索控制器的。 将 `UINavigationController` 创建一个以包含搜索容器，然后将导航控制器添加到视图控制器 `AddChildViewController (navController)` ，并将显示视图 `View.Add (navController.View)` 。

最后，根据表示类型， `ViewDidLoad` 或 `ViewDidAppear` 方法将调用 `ShowSearchController` 方法来向用户显示搜索：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

当应用运行时，如果用户选择了 "搜索" 选项卡，则会向用户显示完全未筛选的项目列表：

[![默认搜索结果](text-fields-and-search-images/intro02.png)](text-fields-and-search-images/intro02.png#lightbox)

当用户开始输入搜索词时，将按该术语筛选结果列表并自动更新：

[![筛选的搜索结果](text-fields-and-search-images/intro03.png)](text-fields-and-search-images/intro03.png#lightbox)

用户随时可以在搜索结果中将焦点切换到某个项目，然后单击 Siri 遥控器的触摸面以将其选中。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用文本和搜索字段。 它介绍了如何在界面设计器中创建文本和搜索集合内容，并显示了两种不同的方法，以便在 tvOS 中向用户显示搜索字段。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)