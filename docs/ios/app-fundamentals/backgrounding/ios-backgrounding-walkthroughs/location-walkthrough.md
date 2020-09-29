---
title: 演练-Xamarin 中的背景位置
description: 本文档提供有关如何在 backgrounded Xamarin iOS 应用程序中使用位置信息的演练。 它介绍了必需的设置、用户界面和应用程序状态。
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2350db2e8d4f43a33b0ce394e06ffd2c16b6b7ad
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436554"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>演练-Xamarin 中的背景位置

在此示例中，我们将构建一个 iOS 位置应用程序，用于打印有关当前位置的信息：纬度、经度和其他参数到屏幕。 此应用程序将演示如何在应用程序处于活动状态或 Backgrounded 时正确执行位置更新。

本演练介绍一些重要的后台处理概念，包括将应用注册为后台必需的应用程序、在应用程序 backgrounded 时暂停 UI 更新以及使用 `WillEnterBackground` 和 `WillEnterForeground` `AppDelegate` 方法。

## <a name="application-set-up"></a>应用程序设置

1. 首先， ** (c # ) > 单视图应用程序创建新的 iOS > 应用 **。 调用该 _位置_ ，并确保已选择 IPad 和 iPhone。

1. 位置应用程序在 iOS 中限定为后台必需的应用程序。 通过编辑项目的 **info.plist** 文件，将应用程序注册为位置应用程序。

    在 "解决方案资源管理器" 下，双击 " **info.plist** " 文件将其打开，然后滚动到列表的底部。 选中 " **启用后台模式** " 和 " **位置更新** " 复选框。

    在 Visual Studio for Mac 中，它将如下所示：

    [![选中 "启用后台模式" 和 "位置更新" 复选框](location-walkthrough-images/image7.png)](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio 中，需要通过添加以下键/值对，手动更新 **info.plist** ：

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 现在，应用程序已注册，可以从设备获取位置数据。 在 iOS 中， `CLLocationManager` 类用于访问位置信息，并可以引发提供位置更新的事件。

1. 在代码中，创建一个名 `LocationManager` 为的新类，它为用于订阅位置更新的各种屏幕和代码提供单一位置。 在 `LocationManager` 类中，创建一个名为的 `CLLocationManager` 实例 `LocMgr` ：

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    上面的代码在 [CLLocationManager](xref:CoreLocation.CLLocationManager) 类上设置了多个属性和权限：

    - `PausesLocationUpdatesAutomatically` –这是一个布尔值，可根据系统是否允许暂停位置更新来设置。 在某些设备上，它默认为 `true` ，这可能会导致设备在大约15分钟后停止获取后台位置更新。
    - `RequestAlwaysAuthorization` -应传递此方法以向应用程序用户授予允许在后台访问位置的选项。 `RequestWhenInUseAuthorization` 如果希望为用户授予仅在应用程序处于前台时才允许访问位置的选项，也可以传递。
    - `AllowsBackgroundLocationUpdates` –这是在 iOS 9 中引入的布尔属性，可将其设置为允许应用在挂起时接收位置更新。

    > [!IMPORTANT]
    > iOS 8 (和更高) 还需要 **info.plist** 文件中的条目，以将用户显示为授权请求的一部分。

1. 为应用程序所需的权限类型（、、和/或）添加 **info.plist 密钥。** 该应用程序需要 `NSLocationAlwaysUsageDescription` `NSLocationWhenInUseUsageDescription` `NSLocationAlwaysAndWhenInUseUsageDescription` 一个字符串，该字符串将在请求位置数据访问的警报中显示给用户。

1. iOS 9 要求在使用 `AllowsBackgroundLocationUpdates` **info.plist** 时包含 `UIBackgroundModes` 具有值的密钥 `location` 。 如果已完成本演练的步骤2，则该操作应已在 info.plist 文件中。

1. 在 `LocationManager` 类中，使用以下代码创建一个名为的方法 `StartLocationUpdates` 。 此代码显示了如何开始接收位置更新   `CLLocationManager` ：

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    此方法有几个重要事项。 首先，我们执行检查以查看应用程序是否有权访问设备上的位置数据。 我们通过调用来验证这一点 `LocationServicesEnabled` `CLLocationManager` 。 如果用户拒绝了应用程序对位置信息的访问，则此方法将返回 **false** 。

1. 接下来，告诉位置管理器更新的频率。 `CLLocationManager` 提供了许多用于筛选和配置位置数据的选项，包括更新的频率。 在此示例中，将设置 `DesiredAccuracy` 为，每当位置由计量器更改时更新。 有关配置位置更新频率和其他首选项的详细信息，请参阅 Apple 文档中的 [CLLocationManager 类引用](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) 。

1. 最后，对 `StartUpdatingLocation` 实例调用 `CLLocationManager` 。 这会告诉位置管理器在当前位置获取初始修补程序，以及开始发送更新

到目前为止，已创建位置管理器，并配置了要接收的数据类型，并确定了初始位置。 现在，代码需要将位置数据呈现给用户界面。 我们可以使用采用作为参数的自定义事件来执行此 `CLLocation` 操作：

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一步是订阅中的位置更新 `CLLocationManager` ，并在 `LocationUpdated` 新位置数据变得可用时引发自定义事件，并将该位置作为参数传入。 为此，请创建新类 **LocationUpdateEventArgs.cs**。 此代码可在主应用程序中访问，并在引发事件时返回设备位置：

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>用户界面

1. 使用 iOS 设计器生成将显示位置信息的屏幕。 双击要开始的 **主情节提要** 文件。

    在情节提要上，将若干标签拖到屏幕上，作为位置信息的占位符。 在此示例中，有用于纬度、经度、海拔高度、课程和速度的标签。

    布局应如下所示：

    ![IOS 设计器中的示例 UI 布局](location-walkthrough-images/image8.png)

1. 在 Solution Pad 中，双击该 `ViewController.cs` 文件并编辑它以创建 LocationManager 的新实例，并对 `StartLocationUpdates` 其调用。
  将代码更改为如下所示：

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
        get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
    }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    这会在应用程序启动时启动位置更新，但不会显示任何数据。

1. 收到位置更新后，请用位置信息更新屏幕。 下面的方法从事件获取位置 `LocationUpdated` ，并将其显示在 UI 中：

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

我们仍需要订阅 `LocationUpdated` AppDelegate 中的事件，并调用新方法来更新 UI。 在调用后立即在其中添加以下代码 `ViewDidLoad,` `StartLocationUpdates` ：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```

现在，当应用程序运行时，它应该如下所示：

[![示例应用运行](location-walkthrough-images/image5.png)](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>处理活动状态和后台状态

1. 当应用程序处于前台和活动状态时，将会输出位置更新。 若要演示应用进入后台时所发生的情况，请重写 `AppDelegate` 跟踪应用程序状态更改的方法，使应用程序在前台和后台之间转换时写入控制台：

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    在中添加以下代码，以便将 `LocationManager` 更新的位置数据连续打印到应用程序输出，以验证位置信息在后台是否仍然可用：

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. 此代码还有一个问题：尝试在应用程序 backgrounded 时更新 UI 会导致 iOS 终止。 当应用进入后台时，代码需要取消订阅位置更新并停止更新 UI。

    当应用程序要过渡到不同的应用程序状态时，iOS 会向我们提供通知。 在这种情况下，我们可以订阅 `ObserveDidEnterBackground` 通知。

    下面的代码段演示如何使用通知来让视图知道何时暂停 UI 更新。 这将在 `ViewDidLoad` ：

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    当应用程序运行时，输出将如下所示：

    ![控制台中位置输出的示例](location-walkthrough-images/image6.png)

1. 应用程序在前台操作时，会在屏幕上打印位置更新，并在后台操作时继续将数据打印到 "应用程序输出" 窗口。

仅存在一个未解决的问题：第一次加载应用时，屏幕将启动 UI 更新，但无法了解应用重新进入前台的时间。 如果 backgrounded 应用程序恢复到前台，UI 更新将无法继续。

若要解决此问题，请在另一个通知内嵌套调用以启动 UI 更新，这会在应用程序进入活动状态时触发：

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

现在，UI 将在首次启动应用程序时开始更新，并在应用程序重新进入前台时继续更新。

在本演练中，我们构建了一种操作良好的、可识别背景的 iOS 应用程序，该应用程序将位置数据同时打印到屏幕和 "应用程序输出" 窗口。

## <a name="related-links"></a>相关链接

- [Location (第4部分)  (示例) ](/samples/xamarin/ios-samples/location)
- [核心位置框架参考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)