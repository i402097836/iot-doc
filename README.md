# iot-doc
about iot developer guide document
# Eastsoft IoT Cloud

开发文档

---sadfasdfasdf

##<a name="limits"/>Eastsoft IoT 限制
以下限制适用于消息代理：

| 名字   | 描述          |
|   :-------------------   | :------------------   |
| 主题长度限制 | 发布消息的时候传递到消息代理的主题不能超过256个字节编码（UTF-8）。|
| 主题前缀限制 | 主题开头为'$'的被认为是预留主题，除为Thing Shadows服务工作外，不支持发布和订阅。  |
| 在主题和主题过滤中斜杠的最大数量    | 发布消息时提供的主题或者订阅时提供的主题过滤中不能定义超过八个斜杠（/）。|
| 客户端ID大小限制     | 128个字节编码（UTF-8）。|
| 客户端ID前缀限制     | '$'是内部产生的客户端ID的预留前缀。|
| 邮件大小限制         | 每一个发布消息的有效载荷被限制为128KB以内。在Eastsoft IoT服务中将拒绝超过此大小的邮件。     |
| 每个连接的吞吐量	   | Eastsoft IoT限制每个客户端连接入口和出口速率最高到512KB/S。以更高的速率发送或接收的数据将以最高吞吐量进行节流。|
| 每个订阅调用的最大订阅量     | 一个单一的订阅调用请求限最多八个订阅。     |
| 每个会话订阅	| 消息代理限制每个客户端会话订阅最高达50个。订阅请求总数超过50的将导致连接被断开。|
| 最大连接闲置	| 闲置30分钟后的MQTT客户端自动断开连接。当客户端发送一个PUBLISH、SUBSCRIBE、PING、或PUBACK消息时，30分钟闲置计时器复位。|
| 最大入站未确认的消息		| 消息代理允许最多有100条未确认的消息（对所有需要ACK确认消息的限制）。当达到此限制时，除非服务器返回一个ACK，否则将不会接受新的消息。|
| 最大出站未确认的消息    | 消息代理仅允许最多有100条未确认的消息（对所有需要ACK确认消息的限制）。当达到此限制时，将不会有新的信息被发送到客户端，直到客户端确认正在进行的消息。|
| 传递QoS为1消息的最大重试间隔 | 如果一个连接的客户端一个小时都无法接收到ACK上QoS为1的消息，消息代理将丢弃该报文。如果它有100条正在进行的消息，客户端可能无法收到其他消息，它有可能由于过大的有效载荷被节流，或产生其他错误。|

以下限制适用于thing shadows：

* ~~以JSON形式的文件的最大不能超过8KB~~。
* 每个帐户下，您可以存储无限量的JSON对象。
* 如果一个thing shadow在超过12个月以上一直没有更新或检索将会被Eastsoft IoT删除。  

以下限制适用于安全性和身份：

* ~~您最多可以给Eastsoft IoT证书附加十个权限~~。
* ~~你可以拥有多达五个版本的权限命名~~。
* ~~权限文件大小限制为2048个字符（不包括空格）~~。

# Eastsoft IoT Cloud

开发文档

---

##<a name="creatRule"/>创建规则
您可以给连接的things中的路由数据配置规则。规则包括以下内容：

- 规则名称
  + 该规则的名称。
* 可选的说明
   * 该规则的文本描述。
* SQL语句
  * 简化的SQL语法，过滤MQTT主题上接收到的消息，执行指定操作。欲了解更多信息，请参阅[Eastsoft IoT SQL参考]()。
* 一个或多个动作
  * 指定的动作。例如，您可以向MongoDB表中插入数据，发送Email、Http服务或调用republish服务。

当您创建一个规则时，要知道向主题发布的数据量。如果您创建包含通配符主题模式的规则，他们可能匹配大部分的信息，你可能需要增加目标动作的资源处理能力。另外，如果你创建一个新的规则，其中包括一个通配符的主题模式，可能会导致无限循环的情况。

**创建一个规则**

调用[创建规则API]()或使用[Eastsoft IoT Cloud前台页面]()创建一条新的规则


下面例子是，将发送到IOT/test主题的所有消息插入到指定的MongoDB表中。SQL语句过滤这些消息。

    {
        "name": "rulename", 
        "description": "规则描述", 
        "sql": "SELECT * FROM 'iot/test'", 
        "status": 0, //0 disable, else enable
        "actions": [{
            "mongodb": {
                "url": "jdbc:mysql://*/iotplatform", 
                "tablename": "hello", 
                "username": "eastsoft", 
                "password": "eastsoft"
            }
        }]
    }


下面是执行发送Email动作的规则引擎例子：

    {
        "name": "rulename", 
        "description": "规则描述", 
        "sql": "SELECT * FROM 'iot/test'", 
        "status": 0, //0 disable, else enable
        "actions": [{
            "email": {
                "receiver": "hanmeimei@gmail.com;lileilei@gmail.com", 
                "title": "title1"
            }
        }]
    }
下面是执行调用Http服务动作的规则例子：

    {
        "name": "rulename", 
        "description": "规则描述", 
        "sql": "SELECT * FROM 'iot/test'", 
        "status": 0, //0 disable, else enable
        "actions": [{
            "http": {
                "url": www.eastsoft.com.cn,
                "method": "get"，
                "headers":"{"username":"admin","password":"admin"}"
            }
        }]
    }
下面向不同的MQTT主题重新发布规则的例子：

    {
        "name": "rulename", 
        "description": "规则描述", 
        "sql": "SELECT * FROM 'iot/test'", 
        "status": 0, //0 disable, else enable
        "actions": [{
            "republish": {
                "topic": "iot/pub/test"
            }
        }]
    }


