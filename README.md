# 支付接口

## 创建交易

WEB服务API地址为 http://www.fifacoin.com/payment/create

WEB服务API创建交易时所有参数通过key-value POST形式提交。

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |:-------------:| :-----:| :-----:|:-----:|
| amount        | 支付金额       | string | 必填 | 小数点精确到后面两位 |
| currency      | centered      | string  | 必填 | 如USD |
| order_id      | 订单号         | string  | 必填 |      |
| order_name    | 订单名称       | string  | 必填 |      |
| pm            | 应用支付方式   | string | 必填 | 支付方式标识，统一为小写字符，如alipay |
| key           | 应用ID         | string | 必填 |  |
| sig           | 请求签名       | string | 必填 | api接入签名验证， 具体生成规则参考 |


## 创建交易响应

创建交易响应参数说明(json格式)

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |:-------------:| -----:| -----:| -----:|
| status        | 状态          | int | 必填 | 0 失败，1 成功 |
| msg           | 信息          | string  | 可选 |  |
| data          | 返回数据       | string  | 必填 |      |

如下所示，其中 paylink 为支付链接
```
{"status":0,"msg":"\u521b\u5efa\u6210\u529f","data":{"paylink":"http:\/\/sandbox.payssion.com\/pay\/HA18136098319647","transaction":{"transaction_id":"HA18136098319647","state":"pending","amount":"1.00","currency":"USD","pm_id":"sofort","pm_name":"sofort","order_id":"2344","amount_local":"0.88","currency_local":"EUR"}}}
```

## 创建交易签名

创建交易签名api_sig生成分两步骤：

1、将key, pm, amount, currency, order_id,以及该应用的secret字符串，以 “|”为分隔符串联成一个字符串

2、将第一步骤串联起来的的字符串经md5加密生成最终的sig 具体代码示例：

```
$msg = implode("|", array($key, $pm_id, $amount, $currency, $order_id, $secret));
$api_sig = md5($msg);

```

## 异步通知签名

待完成


## 交易查询签名

待完成


## 支付方式（pm）

| 参数          | pm           |
| ------------- |:-------------:|
| 支付宝         | alipay          |
| 微信支付       | wechatpayments          |
| paypal支付     | paypal       |


