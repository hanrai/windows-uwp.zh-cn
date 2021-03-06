---
title: 连接的应用和设备 (Project Rome)
description: 本节介绍如何使用远程系统平台发现远程设备、在远程设备上启动应用，以及与远程设备上的应用服务通信。
ms.date: 06/08/2018
ms.topic: article
keywords: windows 10，uwp，已连接设备、 远程系统、 罗马、 项目罗马
ms.assetid: 7f39d080-1fff-478c-8c51-526472c1326a
ms.localizationpriority: medium
ms.openlocfilehash: ae9229378f75adeb215a881bdaf955b010cd7806
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366375"
---
# <a name="connected-apps-and-devices-project-rome"></a>连接的应用和设备 (Project Rome)

本部分介绍如何将应用连接跨设备和使用的平台[项目罗马](https://developer.microsoft.com/en-us/windows/project-rome)。 若要了解如何在跨平台方案中实现项目罗马，请访问[主文档页的项目罗马](https://docs.microsoft.com/en-us/windows/project-rome/)。

大多数用户拥有多台设备，并且通常在一台设备上开始某项活动，而在其他设备上结束该活动。 为了满足此要求，应用需要横跨设备和平台。 项目罗马，可发现远程设备、 启动远程设备上的应用和与远程设备上的应用服务进行通信。

Windows 10 版本 1607 中引入了[远程系统 API](https://docs.microsoft.com/uwp/api/Windows.System.RemoteSystems)，可使你编写允许用户在一台设备上开始某项任务而在另一台设备上结束该任务的应用。 将集中处理该任务，用户可在最方便的设备上执行工作。 例如，在车上用户可能会收听手机上的收音机，但当回到家中，他们可能会想将播放转到连接家庭立体声系统的 Xbox One。

还可将 Project Rome 用于配套设备或远程控制方案。 使用应用服务消息处理 API 在两台设备之间创建应用通道，以发送和接收自定义消息。 例如，可为手机编写控制电视播放的应用，或者编写提供关于通过其他应用观看的电视节目人物信息的伴侣应用。  

设备可通过蓝牙和无线在近处连接，也可通过云远程连接；它们通过使用设备的用户的 Microsoft 帐户 (MSA) 链接。

请参阅[远程系统 UWP 示例](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/RemoteSystems )获取如何发现远程系统、在远程系统上启动应用，以及使用应用服务在运行在两个系统上的应用之间发送消息的示例。

有关 Project Rome 的一般详细信息（包括跨平台集成资源），请转到 [aka.ms/project-rome](https://aka.ms/project-rome)。

| 主题 | 描述 |
|-------|-------------|
| [启动远程设备上的应用](launch-a-remote-app.md) | 了解如何启动远程设备上的应用。 本主题介绍最简单的用例和初步设置。  |
| [发现远程设备](discover-remote-devices.md)  | 了解如何发现可以连接的设备。 |
| [与远程应用服务进行通信](communicate-with-a-remote-app-service.md) | 了解如何与远程设备上的应用交互。 |
| [通过远程会话连接设备](remote-sessions.md) | 通过在远程会话中加入多个设备来创建跨多个设备的共享体验。 |
| [即便跨设备，也继续用户活动](useractivities.md)| 帮助用户恢复他们在做什么在应用中，即使跨多个设备。|
| [用户活动最佳实践](useractivities-best-practices.md)| 了解有关创建和更新用户活动的建议的做法。|
