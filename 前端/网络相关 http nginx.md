

# HTTP

## HTTP 版本演进

|版本|核心特性|关键问题|
|---|---|---|
|**1.0**|每次请求新建 TCP 连接|性能差|
|**1.1**|持久连接(keep-alive)、管线化、chunked 传输、Host 头|队头阻塞(HOL)|
|==**2.0**==|==多路复用、头部压缩(HPACK)、二进制帧、服务器推送==|TCP 层队头阻塞仍在|
|==**3.0**==|==基于 **QUIC**(UDP)、彻底解决队头阻塞、0-RTT 建连==|尚在普及|

**一句话记忆**：1.1 解决连接复用 → 2.0 解决应用层队头阻塞 → 3.0 解决传输层队头阻塞

**==① 二进制帧（Binary Framing）==**

  1.1: 文本协议，解析靠分隔符

这些帧都有固定的二进制结构，比如：

- 长度
- 类型
- 标志位
- 流 ID
- payload

所以 HTTP/2 的协议层解析，不再是“读一行字符串、找冒号、找空行”，而是“按二进制格式读帧头和帧内容”。  2.0: 二进制帧，每个请求/响应拆成多个帧
       帧有 StreamID 标识属于哪个请求

**==② 多路复用（Multiplexing）⭐ 最重要==**

==二进制帧和多路复用==

多路复用 = 一个TCP连接上，多个请求/响应同时传输、互不阻塞
"多路"：多个请求流
"复用"：共用一个TCP连接
  一个TCP连接上并行交错发送多个请求/响应
  帧通过 StreamID 重新组装

③ 头部压缩（HPACK）
  1.1: 每次请求都带完整header（Cookie可能几KB）
  2.0: 
    - 静态表：61个常见header预定义索引
        - 动态表：连接内出现过的header建立索引
        - 哈夫曼编码压缩值
        → header大小减少 85-95%

**==④ 服务器推送（Server Push）==**
  服务器可以主动推送资源（如请求HTML时主动推CSS/JS）
  实际用的不多，Chrome 已移除支持

QUIC 基于 UDP，自己实现了：
  ① 可靠传输（类似TCP但更好）
  ② 流级别的独立控制
     → 一个流丢包不影响其他流，彻底解决队头阻塞
  ③ 0-RTT / 1-RTT 建连
     TCP+TLS: 需要 3-RTT（TCP握手1 + TLS握手2）
     QUIC:    首次1-RTT，重连0-RTT
  ④ 连接迁移
     TCP用四元组标识连接（IP+端口变了连接就断）
     QUIC用Connection ID → WiFi切4G不断连

## HTTP 方法

| 方法    | 语义             | 幂等 | 备注                               |
| ------- | ---------------- | ---- | ---------------------------------- |
| GET     | 获取资源         | ✅    | 参数在 URL，有长度限制(浏览器层面) |
| POST    | 创建资源         | ❌    | 参数在 body                        |
| PUT     | 全量更新         | ✅    |                                    |
| PATCH   | 部分更新         | ❌    |                                    |
| DELETE  | 删除             | ✅    |                                    |
| OPTIONS | 预检请求         | ✅    | CORS 预检                          |
| HEAD    | 同 GET 但无 body | ✅    |                                    |
```
GET     拿数据        （我要看）
POST    提交数据       （我要创建）
PUT     整体替换       （我要全量覆盖）
PATCH   部分修改       （我只改几个字段）
DELETE  删除          （我要删掉）
OPTIONS 问服务器你支持啥（CORS 预检用）
HEAD    和GET一样但只要响应头（探测资源是否存在/大小）
```

## GET vs POST 核心区别

```
GET:  参数在URL / 可缓存 / 可收藏 / 幂等 / 浏览器回退无害
POST: 参数在body / 不可缓存 / 非幂等 / 回退会重新提交
本质: 语义不同，TCP层面没有区别
```

## 状态码

### 2xx 成功

```
200  OK
201  Created（资源已创建）
204  No Content（成功但无返回体，如 DELETE）
206  Partial Content（范围请求，断点续传） 一般配合RANGE请求头出现，前端需要某个大文件的一部分数据
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

# TCP vs UDP

**HTTP Headers 是总称，请求头只是其中一种，另一种是响应头。**



# 请求

------

## 什么是一次 HTTP 请求？

可以理解成：

```
浏览器/前端 -> 向服务器发一条消息
告诉它：
1. 我要访问哪个地址
2. 我想干什么（方法）
3. 我带了什么信息（请求头、请求体）
```

服务器收到后，再回一条响应。

## 一个请求由什么组成？

### 1. 请求行

```
GET /users/1 HTTP/1.1
```

表示：方法：GET  路径：/users/1  协议版本：HTTP/1.1

------

### 2. 请求头 Headers

是一些“附加说明信息”。

比如：

```
Host: api.xxx.com
Content-Type: application/json
Authorization: Bearer xxx
Accept: application/json
Cookie: a=1; b=2
```

作用是告诉服务器：

- 我要发给谁
- 我传的数据是什么格式
- 我希望你返回什么格式
- 我的身份凭证是什么
- 我带了哪些 cookie

------

### 3. 请求体 Body

真正提交的数据，常见于 POST / PUT / PATCH。（所以get一般没有请求体）

```
{
  "name": "Tom",
  "age": 18
}
```



## 常见请求头

# 响应

### 1. 状态行

表示这次请求处理结果怎么样。

```
HTTP/1.1 200 OK
```

这里最重要的是状态码：

- `200` 成功
- `404` 资源不存在
- `500` 服务器错误

------

### 2. 响应头

告诉浏览器一些附加信息。

比如：

- 返回内容是什么类型
- 能不能缓存
- 要不要设置 cookie
- 是否允许跨域

------

### 3. 响应体

真正返回的数据内容。

比如可能是：

- HTML 页面
- JSON 数据
- 图片
- 文件

```
{
  "code": 0,
  "data": { "name": "Tom" }
}
```





# 域和跨域

跨域：跨的是什么？
**跨域 = 跨源**。
当前页面的源 和 请求目标的源 不一致。

**源 = 协议 + 域名 + 端口**
任意一个不同，就是跨源。

为什么会跨域？因为浏览器有**同源策略**，防止恶意网站读取别的网站的数据。

- 请求**能发出去**

- 但浏览器**不让前端读响应**

  

  ### CORS

  最重要、最常用。
  后端通过响应头告诉浏览器：这个跨域请求允许。

  常见头：

  - `Access-Control-Allow-Origin`
  - `Access-Control-Allow-Methods`
  - `Access-Control-Allow-Headers`
  - `Access-Control-Allow-Credentials`

  ------

  ### 预检请求 OPTIONS

  非简单请求会先发 OPTIONS。

  常见触发：

  - `PUT / DELETE / PATCH`
  - `Content-Type: application/json`
  - 自定义请求头，如 `Authorization`

  ------

  ### Cookie 跨域

  跨域默认不带 cookie。

  要带的话：

  - 前端：`withCredentials: true` / `credentials: 'include'`
  - 后端：`Access-Control-Allow-Credentials: true`
  - 且 `Access-Control-Allow-Origin` 不能是 `*`

  ------

  ### 代理

  实际开发最常见。

  - 开发环境：Vite / Webpack proxy
  - 生产环境：Nginx 反向代理

  本质：让浏览器看起来是同源请求。

# 网关？
