---
title: 跨平台性能
description: 本文档介绍的各种技术可用于提高移动应用程序的性能。 其中讨论了 Profiler、IDisposable 资源、弱引用，SGen 垃圾回收器、大小缩减技术等。
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: f7f5d7daed42eeb3a36dd9b0d09b31a06b0de142
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457947"
---
# <a name="cross-platform-performance"></a>跨平台性能

应用程序性能差表现在许多方面。 这会造成应用程序看起来无响应，导致滚动缓慢，还可降低电池寿命。 但是，优化性能不止需要实现高效的代码。 还必须考虑用户对应用程序性能的体验。 例如，确保操作执行不会妨碍用户执行其他活动，这有助于改进用户的体验。

<a name="profiler"></a>

## <a name="use-the-profiler"></a>使用 Profiler

开发应用程序时，请务必先分析代码，再尝试对代码进行优化。 分析是一种确定代码优化对减少性能问题的哪方面影响最大的方法。 探查器跟踪应用程序的内存使用率，并记录应用程序中方法的运行时间。 此数据可帮助导航到应用程序的执行路径和代码执行开销，以便发现优化的最佳时机。

Xamarin Profiler 将测量、评估和帮助查找应用程序中的性能相关问题。 可用于从 Visual Studio for Mac 或 Visual Studio 内部分析 Xamarin.iOS 和 Xamarin.Android 应用程序。 有关 Xamarin Profiler 的详细信息，请参阅 [Xamarin Profiler 简介](~/tools/profiler/index.md)。

分析应用时建议采用以下最佳做法：

- 避免在模拟器中分析应用程序，因为模拟器可能误报应用程序性能。
- 理想情况下，可在各种设备上执行分析，因为在某一设备上采取性能测量不会始终显示其他设备的性能特征。 但是，至少应在具有最低预期规范的设备上执行分析。
- 关闭所有其他应用程序可确保测量所分析应用程序（而不是其他应用程序）的总体影响。

<a name="idisposable"></a>

## <a name="release-idisposable-resources"></a>释放 IDisposable 资源

`IDisposable` 接口提供一种用于释放资源的机制。 还提供一种应实现的 `Dispose` 方法来显式释放资源。 `IDisposable` 不是析构函数，且仅在以下情况下才得以实现：

- 类拥有非托管资源时。 需要释放的典型非托管资源包括文件、流和网络连接。
- 类拥有托管的 `IDisposable` 资源时。

然后，不再需要该实例时，类型使用者便可调用 `IDisposable.Dispose` 实现来释放资源。 可通过两种方法来实现此目的：

- 通过在 `using` 语句中包装 `IDisposable` 对象。
- 通过在 `try`/`finally` 块中包装对 `IDisposable.Dispose` 的调用。

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>在 using 语句中包装 IDisposable 对象

以下代码示例演示如何在 `using` 语句中包装 `IDisposable` 对象：

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

`StreamReader` 类实现 `IDisposable`，`using` 语句提供一种简便语法，用于在 `StreamReader` 对象超出作用域之前对其调用 `StreamReader.Dispose` 方法。 在 `using` 块中，`StreamReader` 对象为只读，且不能重新分配。 `using` 语句还可确保即使出现异常仍能调用 `Dispose` 方法，因为编译器对 `try`/`finally` 块实现了中间语言 (IL)。

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>在 Try/Finally 块中包装对 IDisposable.Dispose 的调用

以下代码示例演示如何在 `try`/`finally` 语句中包装对 `IDisposable.Dispose` 的调用：

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

`StreamReader` 类实现 `IDisposable`，`finally` 块调用 `StreamReader.Dispose` 方法来释放资源。

有关详细信息，请参阅 [IDisposable 接口](xref:System.IDisposable)。

<a name="events"></a>

## <a name="unsubscribe-from-events"></a>取消订阅事件

为防止内存泄漏，释放订阅者对象之前应取消订阅事件。 取消订阅后，发布对象中的事件委托便存在对封装订户事件处理程序的委托的引用。 只要发布对象保留此引用，垃圾回收就不会回收此订户对象内存。

以下代码示例演示如何取消订阅事件：

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

`Subscriber` 类采用其 `Dispose` 方法取消订阅事件。

使用事件处理程序和 lambda 语法时也可能出现引用循环，因为 Lambda 表达式可引用对象，并使其保持活动状态。 因此，对匿名方法的引用可存储在字段中，并可用于取消订阅事件，如以下代码示例所示：

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

`handler` 字段维护对匿名方法的引用，并用于订阅和取消订阅事件。

<a name="weakreferences"></a>

## <a name="use-weak-references-to-prevent-immortal-objects"></a>使用弱引用来阻止不变对象

> [!NOTE]
> iOS 开发者应查看有关[在 iOS 中避免循环引用](~/ios/deploy-test/performance.md#avoid-strong-circular-references)的文档，确保其应用高效使用内存。

<a name="lazy"></a>

## <a name="delay-the-cost-of-creating-objects"></a>延迟创建对象的开销

可使用延迟初始化来延迟创建对象，直至首次使用该对象。 此方法主要用于提升性能、避免计算和降低内存需求。

出现以下 2 种情况时，建议使用延迟初始化来创建开销很大的对象：

- 应用程序可能不会使用该对象。
- 创建对象之前，必须先完成其他大开销操作。

`Lazy<T>` 类用于定义延迟初始化类型，如以下代码示例所示：

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

首次访问 `Lazy<T>.Value` 属性时出现延迟初始化。 首次访问包装类型时，会创建并返回该包装类型，并将其存储起来以备将来随意访问。

有关延迟初始化的详细信息，请参阅[延迟初始化](/dotnet/framework/performance/lazy-initialization)。

<a name="async"></a>

## <a name="implement-asynchronous-operations"></a>实现异步操作

.NET 提供了许多其本身 API 的异步版本。 与同步 API 不同，异步 API 可确保活动的执行线程在很长一段时间内不会阻止调用线程。 因此，从 UI 线程调用 API 时，请使用可用的异步 API。 这将保持 UI 线程通畅，从而有助于提升用户对应用程序的体验。

此外，应在后台线程中执行长时间运行的操作，以避免阻塞 UI 线程。 .NET 提供的 `async` 和 `await` 关键字允许编写异步代码，可利用该代码在后台线程中执行长时间运行操作并在完成时获得结果。 然而，尽管使用 `await` 关键字可异步执行长时间运行操作，但无法保证该操作在后台线程运行。 相反，通过将长时间运行操作传递给 `Task.Run` 可实现此目的，如以下代码示例所示：

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

`RecognizeFace` 方法在后台线程中执行，同时 `RecognizeFaceButtonClick` 方法需等待 `RecognizeFace` 方法完成后才能继续。

还可取消长时间运行操作。 例如，如果用户在应用程序中导航，则不必继续执行长时间运行操作。 用于实现取消操作的模式如下：

- 创建一个 `CancellationTokenSource` 实例。 此实例将管理和发送取消通知。
- 向每个应取消的任务传递 `CancellationTokenSource.Token` 属性值。
- 为每个任务提供响应取消的机制。
- 调用 `CancellationTokenSource.Cancel` 方法以发出取消通知。

> [!IMPORTANT]
> `CancellationTokenSource` 类实现 `IDisposable` 接口，因此完成处理 `CancellationTokenSource` 实例后应调用 `CancellationTokenSource.Dispose` 方法。

有关详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

<a name="sgen"></a>

## <a name="use-the-sgen-garbage-collector"></a>使用 SGen 垃圾回收器

C# 等托管语言使用垃圾回收功能来回收分配给不再使用的对象的内存。 Xamarin 平台使用以下 2 种垃圾回收器：

- [**SGen**](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) - 这是一种分代垃圾回收器，也是 Xamarin 平台上的默认垃圾回收器。
- [**Boehm**](http://www.hboehm.info/gc/) – 这是一种传统的非分代垃圾回收器。 它是用于 Xamarin.iOS 应用程序（使用 Classic API）的默认垃圾回收器。

SGen 利用以下三个堆中的一个来为对象分配空间：

- **小堆** – 在此处分配新的小型对象。 小堆空间不足时，将发生次要垃圾回收。 所有活动对象均被移动到主堆。
- **主堆** – 在此处保存长时间运行对象。 如果主堆内存不足，则会发生主要垃圾回收。 如果主要垃圾回收无法释放足够内存，SGen 将要求系统提供更多内存。
- **大型对象空间** – 在此处保存需要超过 8000 个字节的对象。 大型对象不会在小堆中分配，而是在此堆中分配。

SGen 的一个优点是：执行次要垃圾回收所需时间与自上次执行次要垃圾回收以来所创建新活动对象的数目成正比。 这可降低垃圾回收对应用程序性能的影响，因为次要垃圾回收比主要垃圾回收所需时间更少。 主要垃圾回收仍会发生，但频率较低。

SGen 是 Xamarin.iOS 9.2.1 及更高版本中的默认垃圾回收器，因此系统将自动使用它。 请注意，无法再在更高版本的 Visual Studio 中更改垃圾回收器。 有关详细信息，请参阅[新的引用计数系统](~/ios/internals/newrefcount.md)。

### <a name="reducing-pressure-on-the-garbage-collector"></a>减轻垃圾回收器的压力

SGen 启动垃圾回收时，将在回收内存的同时停止应用程序的线程。 回收内存的过程中，应用程序可能出现短时间暂停或 UI 卡顿。 暂停时间长短取决于以下 2 个因素：

1. **频率** – 垃圾回收发生的频率。 两次回收之间分配的内存越多，垃圾回收的频率越高。
1. **持续时间** – 每次垃圾回收需要多长时间。 这与所回收的活动对象数大致成正比。

总体而言，这意味着如果分配多个未处于活动状态的对象，则垃圾回收的次数较多、时间较短。 反之，如果缓慢分配处于活动状态的新对象，则垃圾回收的次数更少、时间更长。

若要减轻垃圾回收器的压力，请遵循以下指导：

- 通过使用对象池来避免紧密循环的垃圾回收。 这尤其适用于需要预先创建大部分对象的游戏。
- 不再需要流、网络连接、较大内存块和文件等资源后，立即显式释放这些资源。 有关详细信息，请参阅[释放 IDisposable 资源](#idisposable)。
- 不再需要事件处理程序后立即将它们注销，使系统可收集对象。 有关详细信息，请参阅[取消订阅事件](#events)。

<a name="linker"></a>

## <a name="reduce-the-size-of-the-application"></a>缩减应用程序大小

务必了解每个平台上的编译流程，才能了解应用程序可执行文件大小的来源：

- iOS 应用程序预先 (AOT) 编译为 ARM 汇编语言。 其中包括 .NET Framework，并且仅在启用了适当的链接器选项时才去除未使用的类。
- Android 应用程序编译为中间语言 (IL)，并打包了 MonoVM 和实时 (JIT) 编译。 仅在启用了适当的链接器选项时才去除未使用的框架类。
- Windows Phone 应用程序编译为 IL，并由内置运行时执行。

此外，如果应用程序广泛使用泛型，则最终的可执行文件大小将进一步增加，因为它包含无限可能性的本机编译版本。

为帮助缩减应用程序的大小，Xamarin 平台将链接器包含在了生成工具中。 链接器默认处于禁用状态，必须在应用程序的项目选项中启用。 在生成时，链接器会对应用程序执行静态分析，确定应用程序实际使用的类型和成员。 然后，从应用程序中删除所有未使用的类型和方法。

以下屏幕截图显示了 Visual Studio for Mac 中 Xamarin.iOS 项目的链接器选项：

![Xamarin.iOS 的链接器选项](memory-perf-best-practices-images/linker-options-ios.png)

以下屏幕截图显示了 Visual Studio for Mac 中 Xamarin.Android 项目的链接器选项：

![Xamarin.Android 的链接器选项](memory-perf-best-practices-images/linker-options-droid.png)

链接器提供 3 个可用于控制其行为的设置：

- **不链接** - 链接器不会删除任何未使用的类型和方法。 出于性能方面的考虑，默认对调试版本使用此设置。
- **仅链接 Framework SDK/SDK 程序集** - 此设置只会缩减 Xamarin 提供的程序集大小。 用户代码不受影响。
- **链接所有程序集** - 这是一种针对 SDK 程序集和用户代码的更高性能优化。 对于绑定，这将删除未使用的支持字段并淡化每个实例（或绑定对象），以占用较少内存。

应慎用“链接所有程序集”  ，因为它可能以意外方式中断应用程序。 链接器执行的静态分析可能无法正确识别所有必需代码，从而导致从编译应用程序中删除过多代码。 应用程序崩溃时，只会在运行时表现出这种情况。 因此，务必在更改链接器行为后全面测试应用程序。

如果测试结果表明链接器错误删除了某个类或方法，则可使用以下属性之一来标记未静态引用但应用程序需要的类型或方法：

- `Xamarin.iOS.Foundation.PreserveAttribute` - 此属性适用于 Xamarin.iOS 项目。
- `Android.Runtime.PreserveAttribute` - 此属性适用于 Xamarin.Android 项目。

例如，可能需要保留经过动态实例化的类型的默认构造函数。 此外，使用 XML 序列化可能需要保留类型的属性。

有关详细信息，请参阅[用于 iOS 的链接器](~/ios/deploy-test/linker.md)和[用于 Android 的链接器](~/android/deploy-test/linker.md)。

### <a name="additional-size-reduction-techniques"></a>缩减大小的其他方法

有多种支持移动设备的 CPU 体系结构。 因此，Xamarin.iOS 和 Xamarin.Android 生成 fat 二进制文件  ，其中包含每个 CPU 体系结构的应用程序已编译版本。 这可确保无论 CPU 体系结构为何，移动应用程序均可在设备上运行。

可使用以下步骤进一步缩小应用程序可执行文件：

- 确保生成了发布版本。
- 减少生成应用程序的体系结构数，避免生成 FAT 二进制文件。
- 确保使用 LLVM 编译器，以生成更优的可执行文件。
- 缩小应用程序的托管代码。 通过对每个程序集启用链接器可实现此目的（对于 iOS 项目，选择“链接所有”  ；对于 Android 项目，选择“链接所有程序集”  ）。

对于每个 ABI，Android 应用也能拆分为单独的 APK（“体系结构”）。
通过此博客文章详细了解：[如何减小 Android 应用大小](https://montemagno.com/how-to-keep-your-android-app-size-down/)。

<a name="optimizeimages"></a>

## <a name="optimize-image-resources"></a>优化图像资源

图像是应用程序使用的一些最昂贵的资源，通常以高分辨率捕获。 尽管这可创建包含完整详细信息的生动图像，但显示此类图像的应用程序往往需要占用更多 CPU 才能解码图像和更多内存来存储已解码图像。 如果需要缩小图像才能显示，那么在内存中解码高分辨率图像就是一种资源浪费。 相反，可通过创建已存储图像的多个分辨率版本（接近预计显示大小）来减少 CPU 使用率和内存占用量。 例如，在列表视图中显示的图像分辨率多数时候都比全屏显示的图像分辨率更低。 此外，还可以加载缩小版本的高分辨率图像，在最大程度降低对内存影响的同时有效地显示这些图像。 有关详细信息，请参阅[高效加载大位图](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently)。

无论图像分辨率高低，显示图像资源都可能大大提高应用的内存占用量。 因此，仅应在必要时创建图像，应用程序不再需要图像后应立即将其释放。

<a name="activationperiod"></a>

## <a name="reduce-the-application-activation-period"></a>缩短应用程序激活期限

所有应用程序都有激活期限  ，这是指应用程序启动和应用程序可供使用之间的时间段。 此激活期限是用户对应用程序的第一印象，因此缩短激活期限和用户等待时间对于获得用户对应用程序的良好第一印象非常重要。

应用程序显示其初始 UI 之前，应提供指示用户应用程序正在启动的初始屏幕。 如果应用程序无法快速显示其初始 UI，初始屏幕应通过激活期限告知用户进度，让用户确信应用程序没有挂起。 可通过进度栏或类似控件提供此确信通知。

激活期间，应用程序会执行激活逻辑，通常包括加载和处理资源。 通过确保所需资源已打包在应用内，而不用远程检索，可缩短激活期限。 例如，某些情况可能适合在激活期间加载存储在本地的占位符数据。 然后，显示初始 UI 后，用户便可与应用交互，占位符数据可逐渐替换为远程源。 此外，应用的激活逻辑仅应执行让用户开始使用应用程序所需的工作。 这在激活逻辑延迟加载其他程序集时可起到帮助，因为程序集在首次使用时需完成加载。

<a name="webservicecommunication"></a>

## <a name="reduce-web-service-communication"></a>减少 Web 服务通信

从应用程序连接到 Web 服务可能影响应用程序性能。 例如，网络带宽使用量增加将导致设备电池使用量增加。 此外，用户可能在带宽受限的环境中使用应用程序。 因此，限制应用程序和 Web 服务之间的带宽利用率是明智之选。

减少应用程序带宽使用率的一种方法是：通过网络传输数据之前，先压缩数据。 但是，压缩过程中的其他 CPU 使用率也可能导致电池用量增加。 因此，决定是否通过网络移动压缩数据之前，应仔细对二者进行权衡。

要考虑的另一个问题是在应用程序和 Web 服务之间移动的数据格式。 两种主要格式为：可扩展标记语言 (XML) 和 JavaScript 对象表示法 (JSON)。 XML 是一种基于文本的数据交换格式，可生成相对较大的数据负载，因为它包含大量格式化字符。 JSON 是一种基于文本的数据交换格式，可生成压缩数据负载，这可在发送和接收数据时降低带宽需求。 因此，对于移动应用程序，通常首选 JSON 格式。

在应用程序和 Web 服务之间传输数据时，建议使用数据传输对象 (DTO)。 DTO 包含一组用于跨网络传输的数据。 利用 DTO，通过一次远程调用即可传输更多数据，从而帮助减少应用程序进行远程调用的次数。 通常，传输较大数据负载的远程调用所需时间与仅传输小型数据负载的调用所需时间相差无几。

检索自 Web 服务的数据应在本地缓存，同时利用已缓存数据而不是反复从 Web 服务检索。 但是，采用此方法时，还应实现合适的缓存策略以便更新本地缓存中的数据（如果数据在 Web 服务中发生更改）。

## <a name="summary"></a>总结

本文介绍和讨论了用于提高使用 Xamarin 平台所生成应用程序的性能的方法。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。

## <a name="related-links"></a>相关链接

- [Xamarin.iOS 性能](~/ios/deploy-test/performance.md)
- [Xamarin.Android 性能](~/android/deploy-test/performance.md)
- [Xamarin Profiler 简介](~/tools/profiler/index.md)
- [Xamarin.Forms 性能](~/xamarin-forms/deploy-test/performance.md)
- [异步支持概述](~/cross-platform/platform/async.md)
- [IDisposable](xref:System.IDisposable)