---
ms.openlocfilehash: 5fe6bab32a70c8af03bdca2151312d44fbf3834e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91451539"
---
REST 请求是使用 HTTP 谓词通过 HTTP 发出的，该谓词与 Web 浏览器用于检索页面和将数据发送到服务器所使用的谓词相同。 在本练习中，你将创建一个使用 GET 谓词的类，以从 [OpenWeatherMap](https://openweathermap.org/) Web API 检索数据。 此 Web API 可用于检索指定位置的天气预报数据。 使用此 Web API 需要注册 API 密钥。

> [!div class="nextstepaction"]
> [注册 API 密钥](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在“解决方案资源管理器”的“WebServiceTutorial”项目中，向该项目添加名为 `Constants` 的新类   。 然后在“Constants.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    此代码定义了两个常量。 `OpenWeatherMapEndpoint` 常量定义了 Web 请求的终结点，`OpenWeatherMapAPIKey` 常量定义了 [OpenWeatherMap](https://openweathermap.org/) 服务的个人 API 密钥。

    > [!IMPORTANT]
    > 必须将个人 OpenWeatherMap API 密钥设置为 `OpenWeatherMapAPIKey` 常量的值。

1. 在“解决方案资源管理器”的“WebServicesTutorial”项目中，向该项目添加名为 `WeatherData` 的新类   。 然后在“WeatherData.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    此代码定义了四个类，用于对从 Web 服务检索的 JSON 数据进行建模。 每个属性都使用 `JsonProperty` 属性进行修饰，其中包含 JSON 字段名称。 在将 JSON 数据反序列化为模型对象时，Newtonsoft.Json 将使用此 JSON 字段名称映射到 CLR 属性。

    > [!NOTE]
    > 上面的类定义已经简化，并且没有对从 Web 服务检索到的 JSON 数据进行完全建模。 有关完整的数据模型示例，请参阅[天气应用 (Weather App)](/samples/xamarin/xamarin-forms-samples/weather/) 示例。

1. 在“解决方案资源管理器”的“WebServiceTutorial”项目中，向该项目添加名为 `RestService` 的新类   。 然后，在“RestService.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    此代码定义了一个方法 `GetWeatherDataAsync`，该方法从 [OpenWeatherMap](https://openweathermap.org/) Web API 检索指定位置的天气数据。 此方法使用 `HttpClient.GetAsync` 方法将 GET 请求发送到 `uri` 参数指定的 Web API。 Web API 发送存储在 `HttpResponseMessage` 对象中的响应。 响应包括 HTTP 状态代码，该代码指示 HTTP 请求是成功还是失败。 如果请求成功，则 Web API 将使用 HTTP 状态代码 200（正常）和 JSON 响应进行响应，该响应位于 `HttpResponseMessage.Content` 属性中。 在使用 `JsonConvert.DeserializeObject` 方法反序列化为 `WeatherData` 对象之前，将使用 `HttpContent.ReadAsStringAsync` 方法将此 JSON 数据读取到 `string`。 此方法使用 JSON 字段名称和 `WeatherData` 类中定义的 CLR 属性之间的映射来执行反序列化。

1. 生成解决方案，确保没有任何错误。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“Solution Pad”的“WebServiceTutorial”项目中，向该项目添加名为 `Constants` 的新类   。 然后在“Constants.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    此代码定义了两个常量。 `OpenWeatherMapEndpoint` 常量定义了 Web 请求的终结点，`OpenWeatherMapAPIKey` 常量定义了 [OpenWeatherMap](https://openweathermap.org/) 服务的个人 API 密钥。

    > [!IMPORTANT]
    > 必须将个人 OpenWeatherMap API 密钥设置为 `OpenWeatherMapAPIKey` 常量的值。

1. 在“Solution Pad”的“WebServicesTutorial”项目中，向该项目添加名为 `WeatherData` 的新类   。 然后在“WeatherData.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    此代码定义了四个类，用于对从 Web 服务检索的 JSON 数据进行建模。 每个属性都使用 `JsonProperty` 属性进行修饰，其中包含 JSON 字段名称。 在将 JSON 数据反序列化为模型对象时，Newtonsoft.Json 将使用此 JSON 字段名称映射到 CLR 属性。

    > [!NOTE]
    > 上面的类定义已经简化，并且没有对从 Web 服务检索到的 JSON 数据进行完全建模。 有关完整的数据模型示例，请参阅[天气应用 (Weather App)](/samples/xamarin/xamarin-forms-samples/weather/) 示例。

1. 在“Solution Pad”的“WebServiceTutorial”项目中，向该项目添加名为 `RestService` 的新类   。 然后，在“RestService.cs”中，删除所有模板代码并将其替换为以下代码  ：

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    此代码定义了一个方法 `GetWeatherDataAsync`，该方法从 [OpenWeatherMap](https://openweathermap.org/) Web API 检索指定位置的天气数据。 此方法使用 `HttpClient.GetAsync` 方法将 GET 请求发送到 `uri` 参数指定的 Web API。 Web API 发送存储在 `HttpResponseMessage` 对象中的响应。 响应包括 HTTP 状态代码，该代码指示 HTTP 请求是成功还是失败。 如果请求成功，则 Web API 将使用 HTTP 状态代码 200（正常）和 JSON 响应进行响应，该响应位于 `HttpResponseMessage.Content` 属性中。 在使用 `JsonConvert.DeserializeObject` 方法反序列化为 `WeatherData` 对象之前，将使用 `HttpContent.ReadAsStringAsync` 方法将此 JSON 数据读取到 `string`。 此方法使用 JSON 字段名称和 `WeatherData` 类中定义的 CLR 属性之间的映射来执行反序列化。

1. 生成解决方案，确保没有任何错误。