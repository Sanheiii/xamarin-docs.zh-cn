---
标题： "Android 生成错误– LinkAssemblies 任务意外失败"。主题：疑难解答： xamarin： xamarin assetid： EB3BE685-CB72-48E3-89D7-C845E76B9FA2 ms. 技术： xamarin 窗体作者： davidbritch： dabritch 毫秒。日期：03/07/2019 非 loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 生成错误– LinkAssemblies 任务意外失败

`The "LinkAssemblies" task failed unexpectedly`生成使用窗体的 Xamarin Android 项目时，可能会看到一条错误消息。 当链接器处于活动状态时（通常在*发布*版本上），会发生这种情况，以减小应用包的大小;这种情况发生是因为 Android 目标不会更新为最新框架。 （详细信息： [ Xamarin.Forms 支持的平台](~/get-started/supported-platforms.md#android-platform-support)）

此问题的解决方法是确保具有最新的受支持的 Android SDK 版本，并将**目标框架**设置为最新安装的平台。 此外，建议将**目标 Android 版本**设置为最新安装的平台，将**最低 android 版本**设置为 API 19 或更高版本。 这被视为受支持的配置。

## <a name="setting-in-visual-studio-for-mac"></a>Visual Studio for Mac 中的设置

1. 右键单击 Android 项目，然后选择菜单中的 "**选项**"。
2. 在 "**项目选项**" 对话框中，找到 "**生成 > 常规**"。
3. 将 "**使用 Android 版本编译：（目标框架）** " 设置为最新安装的平台。
4. 在 "**项目选项**" 对话框中，找到 "**生成 > Android 应用程序**"。
5. 将**最低 android 版本**设置为 API 级别19或更高，将**目标 android 版本**设置为你在中选择的最新安装平台（3）。

## <a name="setting-in-visual-studio"></a>Visual Studio 中的设置

1. 右键单击 Android 项目，然后在菜单中选择 "**属性**"。
2. 在项目属性中，请参阅**应用程序**。
3. 将 "**使用 Android 版本编译：（目标框架）** " 设置为最新安装的平台。
4. 在项目属性中，切换到 " **Android 清单**"。
5. 将**最低 android 版本**设置为 API 级别19或更高，将**目标 android 版本**设置为你在中选择的最新安装平台（3）。

更新这些设置后，请清除并重新生成项目，以确保选择所做的更改。
