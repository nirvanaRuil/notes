# Ajax

Ajax = Asynchronous JavaScript and XML (异步的JavaScript和XML) , 它是一种在无需重新加载整个网页的情况下,==**是能够局部刷新的方法**== , 不是语言. 里面的js 负责创建异步对象,发送请求,更新页面dom对象.



Ajax工作原理:

客户端  <---------->  传输  <------------>  服务器端

xhtml                  协议:XMLHttp                 PHP等语言输出结果

css                       载体:文本,json,xml等      

JavaScript



XML:web.xml   网络中的传输的数据格式   ----XML现在**==已经用json替代==**.

maven:项目的构建工具

mybatis:访问数据库的框架

spring:管理和创建对象的框架

springmvc:做web开发的

动态代理:反射

SSM:mybatis&spring&springmvc集合框架

## 局部刷新

核心对象是**异步对象(XMLHttpRequest)**.代替浏览器的行为发送请求,可以有多个异步对象存在,每个都是独立的发送请求获取数据.

优点:在浏览器内部发起请求,获取数据,改变页面的部分内容,网络中数据传输少,提升用户使用感.

这个异步对象是存在于浏览器内存中的,使用js语法创建和使用.

# 异步实现步骤

四个步骤,要配合服务器端使用,不能单独使用嗷

## 1-创建对象方式

`var xxxxx = new XMLHttpRequest();`

## 2-onreadystatechange事件    给异步对象绑定事件

```javascript
xmlHttp.onreadystatechange = function(){
    //处理请求的状态变化
    if(XMLHttp.readyState == 4 && XMLHttp.status == 200){
        //可以处理服务器端的数据了,更新当前页面
        var data = XMLHttp.responseText;
        document.getElementById("name").value = data;
    }
}
```

为啥处理对象在第二步?**回调**....请求状态变化时会自动调用onreadystatechange事件,以下的每一步异步对象都要自动回头调用.

当请求被发送到服务器时,我们需要执行一些基于响应的任务,每当**readyState**改变时,就会触发onreadystatechange事件,此事件可以指定一个函数,处理状态的变化.

**==readyState==属性**:存有XMLHttpRequest的五个状态变化 0-4.

0----请求未初始化,创建对象

1----初始化异步请求对象  XMLHttp.open(请求方式,请求地址,true)

2----异步对象发送请求    XMLHttp.send( )

3----异步对象接收应答数据,从服务器端获取数据     **XMLHttpRequest的内部处理使用**

**4----异步请求对象已经将接收到的数据解析完毕(常用)**      开发人员可以在这处理数据 : **更新当前页面**.

**status属性** : 表示网络请求状况.  当status等于200(像404那种的,叫啥来着)时,表示网络请求是成功的.

## 3-初始异步请求对象

异步方法open( )

 XMLHttp.open(请求方式get|post , "服务器端的访问地址" , 同步|异步请求[默认true-异步])

## 4-使用异步对象发送请求

XMLHttp.send( )

获取服务器端返回数据,使用异步对象的属性responseText.

# jQuery中的Ajax

本质是XMLHttpRequest

1.`jQuery.get(...)`   所有参数:   

url:待载入页面的url地址  *常用

data:待发送key/value参数  *常用

success:载入成功是回调函数  *常用

dataType:返回内容格式--xml,json,script,HTML,txt(载体)

2.`jQuery.post(...)`   所有参数:同上





将文本框输出的值`$(#qwer).val()`发送给服务器,接收服务器返回的数据

```javascript
function a1(){
    $.ajax({
        //url:待载入页面的url地址  json
        //data:待发送的key/value参数
        //success:载入成功时的回调函数
            //data:封装了服务器返回的数据
            //status:状态
        url:"${pageContext.request.contextPath}/ajax/a1",
        data:{"name":$("#txtName").val()},
        success:function (data,status) {
            console.log(data);
            console.log(status);
        }
    });

    //将文本输出的值
    $("txtName").val();
    //发送给服务器
}

<input type="text" id="txtName" onblur="a1()"/>
```

