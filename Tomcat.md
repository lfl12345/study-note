# Tomcat

## Tomcat基础

### web概念

软件架构：

* C/S：客户端/服务器端
* B/S：浏览器/服务端

资源分类：

* 静态资源：所有用户访问后，得到的结构都是一样的，称为静态资源，静态资源可以直接被浏览器解析。例如：HTML，css，JavaScript，jpg
* 动态资源：每个用户访问相同资源后，得到的结果可能不一样，称为动态资源。动态资源被访问后，需要转换为静态资源，再返回给浏览器，通过浏览器进行解析。例如：servlet、jsp

网络通信三要素

1. IP
2. 端口
3. 传输协议

### 常见的web服务器

#### 概念

服务器：安装了服务器软件的计算机

服务器软件：接收用户请求，处理请求，作出响应

web服务器软件：接收用户的请求，作出响应，在web服务器中，可以部署web项目，让用户通过浏览器来访问这些项目。

#### 常见的web服务器软件

* webLogic
* webSphere
* JBOSS
* Tomact，用于中小型JavaEE服务器，仅支持少量的JavaEE规范servlet/jsp

### Tomcat目录结构

| 目录    | 目录下文件               | 说明                                                         |
| ------- | ------------------------ | ------------------------------------------------------------ |
| bin     | /                        | 存放Tomcat的启动、停止等批处理脚本文件                       |
|         | startup.bat\startup.sh   | 用于在Windows和linux下的启动脚本                             |
|         | shutdown.bat\shutdown.sh | 用于在Windows和linux下的停止脚本                             |
| conf    | /                        | 用于存放Tomcat的相关配置文件                                 |
|         | Catalina                 | 用于存储每个虚拟机的Context配置                              |
|         | context.xml              | 用于定义所有web应用均需加载的context配置，如果web应用指定了自己的context.xml，该文件将被覆盖 |
|         | catalina.properties      | Tomcat的环境变量配置                                         |
|         | catalina.policy          | Tomcat运行的安全策略配置                                     |
|         | logging.properties       | Tomcat的日志配置文件，可以通过该文件修改Tomcat的日志级别和日志路径 |
|         | server.xml               | Tomcat服务器的核心配置文件                                   |
|         | tomcat-users.xml         | 定义Tomcat默认的用于及角色映射信息                           |
|         | web.xml                  | Tomcat中所有应用默认的部署描述文件，主要定义了基础servlet和MIME映射 |
| lib     | \                        | Tomcat服务器的依赖包                                         |
| logs    |                          | Tomcat默认的日志存放目录                                     |
| webapps |                          | Tomcat默认的web应用部署目录                                  |
| work    |                          | web应用JSP代码生成和编译的临时目录                           |

### Tomcat启动和停止

 启动：双击bin/startup.bat

停止：双击bin/shutdown.bat

## Tomcat架构

### Tomcat整体架构













