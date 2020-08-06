# JSON

**JSON: JavaScript Object Notation（JavaScript 对象标记法）**.

**JSON是一种存储和交换数据的语法**.

**JSON是通过JavaScript对象标记法书写的文本,文本可被任何编程语言作为数据来读取和使用**.

**JSON是轻量的数据交换格式**.

**JSON独立于语言**.

**JSON具有自我描述性且易于理解**.



## 简介

### 交换数据

当数据在浏览器与服务器之间进行交换时，这些数据**只能是文本**。

JSON 属于文本，并且我们能够把任何 JavaScript 对象转换为 JSON，然后将 JSON 发送到服务器。

我们也能把从服务器接收到的任何 JSON 转换为 JavaScript 对象。

**以这样的方式，我们能够把数据作为 JavaScript 对象来处理，无需复杂的解析和转译**。

### 发送数据

如果数据存储在JavaScript对象中,则可以把该对象转换为JSON,然后将其发送到服务器.

`JSON.stringify()`

```json
var myObj = { name:"Bill Gates",  age:62, city:"Seattle" };
var myJSON =  JSON.stringify(myObj);
window.location = "demo_json.php?x=" + myJSON;
```

### 接收数据

如果以JSON格式接收到数据,能够将它转换为JavaScript对象.

`JSON.parse()`

```json
var myJSON = '{ "name":"Bill Gates",  "age":62, "city":"Seattle" }';
var myObj =  JSON.parse(myJSON);
document.getElementById("demo").innerHTML = myObj.name;
```

### 存储数据

在**==存储数据时，数据必须是某种具体的格式==**，并且无论您选择在何处存储它，文本永远是合法格式之一。

JSON 让 JavaScript 对象存储为文本成为可能。

## JSON语法

JSON语法是JavaScript语法的子集.

### JSON语法规则

JSON 语法衍生于 JavaScript 对象标记法语法：

- ==数据在名称/值对中==.
- ==数据由逗号分隔==.
- ==花括号`{}`容纳对象==.
- ==方括号`[]`容纳数组==.

### JSON数据,名称和值

**JSON 数据写为名称/值对**.

**==字符串值必须由双引号编写==**.

**==JSON对象属性的值可以是一个对象,也可以是一个数组==**.

名称/值由字段名称构成，后跟冒号和值

```json
{"name":"Bill Gates"}
```

JSON 名称需要双引号,而 JavaScript 名称不需要.

JSON – 求值为 JavaScript 对象.

JSON 格式几乎等同于 JavaScript 对象.

在 JSON 中，**==键必须是字符串，由双引号包围==**.

但在 JavaScript 中，键可以是字符串、数字或标识符名称.

访问js对象值:通过.或者[]访问-  `person.name;`  &  `person["name"];`.



#### 有效的数据类型

在 JSON 中，值必须是以下数据类型之一：

- 字符串
- 数字
- 对象（JSON 对象）
- 数组
- 布尔
- null

在 JavaScript 中，以上所列均可为值，外加其他有效的 JavaScript 表达式，包括：

- 函数

- 日期

- undefined

  但是在JSON 中这三个不可以是值.

**JSON中的字符串必须用双引号包围**.

**JSON中的数字必须是整数或浮点数**.

**JSON中的值可以是对象**.

**JSON中的值可以是数组**.

**JSON中的值可以是true/false**.

**JSON中的值可以使null**.

```json
{ "name":"John" }

{ "age":30 }

{
"employee":{ "name":"Bill Gates", "age":62, "city":"Seattle" }
}

{
"employees":[ "Bill", "Steve", "David" ]
}

{ "sale":true }

{ "middlename":null }
```

## JSON.parse()

**JSON 的常规用途是同 web 服务器进行数据传输**.

**在从 web 服务器==接收数据时，数据永远是字符串==**.

**通过 JSON.parse() 解析数据，这些数据会成为 JavaScript 对象**.

从 web 服务器接收到数据:

```json
'{ "name":"Bill Gates", "age":62, "city":"Seattle"}'
```

用 JavaScript 函数 JSON.parse() 把文本转换为 JavaScript 对象：

```json
var obj = JSON.parse('{ "name":"Bill Gates", "age":62, "city":"Seattle"}');
```

确保这段文本以 JSON 格式书写，否则会出现语法错误。

在页面中使用 JavaScript 对象：

```json
<p id="demo"></p> 

<script>
 document.getElementById("demo").innerHTML = obj.name + ", " + obj.age; 
</script>
```

### 来自服务器的JSON

用 XMLHttpRequest 从服务器获取数据

```json
var xmlhttp = new XMLHttpRequest();
xmlhttp.onreadystatechange = function()  {
    if (this.readyState == 4 && this.status == 200) {
        myObj =  JSON.parse(this.responseText);
        document.getElementById("demo").innerHTML  = myObj.name;
    }
};
xmlhttp.open("GET", "json_demo.txt", true);
xmlhttp.send();
```

在对衍生自数组的 JSON 使用` JSON.parse() `后，此方法将**返回 JavaScript 数组**，而不是 JavaScript 对象。



### 怎么处理JSON中的日期和函数

JSON中如果包含日期,把它**写成字符串**,之后使用reviver转换回来.

` JSON.parse() `的第二个参数*reviver* 是函数，在返回值之前，它会检查每个属性.

```json
var text =  '{ "name":"Bill Gates", "birth":"1955-10-28", "city":"Seattle"}';
var obj = JSON.parse(text, function (key, value) {
    if  (key == "birth") {
        return new Date(value);  //用第二个参数reviver
    } else {
         return value;
   }});
 
document.getElementById("demo").innerHTML = obj.name + ", " + obj.birth;
```

对于函数也一样,如果要写函数,就要写成字符串,再用`eval()`转回函数.但是这样做的话,函数在json中是丢失作用域的,应该避免使用.

```json
//把字符串转为函数
var text =  '{ "name":"Bill Gates", "age":"function () {return 62;}", "city":"Seattle"}';
var obj = JSON.parse(text);
obj.age = eval("(" + obj.age + ")");
 
document.getElementById("demo").innerHTML = obj.name + ", " +  obj.age();
```



## JSON.stringify()

**JSON 的常规用途是同 web 服务器进行数据交换**.

**在向 web 服务器==发送数据时，数据必须是字符串==**.

**通过 JSON.stringify() 把 JavaScript 对象转换为字符串**.

对js**对象和数组**进行字符串化:

```json
var obj = { name:"Bill Gates", age:62, city:"Seattle"};
var myJSON =  JSON.stringify(obj);
document.getElementById("demo").innerHTML = myJSON;  //对象

var arr = [ "Bill Gates", "Steve Jobs", "Elon Musk" ];
var myJSON =  JSON.stringify(arr);
document.getElementById("demo").innerHTML = myJSON;//数组
```



`JSON.stringify()`会把任何日期都转换为字符串,不需要做任何处理.

在JavaScript对象/数组中有函数时,如果没有提前进行字符串的转化,`JSON.stringify()`**会把所有函数删除**,包括键和值.

**用`toString()`提前把函数转换为字符串**.

```json
var obj =  { "name":"Bill Gates", "age":function () {return 62;}, "city":"Seattle"};
obj.age = obj.age.toString();  //把函数转为字符串
var myJSON = JSON.stringify(obj);
document.getElementById("demo").innerHTML = myJSON;
```

## JSON对象

JSON的对象:

必须被花括号 {} 包围.

要以键/值对书写.

键必须是字符串，值必须是有效的 JSON 数据类型（字符串、数字、对象、数组、布尔或 null）.

键和值由冒号分隔.

每个键/值对由逗号分隔.



JSON的对象值:

可以通过两种方式来访问-`x = myObj.name;`&`x = myObj["name"];`.

### 遍历对象 for-in

遍历对象属性:

```json
myObj =  { "name":"Bill Gates", "age":62, "car":null };
for (x in myObj) {
   document.getElementById("demo").innerHTML  += x;
}
```

在for-in中访问属性值:  **必须要用括号标记法**访问.

```json
 document.getElementById("demo").innerHTML  += myObj[x];
```

### 嵌套的JSON对象

**==一个 JSON 对象中的值可以是另一个 JSON 对象==** . 就是对象套娃...

```json
myObj =  {
   "name":"Bill Gates",
   "age":62,
   "cars": {
	  "car1":"Porsche",
	  "car2":"BMW",
	  "car3":"Volvo"
   }
}

//访问是对象值的对象
x = myObj.cars.car2;
//或者：
x = myObj.cars["car2"];
```

### 删除对象属性&修改对象值

修改值:  直接赋值修改就好

```json
myObj.cars.car3 = "Mercedes Benz";

myObj.cars["car3"] = "Mercedes Benz";
```

删除对象属性: `delete`

```json
delete myObj.cars.car1;
```

## JSON数组

数组可以是对象属性的值.

```json
"cars":[ "Porsche", "BMW", "Volvo" ]
```

可以通过索引来访问数组值  `x = myObj.cars[0];` .也可以通过for-in/for循环来访问.

### 遍历数组for-in/for循环

通过for-in/for循环访问数组值

```json
for (i  = 0; i < myObj.cars.length; i++) {
    x  += myObj.cars[i];
}

for (i in myObj.cars) {
     x  += myObj.cars[i];
}  //都一样的
```

### JSON对象中的嵌套数组

和对象值中嵌套对象一样的,都可以多层嵌套,其实也可以混合多层嵌套呢.

如果要**访问对象值数组/数组中的数组**,必须要**对每个数组使用for-in循环遍历**才可以.循环嵌套.

```json
myObj =  {
   "name":"Bill Gates",
   "age":62,
   "cars": [
	  { "name":"Porsche",  "models":[ "911", "Taycan" ] },
	  { "name":"BMW", "models":[ "M5", "M3", "X5" ] },
	  { "name":"Volvo", "models":[ "XC60", "V60" ] }
   ]
}

//访问对象值数组/数组中的数组,用for-in
for (i in myObj.cars) {
    x += "<h1>" + myObj.cars[i].name  + "</h1>";
    for (j in myObj.cars[i].models) {
         x += myObj.cars[i].models[j];
    }
}
```

### 修改数组/删除数组项目

修改数组就用索引号来确定.

```json
myObj.cars[1] = "Mercedes Benz";
```

删除数组和删除对象一样的,用`delete`.



一个HTML下拉列表的例子

```js
obj = { "table":"customers", "limit":20 };
dbParam = JSON.stringify(obj);  //字符串化
xmlhttp = new XMLHttpRequest();
xmlhttp.onreadystatechange = function() {
    if (this.readyState == 4  && this.status == 200) {
        myObj = JSON.parse(this.responseText);
         txt += "<select>"
        for (x in myObj) {
             txt += "<option>" + myObj[x].name;
        }
        txt += "</select>" 
        document.getElementById("demo").innerHTML  = txt;
    }
}
xmlhttp.open("POST", "json_demo_db_post.php", true);
xmlhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
xmlhttp.send("x=" + dbParam);
```



## JSONP(了解)

​		因为不同源之间传输数据会有一种叫跨域的现象,JSONP是一种不考虑跨域问题即可传送JSON数据的方法.这就是它出现的目的,它不使用XMLHttpRequest对象,用<script src=" ">代替XMLHttpRequest 对象.







