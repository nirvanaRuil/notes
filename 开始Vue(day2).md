# 开始Vue(day2)

## 品牌案例(综day1承接day2)

详见text9_breadCase.html

#### 数组中的新方法

forEach( ) 全部遍历

some( ) 如果返回return true,就会立即终止这个数组的后续循环

filter( ) 过滤,符合条件的返回新数组

findIndex( ) ==查找索引==

这些方法都会对数组中的每一项进行遍历,执行相关操作.

indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。如果没有找到匹配的字符串则返回 -1。
**空字符串也是子串!!!**所以indexOf中值为空时,字符串中的所有对象都存在.

#### 过滤器

案例详见text9_breadCase.html  &  text10_filter.html

Vue中允许自定义过滤器,过滤器被用于一些常见的文本格式化.过滤器可以用在两个地方:**mustachc插值和v-bind表达式**.过滤器应该添加在js表达式的尾部,由"管道"符指示.

过滤器做数据输出前的最后一层处理,不会修改原数据.就==相当于给照片加滤镜,只是对照片做输出前的修改==.不会改变照片本身的内容.

##### 全局过滤器

过滤器调用时的格式:     ==**{{name|过滤器的名称}}**==
怎么调用?   ==**Vue.filter('过滤器的名称',function(name){ })**==
 过滤器中的function,第一个参数已经被规定死了,永远都是过滤器 管道符前面 传来的数据.

过滤器中也可以传参,可以传多个参数,也可以调用多个过滤器(将数据交给第一个过滤器处理,然后由第一个交给第二个,直到最后一个过滤器处理完).

```javascript
<div id="app">
    <p>{{msg|msgFormat('曾经','啊')}}</p>  
<!--过滤器调用,可以传多个参数-->
</div>
<script>
    Vue.filter('msgFormat',function (msg,amz,am) {
//字符串的replace方法,第一个参数除了可以写一个字符串外,还可以定义一个正则
       return msg.replace(/现在/g,amz+am)
//可以把要替换的具体数据写成参数
    })

	Vue.filter('ming',function (msg) {
        return msg + '===这是字符串'
    })
```

```javascript
<td>{{item.ctime|dateFormat('yyyy-mm-dd')}}</td>

Vue.filter('dateFormat',function (dateStr) {
    let dt = new Date(dateStr)
    //根据给定的时间字符串,得到特定的时间
    let y = dt.getFullYear()
    let m = dt.getMonth() + 1  //因为getMonth()是从零开始的
    let d = dt.getDate()
    //定义年月日
    //getDay()返回一星期中的某一天（0-6）  getDate()返回一个月中的某一天（1-31）
    return `${y}-${m}-${d}`//y + '-' + m + '-' + d的简写式
    // return y + '-' + m + '-' + d
})
//全局过滤器,进行时间的格式化    text9_breadCase.html 

//分开写复用性更高  pattern只在if这里有用
        if (pattern.toLowerCase() === 'yyyy-mm-dd'){
            return `${y}-${m}-${d}`
        }else{
            let hh = dt.getHours()
            let mm = dt.getMinutes()
            let ss = dt.getSeconds()
            return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
        }
    })
```

**replace** 字符串替换,第一个参数除了可以写一个字符串外,还可以定义一个正则.

##### 私有过滤器

==filters属性,定义私有过滤器,过滤器有两个条件 **[过滤器名称 和 处理函数]**==

如果全局过滤器和私有过滤器的名称相同,怎么区分用的哪个?

答:过滤器调用的时候采用的是就近原则,如果私有过滤器和全局过滤器名称一致,**优先调用私有过滤器**.

```javascript
filters:{   
//filters属性,定义私有过滤器,过滤器有两个条件[过滤器名称  处理函数]
    dateFormat:function (dateStr) {
        let dt = new Date(dateStr)
        let y = dt.getFullYear()
        let m = dt.getMonth() + 1  
        let d = dt.getDate()
        return `${y}-${m}-${d}`
    }
}
```

#### ES6字符串新用法padStart/padEnd

详见 text10_filter.html

padStart(maxLengh,fillString=' ')  或  padEnd(maxLengh,fillString=' ')

==用来填充字符串==   maxLengh:表示填充完毕后字符串的长度;fillString:表示用来填充的字符串/空格.

因为是属于字符串的用法,所以对于数字要先用toString转成字符串然后再用padStart/padEnd    `padStart(2,'0')`表示总长度2,在前面加0

#### 自定义按键修饰符

详见text9_breadCase.html

比如写完信息要点添加按钮才能添加信息,现在我们要让按下enter达到同样的效果.

`@keyup.enter="add"` 给enter按键绑定add方法.

`@keyup.键盘事件按键对应编码="add"`

`Vue.config.keyCodes.f2 = 113` 自定义全局按键修饰符 自定义名称 =对应的键盘码码值

全部按键别名:   .enter    .tab    .delete(捕获删除和退格键)     .esc    .space    .up    .down    .left    .right

#### 自定义指令

##### 全局指令

详见text9_breadCase.html

Vue有内置指令,也可以自定义设置指令.所有的指令在调用的时候格式都是`v-`,是一种约定.

自定义全局指令  参数1:**指令名称** 注意,在自定义的时候自定义的名称不需要加v-  ,调用的时候要加v-.     参数2:是**一个对象**,这个对象身上,有一些指令相关的钩子函数,这些函数可以在特定的阶段执行相关的操作.

格式:`Vue.directive('指令名称' ,{ 钩子函数:function(el,其他参数) { el.指令名称()} })`

注意!!在每个函数中,==**第一个参数永远都是el,表示被绑定了指令的那个元素**==,这个el参数,是一个原生的js对象(dom).   注意!自定义指令的==**值必须带单引号**==.

==**解析(在内存中)------>插入DOM树,渲染到页面上.**==    行为必须渲染插入到dom树中,而有些东西不需要,比如样式,它在内存中就可以.  因为==样式==只要通过指令绑定给了元素,不管这个元素有没有插入到界面中去,这个元素必定有了一个内联样式,将来元素肯定会显示到页面中,这时候,**浏览器的渲染引擎必然会解析样式,应用给这个元素**.

指令定义函数提供了几个钩子函数(可选):

==**bind**==:(常用) 每当指令绑定到元素上的时候会立即执行这个函数,只执行一次.这是元素还没有被绑定到DOM树上去,**只解析了存在于内存中**时.==**[执行和js样式有关的操作]**==

**==inserted==:(常用) 表示元素插入到dom中的时候,会执行inserted.**  ==**[执行和js行为有关的操作]**==

在元素刚绑定指令时,还没有插入到dom中去,这时候调用focus方法没有用,因为==一个元素只有插入dom之后才能获取焦点== `el.focus()`   

==**update**==:(常用) 当组建/VNode更新的时候会执行updated,可能触发多次.DOM节点一更新.

componentUpdateed:

unbind:

钩子函数的参数:包括**el,binding,vnode(虚拟dom),oldVnode**.

```javascript
bind:function (el,binding) {
    // el.style.color = 'yellowGreen'
    el.style.color = binding.value  //通过第二个参数binding拿到传递的值,注意!自定义指令的值必须带单引号
}
```

##### 私有指令

`directives:{ '指令名称':{钩子函数:function(el,其他参数){ } } }`

##### 指令函数的简写式

这个简写式里,**function等于直接把代码写到了bind和update中去**.

在`bind` 和 `update` 时触发相同行为，而不关心其它的钩子.

私有指令中的简写式

```javascript
'fontSize':function (el,binding){  //注意,这个简写式里,function等于直接把代码写到了bind和update中去
    el.style.fontSize = parseInt(binding.value) + 'px'  //可用性更强,避免不知输入的是字符串还是数字
}
```

全局指令中的简写式

```javascript
Vue.directive('color',function (el,binding) {
        el.style.color = binding.value  //通过第二个参数binding拿到传递的值,注意!自定义指令的值必须带单引号
    }
```

#### lable

<label> 标签为 input 元素定义标注（标记）。

label 元素不会向用户呈现任何特殊效果。不过，它为鼠标用户改进了可用性。如果您在 label 元素内点击文本，就会触发此控件。就是说，当用户选择该标签时，浏览器就会自动将焦点转到和标签相关的表单控件上。

## 生命周期

生命周期钩子 = 生命周期函数 = 生命周期事件

从Vue实例创建,运行到销毁伴随着各种各样事件,这些事件统称为生命周期

创建期间生命周期函数

运行期间生命周期函数

销毁期间生命周期函数

## Vue-resource的使用

详见text12_结合node剖析jsonp原理.html     text13 _改造text9品牌案例.html

 引入包时必须在Vue下面引入Vue-resource ,因为它依赖于Vue.

是Vue的第三方包,用来完成/ajax/数据请求.     

this.$http. json     表示thisVue实例挂载了$http属性.

支持的HTTP方法：vue-resource的请求API是按照REST风格设计的，它提供了7种请求API：

- get(url, [options])*
- head(url, [options])
- delete(url, [options])
- jsonp(url, [options])*
- post(url, [body], [options])*
- put(url, [body], [options])
- patch(url, [body], [options])

除了jsonp以外，另外6种的API名称是标准的HTTP方法。当服务端使用REST API时，客户端的编码风格和服务端的编码风格近乎一致，这可以减少前端和后端开发人员的沟通成本。

```javascript
getInfo(){
  this.$http.get('http://vue.studyit.io/api/gettlunbo').then(function (result) {   //then的传统写法
//发起get请求之后,通过.then来设置成功的回调函数.  通过result.body拿到服务器返回的成功数据
    })
},
postInfo(){
    this.$http.post('http://vue.studyit.io/api/post',{},{emulateJSON:true}).then( result =>{
        console.log(result.body)
//手动发起的post请求默认没有表单格式,所以有的服务器处理不了.
// 设置post方法的第三个参数(option),设置其提交的内容类型为普通表单数据格式{emulateJSON:true}
    })
}
```

#### 数据请求

详见text12_结合node剖析jsonp原理.html     text13 _改造text9品牌案例.html

**发送ajax数据请求时要趁早!最早可以在`created()`中发**,在你发送数据的时候生命周期依旧在不断走,所以当页面挂载的时候数据已经请求完毕,节省不少加载数据的时间.在这段时间里你可以做别的事情.

为了保证方法的适用性,可以给全局配置请求数据接口的根域名.此时每次单独发起http请求的时候,请求的URL路径**应以相对路径开头**,**前面不能带斜线/**,否则就不会启用根路径做==拼接==.

==**拼接是为了更好的适用性**==,不只是域名可以做拼接.

```javascript
Vue.http.options.root = 'http://vue.studyit.io';  //配置全局请求数据接口的根域名
//此时每次单独发起http请求的时候,请求的URL路径应以相对路径开头,前面不能带斜线/,否则就不会启用根路径做拼接
    Vue.http.options.emulateJSON = true;  
//全局启用emulateJSON选项
```

## Vue中的动画

Vue中的动画不炫酷,是为了提高用户体验,**帮助用户更好的理解页面中的功能**.

#### 过渡(方式一)

在进入/离开的过渡中，会有 6 个 class 切换。

进入和离开各有两个时间点和一个时间段.

1. `v-enter`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
2. `v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。(时间段)
3. `v-enter-to`：2.1.8 版及以上定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 `v-enter` 被移除)，在过渡/动画完成之后移除。
4. `v-leave`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
5. `v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。(时间段)
6. `v-leave-to`：2.1.8 版及以上定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 `v-leave` 被删除)，在过渡/动画完成之后移除。

`v-enter-to`和`v-leave`样式相同.`v-enter`和`v-leave-to`样式相同

想用过渡类名创建动画**,只需要写这两组类**`.v-enter` `.v-leave-to`(动画进入之前的起始状态和离开之后的终止状态)和`.v-enter-active` `.v-leave-active`(入场动画的时间段和离场动画的时间段).

`v-move`设置列表后续元素的效果,同时要设置离开的过程也就是`.v-leave-active`

==把需要被动画控制的元素**用transition包裹**起来==,但是在实现列表过渡的时候,如果需要过渡的元素,是==**通过v-for渲染**出来的,不能使用transition包裹,要使用==
==**transition-group**包裹==

给transition-group添加appear属性,实现页面刚展示出来时的进入效果.
transition默认渲染为span元素,用**tag标签**更改transition将要成为的元素类型.

##### 自定义前缀

给元素的transition标签里加==name="自定义前缀"==,来与v-区分,区分不同组之间的动画.

```javascript
<transition name="my">
    <h3 v-if="flog">哎呦来个动画吧</h3>
</transition>
```

#### 引用animate动画样式(方式二)

引用animate动画样式前==必须都加上基本类animated==,类可以在元素上写,也可以在transition上写.

使用**duration="毫秒值"**设置入场/离场时动画的时长,可以一起设置也可以分开设置  `:duration="{enter:200,leave:400}"`

#### 用钩子函数实现半场动画(方式三)

钩子函数分为入场和离场.可称为==动画生命周期==函数.有先后顺序

一个动画生命周期完成之后,再次点击就是重新再开始一次生命周期.

在transition中添加对动画的声明,然后再在methods中写入事件方法执行.

```html
<transition  
@before-enter="beforeEnter"  
@enter="enter"  
@after-enter="afterEnter"  
@enter-cancelled="enterCancelled" 
前四个进场动画,后四个离场动画
@before-leave="beforeLeave"  
@leave="leave"  
@after-leave="afterLeave"  
@leave-cancelled="leaveCancelled" > 
</transition>
```

当只用 JavaScript 过渡的时候，**在 `enter` 和 `leave` 中必须使用 `done` 进行回调**。否则，它们将被同步调用，过渡会立即完成。

注意:动画钩子函数的第一个参数el,表示要执行动画的那个dom元素,是个原生的js对象.可以认为el是通过document.getElementById("")来获取到原生的js DOM对象

```javascript
beforeEnter(el){
    //表示动画入场之前,动画尚未开始,可以在这个里面设置起始样式
    el.style.transform = 'translate(0,0)'  //起始位设置
},
enter(el,done){
//表示动画开始之后的样式,这里可以设置小球完成动画之后的结束状态
    el.offsetWidth //没有实际作用,但是如果不写出不来动画效果,可以认为这句话会强制动画刷新  加offset
    el.style.transform = 'translate(300px,0px)'
    el.style.transition = 'all 1s ease'

    done()//这里的done其实就是afterEnter函数,这句话可以使动画在结束状态之后立即执行动画完成之后的函数
},
afterEnter(el){
//动画完成之后会调用
    this.flag = !this.flag
}
```