## 快速入门

云原生应用管理平台将通用的管理内部控制规范的要求嵌入信息系统，从满足单位通用管理需要出发，支持分级分行业分部门构建组织关系、灵活设置流程权限、自定义通用管理对象数据格式和报表格式，按照云服务的要求，兼顾个性化需求和统一规范管理要求。各类用户分级定义流程和权限在线审批等监管功能；在应用集中管理基础上，归集大数据，推进各类智能应用。满足组织领导和决策用户可以进行多维和级联数据查询，管理驾驶舱和大数据展示需求。满足各类云原生应用开发、测试、部署、发布、监控、运维、升级、下架等全生命周期管理的需要。开放数据接口提供第三方系统用户调用，实现数据开放共享。

云原生应用管理平台不区分租户的属性。如：订阅开发、运维等工具类应用即为开发租户，订阅运营工具的即为运营租户，订阅具体业务应用的租户即为普通应用租户。

## 准备

登录[云原生应用管理平台](http://platform.assetcloud.org.cn)。注册用户，创建单位。

## 一、注册应用

> 前提：已创建单位并登陆系统

1. 进入开发者中心

   <img :src="$withBase('/image/image-20200602164600053.png')">

   <img :src="$withBase('/image/image-20200602164657457.png')">

2) 注册应用

   <img :src="$withBase('/image/image-20200602132910552.png')">

3) 填写应用相关信息

   <img :src="$withBase('/image/image-20200602133356250.png')">

4) 等待平台审核应用

   <img :src="$withBase('/image/image-20200602133410337.png')">

5) 注册通过

   <img :src="$withBase('/image/image-20200602133502258.png')">



## 二、使用 KEY 和 SECRET 进行开发

> 前提：注册申请已通过

### 查看 KEY 和 SECRET

<img :src="$withBase('/image/image-20200602134346378.png')">

<img :src="$withBase('/image/image-20200602134426162.png')">

### 资产云前端 SDK

![Release](https://img.shields.io/github/v/release/SwingCosmic/assetcloud-sdk)

[原始地址](https://github.com/SwingCosmic/assetcloud-sdk)
[旧版文档](https://gitee.com/assetcloud-hdu/doc-cn/tree/master/app-devlep-flow/sdk/front-end/README.md)

1. 导入和初始化

   导入方式有两种，script标签引入或者npm包引入

   #### script标签引入
   ```html
   <script src="dist/sdk.umd.js"></script>
   <script>
   var ac = new ACSDK.SdkClient();
   ac.init().then(function() {
      console.log("SDK已初始化");
   });
   </script>
   ```
   #### npm包引入（推荐）
   ```javascript
   import SdkClient from "@assetcloud/asset-sdk";
   const ac = new SdkClient();
   await ac.init();
   ```

2. 监听和发送消息

   处理消息有两种方法，添加事件监听器和直接异步发送消息并等待返回结果。
   #### 直接监听事件
   支持处理来自平台主动推送的消息。

   ```javascript
   ac.addEventListener("GET_USER", e => {
     console.log(e.data.data.userId);
   });
   ac.send("GET_USER");
   
   ```
   #### 异步发送消息并返回结果
   返回`Promise`，如果平台返回值的`success`字段为`false`，会自动触发reject。

   ```javascript
   try {
     const res = await ac.sendAsync("GET_USER");
     const { userId } = res.data.data;
   } catch (error) {
     console.error(error.data.msg);
   }
   ```

   返回结果类型

    `AssetCloudEvent<T extends AssetCloudMessage>`

   <table>
    <thead>
      <tr>
        <th colspan=2>字段</th>
        <th>类型</th>
        <th>说明</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td rowspan=4>data</td>
        <td>data</td>
        <td><code>AssetCloudMessageMap[T]</code></td>
        <td>承载数据</td>
      </tr>
      <tr>
        <td>code</td>
        <td><code>number</code></td>
        <td>状态码</td>
      </tr>
      <tr>
        <td>success</td>
        <td><code>boolean</code></td>
        <td>是否成功</td>
      </tr>
      <tr>
        <td>msg</td>
        <td><code>string</code></td>
        <td>返回消息</td>
      </tr>
      <tr>
        <td colspan=2>type</td>
        <td><code>T</code></td>
        <td>消息类型</td>
      </tr>
    </tbody>
  </table>



### 前端可用消息

| 功能 | 消息类型 <br />AssetCloudMessage  | 请求参数 | 返回结果中data的格式<br />AssetCloudMessageMap[T] |
| -- | --  | -- | :--: |
| 获取用户 Id           | GET_USER  | 无 | `{ userId: string }` |
| 获取集团Id            | GET_GROUP | 无 | `{ groupIds: string[] }` |
| 在浏览器打开新的标签页   | OPEN_TAB  | 需要打开的url，<br />如："http://www.baidu.com" | - |
| 跳转到平台首页         | GO_HOME   | 无 | - |

### 后端SDK使用
下载地址：https://gitee.com/assetcloud-hdu/doc-cn/releases

新建一个对象```AssetCloudRequest()```

- ```url```：完整的请求路径 http://platform.assetcloud.org.cn/dev-api/+请求路径；

- ```key```、```secret```:平台获取的key和secret

Get调用方法：

```java
AssetCloudRequest request = new AssetCloudRequest();
request.setUrl("");
request.setKey("");
request.setSecret("");
request.setHttpMethods(HttpMethods.GET);
HttpRequestUtil.send(request);
```

Post调用方法：
```java
AssetCloudRequest request = new AssetCloudRequest();
request.setUrl("");
request.setKey("");
request.setSecret("");
request.setBody("");
request.setHttpMethods(HttpMethods.POST);
HttpRequestUtil.send(request);
```

Delete调用方法：

```java
AssetCloudRequest request = new AssetCloudRequest();
request.setUrl("");
request.setKey("");
request.setSecret("");
request.setHttpMethods(HttpMethods.DELETE);
HttpRequestUtil.send(request);
```

Put调用方法：
```java
AssetCloudRequest request = new AssetCloudRequest();
request.setUrl("");
request.setKey("");
request.setSecret("");
request.setBody("");
request.setHttpMethods(HttpMethods.Put);
HttpRequestUtil.send(request);
```
返回结果为：```AssetCloudResponse<T>```

| 字段    | 类型    | 说明     |
| ------- | ------- | -------- |
| code    | int     | 状态码   |
| success | Boolean | 是否成功 |
| data    | T       | 承载数据 |
| msg     | String  | 返回消息 |



### 后端可用接口

1. 获取当前用户信息

   

2. 

## 三、SDK实现说明

### 前端通信

> 通信规则

平台返回数据格式

```javascript
{
  data: {},           //数据
  from: "HOST",       //数据发送源
  to: "",             //数据接收源
  code: "200",        //请求码
  success: true,      //请求状态
  msg: "发送成功",     //请求消息
  type: "GET_USER",   //当前消息类型
}
```

第三方发送数据格式

```javascript
{
  data: {}, //[可选]数据
  from: `${window.location.origin}${window.location.pathname}`, //[必填]数据发送源,固定内容
  to: "*", //[必填]数据接收源，固定内容
  type: "", //[必填]当前消息类型,根据平台提供接口设置
  checkCode: "", //[必填]时间戳
}
```

> 通信方式

1. 监听平台消息，处理返回数据

   ```javascript
   window.addEventListener("message", (e) => {
     if (e.data.from === "HOST") {
       //处理平台返回的数据
       let data = e.data; //来自平台的数据
       if (data.type == "GER_USER") {
         this.getUserInfo(data.data); //调用后端接口访问中台数据
       }
     }
   });
   ```

2. 向平台发送消息，获取请求参数

   ```javascript
   window.top.postMessage(
     {
       data: {},
       from: `${window.location.origin}${window.location.pathname}`,
       to: "*",
       type: "GET_USER",
       checkCode: new Date().getTime(),
     },
     "*"
   );
   ```



### 后端使用 KEY 和 SECRET

应用开发者通过使用 key 和 secret 访问平台接口。

#### 接口访问方式：GET

#### 请求头 Headers：添加应用 key

例如：`key:2b3dd4af198c4f49935de8cadf049fc9D3hw8EcAyOA211Ib47`

<img :src="$withBase('/image/image-20200602154734300.png')">

#### 请求参数类型

> 在请求参数的最后按顺序添加如下参数：
>
> - timestamp: 当前时间戳，精确到毫秒级，long 类型
> - sign: String 类型。所有请求参数(包括上面的时间戳)串联起来(包括&符号)的字符串，根据**HmacSHA256**算法用 secret 生成签名 sign

以访问http://127.0.0.1:8088/asset-system/person/get/person/by/id 接口，传入用户 id，获取用户信息为例：

```javascript
let userId = req.query.userId; //前端通过消息机制获取的平台提供的用户Id
let timestamp = Date.parse(new Date()); //获取当前时间戳

//以CryptoJS为例，调用CryptoJS.HmacSHA256,传入请求参数串联的字符串和应用secret，生成sign
let hash = CryptoJS.HmacSHA256(
  "userId=" + userId + "&timestamp=" + timestamp,
  "c38fac5362014ae497ef355e457740cfB1DD0FF4CF774E64F92952106943E3350703FEE32EE412817FF703DF67FAD1C265B3"
);

request(
  {
    headers: {
      Connection: "close",
      key: "2b3dd4af198c4f49935de8cadf049fc9D3hw8EcAyOA211Ib47",
    },
    url: "http://127.0.0.1:8088/asset-system/person/get/person/by/id",
    method: "GET",
    json: true,
    body: req.body,
    qs: {
      userId: userId,
      timestamp: timestamp,
      sign: hash.toString(CryptoJS.enc.Hex), //转换成字符串
    },
  },
  (error, response, data) => {
    //将平台返回的用户信息发送给前端
    res.send(data);
  }
);
```

Postman 请求示例：

<img :src="$withBase('/image/image-20200602161822800.png')">