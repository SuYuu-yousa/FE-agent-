
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

晋级淘汰tag，变成列表项了 完成 自测完成

行间插入，变定制项目来了 完成  自测完成

pk信息改名字 完成  自测完成

pk头像遮罩   没给设计稿  
 
榜单遮罩  对下接口 ，从promotion之后的加遮罩，按行加就行。设置成定制化吧 自测完成

pk条. 完成 自测完成


自测



## 需求： 猫耳主播日结方案



## 工程流辅助

  

## agent?mcp?
# 520 活动组件开发 问题总结文档

# 需求梳理：

520活动需要用到三个现有组件，其中前两个使用场景较多

猫耳通用榜单(新榜单组件)：

[https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-ranklist](https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-ranklist)

猫耳小窗组件：

[https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-windowpane](https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-windowpane)

猫耳直播榜单(原有榜单组件)：（这次需求只用在决赛-擂主榜）

[https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-live-rank-list](https://git.bilibili.co/maoer-frontend/maoer-plat-components/missevan-live-rank-list)

Eva平台组件的使用逻辑是：

开发人员本地开发完组件后上传平台。参考：[Eva 组件开发指南](https://doc.weixin.qq.com/doc/w3_ACcAjwb0ADg11DeaxlgT4uRJDwXJw?scode=ANYAEAdoABEiXX7gbNACcAjwb0ADg)

运营在平台编辑页面中，以拖拽等方式配置组件参数和布局，形成页面。

运营将页面发布上线后，页面上的组件根据接口数据展示不同的页面内容。

### 数据展示流程（以榜单为例）

同一类组件的不同实例展示不同数据，“最终”是通过运营配置的key实现的，以榜单组件举例，对于不同榜单

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_627782_gLUNMwhBxrTvtTM3_1776068075?w=308&h=204&type=image/png)

运营配置： 业务类型（区分请求的接口）｜ 活动ID=1668, 榜单key="18"（区分query参数） 如上图

↓

上线后，榜单组件实际会请求：/api/v2/activity/pk/list?event_id=1668&key=18

↓

后端根据 event_id + key 返回对应的榜单数据。

但在配置过程中，由于没有实际的接口返回数据，运营一般会通过上传 JSON 文件或者复制类似活动的配置json，来模拟数据预览效果。

对于榜单组件，上传的方式有两种：

第一种是：载入配置（右上角）

eva平台控制，可包含所有此组件配置内容，下文称为配置json![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_259825__eaAZTn8blMJP9Z8_1776067899?w=1459&h=536&type=image/png)

第二种是：json数据（右下角）

"JSON 数据" 上传后组件用它替代接口请求作为数据源，下文称为数据json

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_803161_8jBFUjRJegNlnac1_1776067914?w=1259&h=581&type=image/png)

第一个json可以包含此组件的所有配置项（所有组件都有的），第二个json仅包含数据（榜单组件的配置项）

ps: 所以其实对于榜单组件，他的配置数据结构是这样的

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_643994_XcqA9mO6AejpsVrg_1776070420?w=406&h=423&type=image/png)

# 问题梳理汇总：

在开发过程/运营配置过程中发现的问题汇总：[520前端组建问题合集](https://doc.weixin.qq.com/sheet/e3_AXEAHgbuABwCN9rvhtOt0RFaAIFmr?scode=ANYAEAdoABECikRwocAcoAUwaCAGc&tab=BB08J2)

大致可以分成四类：

### 1.  新开发功能的bug，开发自测不充分导致

比如：分组赛晋级/淘汰无法改圆角、头像添加胜负遮罩没有平局情况。

这里晋级tag和胜负遮罩都是此次新需求，是自测不充分导致的提出bug

自测手段: 参考 [Eva 组件开发指南](https://doc.weixin.qq.com/doc/w3_ACcAjwb0ADg11DeaxlgT4uRJDwXJw?scode=ANYAEAdoABEiXX7gbNACcAjwb0ADg) &[登录 - 猫耳FM-INFO](https://info.missevan.com/pages/viewpage.action?pageId=118071207)

大概是本地npm run:eva，浏览器开：[https://ff-dev.bilibili.com/?_port_=2000](https://ff-dev.bilibili.com/?_port_=2000) 或 [https://ff-dev.bilibili.co/?_port_=2000](https://ff-dev.bilibili.co/?_port_=2000) ,拖本地组件进来

复制一个线上活动平台的json,载入,查看效果

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_878656_jQaLy3iMsCD75jal_1776073292?w=1919&h=762&type=image/png)

所以其实这里就可以发现一些下文的第2类问题，因为自测过程我们如果也复制线上的json，是可以知道运营复制的json会有哪些字段是"缺失"的，运营和你复制的json应该差不多

### 2.  配置过程中因为数据字段缺失/不对应，运营看不到预览

比如：

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_171417_N_mcVjxZ9DUGENec_1776068409?w=239&h=73&type=image/png)

承接1的问题，运营通常会反馈：新配置勾选了，但是看不到预览效果，这通常是因为上传的json缺失了相关字段导致的

和代码无关，解决办法就是发给运营带新字段的mock数据，然后看到预览效果

但是这里有一个风险：新提供的json(包含新字段)，必须能保证和实际上线的后端接口字段一致，我们只是在根据这个json看预览，并不是上线后从接口拿数据的效果。

还有一种是运营复制错了json，比如用积分榜单的json复制进了pk榜单，这样也会看不到展示，交付新版本组件的同时提供json/提醒运营用正确的就好

### 3.  配置中发现的，现有组件的功能不满足需求（包括历史bug）

不满足需求的例子：定制行布局，PK榜单需要补充实现，现在和背景图对不齐

历史bug的例子：这里切成出PK结果后没图了

不满足需求的情况。

简单来说，这部分的难点在，需要在评估开发项的时候，就对相关组件能力和需求有非常完善的了解，才能比较全的给出这次要在原有组件上补充什么，否则就只能在配置时发现没这个功能，还挺难的。

历史Bug也类似，原有功能问题也大概只能在配置过程中暴露出来

总之这类bug只能大多数出现在配置的过程中，只能预先多留点buffer。

### 4.  配置使用问题，一般是需求中的某些部分和配置之间的联系不直接（不知道该配哪个或者怎么配）

比如：

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_633405_7jzDzlASqJEfaYz5_1776068525?w=197&h=51&type=image/png)

：小窗其实没有key,会随着赛程阶段自动切换

运营对某些配置项有疑问导致的问题，可以通过给运营写配置文档提前避免一部分[小窗组件使用文档](https://doc.weixin.qq.com/doc/w3_AVgA4QayALACNOUdoRlmtRq2bI3KT?scode=ANYAEAdoABEiYw37PkAcoAUwaCAGc)

# 总结&可能的提效手段：

### 1.  数据问题

文中提到，我们在自测的时候，自己导入数据时，是可以发现配置过程中 "因数据字段缺失，运营看不到预览" 这类问题的

所以可以提前同步给运营让他用新数据，或者直接把自己测试用的数据提供给运营，从而直接避免第二类问题的出现

### 2.平台不能接后端吗？这样不就省了数据配置项，也规避了风险吗

能，是可以的，配置时清空 JSON 数据字段，预览时会直接请求后端接口。

参考[登录 - 猫耳FM-INFO](https://info.missevan.com/pages/viewpage.action?pageId=118071207) 中这部分，配置好相关的query，我们开发过程中也可以在自测阶段自己建立测试页预览：

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_313049_9xj05I8tWYLDrLe5_1776080007?w=902&h=156&type=image/png)

但是有时候并不是后端接口先在uat上线，像这次就是这种情况，所以过程中就会新增数据问题。

不太清楚业务流程，但是能确定的一点是在能走接口的时候一定要通过接口预览而不是复制配置json，安全也快捷

### 3.  mcp

目前配置的流程是运营通过figma的视觉图，用组件以低代码的方式在eva平台构建页面，本质上形成的是那个配置json，这个配置能不能生成一下

figma 官方mcp配置（以claudecode为例）：

方式一：插件安装

claude plugin install figma@claude-plugins-official

装完后按提示走 Figma 登录就行，自动配好。

方式二： MCP Server （推荐）

claude mcp add --scope user --transport http figma https://mcp.figma.com/mcp

跑完重启下claude，重新进claude 输入/mcp 也是认证完就行

然后就可以在对话里给cc粘贴figma链接了

比如： 帮我把这个设计实现成代码：https://figma.com/design/xxxxx/MyFile?node-id=1-2

### 4.

![](https://wdcdn.qpic.cn/MTY4ODg1NTg5MTgyNTYxMA_218226_RF5Wlwtc9OUmp14d_1776079766?w=355&h=120&type=image/png)

还在理解，指的是不走配置平台，通过figma skills 直接生成前端代码，类似于直接把配置过程省去？