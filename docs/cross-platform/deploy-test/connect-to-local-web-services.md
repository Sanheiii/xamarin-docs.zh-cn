---
title: 从 iOS 模拟器和 Android 模拟器连接到本地 Web 服务
description: 本文介绍了在 iOS 模拟器或 Android 模拟器中运行的 Xamarin 移动应用程序如何使用在本地运行的 ASP.NET Core Web 服务。
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
ms.openlocfilehash: 9520f4e81403869542cf758627b8e3cfc9fabdd8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457973"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>从 iOS 模拟器和 Android 模拟器连接到本地 Web 服务

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

许多移动应用程序使用 Web 服务。 在开发阶段，在本地部署 Web 服务和从 iOS 模拟器或 Android 模拟器中运行的移动应用程序中使用它是很常见的操作。 这可避免向托管的终结点部署 Web 服务并可直接进行调试体验，因为移动应用程序和 Web 服务都是在本地运行的。

在 iOS 模拟器或 Android 模拟器中运行的移动应用程序可以使用在本地运行并通过 HTTP 公开的 ASP.NET Core Web 服务，如下所示：

- 在 iOS 模拟器中运行的应用程序可以通过你的计算机 IP 地址或通过 `localhost` 主机名连接到本地 HTTP Web 服务。 例如，对于通过 `/api/todoitems/` 相对 URI 公开 GET 操作的本地 HTTP Web 服务，在 iOS 模拟器中运行的应用程序可以通过向 `http://localhost:<port>/api/todoitems/` 发送 GET 请求来使用操作。
- 在 Android 模拟器中运行的应用程序可以通过 `10.0.2.2` 地址连接到本地 HTTP Web 服务，该地址是你的主机环回接口的别名（在开发计算机上为 `127.0.0.1`）。 例如，对于通过 `/api/todoitems/` 相对 URI 公开 GET 操作的本地 HTTP Web 服务，在 Android 模拟器中运行的应用程序可以通过向 `http://10.0.2.2:<port>/api/todoitems/` 发送 GET 请求来使用操作。

但是，若要使在 iOS 模拟器或 Android 模拟器上运行的应用程序能够使用通过 HTTP 公开的本地 Web 服务，还需要执行额外的步骤。 对于此方案，流程如下：

1. 在你的计算机上创建一个自签名的开发证书。 有关详细信息，请参阅[创建开发证书](#create-a-development-certificate)。
1. 配置项目，以便将合适的 `HttpClient` 网络堆栈用于调试版本。 有关详细信息，请参阅[配置项目](#configure-your-project)。
1. 指定本地计算机的地址。 有关详细信息，请参阅[指定本地计算机地址](#specify-the-local-machine-address)。
1. 绕过本地开发证书安全检查。 有关详细信息，请参阅[绕过证书安全检查](#bypass-the-certificate-security-check)。

将依次讨论每项。

## <a name="create-a-development-certificate"></a>创建开发证书

安装 .NET Core SDK 会将 ASP.NET Core HTTPS 开发证书安装到本地用户证书存储。 但是，尽管证书已安装，但它不受信任。 若要信任该证书，请执行以下一次性步骤，以运行 dotnet `dev-certs` 工具：

```dotnetcli
dotnet dev-certs https --trust
```

下面的命令提供有关 `dev-certs` 工具的帮助：

```dotnetcli
dotnet dev-certs https --help
```

或者，当你运行使用 HTTPS 的 ASP.NET Core 2.1 项目（或更高版本）时，Visual Studio 将检测是否缺少开发证书并提供安装和信任。

> [!NOTE]
> ASP.NET Core HTTPS 开发证书是自签名证书。

有关在计算机上启用本地 HTTPS 的详细信息，请参阅[启用本地 HTTPS](/aspnet/core/getting-started#enable-local-https)。

## <a name="configure-your-project"></a>配置项目

在 iOS 和 Android 上运行的 Xamarin 应用程序可以指定 `HttpClient` 类使用的是哪个网络堆栈，可选择托管网络堆栈或本机网络堆栈。 托管堆栈提供与现有 .NET 代码的高级别兼容性，但限于 TLS 1.0 且速度可能更慢并导致更大的可执行文件大小。 本机堆栈运行速度会更快且可以提供更高的安全性，但可能并不会提供 `HttpClient` 类的全部功能。

### <a name="ios"></a>iOS

在 iOS 上运行的 Xamarin 应用程序可以使用托管网络堆栈，或使用本机 `CFNetwork` 或 `NSUrlSession` 网络堆栈。 默认情况下，新 iOS 平台项目使用 `NSUrlSession` 网络堆栈，以支持 TLS 1.2，并使用本机 API，以便提高性能并减小可执行文件大小。 有关详细信息，请参阅 [iOS/macOS 的 HttpClient 和 SSL/TLS 实现选择器](~/cross-platform/macios/http-stack.md)。

### <a name="android"></a>Android

在 Android 上运行的 Xamarin 应用程序可以使用托管 `HttpClient` 网络堆栈，或使用本机 `AndroidClientHandler` 网络堆栈。 默认情况下，新 Android 平台项目使用 `AndroidClientHandler` 网络堆栈，以支持 TLS 1.2，并使用本机 API，以便提高性能并减小可执行文件大小。 有关 Android 网络堆栈的详细信息，请参阅 [Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器](~/android/app-fundamentals/http-stack.md)。

## <a name="specify-the-local-machine-address"></a>指定本地计算机地址

iOS 模拟器和 Android 模拟器均提供对本地计算机上运行的安全 Web 服务的访问。 但它们的本地计算机地址各不相同。

### <a name="ios"></a>iOS

iOS 模拟器使用主机网络。 因此，在此模拟器中运行的应用程序可以通过计算机 IP 地址或通过 `localhost` 主机名连接到在本地计算机中运行的 Web 服务。 例如，对于通过 `/api/todoitems/` 相对 URI 公开 GET 操作的本地安全 Web 服务，在 iOS 模拟器中运行的应用程序可以通过向 `https://localhost:<port>/api/todoitems/` 发送 GET 请求来使用操作。

> [!NOTE]
> 从 Windows 运行 iOS 模拟器中的移动应用程序时，此应用程序将显示在[用于 Windows 的远程 iOS 模拟器](~/tools/ios-simulator/index.md)中。 但是，此应用程序会在配对的 Mac 上运行。 因此，在 Mac 上运行的 iOS应用程序无法对在 Windows 中运行的 Web 服务进行本地主机访问。

### <a name="android"></a>Android

Android模拟器的各个实例独立于开发计算机网络接口，在虚拟路由器的后方运行。 因此，模拟设备无法看到开发计算机或网络中的其他模拟器实例。

但是，各个模拟器的虚拟路由器托管着一个包含预分配地址的特殊网络空间，其中 `10.0.2.2` 地址是主机环回接口的别名（在开发计算机上为 127.0.0.1）。 因此，对于通过 `/api/todoitems/` 相对 URI 公开 GET 操作的本地安全 Web 服务，在 Android 模拟器中运行的应用程序可以通过向 `https://10.0.2.2:<port>/api/todoitems/` 发送 GET 请求来使用操作。

### <a name="xamarinforms-example"></a>Xamarin.Forms 示例

在 Xamarin.Forms 应用程序中，[`Device`](xref:Xamarin.Forms.Device) 类可用于检测应用程序的运行平台。 可将支持访问本地安全 W eb 服务的相应主机名设置如下：

```csharp
public static string BaseAddress =
    Device.RuntimePlatform == Device.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

## <a name="bypass-the-certificate-security-check"></a>绕过证书安全检查

如果试图从在 iOS 模拟器或 Android 模拟器中运行的应用程序调用本地安全 Web 服务，即使在各个平台上使用的是托管网络堆栈，也会引发 `HttpRequestException`。 因为本地 HTTPS 开发证书是自签名证书，而iOS 或 Android 不信任自签名证书。 因此，当应用程序使用本地安全 Web 服务时，需忽略 SSL 错误。 在 iOS 和 Android 上使用托管和本机网络堆栈时，这可以通过以下操作实现：将 `HttpClientHandler` 对象上的 `ServerCertificateCustomValidationCallback` 属性设置为忽略对本地 HTTPS 开发证书的证书安全检查结果的回调：

```csharp
// This method must be in a class in a platform project, even if
// the HttpClient object is constructed in a shared project.
public HttpClientHandler GetInsecureHandler()
{
    HttpClientHandler handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

在此代码示例中，如果待验证的证书不是 `localhost` 证书，会返回服务器证书验证结果。 如果是此证书，将忽略验证结果，并返回 `true`，以指示证书有效。 生成的 `HttpClientHandler` 对象应作为参数传递到调试版本的 `HttpClient` 构造函数：

```csharp
#if DEBUG
    HttpClientHandler insecureHandler = GetInsecureHandler();
    HttpClient client = new HttpClient(insecureHandler);
#else
    HttpClient client = new HttpClient();
#endif
```

## <a name="related-links"></a>相关链接

- [TodoREST（示例）](/samples/xamarin/xamarin-forms-samples/webservices-todorest/)
- [启用本地 HTTPS](/aspnet/core/getting-started#enable-local-https)
- [iOS/macOS 的 HttpClient 和 SSL/TLS 实现选择器](~/cross-platform/macios/http-stack.md)
- [Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器](~/android/app-fundamentals/http-stack.md)