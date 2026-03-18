## HTTP 版本演进

|版本|核心特性|关键问题|
|---|---|---|
|**1.0**|每次请求新建 TCP 连接|性能差|
|**1.1**|持久连接(keep-alive)、管线化、chunked 传输、Host 头|队头阻塞(HOL)|
|**2.0**|多路复用、头部压缩(HPACK)、二进制帧、服务器推送|TCP 层队头阻塞仍在|
|**3.0**|基于 **QUIC**(UDP)、彻底解决队头阻塞、0-RTT 建连|尚在普及|

**一句话记忆**：1.1 解决连接复用 → 2.0 解决应用层队头阻塞 → 3.0 解决传输层队头阻塞



## HTTP 方法

|方法|语义|幂等|备注|
|---|---|---|---|
|GET|获取资源|✅|参数在 URL，有长度限制(浏览器层面)|
|POST|创建资源|❌|参数在 body|
|PUT|全量更新|✅||
|PATCH|部分更新|❌||
|DELETE|删除|✅||
|OPTIONS|预检请求|✅|CORS 预检|
|HEAD|同 GET 但无 body|✅||


## GET vs POST 核心区别

```
GET:  参数在URL / 可缓存 / 可收藏 / 幂等 / 浏览器回退无害
POST: 参数在body / 不可缓存 / 非幂等 / 回退会重新提交
本质: 语义不同，TCP层面没有区别
```

---

## 状态码

### 2xx 成功

```
200  OK
201  Created（资源已创建）
204  No Content（成功但无返回体，如 DELETE）
206  Partial Content（范围请求，断点续传）
```

### 3xx 重定向


```
301  永久重定向（会缓存，SEO权重转移）
302  临时重定向（不缓存）
304  Not Modified（协商缓存命中）
307  临时重定向（保持方法不变，不会把POST变GET）
308  永久重定向（保持方法不变）
```

> **301/302 的坑**：部分浏览器会把 POST 变成 GET，所以有了 307/308

### 4xx 客户端错误

```
400  Bad Request（参数错误）
401  Unauthorized（未认证，需要登录）
403  Forbidden（已认证但无权限）
404  Not Found
405  Method Not Allowed
429  Too Many Requests（限流）
```

### 5xx 服务端错误

```
500  Internal Server Error（服务器内部错误）
502  Bad Gateway（网关/代理收到上游无效响应）
503  Service Unavailable（服务不可用，过载/维护）
504  Gateway Timeout（网关超时）
```

---

## HTTPS = HTTP + TLS


```
对称加密：  速度快，用于传输数据（AES）
非对称加密：速度慢，用于交换密钥（RSA/ECDHE）
流程：非对称加密协商出对称密钥 → 对称加密通信
```

---

## TCP vs UDP
