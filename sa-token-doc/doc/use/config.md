# 框架配置
你可以**零配置启动框架** <br>
但同时你也可以通过配置，定制性使用框架，`Sa-Token`支持多种方式配置框架信息





### 方式1、在 application.yml 配置

``` java
# Sa-Token 配置
sa-token: 
	# token名称 (同时也是cookie名称)
	token-name: satoken
	# token有效期，单位s 默认30天, -1代表永不过期 
	timeout: 2592000
	# token临时有效期 (指定时间内无操作就视为token过期) 单位: 秒
	activity-timeout: -1
	# 是否允许同一账号并发登录 (为true时允许一起登录, 为false时新登录挤掉旧登录) 
	is-concurrent: true
	# 在多人登录同一账号时，是否共用一个token (为true时所有登录共用一个token, 为false时每次登录新建一个token) 
	is-share: true
	# token风格
	token-style: uuid
	# 是否输出操作日志 
	is-log: false
```

如果你习惯于 `application.properties` 类型的配置文件，那也很好办: 百度： [springboot properties与yml 配置文件的区别](https://www.baidu.com/s?ie=UTF-8&wd=springboot%20properties%E4%B8%8Eyml%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9A%84%E5%8C%BA%E5%88%AB)


### 方式2、通过代码配置
模式1：
``` java 
/**
 * Sa-Token代码方式进行配置
 */
@Configuration
public class SaTokenConfigure {

	// 获取配置Bean (以代码的方式配置Sa-Token, 此配置会覆盖yml中的配置)
    @Bean
    @Primary
    public SaTokenConfig getSaTokenConfigPrimary() {
		SaTokenConfig config = new SaTokenConfig();
		config.setTokenName("satoken");             // token名称 (同时也是cookie名称)
		config.setTimeout(30 * 24 * 60 * 60);       // token有效期，单位s 默认30天
		config.setActivityTimeout(-1);              // token临时有效期 (指定时间内无操作就视为token过期) 单位: 秒
		config.setIsConcurrent(true);               // 是否允许同一账号并发登录 (为true时允许一起登录, 为false时新登录挤掉旧登录) 
		config.setIsShare(true);                    // 在多人登录同一账号时，是否共用一个token (为true时所有登录共用一个token, 为false时每次登录新建一个token) 
		config.setTokenStyle("uuid");               // token风格 
		config.setIsLog(false);                     // 是否输出操作日志 
		return config;
	}
	
}
```

模式2：
``` java
// 以代码的方式配置Sa-Token-Config 
@Autowired
public void configSaToken(SaTokenConfig config) {
	// config.setTokenName("satoken333");             // token名称 (同时也是cookie名称)
	// ... 
}
```

PS：两者的区别在于：**`模式1会覆盖yml中的配置，模式2会与yml中的配置合并`**


--- 
### 所有可配置项

你不必立刻掌握整个表格，只需要在用到某个功能时再详细查阅它即可

| 参数名称				| 类型		| 默认值		| 说明																				|
| :--------				| :--------	| :--------	| :--------																			|
| tokenName				| String	| satoken	| Token 名称 （同时也是 Cookie 名称、数据持久化前缀）													|
| timeout				| long		| 2592000	| Token 有效期，单位/秒 默认30天，-1代表永久有效	[参考：token有效期详解](/fun/token-timeout)		|
| activityTimeout		| long		| -1		| Token 临时有效期 （指定时间内无操作就视为token过期） 单位: 秒, 默认-1 代表不限制 （例如可以设置为1800代表30分钟内无操作就过期） 	[参考：token有效期详解](/fun/token-timeout)													|
| isConcurrent			| Boolean	| true		| 是否允许同一账号并发登录 （为 true 时允许一起登录，为 false 时新登录挤掉旧登录）															|
| isShare				| Boolean	| true		| 在多人登录同一账号时，是否共用一个token （为 true 时所有登录共用一个 token, 为 false 时每次登录新建一个 token） 	|
| maxLoginCount			| int		| 12		| 同一账号最大登录数量，-1代表不限 （只有在 `isConcurrent=true`, `isShare=false` 时此配置才有效），[详解](/use/config?id=maxlogincount)	|
| isReadBody			| Boolean	| true		| 是否尝试从 请求体 里读取 Token														|
| isReadHead			| Boolean	| true		| 是否尝试从 header 里读取 Token														|
| isReadCookie			| Boolean	| true		| 是否尝试从 cookie 里读取 Token，此值为 false 后，`StpUtil.login(id)` 登录时也不会再往前端注入Cookie				|
| tokenStyle			| String	| uuid		| token风格， [参考：自定义Token风格](/up/token-style)										|
| dataRefreshPeriod		| int		| 30		| 默认数据持久组件实现类中，每次清理过期数据间隔的时间 （单位: 秒） ，默认值30秒，设置为-1代表不启动定时清理 		|
| tokenSessionCheckLogin	| Boolean	| true	| 获取 `Token-Session` 时是否必须登录 （如果配置为true，会在每次获取 `Token-Session` 时校验是否登录），[详解](/use/config?id=tokenSessionCheckLogin)		|
| autoRenew				| Boolean	| true		| 是否打开自动续签 （如果此值为true, 框架会在每次直接或间接调用 `getLoginId()` 时进行一次过期检查与续签操作），[参考：token有效期详解](/fun/token-timeout)		|
| tokenPrefix			| String	| null		| token前缀，例如填写 `Bearer` 实际传参 `satoken: Bearer xxxx-xxxx-xxxx-xxxx` 	[参考：自定义Token前缀](/up/token-prefix) 			|
| isPrint				| Boolean	| true		| 是否在初始化配置时打印版本字符画													|
| isLog					| Boolean	| false		| 是否打印操作日志																	|
| jwtSecretKey			| String	| null		| jwt秘钥 （只有集成 `sa-token-temp-jwt` 模块时此参数才会生效），[参考：和 jwt 集成](/plugin/jwt-extend)	|
| idTokenTimeout		| long		| 86400		| Id-Token的有效期 （单位: 秒），[参考：内部服务外网隔离](/micro/id-token)					|
| basic					| String	| ""		| Http Basic 认证的账号和密码 [参考：Http Basic 认证](/up/basic-auth)						|
| currDomain			| String	| null		| 配置当前项目的网络访问地址													|
| checkIdToken			| Boolean		| false		| 是否校验Id-Token（部分rpc插件有效）															|
| cookie				| Object	| new SaCookieConfig()	| Cookie配置对象															|

Cookie相关配置：

| 参数名称		| 类型		| 默认值		| 说明																			|
| :--------		| :--------	| :--------	| :--------																		|
| domain		| String	| null		| 作用域（写入Cookie时显式指定的作用域, 常用于单点登录二级域名共享Cookie的场景）			|
| path			| String	| /		| 路径，默认写在域名根路径下			|
| secure		| Boolean	| false		| 是否只在 https 协议下有效												|
| httpOnly		| Boolean	| false		| 是否禁止 js 操作 Cookie 	|
| sameSite		| String	| Lax		| 第三方限制级别（Strict=完全禁止，Lax=部分允许，None=不限制）		|



### 单点登录相关配置 

Server 端：

| 参数名称		| 类型		| 默认值		| 说明																			|
| :--------		| :--------	| :--------	| :--------																		|
| ticketTimeout	| long		| 300		| ticket 有效期 （单位: 秒）														|
| allowUrl		| String	| *			| 所有允许的授权回调地址，多个用逗号隔开（不在此列表中的URL将禁止下放ticket），参考：[SSO整合：配置域名校验](/sso/sso-check-domain)	|
| isSlo			| Boolean	| false		| 是否打开单点注销功能															|
| isHttp		| Boolean	| false		| 是否打开模式三（此值为 true 时将使用 http 请求：校验ticket值、单点注销、获取userinfo），参考：[详解](/use/config?id=isHttp) 	|
| secretkey		| String	| null		| 调用秘钥 （用于SSO模式三单点注销的接口通信身份校验）								|


Client 端：

| 参数名称		| 类型		| 默认值		| 说明											|
| :--------		| :--------	| :--------	| :--------										|
| authUrl		| String	| null		| 配置 Server 端单点登录授权地址					|
| isSlo			| Boolean	| false		| 是否打开单点注销功能							|
| isHttp		| Boolean	| false		| 是否打开模式三（此值为 true 时将使用 http 请求：校验ticket值、单点注销、获取userinfo），参考：[详解](/use/config?id=isHttp) 	|
| checkTicketUrl| String	| null		| 配置 Server 端的 ticket 校验地址							|
| userinfoUrl	| String	| null		| 配置 Server 端查询 userinfo 地址									|
| sloUrl		| String	| null		| 配置 Server 端单点注销地址										|
| ssoLogoutCall	| String	| null		| 配置当前 Client 端的单点注销回调URL （为空时自动获取）	|
| secretkey		| String	| null		| 接口调用秘钥 （用于SSO模式三单点注销的接口通信身份校验）		|

配置示例：
``` yml
# Sa-Token 配置
sa-token: 
    # SSO-相关配置
    sso: 
        # SSO-Server端 单点登录授权地址 
        auth-url: http://sa-sso-server.com:9000/sso/auth
```



### OAuth2.0相关配置 
| 参数名称				| 类型		| 默认值	| 说明																		|
| :--------				| :--------	| :--------	| :--------																	|
| isCode				| Boolean	| true		| 是否打开模式：授权码（`Authorization Code`）								|
| isImplicit			| Boolean	| false		| 是否打开模式：隐藏式（`Implicit`）											|
| isPassword			| Boolean	| false		| 是否打开模式：密码式（`Password`）											|
| isClient				| Boolean	| false		| 是否打开模式：凭证式（`Client Credentials`）								|
| isNewRefresh			| Boolean	| false		| 是否在每次 `Refresh-Token` 刷新 `Access-Token` 时，产生一个新的 Refresh-Token	|
| codeTimeout			| long		| 300		| Code授权码 保存的时间（单位：秒） 默认五分钟									|
| accessTokenTimeout	| long		| 7200		| `Access-Token` 保存的时间（单位：秒）默认两个小时								|
| refreshTokenTimeout	| long		| 2592000	| `Refresh-Token` 保存的时间（单位：秒） 默认30 天								|
| clientTokenTimeout	| long		| 7200		| `Client-Token` 保存的时间（单位：秒） 默认两个小时								|
| pastClientTokenTimeout	| long	| 7200		| `Past-Client-Token` 保存的时间（单位：秒） ，默认为-1，代表延续 `Client-Token` 的有效时间 	|

配置示例：
``` yml
# Sa-Token 配置
sa-token: 
    token-name: satoken-server
    # OAuth2.0 配置 
    oauth2: 
        is-code: true
        is-implicit: true
        is-password: true
        is-client: true
```

##### SaClientModel属性定义
| 参数名称				| 类型		| 默认值	| 说明													|
| :--------				| :--------	| :--------	| :--------											|
| clientId				| String	| null		| 应用id，应该全局唯一								|
| clientSecret			| String	| null		| 应用秘钥											|
| contractScope			| String	| null		| 应用签约的所有权限, 多个用逗号隔开					|
| allowUrl				| String	| null		| 应用允许授权的所有URL, 多个用逗号隔开 （可以使用 `*` 号通配符）			|
| isCode				| Boolean	| false		| 单独配置此 Client 是否打开模式：授权码（`Authorization Code`）		|
| isImplicit			| Boolean	| false		| 单独配置此 Client 是否打开模式：隐藏式（`Implicit`）		|
| isPassword			| Boolean	| false		| 单独配置此 Client 是否打开模式：密码式（`Password`）		|
| isClient				| Boolean	| false		| 单独配置此 Client 是否打开模式：凭证式（`Client Credentials`）		|
| isAutoMode			| Boolean	| true		| 是否自动判断此 Client 开放的授权模式。 参考：[详解](/use/config?id=isAutoMode)  |
| isNewRefresh			| Boolean	| 取全局配置		| 单独配置此Client：是否在每次 `Refresh-Token` 刷新 `Access-Token` 时，产生一个新的 Refresh-Token [ 默认取全局配置 ]	|
| accessTokenTimeout	| long		| 取全局配置		| 单独配置此Client：`Access-Token` 保存的时间（单位：秒）  [默认取全局配置]	|
| refreshTokenTimeout	| long		| 取全局配置		| 单独配置此Client：`Refresh-Token` 保存的时间（单位：秒） [默认取全局配置]	|
| clientTokenTimeout	| long		| 取全局配置		| 单独配置此Client：`Client-Token` 保存的时间（单位：秒） [默认取全局配置]	|
| pastClientTokenTimeout	| long	| 取全局配置		| 单独配置此Client：`Past-Client-Token` 保存的时间（单位：秒） [默认取全局配置]	|



### 部分配置项详解

对部分配置项做一下详解 

#### maxLoginCount

配置含义：同一账号最大登录数量。

在配置 `isConcurrent=true`, `isShare=false` 时，Sa-Token 将允许同一账号并发登录，且每次登录都会产生一个新Token，
这些 Token 都会以 `TokenSign` 的形式记录在其 `User-Session` 之上，这就造成一个问题：

随着同一账号登录的次数越来越多，TokenSign 的列表也会越来越大，极端情况下，列表长度可能达到成百上千以上，严重拖慢数据处理速度，
为此 Sa-Token 对这个 TokenSign 列表的大小设定一个上限值，也就是 `maxLoginCount`，默认值=12。

假设一个账号的登录数量超过 `maxLoginCount` 后，将会主动注销第一个登录的会话（先进先出），以此保证队列中的有效会话数量始终 `<= maxLoginCount` 值。


#### tokenSessionCheckLogin
配置含义：获取 `Token-Session` 时是否必须登录 （如果配置为true，会在每次获取 `Token-Session` 时校验是否登录）。

在调用 `StpUtil.login(id)` 登录后，

- 调用 `StpUtil.getSession()` 可以获取这个会话的 `User-Session` 对象。
- 调用 `StpUtil.getTokenSession()` 可以获取这个会话 `Token-Session` 对象。

关于两种 Session 有何区别，可以参考这篇：[Session模型详解](/fun/session-model)，此处暂不赘述。

从设计上讲，无论会话是否已经登录，只要前端提供了Token，我们就可以找到这个 Token 的专属 `Token-Session` 对象，**这非常灵活但不安全**，
因为前端提交的 Token 可能是任意伪造的。

为了解决这个问题，`StpUtil.getTokenSession()` 方法在获取 `Token-Session` 时，会率先检测一下这个 Token 是否是一个有效Token：
- 如果是有效Token，正常返回 `Token-Session` 对象
- 如果是无效Token，则抛出异常。

这样就保证了伪造的 Token 是无法获取 `Token-Session` 对象的。

但是 —— 有的场景下我们又确实需要在登录之前就使用 Token-Session 对象，这时候就把配置项 `tokenSessionCheckLogin` 值改为 `false` 即可。

需要注意的一点是：此时如果前端提交的 Token 是一个无效 Token 的话，框架将不会根据此 Token 创建 `Token-Session` 对象，
而是随机一个新的 Token 值来创建 `Token-Session` 对象，此 Token 值可以通过 `StpUtil.getTokenValue()` 获取到。


#### isAutoMode

配置含义：是否自动判断此 Client 开放的授权模式。

- 此值为 true 时：四种模式（`isCode、isImplicit、isPassword、isClient`）是否生效，依靠全局设置；
- 此值为 false 时：四种模式（`isCode、isImplicit、isPassword、isClient`）是否生效，依靠局部配置+全局配置（两个都为 true 时才打开） 


#### isHttp

配置含义：是否打开单点登录模式三。

- 此配置项为 false 时，代表使用SSO模式二：使用 Redis 校验 ticket 值、删除 Redis 数据做到单点注销、使用 Redis 同步 Userinfo 数据。
- 此配置项为 true 时，代表使用SSO模式三：使用 Http 请求校验 ticket 值、使用 Http 请求做到单点注销、使用 Http 请求同步 Userinfo 数据。


