---
description: 了解移动和绘制命令（小型语言），可用于将路径几何图形指定为 XAML 属性值。
title: 移动和绘制命令语法
ms.assetid: 7772BC3E-A631-46FF-9940-3DD5B9D0E0D9
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 40b959feed09546791840dafe15ab98d65f0ea09
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371157"
---
# <a name="move-and-draw-commands-syntax"></a>移动和绘制命令语法


了解移动和绘制命令（小型语言），可用于将路径几何图形指定为 XAML 属性值。 移动和绘制命令由许多设计和图形工具使用，用于按序列化和交换格式输出矢量图形或形状。

## <a name="properties-that-use-move-and-draw-command-strings"></a>使用移动和绘制命令字符串的属性

移动和绘制命令语法受 XAML 的内部类型转换器支持，可分析命令并生成运行时图形表示形式。 此表示形式基本上是一组用于演示的完成矢量。 矢量本身不会完成表示细节；你仍然需要在元素上设置其他值。 对于 [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) 对象，你还需要适用于 [**Fill**](/uwp/api/Windows.UI.Xaml.Shapes.Shape.Fill)、[**Stroke**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.shape.stroke) 和其他属性的值，然后 **Path** 必须通过某种途径连接到可视化树。 对于 [**PathIcon**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PathIcon) 对象，设置 [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.iconelement.foreground) 属性。

Windows 运行时，可以使用一个字符串，表示移动和绘制命令中有两个属性：[**Path.Data** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data)并[ **PathIcon.Data**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon.data)。 如果你通过指定移动和绘制命令设置其中一个属性，通常会将其设置为 XAML 属性值以及该元素的其他所需属性。 在未获取细节的情况下，如此处所示：

```xml
<Path x:Name="Arrow" Fill="White" Height="11" Width="9.67"
  Data="M4.12,0 L9.67,5.47 L4.12,10.94 L0,10.88 L5.56,5.47 L0,0.06" />
```

[**PathGeometry.Figures** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.pathgeometry.figures)还可以使用移动和绘制命令。 你可以将使用移动和绘制命令的 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 对象与 [**GeometryGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GeometryGroup) 对象中的其他 [**Geometry**](/uwp/api/Windows.UI.Xaml.Media.Geometry) 类型结合起来，然后将其用作 [**Path.Data**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data) 的值。 但是此方法与使用属性定义的数据的移动和绘制命令相比并不常用。

## <a name="using-move-and-draw-commands-versus-using-a-pathgeometry"></a>使用移动和绘制命令与使用 **PathGeometry**

对于 Windows 运行时 XAML，移动和绘制命令将生成包含 [**Figures**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.pathgeometry.figures) 属性值的单个 [**PathFigure**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathFigure) 对象的 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry)。 每个绘制命令都将在此单个 **PathFigure** 的 [**Segments**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.pathfigure.segments) 集合中生成一个 [**PathSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathSegment) 派生类，移动命令将更改 [**StartPoint**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.pathfigure.startpoint)，并且存在的关闭命令可将 [**IsClosed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.pathfigure.isclosed) 设置为 **true**。 如果你在运行时检查 **Data** 值，则可以将此结构作为对象模型导航。

## <a name="the-basic-syntax"></a>基本语法

移动和绘制命令的语法可总结如下：

1.  以可选填充规则开始。 通常仅在不希望 **EvenOdd** 默认时指定它。 （稍后将详细介绍 **EvenOdd**。）
2.  具体指定一个移动命令。
3.  指定一个或多个绘制命令。
4.  指定一个关闭命令。 你可以省略关闭命令，但是这会使你的图像处于打开状态（这不常见）。

此语法的一般规则如下：

-   每个命令都由一个明确的字母表示。
-   该字母可以大写，也可以小写。 按照我们的说明注意大小写。
-   除关闭命令之外的每个命令通常都后跟一个或多个数字。
-   如果命令中具有多个数字，则使用逗号或空格进行分隔。

**\[** _fillRule_ **\]** _moveCommand_ _drawCommand_ **\[** _drawCommand_ **\*\]** **\[** _closeCommand_ **\]**

许多绘制命令都使用你为其提供 _x,y_ 值的点。 只要看到 \*_点_ 占位符可以假定您将提供两个十进制值 _x，y_ 点值。

如果结果清晰，空白区域通常可以忽略。 事实上，如果你对所有数字集（点和大小）使用逗号作为分隔符，则可以忽略所有空白区域。 例如，下面的用法是合法的：`F1M0,58L2,56L6,60L13,51L15,53L6,64z`。 但是包括命令之间的空白区域以获取清晰度是更为典型的用法。

不要将逗号用作十进制数字的小数点；命令字符串由 XAML 解释，并且不会说明特定于文化的数字格式约定，该约定与 **en-us** 当地使用的约定不同。

## <a name="syntax-specifics"></a>语法细节

**填充规则**

有两个可能值为可选填充规则：**F0**或**F1**。 ( **F**始终采用大写形式。)**F0**是默认值; 它会生成**EvenOdd**填充行为，因此通常不指定它。 使用 **F1** 获取 **Nonzero** 填充行为。 这些填充值与 [**FillRule**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.FillRule) 枚举的值一致。

**移动命令**

指定新图形的起点。

| 语法 |
|--------|
| `M ` _startPoint_ <br/>- 或 -<br/>`m` _startPoint_|

| 术语 | 描述 |
|------|-------------|
| _startPoint_ | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/>新图形的起点。|

大写 **M** 指示 *startPoint* 是绝对坐标；小写 **m** 指示 *startPoint* 是上一个点的偏移或 (0,0)（如果没有上一个点）。

**请注意**  是合法指定多个点之后移动命令。 向这些点绘制一条直线，就像你指定了直线命令一样。 但是这不是建议的样式；请改为使用专门的直线命令。

**绘制命令**

绘制命令可由多个形状命令组成：线、横线、竖线、三次方贝塞尔曲线、二次方贝塞尔曲线、平滑三次方贝塞尔曲线、平滑二次方贝塞尔曲线和椭圆弧。

对于所有绘制命令，区分大小写。 大写字母指示绝对坐标，小写字母指示相对于上一个命令的坐标。

分段的控制点相对于前一段的端点。 当连续输入了同一类型的多个命令时，你可以忽略重复的命令输入。 例如，`L 100,200 300,400` 与 `L 100,200 L 300,400` 等效。

**行命令**

在当前点和指定的端点之间创建一条直线。 `l 20 30` 和`L 20,30`是有效的行命令的示例。 定义 [**LineGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LineGeometry) 对象的等效对象。

| 语法 |
|--------|
| `L` _endPoint_ <br/>- 或 -<br/>`l` _endPoint_ |

| 术语 | 描述 |
|------|-------------|
| endPoint | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)<br/>直线的端点。|

**水平线命令**

在当前点和指定的 x 坐标之间创建一条横线。 `H 90` 是有效水平线命令的示例。

| 语法 |
|--------|
| `H ` _x_ <br/> - 或 - <br/>`h ` _x_ |

| 术语 | 描述 |
|------|-------------|
| x | [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) <br/> 直线端点的 x 坐标。 |

**竖线命令**

在当前点和指定的 y 坐标之间创建一条竖线。 `v 90` 是有效竖线命令的示例。

| 语法 |
|--------|
| `V ` _y_ <br/> - 或 - <br/> `v ` _y_ |

| 术语 | 描述 |
|------|-------------|
| *y* | [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) <br/> 直线端点的 y 坐标。 |

**三次方贝塞尔曲线命令**

通过使用两个指定的控制点（*controlPoint1* 和 *controlPoint2*）在当前点和指定的端点之间创建一条三次方贝塞尔曲线。 `C 100,200 200,400 300,200` 是有效曲线命令的示例。 使用 [**BezierSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.BezierSegment) 对象定义 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 对象的等效对象。

| 语法 |
|--------|
| `C ` *controlPoint1* *controlPoint2* *endPoint* <br/> - 或 - <br/> `c ` *controlPoint1* *controlPoint2* *endPoint* |

| 术语 | 描述 |
|------|-------------|
| *controlPoint1* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 曲线的第一个控制点，可确定曲线的开始切线。 |
| *controlPoint2* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 曲线的第二个控制点，可确定曲线的结束切线。 |
| *endPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 要绘制曲线的点。 | 

**二次贝塞尔曲线命令**

通过使用指定的控制点 (*controlPoint*) 在当前点和指定的端点之间创建一条二次方贝塞尔曲线。 `q 100,200 300,200` 是有效的二次贝塞尔曲线命令的示例。 使用 [**QuadraticBezierSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.QuadraticBezierSegment) 定义 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 的等效对象。

| 语法 |
|--------|
| `Q ` *控制点终结点* <br/> - 或 - <br/> `q ` *控制点终结点* |

| 术语 | 描述 |
|------|-------------|
| *controlPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 曲线的控制点，可确定曲线的开始切线和结束切线。 |
| *endPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)<br/> 要绘制曲线的点。 |

**平滑三次方贝塞尔曲线命令**

在当前点和指定的端点之间创建一条三次方贝塞尔曲线。 第一个控制点被假设为相对于当前点的上一个命令的第二个控制点的反射。 如果没有上一个命令或者如果上一个命令不是三次方贝塞尔曲线命令或平滑三次方贝塞尔曲线命令，则假设第一个控制点与当前点一致。 第二个控制点（曲线末端的控制点）由 *controlPoint2* 指定。 例如，`S 100,200 200,300` 是有效的平滑三次方贝塞尔曲线命令。 此命令使用 [**BezierSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.BezierSegment) 定义 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 的等效对象，其中存在之前的曲线段。

| 语法 |
|--------|
| `S` *controlPoint2* *endPoint* <br/> - 或 - <br/>`s` *controlPoint2 终结点* |

| 术语 | 描述 |
|------|-------------|
| *controlPoint2* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 曲线的控制点，可确定曲线的结束切线。 |
| *endPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)<br/> 要绘制曲线的点。 |

**平滑二次贝塞尔曲线命令**

在当前点和指定的端点之间创建一条二次方贝塞尔曲线。 该控制点被假设为相对于当前点的上一个命令的控制点的反射。 如果没有上一个命令或者如果上一个命令不是二次方贝塞尔曲线命令或平滑二次方贝塞尔曲线命令，则该控制点与当前点一致。 此命令使用 [**QuadraticBezierSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.QuadraticBezierSegment) 定义 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 的等效对象，其中存在之前的曲线段。

| 语法 |
|--------|
| `T` *控制点* *终结点* <br/> - 或 - <br/> `t` *控制点* *终结点* |

| 术语 | 描述 |
|------|-------------|
| *controlPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)<br/> 曲线的控制点，可确定曲线的开始切线。 |
| *endPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)<br/> 要绘制曲线的点。 |

**椭圆弧命令**

在当前点和指定的端点之间创建一条椭圆弧。 使用 [**ArcSegment**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ArcSegment) 定义 [**PathGeometry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PathGeometry) 的等效对象。

| 语法 |
|--------|
| `A ` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endPoint* <br/> - 或 - <br/>`a ` *sizerotationAngleisLargeArcFlagsweepDirectionFlagendPoint* |

| 术语 | 描述 |
|------|-------------|
| size  | [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size)<br/>圆弧的 x 半径和 y 半径。 |
| *rotationAngle* | [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) <br/> 椭圆的旋转角度（以度为单位）。 |
| *isLargeArcFlag* | 如果圆弧的角度应为 180 度或更大，则设置为 1；否则，设置为 0。 |
| *sweepDirectionFlag* | 如果圆弧以正角方向绘制，则设置为 1；否则，设置为 0。 |
| *endPoint* | [**点**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point) <br/> 要绘制圆弧的点。|
 
**关闭命令**

结束当前图形，并创建一条可将当前点连接到图形起点的直线。 此命令将在该图形的最后一条线段和第一条线段之间创建一个直线接头（转角）。

| 语法 |
|--------|
| `Z` <br/> - 或 - <br/> `z ` |

**点语法**

介绍了某个点的 x 坐标和 y 坐标。 另请参阅 [**Point**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point)。

| 语法 |
|--------|
| *x*,*y*<br/> - 或 - <br/>*x* *y* |

| 术语 | 描述 |
|------|-------------|
| *x* | [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) <br/> 点的 x 坐标。 |
| *y* | [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) <br/> 点的 y 坐标。 |

**其他说明**

你还可以使用以下特殊值，而不是标准数字值。 这些值区分大小写。

-   **无穷大**:表示**PositiveInfinity**。
-   **\-无穷大**:表示**NegativeInfinity**。
-   **NaN**:表示**NaN**。

你可以使用科学计数法，而不是使用小数或整数。 例如，`+1.e17` 是有效值。

## <a name="design-tools-that-produce-move-and-draw-commands"></a>用于生成移动和绘制命令的设计工具

使用**笔**工具和 Blend for Microsoft Visual Studio 2015 中的其他绘图工具通常将生成[**路径**](/uwp/api/Windows.UI.Xaml.Shapes.Path)对象，通过移动和绘制命令。

你可能在某些控件部件中看到现有移动和绘制命令数据，这些部件已在控件的 Windows 运行时 XAML 默认模板中定义。 例如，某些控件将已定义数据的 [**PathIcon**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PathIcon) 用作移动和绘制命令。

为其他常用矢量图形设计工具提供了输出程序或插件，这些工具用于采用 XAML 格式输出矢量。 它们通常使用 [**Path.Data**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data) 的移动和绘制命令在布局容器中创建 [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) 对象。 XAML 中可能存在多个 **Path** 元素，因此可以应用不同的画笔。 许多这些导出程序或插件的初始编写的 Windows Presentation Foundation (WPF) XAML 或 Silverlight，但 XAML 路径语法与 Windows 运行时 XAML 相同。 通常，你可以使用来自导出程序的 XAML 区块，并将其正确粘贴到 Windows 运行时 XAML 页面中。 （但是，如果 **RadialGradientBrush** 是已转换的 XAML 的一部分，你将无法使用它，因为 Windows 运行时 XAML 不支持该画笔。）

## <a name="related-topics"></a>相关主题

* [绘制形状](https://docs.microsoft.com/windows/uwp/graphics/drawing-shapes)
* [使用画笔](https://docs.microsoft.com/windows/uwp/graphics/using-brushes)
* [**Path.Data**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data)
* [**PathIcon**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PathIcon)

