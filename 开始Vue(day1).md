# 开始Vue(day1)

## 了解Vue

是一套构建用户界面的框架,主要关注视图层.便于与第三方库或既有项目整合.

前端工作主要负责MVC中的V层.主要工作是界面.

#### 提高开发效率的发展历程

原生js---jQuery之类的库---前端模板引擎---Angular.js/Vue.js(减少不必要的dom操作,提高渲染效率;双向数据绑定概念[通过框架提供的指令,只需要关注数据的业务逻辑,不再关心dom如何渲染])

Vue核心概念,让用户不再操作dom元素,解放用户双手,让程序员有更多时间关注业务逻辑.

#### 框架和库的区别

框架:**是一套完整的解决方案**,对项目入侵性较大,如果需要更换框架则需要重新架构整个项目.

库:对项目侵入较小,如果某个库无法完成某些需求,可以很容易切换到其他库实现需求.

#### jQuery和Vue的区别

jQuery是js的第三方库,而JavaScript可以用在任何地方不只是用在前端开发.

Vue只是一个前端开发的框架,他只能用在前端开发中,不能用在别的其他地方.

ajax存在于原生的ECMAjs中,jQuery封装了ajax,使调用更方便.也就是说jQuery的底层还是原生ECMAjs.

#### MVC和MVVM关系图解

MVC是后端分层开发概念.  M是modul层,处理数据的==CRUD(增删改查)==;V视图层,前端页面;C业务逻辑层.

MVVM是前端视图层的概念,主要关注于视图层分离,也就是说,把视图层分为了三部分:Model,View,ViewModel.VM是思想核心,是M和V之间的调度者.

## VM的基本属性

==**el:**==element缩写,表示当前我们new的这个Vue实例,要==控制页面上的哪个区域==.

==**data属性**==中存放的是el中要==用到的数据==.    

==**methods属性**,定义当前Vue中所有可用的方法.其中写的应该是业务逻辑.==

==**filters属性**,定义私有过滤器,过滤器有两个条件 **[过滤器名称 和 处理函数].**==

==**directives属性**:自定义私有指令==

==**components属性**:定义私有组件==

==**生命周期函数**==

beforCreate(){},

created(){},

beforMount(){},

mounted(){},

beforUpdate(){},

updated(){},

beforDestroy(){},

destroyed(){},



==渲染==:让它显示出来,就是加载.

Vue省去了操作dom的操作,直接通过Vue提供的指令,很方便就能把数据渲染到页面上,程序员不再需要手 操作dom元素,前端的Vue等框架不提倡手动操作dom元素.

## Vue指令

#### v-cloak,v-text,v-html指令

==v-cloak==能够解决插值表达式闪烁的问题.什么是闪烁问题`?就是在加载页面的过程中,会出现表达式而不是该加载的信息

`{{ msg }}`==插值表达式==,当 想额外在插值表达式外放内容时.

`<h4 v-text="msg"></h4>`  ==**v-text**==和插值表达式能产生相同的效果

**插值表达式和v-text的区别:**1.v-text没有闪烁问题;2.插值表达式***可以在{{}}外放任意内容***,只会替换自己的这个占位符.而v-text***会覆盖***元素中原本的内容;

**v-html和v-cloak,v-text的区别:** 如果想在msg中的信息加标签,插值表达式和v-text会按原文本输出,不会检测代码,而v-html会检测代码输出.`msg2:'<h1>哎哟这是个啥东西呀</h1>' `   与innerHTML是相同的作用.v-html也会覆盖原本的内容.

```javascript
<div id="app">
<p id="content">{{ msg }}</p>
	<p v-html='msg2'></p> 
//实例所控制的区域就是MVVM中的V部分
//Vue省去了操作dom的操作,直接通过Vue提供的指令,很方便就能把数据渲染到页面上,程序员不再需要手动操作dom元素,前端的Vue等框架不提倡手动操作dom元素</div>
<script>
//当我们导入Vue的包之后,在浏览器内存中,就多了一个Vue构造函数
let vm = new Vue({  //new出来的这个vm对象就是MVVM中的VM调度者
    el:"#app",//el:element缩写,表示当前我们new的这个Vue实例,要控制页面上的哪个区域
    data:{  //data属性中存放的是el中要用到的数据,是MVVM中的M部分,,用来保存每个页面的数据
        msg:'欢迎学习Vue', //msg:message信息
        msg2:'<h1>哎哟这是个啥东西呀</h1>'
})
```

#### v-bind指令

==v-bind是Vue中提供用于**属性绑定**的指令==

可以简写为==:要绑定的属性==

用法:`<input type="button" value="按钮" v-bind:title="myTitle">`

简写:`<input type="button" value="按钮" :title="myTitle+'123'">`

myTitle是data下自定义的一个属性,想要引用这个属性里的属性值,就要用到v-bind指令.

v-bind指令会把后面引号中的东西当做js代码去解析执行,会认为它是一个***表达式*** ,表达式+'字符串'是合法的.可以写合法的表达式.

#### v-on指令

Vue中提供了==v-on**事件绑定**机制==,==v-on的缩写是@,缩写时连同v-on后的":"一并去掉==

 在Vue中,使用事件绑定机制为元素指定处理函数的时候,如果==加了小括号,就可以给函数传参了==.  `@click="add()"`

用法:`<input type="button" value="按钮" v-bind:title="myTitle" v-on:click="alert">`   简写:`<input type="button" value="按钮" :title="myTitle+'123'" @click="alert">`     直接在这写不可以,会把事件认为是一个变量,在data中找.

==methods属性和el,data平级==,他是个对象,==定义当前Vue中所有可用的方法.==

##### 事件修饰符

.stop阻止冒泡  	`@click.stop="btnHandler"`

.prevent阻止默认事件

.capture添加事件侦听器使用事件捕获模式(事件捕获机制)

.self只当事件在该元素本身(比如不是子元素)触发时触发回调,冒泡和捕获不可以触发事件处理函数

.once事件只触发一次

**.self和.stop的区别:**.stop是真正全部阻止, .self只阻止自身的冒泡/捕获行为,并不会影响其他的此行为

#### v-model指令

==唯一的一个**双向绑定**指令,非常重要!==

##### 双向数据绑定

v-model指令,**可以实现数据表单元素和model中数据的双向绑定**. 注意!v-model指令==只能运用在表单元素==中   V---M	

在Vue中,已经实现了数据的双向绑定,每当我们修改了data中的数据,Vue会**默认监听到数据的改动,自动把最新的数据应用到页面上**.当我们意识到上面操作的时候,证明已经入门,我们更多的是在进行VM中的model操作,同时在操作model数据的时候,指定的业务逻辑操作.

表单元素包括哪些?input(radio  text address email ....)  select  CheckBox  textarea

##### 简易计算器

```javascript
<input type="text" v-model="n1" >
    <select  id="opt">
        <option value="+">+</option>
        <option value="-">-</option>
        <option value="*">*</option>
        <option value="/">/</option>
    </select>
    <input type="text" v-model="n2" >
    <input type="button" value="=" @click="calc">
    <input type="text" v-model="result"  >
</div>
<script>
    let vm = new Vue({  //改成var才能在控制台看到window.vm所有属性....
        el:'#app',
        data:{
            n1:'',
            n2:'',
            result:'',
            opt:'+'  //opt默认值
        },
        methods:{
            calc(){  //计算器算数方法
                //逻辑,判断操作符
                //正统方法
                switch (this.opt) {
                    case '+':
                        this.result = parseInt(this.n1) + parseInt(this.n2)
                        break;
                    case '-':
                        this.result = parseInt(this.n1) - parseInt(this.n2)
                        break;
                    case '*':
                        this.result = parseInt(this.n1) * parseInt(this.n2)
                        break;
                    case '/':
                        this.result = parseInt(this.n1) / parseInt(this.n2)
                        break;
                }
//一个投机取巧的方法,代码简洁
// let code =  'parseInt(this.n1)' + this.opt + 'parseInt(this.n2)'
// this.result = eval(code)
            }
        }
    });
```

#### v-for指令和key属性

##### 循环普通数组

for循环  ==v-for="名称 in 数组"==   如果要找到索引,把item改成(item,i)

`<p v-for="(item,i) in list">索引值{{i}}:每一项{{item}}</p>`

##### 循环对象数组(常见)

数组里都是对象

`<p v-for="(user,u) in date">id:{{user.id}}--名称:{{user.name}}--索引:{{u}}</p>`

```javascript
data:{
   list:[1,2,3,4,5,6],
   date:[
        {id:1,name:'wa'},
        {id:2,name:'ze'},
        {id:3,name:'mu'},
        {id:4,name:'li'}
    ]
}
```

##### 循环对象

`<p v-for="(val,key,v) in user">值:{{val}}--键:{{key}}--索引:{{v}}</p>`

键值对一般写法是value在前,key在后  也就是value:key
注意:在遍历对象身上的键值对的时候,除了有value和key,在第三个位置还有索引

##### 迭代数字

`<p v-for="count in 10">这是第{{count}}次循环</p>`

注意:如果使用v-for迭代数字的话,前面的==count值从1 开始==

##### key属性注意事项

2.2.0+的版本里,当在组件中使用v-for时,key是必须的

每次for循环时,通过指定key表示来**指定当前循环项的一个唯一身份(索引和循环项唯一对应,永远不会变化)**.

注意,v-for循环的时候,**key属性只能使用number获取string**
        key在使用的时候==必须使用v-bind属性绑定的形式指定key的值==

在组件中,使用v-for循环的时候,或者在一些特殊情况中,如果v-for有问题,必须在使用v-for的同时,指定唯一的 字符串/数字 类型 ` :key` 值

在数组的**==some方法==**中,如果return true,就会立即终止这个数组的后续循环

```javascript
this.list.some((item,i) => {
                    if (item.id === id){
                        this.list.splice(i,1)
//在数组的some方法中,如果return true,就会立即终止这个数组的后续循环
                         return true;
                    }
                 })
```

> ==**push() 方法**==可向数组的末尾添加一个或多个元素，并返回新的长度。    注意： 新元素将添加在数组的末尾。注意： 此方法改变数组的长度。    提示： 在数组起始位置添加元素请使用 ==**unshift() 方法**==。
>
> ==**splice()**== 方法向/从数组中添加/删除项目，然后返回被删除的项目。arrayObject.splice(index,howmany,item1,.....,itemX)        **index**必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。  howmany必需。要删除的项目数量。如果设置为 0，则不会删除项目。

#### v-if指令和v-show指令

**作用:根据条件来展示元素 true/false**

v-if特点:每次都会重新删除或者创建元素,有较高的切换性能消耗

v-show特点:每次不会进行dom的删除和创建操作,只是切换了元素的display:none样式

适用场景:如果有元素频繁切换,用v-show不要用v-if;如果元素几乎永远不会被显示出来就用v-if.

#### 注意事项

注意!!在Vue实例中,如果想要获取data上的数据,或者想要调用methods中的方法,必须通过==this.数据属性名== 或 ==this.方法名== 来进行访问,这里的this就代表我们new出来的实例.

==如果获取数据或者调用方法必须要用**this**!不管哪一层,千万不要忘记!==    

用**substring**截取字符串,0下标开始1下标结束,不包括1下标;单写一个下标时,从这个下标截到字符串结束.

vm有一个特点,它==会监听自己身上data中所有数据的改变==,一有变化马上同步到V(HTML)/页面中,好处就是程序员只需要关心数据,而不需要考虑如何重新渲染dom页面,节省对dom操作.

**setInterval(执行方法,时间[单位ms]) 定时器**

获取数据必须要用this,但是this外面又加了个方法,这是this没有指向,==此时需要删除function,并且**在()后加=> **==  `setInterval( ()=>{},时间)`

==**=>**解决this的指向问题,使箭头内部的this永远和外部保持一致,=和>中间不能有空格==

`findIndex`==查找索引==

#### 跑马灯案例

```javascript
<body>
<!--创建一个要控制的区域-->
<div id="app">
    <input type="button" value="Vue你再跑" @click="lang">
    <input type="button" value="给我停" @click="stop">
    <h4>{{msg}}</h4>
</div>
<script >
//注意!!!!!在Vue实例中,如果想要获取data上的数据,或者想要调用methods中的方法,必须通过this.数据属性名 或 this.方法名 来进行访问,这里的this就代表我们new出来的实例
    let vm = new Vue({  //vm会监听自己身上data中所有数据的改变,只要数据一发生变化,就会自动把最新的数据从data上同步到页面中去
//好处就是程序员只需要关心数据,而不需要考虑如何重新渲染dom页面,节省对dom操作
        el:'#app',
        data:{
            msg:'Vue你别挣扎了,从了我吧',
            intervalID:null  //在data上定义定时器id,此时每当开启定时器都会重新复制一下
        },
        methods:{
            lang(){  //ES6中的写法,不用写function,会自动写入
                if (this.intervalID != null) return;
                this.intervalID =  setInterval( () => {  //放入定时器中,就能实现自动跑了  不要忘记给intervalID加this,获取数据
 //按钮多点几次事件会被叠加触发,越跑越快,所以要加if每次置空         
//获取数据必须要用this,但是this外面又加了个方法,这是this没有指向,此时需要删除function,并且在()后加=>,使箭头内部的this永远和外部保持一致,=和>中间不能有空格
                    let start = this.msg.substring(0,1)  //用substring截取字符串,0下标开始1下标结束,不包括1下标
                    let end = this.msg.substring(1)  //只写一个数字时是直接从此下标截取到字符串结束
                    this.msg = end + start  //重新拼接得到新的字符串,并赋值给this.msg
                },400)
            },
            stop(){
                clearInterval(this.intervalID) //清除定时器
                this.intervalID = null;   //每当定时器关闭时,如果不重新赋值为null,再次开启定时器时不会等值null,if判断不再成立,将永远不会开启新的定时器
            }
        }
    })
//分析:1.给按钮绑定点击事件
//    2.给按钮的事件处理函数中写相关的业务逻辑代码:拿到msg的字符串,然后调用字符串的substring来进行字符串的截取操作,把第一个字符截取出来放到最后一个位置即可.
//    3.为了实现点击一下按钮自动截取的功能,需要把2步骤中的代码放到定时器中定时截取.
</script>
</body>
```

## 在Vue中使用样式

**==对象就是无序键值对(k:v)的组合==**

使用样式有两种方式:`v-bind:class`和`v-bind:style`(class和内联)

如果==属性里面包含"-"==,比如`font-size`这时候就==必须给这个属性加单引号==

`<h1 :style="{'font-size':20px}">`

==如果要写多个样式时都要带中括号,是引入数组==

#### 使用class样式

1.数组    `<h1 :class="['red','thin']">哎哟在这呢</h1>`,第一种使用方式,直接传递一个数组.注意:这里的class需要使用v-bind做数据绑定.class中需以中括号包括,每个类用单引号,否则会当成数据在data中寻找.不便,仅供了解

2.数组中使用三元表达式    `<h1 :class="[flag?'active':'']">哎哟在这呢</h1>`

3.数组中嵌套对象  `<h1 :class="['red',{'active':flag}]">哎哟在这呢</h1>`

4.==直接使用对象==  `<h1 :class="{red:true,active:true}">在这儿在这儿在这呢</h1>`  可以把对象直接写在data中,然后在class中直接引用属性,如下.

`<h1 :class="{classObj}">在这儿在这儿在这呢</h1>`

```javascript
data:{
    flag:true ,   //三元表达式,判断条件值
    classObj:{thin:true,active:true}
},
```

在为class使用v-bind绑定对象的时候,对象的属性是类名,对象的属性可带引号也可不带,属性的值是一个标识符.       真的是万物皆可对象啊.....整个{}是class的对象,把整个对象写在data中起名叫classObj,这个classObj又作为class的属性被引用....

flag(条件)在data中判断true/false

#### 使用内联样式

1.直接在元素上通过`:style`(v-bind)的形式,书写样式对象`<h1 :style="{color:'red','font-size':20px}">直接书写style</h1>`    属性中有-时必须加单引号

2.将样式对象定义到`data`中,并直接引用到`:style`中

​		在data上定义样式

​		在元素中,通过属性绑定的形式,将样式对象应用到元素中

3.在`:style`中通过数组,引用多个data上的样式对象

​		在data上定义样式

​		在元素中,通过属性绑定的形式,将样式对象应用到元素中

