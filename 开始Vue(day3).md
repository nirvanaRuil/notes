# 开始Vue(day3)

# Vue提供的标签

**component标签**:占位符,展示对应名称的组件

**template标签**:在外定义的组件模板结构应用template包裹

**transition标签**:需要被动画控制的元素用transition包裹;声明动画的钩子,然后在methods中定义

**transition-group标签**:需要被动画控制的元素是通过v-for渲染出来的,要使用transition-group包裹

# Vue中的组件

什么是组件?组件的出现就是为了拆分Vue实例的代码量的,能够让我们以不同的组件来划分不同的功能模块,将来我们需要什么样的功能,就去调用对应的组件即可.

## 组件化和模块化的区别

模块化:是从代码逻辑的角度进行划分;方便代码分层开发,保证每个功能模块职能单一.

前端组件化:是从UI界面的角度进行划分的.方便UI组件的重用.

## 组件的创建方式

详见text18

注意:无论是哪种方式创建的组件,组件的template属性指向的模板内容==**必须有且只有一个根元素**==.

一:使用Vue.extend来创建全局的Vue组件,使用==**Vue.component('组件名称',创建出来的组件模板对象)**==.

```javascript
//参数一:组件名称,将来引用的时候就是标签名称;参数二:Vue.extend创建的组件,其中template就是将要展示的内容
Vue.component('myCom1',Vue.extend({
    template:'<h3>这是用Vue.extend创建的组件</h3>'
}))
```

二:

```javascript
Vue.component('myCom2',{
    template:'<h3>这是直接使用 Vue.component创建的组件</h3>'
})
```

三:在被控制的区域外使用template元素定义组件的模板结构

```javascript
<template id="tmp1">
    <div>
        <h3>这是通过template元素,在外部定义的组件结构</h3>
        <h4>还是不能忘记只能有一个根元素这件事情</h4>
    </div>
</template>
Vue.component('myCom3',{
    template:'#tmp1'
})
```

## 创建私有组件

```javascript
<template id="log">
    <h4>在外部用template定义私有组件模板结构</h4>
</template>

components:{  //定义内部私有组件
    login:{  //组件名称
        template:'<h2>这是自定义的私有login组件</h2>'
    },
    login2:{
        template:'#log'
    }
}
```

## 组件中的data

**组件中的data不是对象,是函数,而且必须要有返回对象return{ }**,template中可以引入return中的数据.

使用方式和实例中的data使用方式完全相同.

```javascript
Vue.component('myCom4',{
    template:'#tmp2',
    data:function () {
//组件中的data不是对象,是函数,而且必须要有返回对象return{}
        return {
            msg:'组件中的数据'
        }
    }
})
```

为什么组件中的data必须是一个function?

如果存在多个组件的话,此时不在function中定义的话,所有的组件都会同时引用methods,而不是分开引用.

## 组件切换

详见text19

方式一:用`v-if`和`v-else`实现,缺陷--**只能切换两个**,两个以上切换无能为力.

```javascript
a href="#" @click.prevent="flag=true">登录</a>
<a href="#" @click.prevent="flag=false">注册</a>

<login1 v-if="flag"></login1>
<login2 v-else="flag"></login2>
```

方法二:用component来展示对应名称的组件.

:is属性,可以用来指定要展示的组件名称.

注意:**组件名称一定要加单引号**.

```javascript
<a href="#" @click.prevent="comName='login3'">登录</a>
    <a href="#" @click.prevent="comName='login4'">注册</a>
    <component :is="comName"> </component>
<!--component是一个占位符  :is属性,可以用来指定要展示的组件名称-->
```

方法三:应用切换动画和mode方式(只是用动画润色)

只需在要动画的组件外包裹上transition.然后在style中设置动画.

`<transition mode="out-in">`  mode属性设置组件切换时候的方式,要不然会切换的不好看啦.

## 父子组件

这父子组件都可以只向对方传值,而不可以从对方那里获取值.

#### 父组件向子组件传值

详见text21

==在子组件上绑定事件==:在引用子组件的时候可以通过属性绑定(`v-bind:传递名称=""`)的形式,把父组件需要传递给子组件的数据,传递到子组件内部,供子组件使用.

传过来归传过来,要怎么用呢?

首先要把父组件传递过来的属性(也就是传递名称),在`props:[ ]`**==数组==**中定义一下,然后再把传递名称插值表达式到子组件中去.

组件中所有props的数据**都是通过父组件传递给子组件**的.

==通过v-bind==绑定事件方法来实现.

##### data和props的区别(重要)

区别一:子组件中的data数据,并不是通过父组件传递过来的,而是子组件本身私有的.比如:子组件通过ajax,请求回来的数据,都可以放在data身上;所有的props中的数据都是通过父组件传递给子组件的.

data数据都是可读可写,而props上的数据是只读.

#### 父组件向子组件传方法

在子组件的methods中通过`this.$emit('func')`可以触发父组件中传过来的方法,==通过v-on==父组件上 绑定自定义事件来实现.

子组件也可以==通过**parent**属性($root)==找到父元素的Vue对象. [这种方法不利于封装,维护,应急方法不建议使用]

在methods中`this.$parent.fangFa(对象)`           在template中直接$parent

#### 子组件向父组件传值 

详见text21

子组件通过事件调用向父组件传值.其实子向父传数据可能只能通过调用方法时加参数....子调用父数据(方法)时,子的方法中可以加参数的`this.$emit('func',this.sonmsg) `,加的参数同时也会传给父组件,然后会相应的在父方法中加参数名.

子组件传递数据给父元素,需要自定义触发事件,实现数据的传值

也可以把子组件中的data传到父组件中去,也是以参数的形式,但是直接写的参数不能和data放在一块儿

==从子组件传参拿到的数据可以存到自己的data中并加以调用==

```javascript
<script>
//定义了一个字面量类型的组件模板对象
let com2 = {
    template: '#tem1', //通过指定了一个id,表示说要去加载这个指定的id的template元素中的内容当做组件的HTML结构.
    data(){
        return{
            sonmsg:{name:'小头', age:6}
        }
    },
    methods: {
        my (){  //当点击子组件按钮时,如何拿到父组件给的方法并调用?
         // this.$emit('func',123,456)
            this.$emit('func',this.sonmsg)  
 //emit原意为触发调用发射.
// 从第二个参数开始传参,传参时要相应的在方法中写上对应参数名
        },
    }, 
}

let vm = new Vue({
    el:'#app',
    data:{
//组件中的data数据,并不是通过父组件传递过来的,而是子组件本身私有的.比如:子组件通过ajax,请求回来的数据,都可以放在data身上
        msg: '这是父组件中的数组嗷',
        style1:{color:'blue'},
        dataFormSon:null  
        //从子组件传参拿到的数据可以存到自己的data中并加以调用
    },
    methods:{
        // show(zhi,zhi2){    //可以直接写参数,也可以把data搬进来,直接写的参数和data还不能搁一块儿写
            //console.log('怎么把方法传递给子组件---'+ zhi + zhi2)
        // }
        show(data){  
            //因为传了参,在这写对应参数名,data就是子组件上的参数
            this.dataFormSon = data
        }
    },
    components:{  //这个components相对于vm而言就是子组件,vm本身也可以算作一个组件
        com1:{  //默认情况下子组件无法访问父组件上的数据和方法
            props:['isName'],  //组件中所有props的数据都是通过父组件传递给子组件的,而且只读,无法重新赋值
//当把值传过来之后要怎么用呢?
//首先要把父组件传递过来的属性(也就是传递名称),在`props:[ ]`数组中定义一下,然后再把要引用的父组件的值插值表达式到子组件中去.
            template:'<h1>这是子组件----{{ isName }}</h1>',
        },
        com2//在外面定义过了   父组件给子组件怎么传方法
    },
});
</script>
```

## 对象怎么保存到localStorage?

详见text22

1把对象保存到localStorage.因为localStorage只支持存放字符串数据,所以要先调用**JSON.stringify**转换为json字符串.
2如果每次都直接在localStorage存放新数据,后面数据会把前面数据覆盖掉.应当在保存最新的数据之前,先在localStorage中获取之前的数据(string),转换为一个数组对象(用**JSON.parse()方法**将一个JSON字符串转换为对象),然后把最新数据push到这个数组中.
 3如果获取到的数据为空或不存在会报错,则可以返回一个**"[ ]"**(返回一个空数组),让json自己转换,此时不会报错.
4把最新获取到的数组再次调用JSON.stringify转为数组字符串,然后调用**localStorage.setItem()**存储数据.

----HTML5 提供了两种在客户端存储数据的新方法：

1.==localStorage== - 没有时间限制的数据存储.
    localStorage.getItem(key):获取指定key本地存储的值
    localStorage.setItem(key,value)：将value存储到key字段
    localStorage.removeItem(key):删除指定key本地存储的值
    localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡.
2.==sessionStorage== - 针对一个 session 的数据存储,当用户关闭浏览器窗口后，数据会被删除.

## ref的应用

ref是单词reference,意为引用.  获取dom节点

`ref`在元素中,只代表这个元素,而==`$refs`可以引用多个ref==.

组件也可以引用ref.    

意义何在?--引用组件的数据和方法(`this.$refs.mylogin.show`)

## 组件插槽slot

实现内容分发,是个占位符

如果有多个slot,则需要写template 加`v-slot=""`,没有加的就是默认值,如果非要写也可以给有默认值的div加上template写上`v-slot="default"`.

插槽（Slot）是Vue提出来的一个概念，正如名字一样，插槽用于决定将所携带的内容，插入到指定的某个位置，从而使模板分块，具有模块化的特质和更大的重用性。

插槽显不显示、怎样显示是由父组件来控制的，而插槽在哪里显示就由子组件来进行控制

```javascript
<slot title="这是标题"> 
	<div > 
    	这是内容
	</div>
<template v-slot="btn"> 这是当有多个slot的时候,要用name,此时就要写个template标签,在此标签中写需要被传值的slot	</template>
</slot>


<slot  name="btn">占位了,slot中div的内容</slot>    
```

`v-slot="btn"`也可以写成:

`class="slotBtn"`---`<slot-btn></slot-btn>`

# 路由

什么是路由?

后端路由:对于普通网站,所有的链接都是URL地址,所有的URL地址都对应响应服务器上对应的资源;

前端路由:只存在于前端,对于单页面应用程序来说,主要通过URL中的hash(#号)来实现不同页面之间的切换,同时,hash有一个特点:http请求中不包含hash的相关内容,所以单页面程序中的页面跳转主要用hash实现.在单页面应用程序中,这种通过hash改变来切换页面的方式,称作前端路由(区别于后端路由).

## 基本结构使用方法

详见text24

VueRouter提供的元素,用来当占位符.

`<router-link to="/路由地址"></router-link>`
`<router-view></router-view>`

我感觉这个路由就是给组件外加了一层url地址.

使用路由有三个重要的点:

1.创建路由对象,里面有routes路由匹配规则(是一个数组);`redirect`,用来设置默认展示;**component**里面**必须放组件模板对象,不能是组件的引用名称**

```javascript
let routerObj = new VueRouter({
    routes:[  //这个route表示路由匹配规则
        {path:'/',redirect:'/login'} ,  
//redirect,来设置默认展示login
        {path:'/login',component:login} ,
//这个里面必须放组件模板对象,不能是组件的引用名称
        {path:'/register',component:register}
    ]
})
```

​		创建一个路由对象,当导入包之后,在window全局对象中,就有了一个路由的构造函数,叫做VueRouter;在new路由对象的时候,可以为构造函数,传递一个配置对象.

​		每个路由规则都是一个对象,这个==规则对象身上有两个必须的属性:1--**path**,表示监听,哪个路由器链接地址;2--**component**,表示如果路由是前面匹配到的path,则展示component属性对应的那个组件.==

2.创建的VueRouter注册到vm的属性router中,用以监听URL地址变化;

```javascript
router:routerObj  //将路由规则对象注册到vm实例上,用来监听URL地址的变化,展示对应组件
```

3.在对应的vm控制区域放上占位符`<router-view>`,VueRouter提供的元素用于将路由器规则匹配的文件由此展示出来.`router-link`表示一个链接,`to=''`是链接对象,可以改变它的标签属性,但是此标签会**默认有一个click事件**.

```javascript
<router-link to="/login"></router-link>
<router-link to="/register"></router-link>
<!--router-link默认渲染a标签,如果要改变渲染标签,用tag=""-->
<router-view></router-view>
<!--这是Vue-router提供的元素,专门用来当做占位符,将来路由规则匹配的文件将会展示到这个router-view中去-->
```

## 设置选中路由高亮显示

详见text24

默认class值为`router-link-active`

```javascript
<style>
        .router-link-active{
            color: #9acfea;
            font-style: italic;
            font-size: 17px;
        }
    /*    实现路由高亮*/
    </style>

linkActiveClass:'xiugai'  //修改默认路由高亮class值
```

### 切换启动动画

```javascript
.v-enter,
.v-leave-to{
    opacity: 40%;
    transform: translateX(30px);
}
.v-enter-active,
.v-leave-active{
    transition: all 0.3s ease;
}

<transition mode="out-in">  //改变动画模式
    <router-view></router-view>
</transition>
```

## 在路由规则中定义参数(动态路由匹配)

组件中也有生命周期函数.

1.==用`query`传递参数==,`this.$route.query.id`,可以一次传递多个参数,如果在路由中使用查询字符串,给路由传递参数,不需要修改路由规则的path.

```javascript
<div id="app">
<!--如果在路由中使用查询字符串,给路由传递参数,不需要修改路由规则的path-->
    <router-link to="/login?id=10&name=richard">登录</router-link>
    <router-link to="/register">注册</router-link>
    <router-view></router-view>
</div>
<script>
    let login = {
        template:'<h1>登录? --- {{ $route.query.id }}---{{ $route.query.name }}</h1>',  //这里this 可以省略
        created(){  //组件的生命周期钩子函数
            // console.log(this.$route)
            console.log(this.$route.query.id)
        }
    }
```

2.==用`params`传递参数==.这种方法与query有些许不同,需要修改路由规则的path,要在path中放入参数,然后在`router-link`中放入参数值,同样可以有多个参数.

`{path:'/register/:id/:name',component:register}`



## 路由的嵌套

用`children:[ ]`路由嵌套.使用children属性实现子路由时,子路由的path前面==不能加斜线(/)==,否则永远以根路径开始请求,这样不方便用户去理解URL地址.

```javascript
let router = new VueRouter({
    routes:[  //这个route表示路由匹配规则
        {
            path :'/account' ,
            component:account,
            children:[  //路由嵌套,父级是'/account'
                {path :'login' , component:login},  //前面不能加斜线
                {path :'register' , component:register},
            ]
        },
```

## 命名视图(和slot用法相同)

**命名视图**,页面上的==router-view都可以起名字,放置对应名称的组件==.实现**==多视图,同时展示多组件==**.

第一种写法,路由直接写在Home页面中

```javascript
<router-view  name="default"></router-view>
//没有任何指定就是default,如果非要写name就写 name="default"
    <div class="contain">
        <router-view name="left"></router-view>
        <router-view name="main"></router-view>
//这种就叫命名视图,页面上的router-view都可以起名字,可以放置对应名称的组件
//视图的名称只是一个值,字符串 . 只有加:后才会考虑是否是变量
    </div>

{path:'/',components:{
    'default':header,   //默认状态
        'left':leftBox,
        'main':mainBox
    }}  //同时展示多组件
```

第二种写法:路由可以不在Home中写,直接全部写在index中

```javascript
import HelloWorld from '@/components/HelloWorld.vue'
import qwe from '@/components/qwe.vue'

Vue.use(VueRouter)
Vue.use(less)

const routes = [
  {
    path: '/',
    name: 'Home',
    components: {
      default:HelloWorld,
      qwe:qwe,  //这两部分组件写在components里
    }
  },
]

//以下是App部分
  <router-view/>  //没有name代表默认部分,就是HelloWorld
  <router-view name="qwe"/>
//是可以有多个router-view的!需要命名视图就是需要 name="appname"
```

什么时候考虑**属性后是一个变量还是一个值**?

当属性只是一个光秃秃的属性,比如`name="left"`,那他就只是一个值,字符串;只有当属性前加`:`时,才会考虑到他是不是一个变量.

## 重定向和别名

### 重定向

通过route配置完成

重定向是当用户访问`/a`时,URL将会被替换为`/b`,然后匹配路由为`/b`

可以直接重定向到一个固定目标,也可以重定向到一个命名路由,甚至可以是一个方法动态返回目标

```javascript
//从/a重定向到/b
const router = new VueRouter({
    routes:[
        {path:'/a' , redirect:'/b'}
    ]
})

//重定向的目标也可以是一个命名路由
const router = new VueRouter({
    routes:[
        {path:'/a' , redirect:{name:'foo'}}
    ]
})

//重定向的目标是一个方法,动态返回重定向目标
const router = new VueRouter({
    routes:[
        {path:'/a' , redirect:to =>{
            //方法接收 目标路由 作为参数
            //return重定向的 字符串路径/路径对象
            return ''
        }}
    ]
})
```

### 别名

`/a`的别名是`/b`,意味着当用户访问`/b`时,URL会保持为`/b`,但是路由匹配规则为`/a`,就像用户访问`/a`一样.

别名的功能让你自由的将UI结构映射到任意的URL,而不是受限于配置的嵌套路由结构.

```javascript
const router = new VueRouter({
    routes:[
        {path:'/a' , component:A, alias:'/b'}
    ]
})
```

## 路由组件传参

在组件中使用`this.$route`会使之与其对应路由形成高度耦合,从而使组件只能在某些特定的URL上使用,限制了其灵活性.

此时需要使用==props==将组件和路由解耦,取代与`route`的耦合.这样可以在任意地方使用该组件,使该组件更易于复用和调试.

如果`props`被设置为true,`this.$route.params`将会被设置为组件属性.

`props`可以是对象模式/函数模式/布尔值模式.

解耦之前:使用`this.$route`:

```javascript
const User = {
    template:'<div>User {{$route.prams.id}}</div>'
}
const router = new VueRouter({
    routes:[
        path{'/user/:id',component:User}
    ]
})
```

解耦之后:使用`props`:

```javascript
const User = {
    props:['id']
    template:'<div>User {{id}}</div>'
}
const router = new VueRouter({
    routes:[
        path{'/user/:id',component:User,props:true},
        {                
        path:'/user/:id',
    	components:{default:User,side:sidebar}
    	props:{default:true,side:false},  
//如果包含命名视图,则需要给每个命名视图加props
		}        
    ]
})
```

# 设置路由导航的两种方法

## 一、router-link :to="..."  

 　to里的值可以是一个字符串路径，或者一个描述地址的对象。例如：

```javascript
// 字符串
<router-link to="apple"> to apple</router-link>
// 对象
<router-link :to="{path:'apple'}">to apple</router-link>
// 命名路由
<router-link :to="{name: 'applename'}">to apple</router-link>
//直接路由带查询参数query，地址栏变成 /apple?color=red
<router-link :to="{path: 'apple', query: {color: 'red' }}">to apple</router-link>
// 命名路由带查询参数query，地址栏变成/apple?color=red
<router-link :to="{name: 'applename', query: {color: 'red' }}">to apple</router-link>
//直接路由带路由参数params，params 不生效，如果提供了 path，params 会被忽略
<router-link :to="{path: 'apple', params: { color: 'red' }}">to apple</router-link>
// 命名路由带路由参数params，地址栏是/apple/red
<router-link :to="{name: 'applename', params: { color: 'red' }}">to apple</router-link>
```

## 二、router.push(...)方法

 　同样的规则也适用于router.push(...)方法。

```javascript
// 字符串
router.push('apple')
// 对象
router.push({path:'apple'})
// 命名路由
router.push({name: 'applename'})
//直接路由带查询参数query，地址栏变成 /apple?color=red
router.push({path: 'apple', query: {color: 'red' }})
// 命名路由带查询参数query，地址栏变成/apple?color=red
router.push({name: 'applename', query: {color: 'red' }})
//直接路由带路由参数params，params 不生效，如果提供了 path，params 会被忽略
router.push({path:'applename', params:{ color: 'red' }})
// 命名路由带路由参数params，地址栏是/apple/red
router.push({name:'applename', params:{ color: 'red' }})
```

## 三、注意点

**1、关于带参数的路由总结如下：**

> 无论是直接路由“path" 还是命名路由“name”，带查询参数query，地址栏会变成“/url?查询参数名：查询参数值“;
>  直接路由“path" 带路由参数params params 不生效;
>  命名路由“name" 带路由参数params 地址栏保持是“/url/路由参数值”;

**2、设置路由map里的path值：**

>  带路由参数params时，路由map里的path应该写成: path:'/apple/:color' ;
>  带查询参数query时，路由map里的path应该写成: path:'/apple' ；

**3、获取参数方法：**

> 在组件中： {{$route.params.color}}
>  在js里： this.$route.params.color

# 路由进阶

## 路由元信息meta(登录验证)

一个路由特有的属性就可以写在路由元信息里 meta

```javascript
VueRouter.beforeEach((to,from,next)=>{    if(to.matched.some(record=>record.meta.requireLogin)){
      //to.matched表示匹配路由的所有路由记录的数组
      //record.meta.requireLogin表示路由记录中的meta中的requireLogin属性
           let token=localStorage.getItem("token"); 
           if(token==null||token==""){
                  next("/login")
            }else{
                  next()
            }
        }else{
              next();
        }
 })
```

==网站登录验证==     一个路由匹配到的所有记录会暴露为`$route`对象(还有**在导航守卫中的**路由对象)的`$route.matched`数组.因此我们**需要对路由记录遍历--用`$route.matched`来检查路由记录中的`meta`字段**.用beforeEach钩子函数进行验证操作.

定义路由的时候可以配置`meta`字段:`meta:{requiresAuth:true}`.用来设置页面跳转的一个判断条件,也就是==**登录验证**==之类的.  meta中

我们称routes配置中的每个路由对象为==**路由记录**==.定义:routes配置的每一个路由对象都称为路由记录.
特点:路由记录是可以嵌套的，如果一个路由匹配成功，它可以匹配多个路由.

## 导航守卫

由一个页面跳转到另一个页面需要经过一系列事件,这些事件就可以称作守卫.就是你得符合相应条件才可以跳转这个意思.  所以他的主要作用就是通过跳转或者取消的方式守卫导航,有多中机会植入路由导航的过程中:全局前置/路由独享的守卫/组件内的守卫.

`beforeRouteEnter(to,from,next){在渲染该组件的对应里由被confirm前调用;不能获取组件实例this,因为在当前守卫执行前组件实例还未创建}`

&`beforeRouteUpdate(to,from,next){在当前路由改变,但是该组件被复用时调用(因为会渲染同样的父组件)}`

&`beforeRouteLeave(to,from,next){导航离开该组件对应路由时调用,能调用this}`

### **全局前置守卫**

用`router.beforeEach`注册一个全局前置守卫.当一个导航触发时,全局前置守卫按照创建顺序调用.守卫是异步解析执行,此时导航在所有守卫resolve完之前一直处于==等待中==.

`router.beforeEach((to,from,next) => { })`

`to`---即将要进入的目标路由对象;`from`--当前导航正要离开的路由;`next:function`--一定要调用该方法来resolve这个钩子.

执行效果依赖`next`方法的调用参数.  `next()`--执行管道中的下一个钩子,如果钩子全部执行完则导航状态是confirmed确认的;`next(false)`中断当前导航,URL地址重置到`from`路由对应的地址.

记住:==参数或查询的改变并不会触发进入或离开的导航守卫==.你可以通过观察`$route`对象来应对这些变化,或者使用beforeRouteUpdate的组件内守卫.

## 神奇的注释/webpackChunkName

**/* webpackChunkName: "about" */**   神奇的注释 

可以==按需打包==,将几个包分成小包打在一起  起一个叫about的包名,把about放在里面

==**懒加载**==

# watch监听

watch有两个属性,`newVal,oldVal`  用v-model双向绑定数据.

watch的优势是可以监听到看不见摸不着的.比如路由地址.

```javascript
watch:{    //使用这个属性,可以监视data中指定数据的变化,然后触发watch中对应的function处理函数.
	firstname:function (newVal,oldVal) {
   	 	this.fullname = newVal + '-' + this.lastname
	},
	lastname:function (newVal) {
    	this.fullname = this.firstname + '-' + newVal
	},
}
```

## watch,methods和computed三种监听方式对比

`computed`属性的结果会被保存,除非依赖的响应式属性变化才会重新计算,==主要当属性使用==;

`methods`方法表示一个具体的操作,==主要书写业务逻辑==;

`watch`一个对象,键是需要观察的表达式,值是对应的回调函数.==主要用来监听某些特殊数据的变化==,从而进行某些具体的业务逻辑操作,可以看作是`computed`和`methods`的结合体.

watch和computed相同点:都可以监听到数据变化,书写格式中都用`'需要观察的表达式':function(){}`.不同的是computed的function中必须要有一个返回值`return`.

# Vue-cli中ES6两种引入方法

```
import demo3 from "../views/demo3.vue";
const demo4 = () =>
    import("../views/demo4.vue")
//异步加载,只有当访问到这个页面时才会请求这个文件,用这种方法比较好
```







