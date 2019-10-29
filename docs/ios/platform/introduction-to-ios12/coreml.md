---
title: Xamarin 中的 Core ML 2
description: 本文档介绍了作为 iOS 12 的一部分提供的 Core ML 更新。 特别是，它会查看与新的批处理预测 API 相关的性能改进。
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/15/2018
ms.openlocfilehash: 6245873385caa23e37d5499daa822fa0b699ac1e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032024"
---
# <a name="core-ml-2-in-xamarinios"></a>Xamarin 中的 Core ML 2

Core ML 是 iOS、macOS、tvOS 和 watchOS 上提供的机器学习技术。 它允许应用根据机器学习模型进行预测。

在 iOS 12 中，Core ML 包含一个批处理 API。 此 API 使 Core ML 更高效，并在使用模型进行预测的情况下提高了性能。

## <a name="sample-app-marshabitatcoremltimer"></a>示例应用： MarsHabitatCoreMLTimer

若要使用 Core ML 演示批处理预测，请查看[MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)示例应用。 此示例使用经过训练的核心 ML 模型，根据各种输入来预测在 Mars 上构建 habitat 的成本：阳历面板数量、greenhouses 数和英亩数。

本文档中的代码片段来自此示例。

## <a name="generate-sample-data"></a>生成示例数据

在 `ViewController`中，示例应用的 `ViewDidLoad` 方法会调用 `LoadMLModel`，这会加载包含的核心 ML 模型：

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

然后，该示例应用会创建 100000 `MarsHabitatPricerInput` 对象，以用作顺序核心 ML 预测的输入。 每个生成的示例都有一个随机值集，其中包含阳历面板的数量、greenhouses 数和英亩数量：

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

点击任意应用的三个按钮会执行两个预测序列：一个使用 `for` 循环，另一个使用 iOS 12 中引入的新 batch `GetPredictions` 方法：

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for 循环

测试 naively 的 `for` 循环版本将循环访问指定的输入数目，并为每个输入调用[`GetPrediction`](xref:CoreML.MLModel.GetPrediction*)并放弃结果。 方法为进行预测所需的时间：

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions （新建批处理 API）

测试的批处理版本从输入数组创建一个 `MLArrayBatchProvider` 对象（因为这是 `GetPredictions` 方法所必需的输入参数），将创建一个[`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
对象，该对象阻止预测计算限制于 CPU，并使用 `GetPredictions` API 提取预测，并再次放弃结果：

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>结果

在模拟器和设备上，`GetPredictions` 比基于循环的核心 ML 预测快得多。

## <a name="related-links"></a>相关链接

- [示例应用– MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [核心 ML 中的新增功能，第1部分（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/708/)
- [核心 ML 中的新增功能，第2部分（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Xamarin 中的 Core ML 简介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [核心 ML （Apple）](https://developer.apple.com/documentation/coreml?language=objc)
- [使用 Core ML 模型](https://developer.apple.com/machine-learning/build-run-models/)
