# DOM(文档对象模型)

js通过DOM来对HTML进行操作 . 对象表示将网页中每个部分都转换成了对象 . 使用模型来获取对象之间的关系 , 方便获取对象 .  模型就是体现节点与节点之间的关系 .

**DOM树**上的每一个节点都有唯一的id . 

- **文档节点** : 整个HTML文档   #document
- **元素节点** : HTML文档中的HTML标签   
- **属性节点** : 元素的属性  
- **文本节点** : HTML标签中的文本内容

事件: 文档或浏览器窗口中发生的一些特定的交互瞬间 . 

`window.onload = function(){}` . 页面加载完成后执行

对于自结束标签 , `innerHTML`没有意义 . 

## DOM查询

`document.getElementById("")`	通过id获取指定元素

`document.getElementsByClassName("")`   根据class属性值获取一组元素节点对象 . 不支持IE8及以下浏览器,但是可以用querySelector .    获取class为box1中的所有的div :   `document.querySelector(".box div")`  需要一个选择器的字符串作为参数 , 可以**根据一个css选择器来查询一个元素节点对象** .  如果满足条件的元素有多个 , 只会返回唯一的一个元素 .   `document.querySelectorAll(".box")`  将符合条件元素封装并返回数组 .

`city.children`  children属性可以获取当前元素的所有子元素 . 

`city.firstElementChild` firstElementChild属性获取当前元素的第一个子元素 . 不支持IE8及以下的浏览器 , 如果需要兼容 , 还是尽量不要使用吧 ....

`document.body`  保存的是body的引用

`document.documentElement`  保存的是HTML根标签

`document.all`  代表页面中所有元素



### 获取元素节点的子节点

`document.getElementsByTagName("")`  通过标签名获取一组元素,**返回类数组对象(指定标签名后代节点)** . 

`city.childNodes`  childNodes属性会获取包括文本节点在内的**所有节点** , DOM标签中的空白也会被当成文本节点 . 在IE8以下的浏览器中不会将空白文本当成子节点 .

`city.firstChild` firstChild属性获取到当前元素的**第一个子节点**(包括空白节点) .  

`city.lastChild`  lastChild属性获取当前元素的**最后一个节点** . 

### 获取元素节点的兄弟节点

`document.getElementsByName("")`  getElementsByName()方法通过name获取一组元素 . 返回当前节点的指定标签名**后代节点** . 

`parentNode`  parentNode属性表示当前节点的父节点 . 

`previousSibling`  previousSibling属性表示当前节点的前一个兄弟节点 . 也可能获取到空白文本 . 

`previousElementSibling`  previousElementSibling属性获取前一个兄弟元素 , IE8不支持 .

`nextSibling`  nextSibling属性表示当前节点的后一个兄弟节点 . 

读取元素节点属性时,class属性不能采取一般 `元素.属性` 的方式 ,要写成 `元素.className` 才行 . 

## DOM增删改





















