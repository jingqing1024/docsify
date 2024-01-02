# 简介

无

# API-加解密过程

## RequestHeader

```json
{
    "Content-Type": "application/json",
    "authorization": "{{authorization}}"
}
```

### authorization生成方式

1.algorithm：

```json
{
 	"alg":"HS256"
}
```

2.playload:

```json
{
    "jti":"",#随机数
    "iat":"timestamp",#JWT创建时间
    "exp":"timestamp2",#JWT过期时间
    "accountID":""#授权账户ID
}
```

3.signature

```java
hmacsha256_3(requestBody)
```

4.Final Encrypt Authorization

```java
BASE_64(algorithm)||'.'||BASE_64(playload)||'.'||BASE_64(signature)
```

## RequestBody

```json
{
    "data":"encryptData"
}
```

### encryptData加密方式

```java
RSA256(decryptData,privateKey)
```

### 参数说明
| 字段 | 说明 |
| --- | --- |
| encryptData | 加密后请求参数 |
| decryptData | 加密前请求参数 |
| privateKey | 开户时由opgs生成提供给商户 |

## ResponseBody

```json
{
    "code":"响应码",
    "message":"返回消息",
    "data":"加密后参数",
}
```

# 网关API接口说明

# 信用卡

## 授权接口
```
当向OPGS发送信用卡交易请求时，您需要提供信用卡的详细信息。您可以通过直接发送卡的详细信息或者cardId进行请求。 如果通过cardId进行请求，您需要先通过创建卡信息将卡信息进行保存从而得到cardId，然后您就可以使用这个cardId发送信用卡交易请求。
```
### 请求地址

```
/opgs/auth
```

### 请求方式

```
POST
```

### 请求头（header）

```json
{
    "Content-Type": "application/json",
    "authorization": "{{authorization}}"
}
```

### 请求头字段说明
| key | value | 说明 |
| --- | --- | --- |
| Content-Type | "application/json" | 请求类型 |
| authorization | "{{authorization}}" | 需要提供的authorization字段，字段说明详见：authorization生成方式 |

### 请求参数示例（加密）（body）

```json
{
    "data":"encryptData"
}
```

### 请求参数示例（解密）

```json
{
    "accountId": "1003",
    "channel": "worldpay",
    "merchantInfo": {
        "idCode": "SWIFTPASSECOMFSUSD",
        "pfId": "123456",
        "isoId": "123456",
        "subName": "hhh",
        "subId": "1234",
        "subPostalCode": "000000",
        "subStreet": "Regent street",
        "subCity": "London",
        "subCountryCode": "826",
        "merchantEntity": "default"
    },
    "transactionInfo": {
        "tradeNo": "{{orderCode}}",
        "amountTransaction": "1000",
        "transactionCurrencyCode": "HKD",
        "description": "d14654e6qw13dqweqwe"
    },
    "cardholderInfo": {
        "billingPostalCode": "537255",
        "billingCountryCode": "HK",
        "billingEmail": "huangbinsong@sinocompliance.com",
        "ipAddress": "192.168.2.1",
        "sessionId": "33654786415",
        "accountNum": "4444333322221111",
        "cardholderName": "koko",
        "instrumentType": "card/plain",
        "dateExpiration": "2310",
        "browserAcceptHeader": "text/html",
        "browserUserAgentHeader": "Mozilla/5.0 ..."
    }
}
```

### 请求参数说明

#### data
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | M | String | all | 授权账户号，商户注册时由OPGS系统提供 |
| channel | M | String | all | 渠道标识 |
| merchantInfo | M | JSON | all | 商户信息 |
| transactionInfo | M | JSON | all | 交易信息 |
| cardholderInfo | M | JSON | all | 持卡人信息 |

##### merchantInfo
| 字段 | 要求 | 类型 | **渠道** | 说明 |
| --- | --- | --- | --- | --- |
| type | M | String | worldpay<br />worldpayJson | 商户类型（MCC） |
| idCode | M | String | worldpay | 商户号 |
| countryCode | M | String | liquido | 商户国家代码 |
| pfId | M | String | worldpay<br />worldpayJson<br />checkout | 支付服务商id，当角色为支付服务商时需要填充 |
| subName | M | String | worldpay<br />worldpayJson<br />checkout | 子商户名称，当请求角色为支付服务商时需要填充 |
| subId | M | String | worldpay<br />worldpayJson<br />liquido | 子商户id，支付服务商分配，当请求角色为支付服务商时需要填充 |
| subStreet | M | String | worldpay<br />worldpayJson | 子商户街道地址（第一行）当请求角色为支付服务商时需要填充 |
| subCity | M | String | worldpay<br />worldpayJson | 子商户-城市，当请求角色为支付服务商时需要填充 |
| subState | C | String | worldpay<br />worldpayJson | 子商户-州，当请求角色为支付服务商时需要填充 |
| subCountryCode | M | String | worldpay<br />worldpayJson<br />checkout | 子商户-国家代码，当请求角色为支付服务商时需要填充 |
| subPostalCode | M | String | worldpay<br />worldpayJson | 子商户-邮编（香港可填000000），当请求角色为支付服务商时需要填充 |
| subTaxId | C | String | worldpay<br />worldpayJson<br />checkout | 子商户-税号，当请求角色为支付服务商时需要填充 |
| isoId | C | String | worldpay<br />worldpayJson | 独立销售组织（ISO）id，当请求角色为支付服务商时需要填充 |
| subEmailAddress | C | String | worldpay<br />worldpayJson<br />checkout | 子商户-电子邮箱，当请求角色为支付服务商时需要填充 |
| subTelephone | C | String | worldpay<br />worldpayJson<br />checkout | 子商户-电话号码，当请求角色为支付服务商时需要填充 |
| merchantEntity | M | String | worldpayJson | 商户实体名称 |

##### cardHolderInfo
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| accountNum | M | String | all | 持卡人账号 |
| dateExpiration | M | String | all | 卡过期时间，格式yyMM |
| cardholderName | M | String | all | 持卡人姓名 |
| billingAddress1 | C | String | worldpay<br />worldpayJson<br />checkout<br />tap | 账单地址第1行，若账单-城市提供，该字段为必填 |
| billingAddress2 | C | String | worldpay<br />worldpayJson<br />checkout | 账单地址第2行 |
| billingAddress3 | C | String | worldpay<br />worldpayJson | 账单地址第3行 |
| billingPostalCode | M | String | worldpay<br />worldpayJson<br />checkout<br />liquido | 账单-邮政编码 |
| billingCity | M | String | worldpay<br />worldpayJson<br />checkout<br />liquido<br />tap | 账单-城市，若账单地址第1行提供，该字段为必填 |
| billingState | C | String | worldpay<br />worldpayJson<br />checkout<br />liquido | 账单-州/省，北美使用2字符长的ISO代码，其他自由 |
| billingCountryCode | M | String | worldpay<br />worldpayJson<br />checkout<br />liquido | 账单-2位字母国家代码，大写 |
| billingEmail | M | String | worldpay | 账单-电子邮箱（若开通发送交易状态给持卡人，则通过该邮箱发送） |
| billingDistrict | C | String | liquido | 账单-区县 |
| billingStreet | C | String | liquido<br />tap | 账单-街道 |
| shippingAddress1 | C | String | worldpay | 收货地址第1行，若city提供，该字段为必填 |
| shippingAddress2 | C | String | worldpay | 收货地址第2行 |
| shippingAddress3 | C | String | worldpay | 收货地址第3行 |
| shippingPostalCode | C | String | worldpay | 收货-邮政编码 |
| shippingCity | C | String | worldpay | 收货-城市，若账单地址第1行提供，该字段必填 |
| shippingState | C | String | worldpay | 收货-州/省，北美使用2字符长的ISO代码，其他自由 |
| shippingCountryCode | C | String | worldpay | 收货-2位字母国家代码，大写 |
| ipAddress | M | String | worldpay<br />checkout<br />mymoney | 购物者IP地址 |
| sessionId | M | String | worldpay | 购物者浏览器的session_id |
| browserAcceptHeader | M |  | worldpay | 购物者浏览器的acceptHeader |
| browserUserAgentHeader | M | String | worldpay | 购物者浏览器的用户代理 |
| cvc | C | String | worldpayJson<br />liquido<br />tap<br />mymoney | CVC 是卡背面上的一组 3 或 4 个数字的独特号码 |
| instrumentType | M | String | worldpayJson | 付款类型说明（card/plain,card/wallet+applepay,card/wallet+googlepay） |
| walletToken | C | String | worldpayJson | 钱包token，由钱包传输过来【付款类型是card/wallet+applepay,card/wallet+googlepay时，必须提供】 |
| authenticationVersion | C | String | worldpayJson<br />checkout | 授权版本，对于 3DS1 - 1.0.2 对于 3DS2 - 2.1.0 或 2.2.0 |
| authenticationType | C | String | worldpayJson | 授权类型（3DS） |
| billingPhoneCountryCode | C | String | checkout | 持卡人国际国家电话代码 |
| billingPhoneNumber | C | String | checkout | 持卡人电话代码 |
| billingDescriptorName | C | String | checkout | 付款的动态描述 |
| billingDescriptorCity | C | String | checkout | 声明中应显示的引用 |
| billingDescriptorReference | C | String | checkout | 账单参考号 |
| payerDocumentId | C | String | liquido | 证件号码 |
| payerDocumentType | C | String | liquido | 证件类型，例如巴西居民的CPF或者CNPJ；例如墨西哥的IFE、INE或MEXICO_PASSPORT_ID |
| payerName | C | String | liquido | 名字，全称 |
| payerEmail | C | String | liquido | 邮箱 |
| payerPhone | C | String | liquido | 电话号码 |
| cardExpMonth | C | String | tap、mymoney | 信用卡过期月份，格式：MM<br />例如：01，06，12 |
| cardExpYear | C | String | tap、mymoney | 信用卡过期月份，格式：YYYY<br />例如：2039 |
| addressAvenue | C | String | tap | 街道 |
| addressCountry | C | String | tap | 国家名称<br />例如：Kuwait |

##### transactionInfo
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| tradeNo | M | String | all | 订单流水号，一笔交易唯一 |
| amountTransaction | M | Integer | all | 交易金额，按照ISO-4217币种规定填写小数位，如果金额为10，币种为USD（辅币位为2），则应填写 1000 |
| transactionCurrencyCode | M | String | all | 交易货币代码，使用ISO-4217的3位货币代码，必须为大写 |
| description | M | String | all | 您指定的订单描述。字母数字，最多  70 个字符。如果您的订单描述超出字符限制，则会在捕获文件中被截断。 |
| dynamicInteractionType | C | String | worldpay | 交易交互类型 |
| dynamic3DS | C | String | worldpay | 是否覆盖预设的3DS规则,可选值:0 = no3DS,1 = do3DS |
| ephemeralPublicKey | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| publicKeyHash | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| appleTransactionId | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| appleSignature | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| appleVersion | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| appleData | C | String | worldpay | 当为移动支付-ApplePay时需要填充 |
| googleProtocolVersion | C | String | worldpay | 当为移动支付-GooglePay时需要填充 |
| googleSignature | C | String | worldpay | 当为移动支付-GooglePay时需要填充 |
| googleSignedMessage | C | String | worldpay | 当为移动支付-GooglePay时需要填充 |
| payChannel | C | String | worldpayJson | 支付渠道表示持卡人与商家的互动。可选值：moto，提供值以将授权作为邮购或电话订购 （MOTO） 事务进行处理。如果未提供则默认将作为电子商务处理。moto注意：3ds交易不可选moto |
| debtRepayment | C | String | worldpayJson | DRI 是为偿还债务而标记某项支付的标志。可选值：true（仅在 true 时提供） |
| instructionNarrativeLine1 | M | String | worldpayJson | 商户落款 |
| instructionNarrativeLine2 | C | String | worldpayJson | 有关支付的其他详情，e.g.订单号、电话号码。 |
| programProtocol | C | String | worldpay | 3ds版本号 |
| dsTransactionID | C | String | worldpay<br />worldpayJson | 3ds认证返回的dsTransID |
| eci | C | String | worldpay<br />worldpayJson<br />checkout | 电子商务指标 |
| universalCardholderAuthenticationField | C | String | sinopay<br />worldpay<br />worldpayJson | 3ds认证返回的authenticationValue |
| threedsEnabled | C | String | checkout | 是否将此付款作为 3D Secure 付款处理 |
| threedsCryptogram | C | String | checkout | 卡方案使用 Base64 编码的加密标识符 |
| threedsXid | C | String | checkout | 3D 安全交易标识符 |
| previousPaymentId | C | String | checkout | 可以将付款链接到现有一系列付款的标识符。仅适用于使用存储的卡详细信息的商家发起的交易 |
| successUrl | C | String | checkout | 对于重定向付款方式，覆盖帐户中配置的默认成功重定向 URL |
| failureUrl | C | String | checkout | 对于重定向付款方式，覆盖帐户中配置的默认失败重定向 URL |
| paymentMethod | M | String | liquido | 交易方式，枚举值CREDIT_CARD |
| paymentFlow | M | String | liquido | 交易流程, 如DIRECT 或 REDIRECT |
| proposalId | C | String | liquido | 分期付款方案id |
| installments | C | integer | liquido | 分期付款次数. 不分期的时候请缺省该参数 |
| callbackUrl | C | String | liquido | 接收交易结果回调的url |
| shippingAddress1 | C | String | checkout | 消费信息地址第一行 |
| shippingAddress2 | C | String | checkout | 消费信息地址第二行 |
| shippingName | C | String | liquido | 名称 |
| shippingPhone | C | String | liquido | 电话号码 |
| shippingEmail | C | String | liquido | 邮箱地址 |
| shippingPostalCode | C | String | liquido<br />checkout | 邮编 |
| shippingCity | C | String | liquido<br />checkout | 城市 |
| shippingState | C | String | liquido<br />checkout | 街道 |
| shippingCountryCode | C | String | liquido<br />checkout | 国家代码 |
| shippingPhoneCountryCode | C | String | checkout | 消费信息电话号国家代码 |
| shippingPhoneNumber | C | String | checkout | 消费信息电话号 |
| shippingFromAddressZip | C | String | checkout | 消费信息来源地址邮编 |
| payerIpAddress | M | String | liquido | 客户请求源IP地址 |
| payerLoginId | M | String | liquido | 客户登录ID |
| payerDeviceId | M | String | liquido | 客户设备ID |
| use3ds | C | String | liquido | 是否使用3DS支付验证，默认（false） |
| card3dsInfoRedirectUrl | C | String | liquido | 墨西哥信用卡3DS支付验证完成认证之后的重定向url。墨西哥信用卡支付场景下，use3ds为true时必填 |
| capture | C | String | liquido | 巴西/墨西哥信用卡预授权支付交易场景，使用预授权时必填false |
| merchantPageUrl | C | String | liquido | 重定向回商户页面的URL |
| customerInitiated | M | String | tap | 此参数确定费用是否由客户发起，固定填 'true' |
| threeDSecure | C | boolean | tap | 特定授权付款的 3D 验证请求状态。 |
| saveCard | C | boolean | tap | 是否保存卡信息 |
| statementDescriptor | C | String | tap | 订单描述 |
| metadataUdf1 | C | String | tap | 要添加到授权请求的任何附加信息集作为键/值对。可以通过向单个键发布空值来取消设置它们。可以通过将空值发布到元数据来取消设置所有键。 |
| metadataUdf2 | C | String | tap | 要添加到授权请求的任何附加信息集作为键/值对。可以通过向单个键发布空值来取消设置它们。可以通过将空值发布到元数据来取消设置所有键。 |
| metadataUdf3 | C | String | tap | 要添加到授权请求的任何附加信息集作为键/值对。可以通过向单个键发布空值来取消设置它们。可以通过将空值发布到元数据来取消设置所有键。 |
| receiptEmail | C | boolean | tap | 如果需要将此费用的收据发送到客户的电子邮件地址，请将其设置为 ' true 。 |
| receiptSms | C | boolean | tap | 如果需要将此费用的收据发送到客户的电话号码，请将其设置为 ' true 。 |
| customerId | C | String | tap | 交易需要用到客户id，如果不填id则需要补充下面的客户信息 |
| customerFirstName | C | String | tap | 客户的名字 |
| customerMiddleName | C | String | tap | 客户的中间名 |
| customerLastName | C | String | tap | 客户的姓氏 |
| customerEmail | C | String | tap | 客户的电子邮件 ID |
| customerPhoneCountryCode | C | String | tap | 客户的国家/地区代码（不要添加 + 或 0） |
| customerPhoneNumber | C | String | tap | 客户的电话号码 |
| sourceId | M | String | tap | 需要授权的付款方式的来源。（token or src_all or src_card)）如果选择token方式，则需要填写card信息 |
| authorizeDebit | C | boolean | tap | 接受值 true 或 false 的布尔参数。如果设置为 true，则即使支付网络不支持交易，API 也会处理交易。需要注意的是，目前仅 mada 事务支持此功能。 |
| postUrl | C | String | tap | 接收回调通知的地址 |
| redirectUrl | O | String | tap | 授权完成后需要将客户重定向到的 URL。当threeDSecure为true时必填 |

### 响应参数示例（加密）

```json
{
	"code":200,
	"message": "succeed",
    "data":"encryptData"
}
```

### 响应参数示例（解密）

```json
{
      "accountId": "1003",
      "tradeNo":"20230926110923350909",
      "reference": "20230926110923350910"
}
```

### 响应参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| code | M | Integer | 响应码 |
| message | M | String | 响应消息 |
| data | C | JSON | 响应内容 |

#### data
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| accountId | M | String | 授权账户号，商户注册时由OPGS系统提供 |
| tradeNo | M | String | 订单流水号，一笔交易唯一 |
| reference | M | String | 授权订单参考号，由OPGS系统生成 |
| settleReference | C | String | 结算参考号，设置自动结算时填充 |
| transactionInfo | C | object | 交易成功返回的交易详细信息 |
| errorData | C | JSON Array | 错误信息，请求失败时填充 |

#### transactionInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| transactionUrl | C | String | tap交易返回的，需要进行授权或者交易的url地址 |

### 请求失败示例

```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```

```json
{
    "code": 501,
    "message": "UNAUTHORIZED"
}
```

### 

## 授权取消接口

下列为支持取消授权功能所支持的渠道，支付方式

| channelID | paymentMethod |
| --- | --- |
| wolrdpay | CREDIT_CARD |
| checkout | CREDIT_CARD |
| liquido | CREDIT_CARD |
| liquido | BANK_TRANSFER |
| liquido | PAY_CASH |


注：订单授权成功，未结算时可使用取消接口取消未结算订单，已结算订单不能取消

### 请求地址

```
/opgs/revoke
```

### 请求方式

```
post
```

### 请求头（header）

```json
{
    "Content-Type": "application/json",
    "authorization": "{{authorization}}"
}
```

### 请求头字段说明
| key | value | 说明 |
| --- | --- | --- |
| Content-Type | "application/json" | 请求类型 |
| authorization | "{{authorization}}" | 需要提供的authorization字段，字段说明详见：authorization生成方式 |

### 请求参数示例（加密）（body）

```json
{
    "data":"encryptData"
}
```

### 请求参数示例（解密）

```json
{
    "accountId": "1003",
    "channel": "worldpay",
    "merchantInfo": {
        "idCode": "SWIFTPASSECOMFSUSD"	
    },
    "transactionInfo": {
        "tradeNo": "20231102111111957734"
    }
}
```

### 请求参数说明

#### data
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | M | String | all | 授权账户号，商户注册时由OPGS系统提供 |
| channel | M | String | all | 渠道标识 |
| merchantInfo | C | JSON | worldpay | 商户信息，worldpay渠道需要提供 |
| transactionInfo | M | JSON | all | 交易信息 |

##### merchantInfo
| 字段 | 要求 | 类型 | **渠道** | 说明 |
| --- | --- | --- | --- | --- |
| idCode | M | String | worldpay | 商户号 |

##### transactionInfo
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| tradeNo | M | String | all | 原始授权订单的订单流水号 |

### 响应参数示例（加密）

```json
{
	"code":200,
	"message": "succeed",
    "data":"encryptData"
}
```

### 响应参数示例（解密）

```json
{
      "accountId": "1003",
      "tradeNo":"20230926110923350909",
      "reference": "20230926110923350910"
}
```

### 响应参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| code | M | Integer | 响应码 |
| message | M | String | 响应消息 |
| data | C | JSON | 响应内容 |

#### data
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| accountId | M | String | 授权账户号，商户注册时由OPGS系统提供 |
| tradeNo | M | String | 订单流水号，一笔交易唯一 |
| reference | M | String | 授权取消订单参考号，由OPGS系统生成 |
| errorData | C | JSON Array | 错误信息，请求失败时填充 |

### 请求失败示例

```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```

```json
{
    "code": 501,
    "message": "UNAUTHORIZED"
}
```

### 

## 绑定信用卡

```
用于liquido渠道信用卡付款时，商户可以选择先绑定信用卡，后续的付款采用cardId进行付款,其他渠道暂不支持
```
#### 请求地址
```
/bindCard/bind
```

#### 请求方式

```
POST
```

#### 请求头（header）

```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```
#### 请求参数示例（加密）（body）
```json
{
    "data":"encryptData"
}
```
#### 请求参数示例（解密）
```json
{
    "country": "BR",
    "cardholderName": "Authorized",
    "cardNumber": "4111111111111111",
    "expirationMonth": "09",
    "expirationYear": "2024",
    "cvc": "123",
    "name": "username",
    "email": "username@liquido.com",
    "accountId": "1003"
}
```
#### 请求参数说明
| **名称** | **类型** | **必选** | **中文名** | **说明** |
| --- | --- | --- | --- | --- |
| country | string | 是 | 国家名称代码 | 列如MX |
| cardholderName | string | 是 | 持卡人名称 | none |
| cardNumber | string | 是 | 卡号 | none |
| expirationMonth | string | 是 | 卡过期月份 | none |
| expirationYear | string | 是 | 卡过期年份 | none |
| cvc | string | 是 | 信用卡安全码. | none |
| accountId | string | 是 | OPGS系统商户号 | none |
| name | string | 否 | 名字，全称 | 国家代码为MX时必填 |
| email | string | 否 | 邮箱 | 国家代码为MX时必填 |

#### 响应参数示例（加密）
```json
{
	"code":200,
	"message": "succeed",
  "data":"encryptData"
}
```
#### 响应参数示例（解密）
```json
{
    "expirationYear": "2024",
    "accountId": "1003",
    "last4": "1111",
    "createTime": "2023-12-04T11:38:09.557",
    "cardHolderName": "Authorized",
    "bin": "411111",
    "cardId": "25bb83dc-e8f2-4fc9-bc01-b244e5fc7008",
    "id": "194403a3-5767-40d5-99b6-a7f2d25ba702",
    "expirationMonth": "9",
    "type": "1",
    "brand": "VISA"
}
```
#### 响应参数说明
| **名称** | **类型** | **必选** | **约束** | **中文名** | **说明** |
| --- | --- | --- | --- | --- | --- |
| code | integer | true | none | 请求状态码 | 成功：200；其它：失败 |
| message | string | true | none | 请求结果 | 成功：success |
| expirationYear | string | true | none | 过期年份 |  |
| last4 | string | true | none | 卡号后四位 |  |
| cardHolderName | string | true | none | 持卡人名称 |  |
| bin | string | true | none | 卡号前六位 |  |
| expirationMonth | string | true | none | 过期月份 |  |
| type | string | true | none | 类型 | 1为绑卡返回数据,2为查询返回数据 |
| brand | string | true | none | 卡品牌 |  |
| createTime | sting | true | none | 创建时间 |  |
| cardId | string | true | none | 保存的卡ID. |  |
| id | string | true | none | 系统唯一标识 |  |
| accountId | string | true | none | 系统商户号 |  |
| FailData | object | true | none | 失败响应参数 |  |
| errorData | string | true | none | 失败原因 |  |

## 分期付款
```
信用卡分期付款与信用卡普通付款类似。终端用户可以选择将付款总金额在定期时间内分别支付分期交易金额。第一笔分期付款金额将立即记入终端用户的信用卡，剩下的分期付款金额将定期自动扣款。
发起信用卡分期交易前，您需要先联系Liquido获取分期计划并拿到Plan ID。然后您需要使用这个Plan ID来创建分期方案，以获取Proposal ID。 请注意，若您需要在方案里面包含除了分期之外的其他目的方案，那么您需要通过创建多目的方案来创建方案。 使用Proposal ID(proposalId) 和分期付款期数(installments)，您就可以前往发起信用卡分期付款。
```
### 查询分期计划
```
用于查询当前商户由渠道分配的分期计划有哪些，分期方案需要渠道配置
```
#### 请求地址

```
/installment/get/plan
```
#### 请求方式
```
POST
```
#### 请求头（header）
```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```
#### 请求参数示例（加密）（body）

```json
{
    "data": "encryptData"
}
```

#### 请求参数示例（解密）

```json
{
    "accountId": "1003",
    "installmentPlanId": "bb7049cf-5884-4c39-bef0-a63df980d4e3"
}
```

#### 请求参数说明

| 名称 | 位置 | 类型 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | body | string | 是 | OPGS系统账号 |
| installmentPlanId | body | string | 否 | 分期计划id，传递查询单个计划，不传查询全部计划 |

#### 响应参数示例（加密）
```json
{
	"code":200,
	"message": "succeed",
 	"data":"encryptData"
}
```
#### 响应参数示例（解密）

```json
{
    "planDetails": {
        "installment": [
            {
                "rate": 0,
                "installments": 1,
                "modifierType": "RATE"
            },
            {
                "rate": 0.02,
                "installments": 2,
                "modifierType": "RATE"
            },
            {
                "rate": 0.03,
                "installments": 3,
                "modifierType": "RATE"
            },
            {
                "rate": 0.04,
                "installments": 4,
                "modifierType": "RATE"
            },
            {
                "rate": 0.05,
                "installments": 5,
                "modifierType": "RATE"
            },
            {
                "rate": 0.06,
                "installments": 6,
                "modifierType": "RATE"
            },
            {
                "rate": 0.07,
                "installments": 7,
                "modifierType": "RATE"
            },
            {
                "rate": 0.08,
                "installments": 8,
                "modifierType": "RATE"
            },
            {
                "rate": 0.09,
                "installments": 9,
                "modifierType": "RATE"
            },
            {
                "rate": 0.1,
                "installments": 10,
                "modifierType": "RATE"
            },
            {
                "rate": 0.11,
                "installments": 11,
                "modifierType": "RATE"
            },
            {
                "rate": 0.12,
                "installments": 12,
                "modifierType": "RATE"
            }
        ]
    },
    "id": "bb7049cf-5884-4c39-bef0-a63df980d4e3",
    "type": "INSTALLMENT"
}
```



#### 响应参数说明

| 名称 | 位置 | 类型 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| code | body | integer | 是 | 响应码 |
| message | body | string | 是 | 响应信息 |
| data | body | JSON | 否 | 响应数据 |

##### data参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 分期计划ID，UUID |
| type | string | 分期计划类型，INSTALLMENT |
| planDetails | JSON | 分期计划详情 |

##### planDetails参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| type | string | 分期计划类型，INSTALLMENT |
| installment | JSON Array | 分期付款详情 |

##### installment参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| modifierType | string | 类型，RATE |
| rate | Positive Float | 分期费率 |
| installments | Integer | 分期付款期数 |

#### 请求失败示例
```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```
```json
{
  "code": 502,
  "message": "Bad gateway"
}
```
### 创建分期方案
#### 请求地址

```api
/installment/create/proposals
```

#### 请求方式
```
POST
```
#### 请求头（header）

```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```

#### 请求参数示例（加密）（body）

```json
{
    "data": "encryptData"
}
```

#### 请求参数示例（解密）

```json
{
    "accountId": "1003",
    "amount": 1000000,
    "country": "BR",
    "currency": "USD",
    "targetCurrency": "BRL",
    "installmentPlanId": "bb7049cf-5884-4c39-bef0-a63df980d4e3"
}
```

#### 请求参数说明
| 名称 | 位置 | 类型 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | body | string | 是 | OPGS系统账号 |
| amount | body | integer | 是 | 金额，单位是货币最小粒度。 如 1 = 0.01BRL |
| country | body | string | 是 | 国家编号国际缩写码，遵循ISO 3166-1 alpha-2 code标准, 如巴西BR |
| currency | body | string | 是 | 交易币种，ISO currency code |
| targetCurrency | body | string | 否 | 交易币种非BRL或MXN时需传递，货币编码，如USD、BRL、MXN |
| installmentPlanId | body | string | 是 | 分期付款计划ID |


#### 响应参数示例（加密）

```json
{
	"code":200,
	"message": "succeed",
 	"data":"encryptData"
}
```

#### 响应参数示例（解密）

```json
{
    "proposalDetails": {
        "installmentProposalDetails": [
            {
                "totalAmount": 2741755,
                "installmentAmount": 2741755,
                "installments": 1
            },
            {
                "totalAmount": 2796590,
                "installmentAmount": 1398295,
                "installments": 2
            },
            {
                "totalAmount": 2824008,
                "installmentAmount": 941336,
                "installments": 3
            },
            {
                "totalAmount": 2851425,
                "installmentAmount": 712856,
                "installments": 4
            },
            {
                "totalAmount": 2878843,
                "installmentAmount": 575769,
                "installments": 5
            },
            {
                "totalAmount": 2906260,
                "installmentAmount": 484377,
                "installments": 6
            },
            {
                "totalAmount": 2933678,
                "installmentAmount": 419097,
                "installments": 7
            },
            {
                "totalAmount": 2961095,
                "installmentAmount": 370137,
                "installments": 8
            },
            {
                "totalAmount": 2988513,
                "installmentAmount": 332057,
                "installments": 9
            },
            {
                "totalAmount": 3015931,
                "installmentAmount": 301593,
                "installments": 10
            },
            {
                "totalAmount": 3043348,
                "installmentAmount": 276668,
                "installments": 11
            },
            {
                "totalAmount": 3070766,
                "installmentAmount": 255897,
                "installments": 12
            }
        ],
        "fxProposalDetails": {
            "fxRate": {
                "fxRateTarget": "BRL",
                "fxRateTimestamp": 1664443743,
                "fxRateBase": "USD",
                "fxRateExchangeRate": 5.4835098
            },
            "targetAmount": 2741755,
            "baseAmount": 500000
        }
    },
    "planDetails": {
        "fx": [
            {
                "fxRateTarget": "BRL",
                "spreadRate": 0.02,
                "fxRateBase": "USD"
            },
            {
                "fxRateTarget": "MXN",
                "spreadRate": 0.02,
                "fxRateBase": "USD"
            }
        ],
        "installment": [
            {
                "rate": 0,
                "installments": 1,
                "modifierType": "RATE"
            },
            {
                "rate": 0.02,
                "installments": 2,
                "modifierType": "RATE"
            },
            {
                "rate": 0.03,
                "installments": 3,
                "modifierType": "RATE"
            },
            {
                "rate": 0.04,
                "installments": 4,
                "modifierType": "RATE"
            },
            {
                "rate": 0.05,
                "installments": 5,
                "modifierType": "RATE"
            },
            {
                "rate": 0.06,
                "installments": 6,
                "modifierType": "RATE"
            },
            {
                "rate": 0.07,
                "installments": 7,
                "modifierType": "RATE"
            },
            {
                "rate": 0.08,
                "installments": 8,
                "modifierType": "RATE"
            },
            {
                "rate": 0.09,
                "installments": 9,
                "modifierType": "RATE"
            },
            {
                "rate": 0.1,
                "installments": 10,
                "modifierType": "RATE"
            },
            {
                "rate": 0.11,
                "installments": 11,
                "modifierType": "RATE"
            },
            {
                "rate": 0.12,
                "installments": 12,
                "modifierType": "RATE"
            }
        ]
    },
    "submitTime": "2023-12-15T02:14:29.612469Z",
    "expirationTime": "2023-12-15T03:14:29.612469Z",
    "paymentIntent": {
        "country": "BR",
        "amount": 500000,
        "targetCurrency": "BRL",
        "installmentPlanId": "bb7049cf-5884-4c39-bef0-a63df980d4e3",
        "currency": "USD"
    },
    "id": "bb68f6b3-872a-4eaa-8d26-d5264ed6377e"
}
```
#### 响应参数说明
| 名称 | 位置 | 类型 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| code | body | integer | 是 | 响应码 |
| message | body | string | 是 | 响应信息 |
| data | body | JSON | 否 | 响应数据 |

##### data参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 方案ID |
| submitTime | string | 方案提交时间 |
| expirationTime | string | 方案过期时间 |
| paymentIntent | JSON | 方案内容 |
| planDetails | JSON | 计划详情 |
| proposalDetails | JSON | 方案详情 |

##### paymentIntent参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| amount | integer | 金额，单位是货币最小粒度。 如 1 = 0.01BRL |
| country | string | 国家编号国际缩写码，遵循ISO 3166-1 alpha-2 code标准, 如巴西BR |
| currency | string | 货币编码，如USD或BRL |
| targetCurrency | string | 货币编码，如USD、BRL、MXN |
| installmentPlanId | string | 分期付款计划ID |

##### planDetails参数说明
| 名称 | 类型 | 说明 |
| --- | --- | --- |
| installment | JSON Array | 分期计划详情 |
| fx | JSON Array | 换汇计划详情 |
| tax | JSON Array | 税费计划详情 |

##### installment参数说明
| installment | JSON Array | 分期付款计划详情 |
| --- | --- | --- |
| modifierType | string | 类型，RATE |
| rate | Positive Float | 分期费率 |
| installments | Integer | 分期付款期数 |

##### fx参数说明

| installment | JSON Array     | 分期付款计划详情 |
| ----------- | -------------- | ---------------- |
| base        | string         | 当前货币编码     |
| target      | string         | 目标货币编码     |
| spreadRate  | Positive Float | 换汇利差率       |

##### tax参数说明

| installment  | JSON Array     | 分期付款计划详情 |
| ------------ | -------------- | ---------------- |
| modifierType | string         | 类型，RATE       |
| rate         | Positive Float | 税率             |
| type         | Integer        | 税率类型         |


##### proposalDetails参数说明
| installment | JSON Array | 分期付款计划详情 |
| --- | --- | --- |
| installmentProposalDetails | JSON Array | 分期付款方案详情 |
| fxProposalDetails | JSON | 换汇方案详情 |
| taxProposalDetails | JSON | 税费方案详情 |


##### installmentProposalDetails参数说明
| installment | JSON Array | 分期付款计划详情 |
| --- | --- | --- |
| fxRate | JSON | 原始币种分期付款金额，单位是货币最小粒度。 如 1 = 0.01BRL |
| baseAmount | Integer    | 当前货币的金额，单位是货币最小粒度。 如 1 = 0.01USD       |
| targetAmount | Integer    | 目标货币的金额，单位是货币最小粒度。 如 1 = 0.01BRL |

##### fxRate参数说明

| installment  | JSON Array     | 分期付款计划详情                                    |
| ------------ | -------------- | --------------------------------------------------- |
| modifierType | string         | 类型，RATE                                          |
| base         | string         | 当前货币编码，如 USD                                |
| target       | string         | 目标货币的金额，单位是货币最小粒度。 如 1 = 0.01BRL |
| timestamp    | Integer        | 货币转换时的时间戳                                  |
| exchangeRate | Positive Float | 汇率                                                |

##### taxProposalDetails参数说明

| installment | JSON Array | 分期付款计划详情 |
| ----------- | ---------- | ---------------- |
| taxRate     | JSON       | 汇率详情         |
| amount      | Integer    | 税费金额。       |

##### taxRate参数说明

| installment  | JSON Array     | 分期付款计划详情 |
| ------------ | -------------- | ---------------- |
| modifierType | String         | 类型，RATE       |
| rate         | Positive Float | 税率             |
| type         | String         | 税率类型         |

#### 请求失败示例

```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```

```json
{
  "code": 502,
  "message": "Bad gateway"
}
```

### 查询分期付款方案
#### 请求地址
```
/installment/get/proposals
```
#### 请求方式
```
post
```
#### 请求头（header）

```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```

#### 请求参数示例（加密）（body）

```json
{
    "data": "encryptData"
}
```

#### 请求参数示例（解密）

```json
{
    "accountId": "1003",
    "proposalId": "bb7049cf-5884-4c39-bef0-a63df980d4e3"
}
```

#### 请求参数说明
| 名称 | 位置 | 类型 | 必选 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | body | string | 是 | OPGS系统账号 |
| proposalId | body | string | 是 | 分期付款方案ID |

#### 响应参数示例（加密）

```json
{
	"code":200,
	"message": "succeed",
 	"data":"encryptData"
}
```

#### 响应参数示例（解密）

同创建分期方案

#### 响应参数说明

同创建分期方案

#### 请求失败示例

```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```

```json
{
  "code": 502,
  "message": "Bad gateway"
}
```

# 收银台
## 支付接口
### http请求
```
post /bank/pay
```
### Request Headers
```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```

#### http Headers字段说明
| key | value | 说明 |
| --- | --- | --- |
| Content-Type | "application/json" | 请求类型 |
| authorization | "{{authorization}}" | 需要提供的authorization字段，字段说明详见：authorization生成方式 |


### Request Body

```json
{
    "data":"encryptData"
}
```

#### 请求参数示例

```json
{
    "tradeNo": "20231113101101446680",
    "accountId":"1003",
    "channel":"tap",
    "transactionInfo":{
        "amount": 1.00,
        "countryCode":"Kuwait",
        "currency":"KWD",
        "paymentMethod":"CARD",
        "callbackUrl":"https://webhook.site/a60e0c04-d13b-4ede-b2e5-6807ec4f85b5",
        "redirectUrl":"http://your_website.com/redirecturl",
        "description":"Transaction Description"
    },
	"merchantInfo":{
        "merchantId":"123456789"
    },
    "customerInfo":{
        "customerId":"cus_TS04A0820231123Rl590311326",
		    "customerName":"John Doe",
		    "customerEmail":"john.doe@example.com",
		    "customerCountryCode":"965",
		    "customerMobilePhone":"861234567890",
        "customerInitiated":true
    },
    "riskInfo":{
        "ipAddress":"192.168.1.1"
    },
    "threeDSecureInfo":{
        "threeDSecure":true
    },
    "cardInfo":{
        "saveCard":true
    }
}
```

#### 请求参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| tradeNo | M | String | 订单号 |
| accountId | M | String | 授权账户号，商户注册时由OPGS系统提供 |
| channel | M | String | 渠道标识，详见**渠道与交易方式说明**。 |
| transactionInfo | M | object | 交易信息 |
| merchantInfo | C | object | 商户信息 |
| customerInfo | M | object | 子商户信息 |
| shippingInfo | C | object | 订单信息 |
| billingInfo | C | object | 账单信息 |
| riskInfo | C | object | 风险信息 |
| cardInfo | C | object | 卡信息 |
| threeDSecureInfo | C | object | 3DS信息 |
| additionalInfo | C | object | 附加信息 |


##### transactionInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| amount | M | BigDecimal | 交易金额 |
| countryCode | M | String | 国家代码 |
| currency | M | String | 交易币种 |
| paymentMethod | M | String | 交易方式，详见**渠道与交易方式说明**。 |
| callbackUrl | C | String | 通知回调地址 |
| redirectUrl | O | String | 交易完成后跳转的重定向地址。<br />【tap 渠道】：付款完成后，付款人将被重定向到此 URL（KNET、 3DS请求需要重定向 URL） |
| description | C | String | 描述 |


##### merchantInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| merchantId | C | String | 商户id。<br />【tap 渠道】：需要将资金路由到的商家帐户的 ID。可在点击仪表板上使用（goSell > API 凭据>商家 ID）<br />【thunes 渠道】：商户id<br />【liquido 渠道】子商户ID。商户类型为PSP时必填。 |


##### customerInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| customerId | O | String | 【tap 渠道】如果没有填写客户的详细信息，可直接填写客户id进行交易 |
| customerName | M | String | 客户的名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| customerCompanyName | C | String | 客户公司名称 |
| customerType | C | String | 客户类型 |
| customerEmail | M | String | 客户的电子邮件地址 |
| customerHomePhone | C | String | 客户的家庭电话号码 |
| customerMobilePhone | O | String | 【liquido、tap渠道】客户的手机号码 |
| customerCountryCode | O | String | 语言和国家/地区。根据 ISO 639 和 ISO 3166<br />【tap 渠道】需要填写 |
| customerInitiated | C | boolean | 【tap 渠道】<br />此参数确定费用是否由客户发起 |
| customerDocumentId | O | String | 【liquido 渠道】证件号码 |
| customerDocumentType | O | String | 【liquido 渠道】证件类型，暂时支持CPF或者CNPJ |


##### shippingInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| orderId | O | String | 订单号<br />【liquido thunes 渠道】必须填写 |
| orderCurrency | C | String | 订单币种 |
| shippingName | C | String | 名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| shippingPhone | C | String | 电话号码，需包含“+55”的前缀。<br />【thunes】：收货人手机号码 |
| shippingEmail | C | String | 邮箱地址 |
| shippingStreet | C | String | 街道 |
| shippingNumber | C | String | 门牌号 |
| shippingComplement | C | String | 补充信息 |
| shippingDistrict | C | String | 区县 |
| shippingCity | C | String | 城市 |
| shippingState | C | String | 州 |
| shippingZipCode | C | String | 邮政编码 |
| shippingCountry | C | String | 国家代码 |
| shippingHomePhone | C | String | 收货人家庭电话号码 |


##### billingInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| billingName | C | String | 账单接收人名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| billingZipCode | C | String | 账单接收人邮编 |
| billingState | C | String | 账单接收人所在州 |
| billingCity | C | String | 账单接收人所在城市 |
| billingDistrict | C | String | 账单接收人所在区域 |
| billingStreet | C | String | 账单接收人所在街道 |
| billingNumber | C | String | 账单接收人所在门牌号 |
| billingComplement | C | String | 账单接收人地址补充信息 |
| billingCountry | O | String | 账单接收人所在国家<br />【thunes 渠道】必填 |
| billingHomePhone | C | String | 账单接收人家庭电话号码 |
| billingMobilePhone | C | String | 账单接收人手机号码 |


##### riskInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| ipAddress | C | String | 【liquido 渠道】客户请求原ip地址 |


##### cardInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| saveCard | C | boolean | 【tap 渠道】是否保存卡信息，默认false |


##### threeDSecureInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| threeDSecure | C | boolean | 是否使用3ds，默认ture |


##### additionalInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| paymentFlow | O | String | 交易流程, 如DIRECT 或 REDIRECT<br />【liquido 渠道】必填 |
| totalAmount | C | BigDecimal | 全部订单金额，包括税费和运费 |
| vatAmount | C | BigDecimal | 税费 |


#### 响应参数示例

##### 成功

```json
{
    "code": 200,
    "message": "succeed",
    "accountId": "1003",
    "tradeNo": "20231113091133589792",
    "reference": "20231113091133589792",
    "paymentUrl": "https://app.picpay.com/checkout/NjU1MTdjZjVkZGNmZmJmNTg2MGRjYWMy?nc=true"
}
```

##### 失败

```json
{
    "code": 409,
    "message": "Order already exist"
}
```

```json
{
    "code": 500,
    "message": "System error"
}
```

#### 响应参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| code | M | Integer | 返回码 |
| message | M | String | 返回码描述 |
| accountId | C | String | 授权账户号，商户注册时由OPGS系统提供 |
| tradeNo | C | String | 订单号 |
| reference | C | String | 参考号 |
| paymentUrl | C | String | 支付链接 |


### 渠道与交易方式说明
| **paymentMethod** | **channel（渠道）** |
| --- | --- |
| PIC_PAY | liquido |
| MERCADO_PAGO | liquido |
| AME_QR | liquido |
| PAYPAL | liquido |
| CREDIT_CARD | tap |
| DEBIT_CARD | tap |
| KNET | tap |
| BENEFIT | tap |
| OMANNET | tap |
| EASYPAISA | thunes |
| JAZZCASH | thunes |


## 查询接口

### http请求

```
post /bank/query
```

### Request Headers

```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```

#### http Headers字段说明
| key | value | 说明 |
| --- | --- | --- |
| Content-Type | "application/json" | 请求类型 |
| authorization | "{{authorization}}" | 需要提供的authorization字段，字段说明详见：authorization生成方式 |


### Request Body

#### 请求参数示例

```json
{
    "accountId":"1003",
    "channel":"tap",
    "tradeNo":"20231115161194165089"
}
```

#### 请求参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| accountId | M | String | 授权账户号，商户注册时由OPGS系统提供 |
| channel | M | String | 渠道标识，详见**渠道可选值枚举说明**。 |
| tradeNo | M | String | 订单号 |
| reference | C | String | 参考号，如果传入，则查询单条订单信息 |


### Response Body

#### 响应参数示例

##### 成功

```json
{
    "code": 200,
    "message": "succeed",
    "accountId": "1003",
    "channel": "tap",
    "data": [
        {
            "tradeNo": "20231115161194165089",
            "reference": "20231115164507472",
            "transactionInfoVo": {
                "amount": 1000,
                "currency": "KWD",
                "paymentMethod": "CREDIT_CARD",
                "callbackUrl": "http://localhost:9999/notify",
                "redirectUrl": "http://your_website.com/redirecturl",
                "description": "sample",
                "status": 3
            },
            "customerInfoVo": {
                "customerName": "Test Test",
                "customerEmail": "test@test.com",
                "customerMobilePhone": "50000000",
                "customerCountryCode": "965",
                "customerInitiated": true
            },
            "threeDSecureInfoVo": {
                "threeDSecure": true
            },
            "cardInfo": {
                "saveCard": false
            }
        }
    ]
}
```

##### 失败

```json
{
    "code": 450,
    "message": "Unrecognized channel"
}
```

```json
{
    "code": 405,
    "message": "Order not found",
    "accountId": "1003",
    "channel": "tap"
}
```

```json
{
    "code": 406,
    "message": "Merchant not found",
    "accountId": "10031",
    "channel": "tap"
}
```

```json
{
    "code": 500,
    "message": "System error"
}
```

#### 响应参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| code | M | String | 返回码 |
| message | M | String | 返回描述 |
| accountId | C | String | 授权账户号，商户注册时由OPGS系统提供 |
| channel | C | String | 渠道标识，详见**渠道与交易方式说明**。 |
| data | C | arrays of object | 查询数据集合 |


##### data
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| tradeNo | M | String | 订单号 |
| reference | M | String | 参考号 |
| transactionInfo | M | object | 交易信息 |
| merchantInfo | C | object | 商户信息 |
| customerInfo | C | object | 子商户信息 |
| shippingInfo | C | object | 订单信息 |
| billingInfo | C | object | 账单信息 |
| riskInfo | C | object | 风险信息 |
| cardInfo | C | object | 卡信息 |
| threeDSecureInfo | C | object | 3DS信息 |
| additionalInfo | C | object | 附加信息 |


##### transactionInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| amount | M | BigDecimal | 交易金额 |
| countryCode | M | String | 国家代码 |
| currency | M | String | 交易币种 |
| paymentMethod | M | String | 交易方式，详见**渠道与交易方式说明**。 |
| callbackUrl | C | String | 通知回调地址 |
| redirectUrl | C | String | 交易完成后跳转的重定向地址。 |
| description | C | String | 描述 |
| status | M | Integer | 订单状态。枚举：<br />0 处理中<br />1 成功<br />2 拒绝<br />3 失败 |


##### merchantInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| merchantId | C | String | 商户id。 |


##### customerInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| customerId | C | String | 客户id |
| customerName | C | String | 客户的名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| customerCompanyName | C | String | 客户公司名称 |
| customerType | C | String | 客户类型 |
| customerEmail | C | String | 客户的电子邮件地址 |
| customerHomePhone | C | String | 客户的家庭电话号码 |
| customerMobilePhone | C | String | 客户的手机号码 |
| customerCountryCode | C | String | 语言和国家/地区。根据 ISO 639 和 ISO 3166 |
| customerInitiated | C | boolean | 此参数确定费用是否由客户发起 |
| customerDocumentId | C | String | 证件号码 |
| customerDocumentType | C | String | 证件类型，暂时支持CPF或者CNPJ |


##### shippingInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| orderCurrency | C | String | 订单币种 |
| shippingName | C | String | 名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| shippingPhone | C | String | 电话号码，需包含“+55”的前缀。 |
| shippingEmail | C | String | 邮箱地址 |
| shippingStreet | C | String | 街道 |
| shippingNumber | C | String | 门牌号 |
| shippingComplement | C | String | 补充信息 |
| shippingDistrict | C | String | 区县 |
| shippingCity | C | String | 城市 |
| shippingState | C | String | 州 |
| shippingZipCode | C | String | 邮政编码 |
| shippingCountry | C | String | 国家代码 |
| shippingHomePhone | C | String | 收货人家庭电话号码 |


##### billingInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| billingName | C | String | 账单接收人名称，姓名与姓氏用空格隔开，格式：firstname lastname，例如：John Smith |
| billingZipCode | C | String | 账单接收人邮编 |
| billingState | C | String | 账单接收人所在州 |
| billingCity | C | String | 账单接收人所在城市 |
| billingDistrict | C | String | 账单接收人所在区域 |
| billingStreet | C | String | 账单接收人所在街道 |
| billingNumber | C | String | 账单接收人所在门牌号 |
| billingComplement | C | String | 账单接收人地址补充信息 |
| billingCountry | C | String | 账单接收人所在国家 |
| billingHomePhone | C | String | 账单接收人家庭电话号码 |
| billingMobilePhone | C | String | 账单接收人手机号码 |


##### riskInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| ipAddress | C | String | 请求原ip地址 |


##### cardInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| saveCard | C | boolean | 是否保存卡信息，默认false |


##### threeDSecureInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| threeDSecure | C | boolean | 是否使用3ds，默认ture |


##### additionalInfo
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| paymentFlow | C | String | 交易流程, 如DIRECT 或 REDIRECT |
| totalAmount | C | BigDecimal | 全部订单金额，包括税费和运费 |
| vatAmount | C | BigDecimal | 税费 |


### 渠道可选值枚举说明
| 渠道 | channel值 |
| --- | --- |
| tap | tap |

# 退款接口

注：授权订单已结算，可使用退款接口退款已结算订单，未结算订单不能退款

## http请求

```
post	/opgs/refund
```

## Request Headers

```json
{
    "Content-Type": "application/json",
    "authorization": "{{authorization}}"
}
```

### http Headers字段说明
| key | value | 说明 |
| --- | --- | --- |
| Content-Type | "application/json" | 请求类型 |
| authorization | "{{authorization}}" | 需要提供的authorization字段，字段说明详见：authorization生成方式 |


## Request Body

```json
{
    "data":"encryptData"
}
```

### 请求参数示例

```json
{
    "accountId": "1003",
    "channel": "worldpay",
    "merchantInfo": {
        "idCode": "SWIFTPASSECOMFSUSD"	
    },
    "transactionInfo": {
        "tradeNo": "20231102111111957734",
        "amountTransaction": "1000",
        "transactionCurrencyCode": "HKD",
        "description": "其他"
    }
}
```

### 请求参数说明

#### data
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| accountId | M | String | all | 授权账户号，商户注册时由OPGS系统提供 |
| channel | M | String | all | 渠道标识 |
| merchantInfo | C | JSON | worldpay | 商户信息，worldpay渠道需要提供 |
| transactionInfo | M | JSON | all | 交易信息 |


##### merchantInfo
| 字段 | 要求 | 类型 | **渠道** | 说明 |
| --- | --- | --- | --- | --- |
| idCode | M | String | worldpay | 商户号 |


##### transactionInfo
| 字段 | 要求 | 类型 | 渠道 | 说明 |
| --- | --- | --- | --- | --- |
| tradeNo | M | String | all | 原始结算订单的订单流水号 |
| amountTransaction | M | Integer | all | 退款金额，数值不能大于已结算金额 |
| transactionCurrencyCode | M | String | all | 交易货币代码，ISO三位数字代码，worldpay渠道使用ISO4217的3位货币代码，必须为大写 |
| description | C | String | myMoney | 退款描述，myMoney需提供 |


### 响应参数示例

#### 成功

```json
{
	"code":200,
	"message": "succeed",
    "data":"encryptData"
}
```

##### data

```json
{
      "accountId": "1003",
      "tradeNo":"20230926110923350909",
      "reference": "20230926110923350910"
}
```

#### 失败

```json
{
    "code": 422,
    "message": "Invalid data was sent",
    "data": {
        "errorData": [
            "accountId"
        ]
    }
}
```

```json
{
    "code": 501,
    "message": "UNAUTHORIZED"
}
```

### 响应参数说明
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| code | M | Integer | 响应码 |
| message | M | String | 响应消息 |
| data | C | JSON | 响应内容 |


#### data
| 字段 | 要求 | 类型 | 说明 |
| --- | --- | --- | --- |
| accountId | M | String | 授权账户号，商户注册时由OPGS系统提供 |
| tradeNo | M | String | 订单流水号，一笔交易唯一 |
| reference | M | String | 退款订单参考号，由OPGS系统生成 |
| errorData | C | JSON Array | 错误信息，请求失败时填充 |


# 查询与回调
## worldPay渠道
### 一、订单查询
#### 描述
> 商户通过账户、交易等信息可以进行查询授权等的订单信息（支持单/多条查询）

#### 请求方式
> **POST**

#### 请求地址
```
http://xxxxxx/xx/api/online/inquiry
```
#### 请求参数
##### Params：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 100000010170 |
| channel | int | 渠道 | 可选值：<br />2:WorldPayJson<br />1:WorldPay | 1 |

> channel 参数用于区分所属卡组及渠道的交易信息

##### Body:
json格式

| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| tradeNo | String(20) | 交易流水号，支持单条or多条查询 | **是** | 2542745242452 |
| merchantCode | String(64) | 商户号 | **WorldPay必填** |  |

#### 请求示例
##### 请求地址栏：
```json
http://xxxxxx/xx/api/online/inquiry?accountId=100000010170&channel=1
```
##### 单条数据查询示例：
```json
{
    "merchantCode": "SWIFTPASSECOMHKD",
    "tradeNo": [
        "20230810150868450474"
    ]
}
```

##### 多条数据查询示例：

```json
{
    "merchantCode": "SWIFTPASSECOMHKD",
    "tradeNo": [
        "20230810150868450474",
        "987654321852"
    ]
}
```

##### 对于channel = 2的查询,示例如下

```json
{
    "merchantCode": "SWIFTPASSECOMHKD",
    "tradeNo": [
        "20230810150868450474"
    ]
}
```

#### 响应参数

数据格式：json

| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| status | String(3) | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#statusCode) | 是 | 200 |
| msg | String(50) | 请求消息 | 是 | query success |
| data | String(max) | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#authInfo)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#responseExample) |

> 解密密钥：RSA的私钥，可联系相关人员获取


#### 解密后的信息
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| tradeNo | String(20) | 流水号，唯一 | 是 |  |
| amountTransaction | String(12) | 交易金额，整数 | 是 | 见示例 |
| debitCreditIndicator | String(10) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 当为WorldPay，则会返回 | 见示例 |
| authorizationIDResponse | String(6) | 授权ID响应 | 否，当授权成功则会有该值 |  |
| amountSettlement | String(12) | 结算金额 | 否 |  |
| amountCardNumberBilling | String(12) | 持卡人账单金额 | 否 |  |
| conversionSettlementRate | String(12) | 结算转换汇率 | 否 |  |
| conversionCardHolderBillRate | String(8) | 交易金额与持卡人币种的转换率 | 若填写了 amountCardNumberBilling ，则为必填 |  |
| dateConversion | String(4) | 应用的转换汇率的日期 | 否 |  |
| amountTransactionFee | String | 交易费用 | 否 |  |
| retrievalReferenceNumber | String | 检索参考编号 | 否 |  |
| cardAcceptorTerminalID | String | 卡接受器终端ID | 否 |  |
| currencyCodeSettlement | String(3) | 货币代码，结算 | 否 |  |
| currencyCodeCardHolder | String | 货币代码，持卡人账单 | 否 |  |
| transactionCurrencyCode | String | 交易币种 | WorldPay则会返回 |  |
| thirdPartyReference | String(200) | 第三方关联信息（用于跟踪发卡行延迟退款的情况） | 否 |  |
| cvcResultCode | String | CVC验证结果 | 否 |  |
| avsResultCode | String | AVS验证结果 | 否 |  |
| aavAddressResultCode | String | AAV验证地址结果 | 否 |  |
| aavPostcodeResultCode | String | AAV邮箱结果验证结果 | 否 |  |
| aavCardholderNameResultCode | String | AAV持卡人姓名验证结果 | 否 |  |
| aavTelephoneResultCode | String | AAV手机号验证结果 | 否 |  |
| aavEmailResultCode | String | AAV邮箱验证结果 | 否 |  |
| OCTTxnId | String(20) | 特定交易的唯一标识 | 否 |  |
| riskScore | String(3) | 风控评分 | 否 |  |
| orderBalanceInfos | array | 各交易类型当前的可操作余额 | 当WorldPay，且有可用余额时，则返回 |  |
| journalType | String | 记录类型 | 当WorldPay时，则会返回 |  |
| journalSent | String | 是否已发送 | 当WorldPay时，则会返回 |  |
| orderAmountRecords | array | 付款状态和资金流动的有用信息 | 当WorldPay时，则会返回 |  |
| orderReferences | array | 关联信息，当Capture，退款时有传则会返回 | 当WorldPay时，则会返回 |  |
| bookingDate | String | 操作时间，格式20230801 | 当WorldPay时，则会返回 |  |
| accountNum | String(21) | 卡号 | 是 | 44441111 |
| cardholderCountryCode | String(3) | 持卡人国家代码 | 否 | 见示例 |
| cardType | String(10) | 卡类型，credit or debit | 否 | creditcard |
| dateExpiration | String(10) | 卡有效日期 | 否 | 202307 |
| cardholderName | String | 持卡人名称 | 否 |  |
| issuerCountryCode | String | 发卡行国家代码 | 否 |  |
| issuerName | String | 发卡行名称 | 否 | CARD_ISSUER_NAME |
| cardProductType | String | 卡账户类型 | 否 | Commercial Corporate Card |
| additionalResponseData | String | 其他响应数据 | 否 | 见示例 |
| additionalDataPU | String | 其他数据-私有使用 | 否 | 见示例 |
| additionalAmounts | String | 额外金额 | 否 | 见示例 |
| iicSystemReletedData | String | 集成电路卡(ICC)系统相关数据 | 否 | 见示例 |
| paymentAccountData | String | 支付账户数据 | 否 |  |
| INFData | String | 中间网络设施(INF)数据 | 否 |  |
| accountId1 | String | 帐户ID-1 | 否 |  |
| accountId2 | String | 帐户ID-2 | 否 |  |
| additionalTransactionReferenceData | String | 其他事务处理参考数据 | 否 |  |
| additionalTransactionNu | String | 其他数据（国家用途） | 否 |  |
| recordData | String | 记录数据 | 否 |  |
| authorizingAgentIDCode | String | 授权代理ID代码 | 否 |  |
| receiptFreeText | String | 接收免费文本 | 否 |  |
| memberDefinedData | String | 成员定义的数据 | 否 |  |
| privateData | String | 私人[专用]数据 | 否 |  |
| acquirerInstitutionIdCode | String | 收单行机构号 | 否 | 见示例 |
| forwardIdCode | String | 转发机构号 | 否 | 见示例 |
| merchantEntity | String | 商户实体 | 否 |  |
| mcc | String | 商户类型码 | 否 |  |
| pfId | String | 支付服务商id | 否 |  |
| subMerchantId | String | 子商户id | 否 |  |
| subMerchantName | String | 子商户名称 | 否 |  |
| subMerchantPostalCode | String | 子商户邮编 | 否 |  |
| subMerchantStreet | String | 子商户街道地址 | 否 |  |
| subMerchantState | String | 子商户地区/省份 | 否 |  |
| subMerchantCity | String | 子商户城市 | 否 |  |
| subMerchantCountryCode | String | 子商户国家代码（三位数字） | 否 |  |
| subMerchantTaxId | String | 子商户税号 | 否 |  |
| subMerchantEmail | String | 子商户邮编 | 否 |  |
| subMerchantTelephone | String | 子商户电话 | 否 |  |
| channel | String | 支付渠道表示持卡人与商家的互动 | 否 |  |
| instructionNarrativeLine1 | String | 商户落款 | 否 |  |
| instructionNarrativeLine2 | String | 有关支付的其他详情，e.g.订单号、电话号码。 | 否 |  |
| instrumentType | String | 付款类型说明 | 否 |  |
| debtRepayment | String | DRI 是为偿还债务而标记某项支付的标志。可能的值：true（仅在 true 时提供） | 否 |  |
| cvc | String | CVC 是卡背面上的一组 3 或 4 个数字的独特号码 | 否 |  |
| billingAddress1 | String | 账单地址1 | 否 |  |
| billingAddress2 | String | 账单地址2 | 否 |  |
| billingAddress3 | String | 账单地址3 | 否 |  |
| billingCity | String | 账单城市 | 否 |  |
| billingCountryCode | String | 账单国家(字母) | 否 |  |
| billingState | String | 账单省份/地区 | 否 |  |
| billingPostalCode | String | 账单邮编 | 否 |  |
| walletToken | String | 钱包token，由钱包传输过来 | 否 |  |
| cardExpiryDateMonth | String | 卡过期月份 | 否 |  |
| cardExpiryDateYear | String | 卡过期年份 | 否 |  |
| authenticationVersion | String | 授权版本，对于 3DS1 - 1.0.2 对于 3DS2 - 2.1.0 或 2.2.0 | 否 |  |
| authenticationType | String | 授权类型（3DS） | 否 |  |
| authenticationEci | String | 电子商务指标 (ECI) | 否 |  |
| authenticationAuthenticationValue | String | 3DS 验证结果证据的密码值 | 否 |  |
| authenticationTransactionId | String | 由发行机构生成的独特身份验证交易识别码 | 否 |  |
| status | number | 付款状态0：处理中，1：成功，2：拒绝 3.失败 | 否 |  |
| errorCode | String | 拒绝码 | 否 |  |
| errorMessage | String | 拒绝的消息 | 否 |  |
| payType | number | 交易类型 | 否 |  |
| countType | number | 交易量型【1：全量 2：部分】 | 否 |  |
| tdsIdentity | number | 3ds标识【0：非3ds 1：3ds】 | 否 |  |
| reference | String | 参考号 | 否 |  |


##### orderBalanceInfos参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| balanceAccountType | String(20) | 账户类型（可体现在交易的流程） | 是 | 见示例 |
| balanceAmount | String(12) | 金额，整数 | 是 | 12.00 |
| balanceCurrencyCode | String(3) | 币种 | 是 | HKD |
| balanceDebitCreditIndicator | String(6) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 是 | credit |


##### orderAmountRecords参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountType | String(20) | 账户交易详情 | 是 | AUTHORISED |
| batchId | String(12) | 处理批次 | 是 | 29 |
| amount | String(12) | 金额 | 是 | 1200 |
| currencyCode | String(3) | 币种 | 是 | HKD |
| debitCreditIndicator | String(6) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 是 | credit |


##### orderReferences参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| type | String(10) | 关联信息所在支付流程 | 是 | capture |
| reference | String(12) | 关联信息内容 | 是 | capture关联号等自定义信息 |


#### 响应示例
```
{
    "status": "200",
    "msg": "query success",
    "data": "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"
}
```
#### data解密后的信息

```json
[
    {
          "accountNum":"4444********1111",
          "cardType":"creditcard",
          "dateExpiration":"202307",
          "cardBrand":"VISA",
          "cardholderName":"****",
          "issuerCountryCode":"N/A",
          "issuerName":"CARD_ISSUER_NAME",
          "cardProductType":"Commercial Corporate Card",
          "amountTransaction":"5200",
        "debitCreditIndicator":"credit",
        "transactionCurrencyCode":"HKD",
        "cvcResultCode":"A",
        "avsResultCode":"A",
        "aavAddressResultCode":"A",
        "aavPostcodeResultCode":"A",
        "aavCardholderNameResultCode":"A",
        "aavTelephoneResultCode":"A",
        "aavEmailResultCode":"A",
        "orderBalanceInfos":[
         {
            "balanceAccountType":"IN_PROCESS_AUTHORISED",
            "balanceAmount":"5200",
            "balanceCurrencyCode":"HKD",
            "balanceDebitCreditIndicator":"credit"
            }
        ],
        "riskScore":"0",
        "journalType":"AUTHORISED",
        "journalSent":"N",
        "bookingDate":"20230801",
        "orderAmountRecords":[{
                "accountType":"IN_PROCESS_AUTHORISED",
                "batchId":"30",
                "amount":"5200",
                "currencyCode":"HKD",
           		"debitCreditIndicator":"credit"
            }],
        "orderReferences":[{
                "type":"refund",
                "reference":"YourReference"
            }]     
      }
]
```
### 二、异步回调

#### 描述

> 当渠道为WorldPay时，且商户提供了回调地址，我们则会发送支付结果


#### 商户提供地址

> 该地址必须为HTTPS，且外网可访问


#### 异步回调参数
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 100000010170 |
| merchantCode | String(64) | 商户ID | **当渠道为WorldPay时，为必填** |  |
| channel | int | 渠道 | 可选值：<br />0:Master<br />1:WorldPay-xml<br />2:WorldPay-json | 1 |
| data | text | 交易详细数据 | 使用RSA私钥进行解密 | 这个待确认是要明文还是要密文 |

#### data解密后的信息
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| tradeNo | String(20) | 流水号，唯一 | 是 |  |
| processingCode | String(6) | 交易信息 | 当为Master，则会返回 | 见示例 |
| amountTransaction | String(12) | 交易金额，整数 | 是 | 见示例 |
| debitCreditIndicator | String(10) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 当为WorldPay，则会返回 | 见示例 |
| dateAndTimeTransmission | String(10) | 交易传输时间<br />日期格式:**MMDDhhmmss** | 当为Master，则会返回 |  |
| systemTraceAuditNumber | String(6) | 交易追踪号6为数字的唯一值，响应返回会返回同样的值 | 当为Master，则会返回 |  |
| dateSettlement | String(20) | 结算日期 | 当为Master，则会返回 |  |
| authorizationIDResponse | String(6) | 授权ID响应 | 否，当授权成功则会有该值 |  |
| networkData | String(10) | 网络数据 | 当为Master，则会返回 |  |
| amountSettlement | String(12) | 结算金额 | 否 |  |
| amountCardNumberBilling | String(12) | 持卡人账单金额 | 否 |  |
| conversionSettlementRate | String(12) | 结算转换汇率 | 否 |  |
| conversionCardHolderBillRate | String(8) | 交易金额与持卡人币种的转换率 | 若填写了 amountCardNumberBilling ，则为必填 |  |
| dateConversion | String(4) | 应用的转换汇率的日期 | 否 |  |
| amountTransactionFee | String | 交易费用 | 否 |  |
| retrievalReferenceNumber | String | 检索参考编号 | 否 |  |
| cardAcceptorTerminalID | String | 卡接受器终端ID | 否 |  |
| currencyCodeTransaction | String | 货币代码，交易币种 | 当为Master，则会返回 |  |
| currencyCodeSettlement | String(3) | 货币代码，结算 | 否 |  |
| currencyCodeCardHolder | String | 货币代码，持卡人账单 | 否 |  |
| transactionCurrencyCode | String | 交易币种 | WorldPay则会返回 |  |
| thirdPartyReference | String(200) | 第三方关联信息（用于跟踪发卡行延迟退款的情况） | 否 |  |
| cvcResultCode | String | CVC验证结果 | 否 |  |
| avsResultCode | String | AVS验证结果 | 否 |  |
| aavAddressResultCode | String | AAV验证地址结果 | 否 |  |
| aavPostcodeResultCode | String | AAV邮箱结果验证结果 | 否 |  |
| aavCardholderNameResultCode | String | AAV持卡人姓名验证结果 | 否 |  |
| aavTelephoneResultCode | String | AAV手机号验证结果 | 否 |  |
| aavEmailResultCode | String | AAV邮箱验证结果 | 否 |  |
| OCTTxnId | String(20) | 特定交易的唯一标识 | 否 |  |
| riskScore | String(3) | 风控评分 | 否 |  |
| orderBalanceInfos | array | 各交易类型当前的可操作余额 | 当WorldPay，且有可用余额时，则返回 |  |
| journalType | String | 记录类型 | 当WorldPay时，则会返回 |  |
| journalSent | String | 是否已发送 | 当WorldPay时，则会返回 |  |
| orderAmountRecords | array | 付款状态和资金流动的有用信息 | 当WorldPay时，则会返回 |  |
| orderReferences | array | 关联信息，当Capture，退款时有传则会返回 | 当WorldPay时，则会返回 |  |
| bookingDate | String | 操作时间，格式20230801 | 当WorldPay时，则会返回 |  |
| accountNum | String(21) | 卡号 | 是 | 44441111 |
| cardholderCountryCode | String(3) | 持卡人国家代码 | 否 | 见示例 |
| cardType | String(10) | 卡类型，credit or debit | 否 | creditcard |
| dateExpiration | String(10) | 卡有效日期 | 否 | 202307 |
| cardholderName | String | 持卡人名称 | 否 |  |
| issuerCountryCode | String | 发卡行国家代码 | 否 |  |
| issuerName | String | 发卡行名称 | 否 | CARD_ISSUER_NAME |
| cardProductType | String | 卡账户类型 | 否 | Commercial Corporate Card |
| additionalResponseData | String | 其他响应数据 | 否 | 见示例 |
| additionalDataPU | String | 其他数据-私有使用 | 否 | 见示例 |
| additionalAmounts | String | 额外金额 | 否 | 见示例 |
| iicSystemReletedData | String | 集成电路卡(ICC)系统相关数据 | 否 | 见示例 |
| paymentAccountData | String | 支付账户数据 | 否 |  |
| INFData | String | 中间网络设施(INF)数据 | 否 |  |
| accountId1 | String | 帐户ID-1 | 否 |  |
| accountId2 | String | 帐户ID-2 | 否 |  |
| additionalTransactionReferenceData | String | 其他事务处理参考数据 | 否 |  |
| additionalTransactionNu | String | 其他数据（国家用途） | 否 |  |
| recordData | String | 记录数据 | 否 |  |
| authorizingAgentIDCode | String | 授权代理ID代码 | 否 |  |
| receiptFreeText | String | 接收免费文本 | 否 |  |
| memberDefinedData | String | 成员定义的数据 | 否 |  |
| privateData | String | 私人[专用]数据 | 否 |  |
| acquirerInstitutionIdCode | String | 收单行机构号 | 否 | 见示例 |
| forwardIdCode | String | 转发机构号 | 否 | 见示例 |
| merchantEntity | String | 商户实体 | 否 | worldpayJson可能存在 |
| mcc | String | 商户类型码 | 否 | worldpayJson可能存在 |
| pfId | String | 支付服务商id | 否 | worldpayJson可能存在 |
| subMerchantId | String | 子商户id | 否 | worldpayJson可能存在 |
| subMerchantName | String | 子商户名称 | 否 | worldpayJson可能存在 |
| subMerchantPostalCode | String | 子商户邮编 | 否 | worldpayJson可能存在 |
| subMerchantStreet | String | 子商户街道地址 | 否 | worldpayJson可能存在 |
| subMerchantState | String | 子商户地区/省份 | 否 | worldpayJson可能存在 |
| subMerchantCity | String | 子商户城市 | 否 | worldpayJson可能存在 |
| subMerchantCountryCode | String | 子商户国家代码（三位数字） | 否 | worldpayJson可能存在 |
| subMerchantTaxId | String | 子商户税号 | 否 | worldpayJson可能存在 |
| subMerchantEmail | String | 子商户邮编 | 否 | worldpayJson可能存在 |
| subMerchantTelephone | String | 子商户电话 | 否 | worldpayJson可能存在 |
| channel | String | 支付渠道表示持卡人与商家的互动 | 否 | worldpayJson可能存在 |
| instructionNarrativeLine1 | String | 商户落款 | 否 | worldpayJson可能存在 |
| instructionNarrativeLine2 | String | 有关支付的其他详情，e.g.订单号、电话号码。 | 否 | worldpayJson可能存在 |
| instrumentType | String | 付款类型说明 | 否 | worldpayJson可能存在 |
| debtRepayment | String | DRI 是为偿还债务而标记某项支付的标志。可能的值：true（仅在 true 时提供） | 否 | worldpayJson可能存在 |
| cvc | String | CVC 是卡背面上的一组 3 或 4 个数字的独特号码 | 否 | worldpayJson可能存在 |
| billingAddress1 | String | 账单地址1 | 否 | worldpayJson可能存在 |
| billingAddress2 | String | 账单地址2 | 否 | worldpayJson可能存在 |
| billingAddress3 | String | 账单地址3 | 否 | worldpayJson可能存在 |
| billingCity | String | 账单城市 | 否 | worldpayJson可能存在 |
| billingCountryCode | String | 账单国家(字母) | 否 | worldpayJson可能存在 |
| billingState | String | 账单省份/地区 | 否 | worldpayJson可能存在 |
| billingPostalCode | String | 账单邮编 | 否 | worldpayJson可能存在 |
| walletToken | String | 钱包token，由钱包传输过来 | 否 | worldpayJson可能存在 |
| cardExpiryDateMonth | String | 卡过期月份 | 否 | worldpayJson可能存在 |
| cardExpiryDateYear | String | 卡过期年份 | 否 | worldpayJson可能存在 |
| authenticationVersion | String | 授权版本，对于 3DS1 - 1.0.2 对于 3DS2 - 2.1.0 或 2.2.0 | 否 | worldpayJson可能存在 |
| authenticationType | String | 授权类型（3DS） | 否 | worldpayJson可能存在 |
| authenticationEci | String | 电子商务指标 (ECI) | 否 | worldpayJson可能存在 |
| authenticationAuthenticationValue | String | 3DS 验证结果证据的密码值 | 否 | worldpayJson可能存在 |
| authenticationTransactionId | String | 由发行机构生成的独特身份验证交易识别码 | 否 | worldpayJson可能存在 |
| status | number | 付款状态0：处理中，1：成功，2：拒绝 3.失败 | 否 | worldpayJson可能存在 |
| errorCode | String | 拒绝码 | 否 | worldpayJson可能存在 |
| errorMessage | String | 拒绝的消息 | 否 | worldpayJson可能存在 |
| payType | number | 交易类型 | 否 | worldpayJson可能存在 |
| countType | number | 交易量型【1：全量 2：部分】 | 否 | worldpayJson可能存在 |
| tdsIdentity | number | 3ds标识【0：非3ds 1：3ds】 | 否 | worldpayJson可能存在 |
| reference | String | 部分请款/退款参考号 | 否 | worldpayJson可能存在 |

##### orderBalanceInfos参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| balanceAccountType | String(20) | 账户类型（可体现在交易的流程） | 是 | 见示例 |
| balanceAmount | String(12) | 金额，整数 | 是 | 12.00 |
| balanceCurrencyCode | String(3) | 币种 | 是 | HKD |
| balanceDebitCreditIndicator | String(6) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 是 | credit |


##### orderAmountRecords参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountType | String(20) | 账户交易详情 | 是 | AUTHORISED |
| batchId | String(12) | 处理批次 | 是 | 29 |
| amount | String(12) | 金额 | 是 | 1200 |
| currencyCode | String(3) | 币种 | 是 | HKD |
| debitCreditIndicator | String(6) | 金额为正数（"credit"）或负数（"debit"）；可选值： credit，debit | 是 | credit |


##### orderReferences参数：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| type | String(10) | 关联信息所在支付流程 | 是 | capture |
| reference | String(12) | 关联信息内容 | 是 | capture关联号等自定义信息 |


#### 异步回调示例

```json
{
    "status": "200",
    "msg": "query success",
    "data": "F4+G5cIDqZUz+Jm1Guh2jHsJRhrjwbC2XGIkALsKw4y1ZKt8COj7jyAymE3sXD0TLy6HcyLjK1ZeaXoHRk0bFoWVTL2Y7IM1HYNSh2TsHoCQTTQzvLVKzOCN+wejhelumcDyxKnNIFP7LTbSYkifYsh3x8ndm6yWjJEI+96Uv1TMSo8FHtMOsWSTu4nzPpnGEq+h8t73fjImHud/3jX3pFbnBHt2V09b2pCMxHG46NnuNSrs4S2yWL3GsU6iUDbz/ftDN/TrDcfI9yTLzvwUUKLcvE9uGL9qYyt6eFmp2qQ5GZ8+86ieB4j1w1pFelKEkMi0eglNpj/6KM2ZhHbWZzvMuo2+ihhCwuUt3hmSUx46rzY3xdrFBQ38iNEc0mBY7WIxOyNJDD6Z1CKjoJwecTwhaAzuf+MjLzoh11sAnuEtURJK+GIkObKAwXsXJxUilV8yQg1n0m5jJ2cJAQlfEY8qqwLfR2WrZPTVoGGD0Cd85ppfyYUXnaFS4yIVpSi8VWI2HlX28ok+Y+PX03ks6MWGV0jnh2kDWm4MmWDtvQ7R9TvFSruCBk2fAl78dXzWYo97lox8Ot/A2HQxD2ns+K2xfx8dsy0l0Pttq9EOgrS7PKhTlPcE04lar8p4zuVBx1J8249EbYCnQTtfRI6jv5j/OJqxnDcPJP+FHvNeOoKbMujIjepV0tEMvn0jKCIWyqSEwEQzi+Ndu4E2aRZ8Rgriw291lEASRBe6Q6UTPKF5cKXG7AtMOVVFrrl/iD3y0BxnMeUXP1RDI1AlnqW8NfuBoj5LvMaL5JNrEa93yePMvJoiNf/n6YSpSQqNIx0fXSWayLRta9wpNV+V5SBatRqGCG2vhWVw5UymaTN2ycYgRkI7HW2bcBMKI76+Mvk+rc11z6dqvRiV0MU9gbNpXTELCDK9Kk0TeMMHL7c/+taHJQpNx67VFhpX9sG67i7LRqvjKkdj5Jg7ofUTFRxEeQqKaL3EI2nC6BQ0vl0oHoxTMXecnxHRuDVsCSHLGkTfQCXPMtpBv3bsdbLJEto7bTf+bol7y/6AFclcUgpnjhTyvGa5qAKKR+cNw0IHx0L32liRBUJfkgh7f9QgHToZ5zuLh0v+BD86tDGzOrAU3hTDWgbhDzTgbVVoGIHUdisrKjT/wP6xj2htbCKyMn2IYqfBSdz7gqdSNNgJFO/AYR4IIK38Hw2kIHpc+jKgaUtJVnonZ21ndsH0WHjxaGMH/H0aRLd8LfAXhWSo81/++shbs6lkznlYFcVZzbUosOeLLROT4GKG9fj8RzKBoKvZEoBklc6ZnWCTmF1ycCUmk5xzju8u+h076JTtTRp4oo2IJ9aOfnS22aGhjq6Fyk6vYQJ4RbYxekD84h6ANcfJQyeS/vrBdlPxCvbhhAMLEbH8xYiHlaM4is762bFYKHpH7T/1JoY7OAviGJczbZ0G5M2NXtT9nMi7aIAQj2rgNjGtdbEItwHfl+o8bx3bhZpPYhlnXhvxhZF7caE5NncUblPw5YbRnmLwFNeDVDNsEFQnv/vfvG9hH2d21H1ZigJ"
```

data解密后数据

```json
[
    {
          "accountNum":"4444********1111",
          "cardType":"creditcard",
          "dateExpiration":"202307",
          "cardBrand":"VISA",
          "cardholderName":"****",
          "issuerCountryCode":"N/A",
          "issuerName":"CARD_ISSUER_NAME",
          "cardProductType":"Commercial Corporate Card",
          "amountTransaction":"5200",
        "debitCreditIndicator":"credit",
        "transactionCurrencyCode":"HKD",
        "cvcResultCode":"A",
        "avsResultCode":"A",
        "aavAddressResultCode":"A",
        "aavPostcodeResultCode":"A",
        "aavCardholderNameResultCode":"A",
        "aavTelephoneResultCode":"A",
        "aavEmailResultCode":"A",
        "orderBalanceInfos":[
         {
            "balanceAccountType":"IN_PROCESS_AUTHORISED",
            "balanceAmount":"5000",
            "balanceCurrencyCode":"HKD",
            "balanceDebitCreditIndicator":"credit"
            }
        ],
        "riskScore":"0",
        "journalType":"AUTHORISED",
        "journalSent":"N",
        "bookingDate":"20230801",
        "orderAmountRecords":[{
                "accountType":"IN_PROCESS_AUTHORISED",
                "batchId":"30",
                "amount":"5000",
                "currencyCode":"HKD",
           		"debitCreditIndicator":"credit"
            }],
        "orderReferences":[{
                "type":"refund",
                "reference":"YourReference"
            }]     
      }
]
```

#### 商户应答

> 商户在收到回调应该在30s内响应，HTTP Code:200  Response Body:SUCCEED，

## liquido渠道

### 交易通知数据同步下发

#### 描述

> 当渠道为liquido时，且商户订单参数传递了下发地址，则会发送支付结果


#### 商户提供地址

> 该地址必须为HTTPS，且外网可访问


#### 异步回调参数
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| eventType | String | Notification 事件类型 | **是** | CHARGE_SUCCEEDED, |
| data | JSON | 下发数据 | **是** |  |


#### data数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| chargeDetails | JSON | 下发事件详情 | **是** |  |


#### chargeDetails数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| transferStatusCode | integer | 此次交易的状态码, 200表示交易成功、进行中或预授权成功, 其他代表失败 | 是 | CHARGE_SUCCEEDED, |
| transferErrorMsg | String | 此次交易失败原因 | 是 |  |
| idempotencyKey | String | 此参数用于幂等目的，交易的唯一标识ID 长度不大于128个字节。 | 是 | 1ec983fa-1a37-679b-809b-067861d87ab0 |
| referenceId | String | 此次交易的ID | 是 | 1ec983fa-1a37-679b-809b-067861d87ab0 |
| paymentMethod | String | 支付方式. 枚举值, CREDIT_CARD | 是 | CREDIT_CARD |
| amount | integer | 收款金额 | 是 | 200 |
| currency | String | 收款货币单位，长度为3个字节，BRL | 是 | BRL |
| country | String | 国家编码 BR | 是 | BR |
| finalAmount | integer | 用于创建收款订单的最终金额。对于需要转换汇率的收款订单，这个字段的值表示源请求金额被转换后的金额。 | 是 | 100 |
| finalCurrency | String | finalAmount的收款货币单位 | 是 | BRL |
| createTime | String | 创建时间 | 是 | 2022-03-01 17:53:18 GMT-08:00 |
| scheduledTime | String | 执行时间 | 是 | 2022-03-01 17:53:18 GMT-08:00 |
| finalStatusTime | String | 最终状态更新时间. 最终状态，包括"REFUNDED", "FAILED" | 是 | 2022-03-01 17:53:18 GMT-08:00 |
| payer | JSON | 付款人信息 | 是 |  |
| transferDetails | JSON | 交易类型的详细信息 | 是 |  |
| transferStatus | String | 汇款状态，枚举值，"SETTLED", "FAILED", "IN_PROGRESS", "AUTHORIZED" | 是 |  |
| description | String | 描述信息 | 否 |  |
| callbackUrl | String | 商户接收通知的回调地址. 接收http的get请求. | 否 |  |
| subMerchantId | String | 子商户ID。 | 否 |  |


#### payer数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| name | String | 名字，全称（入参名字规范：只允许大小写字母，数字和空格的组合。请勿包含西班牙语和葡萄牙语字母，以及其他特殊字符。） | 否 | username |
| email | String | 邮箱。 | 否 | [username@liquido.com](mailto:username@liquido.com) |
| phone | String | 电话号码。 | 否 | 12345678901 |
| document | JSON | 可确认身份的证件，例如巴西居民的CPF. | 否 |  |
| billingAddress | JSON | 账单邮寄地址. | 否 |  |


#### document数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| documentId | String | 证件号码 | 否 | 53033315550 |
| type | String | 证件类型，暂时支持CPF或者CNPJ | 否 | CPF |


#### billingAddress数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| zipCode | String | 邮政编码，如巴西的CEP | 否 | 04849333 |
| state | String | 州，缩写，如圣保罗是SP | 否 | SP |
| city | String | 城市 | 否 | sao paulo |
| district | String | 区县. | 否 | Jardim Gaivotas |
| street | String | 街道. | 否 | Rua 9 de setembro |
| number | String | 门牌号. | 否 | 15 |
| complement | String | 补充信息. | 否 | casa |
| country | String | 国家代码. | 否 | BR |


#### transferDetails数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| cardInfo | JSON | 卡详细信息 | 否 |  |
| cardId | String | 卡ID, 使用卡信息支付时不返回，用卡ID支付时会返回 | 否 | 8736453a-dad0-4f26-85f0-95e8787d0237 |
| installments | Integer | 分期付款次数 | 否 | 6 |


#### cardInfo数据说明
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| cardHolderName | String | 持卡人姓名 | 否 | card_holder_name |
| bin | String | 卡号前6位数字 | 否 | 458124 |
| last4 | String | 卡号后4位数字 | 否 | 0000 |
| brand | String | 信用卡组织. 如 VISA, MASTERCARD | 否 | VISA |
| expirationMonth | Integer | 1位数或者2位数的信用卡有效期月份。 | 否 | 9 |
| expirationYear | Integer | 信用卡有效期年份，4位数。 | 否 | 2024 |


#### 汇款状态说明
| **参数名称** | **描述** |
| --- | --- |
| IN_PROGRESS | 交易处理中 |
| SETTLED | 交易完成 |
| REFUNDING | 退款中 |
| REFUNDED | 已退款 |
| CHARGED_BACK | 拒付 |
| FAILED | 交易失败，异常 |


#### Notification 事件类型说明
| **参数名称** | **描述** |
| --- | --- |
| CHARGE_SUCCEEDED | 成功事件 |
| CHARGE_FAILED | 失败事件 |
| CHARGE_CHARGED_BACK | 拒付事件 |
| CHARGE_REFUND_SUCCEEDED | 退款成功事件 |
| CHARGE_REFUND_FAILED | 退款失败事件 |


#### 异步回调示例

```json
{
    "eventType": "CHARGE_SUCCEEDED",
    "data": {
        "chargeDetails": {
            "transferStatusCode": 200,
            "transferErrorMsg": null,
            "idempotencyKey": "1ec983fa-1a37-679b-809b-067861d87ab0",
            "referenceId": "1ec983fa-1a37-679b-809b-067861d87ab0",
            "paymentMethod": "CREDIT_CARD",
            "amount": 100,
            "currency": "BRL",
            "country": "BR",
            "finalAmount": 100,
            "finalCurrency": "BRL",
            "createTime": "2022-03-01 17:53:18 GMT-08:00",
            "scheduledTime": "2022-03-01 17:53:18 GMT-08:00",
            "finalStatusTime": null,
            "payer": {
                "name": "username",
                "email": "username@liquido.com",
                "document": {
                    "documentId": "53033315550",
                    "type": "CPF"
                },
                "phone": "12345678901",
                "billingAddress": {
                    "zipCode": "04849333",
                    "state": "SP",
                    "city": "sao paulo",
                    "district": "Jardim Gaivotas",
                    "street": "Rua 9 de setembro",
                    "number": "15",
                    "complement": "casa",
                    "country": "BR"
                }
            },
            "transferDetails": {
                "card": {
                    "cardInfo": {
                        "cardHolderName": "card_holder_name",
                        "expirationMonth": 9,
                        "expirationYear": 2024,
                        "brand": "VISA",
                        "bin": "458124",
                        "last4": "0000"
                    },
                    "cardId": "8736453a-dad0-4f26-85f0-95e8787d0237"
                }
            },
            "transferStatus": "SETTLED",
            "description": "this is a test pay",
            "callbackUrl": "https://api.client.com/callback/",
            "subMerchantId": "UUID"
        }
    }
}
```

#### 商户应答

无

### 交易通知数据异步下发

如下发失败，我们会每间隔30s发送一次，直至达到最大发送次数，不再发送；可以使用订单查询进行支付结果查询

### 订单查询-查询支付/退款状态

#### 请求方式

> POST


#### 请求URL

> /api/online/inquiry


#### 请求参数

##### Params：
| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 1003 |
| channel | int | 渠道。可选值：<br />4:liquido | **是** | 4 |


> channel 参数用于区分所属卡组及渠道的交易信息


##### Body:

> json 格式

| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| tradeNo | String | 订单号 | **是** | 0bd88f8a-2c7e-4b95-903f-8da45d0e6129 |
| reference | String | OPGS参考号，当需要针对交易状态进行查询订单时请提供此字段 | 否 |  |

##### 请求示例

###### Params：

> [http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=4](http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=4)


###### Body：

```
{   
    "tradeNo":"0bd88f8a-2c7e-4b95-903f-8da45d0e6129"
}
```
#### 响应参数
> 数据格式：json

| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| status | String(3) | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81) | 是 | 200 |
| msg | String(50) | 请求消息 | 是 | query success |
| data | String(max) | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E8%A7%A3%E5%AF%86%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%A4%BA%E4%BE%8B) |

> 解密密钥：RSA的私钥，可联系相关人员获取


#### 解密后的信息

以下是将给定的参数、数据类型和描述转换为Markdown格式的表格：
> 解析之后是一个数组，里面包含多条数据，如果只传递了 tradeNo 查询，则数组可能会返回多条数据，如果传递了 tradeNo 和 reference ，数组里包含一条数据。

| 名称 | 类型 | 必选 | 约束 | 中文名 | 说明 |
| --- | --- | --- | --- | --- | --- |
| country | string | false | none |  | 国家编码 |
| amount | integer | false | none |  | 收款金额 |
| currency | string | false | none |  | 收款货币单位，长度为3个字节 |
| finalAmount | integer | false | none |  | 用于创建收款订单的最终金额。对于需要转换汇率的收款订单，这个字段的值表示源请求金额被转换后的金额。 |
| finalCurrency | string | false | none |  | finalAmount的收款货币单位 |
| createTime | string | false | none |  | 创建时间 |
| scheduledTime | string | false | none |  | 执行时间 |
| payerName | string | false | none |  | 名字，全称（入参名字规范：只允许大小写字母，数字和空格的组合。请勿包含西班牙语和葡萄牙语字母，以及其他特殊字符。） |
| payerEmail | string | false | none |  | 邮箱 |
| payerDocumentId | string | false | none |  | 证件号码 |
| payerDocumentType | string | false | none |  | 证件类型，暂时支持CPF或者CNPJ |
| payerPhone | string | false | none |  | 证件号码 |
| billingAddressZipCode | string | false | none |  | 邮政编码，如巴西的CEP |
| billingAddressState | string | false | none |  | 州，缩写，如圣保罗是SP |
| billingAddressCity | string | false | none |  | 城市. |
| billingAddressDistrict | string | false | none |  | 区县. |
| billingAddressStreet | string | false | none |  | 街道. |
| billingAddressNumber | string | false | none |  | 门牌号. |
| billingAddressComplement | string | false | none |  | 补充信息. |
| billingAddressCountry | string | false | none |  | 国家代码. |
| finalStatusTime | string | false | none |  | 最终状态更新时间. 最终状态，包括"REFUNDED", "FAILED" |
| transferStatus | string | false | none |  | 汇款状态，枚举值，"SETTLED", "FAILED", "IN_PROGRESS" |
| transferStatusCode | integer | false | none |  | 此次交易的状态码, 200表示交易成功或者进行中, 其他代表失败 |
| transferErrorMsg | string | false | none |  | 此次交易失败原因 |
| description | string | false | none |  | 描述信息 |
| callbackUrl | string | false | none |  | 接收交易状态变更通知的地址。接收post请求。 |
| subMerchantId | string | false | none |  | 子商户ID。 |
| paymentMethod | string | false | none |  | 支付方式. 枚举值, BOLETO |
| tradeNo | string | false | none |  | 订单号 |
| reference | string | false | none |  | OPGS参考号 |
| paymentFlow | string | false | none |  | 支付流程. 枚举值, DIRECT |
| barcode | string | false | none |  | boleto-二维码 |
| digitalLine | string | false | none |  | boleto-数字线 |
| paymentDeadline | integer | false | none |  | boleto-支付期限 |
| cardId | string | false | none |  | Card-卡ID, 使用卡信息支付时不返回，用卡ID支付时会返回 |
| installments | integer | false | none |  | Card-分期付款次数 |
| cardholderName | string | false | none |  | Card-持卡人名称 |
| bin | string | false | none |  | Card-卡号前6位数字 |
| last4 | string | false | none |  | Card-卡号后4位数字 |
| brand | string | false | none |  | Card-信用卡组织. 如 VISA, MASTERCARD |
| expirationMonth | string | false | none |  | Card-信用卡有效期月份，2位数 |
| expirationYear | string | false | none |  | Card-信用卡有效期年份，4位数 |
| qrCode | string | false | none |  | pix-二维码 |
| qrCodeType | string | false | none |  | pix-二维码类型枚举，STATIC，DYNAMIC |
| expirationTime | string | false | none |  | pix-支付期限 |
| deepLink | string | false | none |  | AME-调用Ame Digital app的链接 |
| qrCodeLink | string | false | none |  | AME-二维码的链接 |
| transactionId | string | false | none |  | AME-在Ame方的交易id |
| paymentUrl | string | false | none |  | picPay-支付链接 |
| content | string | false | none |  | picPay-支付二维码的内容 |
| base64 | string | false | none |  | picPay-base64编码后的支付二维码内容 |
| checkoutProUrl | string | false | none |  | mercadoPago-mercado pago的checkout链接 |
| bankName | string | false | none |  | bankTransfer-银行名称 |
| bankCode | string | false | none |  | 银行代码，3位数字 |
| beneficiaryName | string | false | none |  | 受益人名称，例如 BRL_LIQUIDO |
| bankAccountNumber | string | false | none |  | 受益人账号 |
| bankAccountType | string | false | none |  | 受益人账号类型，例如 ContaCorrente |
| bankBranchId | string | false | none |  | 银行分行Id |
| bankIspb | string | false | none |  | 银行的ISPB, 用途和缩写bankCode一致, 8位数字 |
| bankDocumentId | string | false | none |  | 证件号码 |
| bankDocumentType | string | false | none |  | 证件类型，暂时支持CPF或者CNPJ |
| expiresAt | integer | false | none |  | OXXO-交易过期时间的时间戳 |


#### 响应示例

> 成功


```
{
  "status": "200",
  "msg": "query success",
  "data": "cBwHEJ7JJw0edTZgW7Al7eq8sVPI4hehuF/Doy6CMSIs/LvTjjJMOv2hAkI/hVViUFlZnlif59vt/WOBsrVuEgFPUwMzxD/qZCaflKDiQQpTUoafe/jqEQrax/1KTCb7UPItrRXFA5iAlScK7ZvZMjJhTFk0QX9L4bVKvYqgrxEn8Rf3f7GTSBzKPZaHFeDBEE0cpRVecuQysXYuqgcNWUTCKp5oVM+maSwRfGcsp/QRujyeU9sUEdZ0Xr+kLHA/BgaAtakdKLnbBzCcMlKEvl/bqJgJSSrajaTV4UHBnEq4anVpMKH28VFHC/1LsT0COizqedjglY4LpVw6fw6Jk1/QyfgPa4sXApmnTO2NidYQNBdFTIEUI94qwk9Pu/hJ+s/1bGLHNKuHeMM+bQCI8E+Nz8MsitJ4Xu68bEeWj9g0IEo2CX1bB644u0rYoBW7SwqgBlZPix/H4pSi/p+5aFxRIQkU6wlM6BAUScA65NvQUYnPTZAc1bh6azYOPflp1JPOfNJqFMElOpI6VyxWFYI72cKIEDEbLmpBcwgZ2Du56aRIfnNbZEcWDaP0O2CRKSDvtdInQLaMbCofCkdxnZMFdpOB2q/KhfPHHTQ5CwgyZ0A51y4ozQ5YFHu9QKXFhC636ph93UpV4dl4/rlhV9l+KOZZMwZtkMYIN+fJWOWDoBwbw7wP6vMQ90S57gzP42oKzzAAs3ACeFppaTQBuqKj2BFW6bQEu9fTgzXNFczVV0OFcFQ5PtBY283rH+HNxE2OuEYRPvQ1tPWxKZDw7APoenvj6ItURPgrNVLEskoFb8MO/sSLrN2ZiaQHEVZVzA8mZRGSpEdGiNgG5xScDykGs2Qsd9v0Uq7z/2Y+e7SLrMgvQFDWyddTe/42Z32q/GmKqR/RCRcpXOQKVK6648BwMfw5zr2p98krmpfErxUdm2+1FELtRdb0KCy0ybQXh6z+lbgRPDqAIkVmJbJL15jM6KyRLcy61jSHrGkDOz6Lh5NurGWlJq+YhUF/4pRzm/w/gzxjBmo0pCZ8tmgR+eshhsHMhVkxQjUBvMXROFXpAY+Yo/tnHqSYDN2ElAlxZn2hyG4ZtxKxH/hp4SGx7F2yZKFb8u+HwszjPVM3qJAwvymNFvuAz4e+ZqyRXGOXUNNpJStPfrx5O7AcPckckcEvPWLl4xkdy5QzK7Rp1eww56V+RfZB1EarM7kxqjbv/cIOYCp+J5Xw9L/bKOFPMO+qOXn29WIQkkQRgs3X5Ysv0BN7Kuz9YK4uyPMuCr0kICQd2ArNGCFh5p32evTG2ZKD13VrGgM4AnaBNpwQekNHs4zRMPupnMCYUKkHkZhaP06gHUukdGXMcHfNOVtduYFbR3GxK65evOmqKGVf0nbiXYdP1hAQ38iM4kPCvPOaXtvbAk8K1WQkwTRgEsMq/J3KmpDwHXKa2dCdOTQ7RDvxMHBBMIKclpQgD4y9mwtpNxPVnMAS9OYZE/XY17nXukQ46YHB88LZx8sZyODyEd98XGmP9Jeid4GchRE10+1QArUnht/CCcXY87RFlxIy0VN3Jm2REJjnJbvqFUtU0TGsETJT1aUfdPTazFeMhsvMbg+tYNPnLHo5y0T16aOYQJR7+HFDYr77RZ4MejRXIw/smeKgckpj/NQ4mvXXPHS4yj6IxehSsCrEZLrh7/5xVxMhhofK76L0jciuo4edmR8="
}
```

##### data解密后的信息示例

```
[
    {
        "country": "BR",
        "transferStatus": "SETTLED",
        "bin": "411111",
        "finalCurrency": "BRL",
        "billingAddressZipCode": "04849333",
        "reference": "20231124181139294705",
        "callbackUrl": "https://connect.next-api.com/houlonglong/opgs/notification/77/1003",
        "brand": "VISA",
        "payerEmail": "username@liquido.com",
        "expirationYear": "2026",
        "billingAddressCity": "sao paulo",
        "scheduledTime": "2023-11-28 02:21:54 UTC",
        "tradeNo": "20231124181139584365",
        "expirationMonth": "9",
        "billingAddressState": "SP",
        "billingAddressStreet": "test",
        "paymentMethod": "CREDIT_CARD",
        "billingAddressCountry": "BR",
        "last4": "1111",
        "payerDocumentType": "CPF",
        "finalAmount": 1000,
        "payerName": "username",
        "currency": "BRL",
        "amount": 1000,
        "billingAddressDistrict": "test",
        "finalStatusTime": "2023-11-28 02:21:54 UTC",
        "transferStatusCode": 200,
        "payerDocumentId": "53033315550",
        "payerPhone": "+555058149876",
        "transferErrorMsg": "",
        "createTime": "2023-11-28 02:20:15 UTC",
        "cardholderName": "Authorized",
        "paymentFlow": "DIRECT"
    }
]
```

### 订单查询-V2-查询支付/退款状态

#### 请求方式

> POST


#### 请求URL

> /api/online/inquiry/v2


#### 请求参数

##### Params：
| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 1003 |
| channel | int | 渠道。可选值：<br />4:liquido | **是** | 4 |


> channel 参数用于区分所属卡组及渠道的交易信息


##### Body:

> json 格式

| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| tradeNo | String | 订单号 | **是** | 0bd88f8a-2c7e-4b95-903f-8da45d0e6129 |
| reference | String | OPGS参考号，当需要针对交易状态进行查询订单时请提供此字段 | 否 |  |

##### 请求示例

###### Params：

> [http://xxxxxx/xx/api/online/inquiry/v2?accountId=1003&channel=4](http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=4)


###### Body：

```
{   
    "tradeNo":"0bd88f8a-2c7e-4b95-903f-8da45d0e6129"
}
```
#### 响应参数
> 数据格式：json

| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| status | String(3) | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81) | 是 | 200 |
| msg | String(50) | 请求消息 | 是 | query success |
| data | String(max) | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E8%A7%A3%E5%AF%86%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%A4%BA%E4%BE%8B) |

> 解密密钥：RSA的私钥，可联系相关人员获取


#### 解密后的信息

以下是将给定的参数、数据类型和描述转换为Markdown格式的表格：
> 解析之后是一个数组，里面包含多条数据，如果只传递了 tradeNo 查询，则数组可能会返回多条数据，如果传递了 tradeNo 和 reference ，数组里包含一条数据。

| 名称 | 类型 | 必选 | 约束 | 中文名 | 说明 |
| --- | --- | --- | --- | --- | --- |
| country | string | false | none |  | 国家编码 |
| amount | integer | false | none |  | 收款金额 |
| currency | string | false | none |  | 收款货币单位，长度为3个字节 |
| finalAmount | integer | false | none |  | 用于创建收款订单的最终金额。对于需要转换汇率的收款订单，这个字段的值表示源请求金额被转换后的金额。 |
| finalCurrency | string | false | none |  | finalAmount的收款货币单位 |
| createTime | string | false | none |  | 创建时间 |
| scheduledTime | string | false | none |  | 执行时间 |
| payerName | string | false | none |  | 名字，全称（入参名字规范：只允许大小写字母，数字和空格的组合。请勿包含西班牙语和葡萄牙语字母，以及其他特殊字符。） |
| payerEmail | string | false | none |  | 邮箱 |
| payerDocumentId | string | false | none |  | 证件号码 |
| payerDocumentType | string | false | none |  | 证件类型，暂时支持CPF或者CNPJ |
| payerPhone | string | false | none |  | 证件号码 |
| billingAddressZipCode | string | false | none |  | 邮政编码，如巴西的CEP |
| billingAddressState | string | false | none |  | 州，缩写，如圣保罗是SP |
| billingAddressCity | string | false | none |  | 城市. |
| billingAddressDistrict | string | false | none |  | 区县. |
| billingAddressStreet | string | false | none |  | 街道. |
| billingAddressNumber | string | false | none |  | 门牌号. |
| billingAddressComplement | string | false | none |  | 补充信息. |
| billingAddressCountry | string | false | none |  | 国家代码. |
| finalStatusTime | string | false | none |  | 最终状态更新时间. 最终状态，包括"REFUNDED", "FAILED" |
| transferStatus | string | false | none |  | 汇款状态，枚举值，"SETTLED", "FAILED", "IN_PROGRESS" |
| transferStatusCode | integer | false | none |  | 此次交易的状态码, 200表示交易成功或者进行中, 其他代表失败 |
| transferErrorMsg | string | false | none |  | 此次交易失败原因 |
| description | string | false | none |  | 描述信息 |
| callbackUrl | string | false | none |  | 接收交易状态变更通知的地址。接收post请求。 |
| subMerchantId | string | false | none |  | 子商户ID。 |
| paymentMethod | string | false | none |  | 支付方式. 枚举值, BOLETO |
| tradeNo | string | false | none |  | 订单号 |
| reference | string | false | none |  | OPGS参考号 |
| paymentFlow | string | false | none |  | 支付流程. 枚举值, DIRECT |
| barcode | string | false | none |  | boleto-二维码 |
| digitalLine | string | false | none |  | boleto-数字线 |
| paymentDeadline | integer | false | none |  | boleto-支付期限 |
| cardId | string | false | none |  | Card-卡ID, 使用卡信息支付时不返回，用卡ID支付时会返回 |
| installments | integer | false | none |  | Card-分期付款次数 |
| cardholderName | string | false | none |  | Card-持卡人名称 |
| bin | string | false | none |  | Card-卡号前6位数字 |
| last4 | string | false | none |  | Card-卡号后4位数字 |
| brand | string | false | none |  | Card-信用卡组织. 如 VISA, MASTERCARD |
| expirationMonth | string | false | none |  | Card-信用卡有效期月份，2位数 |
| expirationYear | string | false | none |  | Card-信用卡有效期年份，4位数 |
| qrCode | string | false | none |  | pix-二维码 |
| qrCodeType | string | false | none |  | pix-二维码类型枚举，STATIC，DYNAMIC |
| expirationTime | string | false | none |  | pix-支付期限 |
| deepLink | string | false | none |  | AME-调用Ame Digital app的链接 |
| qrCodeLink | string | false | none |  | AME-二维码的链接 |
| transactionId | string | false | none |  | AME-在Ame方的交易id |
| paymentUrl | string | false | none |  | picPay-支付链接 |
| content | string | false | none |  | picPay-支付二维码的内容 |
| base64 | string | false | none |  | picPay-base64编码后的支付二维码内容 |
| checkoutProUrl | string | false | none |  | mercadoPago-mercado pago的checkout链接 |
| bankName | string | false | none |  | bankTransfer-银行名称 |
| bankCode | string | false | none |  | 银行代码，3位数字 |
| beneficiaryName | string | false | none |  | 受益人名称，例如 BRL_LIQUIDO |
| bankAccountNumber | string | false | none |  | 受益人账号 |
| bankAccountType | string | false | none |  | 受益人账号类型，例如 ContaCorrente |
| bankBranchId | string | false | none |  | 银行分行Id |
| bankIspb | string | false | none |  | 银行的ISPB, 用途和缩写bankCode一致, 8位数字 |
| bankDocumentId | string | false | none |  | 证件号码 |
| bankDocumentType | string | false | none |  | 证件类型，暂时支持CPF或者CNPJ |
| expiresAt | integer | false | none |  | OXXO-交易过期时间的时间戳 |


#### 响应示例

> 成功


```
{
  "status": "200",
  "msg": "query success",
  "data": "cBwHEJ7JJw0edTZgW7Al7eq8sVPI4hehuF/Doy6CMSIs/LvTjjJMOv2hAkI/hVViUFlZnlif59vt/WOBsrVuEgFPUwMzxD/qZCaflKDiQQpTUoafe/jqEQrax/1KTCb7UPItrRXFA5iAlScK7ZvZMjJhTFk0QX9L4bVKvYqgrxEn8Rf3f7GTSBzKPZaHFeDBEE0cpRVecuQysXYuqgcNWUTCKp5oVM+maSwRfGcsp/QRujyeU9sUEdZ0Xr+kLHA/BgaAtakdKLnbBzCcMlKEvl/bqJgJSSrajaTV4UHBnEq4anVpMKH28VFHC/1LsT0COizqedjglY4LpVw6fw6Jk1/QyfgPa4sXApmnTO2NidYQNBdFTIEUI94qwk9Pu/hJ+s/1bGLHNKuHeMM+bQCI8E+Nz8MsitJ4Xu68bEeWj9g0IEo2CX1bB644u0rYoBW7SwqgBlZPix/H4pSi/p+5aFxRIQkU6wlM6BAUScA65NvQUYnPTZAc1bh6azYOPflp1JPOfNJqFMElOpI6VyxWFYI72cKIEDEbLmpBcwgZ2Du56aRIfnNbZEcWDaP0O2CRKSDvtdInQLaMbCofCkdxnZMFdpOB2q/KhfPHHTQ5CwgyZ0A51y4ozQ5YFHu9QKXFhC636ph93UpV4dl4/rlhV9l+KOZZMwZtkMYIN+fJWOWDoBwbw7wP6vMQ90S57gzP42oKzzAAs3ACeFppaTQBuqKj2BFW6bQEu9fTgzXNFczVV0OFcFQ5PtBY283rH+HNxE2OuEYRPvQ1tPWxKZDw7APoenvj6ItURPgrNVLEskoFb8MO/sSLrN2ZiaQHEVZVzA8mZRGSpEdGiNgG5xScDykGs2Qsd9v0Uq7z/2Y+e7SLrMgvQFDWyddTe/42Z32q/GmKqR/RCRcpXOQKVK6648BwMfw5zr2p98krmpfErxUdm2+1FELtRdb0KCy0ybQXh6z+lbgRPDqAIkVmJbJL15jM6KyRLcy61jSHrGkDOz6Lh5NurGWlJq+YhUF/4pRzm/w/gzxjBmo0pCZ8tmgR+eshhsHMhVkxQjUBvMXROFXpAY+Yo/tnHqSYDN2ElAlxZn2hyG4ZtxKxH/hp4SGx7F2yZKFb8u+HwszjPVM3qJAwvymNFvuAz4e+ZqyRXGOXUNNpJStPfrx5O7AcPckckcEvPWLl4xkdy5QzK7Rp1eww56V+RfZB1EarM7kxqjbv/cIOYCp+J5Xw9L/bKOFPMO+qOXn29WIQkkQRgs3X5Ysv0BN7Kuz9YK4uyPMuCr0kICQd2ArNGCFh5p32evTG2ZKD13VrGgM4AnaBNpwQekNHs4zRMPupnMCYUKkHkZhaP06gHUukdGXMcHfNOVtduYFbR3GxK65evOmqKGVf0nbiXYdP1hAQ38iM4kPCvPOaXtvbAk8K1WQkwTRgEsMq/J3KmpDwHXKa2dCdOTQ7RDvxMHBBMIKclpQgD4y9mwtpNxPVnMAS9OYZE/XY17nXukQ46YHB88LZx8sZyODyEd98XGmP9Jeid4GchRE10+1QArUnht/CCcXY87RFlxIy0VN3Jm2REJjnJbvqFUtU0TGsETJT1aUfdPTazFeMhsvMbg+tYNPnLHo5y0T16aOYQJR7+HFDYr77RZ4MejRXIw/smeKgckpj/NQ4mvXXPHS4yj6IxehSsCrEZLrh7/5xVxMhhofK76L0jciuo4edmR8="
}
```

##### data解密后的信息示例

```
[
    {
        "country": "BR",
        "transferStatus": "SETTLED",
        "bin": "411111",
        "finalCurrency": "BRL",
        "billingAddressZipCode": "04849333",
        "reference": "20231124181139294705",
        "callbackUrl": "https://connect.next-api.com/houlonglong/opgs/notification/77/1003",
        "brand": "VISA",
        "payerEmail": "username@liquido.com",
        "expirationYear": "2026",
        "billingAddressCity": "sao paulo",
        "scheduledTime": "2023-11-28 02:21:54 UTC",
        "tradeNo": "20231124181139584365",
        "expirationMonth": "9",
        "billingAddressState": "SP",
        "billingAddressStreet": "test",
        "paymentMethod": "CREDIT_CARD",
        "billingAddressCountry": "BR",
        "last4": "1111",
        "payerDocumentType": "CPF",
        "finalAmount": 1000,
        "payerName": "username",
        "currency": "BRL",
        "amount": 1000,
        "billingAddressDistrict": "test",
        "finalStatusTime": "2023-11-28 02:21:54 UTC",
        "transferStatusCode": 200,
        "payerDocumentId": "53033315550",
        "payerPhone": "+555058149876",
        "transferErrorMsg": "",
        "createTime": "2023-11-28 02:20:15 UTC",
        "cardholderName": "Authorized",
        "paymentFlow": "DIRECT"
    }
]
```

### 查询实时余额-巴西/墨西哥

#### 请求方式

> POST


#### 请求URL

> /api/online/query/balance


#### 请求参数

##### Params：
| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| accountId | String | 账户ID，商户标识 | **是** | 1003 |


##### Body:
> json 格式

| 对应的域 | 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- | --- |
|  | countryCode | String | 国家代码 | **是** | 枚举：BR-巴西  MX-墨西哥 |

#### 请求示例
##### Params：
> [http://xxxxxx/xx/api/online/query/balance?accountId=1003](http://xxxxxx/xx/api/online/query/balance?accountId=1003)


##### Body：
```
{
    "countryCode":"BR"
}
```
##### 响应参数
> 数据格式：json

| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| code | String | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81) | 是 | 200 |
| message | String | 请求消息 | 是 | Succeed |
| data | String | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E8%A7%A3%E5%AF%86%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%A4%BA%E4%BE%8B) |

> 解密密钥：RSA的私钥，可联系相关人员获取

##### 解密后的信息
以下是将给定的参数、数据类型和描述转换为Markdown格式的表格：

| 名称 | 类型 | 必选 | 约束 | 中文名 | 说明 |
| --- | --- | --- | --- | --- | --- |
| currency | string | false | none |  | 货币种类, 如 BRL、 MXN |
| totalBalance | integer | false | none |  | 账号的实时余额(单位：分) |
| withdrawableAmount | integer | false | none |  | PAY-IN 可提现余额 + PAY-OUT 账户余额 (单位：分) |

##### 响应示例
> 成功

```
{
    "code": 200,
    "message": "Succeed",
    "data": "QNRAR4ZHj39XHEWcdRQVGkGbBII5GUczCabwi4A6xqsWZaOS1nT42HU9YDW9u1RK0/NXDwxw/4GRGqaIARoFqsMM0SFaSW5igYFqWajQzdPL+sUIM9IX7yJs98P9LJRK7iTlJtgSC0IKlZS/uW0IVoKSboKhfVKz561uUzO+hnk="
}
```
###### data解密后的信息示例
```
{
    "withdrawableAmount": 9999,
    "totalBalance": 9999,
    "currency": "BRL"
}
```
### 绑卡查询
#### 请求路径
/bindCard/query
#### 请求方式
POST
#### 请求头（header）
```json
{
  "Content-Type": "application/json",
  "authorization": "{{authorization}}"
}
```
#### 请求参数示例（加密）（body）
```json
{
    "data":"encryptData"
}
```
#### 请求参数示例（解密）
```json
{
  "cardId": "dabf3787-c172-4503-9f84-43792f8a29e4",
  "accountId": "1003"
}
```
#### 请求参数说明
| **名称** | **类型** | **必选** | **中文名** | **说明** |
| --- | --- | --- | --- | --- |
| accountId | string | 是 | OPGS商户id | 列如MX |
| cardId | string | 是 | 卡ID | 绑卡之后返回的唯一标识号 |

#### 响应参数示例（加密）
```json
{
	"code":200,
	"message": "succeed",
  "data":"encryptData"
}
```
#### 响应参数示例（解密）
```json
{
    "expirationYear": "2024",
    "accountId": "1003",
    "last4": "1111",
    "createTime": "2023-12-04T11:38:09.557",
    "cardHolderName": "Authorized",
    "bin": "411111",
    "cardId": "25bb83dc-e8f2-4fc9-bc01-b244e5fc7008",
    "id": "194403a3-5767-40d5-99b6-a7f2d25ba702",
    "expirationMonth": "9",
    "type": "2",
    "brand": "VISA"
}
```
#### 响应参数说明
| **名称** | **类型** | **必选** | **约束** | **中文名** | **说明** |
| --- | --- | --- | --- | --- | --- |
| code | integer | true | none | 请求状态码 | 成功：200；其它：失败 |
| message | string | true | none | 请求结果 | 成功：success |
| expirationYear | string | true | none | 过期年份 |  |
| last4 | string | true | none | 卡号后四位 |  |
| cardHolderName | string | true | none | 持卡人名称 |  |
| bin | string | true | none | 卡号前六位 |  |
| expirationMonth | string | true | none | 过期月份 |  |
| type | string | true | none | 类型 | 1为绑卡返回数据,2为查询返回数据 |
| brand | string | true | none | 卡品牌 |  |
| createTime | sting | true | none | 创建时间 |  |
| cardId | string | true | none | 保存的卡ID. |  |
| id | string | true | none | 系统唯一标识 |  |
| accountId | string | true | none | 系统商户号 |  |
| FailData | object | true | none | 失败响应参数 |  |
| errorData | string | true | none | 失败原因 |  |

## Tap渠道
### 订单查询-授权订单查询，支付订单查询，退款订单查询
#### 请求方式
POST
#### 请求URL
/api/online/inquiry/v2
#### 请求参数
##### Params：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 1003 |
| channel | int | 渠道。可选值：0:Master1:WorldPay2:WorldPayJson4:liquido5:Tap | **是** | 4 |

channel 参数用于区分所属卡组及渠道的交易信息
##### Body:
json 格式

| **对应的域** | **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- | --- |
|  | tradeNo | String | 订单号 | **是** | 0bd88f8a-2c7e-4b95-903f-8da45d0e6129 |
|  | reference | String | 参考号 | 否 | uixs88f8a-2c7e-4b95-9052-8da45d0e6129 |

#### 请求示例
##### Params：
[http://xxxxxx/xx/api/online/inquiry/v2?accountId=1003&channel=5](http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=5)
##### Body：
```
{   
    "tradeNo":"0bd88f8a-2c7e-4b95-903f-8da45d0e6129",
    "reference":"uixs88f8a-2c7e-4b95-9052-8da45d0e6129"
}
```
#### 响应参数
数据格式：json

| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| status | String(3) | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81) | 是 | 200 |
| msg | String(50) | 请求消息 | 是 | query success |
| data | String(max) | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E8%A7%A3%E5%AF%86%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%A4%BA%E4%BE%8B) |

解密密钥：RSA的私钥，可联系相关人员获取<br />解密后的信息

| **名称** | **类型** | **必选** | **约束** | **中文名** | **说明** |
| --- | --- | --- | --- | --- | --- |
| cardObject | string | 是 |  | 卡对象 | 卡的对象类型例 card |
| cardBrand | string | 是 |  | 卡品牌 | 例 VISA |
| cardFirstSix | string | 是 |  | 卡首六位 |  |
| cardLastFour | string | 是 |  | 卡后四位 |  |
| receiptId | string | 是 |  | 收据id |  |
| receiptEmail | boolean | 是 |  | 收据电子邮件 | 是否发送到邮箱 |
| receiptSms | boolean | 是 |  | 收据短信 | 是否发送短信 |
| responseCode | string | 是 |  | 响应代码 | 响应码 例：000 |
| responseMessage | string | 是 |  | 响应消息 | 响应消息 例：APPROVED |
| redirectUrl | string | 是 |  | 重定向 URL |  |
| redirectStatus | string | 是 |  | 重定向状态 |  |
| cardBrand | string | 是 |  | 卡品牌 |  |
| tradeNo | string | 是 |  | 订单号 | 关联的订单号 |
| referencePayment | string | 是 |  | 付款参考 | 付款参考号 |
| referenceAcquirer | string | 是 |  | 收单参考 | 收单参考号 |
| sourcePaymentType | string | 是 |  | 支付类型 |  |
| sourceType | string | 是 |  | 来源类型 | CARD_NOT_ PRESENT卡不在场 |
| sourcePaymentMethod | string | 是 |  | 支付方式 | 卡支付方式 例：VISA |
| sourceChannel | string | 是 |  | 支付渠道 | 例：INTERNET |
| sourceObject | string | 是 |  | 类别 | 例：authorize |
| status | string | 是 |  | 状态 | 处理状态 例CAPTURED |
| object | string | 是 |  |  | 交易类型 例:charge |
| customerId | string | 是 |  | 客户 ID | 子商户id |
| customerInitiated | boolean | 是 |  | 客户发起 | 是否由客户发起 |
| customerPhoneNumber | string | 是 |  | 客户电话号码 | 客户电话号码 |
| customerFirstName | string | 是 |  | 客户名字 | 客户名 |
| customerMiddleName | string | 是 |  | 客户中间名 |  |
| customerLastName | string | 是 |  | 客户姓氏 | 子商户姓 |
| customerPhoneCountryCode | string | 否 |  | 客户国家电话区号 |  |
| customerEmail | string | 是 |  | 客户电子邮件 |  |
| sourceChannel | string | 是 |  | 来源渠道 | 例：INTERNET |
| postStatus | string | 是 |  | 发布状态 |  |
| description | string | 是 |  | 描述 |  |
| transactionExpiryType | string | 是 |  | 交易到期类型 | 例：MINUTE |
| autoReversed | boolean | 是 |  | 自动撤销 | 是否自动撤销 |
| transactionCreated | string | 是 |  | 交易创建时间 | 交易创建的时间 |
| threeDSecure | boolean | 是 |  | 3DS安全认证 |  |
| transactionTimezone | string | 是 |  | 交易时区 | 交易时区 |
| currency | string | 是 | 长度为 3 | 收款货币单位 | 长度为 3 个字节 |
| receiptId | string | 是 |  | 收据 ID |  |
| amount | int | 是 |  | 金额 |  |
| product | bigdecimal | 是 |  | 产品 |  |

#### 响应示例
成功
```
{
    "status": "200",
    "msg": "Succeed",
    "data": "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"
}
```
#### data解密后的信息示例
返回结果
```
[
    {
        "cardObject": "card",
        "redirectUrl": "http://your_website.com/redirect_url",
        "customerInitiated": true,
        "sourceChannel": "INTERNET",
        "receiptEmail": true,
        "threeDSecure": false,
        "postStatus": "ERROR",
        "description": "Test Description",
        "transactionExpiryType": "MINUTE",
        "responseCode": "00",
        "customerMiddleName": "test",
        "transactionCreated": "1699010492092",
        "customerEmail": "test@test.com",
        "transactionTimezone": "UTC+03:00",
        "customerId": "cus_TS07A4120230943Oc950211892",
        "customerLastName": "test",
        "currency": "KWD",
        "redirectStatus": "SUCCESS",
        "cardBrand": "VISA",
        "cardFirstSix": "450875",
        "receiptId": "203203231121118993",
        "amount": 1.000,
        "product": "GOSELL",
        "customerPhoneCountryCode": "965",
        "referenceAcquirer": "330708124595",
        "method": "GET",
        "tradeNo": "ord_01",
        "referencePayment": "3203231121115920441",
        "sourceObject": "authorize",
        "receiptSms": true,
        "transactionExpiryPeriod": 30,
        "cardLastFour": "1019",
        "sourcePaymentType": "DEBIT",
        "sourceType": "CARD_NOT_PRESENT",
        "sourcePaymentMethod": "VISA",
        "customerFirstName": "test",
        "responseMessage": "Approved",
        "customerPhoneNumber": "51234567",
        "status": "CAPTURED"
    },
    {
        "cardObject": "card",
        "redirectUrl": "http://your_website.com/redirecturl",
        "customerInitiated": true,
        "sourceChannel": "INTERNET",
        "receiptEmail": true,
        "threeDSecure": true,
        "postStatus": "CANCEL",
        "description": "",
        "transactionExpiryType": "MINUTE",
        "responseCode": "00",
        "customerMiddleName": "test",
        "transactionCreated": "1699010429490",
        "customerEmail": "test@test.com",
        "transactionTimezone": "UTC+03:00",
        "customerId": "cus_TS07A4120230943Oc950211892",
        "customerLastName": "test",
        "currency": "KWD",
        "redirectStatus": "SUCCESS",
        "cardBrand": "VISA",
        "cardFirstSix": "450875",
        "receiptId": "203103231120114901",
        "amount": 1,
        "product": "GOSELL",
        "customerPhoneCountryCode": "965",
        "referenceAcquirer": "330708124595",
        "method": "GET",
        "tradeNo": "ord_0001",
        "referencePayment": "3103231120110680529",
        "sourceObject": "token",
        "receiptSms": true,
        "transactionExpiryPeriod": 30,
        "cardLastFour": "1019",
        "sourcePaymentType": "DEBIT",
        "sourceType": "CARD_NOT_PRESENT",
        "sourcePaymentMethod": "VISA",
        "customerFirstName": "test",
        "responseMessage": "Approved",
        "customerPhoneNumber": "51234567",
        "status": "CAPTURED"
    }
]
```
<br /> 
### 交易数据同步下发
#### 描述
> 当渠道为tap时，且商户订单参数传递了回调地址，则会发送支付结果


#### 商户提供地址
> 该地址必须为HTTPS，且外网可访问


#### 异步回调参数
| 参数名称 | 数据类型 | 描述 | 是否必填 | 示例 |
| --- | --- | --- | --- | --- |
| tradeNo | String | 订单号 | M |  |
| reference | String | 付款参考号 | M |  |
| customerInitiated | boolean | 是否由客户发起 | M |  |
| status | String | 状态 | M |  |
| amount | BigDecimal | 金额 | M |  |
| currency | String | 货币 | M |  |
| threeDSecure | boolean | 是否3DS请求 | M |  |
| saveCard | boolean | 是否保存卡信息 | M |  |
| merchantId | String | 商户id | C |  |
| statementDescriptor | String | 订单描述 | C |  |
| transactionTimezone | String | 交易时区 | C |  |
| transactionCreated | String | 交易创建时间 | C |  |
| transactionUrl | String | 交易链接 | C |  |
| transactionExpiryPeriod | Integer | 交易过期时间 | C |  |
| transactionExpiryType | String | 交易过期类型 | C |  |
| transactionAmount | BigDecimal | 交易金额 | C |  |
| transactionCurrency | String | 交易货币 | C |  |
| responseCode | String | 响应代码 | M |  |
| responseMessage | String | 响应消息 | M |  |
| ThreeDSecureId | String | 3DS id | C |  |
| ThreeDSecureStatus | String | 3DS 状态 | C |  |
| cardObject | String | 卡对象，卡的对象类型例 card | C |  |
| cardFirstSix | String | 卡首六位 | C |  |
| cardBrand | String | 卡品牌，例 VISA | C |  |
| cardLastFour | String | 卡后四位 | C |  |
| receiptId | String | 收据id | C |  |
| receiptEmail | boolean | 收据电子邮件是否发送到邮箱 | C |  |
| receiptSms | boolean | 收据短信是否发送短信 | C |  |
| customerId | String | 客户id | C |  |
| customerFirstName | String | 客户姓名 | M |  |
| customerMiddleName | String | 客户中间名 | M |  |
| customerLastName | String | 客户姓氏 | M |  |
| customerEmail | String | 客户邮箱 | M |  |
| customerPhoneCountryCode | String | 客户国家代码 | M |  |
| customerPhoneNumber | String | 客户手机号码 | M |  |
| sourceType | String | 类别 | M |  |
| sourcePaymentType | String | 来源类别 | M |  |
| sourcePaymentMethod | String | 来源支付方法 | M |  |
| sourceChannel | String | 来源渠道 | M |  |
| sourceId | String | 支付方式/支付id | M |  |
| sourceObject | String | 来源对象 | M |  |
| redirectUrl | String | 重定向 URL | C |  |
| redirectStatus | String | 重定向状态 | C |  |
| postUrl | String | 通知回调地址 | C |  |
| postStatus | String | 通知回调状态 | C |  |
| description | String | 描述 | C |  |
| metadataUdf1 | String | 保留字1 | C |  |
| metadataUdf2 | String | 保留字2 | C |  |
| metadataUdf3 | String | 保留字3 | C |  |
| paymentAgreementId | String | 支付协议号 | C |  |
| totalPaymentsCount | BigDecimal | 总付款金额 | C |  |


#### 异步回调示例
```json
{
  "amount": 1,
  "cardBrand": "VISA",
  "cardFirstSix": "450875",
  "cardLastFour": "1019",
  "cardObject": "card",
  "currency": "KWD",
  "customerEmail": "test@test.com",
  "customerFirstName": "Test",
  "customerId": "cus_TS06A1920230642Hs4a0611912",
  "customerInitiated": true,
  "customerLastName": "Test",
  "customerMiddleName": "Test",
  "customerPhoneCountryCode": "965",
  "customerPhoneNumber": "50000000",
  "description": "",
  "merchantId": "",
  "metadataUdf1": "test_data_1",
  "metadataUdf2": "test_data_2",
  "metadataUdf3": "test_data_3",
  "postStatus": "PENDING",
  "postUrl": "https://webhook.site/a60e0c04-d13b-4ede-b2e5-6807ec4f85b5",
  "receiptEmail": true,
  "receiptId": "203606230924114238",
  "receiptSms": true,
  "redirectStatus": "SUCCESS",
  "redirectUrl": "http://your_website.com/redirecturl",
  "reference": "20231106184621982",
  "responseCode": "00",
  "responseMessage": "Approved",
  "saveCard": false,
  "sourceChannel": "INTERNET",
  "sourceId": "tok_qVOb2323624ZFc66ZN104694",
  "sourceObject": "token",
  "sourcePaymentMethod": "VISA",
  "sourcePaymentType": "DEBIT",
  "sourceType": "CARD_NOT_PRESENT",
  "statementDescriptor": "sample",
  "status": "CAPTURED",
  "threeDSecure": true,
  "tradeNo": "20231106181156282237",
  "transactionAsynchronous": false,
  "transactionCreated": "1699262674551",
  "transactionExpiryPeriod": 30,
  "transactionExpiryType": "MINUTE",
  "transactionTimezone": "UTC+03:00"
}
```

#### 商户应答
> 无


### 交易通知数据异步下发

> 如下发失败，我们会每间隔30s发送一次，直至达到最大发送次数，不再发送；可以使用订单查询进行支付结果查询

## myMoney渠道
### 交易订单查询，退款订单查询
#### 请求方式
POST
#### 请求URL
/api/online/inquiry
#### 请求参数
##### Params：
| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| accountId | String(12) | 账户ID，商户标识 | **是** | 1003 |
| channel | int | 渠道。可选值：0:Master1:WorldPay2:WorldPayJson4:liquido5:Tap | **是** | 4 |

channel 参数用于区分所属卡组及渠道的交易信息
##### Body:
json 格式

| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| tradeNo | String | 订单号 | **是** | 0bd88f8a-2c7e-4b95-903f-8da45d0e6129 |
| reference | String | 参考号 | 否 | uixs88f8a-2c7e-4b95-9052-8da45d0e6129 |

#### 请求示例
##### Params：
[http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=5](http://xxxxxx/xx/api/online/inquiry?accountId=1003&channel=5)
##### Body：
```
{   
    "tradeNo":"0bd88f8a-2c7e-4b95-903f-8da45d0e6129",
    "reference":"uixs88f8a-2c7e-4b95-9052-8da45d0e6129"
}
```
#### 响应参数
数据格式：json

| **参数名称** | **数据类型** | **描述** | **是否必填** | **示例** |
| --- | --- | --- | --- | --- |
| status | String(3) | 请求状态，可见[状态码说明](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81) | 是 | 200 |
| msg | String(50) | 请求消息 | 是 | query success |
| data | String(max) | 加密后的信息，详细的内容见[解密后的信息](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E8%A7%A3%E5%AF%86%E5%90%8E%E7%9A%84%E4%BF%A1%E6%81%AF)<br />**使用RSA私钥进行解密** | 是 | 见以下[响应示例](https://sinopay.yuque.com/pynmqw/stppdi/fzb5uvi9nelwofas#%20%E5%93%8D%E5%BA%94%E7%A4%BA%E4%BE%8B) |

解密密钥：RSA的私钥，可联系相关人员获取<br />解密后的信息

| 名称 | 类型 | 必选 | 约束 | 中文名 | 说明 |
| --- | --- | --- | --- | --- | --- |
| goodsTitle | String | C |  | 商品标题 |  |
| goodsDetail | String | C |  | 商品详情 |  |
| reference | String | M |  | 付款参考号 |  |
| feeType | String | C |  | 费用类型 | 例：GBP |
| totalFee | String | M |  | 总费用 |  |
| cashFeeType | String | C |  | 现金费用类型 | 用户用于支付的货币类型 |
| cashFee | BigDecimal | C |  | 现金费用 |  |
| rate | String | C |  | 费率 |  |
| tradeNo | String | M |  | 交易号 | 订单号 |
| deviceInfo | String | C |  | 设备信息 | 终端设备ID，此字段由Merchant定义。商家可以使用此字段来区分订单 |
| serviceType | String | C |  | 服务类型 | 支付服务提供商 |
| type | String | M |  | 类型 | 卡类型 |
| transactionStatus | String | M |  | 交易状态 |  |
| transactionRate | String | C |  | 交易费率 |  |
| transactionFee | BigDecimal | C |  | 交易费用 |  |
| transactionTimeUk | String | C |  | 交易时间（英国时区） |  |
| refundFee | BigDecimal | M |  | 退款费用 | 指定此退款事务处理的退款金额。 |
| notifyStatus | String | C |  | 通知状态 |  |
| totalRefund | BigDecimal | C |  | 总退款 |  |
| currency | String | C |  | 货币 | <br /> |
| refundStatus | String | C |  | 退款状态 | SUCCESS or FAIL. |
| refundDesc | String | C |  | 退款描述 | 退款原因 |

#### 响应示例
成功
```
{
    "status": "200",
    "msg": "Succeed",
    "data": "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"
}
```
#### data解密后的信息示例
返回结果
```
{
{
"reference":"10021",
 "type":"VISA",
 "tradeNo":"20200301181614983793",
 "refundId":"20200309025417681234",
 "totalFee":"0.01",
 "refundFee":"0.01",
 "totalRefund":"0.01",
 "currency":"GBP",
 "refundStatus":"SUCCESS",
 "refundDesc":"Customer returns"
 },
 {
 "reference":"10010",
 "goodsTitle":"MyMoney Online Pay",
 "goodsDetail":"MyMoney_goods_detail",
 "feeType":"GBP",
 "totalFee":"0.15",
 "cashFeeType":"GBP",
 "cashFee":"0.15",
 "rate":"903247",
 "deviceInfo":"10000",
 "serviceType":"TRUSTPAY",
 "type":"VISA",
 "transactionStatus":"SUCCESS",
 "transactionRate":"02.50",
 "transactionFee":"0.0003",
 "transactionTimeUk":"2020-03-01 10:01:57",
 "refundFee":"0.01",
 "notifyStatus":"SUCCESS"
 }
}
```
## 异步回调重复机制

> 如商户在30内无应答，我们会在间隔30s内，连续发送5次，直至达到最大发送次数，不再发送；可以使用订单查询进行支付结果liquido

# channel可选值枚举说明
| 渠道 | channel值 |
| --- | --- |
| worldpay | worldpay |
| worldpayJson | worldpayJson |
| checkout | checkout |
| liquido | liquido |
| tap | tap |
| mymoney | mymoney |


# 响应码说明
| **code** | **message** | **description** |
| --- | --- | --- |
| 200 | succeed | 请求成功 |
| 205 | Pending | 处理中 |
| 206 | Card verified | 卡验证 |
| 207 | Declined | 授权拒绝 |
| 400 | Unknown error | 未知错误 |
| <br /><br />401 | Authorization not allowed | 授权不允许 |
|  | please check the request params | 参数不合法，请检查请求参数【仅worlPay查询接口使用】 |
| 402 | Cancel not allowed | 撤销不允许 |
| 403 | Capture not allowed | 请款不允许 |
| 404 | Refund not allowed | 退款不允许 |
| 405 | Order not found | 订单未找到 |
| 406 | Merchant not found | 商户未找到 |
| 407 | Merchant config not found | 商户配置未找到 |
| 408 | Merchant country nonsupport | 商户所在国家不支持 |
| 409 | Order already exist | 订单号已存在 |
| 410 | Original transaction is already pending a refund | 当前交易已存在退款未完成，请稍后再试 |
| 422 | Invalid data was sent | 请求的数据缺失或无效 |
| 429 | Too many requests or duplicate request detected | 频繁请求或重复请求 |
| 450 | Unrecognized channel | 无法识别的渠道 |
| 451 | Unrecognized payment method | 无法识别的支付方式 |
| 500 | System error | 系统错误 |
| 502 | Bad gateway | 网关错误 |

