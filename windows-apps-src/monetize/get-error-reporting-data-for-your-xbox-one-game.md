---
description: 使用 Microsoft Store 分析 API 中的此方法，可获取给定日期范围和其他可选筛选器的聚合错误报告数据。
title: 获取 Xbox One 游戏的错误报告数据
ms.date: 11/06/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 服务, Microsoft Store 分析 API, 错误
ms.localizationpriority: medium
ms.openlocfilehash: 22dff391e787e1763cb730272ba9cea029758c99
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57662072"
---
# <a name="get-error-reporting-data-for-your-xbox-one-game"></a>获取 Xbox One 游戏的错误报告数据

使用此方法已引入通过 Xbox 开发人员门户 (XDP) 和 XDP 分析合作伙伴中心仪表板中提供在 Microsoft Store analytics API 以获取有关你的 Xbox One 的聚合的错误报告数据游戏中。

可以使用来检索其他错误信息[获取游戏中你的 Xbox One 的错误的详细信息](get-details-for-an-error-in-your-xbox-one-game.md)，[获取游戏中你的 Xbox One 的错误的堆栈跟踪](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)，和[下载的 CAB 文件在 Xbox One 游戏中错误](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)方法。

## <a name="prerequisites"></a>必备条件


若要使用此方法，首先需要执行以下操作：

* 完成 Microsoft Store 分析 API 的所有[先决条件](access-analytics-data-using-windows-store-services.md#prerequisites)（如果尚未这样做）。
* [获取 Azure AD 访问令牌](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。

## <a name="request"></a>请求


### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits``` |


### <a name="request-header"></a>请求头

| 标头        | 在任务栏的搜索框中键入   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** *token*&lt;&gt;。 |


### <a name="request-parameters"></a>请求参数

| 参数        | 在任务栏的搜索框中键入   |  描述      |  必需  
|---------------|--------|---------------|------|
| applicationId | 字符串 | **Store ID** Xbox One 的游戏为其检索错误的报告数据。 **Store ID**可在合作伙伴中心中的应用标识页上。 举例**Store ID**是 9WZDNCRFJ3Q8。 |  是  |
| startDate | 日期 | 要检索的错误报告数据日期范围中的开始日期。 默认值为当前日期。 如果 *aggregationLevel* 是 **day**、**week** 或 **month**，此参数应采用 ```mm/dd/yyyy``` 格式指定日期。 如果 *aggregationLevel* 是 **hour**，此参数可以采用 ```mm/dd/yyyy``` 格式指定日期或者采用 ```yyyy-mm-dd hh:mm:ss``` 格式指定日期和时间。  |  否  |
| endDate | 日期 | 要检索的错误报告数据日期范围中的结束日期。 默认值为当前日期。 如果 *aggregationLevel* 是 **day**、**week** 或 **month**，此参数应采用 ```mm/dd/yyyy``` 格式指定日期。 如果 *aggregationLevel* 是 **hour**，此参数可以采用 ```mm/dd/yyyy``` 格式指定日期或者采用 ```yyyy-mm-dd hh:mm:ss``` 格式指定日期和时间。 |  否  |
| top | int | 要在请求中返回的数据行数。 如果未指定，最大值和默认值为 10000。 当查询中存在多行数据时，响应正文中包含的下一个链接可用于请求下一页数据。 |  否  |
| skip | int | 要在查询中跳过的行数。 使用此参数可以浏览较大的数据集。 例如，top=10000 和 skip=0，将检索前 10000 行数据；top=10000 和 skip=10000，将检索之后的 10000 行数据，依此类推。 |  否  |
| filter |字符串  | 在响应中筛选行的一条或多条语句。 每条语句包含的响应正文中的字段名称和值使用 **eq** 或 **ne** 运算符进行关联，并且语句可以使用 **and** 或 **or** 进行组合。 *filter* 参数中的字符串值必须使用单引号括起来。 你可以指定响应正文中的以下字段：<p/><ul><li>**应用程序名称**</li><li>**failureName**</li><li>**failureHash**</li><li>**symbol**</li><li>**osVersion**</li><li>**osRelease**</li><li>**eventType**</li><li>**market**</li><li>**deviceType**</li><li>**包名称**</li><li>**packageVersion**</li><li>**date**</li></ul> | 否   |
| aggregationLevel | 字符串 | 指定用于检索聚合数据的时间范围。 可以是以下字符串之一：**hour**、**day**、**week** 或 **month**。 如果未指定，默认值为 **day**。 如果指定 **week** 或 **month**，则 *failureName* 和 *failureHash* 值限制为 1000 个存储桶。<p/><p/>**注意：**&nbsp;&nbsp;如果指定 **hour**，则只能检索之前 72 小时的错误数据。 若要检索早于 72 小时的错误数据，请指定 **day** 或其他聚合级别之一。  | 否 |
| orderby | 字符串 | 对结果数据值进行排序的语句。 语法是 *orderby=field [order],field [order],...*。*field* 参数可以是以下字符串之一。<ul><li>**应用程序名称**</li><li>**failureName**</li><li>**failureHash**</li><li>**symbol**</li><li>**osVersion**</li><li>**osRelease**</li><li>**eventType**</li><li>**market**</li><li>**deviceType**</li><li>**包名称**</li><li>**packageVersion**</li><li>**date**</li></ul><p>*order* 参数是可选的，可以是 **asc** 或 **desc**，用于指定每个字段的升序或降序排列。 默认值为 **asc**。</p><p>下面是一个 *orderby* 字符串的示例：*orderby=date,market*</p> |  否  |
| groupby | 字符串 | 仅将数据聚合应用于指定字段的语句。 可以指定的字段如下所示：<ul><li>**failureName**</li><li>**failureHash**</li><li>**symbol**</li><li>**osVersion**</li><li>**eventType**</li><li>**market**</li><li>**deviceType**</li><li>**包名称**</li><li>**packageVersion**</li></ul><p>返回的数据行会包含 *groupby* 参数中指定的字段，以及以下字段：</p><ul><li>**date**</li><li>**applicationId**</li><li>**应用程序名称**</li><li>**deviceCount**</li><li>**eventCount**</li></ul><p>*groupby* 参数可以与 *aggregationLevel* 参数结合使用。 例如：*&amp;groupby=failureName,market&amp;aggregationLevel=week*</p></p> |  否  |


### <a name="request-example"></a>请求示例

下面的示例演示用于获取 Xbox One 游戏错误报告数据的多个请求。 替换*applicationId*值替换**Store ID**为您的游戏。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits?applicationId=BRRT4NJ9B3D1&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits?applicationId=BRRT4NJ9B3D1&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'Console' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应


### <a name="response-body"></a>响应正文

| 值      | 在任务栏的搜索框中键入    | 描述     |
|------------|---------|--------------|
| 值      | 数组   | 包含聚合错误报告数据的对象数组。 有关每个对象中的数据的详细信息，请参阅以下[错误值](#error-values)部分。     |
| @nextLink  | 字符串  | 如果存在数据的其他页，此字符串中包含的 URI 可用于请求下一页数据。 例如，当请求的 **top** 参数设置为 10000，但查询的错误超过 10000 行时，就会返回此值。 |
| TotalCount | 整数 | 查询的数据结果中的行总数。     |


### <a name="error-values"></a>错误值

*Value* 数组中的元素包含以下值。

| 值           | 在任务栏的搜索框中键入    | 描述        |
|-----------------|---------|---------------------|
| 日期            | 字符串  | 错误数据的日期范围内的第一个日期，格式为 ```yyyy-mm-dd```。 如果请求指定某一天，此值就是该日期。 如果请求指定更长的日期范围，此值则是该日期范围的第一个日期。 为指定的请求*aggregationLevel*的值**小时**，此值还包括一个时间值格式```hh:mm:ss```中发生了错误的本地时区。  |
| applicationId   | 字符串  | **Store ID**的 Xbox One 的游戏，您要为其检索数据时出错。   |
| applicationName | 字符串  | 游戏的显示名称。   |
| failureName     | 字符串  | 故障的名称，它由四个部分组成：一个或多个问题类、异常/错误检查代码、发生故障的映像的名称和相关的函数名称。  |
| failureHash     | 字符串  | 错误的唯一标识符。   |
| symbol          | 字符串  | 分配给该错误的符号。 |
| osVersion       | 字符串  | 出现错误的操作系统版本。 这始终是值**Windows 10**。  |
| osRelease       | 字符串  |  Windows 10 OS 版本或试验环指定 （作为 OS 版本中 subpopulation) 发生了错误的以下字符串之一。<p/><ul><li><strong>版本 1507</strong></li><li><strong>版本 1511</strong></li><li><strong>版本 1607</strong></li><li><strong>版本 1703</strong></li><li><strong>版本 1709</strong></li><li><strong>1803 的版本</strong></li><li><strong>发布预览</strong></li><li><strong>深入了解快速</strong></li><li><strong>深入了解速度缓慢</strong></li></ul><p>如果操作系统版本或外部测试 Ring 未知，则此字段的值为 <strong>Unknown</strong>。</p>    |
| eventType       | 字符串  | 以下字符串之一：<ul><li>**崩溃**</li><li>**挂起**</li><li>**内存故障**</li></ul>      |
| market          | 字符串  | 设备市场的 ISO 3166 国家/地区代码。   |
| deviceType      | 字符串  | 出现错误的设备的类型。 这始终是值**控制台**。    |
| packageName     | 字符串  | 与此错误关联的唯一名称游戏的包。      |
| packageVersion  | 字符串  | 游戏与此错误关联的包的版本。   |
| deviceCount     | 整数 | 对应于指定聚合级别的该错误的唯一设备数目。  |
| eventCount      | 整数 | 归因于指定聚合级别的该错误的事件数目。      |


### <a name="response-example"></a>响应示例

以下示例举例说明此请求的 JSON 响应正文。

```json
{
  "Value": [
    {
      "date": "2017-03-09",
      "applicationId": "BRRT4NJ9B3D1",
      "applicationName": "Contoso Sports",
      "failureName": "APPLICATION_FAULT_8013150a_StoreWrapper.ni.DLL!70475e55",
      "failureHash": "5a6b2170-1661-ed47-24d7-230fed0077af",
      "symbol": "storewrapper_ni!70475e55",
      "osVersion": "Windows 10",
      "osRelease": "Version 1803",
      "eventType": "crash",
      "market": "US",
      "deviceType": "Console",
      "packageName": "",
      "packageVersion": "0.0.0.0",
      "deviceCount": 0.0,
      "eventCount": 1.0
    }
  ],
  "@nextLink": "failurehits?applicationId=BRRT4NJ9B3D1&aggregationLevel=week&startDate=2017/03/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 21
}

```

## <a name="related-topics"></a>相关主题

* [获取游戏中你的 Xbox One 的错误的详细信息](get-details-for-an-error-in-your-xbox-one-game.md)
* [获取游戏中你的 Xbox One 的错误的堆栈跟踪](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
* [下载您的 Xbox One 游戏中的错误的 CAB 文件](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)
* [使用 Microsoft Store 服务的访问分析数据](access-analytics-data-using-windows-store-services.md)
