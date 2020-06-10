---
标题： "添加特定于 iOS 的格式设置" 说明： "本文介绍了如何设置特定于 iOS 的外观，而无需使用 Xamarin.Forms 自定义呈现器。"
ms-chap： xamarin assetid： CE50E207-D092-4D88-8439-1B51F178E7ED： xamarin 窗体作者： davidbritch： dabritch ms. 日期：01/29/2016 非 loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="adding-ios-specific-formatting"></a>添加特定于 iOS 的格式

设置特定于 iOS 格式的一种方法是为控件创建[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器，并为每个平台设置特定于平台的样式和颜色。

控制 iOS 应用外观的其他选项 Xamarin.Forms 包括：

- 配置 info.plist 中的显示选项[ **Info.plist**](#customizing-infoplist)
- 通过[ `UIAppearance` API](#uiappearance-api)设置控件样式

下面讨论了这些备选方法。

## <a name="customizing-infoplist"></a>自定义信息。 info.plist

**Info.plist**文件使你可以配置 iOS 应用程序的 renderering 的某些方面，例如如何（以及是否）显示状态栏。

例如， [Todo 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)使用以下代码在所有平台上设置导航栏颜色和文本颜色：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

结果显示在下面的屏幕代码段中。 请注意，状态栏项为黑色（无法在中设置此项， Xamarin.Forms 因为它是特定于平台的功能）。

![](theme-images/status-default-sml.png "iOS Theming")

理想情况下，状态栏也是白色-我们可以直接在 iOS 项目中完成此操作。 将以下条目添加到**info.plist** ，以强制将状态栏作为白色：

![](theme-images/info-plist.png "iOS Info.plist Entries")

或直接编辑相应的**info.plist**文件以包括：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

现在，当应用程序运行时，导航栏为绿色，其文本为白色（由于 Xamarin.Forms 格式设置） *，并且*由于特定于 iOS 的配置，状态栏文本也是白色的：

![](theme-images/status-white-sml.png "iOS Theming")

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可用于在许多 iOS 控件上设置视觉对象属性，*而无*需创建[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

将一行代码添加到**AppDelegate.cs** `FinishedLaunching` 方法，可以使用其属性来对给定类型的所有控件进行样式 `Appearance` 。 下面的代码包含两个示例：全局设置选项卡栏和切换控件的样式：

IOS 项目中的**AppDelegate.cs**

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

默认情况下，中的选定选项卡栏图标[`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
为蓝色：

![](theme-images/tabbar-default.png "Default iOS Tab Bar Icon in TabbedPage")

若要更改此行为，请设置 `UITabBar.Appearance` 属性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这会导致所选的选项卡为绿色：

![](theme-images/tabbar-custom.png "Green iOS Tab Bar Icon in TabbedPage")

使用此 API，你可以自定义Xamarin.Forms
`TabbedPage`在 iOS 上，代码非常少。 有关使用自定义呈现器设置选项卡的特定字体的详细信息，请参阅[自定义选项卡食谱](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)。

### <a name="uiswitch"></a>UISwitch

`Switch`控件是可以轻松地进行样式化的另一个示例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这两个屏幕捕获将在 `UISwitch` 左侧显示默认控件，并在 Todo 示例的右侧显示自定义版本（设置 `Appearance` ）： [Todo sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

![](theme-images/switch-default.png "默认 UISwitch 颜色") ![](theme-images/switch-custom.png "自定义的 UISwitch 颜色")

### <a name="other-controls"></a>其他控件

许多 iOS 用户界面控件可以使用[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)的默认颜色和设置其他属性。

## <a name="related-links"></a>相关链接

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自定义选项卡](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
