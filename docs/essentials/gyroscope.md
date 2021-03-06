---
title: Xamarin.Essentials：陀螺仪
description: Xamarin.Essentials 中的 Gyroscope 类使你能够监控设备的陀螺仪传感器，此传感器测量围绕设备三个主轴的旋转。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a085ac5bd18e660339443ea33fe552dd86259cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802292"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials：陀螺仪

Gyroscope 类使你能够监控设备的陀螺仪传感器，此传感器测量围绕设备三个主轴的旋转。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>使用 Gyroscope

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `Start` 和 `Stop` 方法来使用 Gyroscope 功能以侦听陀螺仪的变化。 然后通过 `ReadingChanged` 事件反馈任何变化（以 rad/s 为单位）。 示例用法如下：

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Gyroscope 源代码](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Gyroscope)
- [Gyroscope API 文档](xref:Xamarin.Essentials.Gyroscope)
