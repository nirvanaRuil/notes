# 零散 最基础知识

js执行顺序由上到下.

**字面量** :都是一些不可改变的值;可以直接使用,但一般不会这么干.

**变量** :可以用来保存字面量,而且变量的值可以任意改变.在开发中都是用变量去保存字面量.用变量对字面量进行定义.

**声明/定义变量** :用var let const声明.只声明一次.

**赋值** :只声明没赋值报错undefined. 

**标识符** :js中可以自主命名的都可以是标识符.例如-变量名,函数名,属性名.

**标识符规则** :1.可以含有字母,数组,_,$;2.不能以数字开头;3.不能是ES中的关键字或保留字;4.一般都采用驼峰命名法.

**数据类型** :指的就是字面量类型,一共有六种----String 字符串 ;  Number 数值  ;  Boolean  布尔值  ;  Null  空值  ;  Undefined 未定义  ;  Object 对象  .  

**typeof** :检查变量类型.  

**String 字符串** :在js中需要使用引号,不使用就是变量;

**Number 数值**:所有数值都是Number类型,包括整数和浮点数(小数) ; 最大值:Number.MAX_VALUE,返回Infinity,表示正无穷,是一个字面量,typeof返回number ; 最小值:Number.MIN_VALUE ,表示最小正值 ; NaN : 特殊数字,表示Not A Number,typeof返回number ; 整数运算可以基本保证精确 ; 如果进行浮点数运算可能得到不精确结果 ; 不要进行精确度比较高的运算 .

**Boolean  布尔值** :有两个值 true&false,表示逻辑上的真和假 ; typeof返回boolean.

**Null  空值&Undefined 未定义** :Null只有一个值就是null,专门表示一个为空的对象 ; typeof返回object . Undefined只有一个值就是undefined , 当声明一个变量但是没有赋值时,值就是undefined; typeof返回undefined.

**强制类型转换** :将一个数据类型强制转换为其他的数据类型 . 主要是指将其他的数据类型转换为String,Number,Boolean . 

**其他数据类型转为String** :方式一 :调用被转换数据类型的toString()方法 , 该方法不会影响到原变量,它会将转换的结果返回 ; null和undefined没有toString()方法 . 方式二 : 调用string()函数 , 将被转换数据类型作为参数传递给函数 ; 此方法中对于Number和Boolean 实际上就是调用toString()方法

**其他数据类型转为Number** :

**其他数据类型转为Boolean** :