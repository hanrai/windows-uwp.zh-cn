---
title: Texture3D 子资源平铺
description: 此表说明了 Texture3D 子资源的平铺方式。
ms.assetid: 210D03E4-CF12-47E0-BA2F-C8D059B17D3E
keywords:
- Texture3D 子资源平铺
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5b63fdeeffd4b95afab6556b6f0318732ff988b0
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370905"
---
# <a name="texture3d-subresource-tiling"></a>Texture3D 子资源平铺


此表说明了 [**Texture3D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture3d) 子资源的平铺方式。 此表中的值未计入尾部 mip 打包。

此表采用了 [**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d) 平铺，将 x/y 尺寸分别除以 4，再加上 16 层的深度值。 第一平面的所有磁贴（二维平面的磁贴限定前 16 层的深度值）先于后面的平面出现。

**注意**  流式资源中的   [**Texture3D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture3d) 支持在流式资源的初步实现中并不显现，但所需的磁贴形状（以后的版本可能支持）会列在此处。

 

| 位数/像素（每像素选择 1 个示例） | 磁贴尺寸（像素，宽 x 高 x 深） |
|-----------------------------|---------------------------------|
| 8                           | 64x32x32                        |
| 16                          | 32x32x32                        |
| 32                          | 32x32x16                        |
| 64                          | 32x16x16                        |
| 128                         | 16x16x16                        |
| BC1,4                       | 128x64x16                       |
| BC2,3,5,6,7                 | 64x64x16                        |

 

不支持流式处理资源的格式位计数是 96 bpp 格式，视频格式，DXGI\_格式\_R1\_UNORM、 DXGI\_格式\_R8G8\_B8G8\_UNORM，和 DXGI\_格式\_R8R8\_G8B8\_UNORM。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[如何平铺流式处理资源的区域](how-a-streaming-resource-s-area-is-tiled.md)

 

 




