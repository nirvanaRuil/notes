# ECMA

JavaScript = ECMAScript + JavaScript特有的(BOM ,DOM)

ECMAScript 是由欧洲计算机制造商协会,指定出客户端程序脚本语言的标准

js是阻塞式的,如果写到body上面, 是先加载js,动作, 后加载页面标签; 所以一般会放到下面

- 数据类型
  - 原始数据类型: 基本数据类型 
    - number 特殊的有 NaN 是一个不是数字的数字类型 not a number
    - string
    - boolean
    - null 一个对象为空的占位符
    - undefined 未定义, 如果一个变量没有初始化 则会被默认复制为undefined
  - 引用数据类型:对象

```js
    let str = "abc"
    let n = null
    //拿到数据类型
    let i =typeof (n)
    let j =typeof str
```

- 特殊语法
  - 如果一行只有一个语句,可以不加分号,  建议用分号
  - 声明变量带var 是局部变量, 不带var是全局变量 建议带上

- ECMAscript 的基本对象

```js
  
Function 函数对象
	方式1
	function f1(a,b) {
            alert(a+b)
        }
        f1(3,4)
	方式2
    let f2 = function (a,b) {
            alert(a+b)
        }
        f2(8,8)
	-----方法的属性
            alert(f1.length) 形参的个数
     特点
     	形参数据类型不用谢, 定义同名方法会覆盖
		方法调用只与方法名有关,与传入的参数无关, 传不传,多传少传都会执行
		多传的是被一个内置对象(一个数组) 叫arguments,封装了实际的参数
---- 计算任意个数字的和, arguments数组默认保存所有参数, 可以不定义形参
        function add() {
            let sum = 0 ;
            for (let i = 0; i < arguments.length; i++) {
                sum += arguments[i];
            }
            return sum
        }
        let sum = add(1,2,3,6,5);
        alert(sum)
---- 还有如下几个
Array
Boolean 包装类
Date
Math
Number 包装类
String 包装类
RegExp
Global

```



```js
Array数组对象
var arr = new Array(元素列表);
var arr = new Array(默认长度);
var arr = [元素列表];
    let arr1 = new Array(1,2,3);
    let arr2 = new Array(5);
    let arr3 = [1,2,3];
特点: 数组中元素的类型是可变的 
	 let arr4 = [1,"acv",true];
	 数组的长度是可变的 越界了是undefined
     let arr4 = [1,"acv",true];
    	arr4[10] = "hehe"
属性:length 长度
方法:
	let arr1 = new Array(1,2,3);
    console.log(arr1.join("/")) 以/隔开拼接成字符串
	
	arr1.push(11);  加入元素11
```



```js
Date对象
    let date = new Date();
    console.log(date) //Date Wed Aug 05 2020 03:56:37 GMT+0800 (中国标准时间)
    let d = date.toLocaleDateString()
    console.log(d) // 2020/8/5
    let time = date.getTime(); //返回当前对象描述的时间到1970/1/1/0 的毫秒值的差值
    console.log(time)
```



```js
Math对象  不用创建  直接使用
    console.log(Math.PI) //圆周率
    console.log(Math.random()) //[0,1)区间的随机数 小数
    console.log(Math.round(3.1546546)) //四舍五入
    console.log(Math.ceil(3.154)) //向上取整
    console.log(Math.floor(3.556)) //向下取整
    let num = Math.floor(Math.random()*100+1)//1-100
    console.log(num)
```



**RegExp 正则表达式对象**

- 规则

  - 单个字符:[]

    - [a] [ab] [a-z] [a-zA-Z] 分别是a, a或者b a到z

      - \d 单个数字字符包含[0-9]

      - \w 单个单词字符 [a-zA-Z0-9_]

        ```js
         * 出现0次或多次
         ? 出现0次或1次
         + 出现1次或多次
        {m,n} 个数在[m,n]取值范围 
        {,n} 最多n个
        {m,} 最少m个
        开始^
        结束$
        
            let reg = new RegExp("\w{6,12}") ;//反斜线处理w前面的\转义
            let reg2 = /^\w{6,12}$/ ;
        
            let name = "zhangsan";
            let flag = reg2.test(name);
            console.log(flag) //true 
        ```



```js
Global对象
特点 全局对象,这个Global中封装的方法不需要对象可以直接调用; 方法名();

```

# BOM

browser object model 浏览器对象模型

```js
BOM对象组成:
	window 窗口对象
    location 地址栏对象
    history 历史记录对象
    navigator 浏览器对象 //基本用不到
    screen 显示器屏幕对象 //基本用不到
    DOM对象
```

## window 窗口对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<input type="button" value="打开bilibili" id="btn">
<input type="button" value="关闭打开的窗口" id="btn2">
<input type="button" value="关闭打印" id="btn3">

<script>
    /*
    window
    1.不需要创建可以直接使用, window.方法名();   window可以省略;
    2.方法
        1.与弹出窗口有关
            alert
            confirm 确认取消按钮对话框
            prompt 用户输入框
        2.与打开关闭有关的
            open 打开一个新窗口
            close 关闭浏览器窗口
        3.定时器相关的方法
            setTimeout 指定的时间之后调用方法  执行一次
            clearTimeout 取消setTimeout设定的定时器
            setInterval  按照周期来调用方法
            clearInterval 取消上面这个周期定时器
    3.属性: 获取其他BOM对象
                history
                location
                navigator
                screen
            获取dom对象
                 document

     */
    let h1 = window.history; //window可以省略
    alert(h1) //弹出history对象

    // setTimeout("alert('bobo')",2000)

    let interval = setInterval(log1,1000);
    function clear() {
        clearInterval(interval)

    }
    function log1 () {
        console.log(new Date())
    }
    let btn3 = document.getElementById("btn3");
    btn3.onclick = clear;

    //2.与打开关闭有关的
    let btn = document.getElementById("btn");
    let bilibili  ;
    btn.onclick = function () {
         bilibili = open("http://www.bilibili.tv"); //返回值是一个窗口对象
    };
    let btn2 = document.getElementById("btn2");
    btn2.onclick = function () {
        // close(); // 这个是window.close 所以这样会关闭自己
        bilibili.close();
    };


    /*1.与弹出窗口有关
        prompt("请输入你的银行卡号和密码"); //返回用户输入的值
        window.alert("hello")
        let flag = confirm("你爱我吗?"); //确定 返回true  否则返回false
        if (flag) {
            console.log("知道了")
        }else{
            console.log("狗东西")
        }

     */
</script>
</body>
</html>
```



## location 地址栏对象

```html
<body>
<input type="button" value="刷新" id="btn">
<input type="button" value="去b站" id="btn1">


<script>
//刷新页面
    let btn = document.getElementById("btn");
    btn.onclick = function () {
        location.reload()
    }
//设置url  可以实现本页转跳 不打开新页面
    let href = location.href;
    let btn1 = document.getElementById("btn1");
    btn1.onclick = function () {
        location.href = "http://www.bilibili.tv"
    }
// let href = location.href;
// alert(href) 获取url
</script>
<script>
    /*
    获取
        window.location  window可以省略
    方法:
        reload() 重新加载当前文档  刷新
    属性:
        设置或返回完整的url
     */
</script>

</body>
```



## history 历史记录对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>history</title>
    <script>
        /*
        window.history 指的是当前window窗口访问过的历史记录, 并不是浏览器历史记录
        方法:
            back 前一页 后退
            forward 后一页 前进
            go 加载某个具体历史访问页 参数如果是正数  前进几个历史记录, 是负数就后退几个历史记录
        属性:
            length 返回当前窗口历史列表中url的数量
         */
    </script>
</head>
<body>
<input type="button" value="获取历史记录条数" id="btn">
<a href="04-轮播图.html" >轮播页面</a>
<input type="button" value="前进" id="forward">
<script>
    let btn = document.getElementById("btn");
    btn.onclick = function () {
        let length = history.length;
        console.log(length)

    }

    let forward = document.getElementById("forward");
    forward.onclick = function () {
       history.forward()
       // history.go(1) 与forward等价

    }


    // let length = history.length;
    // alert(length)
</script>
</body>
</html>
```





# DOM

概念:

​	document object model 文档对象模型

根元素是html标签 里面的都是子元素

w3c dom标准被分为3部分

- 核心dom -针对任何结构化文档的标准模型
  - **document 文档对象**
  - **element 元素对象**
  - attribute 属性对象
  - text 文本对象
  - comment 注释对象
  - **node 节点对象 是上面五个的父对象, 相当于上面五个继承与node**
- xml dom 针对xml 文件的标准模型
- html dom 针对html 文件的标准模型

## 核心dom

- document对象的方法

  - 获取element对象 get
  - 创建其他dom对象 create

- element对象

  - 通过document对象来创建和获取

  - 方法 

    - removeAttribute

    - setAttribute

      - ```js
         let a = document.getElementsByTagName("a")[0];
            a.setAttribute("href","https://www.baidu.com")
        ```

- node 节点对象

  - 所有dom对象都可以被认为是一个节点对象
  - 方法
    - crud dom树 appendchild 添加节点 removechild 删除 replace 替换 
  - 属性
    - parentNode 返回当前节点的父节点

```html
node示例代码
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>node</title>
    <style>
        div{
            border: 1px solid green;
            width: 200px;
        }
        #div2 {
            color: red;
            width: 100px;
            margin: 3px;
        }
        #p1 {
            color: cadetblue;
        }
    </style>
</head>
<body>

<div id="div1">
    <div id="div2">
        div1
    </div>
    div
</div>
<input type="button" value="删除">
<!--下面这个写法是组织跳转的固定写法 -->
<a href="javascript:void (0)" id="del">删除子节点</a>
<a href="#" id="create">创建子节点</a>


<script>
    let del = document.getElementById("del");
    let create = document.getElementById("create");
    let btn = document.getElementsByTagName("input")[0];
    btn.onclick = function () {
        let div1 = document.getElementById("div1");
        let div2 = document.getElementById("div2");
        div1.removeChild(div2)

    }

    create.onclick = function () {
        let div1 = document.getElementById("div1");
        let p = document.createElement("p");
        p.setAttribute("id","p1") //设置id属性 对应样式 创建出来就会有样式
        p.innerText = "我被创建了"
        div1.appendChild(p)

    }
    del.onclick = function () {
        let div1 = document.getElementById("div1");
        let div2 = document.getElementById("div2");

        div1.removeChild(div2)

    }
    let div2 = document.getElementById("div2");
    let parentNode = div2.parentNode;
    alert(parentNode)
</script>
</body>
</html>
```



## htmldom

- 标签体的设置和获取 innerHTML

- 使用html元素对象的属性  比如img的src属性

- 控制设置样式

  - ```js
    		div1.style.border = " 1px solid skyblue"
            div1.style.color = "green"
            div1.style.fontSize = "30px"
    ```

  - ```js
    提前定义好样式 .d1
    div2.onclick = function () {
            //修改样式方式2
            div2.className = "d1"
        }
    ```

### 事件

事件:是某些操作 点击 指向等等

事件源: 按钮  标签 文本输入框

监听器: js程序代码

注册监听: 将事件, 事件源,监听器 结合在一起, 然后事件源发生了事件就触发某个监听器代码

常见的事件:

- 点击事件
  - onclick 单击   
  - ondblclick 双击
- 焦点事件
  - onblur 失去焦点  一般用于表单校验
  - onfocus 获得焦点 
- 加载事件
  - onload 一个页面或者一幅图像完成加载时触发
  - 一般给body和window加onload  这样可以把js代码写到上面
- 鼠标事件: 
  - onmousedown 鼠标按下
  - up 鼠标松开
  - move 鼠标移动
  - over 指向
  - out 鼠标移出
- 键盘事件
  - onkeydown 某个按键被按下
  - onkeyup 松开
  - onkeypress 按下后松开
- 选中和改变事件
  - onchange 域的内容被改变
  - onselect 被选中
- 表单事件
  - onsubmit 确认按钮被点击
    - 可以阻止表单的提交
  - onreset 重置按钮被点击



# bootstrap

## 响应式开发

- 容器定义

  - container	固定宽度 就可能有留白
  - container-fluid   百分百宽度

- 定义行 样式:row 行

- 定义元素: 指定元素在不同设备上,所占栅格数量 样式 col 列

  - col - 设备带号 - 格子数

  - ```
    col-xs-	超小屏幕 手机 (<768px)
    col-sm- 小屏幕 平板 (≥768px)
    col-md- 中等屏幕 桌面显示器 (≥992px)
    col-lg- 大屏幕 大桌面显示器 (≥1200px)
    详见如下链接
    https://v3.bootcss.com/css/#grid
    ```



- 注意

  - 一行格子超过12会自动换行
  - 格子适用于大于分界点的设备, 自动拉伸
  - 小于分界点, 会一个元素占满一整行

- 图片

  - ```
    class="img-responsive" 任意页面都占满百分百自动缩放
    <img src="..." alt="..." class="img-rounded"> 方的
    <img src="..." alt="..." class="img-circle"> 圆的
    <img src="..." alt="..." class="img-thumbnail"> 相框
    ```



- 表格表单
- 导航条在组件分类



# xml

- 概念 extensible Markup language 可扩展标记语言  标签都是自定义的

- 功能

  - 存储数据 1, 配置文件, 2, 在网络中传输

- 与html的区别

  - 标签是自定义的
  - 语法严格
  - xml是存储数据的, html是展示数据的

- 语法

  - 后缀名是.xml
  - 第一行必须是文件声明
  - 文档中有且仅有一个根标签
  - 属性值必须用引号 单双均可
  - 标签必须正确关闭
  - 标签名称区分大小写

- 组成

  - ```
    <?xml version="1.0" encoding="utf-8" standalone='yes' ?>
    文档声明 最后的yes 不依赖其他文件 no表示会依赖其他文件
    <users>
        <user id="1">
            <name>zhangsan</name>
            <age>12</age>
            <gender>male</gender>
        </user>
        <user id="2">
            <name>lisi</name>
            <age>11</age>
            <gender>female</gender>
        </user>
    </users>
    ```

  - id属性值唯一

  - ```xml
    <code>
                <![CDATA[a>b]]> 这个里面的内容无需转义, 会正常显示 
    </code>
    ```

## 约束

谁编写xml  : 用户 软件使用者

谁解析xml: 软件

说明文档:规定xml文档的书写规则, 即可以写那些标签和属性   这个就是约束文档

- 分类

  - DTD 一种简单的约束技术 后缀名是.dtd
  - Schema 一种复杂的约束技术 后缀名是.xsd

- 解析

  - DOM 将标记语言文档一次性加载进内存, 在内存形成一个dom树
  - 优点 操作方便 可以对文档进行crud的操作
  - 缺点 占内存 不适应内存小的设备
  - SAX  逐行读取 释放 , 基于事件驱动, 
  - 优点  不占内存
  - 缺点 只能读取 不能增删改

  一般服务端用dom思想, 移动端用sax思想

- 常见的解析器

  - JAXP sun公司提供的解析器, 支持dom和sax两种思想  性能不好
  - DOM4J 一款优秀的解析器
  - JSOUP 原本是解析html  基于DOM思想 
    - 使用 导入jar包 
    - 获取document对象
    - 获取对应的标签
    - 获取标签数据
  - PULL  安卓系统内置解析器  sax思想

