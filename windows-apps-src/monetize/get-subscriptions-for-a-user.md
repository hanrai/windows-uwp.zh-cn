---
ms.assetid: 94B5B2E9-BAEE-4B7F-BAF1-DA4D491427D7
description: 在 Microsoft Store 购买 API 中使用此方法来获取给定用户有权使用的订阅。
title: 获取用户订阅
ms.date: 07/10/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 购买 API, 订阅
ms.localizationpriority: medium
ms.openlocfilehash: b568531ce0807ebc5be0d27a78b94547e8473ae6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651622"
---
# <a name="get-subscriptions-for-a-user"></a>获取用户订阅

在 Microsoft Store 购买 API 中使用此方法来获取给定用户有权使用的订阅加载项。

> [!NOTE]
> 此方法仅供已由 Microsoft 预配能够为通用 Windows 平台 (UWP) 应用创建订阅加载项的开发人员帐户使用。 对于大多数开发人员帐户来说，订阅加载项目前尚不可用。

## <a name="prerequisites"></a>必备条件

若要使用此方法，你需要：

* 受众 URI 值为 `https://onestore.microsoft.com` 的 Azure AD 访问令牌。
* 一个 Microsoft Store ID 密钥，表示要获得其订阅的用户的身份。

有关详细信息，请参阅[管理来自服务的产品授权](view-and-grant-products-from-a-service.md)。

## <a name="request"></a>请求


### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI                                            |
|--------|--------------------------------------------------------|
| POST   | ```https://purchase.mp.microsoft.com/v8.0/b2b/recurrences/query``` |


### <a name="request-header"></a>请求头

| 标头         | 在任务栏的搜索框中键入   | 描述      |
|----------------|--------|-------------------|
| 授权  | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** *token*&lt;&gt;。                           |
| 主机           | 字符串 | 必须设置为值 **purchase.mp.microsoft.com**。                                            |
| 内容长度 | 数字 | 请求正文的长度。                                                                       |
| 内容类型   | 字符串 | 指定请求和响应类型。 当前，唯一受支持的值为 **application/json**。 |


### <a name="request-body"></a>请求正文

| 参数      | 在任务栏的搜索框中键入   | 描述     | 必需 |
|----------------|--------|-----------------|----------|
| b2bKey         | 字符串 | [Microsoft Store ID 密钥](view-and-grant-products-from-a-service.md#step-4)，表示要获取其订阅的用户的身份。  | 是      |
| ContinuationToken |  字符串     |  如果用户具有多个订阅的权利，则在达到页面限制时，响应正文将会返回一个延续令牌。 在后续调用中提供此处的延续令牌以检索剩余 产品。    | 否      |
| pageSize       | 字符串 | 要在一次响应中返回的最大订阅数。 默认值为 25。     |  否      |


### <a name="request-example"></a>请求示例

以下示例演示如何使用此方法来获取给定用户有权使用的订阅加载项。 将 *b2bKey* 值替换为代表要获取其订阅的用户身份的 [Microsoft Store ID 密钥](view-and-grant-products-from-a-service.md#step-4)。

```json
POST https://purchase.mp.microsoft.com/v8.0/b2b/recurrences/query HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
Host: https://purchase.mp.microsoft.com

{
  "b2bKey":  "eyJ0eXAiOiJ..."
}
```


## <a name="response"></a>响应

此方法将会返回一个 JSON 响应正文，其中包含用于描述用户有权使用的订阅加载项的数据对象集合。 以下示例演示了适合于具有某个订阅权利的用户的响应正文。

```json
{
  "items": [
    {
      "autoRenew":true,
      "beneficiary":"pub:gFVuEBiZHPXonkYvtdOi+tLE2h4g2Ss0ZId0RQOwzDg=",
      "expirationTime":"2017-06-11T03:07:49.2552941+00:00",
      "id":"mdr:0:bc0cb6960acd4515a0e1d638192d77b7:77d5ebee-0310-4d23-b204-83e8613baaac",
      "lastModified":"2017-01-08T21:07:51.1459644+00:00",
      "market":"US",
      "productId":"9NBLGGH52Q8X",
      "skuId":"0024",
      "startTime":"2017-01-10T21:07:49.2552941+00:00",
      "recurrenceState":"Active"
    }
  ]
}
```


### <a name="response-body"></a>响应正文

响应正文包含以下数据。

| 值        | 在任务栏的搜索框中键入   | 描述            |
|---------------|--------|---------------------|
| 项目 | 数组 | 对象数组，包含指定用户有权使用的每个订阅加载项的数据。 有关每个对象中的数据的详细信息，请参阅下表。  |


*items* 数组中的每个对象包含以下值。

| 值        | 在任务栏的搜索框中键入   | 描述                                                                 |
|---------------|--------|-----------------------------------------------|
| autoRenew | 布尔 |  表示是否已将订阅配置为在当前订阅期结束时自动续订。   |
| 受益人 | 字符串 |  与此订阅关联的权利受益人的 ID。   |
| expirationTime | 字符串 | 订阅截止日期和时间（ISO 8601 格式）。 仅当订阅处于特定状态时，此字段才可用。 截止时间通常表示当前状态截止的时间。 例如，对于活动的订阅，截止日期表示下一次自动续订发生的时间。    |
| expirationTimeWithGrace | 字符串 | 日期和时间的订阅将过期宽限期内，包括采用 ISO 8601 格式。 此值指示时，用户将失去访问权限的订阅后订阅无法自动续订。    |
| id | 字符串 |  订阅 ID。 使用此方法表示在你调用[更改用户订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)方法时想要修改的订阅。    |
| isTrial | 布尔 |  表示订阅是否为试用版。     |
| lastModified | 字符串 |  上次修改订阅的日期和时间（ISO 8601 格式）。      |
| market | 字符串 | 用户在其中获取订阅的国家/地区代码（两个字母 ISO 3166-1 alpha-2 格式）。      |
| productId | 字符串 |  表示 Microsoft Store 目录中的订阅加载项的[产品](in-app-purchases-and-trials.md#products-skus-and-availabilities)的 [Store ID](in-app-purchases-and-trials.md#store-ids)。 产品的示例应用商店 ID 为 9NBLGGH42CFD。     |
| skuId | 字符串 |  表示 Microsoft Store 目录中的订阅加载项的 [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) 的 [Store ID](in-app-purchases-and-trials.md#store-ids)。 SKU 的示例应用商店 ID 为 0010。    |
| startTime | 字符串 |  订阅的开始日期和时间（ISO 8601 格式）。     |
| recurrenceState | 字符串  |  以下值之一：<ul><li>**None**：&nbsp;&nbsp;这表示永久订阅。</li><li>**Active**：&nbsp;&nbsp;订阅有效且用户有权使用服务。</li><li>**Inactive**：&nbsp;&nbsp;订阅已过期，并且用户已关闭订阅的自动续订选项。</li><li>**Canceled**：&nbsp;&nbsp;订阅已在截止日期之前被故意终止，提供或不提供退款。</li><li>**InDunning**:&nbsp;&nbsp;订阅正处于*催缴*状态（即，订阅即将过期，且 Microsoft 正尝试获取款项以自动续订此订阅）。</li><li>**Failed**：&nbsp;&nbsp;催缴期已结束，并且在多次尝试后仍无法续订此订阅。</li></ul><p>**注意：**</p><ul><li>**Inactive**/**Canceled**/**Failed** 为终端状态。 当订阅处于这些状态之一时，用户必须重新购买订阅才能再次激活订阅。 在这些状态下，用户无权使用服务。</li><li>当订阅处于 **Canceled** 状态时，expirationTime 将会更新为取消的日期和时间。</li><li>在整个生命周期中，订阅 ID 将保持相同。 打开或关闭自动续订选项并不会更改订阅 ID。 如果用户在达到终端状态之后重新购买了订阅，则会创建新的订阅 ID。</li><li>订阅 ID 可用于在单个订阅上执行任何操作。</li><li>当用户在取消或中断订阅之后重新购买了订阅时，如果查询该用户的结果，则你将会获得两个条目：一个处于终端状态的旧订阅 ID 和一个处于活动状态的新订阅 ID。</li><li>最好始终检查 recurrenceState 和 expirationTime，因为更新至 recurrenceState 可能会延迟几分钟（有时候会是几小时）。       |
| cancellationDate | 字符串   |  用户订阅取消的日期和时间（ISO 8601 格式）。     |


## <a name="related-topics"></a>相关主题

* [从服务管理产品的权利](view-and-grant-products-from-a-service.md)
* [更改用户的订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)
* [产品的查询](query-for-products.md)
* [为满足报告易耗型产品](report-consumable-products-as-fulfilled.md)
* [续订 Microsoft Store ID 键](renew-a-windows-store-id-key.md)
