---
description: 使用当前的 Mac 计算机开发 Windows 应用。
title: 在 Mac 上设置 Windows 10
ms.assetid: 6D520610-5DE0-476E-A792-AA57E002D309
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5bdd090b380160952dfbb8b5f04b95b6c84b75b7
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322345"
---
# <a name="setting-up-your-mac-with-windows-10"></a>在 Mac 上设置 Windows 10


使用当前的 Mac 计算机开发 Windows 应用。

## <a name="run-windows-on-your-mac-and-use-visual-studio"></a>在 Mac 上运行 Windows 并使用 Visual Studio

准备好开始开发通用 Windows 应用，却没有一台随手可用的电脑？ 没关系 - 你可以使用 Mac！ Apple Boot Camp、 根据 Oracle VirtualBox、 VMware Fusion 和 Parallels Desktop 等热门第三方解决方案，可以在 Apple 计算机上安装 Windows 10 和 Microsoft Visual Studio。

**请注意**  需要将磁盘或 USB 闪存驱动器上的 Windows 10 可启动映像。 如果你是 MSDN 订户，可以从 MSDN 订户下载中心下载安装映像。 如果不是订户，安装程序是否可以从购买[Microsoft Store](https://www.microsoft.com/store/apps)。 也可以从[此位置](https://go.microsoft.com/fwlink/?LinkId=623906)下载安装程序，这在你已运行 Windows 并希望升级时很有用。

Windows 运行后，可以安装最新版本的 Visual Studio 中从[开发人员下载适用于 Windows 10](https://developer.microsoft.com/en-us/windows/downloads)并开始编写应用程序 ！

**请注意**  如果你打算使用 Visual Studio 设备模拟器，您**必须**安装 64 位 (x64) 版本的 Windows 10 专业版或更好。 遗憾的是，某些较旧的 Mac 电脑无法运行 64 位 Windows。 请在此 [Apple 支持页面](https://go.microsoft.com/fwlink/p/?LinkID=397959)上与 Apple 联系以核实你的硬件是否兼容。

## <a name="apple-boot-camp"></a>Apple Boot Camp

Boot Camp 助手应用是预装在每个新的 Mac 上，启动它将引导您完成安装 Windows 10 的过程。 你只需要一个 Windows 的副本（来自上面列出的源）和至少 30 GB 的可用磁盘空间。 安装后，你可以选择启动到 Mac OSX 或 Windows 10 中。 有关详细信息，请参阅 Apple 的 [Boot Camp 说明页面](https://go.microsoft.com/fwlink/?LinkId=623912)。

## <a name="parallels-desktop"></a>Parallels Desktop

使用 Parallels Desktop 11，可以将包括 Visual Studio 和 Cortana 在内的 Windows 应用与现有的 Mac 应用程序并列运行。 专业版可用于开发人员，它具有一些额外功能，其中包括改进的调试以及对 Docker 和 Jenkins 的支持。 有关详细信息和免费试用版，请参阅 [Parallels Desktop](https://go.microsoft.com/fwlink/p/?LinkId=281827)。

## <a name="vmware-fusion"></a>VMWare Fusion

来自 VMWare 的 Fusion 8 可以使你在 Mac 桌面上直接运行 Visual Studio。 专业版可用于为开发人员提供一些更高级的功能，如 vSphere 支持 有关详细信息和免费试用版，请参阅 [VMWare Fusion](https://go.microsoft.com/fwlink/p/?LinkId=281826)。

## <a name="oracle-virtualbox"></a>Oracle VirtualBox

VirtualBox 是一款用于在计算机上运行虚拟机的免费应用程序，它支持在 Mac 上运行 Windows。 它是只提供基本服务的选项，但是价格很具有吸引力。 有关详细信息，请参阅 [VirtualBox](https://go.microsoft.com/fwlink/p/?LinkId=280599)。

