# Simuse

**目录：**

* [Simuse](#simuse)
  * [Simuse API 说明文档](#simuse-api-说明文档)
    * [使用Simuse API](#使用simuse-api)
    * [数据包](#数据包)
    * [注册mirai\-api\-http Session](#注册mirai-api-http-session)
    * [发送/获取消息](#发送获取消息)
      * [发送消息（简单方式）](#发送消息简单方式)
      * [发送消息（消息链方式）](#发送消息消息链方式)
      * [获取消息](#获取消息)
      * [撤回消息](#撤回消息)
    * [管理](#管理)
      * [禁言成员](#禁言成员)
      * [解除禁言](#解除禁言)
      * [踢出群成员](#踢出群成员)
      * [设置群](#设置群)
      * [设置群成员资料](#设置群成员资料)
      * [退出群聊](#退出群聊)
    * [获取资料](#获取资料)
      * [获取bot好友列表](#获取bot好友列表)
      * [获取bot群列表](#获取bot群列表)
      * [获取群的设置信息](#获取群的设置信息)
      * [获取群的成员列表](#获取群的成员列表)
      * [获取群成员资料](#获取群成员资料)

## Simuse API 说明文档

### 使用Simuse <code>API</code>

Simuse的所有<code>API</code>函数在<code>simuse.py</code>中所定义

通过<code>import</code>方法来导入Simuse<code>API</code>

```python
import simuse
```

并且确定<code>mirai-api-http</code>配置的<code>singlemode</code>为<code>false</code>，使用<code>http</code>模式

---

### 数据包

Simuse将一些<code>mirai-api-http</code>需要的数据打包，Simuse所有<code>API</code>函数的<code>data</code>参数都是此数据包

可以通过<code>Get_data</code>函数获取目录下的数据包（目录下必须有data.json文件，否则抛出异常）

```python
Get_data()
```

**参数：**

| 名字 | 类型 | 举例 | 是否可选 | 说明 |
| :--: | :--: | :--: | :------: | :--: |
|  无  |      |      |          |      |

**返回值：**

| 类型 |  说明  |
| :--: | :----: |
| dict | 数据包 |

**data.json文件的定义：**

```
{
 "Key": "xxxxx", //在mirai-api-http中设置的密钥
 "host": "127.0.0.1", //mirai-api-http中设置的地址（若设置成0.0.0.0，请填写127.0.0.1）
 "port": "8080", //mirai-api-httpmirai-api-http中设置的端口
 "qq": "xxx",  //所登录mirai的QQ号
}
```



当然，也可以自行创建数据包，即定义一个字典变量，给予以下键值对：

<code>data={"Key":"  " "host":"  " "port":"  "qq":"  "}</code>

---

### 注册mirai-api-http Session

取得数据包后，使用<code>Get_Session</code>函数注册一个Session，同时将Session绑定到此数据包

```python
Get_Session(data)
```

**参数：**

| 名字 | 类型 | 举例 | 是否可选 |  说明  |
| :--: | :--: | :--: | :------: | :----: |
| data | dict |      |    F     | 数据包 |

**返回值：**

| 类型 |                 说明                  |
| :--: | :-----------------------------------: |
| dict | 返回的新数据包中将带有所绑定的Session |

**例：**

```python
data = Get_data()
data = Get_Session(data)  # 只需获取一次（程序中存在异步调用或线程并发除外）
Send_Message(data,123456,2,'你好',1)
Send_Message(data,123456,2,'早上好',1) # 示例为向QQ123456发送消息“你好”和“早上好”
```

**注意：**

每次调用<code>Get_Session</code>函数，返回值中绑定的Session都将不同，

---

### 发送/获取消息

#### 发送消息（简单方式）

```python
Send_Message(data,target,target_type,message,message_type,path)
```

**参数：**

|     名字     |   类型   |  举例  | 是否可选 |                             说明                             |
| :----------: | :------: | :----: | :------: | :----------------------------------------------------------: |
|     data     |   dict   |        |    F     |                            数据包                            |
|    target    | int/dict | 123456 |    F     | 群号/QQ号/{'qq':QQ号,'group':群号}(当target_type为3时按此格式传入字典) |
| target_type  |   int    |   1    |    F     |                   1-群，2-私聊，3-临时会话                   |
|   message    |   str    |  你好  |    F     |             需发送的文字，图片url或本地图片路径              |
| message_type |   int    |   1    |    F     |                        1-文字，2-图片                        |
|     path     |   int    |   1    |    T     | 若path=1，则按照本地路径的方式发送图片，message需为图片的路径 |

**返回值：**

| 类型 |       说明       |
| :--: | :--------------: |
| int  | 本次发送的消息id |

---

#### 发送消息（消息链方式）

```python
Send_Message_Chain(data,target,target_type,messagechain)
```

**参数：**

|     名字     |   类型   |  举例  | 是否可选 |                             说明                             |
| :----------: | :------: | :----: | :------: | :----------------------------------------------------------: |
|     data     |   dict   |        |    F     |                            数据包                            |
|    target    | int/dict | 123456 |    F     | 群号/QQ号/{'qq':QQ号,'group':群号}(当target_type为3时按此格式传入字典) |
| target_type  |   int    |   1    |    F     |                   1-群，2-私聊，3-临时会话                   |
| messagechain |   list   |        |    F     |                            消息链                            |

**返回值：**

| 类型 |       说明       |
| :--: | :--------------: |
| int  | 本次发送的消息id |

------

#### 获取消息

```python
Fetch_Message(data)
```

**参数：**

| 名字 | 类型 | 举例 | 是否可选 |  说明  |
| :--: | :--: | :--: | :------: | :----: |
| data | dict |      |    F     | 数据包 |

**返回值：**

| 类型 |  说明  |
| :--: | :----: |
| list | 消息链 |

---

#### 撤回消息

```python
Recall_Message(data,messageid)
```

**参数：**

|   名字    | 类型 |  举例  | 是否可选 |      说明      |
| :-------: | :--: | :----: | :------: | :------------: |
|   data    | dict |        |    F     |     数据包     |
| messageid | int  | 123456 |    F     | 需撤回的消息id |

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

### 管理

#### 禁言成员

（需要bot有相关权限）

```python
Mute(data,target,member,time)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |      说明       |
| :----: | :--: | :----: | :------: | :-------------: |
|  data  | dict |        |    F     |     数据包      |
| target | int  | 123456 |    F     |      群号       |
| member | int  | 123456 |    T     |   群成员QQ号    |
|  time  | int  |   60   |    T     | 禁言时间，单位S |

若不传入<code>member</code>，则默认全体禁言

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

#### 解除禁言

（需要bot有相关权限）

```python
Unmute(data,target,member)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |    说明    |
| :----: | :--: | :----: | :------: | :--------: |
|  data  | dict |        |    F     |   数据包   |
| target | int  | 123456 |    F     |    群号    |
| member | int  | 123456 |    T     | 群成员QQ号 |

若不传入<code>member</code>，则解除全体禁言

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

#### 踢出群成员

（需要bot有相关权限）

```python
Kick(data,target,member)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |    说明    |
| :----: | :--: | :----: | :------: | :--------: |
|  data  | dict |        |    F     |   数据包   |
| target | int  | 123456 |    F     |    群号    |
| member | int  | 123456 |    T     | 群成员QQ号 |

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

#### 设置群

（需要bot有相关权限）

```python
Group_Config(data,target,config)
```

**参数：**

|  名字  | 类型 |             举例              | 是否可选 |    说明    |
| :----: | :--: | :---------------------------: | :------: | :--------: |
|  data  | dict |                               |    F     |   数据包   |
| target | int  |            123456             |    F     |    群号    |
| config | dict | name='ok',announcement='test' |    F     | 需设置内容 |

**config中的参数名称：**

|       名字        | 类型 |         说明         |
| :---------------: | :--: | :------------------: |
|       name        | str  |         群名         |
|   announcement    | str  |        群公告        |
|    confessTalk    | bool |    是否开启坦白说    |
| allowMemberInvite | bool |   是否允许群员邀请   |
|    autoApprove    | bool | 是否开启自动审批入群 |
|   anonymousChat   | bool |   是否允许匿名聊天   |

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

#### 设置群成员资料

（需要bot有相关权限）

```python
Member_Info(data,target,memberid,config)
```

**参数：**

|   名字   | 类型 |             举例              | 是否可选 |    说明    |
| :------: | :--: | :---------------------------: | :------: | :--------: |
|   data   | dict |                               |    F     |   数据包   |
|  target  | int  |            123456             |    F     |    群号    |
| memberid | int  |            123456             |    F     |  群成员QQ  |
|  config  | dict | name='ok',specialTitle='test' |    F     | 需设置内容 |

**config中的参数名称：**

|     名字     | 类型 |  说明  |
| :----------: | :--: | :----: |
|     name     | str  | 群名片 |
| specialTitle | str  | 群头衔 |

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

#### 退出群聊

```python
Quit(data,target)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |  说明  |
| :----: | :--: | :----: | :------: | :----: |
|  data  | dict |        |    F     | 数据包 |
| target | int  | 123456 |    F     |  群号  |

**返回值：**

| 类型 |     说明      |
| :--: | :-----------: |
| dict | 来自mas的响应 |

---

### 获取资料

#### 获取bot好友列表

```python
Get_Friend(data)
```

**参数：**

| 名字 | 类型 | 举例 | 是否可选 |  说明  |
| :--: | :--: | :--: | :------: | :----: |
| data | dict |      |    F     | 数据包 |

**返回值：**

| 类型 |   说明   |
| :--: | :------: |
| list | 好友列表 |

---

#### 获取bot群列表

```python
Get_Group(data)
```

**参数：**

| 名字 | 类型 | 举例 | 是否可选 |  说明  |
| :--: | :--: | :--: | :------: | :----: |
| data | dict |      |    F     | 数据包 |

**返回值：**

| 类型 |  说明  |
| :--: | :----: |
| list | 群列表 |

---

#### 获取群的设置信息

```python
Get_Groupinfo(data,target)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |  说明  |
| :----: | :--: | :----: | :------: | :----: |
|  data  | dict |        |    F     | 数据包 |
| target | int  | 123456 |    F     |  群号  |

**返回值：**

| 类型 |    说明    |
| :--: | :--------: |
| dict | 群设置信息 |

----

#### 获取群的成员列表

```python
Get_Groupmember(data,target,deal)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |                             说明                             |
| :----: | :--: | :----: | :------: | :----------------------------------------------------------: |
|  data  | dict |        |    F     |                            数据包                            |
| target | int  | 123456 |    F     |                             群号                             |
|  deal  | int  |   0    |    T     | 若此值为0则还可返回群成员的 joinTimestamp-入群时间戳，lastSpeakTimestamp-最后一次发言时间戳，muteTimeRemaining-禁言剩余时间 |

**返回值：**

| 类型 |    说明    |
| :--: | :--------: |
| list | 群成员列表 |

---

#### 获取群成员资料

```python
 Get_memberinfo(data,target,id,deal)
```

**参数：**

|  名字  | 类型 |  举例  | 是否可选 |                             说明                             |
| :----: | :--: | :----: | :------: | :----------------------------------------------------------: |
|  data  | dict |        |    F     |                            数据包                            |
| target | int  | 123456 |    F     |                             群号                             |
|   id   | int  | 123456 |    F     |                          群成员QQ号                          |
|  deal  | int  |   0    |    T     | 若此值为0则还可返回群成员的 joinTimestamp-入群时间戳，lastSpeakTimestamp-最后一次发言时间戳 |

**返回值：**

| 类型 |    说明    |
| :--: | :--------: |
| dict | 群成员资料 |
