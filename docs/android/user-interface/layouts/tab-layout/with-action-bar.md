---
title: 带有 ActionBar 的选项卡式布局
description: 本指南介绍和说明如何使用 ActionBar Api 在 Xamarin Android 应用程序中创建选项卡式用户界面。
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: b1e6fadb52f6009e28d18143ac24c5180dae037c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457037"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>带有 ActionBar 的选项卡式布局

_本指南介绍和说明如何使用 ActionBar Api 在 Xamarin Android 应用程序中创建选项卡式用户界面。_

## <a name="overview"></a>概述

操作栏是一个 Android UI 模式，用于为选项卡、应用程序标识、菜单和搜索等关键功能提供一致的用户界面。 在 Android 3.0 (API level 11) 中，Google 将 ActionBar Api 引入 Android 平台。 ActionBar Api 引入 UI 主题，以便为选项卡式用户界面提供一致的外观和外观。 本指南讨论如何向 Xamarin Android 应用程序添加操作栏选项卡。 本文还讨论了如何使用 Android 支持库 v7 将 ActionBar 选项卡向后移植到面向 Android 2.1 2.3 的 Xamarin Android 应用程序。 

请注意， `Toolbar` 是一个较新的更一般化操作栏组件，你应使用而不是 `ActionBar` (`Toolbar` 来替换 `ActionBar`) 。 有关详细信息，请参阅 [Toolbar](~/android/user-interface/controls/tool-bar/index.md)。 

## <a name="requirements"></a>要求

面向 API level 11 (Android 3.0) 或更高版本的任何 Xamarin 应用程序均可作为本机 Android Api 的一部分访问 ActionBar Api。 

一些 ActionBar Api 已回)  (Android 2.1，可通过 [V7 AppCompat 库](https://developer.android.com/tools/support-library/features.html#v7-appcompat)获得，可通过 [Xamarin android 支持库-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) 包提供给 xamarin android 应用。

## <a name="introducing-tabs-in-the-actionbar"></a>ActionBar 中的选项卡简介

操作栏尝试同时显示其所有选项卡，并根据最宽选项卡标签的宽度使所有选项卡大小相等。 下面的屏幕截图对此进行了说明： 

![显示了所有大小相等的选项卡的 ActionBar 的示例屏幕截图](with-action-bar-images/image1.png)

当 ActionBar 无法显示所有选项卡时，它将在水平滚动视图中设置选项卡。 用户可以向左或向右轻扫以查看剩余的选项卡。 Google Play 的这一屏幕截图显示了一个示例： 

![水平滚动视图中选项卡的示例屏幕截图](with-action-bar-images/image2.png)

操作栏中的每个选项卡都应与一个 [*片段*](~/android/platform/fragments/index.md)相关联。 当用户选择某个选项卡时，应用程序将显示与该选项卡关联的片段。ActionBar 不负责向用户显示相应的片段。 相反，ActionBar 将通过实现 ActionBar 接口的类在选项卡中通知应用程序的状态更改。 此接口提供了三种回拨方法，当选项卡的状态发生更改时，将调用该方法： 

- **OnTabSelected** -当用户选择该选项卡时，将调用此方法。它应显示片段。

- **OnTabReselected** -当选项卡已选中但用户再次选择该选项卡时，将调用此方法。 此回调通常用于刷新/更新所显示的片段。

- **OnTabUnselected** -当用户选择另一个选项卡时，将调用此方法。此回调用于在显示的片段消失之前保存状态。

Xamarin 对类中的 `ActionBar.ITabListener` 事件进行包装。 `ActionBar.Tab` 应用程序可将事件处理程序分配给一个或多个此类事件。 " `ActionBar.ITabListener` 操作栏" 选项卡将引发的) 中的每个方法都有三个事件 (： 

- TabSelected
- TabReselected
- TabUnselected

### <a name="adding-tabs-to-the-actionbar"></a>将选项卡添加到 ActionBar

ActionBar 是 Android 3.0 (API level 11) 和更高版本，可用于以此 API 为目标的任何 Xamarin 应用程序。 

以下步骤演示了如何将 ActionBar 选项卡添加到 Android 活动： 

1. 在 `OnCreate` &ndash; *初始化任何 UI 小组件之前*，在活动的方法中， &ndash; 应用程序必须将上的设置 `NavigationMode` 为， `ActionBar` `ActionBar.NavigationModeTabs` 如以下代码片段所示：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 使用创建新选项卡 `ActionBar.NewTab()` 。

3. 分配事件处理程序或提供一个自定义 `ActionBar.ITabListener` 实现，该实现将响应用户与 ActionBar 选项卡交互时引发的事件。

4. 将在上一步中创建的选项卡添加到 `ActionBar` 。

下面的代码是使用这些步骤将选项卡添加到使用事件处理程序来响应状态更改的应用程序的一个示例： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```

#### <a name="event-handlers-vs-actionbaritablistener"></a>事件处理程序与 ActionBar。 ITabListener

应用程序应使用事件处理程序和 `ActionBar.ITabListener` 不同方案。 事件处理程序确实提供一定程度的语法便利性;这样，便无需创建类并实现 `ActionBar.ITabListener` 。 这是一项成本 Xamarin 的便利 &ndash; 。 Android 为你执行此转换，创建一个类并 `ActionBar.ITabListener` 为你实现。 当应用程序的选项卡数量有限时，这种情况很正常。 

当处理多个选项卡时，或在 ActionBar 选项卡之间共享公共功能时，在内存和性能方面可以更高效地创建一个实现的自定义类 `ActionBar.ITabListener` ，并共享类的单个实例。 这会减少 Xamarin Android 应用程序使用的 GREF 的数量。 

### <a name="backwards-compatibility-for-older-devices"></a>旧设备的向后兼容性

[Android 支持库 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) Back 端口 ActionBar 选项卡到 Android 2.1 (API level 7) 。 一旦将此组件添加到项目中，就可以在 Xamarin Android 应用程序中访问选项卡。

若要使用 ActionBar，活动必须为子类 `ActionBarActivity` 并使用 AppCompat 主题，如以下代码片段所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活动可从属性获取对其 ActionBar 的引用 `ActionBarActivity.SupportingActionBar` 。 下面的代码片段演示了在活动中设置 ActionBar 的示例：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```

## <a name="summary"></a>总结

在本指南中，我们介绍了如何使用 ActionBar 在 Xamarin Android 中创建选项卡式用户界面。 我们介绍了如何将选项卡添加到 ActionBar，以及如何通过接口将活动与选项卡事件交互 `ActionBar.ITabListener` 。 此外，我们还了解 Android 支持库 v7 AppCompat 包如何 precise-backports 旧版 Android 的 ActionBar 选项卡。 

## <a name="related-links"></a>相关链接

- [ActionBarTabs (示例) ](/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [工具栏](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [操作栏模式](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)