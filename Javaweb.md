# Servlet概述

动态资源: 被访问后, 需要线**转换**为静态资源, 返回给浏览器解析

通信三要素: ip 端口号 传输协议

- tcp 安全协议 三次握手 速度稍慢
- udp 广播协议  不安全 速度快



web服务器:

​	安装了服务器软件的计算机 (性能比较好) : 接受请求,处理请求,做出响应

​	可以在web服务器部署web项目, 用浏览器来访问

​	又称为web容器

常见java相关的web服务器软件

- webLogic : Oracle 大型javaee服务器, 收费的 , 支持所有javaee规范
- webSphere: IBM 大型javaee服务器, 收费的 , 支持所有javaee规范
- JBOSS : JBOSS公司 大型javaee服务器, 收费的 , 支持所有javaee规范
- tomcat apache基金组织  中小型企业javaee服务器, 仅仅支持少量的javaee规范
  - 开源的 免费的

**javaEE** : 是java语言在企业级开发中使用的技术规范的综合, 一共规定了13项大的规范



Tomcat:

- 启动 bin/startup.bat 双击
- 访问 8080
- 可能遇到的问题
  - 黑窗口一闪而过
    - 没正确配置java_home环境变量 
  - 启动报错: 端口号被占用
    - netstat -ano 指令 查看本机所有运行的端口  在任务管理器关掉占用的app
    - 修改自身端口号  conf/server.xml
  - 一般会将tomcat的默认端口改为80,  
    - 80端口号是http协议的默认端口号 访问的时候可以不写端口号 
- 正常关闭
  - bin/shutdown.bat
  - ctrl+c 在黑窗口按



## Servlet : server applet

servlet 就是一个接口, 定义了java类被浏览器访问, 或者说被tomcat识别的规则

 **我们自定义一个类, 实现servlet接口, 覆写它的方法, 让tomcat识别**

```java
自己写一个类, 并实现servlet 和它里面的五个抽象方法
    
public class ServletDemo1 implements Servlet {
    //这个方法是提供服务的方法
    public void service
}
```



```xml
配置servlet在web.xml
<web-app>
  <display-name>Archetype Created Web Application</display-name>
<!--  配置servlet -->
  <servlet>
    <servlet-name>demo1</servlet-name>
    <servlet-class>lin.servlet.ServletDemo1</servlet-class>
  </servlet>
<!--  配置访问的路径 两个name对应-->
  <servlet-mapping>
    <servlet-name>demo1</servlet-name>
    <url-pattern>/demo1</url-pattern>
  </servlet-mapping>
</web-app>
```



## servlet的执行原理

- 服务器会根据客户端浏览器请求 ,解析url,获取访问servlet的资源路径
- 查找web.xml文件, 是否有对应的url-pattern标签体内容
- 如果有在找对应的servlet-class全了类名
- tomcat将全类名对应的字节码文件加载进内存, 用反射 .Class.forname
- 创建对象 cls.newInstence
- 调用service方法



## servlet的生命周期

```java
init 一次 servlet被创建
    它的作用是加载资源的, 资源之间有依赖,可以通过配置创建时机调整优先级?
    默认情况下,第一次访问时, servlet被创建
    可以去配置指定servlet创建的时机
    <servlet>
    <servlet-name>demo2</servlet-name>
    <servlet-class>lin.servlet.ServletDemo2</servlet-class>
<!--    指定servlet创建的时机 当服务器启动的时候创建 值大于等于0
  默认是被调用时创建 初始值是-1 -->
    <load-on-startup>5</load-on-startup>
  </servlet>
        
    servlet在内存中只存着一个对象, 它是单例的 多用户访问可能存着线程安全问题
    解决:尽量不要在servlet中定义成员变量,用局部变量. 
        就算定义了成员变量,也不要修改值;
        
service 多次
    每次访问servlet都会被调用一次
destroy 一次
    服务器关闭时 servlet销毁, 会执行一次, 只有服务器正常关闭才会执行
```



## servlet3.0 注解

可以不用xml

```java
@WebServlet("/demo")
在实现servlet的类上写资源路径即可  
    <!--        servlet 3.0 支持注解 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>
```

## servlet的体系结构

servlet是一个接口 里面有五个方法

**浏览器直接请求url是get方式 ,  可以通过表单发送post请求**

**get请求会把参数拼接在url中**

实现类

- GenericServlet  抽象类

  - 将Servlet接口的其他方法做了默认的空实现, 只将service方法抽象,只实现这个方法就可以, 其他方法也可以被覆写

  - ```
    //继承这个抽象的实现类, 只需要覆写service一个方法
    @WebServlet("/demo2")
    public class ServletDemo2  extends GenericServlet {
        @Override
        public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
            System.out.println("demo2...");
        }
    }
    ```

- **HttpServlet** extends GenericServlet  也是个抽象类

  - 封装了http协议,简化我们的操作,  里面做了请求方式的判断,  如果是get请求 则doget方法, 如果是post方法, 则调用dopost方法

  - 使用: 继承httpservlet 覆写doget和dopost方法

  - ```java
    //继承这个抽象的实现类, 只需要覆写service一个方法
    //浏览器默认请求是get请求  发送/demo3请求,下面的doGet方法会被调用
    @WebServlet("/demo3")
    public class ServletDemo3 extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("doget方法调用了...");
        }
    
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("dopost方法调用了...");
        }
    }
    
    -------
    html
    <body>
    <!--访问 http://localhost:8080/login.html -->
    <form action="/demo3" method="post">
        <input type="text" name="username">
        <input type="submit" value="提交">
        //提交之后 dopost方法会被调用 
    </form>
    </body>
    </html>
    ```



## Servlet相关配置

- urlPartten   servlet的访问路径  可以定义多个, 一般就定义一个

  - ```
    @WebServlet({"/demo4","/dd4","/d4"})
    public class ServletDemo4 extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("doget方法调用了...");
        }
    }
    ```

  - 定义规则

    - /xxx

    - /xxx/xx   /xxx/* 通配  /* 写什么都能访问到这个servlet 这个的优先级是很低的

    - `@WebServlet("*.do")`   `访问localhost:8080/demo.do`

    - ```
      //@WebServlet({"/demo4","/dd4","/d4"})
      //@WebServlet({"/d1/dd2"})
      @WebServlet("*.do")
      ```



# http请求消息

hyper text transfer protocol 超文本传输协议

传输协议: 定义了, 客户端和服务端通信时,发送数据的格式 

特点:

- 基于tcp/ip 的高级协议
- 默认端口80
- 基于请求响应模型的, 一次请求对应一次响应
- 无状态的协议   每次请求之间相互独立, 不能交互数据

历史版本: 

- 1.0 每一次请求响应都会建立新的连接
- 1.1  会复用连接 

请求消息数据格式

- 请求行
  -  请求方式 请求url 请求协议/版本
  - http有七种请求方式, 常用的有两种
    - GET 请求参数在请求行中,在url后长度是有限制的 不安全
    - POST 请求参数在请求体中,url长度是没有限制的, 可以发送更大的 相对安全
- 请求头
  -  请求头名称 : 请求头值
  - 常见请求头
    - host 请求的ip
    - user-agent   浏览器版本信息   告诉服务器  **解决浏览器兼容问题**
    - Accept: 告诉服务器, 客户端可接受的文件类型
    - Connection :连接状态  复用相关 
    - Referer: http:localhost/login.html 告诉服务器 请求从哪里来,  防盗链
    - if(referer.equals("优酷首页")){ 播放电影 }
- 请求空行
  - 就是一个空行,  分隔post请求头和请求体
- 请求体(正文)
  - 封装 post请求消息的请求参数





```html
原始请求头
GET /login.html HTTP/1.1
Host: localhost:8080
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:79.0) Gecko/20100101 Firefox/79.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: keep-alive

username=aaaa
```



# Request对象

request对象和Response对象的原理

- 这两个对象是服务器创建的, 我们来使用它们
- Request对象是来获取请求消息的
- Response对象是来设置响应消息



一次请求发生的事情,还可以参照servlet的执行原理章节内容

- tomcat服务器会根据请求url中的资源路径创建对应的servletDemo的对象
- tomcat服务器, 会创建Request和Response对象, request对象封装请求消息数据
- tomcat服务器将Request和Response两个对象传递给service方法, 并且去调用service方法
- 程序员可以通过Request对象获取请求消息数据, 通过对Response对象设置响应消息数据
- 服务器在给浏览器做出响应之前, 会从Response对象中拿程序员设置的响应消息数据



## Request对象的继承体系结构

```java
interface ServletRequest 
interface HttpServletRequest extends ServletRequest 
上面这个的实现类是tomcat创建的下面这个RequestFacade
org.apache.catalina.connector.RequestFacade@206e2510
```



## Request功能

```js
URL 统一资源定位符 http://localhost:8080/RequestDemo1 这个是唯一的资源范围小
URI 统一资源标识符 /RequestDemo1  这个范围大
```



获取请求消息数据

### 获取请求行数据  

GET /DEMO1?NAME=ZHANGSAN HTTP/1.1

- 获取请求方式 getMethod   GET
- **获取虚拟目录 getContextPath  /**
- 获取servlet路径 geteServletPath /DEMO1
- 获取get请求参数 getQueryString  NAME=ZHANGSAN
- **获取请求的uri  getRequestURI  /DEMO1**
- 获取请求的url  getRequsetURL  HTTP://localhost:8080/demo1
- 获取协议版本号 getProtocol  HTTP/1.1
- 获取客户机ip  getRemoteAddr

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        System.out.println(req.getMethod());
        System.out.println(req.getContextPath());
        System.out.println(req.getServletPath());
        System.out.println(req.getQueryString());
        System.out.println(req.getRequestURI());
        System.out.println(req.getRequestURL());
        System.out.println(req.getProtocol());
        System.out.println(req.getRemoteAddr());
/*
            GET

            /RequestDemo1
            null
            /RequestDemo1
            http://localhost:8080/RequestDemo1
            HTTP/1.1
            0:0:0:0:0:0:0:1
*/
    }
```



### 获取请求头数据

- ```
   req.getHeaderNames();
   req.getHeader(String name); 根据头的名字获取头的值 这个用的多 
   例如
   System.out.println(req.getHeader("user-agent"));
  ```

示例代码如下

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取所有请求头名称
        final Enumeration<String> headerNames = req.getHeaderNames();
        //遍历
        while (headerNames.hasMoreElements()){
            final String name = headerNames.nextElement();
        //根据名称获取请求头的值
            final String header = req.getHeader(name);
            System.out.println(name+" : "+header);
        }
        /*
        host : localhost:8080
        user-agent : Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:79.0) Gecko/20100101 Firefox/79.0
        accept : text/html,application/xhtml+xml,application/xml;q=0.9,image/webp, *;q=0.8
        accept-language : zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
        accept-encoding : gzip, deflate
        connection : keep-alive
        cookie : remember-me=emhhbmdzYW46MTU5N6s8uuJJHEE7BCB2A49BF42574313C1很长
        upgrade-insecure-requests : 1
         */
    }
-----------------------------------
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // getHeader这个方法用的比较多
        final String agent = req.getHeader("user-agent");
        System.out.println(agent);
        if (agent.contains("Chrome")){
            System.out.println("是来自谷歌的访问");
        }else if (agent.contains("Firefox")){
            System.out.println("是来自火狐的访问");
        }
    }

--------------------------------
     @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //演示获取请求头数据 referer 我从哪里来
        final String referer = req.getHeader("referer");
        System.out.println(referer);
        //http://localhost:8080/login.html
        if (referer != null){
            if (referer.contains("localhost")) {
                System.out.println("播放电影");
            }else{
                System.out.println("想看电影吗 来优酷吧");
            }
        }
    }
```



### 获取请求体数据

请求体只有post方式有请求体, 请求体中封装了post请求的请求参数

- 步骤

  - 获取流对象

    - getReader  获取字符输入流 
    - getInputStream 获取字节输入流

  - 从流对象中拿数据

  - ```
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求消息体
        //获取字符流
        final BufferedReader reader = req.getReader();
        //读取数据
        String line = null;
        while ((line = reader.readLine())!=null) {
            System.out.println(line);
            //username=%E5%BC%A0%E4%B8%89&password=123
        }
    }
    ```

### post中文乱码



```java

//获取字符输出流对象之前, 设置字符集
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
---------------
String username = req.getParameter("username");
get方式 tomcat 将get方式中文乱码解决了
post方式  依旧会乱码 


  @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        final String username = req.getParameter("username");
        System.out.println("get");
        System.out.println(username);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置流的编码格式,解决post请求中文乱码问题
        req.setCharacterEncoding("utf-8");
        this.doGet(req,resp);
    }
===========================
    如果使用tomcat7 接受到乱码的参数可以使用以下代码进行转码
    String rname = req.getparameter("ranme");
	rname = new String(rname.getBytes("iso-8859-1"),"utf-8")
```

### *其他常用方法

- 获取请求参数通用方式  getpost方式都可以用以下方法

  - String getParameter (string name) 根据参数名称获取参数值, username 返回张三
  - String[] getParameterValues (string name)  返回值是字符串数组 同上 用于复选框
  - Enumeration<string> getParameterNames 获取所有请求的参数名称
  - Map<string, String[]> getParameterMap()  获取所有参数的map集合

  ```java
    @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          final String username = req.getParameter("username");
          System.out.println("get");
          System.out.println(username);
      }
  
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         /* final String username = req.getParameter("username");
          System.out.println("post");
          System.out.println(username);
          //用表单演示, 此处打印提交用户名 lisi */
          //如果两个里面逻辑一样, 可以在一个中调用另外一个
          this.doGet(req,resp);
      }
  ```

  

## 请求转发

- 服务器**内部**的资源跳转,比如servlet之间跳转,  但是不能跨域 

- 浏览器地址栏路径没有发生变化

- 这种转发是一次请求, 多个资源之间使用的是一次请求

- ```
  interface ServletRequest {
  public RequestDispatcher getRequestDispatcher(String path);
  //通过Request对象获取请求转发器对象
  //使用RequestDispatcher对象进行转发
  /Requestdispatcher中有如下方法
  public void forward(ServletRequest request, ServletResponse response)
         --------------
          @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          System.out.println("demo8888被访问了...");
          //转发到demo9
          req.getRequestDispatcher("/RequestDemo9").forward(req,resp);
  
      }
  ```
  
- 共享数据

  - **域对象:  一个有作用范围的对象, 可以在范围内共享数据**

  - Request域, 代表一次请求的范围 , 一般用于请求转发的多个资源中共享数据

    - 方法

    - setAttribute(String name, Object  obj)存储数据

    - getattribute(String name) 通过键获取值

    - removeAttribute(String name ) 通过键删除值

    - ```
      req.setAttribute("msg","hello");
      req.getRequestDispatcher("/RequestDemo9").forward(req,resp);
      //然后在demo9中  就可以获取
      System.out.println(req.getAttribute("msg"));
      ```

- 获取servletContext对象

  - ```
    final ServletContext servletContext = req.getServletContext();
    ```

## 一个登陆案例

```
F:\workspace\base\javaStart02\day02-Servlet-login
```



- 创建项目, 导入静态页面, 配置文件, jar包

- 创建数据库环境

- ```sql
  CREATE DATABASE day14; 
   USE day14; 
   
   CREATE TABLE USER( 
       id INT PRIMARY KEY AUTO_INCREMENT,
       username VARCHAR(32) UNIQUE NOT NULL, 
       PASSWORD VARCHAR(32) NOT NULL 
   ); 
   加入了一个数据
  ```

- 创建domian javabean

- 创建dao层 提供login方法 查询数据库

- 写测试类测试dao方法

- 写servlet代码  FailServlet  LoginServlet  SuccessServlet 等等 配置转跳关系

- 启动测试

#### 使用BeanUtils

```java
<!--    引入工具类的包, 简化获取参数,封装成对象的操作-->
    <dependency>
      <groupId>commons-beanutils</groupId>
      <artifactId>commons-beanutils</artifactId>
      <version>1.8.0</version>
    </dependency>
    
    /*      获取请求参数,封装对象
         req.setCharacterEncoding("utf-8");
        final String username = req.getParameter("username");
        final String password = req.getParameter("password");

        final User loginUser = new User();
        loginUser.setPassword(password);
        loginUser.setUsername(username);
 */
 ----------------------------------两种是一样的, 下面的更方便,尤其是参数多的时候
       //获取所有请求参数
        final Map<String, String[]> map = req.getParameterMap();
        final User loginUser = new User();
        try {
            //将map中的参数封装进loginUser对象;
            BeanUtils.populate(loginUser,map);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
```



- 用于封装javabean的工具类, 

  - javabean : 标准的java类, 要求如下

    - 类必须是public修饰
    - 必须提供空参构造器
    - 成员变量必须是private
    - 提供公共的getter setter方法
    - 功能: 封装数据

  - 方法

    - setProperty()
    - getProperty()
    - populate(javabean,获取的参数map)  封装数据的方法

  - ```java
    @Test
    public void test1(){
        final User user = new User();
        try {
            //给javabean属性设置值 此处的username , 对应的是getset方法去掉getset,方法名, 并不对应成员变量
            BeanUtils.setProperty(user,"username","zhangsan");
            System.out.println(user);
            //从javabean种获取值, 此处的username , 对应的是getset方法去掉getset,方法名, 并不对应成员变量
            final String username = BeanUtils.getProperty(user, "username");
            System.out.println(username);
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
    
    }
    ```



#### 数据库时区问题

```
driverClassName =com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost/day14?characterEncoding=UTF-8&userSSL=false&serverTimezone=UTC
username=root
password=123456
initialSize=5
maxActive=10
maxWait=3000
```

#### 项目默认访问页

```html
<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <welcome-file-list>
    <welcome-file>login.html</welcome-file>
<!-- web.xml中   配置项目启动默认访问页-->
  </welcome-file-list>
</web-app>
```



# http响应消息

响应消息: 服务器发送给客户端的数据

- 数据格式
  - 响应行:协议/版本 响应状态码 状态码描述
    - 状态码:服务器告诉客户端浏览器本次请求和响应的一个状态,都是三位数字, 带大致分为5类
      - 1xx:服务器接收客户端消息但是没有接收完成, 等待一段时间后发送1xx询问客户端
      - 2xx:成功;
      - 3xx: 重定向. 302就是重定向  304代表访问缓存
      - 4xx:失败 客户端错误  比如 资源路径错误 404  没有doPost请求的方法, 则会报错405
      - 5xx:失败 服务器端错误
  - 响应头
    - 头的名称: 头的值
    - 常见的响应头
      - Content-Type 服务器告诉客户端响应体的数据格式;字符编码
      - Content-disposition 服务器告诉客户端以什么格式打开响应体数据
        - 值: in-line 默认值,当前页面内打开,    attachment, 以附件形式打开响应体;
  - 响应空行
  - 响应体: 传输的真实数据



- ```html
  示例数据格式
  
  响应行
  HTTP/1.1 304 
  响应头
  ETag: W/"153-1597165575711"
  Date: Tue, 11 Aug 2020 17:06:45 GMT
  Keep-Alive: timeout=20
  Connection: keep-alive
  响应空行
  
  响应体
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
  <h1>HelloWorld</h1>
  
  </body>
  </html>
  ```



# Response对象

## 功能: 设置响应消息

- 设置响应行 HTTP/1.1 304
  - 设置状态码 setStatus(int sc)
- 设置响应头
  - setHeader(String name,string value)
- 设置响应体
  - 获取输出流
    - 字符输出流 printWriter    getWriter()
    - 字节输出流  ServletOutputStream getOutputStream ()
  - 使用输出流,将数据输出到客户端浏览器





## 重定向案例

- 转发的特点
  - 地址栏路径不变
  - 只能访问当前服务器下的资源
  - 转发是一次请求
- 重定向的特点
  - 地址栏路径发生变化
  - 可以访问其他站点,其他服务器的资源
  - 重定向是两次请求
  - 定向过来,当然是读不到msg的, 因为不是同一个域
- 面试题中一般问  forward 转发 和redirect 重定向的区别





```java
/*
重定向  访问/demo2 会重定向去demo1
 */
@WebServlet("/demo2")
public class Demo2Resp extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        /*
        设置状态码为302   设置响应头location 即为重定向的资源路径
         */
        System.out.println("demo2被访问了");
        /*
        resp.setStatus(302);
        resp.setHeader("location","/demo1");*/

        //简单的一种写法 和上面的等价
        //resp.sendRedirect("/demo1");
         resp.sendRedirect("http://www.bilibili.tv");
    }
```

## 路径的相对和绝对

如果用jsp推荐用绝对路径

- 相对路径 : 不能确定唯一资源,有相对位置关系

  - ./index.html  不以斜杠开头

  - 规则  找到当前资源和目标资源之间的相对位置关系

  - ./代表当前路径 ../代表后退一级

  - ```html
    <body>
    <h1>找到当前资源和目标资源之间的相对位置关系</h1>
    <p>当前资源路径http://localhost:8080/test.html</p>
    <hr>
    <p>目标资源路径http://localhost:8080/demo1</p>
    <p>./demo1等价demo1  所以./可以不写</p>
    <a href="./demo1">测试相对路径 </a>
    
    </body>
    ```

- 绝对路径:通过绝对路径可以确定唯一资源  **以斜杠开头** 省略域名端口号也算

  - 给客户端浏览器使用: 需要加虚拟目录(项目的访问路径)

  - 一般建议使用动态获取虚拟目录拼串,这样之后修改了项目目录也可以继续使用

  - ```final String contextPath = req.getContextPath();```

    - ```
      <h1>绝对路径</h1>
      <a href="/demo2">给客户端使用,需要加项目的虚拟目录,但是我的是/所以不加</a>
      <p>绝对路径就是在端口号后面拼串</p>
      ```

  - 给服务器使用: 转发不需要加虚拟路径  但是重定向需要加虚拟目录

    - ```java
      
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         req.getRequestDispatcher("/demo2").forward(req,resp);
         //转发的绝对路径不需要写虚拟路径, 这个是给服务端用的
      }
      
      重定向需要加虚拟目录,因为我的虚拟目录是/ 所以不加 示例代码
           @Override
          protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
              /*
              设置状态码为302   设置响应头location 即为重定向的资源路径
              重定向需要加虚拟目录,因为我的虚拟目录是/ 所以不加
               */
              System.out.println("demo2被访问了");
              /*
              resp.setStatus(302);
              resp.setHeader("location","/demo1");*/
      
              //简单的一种写法 和上面的等价
              req.setAttribute("msg","helloWorld");
              resp.sendRedirect("/demo1");
      //        resp.sendRedirect("http://www.bilibili.tv");
          }
      ```

### 动态获取虚拟目录

```java
//动态获取虚拟目录(项目路径)
final String contextPath = req.getContextPath();
resp.sendRedirect(contextPath +"/demo1");
```



## 服务器输出字符到浏览器

- 获取输出流对象
- 写输出数据
- 乱码问题见如下代码示例

```java

只要设置了header 另外的与字符集相关的可以不写  获取流之前!!
    推荐使用简单的写法 resp.setContentType("text/html;charset=utf-8");
-----
    @WebServlet("/demo4")
public class Demo4Stream extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取字符输出流对象之前, 设置字符集
        resp.setCharacterEncoding("utf-8");
        
        //告诉浏览器,服务器发送的消息数据的编码集   只要设置了header 上面的可以不写
        resp.setHeader("content-type","text/html;charset=utf-8");
        //下面这个是简单的一个方法, 推荐使用
        resp.setContentType("text/html;charset=utf-8");
        
        //获取字符输出流
        //req resp 这两个对象是tomcat实现的, 编码用的是ISO-8859-1 所以会中文乱码
        final PrintWriter printWriter = resp.getWriter();
        //输出数据
        printWriter.write("<h1>hello</h1>");
        printWriter.write("<h1>你好</h1>");
        printWriter.write("你好");

    }
```



## 	输出字节流到浏览器

字节流可以写字节,也可以写字符, 什么都可以写

一般用这个东西来输出图片数据

```java
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=utf-8");
        //获取字节输出流
        final ServletOutputStream respOutputStream = resp.getOutputStream();
        //输出数据  默认使用GBK 设置成utf8,还要告诉浏览器 ,所以要加上上面的setcontentType
        respOutputStream.write("hello你好啊".getBytes("utf-8"));
    }
```



## 验证码案例

本质是图片   防止恶意表单注册 

```
F:\workspace\base\javaStart02\day03-Response\src\main\java\servlet\Demo6CheckCode.java
```



# ServletContext是最大的对象

在服务启动时,它只创建一次,每个应用只有一个

**它的域的范围很大, 所有用户的数据**,可以跨Servlet

这个对象使用是很谨慎的, 生命周期很长, 服务器启动就创建, 服务器关闭才销毁;

- 概念: 代表整个web应用 ,web工程,   可以和Servlet容器(服务器)来通信

- 获取:

  - 通过request对象获取

  - ```
    Request.getServletContext();
    
    ```

  - 通过HttpServlet获取

  - ```
    this.getServletContext() ;  这个方法继承自GenericServlet
    /*
            servletContext对象的获取 同一个域当然获取的是同一个对象, 是true
             */
            final ServletContext context1 = req.getServletContext();
            final ServletContext context2 = this.getServletContext();
            System.out.println(context1==context2);
            System.out.println(context1);
            System.out.println(context2);
    ```



- 功能:

  - 获取MIME类型: 互联网通信过程中定义的一种文件数据类型标准, http协议遵循这个标准

    - 格式   大类型/小类型  比如 text/html  image/jpeg

    - 获取

      - ```java
        @Override
            protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                   // 演示获取mime类型对象
                final ServletContext context  = this.getServletContext();
                 //定义文件名称
                String filename = "a.jpg";
                //获取mime类型
                final String mimeType = context.getMimeType(filename);
                System.out.println(mimeType);
                //image/jpeg
            }
        ```

  - 它是一个域对象: 所以可以共享数据

    - setAttribute getAttribute removeAttribute

    - ```java
      //在demo3设置属性, 这段要调用就要先访问demo3 , 然后去访问demo4获取
              final ServletContext context  = this.getServletContext();
              context.setAttribute("msg","ha哈");
        //在demo3中设置一个属性, 在demo4中演示读取
              final ServletContext context  = this.getServletContext();
              System.out.println(context.getAttribute("msg"));
      ```

  

  - 获取文件的真实路径,即部署到服务器之后的路径

  - src下的资源路径也可以通过ClassLoader获取

    - getRealPath(String path)

  - ```java
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
               // 获取文件路径
            final ServletContext context  = this.getServletContext();
            String path = context.getRealPath("/b.txt");// web目录下
            String path2 = context.getRealPath("/WEB-INF/c.txt");// WEB-INF目录下
            String path3 = context.getRealPath("/WEB-INF/classes/a.txt");// src目录下 src下的也可以通过ClassLoader获取
            System.out.println(path);
            System.out.println(path2);
            System.out.println(path3);
            final File file = new File(path);
    //        F:\workspace\base\javaStart02\day03-Response\target\day03-Response\b.txt
    //        F:\workspace\base\javaStart02\day03-Response\target\day03-Response\WEB-INF\c.txt
    //        F:\workspace\base\javaStart02\day03-Response\target\day03-Response\WEB-INF\classes\a.txt
    ```



# 文件下载案例

- 网页显示超链接
- 点击超链接弹出下载提示框
- 完成图片文件下载



- 超链接指向的资源如果能被浏览器解析, 会直接展示, 如果不能则会弹出下载提示

- 使用响应头设置资源的打开方式

  - ```java
    设置了这个就不会直接展示了
    content-disposition:attachment:filename=xx
    ```

  - 步骤

    - 定义页面 编辑href  指向Servlet 传递资源的名称filename
    - 定义Servlet
      - 获取下载的文件名称, 加载进内存, 使用字节输入流
      - 将数据写出到Response输出流 指定header content-disposition:attachment:filename=xx

```java
package download;

import utils.DownloadUtils;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;

@WebServlet("/downloadServlet")
public class DownLoadServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求名称
        String filename = req.getParameter("filename");
        ServletContext servletContext = this.getServletContext();
        //使用字节输入流加载文件进内存
        //找到文件的服务器路径 用字节流关联
        final String path = servletContext.getRealPath("/img/" + filename);
        final FileInputStream fileInputStream = new FileInputStream(path);

        //设置Response的响应头 ,建议设置两个头
        final String mimeType = servletContext.getMimeType(filename);
        //设置mime类型
        resp.setHeader("content-type",mimeType);
        
        //解决文件中文名乱码
        //获取useragent请求头
        final String agent = req.getHeader("user-agent");
        //使用工具类编码文件名
         filename = DownloadUtils.getFileName(agent, filename);


        //打开方式  下载    设置弹框的文件的名字
        resp.setHeader("content-disposition","attachment;filename="+filename);



        //将输入流数据写出到输出流
        final ServletOutputStream outputStream = resp.getOutputStream();
        //字节数组做缓冲区
        final byte[] buff = new byte[1024 * 8];
        int len = 0 ;
        //从缓冲区读到的赋值给len 不等于-1 说明没有读到文件末尾,就不停的把缓冲区的写出去
        while ((len = fileInputStream.read(buff)) != -1){
            outputStream.write(buff,0,len);
        }
        fileInputStream.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}

```

### 下载文件名乱码

解决思路

- 获取客户端使用的浏览器版本信息
- 根据不同的版本信息,设置filename的编码方式

工具类代码如下

```java
package utils;

import sun.misc.BASE64Encoder;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

//解决下载文件名乱码的工具类
public class DownloadUtils {

    public static String getFileName (String agent, String filename) throws UnsupportedEncodingException {
        if (agent.contains("MSIE")) {
            //ie浏览器
            filename = URLEncoder.encode(filename, "utf-8");
            filename = filename.replace("+", " ");
        }else if (agent.contains("Firefox")) {
            //火狐浏览器
            final BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?"+base64Encoder.encode(filename.getBytes("utf-8"))+"?=";
        }else {
            //其他浏览器
            filename = URLEncoder.encode(filename,"utf-8");
        }
        return filename;
    }

}
```



# 会话技术

会话技术

一次会话中包含多次请求和响应

一次会话指的是浏览器第一次给服务器资源发送请求, 会话建立,直到有一方断开为止 

功能:  共享数据    在一次会话的范围内

- 方式:
  - 客户端会话技术 Cookie
  - 服务器端会话技术 Session



# Cookie

概念 客户端会话技术, 将数据保存到客户端

- 使用步骤

  - ```java
    创建cookie对象 绑定数据
        new Cookie(String name, String value);
    发送Cookie对象
        resp.addCookie(Cookie cookie);
    获取Cookie,拿到数据
        Cookie[] req.getCookies();
     @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            //获取cookie
            final Cookie[] cookies = req.getCookies();
            for (Cookie cookie : cookies) {
                System.out.println(cookie.getName());
                System.out.println(cookie.getValue());
                System.out.println("--------------");
            }
        }
    
    ```

- 实现原理

  - 基于响应头 set-cookie和请求头cookie实现

- 细节处理

  - 一次可以发送多个cookie

  - ```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        final Cookie c1 = new Cookie("msg", "hello");
        final Cookie c2 = new Cookie("name", "zhangsan");
        //创建cookie 添加数据 发送cookie
        resp.addCookie(c1);
        resp.addCookie(c2);
    }
    ```

  - cookie在浏览器保存的时间

    - 默认情况下, 浏览器关闭后, cookie数据被销毁
    - 设置cookie的生命周期
      - setMaxAge(int seconds) 
        - 正数  持久化到硬盘中, 代表存活时间 单位是秒
        - 负数 默认值 浏览器一关就没了
        - 0  删除cookie信息

  - cookie在tomcat8之后可以存中文  特殊字符还是不支持

    - 在之前需要转码---一般采用url编码%xx

  - cookie的**获取范围**

    - 在一个tomcat中部署多个项目 /day03 和 /day04, 默认情况下不能共享
      - cookie中有个方法 setPath(String path) :设置获取范围, 默认情况下是当前虚拟目录
      - 比如改成/ 就所有项目都可以共享了
    - 多个tomcat之间共享cookie
      - setDomain(String path)   设置cookie的一级域名相同, 则多个服务器可以共享
      - setDomain(".baidu.com");  则tieba.baidu.com和news.baidu.com可以共享

- cookie的特点

  - 存在客户端浏览器, 不太安全
  - 浏览器对当个cookie大小有限制一般是4kb, 同一域名总cookie数量限制一般是20个
  - 作用
    - 用于存储少量不太敏感的数据
    - 在不登录的情况下完成服务器对客户端的身份识别
      - 比如百度首页的一些皮肤设置, 是以cookie的形式存在浏览器,下次打开本地还是有效的,虽然没有登录



# cookie案例

记住上一次访问时间

思路:

​	判断是否有一个名字叫lasttime的cookie信息, 如果有就不是第一次登录



```java
package cookie;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLDecoder;
import java.net.URLEncoder;
import java.text.SimpleDateFormat;
import java.util.Date;

@WebServlet("/cookieTest")
public class CookieTest extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置编码格式
        resp.setContentType("text/html;charset=utf-8");

        final Cookie[] cookies = req.getCookies();
        boolean flag = false;
        if (cookies != null && cookies.length>0){
            for (Cookie cookie : cookies) {
                final String name = cookie.getName();
                if ("lastTime".equals(name)){
                    //证明有, 则不是第一次访问
                    flag = true;
                    //获取当前时间, 重新设置cookie的值, 返回cookie
                    Date date = new Date();
                    final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
                    String str_date = dateFormat.format(date);
                    str_date = URLEncoder.encode(str_date, "utf-8"); //编码, 解决上面空格的问题
                    cookie.setValue(str_date);
                    //设置存活时间
                    cookie.setMaxAge(60*60*24*30);//一个月 单位是秒
                    resp.addCookie(cookie);


                    String value = cookie.getValue();
                    value = URLDecoder.decode(value, "utf-8"); //解码 然后浏览器才能正常显示
                    resp.getWriter().write("<h1>欢迎回来,您上一次访问的时间是:"+value+"</h1>");
                    break;
                }
            }
        }
        if (cookies == null||cookies.length ==0 || flag==false ){
            //没有
            //获取当前时间, 重新设置cookie的值, 返回cookie
            Date date = new Date();
            final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
            String str_date = dateFormat.format(date);
            str_date = URLEncoder.encode(str_date, "utf-8");  //编码, 解决上面空格的问题
            final Cookie cookie = new Cookie("lastTime",str_date);

            //设置存活时间
            cookie.setMaxAge(60*60*24*30);//一个月 单位是秒
            resp.addCookie(cookie);

            resp.getWriter().write("<h1>欢迎首次登陆!</h1>");


        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}

```



# jsp入门

java server pages: java服务器端页面

能写html标签 也可以写java代码   是用来简化书写的



- 服务器解析请求, 找是否有index.jsp资源
- 如果找到了, 会将其转为.java文件
- 编译.java文件,生成.class字节码文件
- 由字节码文件提供响应   它的本质是一个servlet



jsp脚本

```jsp
<%System.out.println("hello jsp");
    final String contextPath = request.getContextPath();
    out.print(contextPath);
%>
<% response.getWriter().write("response,这个写的内容永远在最前面显示,一般不用, 会打乱页面布局");%>
 定义的代码在service方法中, 是局部的 

<%! int i=3;%>
  定义成员变量和成员方法 在转换后的java类中 这种用的很少,是全局的 
<%= i+ "hello"%>
  定义输出 , 会直接输出到页面上  
```

jsp的内置对象

一共有九个  比如  req resp out

服务器处理请求会先找Response缓冲区, 所以resp相关内容永远在out之前输出



# Session

将数据保存在服务器端的对象中 HttpSession对象

在一次会话的多次请求之间共享数据;

```java
final HttpSession session = req.getSession();
        session.setAttribute("msg","helloSession");

final HttpSession session = req.getSession();
        //获取session数据
        final Object msg = session.getAttribute("msg");
        System.out.println(msg);
```

- 原理
  - session是依赖于cookie的, 第一次获取session的时候会在内存中创建一个新的session对象,有一个唯一的id, 响应的时候会发送一个响应头  set-cookie: Jsessionid=2834902085,  会存在浏览器中, 下次再请求的时候会带着cookie的信息, 所以服务器可以确认它是同一个会话;



- 细节,

  - 客户端关闭后,服务器不关闭, 两次获取的不是同一个session;

    - ```java
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          final HttpSession session = req.getSession();
          System.out.println(session);
          //期望浏览器关闭后, session也能相同 即保留会话
          final Cookie c = new Cookie("JSESSIONID", session.getId());
          c.setMaxAge(60*60);
          resp.addCookie(c);
      
      }
      ```

  - 客户端不关闭, 服务器关闭, 获取的不是同一个session

    - 不是同一个, 但是要确保数据不丢失 
      - session的钝化 服务器正常关闭之前, 将session序列化到硬盘上
      - session的活化, 服务器启动后将session文件转化为内存中的session对象即可
    - tomcat把这个事情已经处理好了, 

  - session什么时候被销毁

    - 服务器关闭

    - session对象调用一个方法invalidate()

    - session默认失效时间是30分钟 可以在tomat的xml中配置 

      - ```xml
        <session-config>
            <session-timeout>30</session-timeout>
        </session-config>
        ```

      -

  - session的特点

    - session用于存储一次会话的多次请求数据, 存在服务器端
    - session可以存储任意类型,任意大小的数据

  - session与cookie的区别

    - session存储在服务器端 cookie存在客户端
    - session没有数据大小限制, cookie有
    - session数据安全, cookie相对不安全



# session案例

验证码需求

```java
	设置Request的编码
    获取参数map集合
    获取验证码
    将用户信息封装到user对象
    判断生成的验证码是否和用户输如的一致
        从session中获取程序生成的验证码
        一致:
			判断用户名和密码是否一致
                查询数据库
                	正确:登录成功 存储数据 重定向跳转success
                	不正确: 给提示信息 跳转登录页面 
        不一致:给用户页面提示信息, 验证码错误 Request
            	转跳登录页面 转发
            
```



# Filter Listener

## Filter 

过滤器:web中的过滤器, 当访问服务器资源的时候,过滤器可以将请求拦截下来,完成一些特殊功能

```xml
		<dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.3</version>
        </dependency>
        需要这个依赖 spring底层也是这个依赖
```



编写过滤器

- 定义一个接口, 实现filter接口  这个接口在

- 复写方法

- 配置拦截路径 xml或者注解配置

  - ```java
    @WebFilter("/*") //访问所有资源之前都会执行该过滤器
    public class Filter01 implements Filter {
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            System.out.println("filter方法执行了...");
            //放行  不放行的话页面没有内容显示
            filterChain.doFilter(servletRequest,servletResponse);
        }
    ```

过滤器的细节

- xml配置

  - ```xml
    <web-app>
      <display-name>Archetype Created Web Application</display-name>
      <filter>
        <filter-name>demo1</filter-name>
        <filter-class>com.lin.Filter01</filter-class>
      </filter>
      <filter-mapping>
        <filter-name>demo1</filter-name>
        <url-pattern>/*</url-pattern>
      </filter-mapping>
    
    </web-app>
    ```

  -

- 过滤器的生命周期方法

  - ```java
    /*
    在服务器启动之后会创建filter对象,然后调用init方法  执行一次 一般用来加载资源
    在每次请求被拦截资源时, 会执行dofilter方法 执行多次
    在服务器关闭之后, filter对象被销毁, 如果正常关闭, 则会调用destroy方法 执行一次 一般用来释放资源
     */
    ```

- 过滤器配置详解

  - 拦截路径的配置

    - ```
      /index.jsp 只访问index.jsp的时候会执行拦截器方法
      /user/* 一级索引下的所有资源 包括servlet也会拦截,不只是静态资源
      *.jsp 所有jsp资源过滤器会被执行
      /* 访问所有资源都会执行
      ```

  - 拦截方式的配置

    - ```java
      资源被访问的方式
      注解配置 
          /*
      @dispatcherTypes
      	request 直接请求
      	forward 转发访问资源
      	include 包含访问资源
      	error 错误跳转资源
      	async 异步访问资源*/
      	
      @WebFilter(value = "/index.html",dispatcherTypes = {DispatcherType.REQUEST,DispatcherType.FORWARD})
      //访问所有资源之前都会执行该过滤器 配置拦截的请求方式,是个数组,可以写多个值 
      public class Filter04 implements Filter {
          
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
              System.out.println("updateServlet执行了..");
              //转发
              req.getRequestDispatcher( "/success.html").forward(req,resp);
          }
          /*注意此处转发路径前要加/,就是自动在根路径下找资源;*/
          
          xml
              
       <display-name>Archetype Created Web Application</display-name>
        <filter>
          <filter-name>demo1</filter-name>
          <filter-class>com.lin.Filter01</filter-class>
        </filter>
        <filter-mapping>
          <filter-name>demo1</filter-name>
          <url-pattern>/*</url-pattern>
          <dispatcher>在此处配置那五个值</dispatcher>
        </filter-mapping>
      ```

      

- 多个过滤器的执行顺序

- ```
  请求→ 过滤器1→ 过滤器2→ 资源3
  如果有两个过滤器,执行顺序是12321
  
  过滤器优先级--先后顺序
  注解配置的过滤器:
  	按照类名的字符串比较规则比较,值小的先执行
  web.xml配置的过滤器:
  	谁配置在上面,谁先执行
  ```

- 脏话过滤器的实现思路

  - 用动态代理模式代理req对象的getParameter方法,在增强的方法中搞个io流从配置文件加载敏感词汇, 然后做string.replaceAll进行替换, 之后再返回这个值作为原方法增强之后的返回值;  并且最终,return method.invoke()
  
  - 核心代码
  
  - ```java
    package com.lin;
    
    
    import com.sun.deploy.net.HttpRequest;
    
    import javax.servlet.*;
    import javax.servlet.annotation.WebFilter;
    import java.io.*;
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;
    
    
    @WebFilter(  "/*" )
    public class FilterWorld implements Filter {
        private List<String> list = new ArrayList<>();
        //
        @Override
        public void init(FilterConfig Config) throws ServletException {
            System.out.println("初始化加载敏感词汇进数组");
            //加载文件,读取文件,将文件数据添加到list 释放资源
            final String realPath = Config.getServletContext().getRealPath("/WEB-INF/classes/敏感词汇.txt");
            try {
                //BufferedReader br = new BufferedReader(new FileReader(realPath));
                //上面的写法读取utf8会乱码, 采用下面的写法就没事了
                // 因为InputStreamReader和BufferedReader都继承自Reader,而BufferedReader的构造器又是Reader.
                final BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(realPath), "UTF-8"));
                String line = null;
                while ((line = br.readLine()) != null){
                    list.add(line);
                }
                list.add("小红");
                br.close();
            } catch ( Exception e) {
                e.printStackTrace();
            }
            System.out.println(list);
    
        }
    
        @Override
        public void doFilter(ServletRequest req, ServletResponse resp, FilterChain filterChain) throws IOException, ServletException {
            //创建代理对象 强转
            ServletRequest proxy_req = (ServletRequest)Proxy.newProxyInstance(req.getClass().getClassLoader(),req.getClass().getInterfaces(),new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    //此处的Object proxy, Method method, Object[] args分别是代理对象的引用,方法 ,参数
                    //增强getParameter方法
                    if ("getParameter".equals(method.getName())){
                        //增强
                         String value = (String) method.invoke(req, args);
                         if (value != null){
                             for(String str : list){
                                 if (value.contains(str)){
                                     value = value.replaceAll(str,"***");
                                 }
                             }
                         }
                         return value;
                    }else {
                        //如下return是else的,如果不是我们要增强的方法, 则直接调用原本的方法
                        return method.invoke(req,args);
                    }
    
                }
            });
            //注意: 最后这里,要把放行的对象替换成我们增强过方法的代理对象;
            filterChain.doFilter(proxy_req,resp);
        }
    
        @Override
        public void destroy() {
            System.out.println("过滤器销毁执行了.."+new Date());
        }
    }
    ```
  
  - ```java
    @WebServlet("/test")
    public class Servlet02 extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            final String name = req.getParameter("name");
            final String msg = req.getParameter("msg");
            System.out.println(name+":"+msg);
        }
    ```



## Listener

事件监听机制

事件 一件事

事件源 事件发生的地方

监听器是一个对象,  

注册监听: 将事件,事件源,监听器绑定在一起, 当事件源上的某个事件发生后, 执行监听器

**ServletContextListener**接口

监听ServletContext对象的创建和销毁

```java
public interface ServletContextListener extends EventListener {
    default void contextInitialized(ServletContextEvent sce) {
        ServletContext对象创建后会调用该方法
            服务器启动自动调用该方法
            一般用来加载资源
    }

    default void contextDestroyed(ServletContextEvent sce) {
        ServletContext对象被销毁之前会调用该方法
            服务器关闭后该方法被调用
    }
}
```



- 定义一个类实现ServletContextListener接口

- 覆写上面的方法

- 配置xml     注解@WebListener

  - ```
    <!--注册监听器-->
        <listener>
            <listener-class>com.listener.ContextLoaderListener</listener-class>
        </listener>
    <!--    指定初始化参数-->
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/classes/applicationContext.xml</param-value>
        </context-param>
    </web-app>
    ```



- ​	

  ```java
  @WebListener
  public class ContextLoaderListener implements ServletContextListener {
      @Override
      public void contextInitialized(ServletContextEvent servletContextEvent) {
          System.out.println("contextInitialized");
          /*
          一般用来加载资源
           */
          final ServletContext servletContext = servletContextEvent.getServletContext();
          final String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
          final String realPath = servletContext.getRealPath(contextConfigLocation);
          try {
              FileInputStream fin = new FileInputStream(realPath);
              System.out.println(fin);
          } catch (FileNotFoundException e) {
              e.printStackTrace();
          }
      }
  
      @Override
      public void contextDestroyed(ServletContextEvent servletContextEvent) {
          System.out.println("contextDestroyed");
      }
  }
  ```

