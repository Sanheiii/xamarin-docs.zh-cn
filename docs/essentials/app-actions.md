---
title: Xamarin.Essentials：应用操作
description: 通过 Xamarin.Essentials 的 Accelerometer 类，可以从应用程序图标创建和响应应用程序快捷方式。
ms.assetid: 5edf9bc5-b721-448c-a8a2-0a9d4d0c792c
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: add9143ff1a9546d3d2c8cfb851f621083bf356d
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414655"
---
# <a name="no-locxamarinessentials-app-actions"></a>Xamarin.Essentials：应用操作

通过 AppActions 类，可以从应用程序图标创建和响应应用程序快捷方式。

![预发行版 API](~/media/shared/preview.png)

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 AppActions 功能，需要以下特定于平台的设置。

# <a name="android"></a>[Android](#tab/android)

在 `MainActivity` 中添加以下逻辑来处理操作：

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume(this);
}

protected override void OnNewIntent(Intent intent)
{
    base.OnNewIntent(intent);

    Xamarin.Essentials.Platform.OnNewIntent(intent);
}
```

# <a name="ios"></a>[iOS](#tab/ios)

在 `AppDelegate.cs` 中添加以下逻辑来处理操作：

```csharp
public override void PerformActionForShortcutItem(UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
    => Xamarin.Essentials.Platform.PerformActionForShortcutItem(application, shortcutItem, completionHandler);
```

# <a name="uwp"></a>[UWP](#tab/uwp)

在 `App.xaml.cs` 文件的 `OnLaunched` 方法中，将以下逻辑添加到该方法的底部：

```csharp
Xamarin.Essentials.Platform.OnLaunched(e);
```

-----

## <a name="create-actions"></a>创建操作

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```
应用操作可随时创建，但通常在应用程序启动时创建。 调用 `SetAsync` 方法，为应用创建操作列表。


```csharp
try
{
    await AppActions.SetAsync(
        new AppAction("app_info", "App Info", icon: "app_info_action_icon"),
        new AppAction("battery_info", "Battery Info"));
}
catch (FeatureNotSupportedException ex)
{
    Debug.WriteLine("App Actions not supported");
}
```

如果特定版本的操作系统不支持应用操作，则会引发 `FeatureNotSupportedException`。 

可以在 `AppAction` 上设置下列属性：

* ID：用于响应操作点击的唯一标识符。
* 标题：要显示的可见标题。
* 副标题：如果支持副标题，则在标题下面显示它。
* 图标：必须与每个平台的相应资源目录中的图标匹配。

![主屏幕上的应用操作](images/appactions.png)

## <a name="responding-to-actions"></a>响应操作

当应用程序启动时，注册 `OnAppAction` 事件。 选择某个应用操作时，系统会发送该事件，其中包含所选操作的相关信息。

```csharp
public App()
{
    //...
    AppActions.OnAppAction += AppActions_OnAppAction;
}

void AppActions_OnAppAction(object sender, AppActionEventArgs e)
{
    // Don't handle events fired for old application instances
    // and cleanup the old instance's event handler
    if (Application.Current != this && Application.Current is App app)
    {
        AppActions.OnAppAction -= app.AppActions_OnAppAction;
        return;
    }
    Device.BeginInvokeOnMainThread(async () =>
    {
        var page = e.AppAction.Id switch
        {
            "battery_info" => new BatteryPage(),
            "app_info" => new AppInfoPage(),
            _ => default(Page)
        };
        if (page != null)
        {
            await Application.Current.MainPage.Navigation.PopToRootAsync();
            await Application.Current.MainPage.Navigation.PushAsync(page);
        }
    });
}
```

## <a name="getactions"></a>GetActions
可以通过调用 `AppActions.GetAsync()` 来获取当前的应用操作列表。

## <a name="api"></a>API

- [AppActions 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppActions)
- [AppActions API 文档](xref:Xamarin.Essentials.AppActions)
