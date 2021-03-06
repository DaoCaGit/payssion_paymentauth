# 支付接口

## 创建交易

WEB服务API地址为 http://www.fifacoin.com/payment/create

测试服务API地址为 http://demo.fifacoin.com/payment/create

WEB服务API创建交易时所有参数通过key-value POST形式提交。

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |-------------| -----| -----|-----|
| amount        | 支付金额       | string | 必填 | 小数点精确到后面两位 |
| currency      | centered      | string  | 必填 | 如USD |
| order_id      | 订单号         | string  | 必填 |      |
| order_name    | 订单名称       | string  | 必填 |      |
| pm_id         | 应用支付方式   | string | 必填 | 支付方式标识，统一为小写字符，如tenpay_cn |
| key           | 应用ID         | string | 必填 |  |
| sig           | 请求签名       | string | 必填 | api接入签名验证， 具体生成规则参考 |


## 创建交易响应

创建交易响应参数说明(json格式)

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |-------------| -----| -----| -----|
| status        | 状态          | int | 必填 | 0 失败，1 成功 |
| msg           | 信息          | string  | 可选 |  |
| data          | 返回数据       | string  | 必填 |      |

响应示例，其中 paylink 为支付链接
```
{
    "status": 1,
    "msg": "创建成功",
    "data": {
        "paylink": "http:\/\/sandbox.payssion.com\/pay\/HA18136098319647",
        "transaction": {
            "transaction_id": "HA18136098319647",
            "state": "pending",
            "amount": "1.00",
            "currency": "USD",
            "pm_id": "sofort",
            "pm_name": "sofort",
            "order_id": "2344",
            "amount_local": "0.88",
            "currency_local": "EUR"
        }
    }
}
```

### 页面跳转同步通知

会在同步通知页面后面添加如下参数： transaction_id, state, order_id


### 服务器异步通知

1、通过 POST 方式发送通知信息,主要参数如下：   
pm_id：支付方式id  
transaction_id： Payssion平台交易号
order_id：商家订单号  
sub_track_id：其他订单跟踪信息  
amount：订单金额  
paid：已支付金额  
net：扣除先后续费后净额  
currency：交易币种  
description：订单描述   
state：支付状态  
notify_sig:：异步通知签名，具体规则参考签名规则。  

2、state ：支付状态，主要取值如下：

| 支付状态          | 说明        |
| -------------|-------------|
| error          | 支付发生错误  |
| pending         | 未完成支付  |
| completed       | 支付成功  |
| paid_partial    | 部分支付，用户只支付了部分金额  |
| failed          | 支付失败  |
| cancelled       | 交易被取消  |
| cancelled_by_user  | 用户取消支付  |
| rejected_by_bank  | 银行拒绝  |
| expired  | 交易失效  |
| refunded  | 退款成功  |
| refund_pending  | 已申请退款，正在处理退款  |
| refund_failed  | 退款失败  |
| chargeback  | 拒付  |


### 交易查询

线上环境地址
交易查询API地址为：
http://www.fifacoin.com/payment/getDetail

测试环境地址
交易查询API地址为：http://demo.fifacoin.com/payment/getDetail

所有参数通过key-value POST形式提交：

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |-------------| -----| -----|-----|
| api_key       | 应用ID	       | string | 必填 | 添加应用成功后可以看到该应用的key |
| transaction_id| 交易号         | string  | 必填 | 如USD |
| sig           | 请求签名       | string  | 必填 | api接入签名验证， 具体可参考签名生成规则 |

交易查询API响应参数说明(json格式)

| 参数          | 描述           | 类型  | 是否必填 | 参数说明 |
| ------------- |:-------------:| -----:| -----:| -----:|
| status        | 状态          | int | 必填 | 0 失败，1 成功 |
| msg           | 信息          | string  | 可选 | 提示信息 |
| transaction   | 该笔支付的信息	| object  | 必填 |  交易信息    |

响应示例

```
{
    "transaction":
    {
        "transaction_id":"F203853752614827",
        "app_name":"demo",
        "pm_id":"openbucks",
        "currency":"USD",
        "trackid":"",
        "subtrackid":"",
        "amount":"1.00",
        "paid":"0.00",
        "net":"0.00",
        "state":"pending",
        "fees":"0.00",
        "fees_add":"0.00",
        "refund":"0.00",
        "refund_fees":"0.00",
        "created":1422985375,
        "updated":1422985375
    },
    "status":1
    "msg":"查询交易成功"
}    

```
#### transaction

交易信息，主要字段有： transaction_id：Payssion平台交易号  
app_name：应用名称  
pm_id：支付方式id，请联系Payssion技术索取pm_id  
currency：交易币种  
trackid：商家订单号  
subtrackid：其他订单跟踪信息  
amount：订单金额  
paid：已支付金额  
net：扣除先后续费后净额  
state：支付状态  
fees：费用  
fees_add：额外费用  
refund：已退款金额  
refund_fees：退款费用  
created：交易创建时间  
updated：交易更新时间  


## 创建交易签名

创建交易签名sig生成分两步骤：

1、将key, pm, amount, currency, order_id,以及该应用的secret字符串，以 “|”为分隔符串联成一个字符串

2、将第一步骤串联起来的的字符串经md5加密生成最终的sig 具体代码示例：

```
$msg = implode("|", array($key, $pm_id, $amount, $currency, $order_id, $secret));
$sig = md5($msg);
```

## 异步通知签名

异步通知签名notify_sig生成分两步骤：

1、将key, pm_id, amount, currency, order_id, state以及应用的sercret字符串，以 “|”为分隔符串联成一个字符串  

2、将第一步骤串联起来的的字符串经md5加密生成最终的notify_sig 具体代码示例：

```
$msg = implode("|", array($key, $pm_id, $amount, $currency,$order_id, $state, $secret));
$notify_sig = md5($msg);
```


## 交易查询签名

交易查询签名sig生成分两步骤：

1、将key, transaction_id, order_id, 以及应用的sercret字符串，以 “|”为分隔符串联成一个字符串

2、将第一步骤串联起来的的字符串经md5加密生成最终的sig 具体代码示例：

```
$msg = implode("|", array($key, $transaction_id, $order_id, $secret));
$sig = md5($msg);
```

## 支付方式（pm）

| 参数          | pm          |
| ------------- |-------------|
| 支付宝         | alipay_cn   |
| 微信支付       | tenpay_cn   |


