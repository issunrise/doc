# TDEX CHAT API
websocket链接: wss://ttt.tdex.com/chat

&nbsp; &nbsp; [业务逻辑](#1.0)  
&nbsp; &nbsp; [心跳握手](#1.1)  
&nbsp; &nbsp; [频道列表](#1.5)  
&nbsp; &nbsp; [加入频道](#1.2)  
&nbsp; &nbsp; [退出频道](#1.3)  
&nbsp; &nbsp; [用户验证](#1.6)  
&nbsp; &nbsp; [设置昵称](#1.7)  
&nbsp; &nbsp; [发送消息](#1.4)  
&nbsp; &nbsp; [错误代码](#1.8)  

---

 <h3 id='1.1'>业务逻辑</h3>

```
  创建连接->心跳握手->获取频道->无验证->禁止发送消息 
                            ->用户验证->有昵称->发送消息
                                     ->无昵称->设置昵称->发送消息
                                     
 ```                                          

 <h3 id='1.1'>心跳握手</h3>

WebSocket API 支持双向心跳，无论是 Server 还是 Client 都可以发起 ping message，对方返回 pong message。

注：返回的数据里面的 "pong" 的值为收到的 "ping" 的值 注：WebSocket Client 和 WebSocket Server 建立连接之后，WebSocket Server 每隔 5s（这个频率可能会变化） 会向 WebSocket Client 发起一次心跳，WebSocket Client 忽略心跳5次后，WebSocket Server 将会主动断开连接。
 
请求参数：

参数名|类型|说明
---|---|---
ping|string|Unix时间戳
pong|string|Unix时间戳

返回参数: 

参数名|类型|说明
---|---|---
ping|string|Unix时间戳
pong|string|Unix时间戳

请求示例:
```
ping=1520414598
```

返回示例：
```
{
    "pong":"1520473202",
}
```

---
<h3 id='1.5'>频道列表</h3>

请求参数：

参数名|类型|说明
---|---|---
id|string|请求ID(时间戳)
task|string|消息类型

返回参数: 

参数名|类型|说明
---|---|---
task|string|消息类型
data|object|数据对象
id|string|请求ID(时间戳)
result|string|消息类型
rooms|array|频道列表

请求示例:
```
id=1520474198&task=sub
```
返回示例:
```
{
    "task":"sub",
    "data":{
        "id":1520474198,
        "result":"success"
        "subs"[
            "roomzh_cn",
            "roomzh_tw",
            "roomen_us",
            "roomservice",
        ]
    }
}
```
---
<h3 id='1.2'>加入频道</h3>

请求参数：

参数名|类型|说明
---|---|---
id|string|请求ID(时间戳)
task|string|消息类型
sub|string|频道ID


返回参数:

参数名|类型|说明
---|---|---
data|object|数据对象
id|string|请求ID(时间戳)
result|string|消息类型
chats|array|该频道信息列表
mtype|int|消息类型(1用户,2客服,3系统)

请求示例:
```
id=1520474198&sub=roomworld&task=join
```
返回示例:
```
{
    "task":"join",
    "data":{
        "id":1520474198,
        "result":"success"
        "chats"[
            {
                "sub":"roomservice",
                "uid":112312,
                "time":1520474198,
                "msg":"helloworld",
                "mtype":1
            },
            {
                "sub":"roomservice",
                "uid":112312,
                "time":1520474198,
                "msg":"hello world",
                "mtype":1
            },
        ]
    }
}
```

加入频道后每当频道用户列表 userupdate 有更新时，client 会收到数据，示例：

参数名|类型|说明
---|---|---
task|string|消息类型
data|object|数据对象
sub|string|频道ID
uid|string|用户ID
action|string|信息(join,exit)
uids|array|该频道用户列表
cnt|int|在线人数

```
{
    "task":"userupdate",
    "data":{
        "sub":"roomworld",
        "uid":112312,
        "action":"join",
        "uids":[112312],
        "cnt":1
    }
}
```

加入频道后每当频道用户 msg 有更新时，client 会收到数据，示例：

参数名|类型|说明
---|---|---
task|string|消息类型
data|object|数据对象
sub|string|频道ID
uid|string|用户ID
time|int64|时间戳
msg|string|信息
mtype|int|消息类型(1用户,2客服,3系统)

```
{
    "task":"msg",
    "data":{
        "sub":"roomservice",
        "uid":112312,
        "time":1520474198,
        "msg":"hello world",
        "mtype":1
    }
}
```
---
<h3 id='1.3'>退出频道</h3>

请求参数：

参数名|类型|说明
---|---|---
id|string|请求ID(时间戳)
task|string|消息类型
uid|string|用户ID
sub|string|频道ID


返回参数:

参数名|类型|说明
---|---|---
data|object|数据对象
id|string|请求ID(时间戳)
result|string|消息类型



请求示例:
```
id=1520474198&uid=123131&sub=roomworld&task=exit
```
返回示例:
```
{
    "task":"exit",
    "data":{
        "id":1520474198,
        "result":"success"
    }
}
```
---
<h3 id='1.6'>用户验证</h3>

请求参数：

参数名|类型|说明
---|---|---
id|string|请求ID(时间戳)
task|string|消息类型
uid|string|用户ID
token|string|频道ID
time|string|有效时间
ssid|string|会话ID（APP的额外参数mytradeId,WEB忽略）

返回参数: 

参数名|类型|说明
---|---|---
data|object|数据对象
id|string|请求ID(时间戳)
result|string|消息类型
data|string|昵称

请求示例:
```
id=1520474198&uid=abc&token=abc&time=1520474198&ssid=xxxx&task=auth
```

```
{
    "task":"auth",
    "data":{
        "id":1520474198,
        "result":"success",
        "data":"nickname"
    }
}
```

---
<h3 id='1.7'>设置昵称</h3>

请求参数：

参数名|类型|说明
---|---|---
id|string|请求ID(时间戳)
task|string|消息类型
nickname|string|昵称

返回参数: 

参数名|类型|说明
---|---|---
data|object|数据对象
id|string|请求ID(时间戳)
result|string|消息类型


请求示例:
```
id=1520474198&nickname=sunrise&task=setuser
```

```
{
    "task":"setuser",
    "data":{
        "id":1520474198,
        "result":"success"
    }
}
```

---
<h3 id='1.4'>发送消息</h3>

请求参数：

参数名|类型|说明
---|---|---
task|string|消息类型
uid|string|用户ID
sub|string|频道ID
msg|string|消息
mtype|string|消息类型(1用户,2客服,3系统)

返回参数: 

参数名|类型|说明
---|---|---
task|string|消息类型
data|object|数据对象
sub|string|频道ID
uid|string|用户ID
time|int64|时间戳
msg|string|信息
mtype|int|消息类型(1用户,2客服,3系统)

请求示例:
```
msg=asdasd&sub=roomservice&task=msg&mtype=1
```

```
{
    "task":"msg",
    "data":{
        "sub":"roomservice",
        "uid":112312,
        "time":1520474198,
        "msg":"hello world",
        "mtype":1
    }
}
```

<h3 id='1.8'>错误代码</h3>

```
1000 缺少参数	
1001 消息类型错误	
1002 信息不能为空	
1003 发送信息太频繁	
1004 Token验证失败	
1005 Token已过期	
1006 Token缺少有效时间	
1007 请先登录	
1008 昵称已存在	
1009 频道异常	
```

返回示例:
```
{
    "task":"error",
    "data":{
        "id":1520474198,
        "code"1000,
        "result":"error message"
    }
}
```
