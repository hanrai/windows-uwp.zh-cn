---
title: 使用流式资源时，模板格式不受支持
description: 使用流式资源时，包含模板的格式不受支持。
ms.assetid: 90A572A4-3C76-4795-BAE9-FCC72B5F07AD
keywords:
- 使用流式资源时，模板格式不受支持
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 1d35813a6242abd555e87329c25a413285d1d948
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660982"
---
# <a name="stencil-formats-not-supported-with-streaming-resources"></a>使用流式资源时，模板格式不受支持


使用流式资源时，包含模板的格式不受支持。

包含模板的格式包括 DXGI\_格式\_D24\_UNORM\_S8\_UINT （及相关 R24G8 系列中的格式） 和 DXGI\_格式\_D32\_浮动\_S8X24\_UINT （及相关 R32G8X24 系列中的格式）。

某些实现以独立分配方式存储深度和模板，而另一些实现将两者存储在一起。 针对两种方案的磁贴管理必须有所不同，并且任何单个 API 均不可抽象差异或使差异合理化。 我们建议使用未来的硬件，以支持独立深度和模板表面（均独立平铺）。

32 位深度磁贴大小为 128 x 128，8 位模板磁贴大小为 256 x 256。 因此，应用必须适应深度和模板之间的磁贴形状错位。 但不同的呈现器目标表面格式已经存在同样的问题。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[流式处理资源的跨进程和设备共享](streaming-resource-cross-process-and-device-sharing.md)

 

 




