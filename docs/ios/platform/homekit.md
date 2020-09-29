---
title: Xamarin 中的 HomeKit
description: HomeKit 是 Apple 的框架，用于控制家庭自动化设备。 本文介绍了 HomeKit，并介绍了如何在 HomeKit 配件模拟器中配置测试附件，并编写了一个简单的 Xamarin iOS 应用来与这些附件交互。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7d5c5a6543da701b70532134d8ae901564e5c0b8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430170"
---
# <a name="homekit-in-xamarinios"></a>Xamarin 中的 HomeKit

_HomeKit 是 Apple 的框架，用于控制家庭自动化设备。本文介绍了 HomeKit，并介绍了如何在 HomeKit 配件模拟器中配置测试附件，并编写了一个简单的 Xamarin iOS 应用来与这些附件交互。_

[![启用 HomeKit 的应用示例](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

Apple 在 iOS 8 中引入了 HomeKit，以将多个家庭自动化设备从不同供应商无缝集成到一个一致的单位。 通过提升常见协议来发现、配置和控制家庭自动化设备，HomeKit 允许非相关供应商提供的设备协同工作，所有这些设备都无需协调工作。

使用 HomeKit，可以创建一个 Xamarin iOS 应用，用于控制任何启用 HomeKit 的设备，而无需使用供应商提供的 Api 或应用。 使用 HomeKit，你可以执行以下操作：

- 发现启用了新 HomeKit 的主自动化设备，并将其添加到将跨所有用户的 iOS 设备保持的数据库。
- 设置、配置、显示和控制 HomeKit _Home 配置数据库_中的任何设备。
- 与任何预配置的 HomeKit 设备通信，并将其命令用于执行单独的操作或协同工作，如打开厨房中的所有灯光。

除了为启用了 HomeKit 的应用程序提供主配置数据库中的设备之外，HomeKit 还提供对 Siri 语音命令的访问。 如果提供了适当配置的 HomeKit 设置，则用户可以发出语音命令，如 "Siri"，打开客厅中的灯。

<a name="Home-Configuration-Database"></a>

## <a name="the-home-configuration-database"></a>主配置数据库

HomeKit 将给定位置中的所有自动化设备组织到一个主集合。 此集合提供了一种方法，使用户能够将其 home 自动化设备分组为逻辑上排列的单位，并提供有意义且可读的标签。

主集合存储在将自动进行备份并在所有用户的 iOS 设备上同步的 Home 配置数据库。 HomeKit 提供了以下用于处理 Home 配置数据库的类：

- `HMHome` -这是顶级容器，它在单个物理位置保存所有家庭自动化设备的所有信息和配置 (例如。 单个家庭) 居民。 用户可能有多个居住，如其主家庭和假期。 它们可能在同一属性上有不同的 "房子"，如主房子和车库上的来宾房子。 无论采用哪种方式， `HMHome` 都 _必须_ 先设置和存储一个对象，然后才能输入任何其他 HomeKit 信息。
- `HMRoom` -尽管是可选的，但 `HMRoom` 允许用户在家庭 (内定义特定的会议室 `HMHome`) 例如：厨房、卫生间、旧货或客厅。 用户可以将公司内特定位置中的所有家庭自动化设备分组到中 `HMRoom` ，并将其作为一个单元进行操作。 例如，要求 Siri 关闭车库灯。
- `HMAccessory` -这表示已在用户的居民 (（例如智能恒温器) ）中安装的单个、启用物理 HomeKit 的自动化设备。 每个 `HMAccessory` 都分配给 `HMRoom` 。 如果用户尚未配置任何聊天室，HomeKit 会将附件分配给特殊的默认房间。
- `HMService` -表示给定的服务 `HMAccessory` ，如光线的开/关状态或颜色 (是否支持颜色更改) 。 每个都 `HMAccessory` 可以有多个服务，例如，车库门打开工具也包含光。 此外，给定的 `HMAccessory` 还可能具有用户控件之外的服务，如固件更新。
- `HMZone` -允许用户将对象集合分组为 `HMRoom` 逻辑区域，如楼上、Downstairs 或一起。 尽管是可选的，但它允许交互操作，例如请求 Siri 关闭所有光源 downstairs。

<a name="Provisioning-a-HomeKit-App"></a>

## <a name="provisioning-a-homekit-app"></a>预配 HomeKit 应用

由于 HomeKit 施加了安全要求，因此必须在 Apple 开发人员门户和 Xamarin iOS 项目文件中正确配置使用 HomeKit 框架的 Xamarin iOS 应用。

执行以下操作：

1. 登录到 [Apple 开发人员门户](https://developer.apple.com)。
2. 单击 " **证书、标识符 & 配置文件**。
3. 如果尚未执行此操作，请单击 " **标识符** "，为应用创建一个 ID (例如 `com.company.appname`) ，否则编辑现有 ID。
4. 确保已针对给定 ID 检查 **HomeKit** 服务： 

    [![为给定 ID 启用 HomeKit 服务](homekit-images/provision01.png)](homekit-images/provision01.png#lightbox)
5. 保存所做更改。
6. 单击 "**预配配置文件**  >  **开发**"，为你的应用创建新的开发预配配置文件： 

    [![为应用程序创建新的开发预配配置文件](homekit-images/provision02.png)](homekit-images/provision02.png#lightbox)
7. 下载并安装新的预配配置文件，或使用 Xcode 下载并安装该配置文件。
8. 编辑 Xamarin iOS 项目选项，确保使用的是刚才创建的预配配置文件： 

    [![选择刚创建的预配配置文件](homekit-images/provision03.png)](homekit-images/provision03.png#lightbox)
9. 接下来，请编辑 **info.plist** 文件，并确保使用的是用于创建预配配置文件的应用 ID： 

    [![设置应用 ID ](homekit-images/provision04.png)](homekit-images/provision04.png#lightbox)
10. 最后，编辑 **info.plist** 文件，并确保已选择 **HomeKit** 权利： 

    [![启用 HomeKit 权限](homekit-images/provision05.png)](homekit-images/provision05.png#lightbox)
11. 保存对所有文件所做的更改。

设置好这些设置后，应用程序便可访问 HomeKit Framework Api。 有关预配的详细信息，请参阅 [设备预配](~/ios/get-started/installation/device-provisioning/index.md) 和 [预配应用](~/ios/get-started/installation/device-provisioning/index.md) 指南。

> [!IMPORTANT]
> 测试启用了 HomeKit 的应用程序需要已正确设置进行开发的实际 iOS 设备。 HomeKit 无法从 iOS 模拟器进行测试。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 配件模拟器

为了提供一种方法来测试所有可能的主自动化设备和服务，而无需具有物理设备，Apple 创建了 _HomeKit 的附件模拟器_。 使用此模拟器，你可以设置和配置虚拟 HomeKit 设备。

### <a name="installing-the-simulator"></a>安装模拟器

Apple 提供 HomeKit 附属模拟器作为来自 Xcode 的单独下载，因此你需要先安装它，然后才能继续。

执行以下操作：

1. 在 web 浏览器中，访问 [Apple 开发人员的下载](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下载 **适用于 Xcode xxx (的其他工具** ，其中 xxx 是已安装) 的版本： 

    [![下载适用于 Xcode 的其他工具](homekit-images/simulator01.png)](homekit-images/simulator01.png#lightbox)
3. 打开磁盘映像，并在 **应用程序** 目录中安装工具。

安装 HomeKit 附件模拟器后，可以创建虚拟附件进行测试。

### <a name="creating-virtual-accessories"></a>创建虚拟附件

若要启动 HomeKit 附属模拟器并创建几个虚拟附件，请执行以下操作：

1. 从 "应用程序" 文件夹中，启动 HomeKit 附件模拟器： 

    [![HomeKit 配件模拟器](homekit-images/simulator02.png)](homekit-images/simulator02.png#lightbox)
2. 单击该 **+** 按钮，然后选择 " **新建附件 ...**"： 

    [![添加新附件](homekit-images/simulator03.png)](homekit-images/simulator03.png#lightbox)
3. 填写有关新附件的信息，然后单击 " **完成** " 按钮： 

    [![填写有关新附件的信息](homekit-images/simulator04.png)](homekit-images/simulator04.png#lightbox)
4. 单击 " **添加服务"。** 按钮，然后从下拉列表中选择一个服务类型： 

    [![从下拉列表中选择一种服务类型](homekit-images/simulator05.png)](homekit-images/simulator05.png#lightbox)
5. 提供服务的 **名称** ，然后单击 " **完成** " 按钮： 

    [![输入服务的名称](homekit-images/simulator06.png)](homekit-images/simulator06.png#lightbox)
6. 可以通过单击 " **添加特征** " 按钮并配置所需的设置，为服务提供可选特征： 

    [![配置所需设置](homekit-images/simulator07.png)](homekit-images/simulator07.png#lightbox)
7. 重复上述步骤，以创建 HomeKit 支持的每种类型的虚拟 home 自动化设备之一。

创建和配置一些示例虚拟 HomeKit 附件后，你现在可以通过 Xamarin iOS 应用使用和控制这些设备。

## <a name="configuring-the-infoplist-file"></a>配置 info.plist 文件

新的 iOS 10 (和更高) ，开发人员需要将密钥添加 `NSHomeKitUsageDescription` 到应用的文件中， `Info.plist` 并提供一个字符串来声明应用要访问用户的 HomeKit 数据库的原因。 首次运行应用时，将向用户显示此字符串：

[![HomeKit 权限对话框](homekit-images/info01.png)](homekit-images/info01.png#lightbox)

若要设置此密钥，请执行以下操作：

1. 双击 `Info.plist` " **解决方案资源管理器** 中的文件以将其打开以进行编辑。
2. 在屏幕底部，切换到 " **源** " 视图。
3. 向列表中添加新 **项** 。
4. 从下拉列表中，选择 " **隐私-HomeKit 使用说明**"： 

    [![选择隐私-HomeKit 使用说明](homekit-images/info02.png)](homekit-images/info02.png#lightbox)
5. 输入应用要访问用户的 HomeKit 数据库的原因说明： 

    [![输入描述](homekit-images/info03.png)](homekit-images/info03.png#lightbox)
6. 保存对文件所做的更改。

> [!IMPORTANT]
> 如果无法设置该 `NSHomeKitUsageDescription` 文件中的密钥， `Info.plist` 将导致应用程序在运行时以 _静默方式失败_ (在 iOS 10 (或更高) 版本中运行时不会出错) 。

## <a name="connecting-to-homekit"></a>连接到 HomeKit

若要与 HomeKit 进行通信，你的 Xamarin iOS 应用需要首先实例化类的实例 `HMHomeManager` 。 家乡经理是 HomeKit 的核心入口点，负责提供可用家庭的列表、更新和维护该列表并返回用户的 _主主页_。

`HMHome`对象包含有关提供总部的所有信息，包括任何房间、可能包含的组或区域，以及任何已安装的家庭自动化附件。 在 HomeKit 中执行任何操作之前，必须创建至少一个操作， `HMHome` 并将其分配为主 Home。

您的应用程序负责检查是否存在主宿主，如果不存在，则创建和分配一个。

### <a name="adding-a-home-manager"></a>添加家庭经理

若要向 Xamarin iOS 应用添加 HomeKit 感知，请编辑 **AppDelegate.cs** 文件进行编辑，使其类似于以下内容：

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

首次运行应用程序时，系统会询问用户是否允许它访问其 HomeKit 信息：

[![如果用户想要允许其访问 HomeKit 信息，则会询问用户](homekit-images/home01.png)](homekit-images/home01.png#lightbox)

如果用户回答 **"确定"**，则该应用程序将能够使用其 HomeKit 附件，否则将不会，并且对 HomeKit 的任何调用都将失败并出现错误。

在开始使用主管理器后，应用程序需要查看是否已配置了主站，如果不是，则为用户提供创建和分配帐户的方法。

### <a name="accessing-the-primary-home"></a>访问主要主页

如上所述，在 HomeKit 可用之前，必须先创建和配置主 Home，这是应用负责为用户创建和分配主家庭的方式（如果尚不存在）。

当应用程序首次从后台启动或返回时，它需要监视 `DidUpdateHomes` 类的事件 `HMHomeManager` 以检查是否存在主宿主。 如果不存在，则应为用户提供一个接口来创建一个。

可以将以下代码添加到视图控制器以检查主主页：

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

当家乡经理建立与 HomeKit 的连接时， `DidUpdateHomes` 将触发该事件，任何现有的家庭都将加载到该管理器的家乡的集合，并将会加载主主页（如果可用）。

### <a name="adding-a-primary-home"></a>添加主 Home

如果 `PrimaryHome` 的属性在 `HMHomeManager` `null` `DidUpdateHomes` 事件之后，则需要为用户提供一种方法来创建和分配主 Home，然后再继续。

通常，应用程序将为用户提供一个窗体来命名新的主页，然后将其传递到 Home Manager，设置为主版。 对于 **HomeKitIntro** 示例应用程序，在 IOS 设计器中创建了一个模式视图，并 `AddHomeSegue` 从应用程序的主接口调用了该视图。

它为用户提供了一个文本字段，用于为新的主页输入名称，使用一个按钮来添加主页。 当用户点击 " **添加主页** " 按钮时，以下代码将调用 home Manager 来添加 home：

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

`AddHome`方法将尝试创建新的 home，并将其返回给给定的回调例程。 如果该 `error` 属性不为 `null` ，则会发生错误并向用户显示错误消息。 最常见的错误是由不唯一的主名称或不能与 HomeKit 通信的主管理器导致的。

如果已成功创建 home，则需要调用 `UpdatePrimaryHome` 方法将新的主页设置为主 home。 同样，如果 `error` 属性不为，则会 `null` 发生错误并向用户显示错误消息。

还应监视主管理器 `DidAddHome` 和 `DidRemoveHome` 事件，并根据需要更新应用程序的用户界面。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`上面的示例代码中使用的方法是 HomeKitIntro 应用程序中的一个帮助器类，它可以更轻松地处理 IOS 警报。

## <a name="finding-new-accessories"></a>查找新附件

在主管理器中定义或加载主要主机后，你的 Xamarin iOS 应用可以调用 `HMAccessoryBrowser` 来查找任何新的家庭自动化附件，并将其添加到家庭。

调用 `StartSearchingForNewAccessories` 方法以在完成时开始查找新附件和 `StopSearchingForNewAccessories` 方法。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 不应长时间运行，因为它会对 iOS 设备的电池寿命和性能产生负面影响。 Apple 建议在 `StopSearchingForNewAccessories` 一分钟后调用，或仅在向用户显示查找附件 UI 时搜索。

`DidFindNewAccessory`当发现新附件，并将其添加到 `DiscoveredAccessories` 附件浏览器中的列表时，将调用该事件。

此 `DiscoveredAccessories` 列表将包含一个对象集合 `HMAccessory` ，这些对象定义允许启用 HomeKit 的 home 自动化设备及其可用服务，如灯光或车库门控制。

找到新的附件后，应该向用户显示它，以便他们选择它并将其添加到主页。 示例：

[![查找新附件](homekit-images/accessory01.png)](homekit-images/accessory01.png#lightbox)

调用 `AddAccessory` 方法以将所选附件添加到 home 的集合中。 例如：

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

如果该 `err` 属性不为 `null` ，则会发生错误并向用户显示错误消息。 否则，系统会要求用户输入要添加的设备的安装代码：

[![输入要添加的设备的安装代码](homekit-images/accessory02.png)](homekit-images/accessory02.png#lightbox)

在 HomeKit 配件模拟器中，可以在 " **安装代码** " 字段下找到此编号：

[![HomeKit 配件模拟器中的 "设置代码" 字段](homekit-images/accessory03.png)](homekit-images/accessory03.png#lightbox)

对于实际的 HomeKit 附件，设置代码将打印在设备本身的标签上、产品盒中或附件的用户手册中。

你应监视附件浏览器的 `DidRemoveNewAccessory` 事件，并更新用户界面，以便在用户将附件添加到其主集合后，从可用列表中删除该附件。

## <a name="working-with-accessories"></a>使用附件

在主要主机已建立并添加附件后，你可以提供一系列附件 (和可选房间) 供用户使用。

`HMRoom`对象包含有关给定房间的所有信息以及属于该房间的任何附件。 可以选择将会议室组织到一个或多个区域中。 `HMZone`包含有关给定区域的所有信息以及属于该区域的所有聊天室。

在此示例中，我们将保持简单，并直接使用家庭的附件，而不是将它们组织到房间或区域。

`HMHome`对象包含分配的附件列表，可在其属性中向用户显示该列表 `Accessories` 。 例如：

[![示例附件](homekit-images/accessory04.png)](homekit-images/accessory04.png#lightbox)

在此窗体中，用户可以选择给定附件，并使用其提供的服务。

## <a name="working-with-services"></a>使用服务

用户与给定的 HomeKit 启用的 home 自动化设备交互时，通常通过它提供的服务。 `Services`类的属性 `HMAccessory` 包含 `HMService` 定义设备所提供服务的对象的集合。

服务包括光源、恒温器、、车库门 openers、交换机或锁等。 某些设备 (如车库门打开工具) 将提供多个服务，例如，一种光源，可以打开或关闭门。

除了给定附件提供的特定服务以外，每个附件都包含一个 `Information Service` 用于定义属性的属性，例如名称、制造商、型号和序列号。

### <a name="accessory-service-types"></a>附件服务类型

以下服务类型可通过 `HMServiceType` 枚举获得：

- **AccessoryInformation** -提供有关给定的 home 自动化设备 (附件) 的信息。
- **AirQualitySensor** -定义空气质量传感器。
- **电池** -定义附件电池的状态。
- **CarbonDioxideSensor** -定义碳二氧化碳传感器。
- **CarbonMonoxideSensor** -定义碳 Monoxide 传感器。
- **ContactSensor** -定义一个联系人传感器 (例如) 打开或关闭的窗口。
- **门** -定义门状态传感器 (如 "打开" 或 "关闭") 。
- **风扇** -定义远程受控风扇。
- **GarageDoorOpener** -定义车库门打开工具。
- **HumiditySensor** -定义湿度传感器。
- **LeakSensor** -定义) 的热水热水或 washing 机的泄漏传感器 (例如。
- **灯泡** -定义一个单独的光源或一种光源，它属于另一个附件 (如车库门打开工具) 。
- **LightSensor** -定义光传感器。
- **LockManagement** -定义管理自动护盖锁定的服务。
- **LockMechanism** -定义远程受控锁 (如) 门锁。
- **MotionSensor** -定义运动传感器。
- **OccupancySensor** -定义占用空间的传感器。
- **插座** -定义远程受控壁插座。
- **SecuritySystem** -定义 home 安全系统。
- **StatefulProgrammableSwitch** -定义一种可编程的开关，该开关将在发出时保持状态 (如) 翻转开关。
- **StatelessProgrammableSwitch** -定义一个可编程的开关，该开关将在触发后返回到其初始状态 (如) "推送" 按钮。
- **SmokeSensor** -定义冒烟传感器。
- **开关** -定义打开/关闭交换机，如标准墙壁开关。
- **温度传感器** -定义温度传感器。
- **恒温器** -定义用于控制 HVAC 系统的智能恒温器。
- **窗口** -定义可以开始远程打开或关闭的自动窗口。
- **WindowCovering** -定义一个远程控制窗口，其中包括可以打开或关闭的百叶窗等。

### <a name="displaying-service-information"></a>显示服务信息

加载后， `HMAccessory` 您可以查询 `HNService` 它提供的各个对象并向用户显示该信息：

[![显示服务信息](homekit-images/accessory05.png)](homekit-images/accessory05.png#lightbox)

尝试使用之前，应始终检查 `Reachable` 的属性 `HMAccessory` 。 如果用户不在设备范围内或已拔出，则无法访问附件。

选择服务后，用户可以查看或修改该服务的一个或多个特征，以监视或控制给定的 home 自动化设备。

<a name="Working-with-Characteristics"></a>

## <a name="working-with-characteristics"></a>使用特性

每个 `HMService` 对象都可以包含一个 `HMCharacteristic` 对象集合，这些对象可以提供有关服务状态的信息 (如打开或关闭门) 或者允许用户调整状态 (如设置光源) 的颜色）。

`HMCharacteristic` 不仅提供有关特征及其状态的信息，还提供用于通过 _特征元数据_ 处理状态的方法 (`HMCharacteristisMetadata`) 。 此元数据可提供 (的属性，例如最小和最大值范围) 在向用户显示信息或允许用户修改状态时非常有用。

`HMCharacteristicType`枚举提供一组可按如下所示定义或修改的特征元数据值：

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- AirQuality
- AudioFeedback
- BatteryLevel
- 亮度
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- ChargingState
- ContactState
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- 色调
- 识别
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- 日志
- 制造商
- “模型”
- MotionDetected
- 名称
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- 饱和度
- SerialNumber
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- 版本

### <a name="working-with-a-characteristics-value"></a>使用特性的值

若要确保应用具有给定特征的最新状态，请调用类的 `ReadValue` 方法 `HMCharacteristic` 。 如果该 `err` 属性不为 `null` ，则表示出现错误，并且可能会向用户显示该属性。

特性的 `Value` 属性包含给定特性的当前状态作为 `NSObject` ，因此不能直接在 c # 中使用。

若要读取该值，请将以下 helper 类添加到 **HomeKitIntro** 示例应用程序：

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter`每当应用程序需要读取特性的当前状态时使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上述代码行将值转换为 `float` ，然后在 Xamarin c # 代码中使用。

若要修改 `HMCharacteristic` ，请调用其 `WriteValue` 方法，并将新值包装在 `NSObject.FromObject` 调用中。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果该 `err` 属性不为 `null` ，则会发生错误并向用户显示错误。

### <a name="testing-characteristic-value-changes"></a>测试特征值更改

使用 `HMCharacteristics` 和模拟的附件时， `Value` 可以在 HomeKit 附件模拟器内监视对属性进行的修改。

在实际 iOS 设备硬件上运行 **HomeKitIntro** 应用程序时，应立即在 HomeKit 附件模拟器中看到对特征值所做的更改。 例如，在 iOS 应用中更改光的状态：

[![更改 iOS 应用中的灯光状态](homekit-images/test01.png)](homekit-images/test01.png#lightbox)

应更改 HomeKit 附件模拟器中光源的状态。 如果值不更改，请在写入新的特征值时检查错误消息的状态，并确保附件仍可访问。

## <a name="advanced-homekit-features"></a>高级 HomeKit 功能

本文介绍了在 Xamarin iOS 应用程序中使用 HomeKit 附件所需的基本功能。 但是，在本简介中没有涉及到 HomeKit 的多个高级功能：

- **会议室** -HomeKit 已启用的附件可选择性地由最终用户组织到聊天室。 这允许 HomeKit 以一种易于用户理解和使用的方式提供附件。 有关创建和维护会议室的详细信息，请参阅 Apple 的 [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom) 文档。
- **区域** -可以选择由最终用户组织到区域中。 区域指的是用户可能将其视为单个单元的房间的集合。 例如：楼上、Downstairs 或一起。 同样，这允许 HomeKit 以一种对最终用户有意义的方式提供和使用附件。 有关创建和维护区域的详细信息，请参阅 Apple 的 [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone) 文档。
- **操作和操作集** -操作修改附件服务特征并可分组到集。 操作集作为脚本来控制一组附件并协调其操作。 例如，"观看电视" 脚本可能会关闭遮蔽，使光源变暗，并打开电视及其声音系统。 有关创建和维护操作和操作集的详细信息，请参阅 Apple 的 [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction) 和 [HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet) 文档。
- **触发器** -触发器可以在满足一组给定条件时激活一个或多个操作集。 例如，打开 "portch" 灯，并在其深超出时锁定所有外部门。 有关创建和维护触发器的详细信息，请参阅 Apple 的 [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger) 文档。

由于这些功能使用以上所述的相同技术，因此应使用 Apple 的 [HomeKitDeveloper Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)、 [HomeKit 用户界面准则](https://developer.apple.com/homekit/ui-guidelines/) 和 [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)来轻松实现这些功能。

## <a name="homekit-app-review-guidelines"></a>HomeKit 应用评审准则

在 iTunes 应用商店中将启用了 HomeKit 的 Xamarin iOS 应用提交到 iTunes Connect 后，请确保遵循 Apple 的适用于启用 HomeKit 的应用的指南：

- 如果使用 HomeKit 框架，则该应用的主要用途 _必须_ 是 "家庭自动化"。
- 应用的营销文本必须通知用户正在使用 HomeKit，并且必须提供隐私策略。
- 严格禁止收集用户信息或使用 HomeKit 进行广告。

有关完整的审查准则，请参阅 Apple 的 [App Store 审核指南](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>IOS 9 中的新增功能

Apple 已对适用于 iOS 9 的 HomeKit 进行了以下更改和添加：

- **维护现有对象** -如果修改了现有附件，则 () 的主管理器 `HMHomeManager` 将通知你已修改的特定项目。
- **永久性标识符** -所有相关的 HomeKit 类现在都包含一个 `UniqueIdentifier` 属性，该属性用于在已启用 HomeKit 的应用中唯一标识给定项， (或同一应用) 的实例。
- **用户管理** -添加了一个内置视图控制器，以便为用户提供对主用户家庭中 HomeKit 设备的访问权限的用户管理。
- **用户功能** -HomeKit 用户现在拥有一组权限，可控制他们能够在 HomeKit 和 HomeKit 支持的附件中使用的功能。 应用只应显示当前用户的相关功能。 例如，只有管理员才能维护其他用户。
- **预定义的场景** 预定义的场景已针对平均 HomeKit 用户发生的四个常见事件创建：获取、离开、返回、转到平台。 不能从家里删除这些预定义的场景。
- 在 iOS 9 中，**场景和 Siri** Siri 更深入地支持场景，可以识别在 HomeKit 中定义的任何场景的名称。 用户只需将其名称与其名称 Siri 即可执行场景。
- **附件类别** -一组预定义的类别已添加到所有附件，有助于识别要添加到家庭或从应用中进行处理的附件类型。 这些新类别在附件设置期间可用。
- **Apple Watch 支持** -HomeKit 现可用于 watchOS，Apple Watch 将能够控制启用 HomeKit 的设备，而不会在手表附近显示 iPhone。 WatchOS 的 HomeKit 支持以下功能：查看家庭内容、控制附件和执行场景。
- **新事件触发器类型** -除了 ios 8 中支持的计时器类型触发器外，ios 9 现在还支持基于附件状态的事件触发器 (如传感器数据) 或地理位置。 事件触发器使用 `NSPredicates` 为其执行设置条件。
- **远程访问** -使用远程访问时，用户现在可以在远离远程位置的房子时控制其 HomeKit 启用的家庭自动化附件。 在 iOS 8 中，仅当用户在家中有第三代 Apple TV 时才支持此项。 在 iOS 9 中，此限制已提升，并支持通过 iCloud 和 HomeKit 附件协议 (HAP) 进行远程访问。
- **新的蓝牙低能耗 (BLE) 功能** -HomeKit 现在支持更多可通过蓝牙低功耗 (BLE) 协议进行通信的附件类型。 使用 HAP 安全隧道，HomeKit 附件可以通过 Wi-fi (公开其他蓝牙附件（如果它不在) 的蓝牙范围内）。 在 iOS 9 中，BLE 附件完全支持通知和元数据。
- **新的附件类别** -Apple 在 iOS 9 中添加了以下新的附件类别：窗口 Coverings、电动门和 Windows、警报系统、传感器和可编程交换机。

有关 iOS 9 中 HomeKit 的新功能的详细信息，请参阅 Apple 的 [HomeKit 索引](https://developer.apple.com/homekit/) 和 [HomeKit 视频中的新增](https://developer.apple.com/videos/wwdc/2015/?id=210) 功能。

## <a name="summary"></a>总结

本文介绍了 Apple 的 HomeKit home 自动化框架。 其中介绍了如何使用 HomeKit 附件模拟器设置和配置测试设备，以及如何使用 HomeKit 创建一个简单的 Xamarin iOS 应用来发现、使用和控制主自动化设备。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)