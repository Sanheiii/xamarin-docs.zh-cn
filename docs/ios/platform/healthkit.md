---
title: Xamarin 中的 HealthKit
description: 本文档介绍 iOS 8 中引入的 HealthKit，它为与运行状况相关的信息提供集中、协调和安全的数据存储。 本文介绍如何预配 HealthKit 应用，以及如何编写使用 HealthKit 框架的代码。
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 3d5bdfd78658803dcbb159101050aea48008b69e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435225"
---
# <a name="healthkit-in-xamarinios"></a>Xamarin 中的 HealthKit

运行状况工具包提供了一个安全的数据存储，用于存储用户的与运行状况相关的信息。 运行状况工具包应用程序可以拥有用户的显式权限，读取和写入此数据存储并在添加相关数据时接收通知。 应用可以显示数据，或者用户可以使用 Apple 提供的运行状况应用来查看其所有数据的仪表板。

由于与运行状况相关的数据非常敏感且至关重要，因此健康套件是强类型化的，它具有度量单位，并与记录的信息类型明确关联 (例如，血糖级别或心率) 。 此外，健康套件应用必须使用显式权利，必须请求对特定类型的信息的访问权限，并且用户必须显式授予应用对这些类型的数据的访问权限。

本文将介绍以下内容：

- 健康套件的安全要求，包括应用程序预配和请求用户访问运行状况工具包数据库的权限;
- 健康套件的类型系统，它最大程度地减少了错误应用或错误解释数据的可能性;
- 写入共享的、系统范围的运行状况工具包数据存储。

本文不会介绍更高级的主题，如查询数据库、转换度量单位或接收新数据的通知。

在本文中，我们将创建一个示例应用程序来记录用户的心率：

[![用于记录用户心率的示例应用程序](healthkit-images/image01.png)](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>要求

若要完成本文中所述的步骤，需要满足以下要求：

- **Xcode 7 和 iOS 8 (或更高) ** –需要在开发人员的计算机上安装和配置 Apple 的最新 Xcode 和 ios api。
- **Visual Studio for Mac 或 Visual Studio** –应在开发人员的计算机上安装和配置 Visual Studio for Mac 的最新版本。
- **ios 8 (或更高) 设备** –运行最新版 ios 8 或更高版本的 ios 设备用于测试。

> [!IMPORTANT]
> IOS 8 中引入了运行状况工具包。 目前，运行状况工具包在 iOS 模拟器上不可用，调试需要连接到物理 iOS 设备。

## <a name="creating-and-provisioning-a-health-kit-app"></a>创建和设置运行状况工具包应用
在 Xamarin iOS 8 应用程序可以使用 HealthKit API 之前，必须正确配置和预配该 API。 本部分介绍正确设置 Xamarin 应用程序所需的步骤。

运行状况工具包应用需要：

- 显式 **应用 ID**。
- 与该显式**应用 ID**和具有**健康套件**权限相关联的**预配配置文件**。
- `Entitlements.plist`具有 `com.apple.developer.healthkit` 设置为的类型的属性的 `Boolean` `Yes` 。
- 一个 `Info.plist` `UIRequiredDeviceCapabilities` ，其键包含值为的 `String` 项 `healthkit` 。
- `Info.plist`还必须具有相应的隐私解释条目： `String` 有关密钥的说明（ `NSHealthUpdateUsageDescription` 如果应用要写入数据）和对 `String` 密钥的说明（ `NSHealthShareUsageDescription` 如果应用要读取运行状况工具包数据）。

若要了解有关预配 iOS 应用的详细信息，Xamarin**入门**系列中的[设备预配](~/ios/get-started/installation/device-provisioning/index.md)文章介绍了开发人员证书、应用 Id、预配配置文件和应用权利之间的关系。

<a name="explicit-appid"></a>

### <a name="explicit-app-id-and-provisioning-profile"></a>显式应用 ID 和预配配置文件

创建显式 **应用 ID** 和适当的 **预配配置文件** 在 Apple 的 [iOS 开发人员中心](https://developer.apple.com/devcenter/ios/index.action)内完成。 

当前 **应用 id** 列在开发人员中心的 " [证书"、"标识符 & 配置文件](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) " 部分中。 通常，此列表将显示**ID**值 `*` ，指示**应用 ID**  -  **名称**可以与任意数量的后缀一起使用。 此类 *通配符应用 id* 不能与运行状况工具包一起使用。

若要创建显式 **应用 ID**，请单击 **+** 右上角的按钮以转到 " **注册 iOS 应用 id** " 页：

[![在 Apple 开发人员门户中注册应用](healthkit-images/image02.png)](healthkit-images/image02.png#lightbox)

如上图所示，在创建应用程序说明后，请使用 " **显式应用 id** " 部分为应用程序创建 ID。 在 "**应用服务**" 部分中，检查 "**启用服务**" 部分中的**运行状况工具包**。

完成后，请按 " **继续** " 按钮，在帐户中注册 **应用 ID** 。 你将返回到 " **证书、标识符和配置文件** " 页。 单击 " **预配配置文件** "，转到当前预配配置文件的列表，然后单击 **+** 右上角的按钮以转到 " **添加 IOS 预配配置文件** " 页。 选择 " **IOS 应用开发** " 选项，然后单击 " **继续** " 以转到 " **选择应用 ID** " 页。 在此处选择之前指定的显式 **应用 ID** ：

[![选择显式应用 ID](healthkit-images/image03.png)](healthkit-images/image03.png#lightbox)

单击 "**继续**"，然后浏览其余屏幕，你将在其中指定你的**开发人员证书 () **、**设备 () **以及此**预配配置文件**的**名称**：

[![正在生成预配配置文件](healthkit-images/image04.png)](healthkit-images/image04.png#lightbox)

单击 " **生成** " 并等待创建配置文件。 下载文件，然后双击该文件以安装在 Xcode 中。 你可以在 **Xcode > 首选项 > 帐户 > 查看详细信息 ...** 你应该会看到刚刚安装的预配配置文件，并且它的 " **权利** " 行中应该有用于运行状况工具包和任何其他特殊服务的图标：

[![查看 Xcode 中的配置文件](healthkit-images/image05.png)](healthkit-images/image05.png#lightbox)

<a name="associating-appid"></a>

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>将应用 ID 和预配配置文件与 Xamarin iOS 应用相关联

按照说明创建并安装适当的 **预配配置文件** 后，通常可以在 Visual Studio for Mac 或 Visual Studio 中创建解决方案。 运行状况工具包访问适用于任何 iOS c # 或 F # 项目。

请不要逐步完成创建 Xamarin iOS 8 项目的过程，而是打开附加到本文的示例应用 (其中包括预建的情节提要和代码) 。 若要将示例应用与运行状况工具包的 **预配配置文件**相关联，请在 **Solution Pad**中右键单击你的项目并打开其 " **选项** " 对话框。 切换到 " **IOS 应用程序** " 面板，然后输入之前创建的显式 **应用 ID** 作为应用的 **捆绑包标识符**：

[![输入显式应用 ID](healthkit-images/image06.png)](healthkit-images/image06.png#lightbox)

现在，切换到 " **IOS 捆绑签名** " 面板。 最近安装的 **预配配置文件**及其与显式 **应用 ID**的关联现在将作为 **预配配置文件**提供：

[![选择预配配置文件](healthkit-images/image07.png)](healthkit-images/image07.png#lightbox)

如果**预配配置文件**不可用，请在**ios 应用程序**面板中仔细检查**捆绑标识符**，与**ios 开发人员中心**中指定的相同，并 (**Xcode > 首选项 > 帐户 > 查看详细信息 ...**) 中安装**预配配置文件**。

如果选择了 "启用运行状况工具包的 **预配配置文件** "，请单击 **"确定"** 以关闭 "项目选项" 对话框。

### <a name="entitlementsplist-and-infoplist-values"></a>Info.plist 和 info.plist 值

该示例应用包含一个 `Entitlements.plist` 文件 (这对于启用了运行状况工具包的应用程序而言是必需的) ，而不是包含在每个项目模板中。 如果你的项目不包括权利，请右键单击项目，选择 "文件" " **> 新建文件 ..." > iOS > "info.plist** " 手动添加一个。

最终， `Entitlements.plist` 必须具有以下键和值对：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

同样， `Info.plist` 应用的值必须具有 `healthkit` 与键关联的值 `UIRequiredDeviceCapabilities` ：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文中提供的示例应用程序包含一个预配置 `Entitlements.plist` 的，其中包括所有必需的键。

<a name="programming"></a>

## <a name="programming-health-kit"></a>编程运行状况工具包

运行状况工具包数据存储是在应用之间共享的专用用户特定数据存储。 由于运行状况信息非常敏感，用户必须采取积极措施来允许数据访问。 此访问可能是部分 (写入但不能读取、访问某些类型的数据，但不能访问其他类型的数据，等等。 ) 并可随时撤消。 健康工具包应用程序应保守编写，了解到很多用户将不愿意存储其与运行状况相关的信息。

健康套件数据仅限 Apple 指定的类型。 严格定义这些类型：某些类型（如血糖类型）仅限于 Apple 提供的枚举的特定值，而其他类型则将量与度量单位 (如克、卡路里和升) 组合在一起。 即使是共享兼容度量单位的数据，也可以通过它们进行区分 `HKObjectType` ; 例如，类型系统将捕获一次尝试将值存储 `HKQuantityTypeIdentifier.NumberOfTimesFallen` 到预期的字段（ `HKQuantityTypeIdentifier.FlightsClimbed` 即使两者都使用 `HKUnit.Count` 度量单位）。

运行状况工具包数据存储中的存储类型是的所有子类 `HKObjectType` 。 `HKCharacteristicType` 对象存储生物性爱、血糖类型和出生日期。 但更常见的是 `HKSampleType` 对象，这些对象表示在特定时间或一段时间内采样的数据。 

[![HKSampleType 对象图](healthkit-images/image08.png)](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的，并且具有四个具体子类。 目前只有一种类型的 `HKCategoryType` 数据，即休眠分析。 运行状况工具包中的大部分数据的类型为 `HKQuantityType` ，并将其数据存储在 `HKQuantitySample` 对象中，这些对象是使用熟悉的工厂设计模式创建的：

[![运行状况工具包中的大部分数据类型为 HKQuantityType，并将其数据存储在 HKQuantitySample 对象中](healthkit-images/image09.png)](healthkit-images/image09.png#lightbox)

`HKQuantityType` 类型的范围从 `HKQuantityTypeIdentifier.ActiveEnergyBurned` 到 `HKQuantityTypeIdentifier.StepCount` 。 

<a name="requesting-permission"></a>

### <a name="requesting-permission-from-the-user"></a>正在请求用户的权限

最终用户必须执行相应的步骤，以允许应用读取或写入健康套件数据。 这是通过预安装在 iOS 8 设备上的运行状况应用来完成的。 首次运行运行状况包应用时，将向用户提供系统控制的 **运行状况访问** 对话框：

[![用户显示系统控制的运行状况访问对话框](healthkit-images/image10.png)](healthkit-images/image10.png#lightbox)

之后，用户可以使用运行状况应用的 " **源** " 对话框更改权限：

[![用户可以使用 "健康应用源" 对话框更改权限](healthkit-images/image11.png)](healthkit-images/image11.png#lightbox)

由于运行状况信息非常敏感，应用程序开发人员应该编写其程序保守，并假定在应用程序运行时权限将被拒绝和更改。 最常见的用法是在方法中请求权限 `UIApplicationDelegate.OnActivated` ，然后根据需要修改用户界面。

### <a name="permissions-walkthrough"></a>权限演练

在运行状况工具包预配的项目中，打开 `AppDelegate.cs` 文件。 请注意文件顶部使用的语句 `HealthKit` 。

下面的代码与运行状况工具包权限相关：

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

这些方法中的所有代码都可以在中以内联方式完成 `OnActivated` ，但示例应用程序使用不同的方法来使其意图更清晰：执行以下 `ValidateAuthorization()` 步骤：请求访问 (和读取的特定类型; 如果应用程序所需) 和 `ReactToHealthCarePermissions()` 是在用户与运行状况中的权限对话框交互之后激活的回调。

的工作 `ValidateAuthorization()` 是生成 `HKObjectTypes` 应用程序编写的集合，并请求授权来更新该数据。 在示例应用中， `HKObjectType` 用于键的 `KHQuantityTypeIdentifierKey.HeartRate` 。 此类型将添加到集合 `typesToWrite` 中，而集 `typesToRead` 保留为空。 这些集和对回调的引用 `ReactToHealthCarePermissions()` 将传递给 `HKHealthStore.RequestAuthorizationToShare()` 。

在 `ReactToHealthCarePermissions()` 用户与 "权限" 对话框进行交互并传递两部分信息时，将调用该回调：一个 `bool` 值。 `true` 如果用户已与 "权限" 对话框交互，则将调用该回调; 如果用户已与 "权限" 对话框进行交互，则将调用该回调 `NSError` 。

> [!IMPORTANT]
> 若要清楚地了解此函数的参数： _success_ 和 _error_ 参数并不指示用户是否已授予访问运行状况工具包数据的权限！ 它们仅指示用户已获得允许访问数据的机会。

若要确认应用是否有权访问数据，请 `HKHealthStore.GetAuthorizationStatus()` 使用，并传入 `HKQuantityTypeIdentifierKey.HeartRate` 。 根据返回的状态，应用启用或禁用输入数据的功能。 没有用于处理拒绝访问的标准用户体验，有许多可能的选项。 在示例应用中，状态设置为 `HeartRateModel` 单一实例对象，而该对象又引发相关事件。

## <a name="model-view-and-controller"></a>模型、视图和控制器

若要查看 `HeartRateModel` 单一实例对象，请打开 `HeartRateModel.cs` 文件：

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

第一部分是用于创建一般事件和处理程序的样板代码。 类的初始部分 `HeartRateModel` 也是用于创建线程安全单独对象的样板。

然后， `HeartRateModel` 公开3个事件： 

- `EnabledChanged` -表示已启用或禁用心率存储 (请注意，最初禁用存储) 。 
- `ErrorMessageChanged` -对于此示例应用，我们有一个非常简单的错误处理模型：一个字符串，其中包含上一个错误。 
- `HeartRateStored` -当心率存储在运行状况工具包数据库中时引发。

请注意，每当触发这些事件时，它都是通过进行的 `NSObject.InvokeOnMainThread()` ，这允许订户更新 UI。 或者，可以将事件记录为在后台线程上引发，并确保可以将兼容性留给其处理程序。 由于许多函数（如权限请求）都是异步的，并且在非主线程上执行回调，因此线程注意事项在运行状况工具包应用程序中非常重要。

中特定于 Heath 工具包的代码位于 `HeartRateModel` 两个函数 `HeartRateInBeatsPerMinute()` 和中 `StoreHeartRate()` 。 

`HeartRateInBeatsPerMinute()` 将其参数转换为强类型的运行状况工具包 `HKQuantity` 。 数量的类型为指定的， `HKQuantityTypeIdentifierKey.HeartRate` 并将数量的单位 `HKUnit.Count` 除以 (换言之 `HKUnit.Minute` ，单位为 *每分钟*) 。 

`StoreHeartRate()`函数 `HKQuantity` 在示例应用程序中使用 (，) 创建一个 `HeartRateInBeatsPerMinute()` 。 若要验证其数据，则使用 `HKQuantity.IsCompatible()` 方法，该方法将在 `true` 对象的单位可以转换为参数中的单位时返回。 如果创建的数量 `HeartRateInBeatsPerMinute()` 将明显返回 `true` ，但 `true` 如果数量创建为（例如， *每小时节拍*），则它也会返回。 更常见的情况 `HKQuantity.IsCompatible()` 是，可用于验证用户或设备在一种度量衡系统中输入或显示 (例如英制单位) 但可能存储在另一个系统 (如公制 units) 中的电量。 

一旦验证了数量的兼容性，则 `HKQuantitySample.FromType()` 使用工厂方法创建强类型 `heartRateSample` 对象。 `HKSample` 对象具有开始和结束日期;对于瞬时读取，这些值应相同，如示例中所示。 该示例还不会在其参数中设置任何键-值数据 `HKMetadata` ，但可以使用代码（如以下代码）来指定传感器的位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

创建完成后 `heartRateSample` ，代码将使用 using 块创建与数据库的新连接。 在该块中，该 `HKHealthStore.SaveObject()` 方法会尝试异步写入数据库。 生成的 lambda 表达式调用触发相关事件， `HeartRateStored` 或 `ErrorMessageChanged` 。

现在已对模型进行了编程，可以查看控制器反映模型状态的方式。 打开 `HKWorkViewController.cs` 文件。 此构造函数只是将 `HeartRateModel` 单一实例指向事件处理方法 (再次，这可以使用 lambda 表达式来实现，但不同的方法使意向更明显) ：

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

下面是相关处理程序：

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

显然，在具有单个控制器的应用程序中，可以避免创建单独的模型对象和使用控制流事件，但使用模型对象更适用于实际的应用。

## <a name="running-the-sample-app"></a>运行示例应用

IOS 模拟器不支持运行状况工具包。 必须在运行 iOS 8 的物理设备上进行调试。

将正确预配的 iOS 8 开发设备附加到系统。 在 Visual Studio for Mac 并从菜单中选择 " **运行 > 调试**" 作为部署目标。

> [!IMPORTANT]
> 此时将显示与设置相关的错误。 若要解决错误，请查看上面的创建和设置运行状况工具包应用部分。 组件包括： 
>
> - **IOS 开发人员中心** -& 运行状况工具包的预配配置文件的显式应用 ID。 
> - **项目选项** -捆绑标识符 () & 预配配置文件的显式应用 ID。
> - **源代码** -Info.plist & 信息。 info.plist

假设预配已正确设置，你的应用程序将启动。 当它达到其 `OnActivated` 方法时，它将请求健康工具包授权。 首次遇到此错误时，用户将看到以下对话框：

[![此对话框将显示用户](healthkit-images/image12.png)](healthkit-images/image12.png#lightbox)

启用应用程序以更新心率数据，应用将重新出现。 `ReactToHealthCarePermissions`回调将异步激活。 这将导致 `HeartRateModel’s` `Enabled` 属性更改，这将引发 `EnabledChanged` 事件，这将导致 `HKPermissionsViewController.OnEnabledChanged()` 事件处理程序运行，从而启用 `StoreData` 按钮。 下图显示了顺序：

[![此图显示了事件的顺序](healthkit-images/image13.png)](healthkit-images/image13.png#lightbox)

按 " **录制** " 按钮。 这将导致 `StoreData_TouchUpInside()` 处理程序运行，这将尝试分析 `heartRate` 文本字段的值， `HKQuantity` 通过前面讨论的函数转换为， `HeartRateModel.HeartRateInBeatsPerMinute()` 并将此数量传递到 `HeartRateModel.StoreHeartRate()` 。 如前所述，这会尝试存储数据，并将引发 `HeartRateStored` 或 `ErrorMessageChanged` 事件。

双击设备上的 " **主页** " 按钮并打开 "运行状况应用"。 单击 " **源** " 选项卡，你将看到列出的示例应用。 选择它，并且不允许更新心率数据的权限。 双击 " **主页** " 按钮，然后切换回您的应用程序。 同样， `ReactToHealthCarePermissions()` 将调用，但这一次，由于访问被拒绝， **datastorage.storedata** 按钮将被禁用 (请注意，这种情况是异步发生的，用户界面中的更改可能对最终用户) 可见。

## <a name="advanced-topics"></a>高级主题

从健康套件数据库中读取数据非常类似于写入数据：一个指定要尝试访问的数据类型，请求授权，如果授权已被授予，则数据可用，并自动转换为兼容的度量单位。

有一些更复杂的查询函数，它们允许基于谓词的查询和查询在更新相关数据时执行更新。 

运行状况工具包应用程序的开发人员应查看 Apple 的 [应用评审指导原则](https://developer.apple.com/app-store/review/guidelines/#healthkit)的运行状况包部分。

了解安全性和类型系统模型后，在共享运行状况包数据库中存储和读取数据非常简单。 运行状况工具包中的许多函数都以异步方式运行，应用程序开发人员必须相应地编写其程序。

在撰写本文时，当前没有与 Android 或 Windows Phone 中的运行状况工具包等效的。

## <a name="summary"></a>总结

在本文中，我们了解了运行状况工具包如何允许应用程序存储、检索和共享运行状况相关信息，同时还提供了允许用户访问和控制此数据的标准运行状况应用。 

此外，我们还了解了隐私、安全性和数据完整性如何覆盖与运行状况相关的信息以及使用运行状况工具包的应用程序的复杂性， (预配) 、编码 (运行状况工具包的类型系统) 和用户体验 (通过系统对话框和运行状况应用) 用户控制权限。 

最后，我们使用包含的示例应用程序来查看运行状况工具包的简单实现，该应用程序将检测信号数据写入到运行状况工具包存储，并具有异步感知的设计。

## <a name="related-links"></a>相关链接

- [HKWork (示例) ](/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)