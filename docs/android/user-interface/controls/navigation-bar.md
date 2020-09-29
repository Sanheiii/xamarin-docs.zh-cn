---
title: Xamarin Android 导航栏
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: 67c5655c3bbea8cd0a8c21f27719221f599bf481
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457427"
---
# <a name="xamarinandroid-navigation-bar"></a>Xamarin Android 导航栏

Android 4 引入了一个名为 " *导航栏*" 的新的系统用户界面功能，该功能可在不包含 **Home**、 **Back**和 **Menu**硬件按钮的设备上提供导航控件。
以下屏幕截图显示了结点质数设备的导航栏：

 [![Android 导航栏示例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

可以使用多个新标志来控制导航栏及其控件的可见性，以及 Android 3 中引入的系统栏的可见性。 标志在类中定义，如下所示 `Android.View.View` ：

- `SystemUiFlagVisible`&ndash;使导航栏可见。
- `SystemUiFlagLowProfile`&ndash;缩小导航栏中的控件。
- `SystemUiFlagHideNavigation`&ndash;隐藏导航栏。

可以通过设置属性将这些标志应用于视图层次结构中的任何视图 `SystemUiVisibility` 。 如果有多个视图设置了此属性，系统会将它们与或操作组合在一起，并应用它们，只要设置了标志的窗口保持焦点。 删除视图时，还将删除其设置的所有标志。

下面的示例演示一个简单的应用程序，其中单击任意按钮将更改 `SystemUiVisibility` ：

 [![演示可见、低配置文件和隐藏 SystemUiVisibility 的屏幕截图](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

用于更改的代码 `SystemUiVisibility` 将 `TextView` 从每个按钮的单击事件处理程序中设置的属性，如下所示：

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);

lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};

hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};

visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

此外，更改还会 `SystemUiVisibility` 引发 `SystemUiVisibilityChange` 事件。 与设置属性一样 `SystemUiVisibility` ， `SystemUiVisibilityChange` 可为层次结构中的任何视图注册事件的处理程序。 例如，下面的代码使用 `TextView` 实例注册事件：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>相关链接

- [SystemUIVisibilityDemo (示例) ](/samples/xamarin/monodroid-samples/systemuivisibilitydemo)