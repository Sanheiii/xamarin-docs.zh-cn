---
title: 自定义 WebView
description: Xamarin.Forms WebView 是在应用中显示 Web 和 HTML 内容的视图。 本文介绍如何创建一个扩展 WebView 以允许从 JavaScript 调用 C# 代码的自定义呈现器。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/31/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3d0cbae406861565d91b0bbc9f39d661c547ca1e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561152"
---
# <a name="customizing-a-webview"></a>自定义 WebView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

Xamarin.Forms `WebView` 是在应用中显示 Web 和 HTML 内容的视图。本文介绍如何创建一个扩展 `WebView` 以允许从 JavaScript 调用 C# 代码的自定义呈现器。

每个 Xamarin.Forms 视图都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 当 iOS 中的 Xamarin.Forms 应用程序呈现 [`WebView`](xref:Xamarin.Forms.WebView) 时，将实例化 `WkWebViewRenderer` 类，而该操作又会实例化本机 `WkWebView` 控件。 在 Android 平台上，`WebViewRenderer` 类实例化本机 `WebView` 控件。 在通用 Windows 平台 (UWP) 上，`WebViewRenderer` 类实例化本机 `WebView` 控件。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`View`](xref:Xamarin.Forms.View) 和实现它的相应本机控件之间的关系：

![WebView 类及其实现本机类之间的关系](hybridwebview-images/webview-classes.png)

通过在每个平台上为 [`WebView`](xref:Xamarin.Forms.WebView) 创建自定义呈现器，可使用呈现过程来实现平台自定义。 执行此操作的过程如下：

1. [创建](#create-the-hybridwebview)`HybridWebView`自定义控件。
1. [通过 Xamarin.Forms 使用](#consume-the-hybridwebview) `HybridWebView`。
1. 在每个平台上为 `HybridWebView`[创建](#create-the-custom-renderer-on-each-platform)自定义呈现器。

现在，依次讨论每个项目以实现 `HybridWebView` 呈现器，该呈现器可增强 Xamarin.Forms Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) 以允许从 JavaScript 调用 C# 代码。 `HybridWebView` 实例将用于显示要求用户输入其名称的 HTML 页。 然后，当用户单击 HTML 按钮，JavaScript 函数将调用 C# `Action` 显示一个包含用户名称的弹出项。

要详细了解从 JavaScript 调用 C# 的过程，请参阅[从 JavaScript 调用 C#](#invoke-c-from-javascript)。 有关 HTML 页面的详细信息，请参阅[创建网页](#create-the-web-page)。

> [!NOTE]
> [`WebView`](xref:Xamarin.Forms.WebView) 可从 C# 调用 JavaScript 函数，并将任何结果返回给调用的 C# 代码。 有关详细信息，请参阅[调用 JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript)。

## <a name="create-the-hybridwebview"></a>创建 HybridWebView

可通过对 [`WebView`](xref:Xamarin.Forms.WebView) 类创建子类来创建 `HybridWebView` 自定义控件：

```csharp
public class HybridWebView : WebView
{
    Action<string> action;

    public static readonly BindableProperty UriProperty = BindableProperty.Create(
        propertyName: "Uri",
        returnType: typeof(string),
        declaringType: typeof(HybridWebView),
        defaultValue: default(string));

    public string Uri
    {
        get { return (string)GetValue(UriProperty); }
        set { SetValue(UriProperty, value); }
    }

    public void RegisterAction(Action<string> callback)
    {
        action = callback;
    }

    public void Cleanup()
    {
        action = null;
    }

    public void InvokeAction(string data)
    {
        if (action == null || data == null)
        {
            return;
        }
        action.Invoke(data);
    }
}
```

`HybridWebView` 自定义控件创建于 .NET Standard 库项目中，它为控件定义以下 API：

- 指定要加载的网页的地址的 `Uri` 属性。
- 用于向控件注册 `Action` 的 `RegisterAction` 方法。 已注册的操作将从通过 `Uri` 属性引用的 HTML 文件中包含的 JavaScript 调用。
- 移除对已注册的 `Action` 的引用的 `CleanUp` 方法。
- 调用已注册的 `Action` 的 `InvokeAction` 方法。 将从每个平台项目中的自定义呈现器中调用此方法。

## <a name="consume-the-hybridwebview"></a>使用 HybridWebView

通过在自定义控件上声明 `HybridWebView` 自定义控件位置的命名空间并使用命名空间前缀，可以在 .NET Standard 库项目的 XAML 中引用该自定义控件。 以下代码示例展示了 XAML 页可以如何使用 `HybridWebView` 自定义控件：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,40,0,0">
    <local:HybridWebView x:Name="hybridWebView"
                         Uri="index.html" />
</ContentPage>

```

`local` 命名空间前缀可以命名为任何内容。 但是，`clr-namespace` 和 `assembly` 值必须与自定义控件的详细信息相匹配。 声明命名空间后，前缀用于引用自定义控件。

以下代码示例展示了 C# 页可以如何使用 `HybridWebView` 自定义控件：

```csharp
public HybridWebViewPageCS()
{
    var hybridWebView = new HybridWebView
    {
        Uri = "index.html"
    };
    // ...
    Padding = new Thickness(0, 40, 0, 0);
    Content = hybridWebView;
}
```

`HybridWebView` 实例将用于显示每个平台上的本地 Web 控件。 它的 `Uri` 属性设置为一个存储在每个平台项目中的 HTML 文件，并由本机 Web 控件显示。 呈现的 HTML 要求用户输入其名称，并使用 JavaScript 函数调用 C# `Action` 以对 HTML 按钮单击进行响应。

`HybridWebViewPage` 注册要从 JavaScript 调用的操作，如以下代码示例所示：

```csharp
public partial class HybridWebViewPage : ContentPage
{
    public HybridWebViewPage()
    {
        // ...
        hybridWebView.RegisterAction(data => DisplayAlert("Alert", "Hello " + data, "OK"));
    }
}
```

此操作调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法以显示模式弹出项，该弹出项显示在 `HybridWebView` 实例显示的 HTML 页面中输入的名称。

现可将自定义呈现器添加到每个应用程序项目，以通过允许从 JavaScript 调用 C# 代码来增强平台 Web 控件。

## <a name="create-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 在 iOS 上创建 `WkWebViewRenderer` 类的子类，并在 Android 和 UWP 上创建 `WebViewRenderer` 类，用于呈现自定义控件。
1. 替代呈现 [`WebView`](xref:Xamarin.Forms.WebView) 的 `OnElementChanged` 方法，并编写逻辑对其进行自定义。 创建 `HybridWebView` 对象时，调用此方法。
1. 向自定义呈现器类或 AssemblyInfo.cs 添加 `ExportRenderer` 属性，以指定它将用于呈现 Xamarin.Forms 自定义控件。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，都可选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。 但是，呈现[视图](xref:Xamarin.Forms.View)元素时，每个平台项目中都需要自定义呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![HybridWebView 自定义呈现器项目的责任](hybridwebview-images/solution-structure.png)

`HybridWebView` 自定义控件由平台呈现器类呈现，这些类在 iOS 上派生自 `WkWebViewRenderer` 类，而在 Android 和 UWP 上，则派生自 `WebViewRenderer` 类。 这导致每个 `HybridWebView` 自定义控件都使用本机 Web 控件呈现，如以下屏幕截图所示：

![每个平台上的 HybridWebView](hybridwebview-images/screenshots.png)

`WkWebViewRenderer` 和 `WebViewRenderer` 类公开 `OnElementChanged` 方法，创建 Xamarin.Forms 自定义控件时会调用此方法以呈现对应的本机 Web 控件。 此方法采用 `VisualElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素 。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `HybridWebView` 实例的引用。

在每个平台呈现器类中，`OnElementChanged` 方法的替代版本可执行本机 Web 控件自定义。 可以通过 `Element` 属性获取正在呈现的 Xamarin.Forms 控件的引用。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各部分讨论每个本机 Web 控件加载的网页的结构、从 JavaScript 调用 C# 的过程以及在每个平台自定义呈现器类中的实现。

### <a name="create-the-web-page"></a>创建网页

下面的代码示例显示了将由 `HybridWebView` 自定义控件显示的网页：

```html
<html>
<body>
    <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
    <h1>HybridWebView Test</h1>
    <br />
    Enter name: <input type="text" id="name">
    <br />
    <br />
    <button type="button" onclick="javascript: invokeCSCode($('#name').val());">Invoke C# Code</button>
    <br />
    <p id="result">Result:</p>
    <script type="text/javascript">function log(str) {
            $('#result').text($('#result').text() + " " + str);
        }

        function invokeCSCode(data) {
            try {
                log("Sending Data:" + data);
                invokeCSharpAction(data);
            }
            catch (err) {
                log(err);
            }
        }</script>
</body>
</html>
```

网页允许用户在 `input` 元素中输入他们的名称，并在单击时提供引用 C# 代码的 `button` 元素。 实现此操作的过程如下：

- 当用户单击 `button` 元素时，调用 `invokeCSCode` JavaScript 函数，并将 `input` 元素的值传递到函数。
- `invokeCSCode` 函数调用 `log` 函数以显示发送给 C# `Action` 的数据。 然后它调用 `invokeCSharpAction` 方法以调用 C# `Action`，传递来自 `input` 元素的参数。

`invokeCSharpAction` JavaScript 函数未在网页中定义，并将由每个自定义渲染器注入其中。

在 iOS 上，此 HTML 文件位于平台项目的“内容”文件夹中，包含 BundleResource 的生成操作。 在 Android 上，此 HTML 文件位于平台项目的“资产/内容”文件夹中，包含 AndroidAsset 的生成操作。

### <a name="invoke-c-from-javascript"></a>从 JavaScript 调用 C#

从 JavaScript 调用 C# 的过程在每个平台上完全相同：

- 自定义呈现器创建本机 Web 控件并加载 `HybridWebView.Uri` 属性指定的 HTML 文件。
- 加载网页后，自定义呈现器将 `invokeCSharpAction` JavaScript 函数注入到网页中。
- 当用户输入其名称并单击 HTML `button` 元素时，会调用 `invokeCSCode` 函数，并随之调用 `invokeCSharpAction` 函数。
- `invokeCSharpAction` 函数调用自定义呈现器中的方法，该方法随之调用 `HybridWebView.InvokeAction` 方法。
- `HybridWebView.InvokeAction` 方法调用已注册的 `Action`。

以下各节将讨论此过程在每个平台上的实现方式。

### <a name="create-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

以下代码示例展示了适用于 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : WkWebViewRenderer, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        public HybridWebViewRenderer() : this(new WKWebViewConfiguration())
        {
        }

        public HybridWebViewRenderer(WKWebViewConfiguration config) : base(config)
        {
            userController = config.UserContentController;
            var script = new WKUserScript(new NSString(JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
            userController.AddUserScript(script);
            userController.AddScriptMessageHandler(this, "invokeAction");
        }

        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                userController.RemoveAllUserScripts();
                userController.RemoveScriptMessageHandler("invokeAction");
                HybridWebView hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }

            if (e.NewElement != null)
            {
                string filename = Path.Combine(NSBundle.MainBundle.BundlePath, $"Content/{((HybridWebView)Element).Uri}");
                LoadRequest(new NSUrlRequest(new NSUrl(filename, false)));
            }
        }

        public void DidReceiveScriptMessage(WKUserContentController userContentController, WKScriptMessage message)
        {
            ((HybridWebView)Element).InvokeAction(message.Body.ToString());
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 [`WKWebView`](xref:WebKit.WKWebView) 控件中，并将 `invokeCSharpAction` JavaScript 函数注入到网页中。 用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数，并在收到来自网页的消息后调用 `DidReceiveScriptMessage` 方法。 然后此方法将调用 `HybridWebView.InvokeAction` 方法，该方法调用已注册的操作以显示弹出项。

此功能以下述方式实现：

- 呈现器构造函数创建一个 `WkWebViewConfiguration` 对象，并检索其 [`WKUserContentController`](xref:WebKit.WKUserContentController) 对象。 `WkUserContentController` 对象允许发布消息并将用户脚本注入网页。
- 呈现器构造函数创建一个 [`WKUserScript`](xref:WebKit.WKUserScript) 对象，该对象在加载网页后将 `invokeCSharpAction` JavaScript 函数注入到该网页中。
- 呈现器构造函数调用 [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) 方法，以将 [`WKUserScript`](xref:WebKit.WKUserScript) 对象添加到内容控制器中。
- 呈现器构造函数调用 [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) 方法，以将名为 `invokeAction` 的脚本消息处理程序添加到 [`WKUserContentController`](xref:WebKit.WKUserContentController) 对象，这将导致 JavaScript 函数 `window.webkit.messageHandlers.invokeAction.postMessage(data)` 在使用 `WKUserContentController` 对象的所有 `WebView` 实例中的所有框架中定义。
- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) 方法加载 `HybridWebView.Uri` 属性指定的 HTML 文件。 该代码指定该文件存储在 `Content` 项目的文件夹中。 显示网页后，`invokeCSharpAction` JavaScript 函数将注入到网页中。
- 当呈现器附加到的元素发生更改时，便会发布资源。
- 释放呈现器时，将清理 Xamarin.Forms 元素。

> [!NOTE]
> `WKWebView` 类仅支持 iOS 8 及更高版本。

此外，Info.plist 必须更新，以包含以下值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="create-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

以下代码示例展示了适用于 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                ((HybridWebView)Element).Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.SetWebViewClient(new JavascriptWebViewClient(this, $"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 [`WebView`](xref:Android.Webkit.WebView) 控件中，并在网页加载完成后使用 `JavascriptWebViewClient` 类中 `OnPageFinished` 的替代方法将 `invokeCSharpAction` JavaScript函数注入到网页中：

```csharp
public class JavascriptWebViewClient : FormsWebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(HybridWebViewRenderer renderer, string javascript) : base(renderer)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数。 此功能以下述方式实现：

- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - `SetWebViewClient` 方法将新的 `JavascriptWebViewClient` 对象设置为 `WebViewClient` 的实现。
  - [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) 方法将新的 `JSBridge` 实例注入到 WebView 的 JavaScript 上下文的主框架中，并将其命名为 `jsBridge`。 这允许从 JavaScript 访问 `JSBridge` 类中的方法。
  - [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) 方法加载 `HybridWebView.Uri` 属性指定的 HTML 文件。 该代码指定该文件存储在 `Content` 项目的文件夹中。
  - 在 `JavascriptWebViewClient` 类中，页面加载完成后会将 `invokeCSharpAction` JavaScript 函数注入到网页中。
- 当呈现器附加到的元素发生更改时，便会发布资源。
- 释放呈现器时，将清理 Xamarin.Forms 元素。

当执行 `invokeCSharpAction` JavaScript 函数时，它会调用 `JSBridge.InvokeAction` 方法，该方法如下面的代码示例所示：

```csharp
public class JSBridge : Java.Lang.Object
{
    readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

    public JSBridge(HybridWebViewRenderer hybridRenderer)
    {
        hybridWebViewRenderer = new WeakReference<HybridWebViewRenderer>(hybridRenderer);
    }

    [JavascriptInterface]
    [Export("invokeAction")]
    public void InvokeAction(string data)
    {
        HybridWebViewRenderer hybridRenderer;

        if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget(out hybridRenderer))
        {
            ((HybridWebView)hybridRenderer.Element).InvokeAction(data);
        }
    }
}
```

该类必须派生自 `Java.Lang.Object` 且必须使用 `[JavascriptInterface]` 和 `[Export]` 属性修饰公开给 JavaScript 的方法。 因此，当 `invokeCSharpAction` JavaScript 函数注入到网页中并执行时，由于使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 属性进行了修饰，它将调用 `JSBridge.InvokeAction` 方法。 然后，`InvokeAction` 方法调用 `HybridWebView.InvokeAction` 方法，这将调用已注册的操作以显示弹出项。

> [!IMPORTANT]
> 使用 `[Export]` 属性的 Android 项目必须包含对 `Mono.Android.Export` 的引用，否则将导致编译器错误。

请注意，`JSBridge` 类保持对 `HybridWebViewRenderer` 类的 `WeakReference`。 这是为了避免在两个类之间创建循环引用。 有关详细信息，请参阅[弱引用](/en-us/dotnet/standard/garbage-collection/weak-references)。

### <a name="create-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

以下代码示例展示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri($"ms-appx-web:///Content//{((HybridWebView)Element).Uri}");
            }
        }

        async void OnWebViewNavigationCompleted(Windows.UI.Xaml.Controls.WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            ((HybridWebView)Element).InvokeAction(e.Value);
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 `WebView` 控件中，并在加载网页后使用 `WebView.InvokeScriptAsync` 方法将 `invokeCSharpAction` JavaScript函数注入到网页中。 用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数，并在收到来自网页的通知后调用 `OnWebViewScriptNotify` 方法。 然后此方法将调用 `HybridWebView.InvokeAction` 方法，该方法调用已注册的操作以显示弹出项。

此功能以下述方式实现：

- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - 已注册用于 `NavigationCompleted` 和 `ScriptNotify` 事件的事件处理程序。 当本机 `WebView` 控件已完成加载当前内容或导航失败时，将触发 `NavigationCompleted` 事件。 当本机 `WebView` 控件中的内容使用 JavaScript 传递字符串到应用程序时，会激发 `ScriptNotify` 事件。 网页在传递 `string` 参数时通过调用 `window.external.notify` 激发 `ScriptNotify` 事件。
  - `WebView.Source` 属性设置为 `HybridWebView.Uri` 属性指定的 HTML 文件的 URI。 该代码假定该文件存储在项目 `Content` 文件夹中。 显示网页后，会激发 `NavigationCompleted` 事件，并调用 `OnWebViewNavigationCompleted` 方法。 导航成功完成后，会使用 `WebView.InvokeScriptAsync` 方法将 `invokeCSharpAction` JavaScript 函数注入到网页。
- 当呈现器附加到的元素更改时，取消订阅事件。
- 释放呈现器时，将清理 Xamarin.Forms 元素。

## <a name="related-links"></a>相关链接

- [HybridWebView（示例）](/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)