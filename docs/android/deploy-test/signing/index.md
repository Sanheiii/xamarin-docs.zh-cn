---
title: 对 Android 应用程序包进行签名
description: 如何对 Android 应用程序包 (APK) 进行签名以便发布
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/02/2018
ms.openlocfilehash: 44003e55ee88db727c26def56b3363dbf6c982e9
ms.sourcegitcommit: 124d845f8d2768353e8b7fe1ab1d959a589367f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872272"
---
# <a name="signing-the-android-application-package"></a>对 Android 应用程序包进行签名

在[做好应用程序发布准备](~/android/deploy-test/release-prep/index.md)中，使用了“存档管理器”以生成应用并将它放置在存档中以进行签名和发布。 此部分说明如何创建 Android 签名标识、为 Android 应用程序创建新签名证书以及将存档应用即席发布到磁盘。 生成的 APK 可以旁加载到 Android 设备中，而无需经过应用商店。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在[“存档以供发布”](~/android/deploy-test/release-prep/index.md#archive)中，“分发通道”对话框显示两个分发选项。 选择“即席”：

[![“分发通道”对话框](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在[存档以便进行发布](~/android/deploy-test/release-prep/index.md#archive)中，“签名和分发...”对话框向我们提供了两种分发选择。 选择“即席”，然后单击“下一步”：

[![“签名和分发”对话框](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs"></a>
<a name="newcert"></a>
<a name="newcertxs"></a>

## <a name="create-a-new-certificate"></a>创建新证书

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

选择“即席”之后，Visual Studio 会打开对话框的“签名标识”页，如下一个屏幕截图所示。 若要发布 .APK，必须首先使用签名密钥（也称为证书）对它进行签名。

可以通过单击“导入”按钮，然后前进到[对 APK 进行签名](#sign-the-apk)，来使用现有证书。 否则，单击“+”按钮创建新证书：

[![临时签名标识](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

“创建 Android 密钥存储库”对话框显示；使用此对话框来新建可用于对 Android 应用进行签名的签名证书。 输入所需信息（具有红色边框），如此对话框中所示：

[![“创建 Android 密钥存储”对话框](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

下面的示例说明必须提供的信息的种类。 单击“创建”以创建新证书：

[![创建新的证书](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

生成的密钥存储位于以下位置：

C:\\Users\\USERNAME\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\ALIAS\\ALIAS.keystore  

例如，使用“chimp”作为别名，以上步骤会在以下位置创建新签名密钥：

C:\\Users\\USERNAME\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore

> [!IMPORTANT]
> 默认情况下，“AppData”文件夹是隐藏的，你可能需要取消隐藏它来访问它。
>
> 此外，请务必将生成的密钥存储库文件和密码备份在安全的位置 &ndash; 它不包含在解决方案中。 如果密钥存储文件（例如，因为移动到另一台计算机或重新安装了 Windows）丢失，将无法使用与以前版本相同的证书对应用签名。

有关密钥存储的详细信息，请参阅[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

单击“即席”之后，Visual Studio for Mac 会打开“Android 签名标识”对话框，如下一个屏幕截图所示。 若要发布 .APK，必须首先使用签名密钥（也称为证书）对它进行签名。 如果证书已存在，则单击“导入现有密钥”按钮以导入它，然后前进到[对 APK 进行签名](#sign-the-apk)。否则，单击“创建新密钥”按钮以创建新证书：

[![“Android 签名标识”对话框](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

“创建新证书”对话框用于创建可以用于对 Android 应用程序进行签名的新签名证书。 输入所需信息之后，单击“确定”：

[![“新建证书”对话框](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

生成的密钥存储位于以下位置：

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

例如，以上步骤可能会在以下位置创建新签名密钥：

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

> [!NOTE]
> 请确保将生成的密钥存储文件和密码备份在安全的位置 &ndash; 它不包含在解决方案中。 如果密钥存储文件丢失（例如，因为移动到另一台计算机或重新安装了 macOS），将无法使用与以前版本相同的证书对应用签名。

有关密钥存储的详细信息，请参阅[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)。

-----

## <a name="sign-the-apk"></a>对 APK 进行签名

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

单击“创建”时，新密钥存储（包含新证书）会进行保存并在“签名标识”下列出，如下一个屏幕截图所示。 若要在 Google Play 上发布应用，请单击“取消”并转至[发布到 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)。
若要即席发布，请选择要用于签名的签名标识并单击“另存为”以发布应用以用于独立分发。 例如，在此屏幕截图中选择了 **chimp** 签名标识（在前面创建）：

[![签名标识示例](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

接下来，**存档管理器**会显示发布进度。 发布过程完成时，“另存为”对话框会打开，要求提供要在其中存储生成的 .APK 文件的位置：

[![“另存为”对话框](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

导航到所需位置并单击“保存”。 如果密钥密码未知，则“签名密码”对话框会出现，提示输入所选证书的密码：

[![“签名密码”对话框](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

签名过程完成之后，单击“打开分发”：

[![打开“分发”按钮](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

这会使 Windows 资源管理器打开包含生成的 APK 文件的文件夹。 此时，Visual Studio 已将 Xamarin.Android 应用程序编译为准备好进行分发的 APK。
下面的屏幕截图显示准备好进行发布的应用 (**MyApp.MyApp.apk**) 的示例：

[![Windows 资源管理器中显示的 APK](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

如此处所示，一个新证书已添加到密钥存储中。 若要在 Google Play 上发布应用，请单击“取消”并转至[发布到 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)。
否则，单击“下一步”以即席发布应用（对于独立分发），如此示例所示：

[![“签名和分发”对话框](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

“即席发布”对话框会在发布之前提供已签名应用的摘要。 如果此信息正确，则单击“发布”。

[![“临时发布”对话框](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

“输出 APK 文件”对话框会将 APK 保存到指定路径。 单击“保存” 。

![“输出 APK 文件”对话框](images/xs/06-output-apk-file.png)

接下来，为证书输入密码（在“创建新证书”对话框中使用的密码），然后单击“确定”：

![输入证书密码](images/xs/07-signing-certificate.png)

APK 会使用证书进行签名并保存到指定位置。 单击“在查找器中展现”：

[![“发布成功”对话框](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

这会将查找器打开到已签名 APK 文件的位置：

[![查找器中显示的 APK](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

APK 已准备好从查找器进行复制并发送到其最终目标。 最好在分发之前将 APK 安装在 Android 设备上并进行试用。 有关发布“临时”APK 的详细信息，请参阅[独立发布](~/android/deploy-test/publishing/publishing-independently.md)。

-----

## <a name="next-steps"></a>后续步骤

对要发行的应用程序包进行签名之后，必须发布它。 以下各部分介绍用于发布应用程序的几种方法。


## <a name="related-links"></a>相关链接

- [Android 生成密钥存储](https://developer.android.com/studio/publish/app-signing#generate-key)
