---
title: “部署”复选框在 Configuration Manager 中处于禁用状态
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: c3d0b8f2da971238b98253405f3b8fe08699ab56
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997210"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>“部署”复选框在 Configuration Manager 中处于禁用状态

从 Xamarin 3.5 起，无论何时按“开始”**** 工具栏按钮或选择“调试”>“开始调试”**** 菜单项，Xamarin.iOS 项目都会自动进行部署。 在运行上述任一命令之前，仍需要将所需的 Xamarin 应用项目设置为**启动项目**。

因此，会在 Visual Studio Configuration Manager for Xamarin.iOS 项目中故意禁用“部署”**** 复选框：

![Visual Studio Configuration Manager 在 Xamarin 3.5 中显示为 Xamarin iOS 项目禁用的 "部署" 复选框](deploy-checkboxes-images/configuration.png)

此更改可消除在较旧版本的 Xamarin（版本 3.3 及更早版本）中，当 Xamarin.iOS 应用项目尚未设为待部署时，可能出现的错误：

![错误对话框：需要先部署项目 iPhoneApp1，然后才能启动。 验证项目在解决方案配置管理器中已选定为“待部署”。](deploy-checkboxes-images/error.png)
