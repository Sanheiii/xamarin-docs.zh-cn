---
title: Xamarin.Forms 切换
description: 本文介绍如何使用 web Xamarin.Forms 视图类向用户显示本地或网络 web 内容和文档。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d535956e03ac02e2cf0c5d7528ee8e363a2a1e55
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561646"
---
# <a name="no-locxamarinforms-webview"></a>Xamarin.Forms 切换

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) 是用于在应用程序中显示 web 和 HTML 内容的视图：

![在应用浏览器中](webview-images/in-app-browser.png)

## <a name="content"></a>Content

`WebView` 支持以下类型的内容：

- HTML & CSS 网站 &ndash; web 视图对使用 HTML & CSS （包括 JavaScript 支持）编写的网站提供完全支持。
- 文档 &ndash; 因为 Web 视图是使用每个平台上的本机组件实现的，所以 Web 视图能够以基础平台支持的格式显示文档。
- HTML 字符串 &ndash; web 视图可以显示内存中的 html 字符串。
- 本地文件 &ndash; web 视图可以提供应用中嵌入的任何内容类型。

> [!NOTE]
> `WebView` 在 Windows 上，不支持 Silverlight、Flash 或任何 ActiveX 控件，即使该平台上的 Internet Explorer 支持。

### <a name="websites"></a>网站

若要从 internet 显示网站，请将 `WebView` 的 [`Source`](xref:Xamarin.Forms.WebViewSource) 属性设置为字符串 URL：

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必须是使用指定的协议完全相同的 (也就是说，它必须在) 之前预置 "http://" 或 "https://"。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自第9版起，iOS 将仅允许应用程序与默认实现最佳做法安全性的服务器进行通信。 必须将值设置 `Info.plist` 为，以便能够与不安全的服务器通信。

> [!NOTE]
> 如果你的应用程序需要连接到不安全的网站，则应始终使用将该域输入为异常， `NSExceptionDomains` 而不是使用完全关闭 ATS `NSAllowsArbitraryLoads` 。 `NSAllowsArbitraryLoads` 只应在极端紧急情况下使用。

下面演示了如何启用特定域 (在本例中为 xamarin.com) 绕过 ATS 要求：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

最佳做法是仅使某些域能够绕过 ATS，使你可以使用受信任的站点，同时受益不受信任的域中的其他安全性。 下面演示了禁用应用程序的 ATS 的安全方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

有关 iOS 9 中的此新功能的详细信息，请参阅 [应用传输安全性](~/ios/app-fundamentals/ats.md) 。

### <a name="html-strings"></a>HTML 字符串

如果要以代码的方式显示动态定义的 HTML 字符串，则需要创建的实例 [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) ：

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![显示 HTML 字符串的 Web 视图](webview-images/html-string.png)

在上面的代码中， `@` 用于将 HTML 标记为 [逐字字符串文本](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals)，这意味着将忽略大多数转义字符。

> [!NOTE]
> 可能需要设置的 `WidthRequest` 和 `HeightRequest` 属性 [`WebView`](xref:Xamarin.Forms.WebView) 以查看 HTML 内容，具体取决于 `WebView` 是的子布局。 例如，在中，这是必需的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

### <a name="local-html-content"></a>本地 HTML 内容

Web 视图可以显示嵌入在应用中的 HTML、CSS 和 JavaScript 的内容。 例如：

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamarin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

请注意，需要为每个平台自定义上述 CSS 中指定的字体，因为并非每个平台都具有相同的字体。

若要使用显示本地内容 `WebView` ，你需要像打开其他文件一样打开 HTML 文件，然后将内容作为字符串加载到的属性中 `Html` `HtmlWebViewSource` 。 有关打开文件的详细信息，请参阅使用 [文件](~/xamarin-forms/data-cloud/data/files.md)。

以下屏幕截图显示了在每个平台上显示本地内容的结果：

![显示本地内容的 Web 视图](webview-images/local-content.png)

尽管第一页已加载，但并 `WebView` 不知道 HTML 来自何处。 当处理引用本地资源的页面时，这是一个问题。 可能出现这种情况的示例包括：本地页面相互链接，页使用单独的 JavaScript 文件或页面链接到 CSS 样式表。  

若要解决此情况，需告诉在文件 `WebView` 系统上查找文件的位置。 为此，请 `BaseUrl` 在使用的中设置的属性 `HtmlWebViewSource` `WebView` 。

由于每个操作系统上的 filesystem 都不同，因此需要在每个平台上确定 URL。 Xamarin.Forms 公开 `DependencyService` 用于在每个平台上的运行时解析依赖项。

若要使用 `DependencyService` ，请先定义可以在每个平台上实现的接口：

```csharp
public interface IBaseUrl { string Get(); }
```

请注意，在每个平台上实现接口之前，应用程序将不会运行。 在通用项目中，请确保记得使用来设置 `BaseUrl` `DependencyService` ：

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然后必须提供每个平台的接口实现。

#### <a name="ios"></a>iOS

在 iOS 上，web 内容应位于项目的根目录或包含生成操作*BundleResource*的**Resources**目录中，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![IOS 上的本地文件](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![IOS 上的本地文件](webview-images/ios-xs.png)

-----

`BaseUrl`应设置为主绑定的路径：

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

在 Android 上，在 "资产" 文件夹中放置 HTML、CSS 和图像，其中包含生成操作 *AndroidAsset* ，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Android 上的本地文件](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Android 上的本地文件](webview-images/android-xs.png)

-----

在 Android 上， `BaseUrl` 应将设置为 `"file:///android_asset/"` ：

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

在 Android 上，还可以通过当前 Android 上下文访问 " **资产** " 文件夹中的文件，该上下文由 `MainActivity.Instance` 属性公开：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 项目中，将 HTML、CSS 和图像放在项目根中，并将 "生成" 操作设置为 " *内容*"。

`BaseUrl`应设置为 `"ms-appx-web:///"` ：

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>导航

Web 视图支持通过其提供的多个方法和属性进行导航：

- **GoForward ( # B1 ** &ndash; 如果 `CanGoForward` 为 true，则调用 `GoForward` 向前定位到下一个访问的页面。
- **GoBack ( # B1 ** &ndash; 如果 `CanGoBack` 为 true，则调用 `GoBack` 将导航到上次访问的页面。
- **CanGoBack** &ndash;`true`如果有要导航到的页，则为; `false` 如果浏览器位于起始 URL，则为。
- **CanGoForward** &ndash;`true`如果用户已向后导航并可以向前移动到已访问的页面。

在页面中，不 `WebView` 支持多点触控手势。 务必确保内容经过移动优化，并显示而无需缩放。

应用程序通常会在中显示一个链接 `WebView` ，而不是显示在设备的浏览器中。 在这些情况下，允许正常导航是非常有用的，但当用户在开始链接上点击时，应用程序应返回到普通的应用程序视图。

使用内置的导航方法和属性来启用此方案。

首先创建浏览器视图的页面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

在代码隐藏中：

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

就这么简单！

![Web 视图导航按钮](webview-images/in-app-browser.png)

## <a name="events"></a>事件

Web 视图将引发以下事件，以帮助你响应状态更改：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) –当 Web 视图开始加载新页时引发的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) –加载页面和导航停止时引发的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) –发出请求以重新加载当前内容时引发的事件。

[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)事件附带的对象 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 具有四个属性：

- `Cancel` -指示是否取消导航。
- `NavigationEvent` –引发的导航事件。
- `Source` –执行导航的元素。
- `Url` –导航目标。

[`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs)事件附带的对象 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 具有四个属性：

- `NavigationEvent` –引发的导航事件。
- `Result` –介绍使用枚举成员的导航结果 [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) 。 有效值为 `Cancel`、`Failure`、`Success` 和 `Timeout`。
- `Source` –执行导航的元素。
- `Url` –导航目标。

如果预计使用需要很长时间才能加载的网页，请考虑使用 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 和 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 事件来实现状态指示器。 例如：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

两个事件处理程序：

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

这会导致以下输出 (加载) ：

![Web 视图导航事件示例](webview-images/loading-start.png)

已完成加载：

![Web 视图导航事件示例](webview-images/loading-end.png)

## <a name="reloading-content"></a>重新加载内容

[`WebView`](xref:Xamarin.Forms.WebView) 有一个 `Reload` 可用于重载当前内容的方法：

```csharp
var webView = new WebView();
...
webView.Reload();
```

`Reload`调用方法时 `ReloadRequested` ，将触发事件，指示已发出请求以重新加载当前内容。

## <a name="performance"></a>性能

常用的 web 浏览器采用硬件加速呈现和 JavaScript 编译等技术。 在 Xamarin.Forms 4.4 之前， Xamarin.Forms `WebView` 已在 iOS 上通过类实现 `UIWebView` 。 但是，其中许多技术在此实现中不可用。 因此，从 Xamarin.Forms 4.4 开始， Xamarin.Forms `WebView` 是通过类在 iOS 上实现的 `WkWebView` ，它支持更快速地浏览。

> [!NOTE]
> 在 iOS 上， `WkWebViewRenderer` 具有接受参数的构造函数重载 `WkWebViewConfiguration` 。 这样，便可以在创建时配置呈现器。

`UIWebView` Xamarin.Forms `WebView` 出于兼容性原因，应用程序可以返回到使用 iOS 类实现。 可以通过将以下代码添加到应用程序的 iOS 平台项目中的 **AssemblyInfo.cs** 文件来实现此目的：

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView` 默认情况下，Android 上的与内置浏览器的速度一样快。

[UWP Web 视图](/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 呈现引擎。 台式机和平板电脑的性能应与使用 Edge 浏览器本身的性能相同。

## <a name="permissions"></a>权限

为了使 `WebView` 生效，必须确保为每个平台设置权限。 请注意，在某些平台上， `WebView` 将在调试模式下工作，但不能在生成发布时使用。 这是因为，默认情况下，在调试模式下 Visual Studio for Mac 设置某些权限，如 Android 上的 internet 访问权限。

- **UWP** &ndash; 显示网络内容时，要求 Internet (客户端 & 服务器) 功能。
- **Android** &ndash;`INTERNET`仅当显示网络中的内容时才需要。 本地内容不需要特殊权限。
- **iOS** &ndash; 不需要任何特殊权限。

## <a name="layout"></a>布局

与大多数其他 Xamarin.Forms 视图不同， `WebView` 需要 `HeightRequest` `WidthRequest` 在 StackLayout 或 RelativeLayout 中包含和时指定和。 如果无法指定这些属性，则 `WebView` 不会呈现。

下面的示例演示了导致工作的布局，呈现方式 `WebView` 为：

StackLayout with WidthRequest & HeightRequest：

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout with WidthRequest & HeightRequest：

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

*不带*WidthRequest & HeightRequest 的 AbsoluteLayout：

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

*不带*WidthRequest & HeightRequest 的网格。 网格是不需要指定请求的高度和宽度的几个布局之一：

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>调用 JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) 包括从 c # 调用 JavaScript 函数的功能，并将任何结果返回给调用 c # 代码。 这是通过方法完成的 [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) ，如下面的示例： [WebView](/samples/xamarin/xamarin-forms-samples/userinterface-webview)

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法计算指定为参数的 JavaScript，并以形式返回任何结果 `string` 。 在此示例中， `factorial` 调用了 JavaScript 函数，该函数将返回的阶乘 `number` 。 此 JavaScript 函数在加载的本地 HTML 文件中定义 [`WebView`](xref:Xamarin.Forms.WebView) ，如以下示例中所示：

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="cookies"></a>Cookie

可以在上设置 cookie [`WebView`](xref:Xamarin.Forms.WebView) ，然后将其与 web 请求一起发送到指定的 URL。 这是通过将对象添加到来实现的 `Cookie` `CookieContainer` ，然后将其设置为可 `WebView.Cookies` 绑定属性的值。 下面的代码显示了此用法的示例：

```csharp
using System.Net;
using Xamarin.Forms;
// ...

CookieContainer cookieContainer = new CookieContainer();
Uri uri = new Uri("https://dotnet.microsoft.com/apps/xamarin", UriKind.RelativeOrAbsolute);

Cookie cookie = new Cookie
{
    Name = "XamarinCookie",
    Expires = DateTime.Now.AddDays(1),
    Value = "My cookie",
    Domain = uri.Host,
    Path = "/"
};
cookieContainer.Add(uri, cookie);
webView.Cookies = cookieContainer;
webView.Source = new UrlWebViewSource { Url = uri.ToString() };
```

在此示例中，将单个 `Cookie` 添加到 `CookieContainer` 对象，然后将其设置为属性的值 `WebView.Cookies` 。 当将  [`WebView`](xref:Xamarin.Forms.WebView) web 请求发送到指定的 URL 时，cookie 将与请求一起发送。

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>UIWebView 弃用和应用商店拒绝 (ITMS-90809) 

自2020年4月起， [Apple 将拒绝](https://developer.apple.com/news/?id=12232019b) 仍使用弃用的 `UIWebView` API 的应用。 Xamarin.Forms切换为 `WKWebView` 默认值时，仍然存在对二进制文件中的旧 SDK 的引用 Xamarin.Forms 。 当前 [iOS 链接器](~/ios/deploy-test/linker.md) 行为不会删除此结果，因此，在 `UIWebView` 提交到应用商店时，已弃用的 API 仍会从应用中引用。

链接器的预览版本可用于解决此问题。 若要启用预览，需要为链接器提供附加参数 `--optimize=experimental-xforms-product-type` 。

此操作的先决条件包括：

- ** Xamarin.Forms 4.5 或更高版本**。 Xamarin.Forms 如果你的应用程序使用材料视觉对象，则需要4.6 或更高版本。
- **Xamarin 13.10.0.17 或更高版本**。 检查 [Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#version-information)的 Xamarin 版本。 Visual Studio for Mac dbms-guide-8.4.1 和 Visual Studio 16.4.3 中随附了此版本的 Xamarin。
- **删除对 `UIWebView` 的引用**。 你的代码不应引用任何 `UIWebView` 或任何使用的类 `UIWebView` 。

有关检测和删除引用的详细信息 `UIWebView` ，请参阅 [UIWebView 弃用](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)。

### <a name="configure-the-linker"></a>配置链接器

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

请按照以下步骤来删除 `UIWebView` 引用：

1. **打开 iOS 项目属性** &ndash; 右键单击 iOS 项目，然后选择 " **属性**"。
1. **导航到 "IOS 生成" 部分** &ndash; 选择 " **IOS 生成** " 部分。
1. **更新其他 mtouch 参数** &ndash; 在 " **其他 mtouch 参数** " 中，添加此标志 `--optimize=experimental-xforms-product-type` (除了) 中可能已存在的任何值。 注意：此标志与 **链接器行为** 仅设置为 " **SDK** " 或 " **全部链接**" 结合使用。 如果出于任何原因在将链接器行为设置为 "全部" 时发现错误，这很可能是应用程序代码或不是链接器安全的第三方库中的问题。 有关链接器的详细信息，请参阅 [链接 Xamarin IOS 应用](~/ios/deploy-test/linker.md)。
1. **更新所有生成配置** &ndash; 使用窗口顶部的 " **配置** " 和 " **平台** " 列表更新所有生成配置。 要更新的最重要的配置是 **Release/iPhone** 配置，因为这通常用于创建应用商店提交的版本。

在此屏幕截图中，可以看到带有新标志的窗口：

[![在 "iOS 生成" 部分中设置标志](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

请按照以下步骤来删除 `UIWebView` 引用：

1. **打开 iOS 项目选项** &ndash; 右键单击 iOS 项目，然后选择 " **选项**"。
1. **导航到 "IOS 生成" 部分** &ndash; 选择 " **IOS 生成** " 部分。
1. **更新_mtouch_ ** &ndash; **其他_mtouch_参数**中的其他 mtouch 参数，添加此标志 `--optimize=experimental-xforms-product-type` (除了) 中可能已存在的任何值。 注意：此标志与 **链接器行为** 仅设置为 " **SDK** " 或 " **全部链接**" 结合使用。 如果出于任何原因在将链接器行为设置为 "全部" 时发现错误，这很可能是应用程序代码或不是链接器安全的第三方库中的问题。 有关链接器的详细信息，请参阅 [链接 Xamarin IOS 应用](~/ios/deploy-test/linker.md)。
1. **更新所有生成配置** &ndash; 使用窗口顶部的 " **配置** " 和 " **平台** " 列表更新所有生成配置。 要更新的最重要的配置是 **Release/iPhone** 配置，因为这通常用于创建应用商店提交的版本。

在此屏幕截图中，可以看到带有新标志的窗口：

[![在 "iOS 生成" 部分中设置标志](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

现在，当你创建新的 (版本) 生成并将其提交到应用商店时，不应出现有关弃用的 API 的警告。

## <a name="related-links"></a>相关链接

- [使用 Web 视图 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web 视图 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [UIWebView 弃用](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)