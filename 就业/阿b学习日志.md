
# 需求：个播榜单黑名单

Scpoe application

  

  

## RPC（远程过程调用）是什么？

RPC（Remote Procedure Call，远程过程调用）是一种协议，允许程序调用另一个地址空间（通常位于网络上的另一台机器）中的函数或过程

```JavaScript
// RPC调用（代码级）
const user = userService.getUser(123); // 看起来像本地调用

// HTTP API调用
fetch('/api/users/123')  // 明显是网络请求
  .then(res => res.json())
```

添加黑名单的完整流程：

1. 前端 → 后端 API：POST /api/live/online-whitelist/create
    
2. 后端在自己的库里建记录 ✅（这部分已经做了）
    
3. 后端 → RPC 调用排行榜服务：clearRankScore(roomId)
    
4. 排行榜服务清除该房间的积分
    
5. 返回结果给前端
    

  

  

## 泳道&modheader

问题：没加泳道调用rpc服务404了

泳道是用来区分不同业务流量的标签。在微服务架构中，同一个服务可能有多个实例或多个版本，泳道用来指定请求应该路由到哪个实例。

为什么加泳道后就不 404 了？

这说明：

live-service 服务有多个实例 - 可能有不同的版本或环境

不同泳道对应不同的实例 - 泳道 rank-blacklist 指向的实例可能实现了这个接口

默认泳道（空字符串）的实例 可能没有实现这个接口，所以返回 404

这是一种灰度发布或A/B 测试的常见做法，允许新功能先在特定泳道上线，而不影响其他用户。

### 一般做法

其实也是前端用modheader加了个请求头是吧，然后后端能解析这个请求头

1. 前端通过 ModHeader 在请求头（如 `X-Swimlane`）中携带环境标识。
    
2. 后端读取该请求头，根据标识选择对应的配置或数据源（Swimlane）。
    

  

  

## swagger&openapi

Swagger 是 OpenAPI 的前身和实现工具集。在你的项目中：

- **后端**：使用 `@nestjs/swagger` 装饰器（如 `@ApiProperty`、`@ApiOperation`）来生成 Swagger 文档
    
- **前端**：使用 `swagger-typescript-api` 工具读取这个文档
    

OpenAPI 是一个标准化的 API 文档格式（之前叫 Swagger 2.0）。它用 JSON/YAML 描述 REST API 的结构，包括：

所有的端点（endpoints）

请求参数和响应格式

数据类型定义

认证方式等

在你的项目中，后端在 http://localhost:3000/api/swagger-json 暴露了 OpenAPI 文档。

  

  

工作流：

```Plain
后端代码 (DTO + Controller)
    ↓
@ApiProperty 装饰器定义
    ↓
NestJS 生成 Swagger JSON
    ↓
swagger-typescript-api 读取 JSON
    ↓
生成 TypeScript SDK 代码
    ↓
前端使用生成的 API 客户端
```

  

第一步： 后端dto和controller写参数的时候加上这些东西：

`@ApiProperty` 装饰器

**不是**只从 `description` 和 `example` 推断类型的。它的秘密在于 **TypeScript 的类型元数据**。

```TypeScript
// 后端代码
export class RoomInfoResponseDto {
  @ApiProperty({ description: '直播间标题', example: '猫耳官方直播间' })
  roomTitle: string;  // ← TypeScript 类型信息在这里！
}
```

当你写 `roomTitle: string` 时，TypeScript 编译器会记录这个类型信息。`@ApiProperty` 装饰器可以通过 **Reflect 元数据**读取到这个类型，并且生成一个swaagerjson文档，如下：

  

当后端运行时，访问 `http://localhost:3000/api/swagger-json`，你会看到：

```JSON
{
  "components": {
    "schemas": {
      "RoomInfoResponseDto": {
        "type": "object",
        "properties": {
          "roomTitle": {
            "type": "string",  // ← 从 TypeScript 类型推断出来的！
            "description": "直播间标题",
            "example": "猫耳官方直播间"
          },
          "roomId": {
            "type": "number",  // ← 从 TypeScript 类型推断出来的！
            "description": "直播间 ID"
          },
          "anchorUserId": {
            "type": "number",
            "description": "主播 MID"
          },
          "anchorUsername": {
            "type": "string",
            "description": "主播昵称"
          }
        }
      }
    }
  }
}
```

### 3. 前端生成工具

`swagger-typescript-api` 读取这个 JSON，然后生成：

```TypeScript
// 生成的代码
export interface RoomInfoResponseDto {
  /** 主播 MID */
  anchorUserId: number
  /** 主播昵称 */
  anchorUsername: string
  /** 直播间 ID */
  roomId: number
  /** 直播间名称 */
  roomTitle: string
}
```

## 完整的信息流

```Plain
后端 TypeScript 代码
    ↓
@ApiProperty 装饰器
    ↓
Reflect 元数据读取 TypeScript 类型信息
    ↓
NestJS 生成 Swagger JSON（包含 type: "string" 等）
    ↓
swagger-typescript-api 读取 JSON
    ↓
生成前端 TypeScript 接口（roomTitle: string）
```

## 为什么只写 `@ApiProperty()` 也能工作

```TypeScript
// 这样也能工作
@ApiProperty()
roomTitle: string;

// 因为 NestJS 会自动从 TypeScript 类型推断
// 但最好还是明确写上，这样更清楚
@ApiProperty({ description: '直播间标题', example: '猫耳官方直播间' })
roomTitle: string;
```

## 复杂类型为什么需要明确指定

```TypeScript
// ❌ 这样不行
@ApiProperty()
roomIds: number[];

// 为什么？因为 Reflect 元数据对数组类型的支持有限
// 它只能看到 "这是个数组"，但看不到 "数组元素是 number"

// ✅ 所以要明确告诉 Swagger
@ApiProperty({
  type: 'array',
  items: { type: 'number' },
})
roomIds: number[];
```

## 总结

表格 还在加载中，请等待加载完成后再尝试复制

所以你的直觉是对的——**Swagger 不是只从 description 和 example 推断的，它是从 TypeScript 的类型系统推断的**。`@ApiProperty` 只是一个"翻译器"，把 TypeScript 类型翻译成 Swagger 能理解的格式。

  

  

  

## rebase和merge

  

  

## MR 问题&

export async function fetchBatchRoomInfo(body: { roomIds: number[] }) {

return postJsonData('/api/live/online-whitelist/batch-room-info', body)

}

考虑列表拓展能力

考虑type 1|2 改成用enum,方便对应名称

  

#### 软删除/硬删除——rpc与数据表的调用顺序与脏数据问题

  

  

### Dejavu：

### Caster:

  

## uat发布染色

# 需求：系统通知后台二期：通用消息补齐文案变量能力

  

  

  

  

# 需求：公主请上马

## 实现过程：

Parcel

半屏幕

  

先换level1 切图，加两个占位，然后接数据

  

需要接口的：

刷新

no1 -10 的用户名 马形象 马livel

我的马场排名

  

没了？没了

  

## 装根ca，了解“浏览器证书”

在第一次接接口的时候发现开f12，没有反应，直接红了，里面红后面跟的字是：`net::ERR_CERT_AUTHORITY_INVALID`

  

这个报错和解决方式，确实很典型，尤其是在公司内部开发或者使用内网服务的时候。我给你用通俗易懂的方式解释一下。

### 1. 为什么会出现 `net::ERR_CERT_AUTHORITY_INVALID` 这个错误？

  

这个错误的字面意思是：**证书颁发机构无效**。

为了理解它，你可以把**HTTPS**（带锁的网站）想象成一个需要出示**身份证**才能进入的**VIP房间**：

1. **浏览器**：就是门口的**保安**。
    
2. **服务器**：就是你访问的那个**房间**。
    
3. **CA（证书颁发机构）**：就像是**公安局**，是大家都信任的、专门负责发放和认证身份证的权威机构。
    
4. **SSL证书**：就是服务器的**身份证**。
    

正常流程是：浏览器（保安）看到一个网站（房间）说自己是安全的（HTTPS），就会去查它的身份证。如果这张身份证是**公安局（CA）**签发的，浏览器就放行，地址栏出现一把锁。

  

**出问题的情况**：你的同事（或者你们公司的运维）在内部搭建了一个服务（比如 GitLab、Jira 或者某个后端接口），为了省事或省钱，没有花钱去找公安局（全球受信的CA）办身份证，而是**自己用软件伪造了一张“内部通行证”**。

  

浏览器（保安）一看这张身份证：

- **发证机关**不是它信任的公安局列表里的。
    
- **身份证本身**虽然技术上是有效的，但签发者是个“未知机构”。
    
      
    

于是浏览器就会报警：**“发证机构无效（ERR_CERT_AUTHORITY_INVALID），这个人的身份证不可信，我拒绝放行！”** 所以请求直接是红的，不通。

  

---

  

### 2. 同事让你装的 `ca.crt` 是啥东西？

  

`ca.crt` 文件，就是你同事给你的那个 **“伪造的内部公安局的印章”**。

  

刚才说了，你们公司内部自己成立了一个“山寨公安局”（**私有CA根证书**）。这个局自己给自己发了个证明（ca.crt），然后用它给所有内部服务（比如那个接口）签发身份证。

  

- **ca.crt**：就是这个**山寨公安局的根证书**，也就是它的公章。
    
- 你安装它：就是把你电脑里的**保安（浏览器/操作系统）叫过来，指着这个公章告诉它：“你看好了，这个‘内部公安局’从今天起，也是我们信任的机构**了。以后只要是它签发的身份证，你都给我放行。”
    
      
    

---

  

### 3. 为啥装上了，原来不通的接口就通了？

  

当你安装了这个 `ca.crt` 文件后，你的电脑就发生了两个关键变化：

  

1. **建立信任链**：
    

2. 你的浏览器（保安）原本只信任几家大公安局。现在你手动把“内部公安局”加到了它的信任列表里。
    
      
    

3. **验证通过**：
    

4. 当浏览器再次访问那个报错的接口（房间）时，流程变成了：
    
    - 服务器出示它的身份证（SSL证书）。
        
    - 浏览器查看签发者：哦，是“内部公安局”签发的。
        
    - 浏览器去查“内部公安局”是不是在受信列表里？**是的，用户刚刚把我加进去了。**
        
    - 浏览器比对数字签名，确认身份证是真的，没有被篡改。
        
    - **验证通过！** 保安放行，请求成功，页面通了。
        
          
        
    

### 总结一下：

  

- **问题**：你们公司的内部服务用了**自己制作的假身份证（自签名证书）**，浏览器不认识这个发证机关，所以拦截了。
    
- **ca.crt**：就是那个**发证机关（私有CA）的官方公章**。
    
- **为什么通了**：你把公章（ca.crt）装进了电脑的“信任列表”里，告诉浏览器“以后这个机构发的证都认”，所以浏览器就不再报错，允许通信了。
    
      
    

**安全提示**：

这种操作在**公司内部网络**是常规且安全的。但如果有一天你在**公共场所**（比如咖啡馆）或者**陌生网站**让你下载安装一个 `ca.crt`，**千万不要装**，因为别人可以利用这个原理监控你所有的加密通信（中间人攻击）。

  

## bugfix：列表超过10个

返回的列表有时超过10个，做slice截断

## bugfix: 高度超了

  

## cr：不要直接fetch，用内部封装好的工具函数

## cr：token放在query里面太危险了

  

  

# 需求：猫耳header footer优化

# AI怎么用

UI还原辅助

今天搞游戏的UI还原，发现了一个神仙插件，TemPad Dev配合本地MCP，可以替代之前用的Framelink_Figma_MCP,不需要figma 的apikey，且一次还原完成度大概能到90%（会自动切图，且定位），剩下的有问题的大部分是几类：

1. 文字的定位问题，设计到文字渲染的一些问题，所以会有一些偏移
    
2. 组件定位方式的问题，这个跟图层的设计结构有关系，建议先自己复制一份figma，调整图层顺序后再让ai实现
    
3. 滚动容易没有识别等其他问题
    
      
    

相比于Framelink_Figma_MCP的优势：

1. 不会遇到429，API限流问题
    
2. 还原效果更好
    

https://github.com/ecomfe/tempad-dev#mcp-server



## 需求： 520 榜单组件改造

捋一捋，要给榜单加这些东西：

晋级淘汰tag，变成列表项了 完成

行间插入，变定制项目来了 完成  自测完成

pk信息改名字 完成  

pk头像遮罩   没给设计稿

榜单遮罩  ![[Pasted image 20260401171535.png]]

对下接口 ，从promotion之后的加遮罩，按行加就行。设置成定制化吧


pk条. 完成


自测
## 工程流辅助

  

## agent?mcp?