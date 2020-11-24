## Restful Api 接口签名技术方案

#### 1.前言

>当前项目接口签名加密的只有请求中的parameter数据，Method、Path、Body没有纳入签名体系，如果使用restful风格api定义方式，会存在安全风险。故重新定义api接口的签名方式，来支持restful api。


#### 2.基本要素
- **AppKey** 应用key，后端应用配置中心下发，客户端接口必传
- **AppSecret** 应用密钥，后端应用配置中心下发，用以接口签名加密
- **Timestamp** 接口请求发起时间，用以做接口实效校验，与服务器相对时间不超过预设值10分钟
- **Nonce** 10位随机流水号，防止重复提交（暂时不做重复校验）
- **Method** 接口动作类型，例如：GET、POST、PUT等等，注意大写
- **Path** 接口url，例如：
	- **源接口：** https://test-friendship.weli010.cn/friendship/api/auth/signin?a=XXX&b=XXX
	- **url：** /friendship/api/auth/signin
- **Parameter** 接口url中的参数拼接，key字典排序
	- **拼接规则：** key1=value&key2=value1,value2
	- key之间使用 **<&>** 拼接
	- key-value之间使用 **<=>** 拼接
	- value之间使用 **<,>** 拼接，key重复，多个value字典排序拼接
- **Body** 请求中的body数据，JSON String

#### 4.签名算法
- **Source** Method#Path#Parameter#Body#AppSecret 
- 为空的参数过滤移除<对应的**#**也要移除>，例如：接口没有Parameter、Body，那么拼接结果：Method#Path#AppSecret 
- **Signature** 对 **Source** 做MD5加密

api接口中的命名定义映射：

要素 | 参数 | 类型
----|-----|------
AppKey | app_key |  String
Timestamp | app_ts |  Long
Nonce | app_nonce |  Long
Signature | app_sign |  String


#### 5.签名流程时序图

![](http://static.etouch.cn/imgs/upload/1600756206.8412.jpg)

- **客户端签名**
- **装饰HttpServletRequest** 缓存 getInputStream() 数据
- **参数校验** 数据完整校验，时间有效校验，重复提交校验（暂时没有）
- **签名校验** 验证服务器计算的签名是否同客户端计算的一致

> 因为 getInputStream()方法中的流数据只能读取一次，故自定义装饰类，缓存request中的body内容，且不能影响后续流程中的spring mvc数据绑定（读取的getInputStream()）

#### 6.接入
- 根据需要，需同步接入ContentCachingRequestWrapperReplaceFilter、SignatureInterceptor

#### 7.优化&升级
- 打包成工具模块，最小接入成本
- 支持自定义签名算法？（毕竟现在是简单粗暴的AppSecret salt MD5）
- 过期校验开关、重复校验开关（当然现在是没有的）

#### 8.缺陷
- 因为缓存了inputstream数据，所以有性能、资源上的损耗
- 如果加上了可重复校验，将会影响客户端同服务端的联调测试、问题定位
- 过期校验可能会影响某些时钟非常不准确的用户

#### 9.其他方案（aop）
>Aop拦截Controller，切面中读取Method、Path、Paramter、Body数据，跟拦截器方案区别不大

- **Path** 可以获取的更为细致，拦截器方案获取的是整个RequestURI，但是签名的目的与结果没影响
- **Body** 可以细化到控制body中的那些字段进行签名，但是不如整个Body JSONString签名简单粗暴 




