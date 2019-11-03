//PayPal 快速结账 NVP API 开发指南

PayPal NVP API
	PayPal 快速结账流程
		整个付款过程中调用了三次 API,分别为
		SetExpressCheckout
		该方法请求 PayPa 使用“快速支付快速结账”从您的客户处获取付款。
		GetExpressCheckoutDetails
		该方法返回客户的信息,包括在 PayPal 上存储的姓名和地址。
		DoExpressCheckoutDetails
		该方法请求获取付款。
	使用 SetExpressCheckout 开始结账
	SetExpressCheckout 请求方法通知 PayPal 您正使用“快速结账”从您的客户处获取付款。
	SetExpressCheckout 请求中必须始终包含以下参数:
	PAYMENTREQUEST_0_AMT
	RETURNURL
	CANCELURL
	1. 在 SetExpressCheckout 响应中,您可获得一个唯一地标识该三步交易的 TOKEN。
	您将请求中的该 TOKEN 传递给 GetExpressCheckoutDetails 和 DoExpressCheckoutPayment。
	GetExpressCheckoutDetails 和 DoExpressCheckoutPayment 都会在响应中返回该 TOKEN。
	该示例演示使用最少参数的基本结账。
	2. 将客户的浏览器跳转至 PayPal 登录页面
	您从 SetExpressCheckout 收到成功响应后,请将 SetExpressCheckout 响应中的 TOKEN 作
	为名称/值对添加到以下 URL,并将您客户的浏览器跳转至该 URL:
	https://www.paypal.com/cgi-bin/webscr?cmd=_express-checkout&token=value_from_SetEx
	pressCheckoutResponse
	要将客户的浏览器跳转至 PayPal 登录页面
	使用 GetExpressCheckoutDetails 获取付款人详细信息
	GetExpressCheckoutDetails 方法返回客户的信息,包括在 PayPal 上存储的姓名和地址。
	GetExpressCheckoutDetails 中必须始终包含以下参数:
	TOKEN:使用来自 SetExpressCheckout 响应的值该响应包含该 TOKEN 和客户详细信息
	3.使用 DoExpressCheckoutPayment 获取付款
	使用 DoExpressCheckoutPayment 请求,通过 PayPal 快速结账获取付款。默认情况下,使用
	DoExpressCheckoutPayment 请求进行最终销售。
	DoExpressCheckoutPayment 请求中必须始终包含以下参数:
	TOKEN:使用来自 SetExpressCheckout 响应的值
	PAYERID:使用来自 GetExpressCheckoutDetails 响应的值
	PAYMENTINFO_0_PAYMENTACTION:设置为 Sale。这是 SetExpressCheckout 中的默认值。
	PAYMENTREQUEST_0_AMT:买家账户真实付出的金额,包括物品价格,邮费,保险等的总和。
NVP 格式
	NVP 是指定字符串中的名称和值的一种方法。NVP 是表示 URI 规范中的查询的非正式名称。
	NVP 字符串附加到 URL 上。
	NVP 字符串符合以下准则:
	名称和值用等号(=)分隔。例如:
	FIRSTNAME=Robert
	名称/值对用与号(&)分隔。例如:
	FIRSTNAME=Robert&MIDDLENAME=Herbert&LASTNAME=Moore
	NVP 字符串中每个字段的值已经过 URL 编码。

请求格式
	USER=Your_APIUsername&
	PWD=Your_APIPassword&
	SIGNATURE=Your_Signature&
	METHOD=SetExpressCheckout&
	VERSION=78&
	AMT=10&
	cancelUrl=https://example.com/cancel&
	returnUrl=https://example.com/success
API 凭证
	必需的安全参数:API 凭证
	参数 值 定义
	USER 必需 您的 PayPal API 用户名。
	PWD 必需 您的 PayPal API 密码。
	SIGNATURE 可选 您的 PayPal API 签名字符串。如
	果您
	使用 API 证书,请勿包括该参数
响应格式
	成功响应字段
		ACK=Success&TIMESTAMP=date/timeOfResponse&CORRELATIONID=debug
		gingToken&VERSION=84.0&BUILD=buildNumber
		[successResponseFields]
	API 响应字段 &NAME1=value1&NAME2=value2&NAME3=value3
错误响应 
	ACK=Error&TIMESTAMP=date/timeOfResponse&
 
	CORRELATIONID=debuggingToken&VERSION=84.0&
	 
	BUILD=buildNumber&L_ERRORCODE0=errorCode&
	 
	L_SHORTMESSAGE0=shortMessage&
	 
	L_LONGMESSAGE0=longMessage&L_SEVERITYCODE0=severityCode
 

