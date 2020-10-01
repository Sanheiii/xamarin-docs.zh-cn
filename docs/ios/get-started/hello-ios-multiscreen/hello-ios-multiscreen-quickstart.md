---
title: Hello，iOS 多屏显示 – 快速入门
description: 本文档演示如何扩展 Phoneword 示例应用程序以添加第二个屏幕，并在此过程中介绍了模型-视图-控制器、iOS 导航以及其他核心 iOS 开发概念。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 94bb87e10b8cab2a9a37c7701b59d621f6329b8d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436174"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Hello，iOS 多屏显示 – 快速入门

本部分演练将向 Phoneword 应用程序添加第二个屏幕，将显示使用该应用进行呼叫的电话号码历史记录。 最终的应用程序将拥有第二个屏幕，可显示呼叫历史记录，如下面的屏幕截图所示：

[![最终的应用程序将拥有第二个屏幕，可显示呼叫历史记录，如此屏幕快照所示](hello-ios-multiscreen-quickstart-images/00.png)](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

随附的[深度分析](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)将回顾已生成的程序，并讨论体系结构、导航和此过程中遇到的其他新的 iOS 概念。

## <a name="requirements"></a>要求

本指南将从《了解 iOS》文档中断的位置继续，并要求完成[了解 iOS 快速入门](~/ios/get-started/hello-ios/index.md)。 可从 [iOS 示例入门](/samples/xamarin/ios-samples/hello-ios)下载 Phoneword 应用的已完成版本。

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>macOS 上的演练

本演练将向 **Phoneword** 应用程序添加一个“呼叫历史记录”屏幕。

1. 在 Visual Studio for Mac 中打开 **Phoneword** 应用程序。 如有必要，可从[此处](/samples/xamarin/ios-samples/hello-ios)下载 [了解 iOS 演练](~/ios/get-started/hello-ios/index.md)指南中涉及的完整 Phoneword 应用程序。

2. 从 **Solution Pad** 打开 **Main.storyboard** 文件：

    ![iOS 设计器中的 Main.storyboard](hello-ios-multiscreen-quickstart-images/02new.png)

3. 将“工具箱”中的“导航控制器”拖动到设计图面（可能需将其缩小以适应设计图面的所有大小！） ：

    ![将“导航控制器”从“工具箱”拖动到设计图面上](hello-ios-multiscreen-quickstart-images/03new.png)

4. 将 sourceless segue（即单个视图控制器左侧的灰色箭头）拖动到“导航控制器”，以更改应用程序的起点 ：

    ![将“Sourceless Segue”拖到“导航控制器”，更改应用程序的起点](hello-ios-multiscreen-quickstart-images/04new.png)

5. 单击底部栏选择现有的“根视图控制器”，按“删除”将其从设计图面删除 。
然后，将 Phoneword 场景移动到“导航控制器”旁 ：

    ![将 Phoneword 场景移动到“导航控制器”旁](hello-ios-multiscreen-quickstart-images/05new.png)

6. 将“ViewController”设为“导航控制器”的“根视图控制器” 。 按下 Ctrl 键，并在“导航控制器”内单击 。 此时应出现一条蓝色线条。 然后，仍然按住“Ctrl”不放，从“导航控制器”拖动到“Phoneword”场景，然后松开  。 这称为“按住 Ctrl 键拖动”：

    ![从“导航控制器”拖动到 Phoneword 场景，然后松开](hello-ios-multiscreen-quickstart-images/06.png)

7. 在弹出窗口中，将关系设置为**根**：

    ![将关系设置为“根”](hello-ios-multiscreen-quickstart-images/07new.png)

    ViewController 现在是导航控制器的根视图控制器 ：

    ![现在，ViewController 是导航控制器的根视图控制器](hello-ios-multiscreen-quickstart-images/08.png)

8. 双击 **Phoneword** 屏幕的“标题”栏，然后将**标题**更改为 **Phoneword**：

    ![将标题更改为“Phoneword”](hello-ios-multiscreen-quickstart-images/09.png)

9. 从“工具箱”拖动某个**按钮**，将其置于“呼叫按钮”下。 拖动图柄，使新的**按钮**与“呼叫按钮”宽度相同：

    ![将新按钮的宽度设置为与“呼叫按钮”相同](hello-ios-multiscreen-quickstart-images/10new.png)

10. 在 Properties Pad 中，将按钮“名称”更改为“CallHistoryButton”，并将“标题”更改为“呼叫历史记录”：

    ![将“按钮”的“名称”更改为 CallHistoryButton，并将“标题”更改为“呼叫历史记录”](hello-ios-multiscreen-quickstart-images/11new.png)

11. 创建“呼叫历史记录”屏幕。 在“工具箱”中，将表格视图控制器拖到设计图面上 ：

    ![将“表格视图控制器”拖到设计图面上](hello-ios-multiscreen-quickstart-images/12new.png)

12. 接下来，单击“场景”底部的黑色栏选择“表格视图控制器”。 在 Properties Pad 中，将表格视图控制器的类更改为 `CallHistoryController`，并按“Enter”  ：

    ![将 Table View Controllers 类更改为 CallHistoryController](hello-ios-multiscreen-quickstart-images/13new.png)

    iOS 设计器将生成一个名为 `CallHistoryController` 的自定义后备类，以管理屏幕的“内容视图层次结构”。 **CallHistoryController.cs** 文件将在 **Solution Pad** 中出现：

    ![Solution Pad 中的 CallHistoryController.cs 文件](hello-ios-multiscreen-quickstart-images/14new.png)

13. 双击 **CallHistoryController.cs** 文件即可将其打开，并将内容替换为以下代码：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    保存应用程序 (**⌘ + s**) 并进行生成 (**⌘ + b**)，以确保没有错误。

14. 创建 Phoneword 场景和呼叫历史记录场景之间的 Segue（转换） 。
  在“Phoneword 场景”中，选择“呼叫历史记录按钮”，然后按住 Ctrl 拖动，从“按钮”拖动到“呼叫历史记录”场景   ：

    ![按住 Ctrl，从“按钮”拖动到“呼叫历史记录”场景](hello-ios-multiscreen-quickstart-images/15.png)

    从“操作 Segue”弹出框，选择“显示”

    iOS 设计器将在两个场景之间添加 Segue：

    ![两个场景之间的 Segue](hello-ios-multiscreen-quickstart-images/17new.png)

15. 在 Properties Pad 中，选择“场景”底部的黑色栏并将“视图控制器标题”更改为“呼叫历史记录”，向“表格视图控制器”添加“标题”    ：

    ![在 Properties Pad 中将“视图控制器”标题更改为“呼叫历史记录”](hello-ios-multiscreen-quickstart-images/18new.png)

16. 应用程序运行时，“呼叫历史记录按钮”将打开“呼叫历史记录”屏幕，但表格视图将为空，因为没有代码来记录并显示电话号码 。

    此应用会将电话号码以字符串列表的形式进行存储。

    在 **ViewController** 顶部对 `System.Collections.Generic` 添加 `using` 指令：

    ```csharp
    using System.Collections.Generic;
    ```

17. 使用以下代码修改 `ViewController` 类：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    此处有几个需要注意的要点：

    - 变量 `translatedNumber` 已从 `ViewDidLoad` 方法移到类级变量。
    - 修改了 CallButton 代码，以通过调用 `PhoneNumbers.Add(translatedNumber)` 将已拨号码添加到电话号码列表。
    - 已添加 `PrepareForSegue` 方法。

    保存并生成应用程序，确保没有错误。

18. 在 **iOS 模拟器**内按“启动”按钮，启动该应用程序：

    ![在 iOS 模拟器内按“启动”按钮启动该应用程序](hello-ios-multiscreen-quickstart-images/19.png)

恭喜，你已完成第一个多屏 Xamarin.iOS 应用程序！

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Windows 上的演练

本演练将向 **Phoneword** 应用程序添加一个“呼叫历史记录”屏幕。

1. 在 Visual Studio 中打开 **Phoneword** 应用程序。 如有必要，可从[了解 iOS 演练](~/ios/get-started/hello-ios/index.md)指南下载[完整的 Phoneword 应用程序](/samples/xamarin/ios-samples/hello-ios)。 注意，必须连接 [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 才可使用 iOS 设计器和 iOS 模拟器。

2. 从编辑用户界面开始。 从“解决方案资源管理器”打开 **Main.storyboard** 文件，确保“查看方式”已设为 iPhone 6：

    ![iOS 设计器中的 Main.storyboard](hello-ios-multiscreen-quickstart-images/image1.png)

3. 将“导航控制器”从“工具箱”拖动到设计图面上 ：

    ![将“导航控制器”从“工具箱”拖动到设计图面上](hello-ios-multiscreen-quickstart-images/image2.png)

4. 将“Sourceless Segue”（即“Phoneword”场景左侧的灰色箭头）从“Phoneword”场景拖动到“导航控制器”，以更改应用程序的起点   ：

    ![将“Sourceless Segue”拖到“导航控制器”，更改应用程序的起点](hello-ios-multiscreen-quickstart-images/image3.png)

5. 单击黑色栏选择“根视图控制器”，按“删除”将其从设计图面删除 。
  然后，将 Phoneword 场景移动到“导航控制器”旁 ：

    ![将 Phoneword 场景移动到“导航控制器”旁](hello-ios-multiscreen-quickstart-images/image4.png)

6. 将“ViewController”设为“导航控制器”的“根视图控制器”。 按“Ctrl”键，并在“导航控制器”内单击 。 此时应出现一条蓝色线条。 然后，仍然按住“Ctrl”不放，从“导航控制器”拖动到“Phoneword”场景，然后松开  。 这称为“按住 Ctrl 键拖动”：

    ![从“导航控制器”拖动到 Phoneword 场景，然后松开](hello-ios-multiscreen-quickstart-images/image5.png)

7. 在弹出窗口中，将关系设置为**根**：

    ![将关系设置为“根”](hello-ios-multiscreen-quickstart-images/image6.png)

    现在，“ViewController”是导航控制器的根视图控制器 。

8. 双击 **Phoneword** 屏幕的“标题”栏，然后将**标题**更改为 **Phoneword**：

    ![将标题更改为“Phoneword”](hello-ios-multiscreen-quickstart-images/image7.png)

9. 从“工具箱”拖动某个**按钮**，将其置于“呼叫按钮”下。 拖动图柄，使新的**按钮**与“呼叫按钮”宽度相同：

    ![将新按钮的宽度设置为与“呼叫按钮”相同](hello-ios-multiscreen-quickstart-images/image8.png)

10. 在“属性资源管理器”中，将**按钮**的**名称**更改为 `CallHistoryButton`，并将**标题**更改为**呼叫历史记录**：

    ![将“按钮”的“名称”更改为“CallHistoryButton”，并将“标题”更改为“呼叫历史记录”](hello-ios-multiscreen-quickstart-images/image9.png)

11. 创建“呼叫历史记录”屏幕。 在“工具箱”中，将表格视图控制器拖到设计图面上 ：

    ![将“表格视图控制器”拖到设计图面上](hello-ios-multiscreen-quickstart-images/image10.png)

12. 单击“场景”底部的黑色栏，选择“表格视图控制器”。 在“属性资源管理器”中，将表格视图控制器的类更改为 `CallHistoryController`，并按“Enter”  ：

    ![将 Table View Controllers 类更改为 CallHistoryController](hello-ios-multiscreen-quickstart-images/image11.png)

    iOS 设计器将生成一个名为 `CallHistoryController` 的自定义后备类，以管理屏幕的“内容视图层次结构”。 **CallHistoryController.cs** 文件将在**解决方案资源管理器**中出现：

    ![解决方案资源管理器中的 CallHistoryController.cs 文件](hello-ios-multiscreen-quickstart-images/image12.png)

13. 双击 **CallHistoryController.cs** 文件即可将其打开，并将内容替换为以下代码：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    保存应用程序，以确保没有错误。 可以暂时忽略所有生成警告。

14. 创建 Phoneword 场景和呼叫历史记录场景之间的 Segue（转换） 。
  在“Phoneword 场景”中，选择“呼叫历史记录按钮”，然后“按住 Ctrl 拖动”，从“按钮”拖动到“呼叫历史记录”场景    ：

    ![按住 Ctrl，从“按钮”拖动到“呼叫历史记录”场景](hello-ios-multiscreen-quickstart-images/image13.png)

    从“操作 Segue”弹出框，选择“显示”：

    ![选择“显示”作为 segue 类型](hello-ios-multiscreen-quickstart-images/image14.png)

    iOS 设计器将在两个场景之间添加 Segue：

    ![两个场景之间的 Segue](hello-ios-multiscreen-quickstart-images/image15.png)

15. 在“属性资源管理器”中，选择“场景”底部的黑色栏并将“视图控制器”>“标题”更改为呼叫历史记录，向“表格视图控制器”添加标题    ：

    ![将“视图控制器”标题更改为“呼叫历史记录”](hello-ios-multiscreen-quickstart-images/image16.png)

16. 应用程序运行时，“呼叫历史记录按钮”将打开“呼叫历史记录”屏幕，但表格视图将为空，因为没有代码来记录并显示电话号码 。

    此应用会将电话号码以字符串列表的形式进行存储。

    在 **ViewController** 顶部对 `System.Collections.Generic` 添加 `using` 指令：

    ```csharp
    using System.Collections.Generic;
    ```

17. 使用以下代码修改 `ViewController` 类：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    此处有几个需要注意的要点
    - 变量 `translatedNumber` 已从 `ViewDidLoad` 方法移到类级变量。
    - 修改了 CallButton 代码，以通过调用 `PhoneNumbers.Add(translatedNumber)` 将已拨号码添加到电话号码列表
    - 已添加 `PrepareForSegue` 方法

    保存并生成应用程序，确保没有错误。

    保存并生成应用程序，确保没有错误。

18. 在 **iOS 模拟器**内按“启动”按钮，启动该应用程序：

    ![示例应用的第一个屏幕](hello-ios-multiscreen-quickstart-images/19.png)

恭喜，你已完成第一个多屏 Xamarin.iOS 应用程序！

::: zone-end

现在，应用可使用 Storyboard Segue 和代码处理导航。 现可详细分析刚刚在[了解 Android 多屏显示详述](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md)中了解到的工具和技能。

## <a name="related-links"></a>相关链接

- [Hello，iOS（示例）](/samples/xamarin/ios-samples/hello-ios)
- [iOS 人机界面指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 预配门户](https://developer.apple.com/ios/manage/overview/index.action)