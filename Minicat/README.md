
# 作业
- 作业1 - 代码实现视频讲解

    1、在resources文件夹下新增server.xml文件，添加配置

    2、在Bootstrap.java类中新增loadServerConfig方法，用于加载server.xml文件的配置信息

    3、在Bootstrap.java类中新增loadProjectServlet方法，用于加载webapps下项目的servlet

    4、修改导师的loadServlet方法，使该方法也能加载项目录下的servlet

- 作业二 - 描述Tomcat体系结构

## Tomcat体系架构

### 架构图

![image-20200712173505452](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20200712173505452.png)

### 组件模块

| 构件                          |     子构件      | 说明                                                         |
| ----------------------------- | :-------------: | ------------------------------------------------------------ |
| 1.connector   <br/>*Coyote*   |        —        | 连接器，也叫做**Coyote**。包含了3个子组件。<br/>主要功能是基于TCP/IP协议的Socket通信。同时处理Http协议。<br/>所以也是一个HTTP服务器。 |
|                               |    EndPoint     | 主要处理Socket通信，建立在TCP/IP协议栈。<br/>这里涉及到了**网络IO模型**：**BIO**，**NIO**，**AIO** |
|                               |    Processor    | 处理应用层协议HTTP。有HTTP/1.1，和HTTP/2.0                   |
|                               | ProtocolHandler | 是上面两个组件的统称。有6个协议处理接口实现：<br/>**Http11NioProtocol** (HTTP/1.1的NIO模型)<br/>**Http11Nio2Protocol** (HTTP/1.1的AIO模型)<br/>Http11AprProtocol (基于JNI, native方式支持, 需要安装runtime库)<br>AjpNioProtocol (APJ是二进制的TCP传输协议, 但浏览器不支持，且其他http server也不支持，所以就是鸡肋)<br/> AjpAprProtocol<br/> AjpNio2Protocol |
|                               |     Adapter     | 对象适配器。主要处理原生**Request/Response** 和 **ServletHttp/ServletResponse** 的转换。 |
| 2.container   <br/>*Catalina* |        —        | Servlet容器，也叫做**Catalina**。主要处理Servlet，它实现了Servlet规范。 |
|                               |     Engine      | 一个顶层容器，定义了一些基本的关联关系。                     |
|                               |      Host       | 多个虚拟主机。可以认为就是域名，可以对应多个域名。           |
|                               |     Context     | 表示Web应用程序 (其实就是uri来进行区分)。每个Host下可以有多个Context，用于管理Servlet实例的容器，即可以管理多个Servlet。 |
|                               |     Wrapper     | 一个Context可以持有多个Wrapper。负责具体的一个Servlet，如Servlet装载，初始化，执行，回收。<br>实现类是`StandardWrapper`, 以及负责初始化配置的`ServletConfig` |



`server.xml` 与 各组件的关系如下图：

![image-20200712173427545](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20200712173427545.png)



### 3. 各个组件之间关系，路由映射关系

结构骨架如下：

```xml
<Server port="8005" shutdown="SHUTDOWN">

  <Service name="Catalina">
    <Executor name="commonThreadPool"
      namePrefix="thread-exec-"
      maxThreads="200"
      minSpareThreads="100"
      maxIdleTime="60000"
      maxQueueSize="Integer.MAX_VALUE"
      prestartminSpareThreads="false"
      threadPriority="5"
      className="org.apache.catalina.core.StandardThreadExecutor"/>
 
    <Connector port="8080" 
              protocol="HTTP/1.1"
              executor="commonThreadPool"
              maxThreads="1000"
              minSpareThreads="100"
              acceptCount="1000"
              maxConnections="1000"
              connectionTimeout="20000"
              compression="on"
              compressionMinSize="2048"
              disableUploadTimeout="true"
              redirectPort="8443"
              URIEncoding="UTF-8" />

    <Engine name="Catalina" defaultHost="localhost">
      <Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
        <Context docBase="/Users/yingdian/web_demo" path="/web_demo"/>
      </Host>
      <Host name="www.abc.com"  appBase="webapps" unpackWARs="true" autoDeploy="true"/>
    </Engine>
  </Service>
</Server>
```


