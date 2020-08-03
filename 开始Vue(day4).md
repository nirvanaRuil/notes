# axios(拦截器)

ajax --- axios --- fetch

**axios是基于promise对ajax的一种封装.**

是一个请求的库.可以做前端(ajax)或后端(http)请求都可以.

支持node端和浏览器端,支持promise

`npm i axios`安装

## 基本使用

- 使用默认方式发送请求(默认使用get请求)

```javascript
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    axios({
        url:'  ',
    }).then(res => {
        console.log(res)   //简写:res.log
    })
//一个最基本的数据请求与接收
</script>
```

- 使用指定方式为get/post的**无参请求**

```javascript
<script>
    axios({
        url:'',
        method:'get'  //或者post
    }).then(res => {
        console.log(res)   //简写:res.log
    })
//一个最基本的数据请求与接收
</script>
```

- axios发送get方式有参请求,方式一--参数直接拼接到URL中.

```javascript
axios({ url:'Http://localhost:9999/student/student/findStudentById?id=1',  //参数直接拼接到URL中
    method:'get'
}).then(res => {
    console.log(res)   //简写:res.log
})
```

- axios发送get方式有参请求,方式二--参数写入params.

```javascript
axios({        url:'Http://localhost:9999/student/student/findStudentById',
        params:{
            id:1,
            name:'2'
            //可以有多个参数请求
        },
        method:'get'
    }).then(res => {
        console.log(res)   //简写:res.log
    })
    //get带参的数据请求与接收
```

- axios发送post方式有参请求,参数写入params.

```javascript
axios(
  url:'Http://localhost:9999/student/student/findStudentByName',
     params:{
         name:'2'
            //可以有多个参数请求
     }, //method为post时,使用data后台会出现拿到数据是null的情况,因为json接收不到
        method:'post'
     }).then(res => {
        console.log(res)   //简写:res.log
        })
    //post带参的数据请求与接收
```

method为post时,使用data后台会出现拿到数据是null的情况,因为**axios使用post携带参数请求默认使用application/json**,发送的是一个json数据.

**解决方法**:1---params属性进行数据的传递;

​                 2----"name = 张三"

​				3----服务器端给接收的参数加上@requestBody				

## axios请求方式

简写式

**axios.get/post无参请求**:

get和post的无参请求相同.

```javascript
axios.get('Http://localhost:9999/student/student/findStudentById').then( res => { 
    console.log(res);
}).catch(err => {
        console.log(err);  //请求失败
    })
```

**axios.get有参请求**:

```javascript
axios.get('Http://localhost:9999/student/student/findStudentById', {params: {id: 1 , name:'张三'}}).then( res => {
    console.log(res);
}).catch(err => {
    console.log(err);  //请求失败
})  //get有参请求接收  注意传递的params是个对象,可以多个请求对象
```

**axios.post有参请求**

==推荐这种axios.post有参请求==

使用这种方法后台不需要处理数据,只修改前端代码

如果按照params写会出现null的情况,所以要直接写参:` "name=张三&age=14"`

```javascript
axios.post('Http://localhost:9999/student/student/findStudentById', "name=张三&age=14").then( res => {
    console.log(res);
}).catch(err => {
    console.log(err);  //请求失败
})  //post有参请求接收
```

这种方式也可以,但是后台要处理数据,**要将axios自动装换的json数据转换为Java对象**.

==服务器端给接收的参数加上@requestBody==	

```javascript
axios.post('Http://localhost:9999/student/student/findStudentById', {name:'张三'}).then( res => {
    console.log(res);
}).catch(err => {
    console.log(err);  //请求失败
})  //post有参请求接收
```

## axios的并发请求

基础并发请求,因为==请求成功响应的是一个数组==,所以在这种基础的没经过处理的情况下挤在一起不好辨认.

```javascript
axios.all([
    axios.get('Http://localhost:9999/student/student/findStudent'),
    axios.get('Http://localhost:9999/student/student/getStudent',{params: {id: 1 , name:'张三'}}),
]).then( res => {
    console.log(res);
}).catch(err => {
    console.log(err);
})  //基础并发请求,因为并发请求返回的数据是一个数组,所以在这种基础的没经过处理的情况下挤在一起不好辨认.
```

==用`axios.spread((res1,res2)=>{ }))`== 

```javascript
axios.all([
    axios.get('Http://localhost:9999/student/student/findStudent'),
    axios.get('Http://localhost:9999/student/student/getStudent',{params: {id: 1 , name:'张三'}}),
]).then(
    axios.spread((res1,res2)=>{
        console.log(res1);
        console.log(res2);
    })).catch(err => {
    console.log(err);
})  //改良后的并发请求
```

## axios的全局配置

以==**axios.defaults**==进行设置.

```javascript
axios.defaults.baseURL = 'Http://localhost:9999/student/student';
axios.defaults.timeout = 5000;
axios.get('getAllStudent').then(res => {
    console.log(res);
});   //把公共部分提取出来全局配置,在之后的操作中就不需要写这么长的地址

axios.post('findStudentById').then(res => {
    console.log(res);
})  //在全局配置的基础上继续写,在这样写是不是很方便了呢
```

## axios实例

一个项目中的网络资源不可能只有一个,一成不变的.基于这种情况创建了axios实例.

创建某种实例,基于这个实例创建特定的网络请求,基于创建的axios实例创建属于自己的实例的配置,一个请求参数.

==`let newVar1 = axios.create({ })`创建一个axios实例==.

在实例下对不一样的地方进行添加,和全局配置是一样的思路.

```javascript
let newVar = axios.create({
    baseURL:'Http://localhost:9999/student/student',
    timeout : 5000,
})  //创建axios的实例,只有在使用这个实例的时候才能生效.嗷~原来是这个区别
newVar({
    url:'getAllStudent'
}).then(res => {
    console.log(res);
})


let newVar1 = axios.create({
    baseURL:'Http://localhost:9999/student',
    timeout : 5,  //时间太短了吧,所以这个实例会catch
})
newVar1({
    url:'student/getAllStudent'
}).then(res => {
    console.log(res);
}).catch(error => {
    console.log(error);
})
//对不一样的地方进行添加,和全局配置是一样的思路
```

## axios拦截器

==拦截器的用法:`axios.interceptors.request/response.use(成功方向,失败方向)`==.

axios给我们提供了两大类拦截器:

1.请求方向 ---成功的和失败的

2.是响应方向 ---成功的和失败的

拦截器的作用:主要用于我们在网络请求的时候,在**发起请求之前或者响应的时候对操作进行相应的处理**.

例如在发起请求时可以添加网页加载的动画;使用token认证时,可以检查,决定是否强制登录,

请求方向的成功和失败:

在写完处理请求之前的操作后,要==记得加`return  `给程序放行==,要不然程序打住了不会往下运行.   这竟然和filter是相同的作用啦....?

```javascript
axios.interceptors.request.use(config => {
    console.log("请求进入拦截器");
    console.log(config);
     return config;  //如果不加return的话,程序走到这就停住了,不会再往下运行
}),err => {
    console.log(err);
}  //请求方向的成功和失败两种拦截. 此时不需要把失败的情况用catch写出来,因为人家自己带了成功和失败两种情况啦

axios.get('Http://localhost:9999/student/student/findStudent').then(res =>{
    console.log(res);
})
```

响应方向的成功和失败:

注意:**在响应方向,如果直接写return,会返回所有数据,如果你只想要data,那么就在config后.data**.

```javascript
//响应方向
axios.interceptors.response.use(config => {
    console.log("请求进入拦截器");
    console.log(config);
    return config.data;  //这里直接写return时,会返回所有数据,如果你只想要data,那么就在config后.data
}),err => {
    console.log(err);
}  //响应方向也一样,自带两种情况

axios.get('Http://localhost:9999/student/student/findStudent').then(res =>{
    console.log(res);
})
```

## axios在Vue中的封装

不能每次都在main.js中写axios,所以要重新创建一个.js的文件,封装起来好调用.

第一种封装方式:  传递请求参数

```javascript
//封装位置  文件绝对位置"./request/request.js"
import axios from 'axios'
export function request(config,success,fail) {
    axios({
        url:config
    }).then(res => {
        success(res);
    }).catch(err => {
        fail(err);
    })
}

//调用位置  main.js
import {request} from "./request/request.js";
request( 'Http://localhost:9999/student/student/findStudent'),res => {
  console.log(res);
},err => {
  console.log(err);
}  //第一种方式,传递请求参数,把回调函数返回带到相应的位置
```

第二种封装方式:  请求参数封装在一个回调函数中

```javascript
//封装者位置  文件绝对位置"./request/request.js"
export function request(config) {
    axios.default.baseURL = "Http://localhost:9999/student/student/findStudent";
    axios(config.url).then(
        res => {
            config.success(res);
        }
    ).catch(err => {
        config.fail(err);
    })
}  //第二种封装方式

//调用者位置  main.js
import {request} from "./request/request.js";
request({
    url:'findStudent',
    success:res => {
      console.log(res);
    },
    fail:err => {
      console.log(err);
    }
})  //将回调函数封装到一个回调函数中
```

第三种封装方式:

```javascript
//封装者位置  文件绝对位置"./request/request.js"
let newVar = axios.create({
  baseURL:"Http://localhost:9999/student/student/findStudent",
    timeout:5000
});
return new Promise((resolve,reject) => {
    newVar().then(res => {
        resolve(res);
    }).catch(err => {
        reject(err);  //用的是promise里的东西
    })
})  //第三种封装方式

//调用者位置  main.js
request({
  url:'findStudent',
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})

```

第四种封装方式:(**推荐使用**)

```JavaScript
//封装者位置  文件绝对位置"./request/request.js"
let newVar = axios.create({
  baseURL:"Http://localhost:9999/student/student/findStudent",
    timeout:5000
});
return newVar(config);  //第四种方式,最简洁的方式,axios实例创建完之后默认返回promise实例,不需要进一步处理,直接返回就可以辽

//调用者位置  main.js
request({
  url:'findStudent',
}).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})
```



# Vuex(状态管理模式)

详见css/vuex

vuex是一个专门为Vue.js应用程序开发的==**状态管理模式**==.它采用集中式存储管理应用的所有组件的状态,并以相应的规则保证状态以一种可预测的方式发生变化.Vuex也集成到Vue的官方调试工具devtools extension,提供了诸如零配置的time-travel调试,状态快照导入导出等高级调试功能.

状态就是数据data,为什么要管理? 当遇到多个组件共享状态时,单向数据流的简洁性容易被破坏:1-多个视图依赖于同一状态;2-来自不同视图的行为需要变更统一状态.

数据传递很复杂且容易出错,复用性也不高.所以操作这种**==多组件共享数据==**时(要点:==**多**==&**==共享==**),需要将这样的数据剥离出来单独管理.

通过定义和隔离状态管理中的各种概念通过强制规则维持视图和状态之前的独立性,我们的戴拿将会变得更结构化且易维护.

核心包含四个部分:state[数据源]-getter[计算属性]-mutation[处理/修改状态]-action[异步处理]

## 状态自管理应用包含

- state,驱动应用的数据源
- view,以声明方式将state映射到视图
- actions.响应在view上的用户输入导致的状态变化

单向数据流: state ----> view ----> actions ----> state

## 基本应用

创建整个项目的数据仓库对象,多组件共用的数据放置在`store/index.js`中

```javascript
export default new Vuex.Store({
  //相当于data
  state: {
    num:0
  },
  //相当于methods,在这处理/修改状态
  mutations: {
    add(state){  //默认参数state
      state.num++
    }
  },
  //相当于异步方法
  actions: {
  },
  //模块
  modules: {
  }
})
```

### state

state在组件中的应用:将想要用到的全局state数据,放置在组件的**computed内部**使用,==**v-model的内容将其获取和设置分开**==来即可(用get).

```javascript
//在state中
mutations: {
    add(state){  //默认参数state
      state.num+=2
    },
    set(state,value){
      state.con = value
    }
  },
      
//在想要应用state的组件中
computed:{ 
    msg(){
      return this.$store.state.msg
    },
    con:{  //将获取和设置分开
        get(){
          return this.$store.state.con
        },
        set(value) {
          this.$store.commit('set',value)
        }
    },
}
```

凡是有state的地方都会有一个参数叫`rootState`,根节点状态(通过`context.rootState`暴露).对于模块内的getter,根节点状态会作为第三个参数暴露出来.拒不状态通过`context.state`暴露

#### mapState ==辅助函数==

当一个组件需要获取多个状态时,将这些状态都声明为计算机属性会有些重复和冗余.为了解决这个问题,我们可以使用**==辅助函数==帮助我们生成计算属性**,让你少按几次键.

```javascript
//引入mapState
import { mapState } from 'vuex'  //用来映射数据到computed里
let mapStateData = mapState(['msg','con'])
```

映射方式一共有三种:  ==均只能获取数据并不能设置,但是第三种方法可以有自己新添加的东西而不是只能照搬原有的mutations中的==.

第一种----

```javascript
computed:mapState(['msg','con']), 
    //第一种映射方式,想要哪个就写哪个
```

第二种----

```javascript
computed:mapState({   //第二种方式
  msg:'msg',
  con:() => this.state.con,
})
```

第三种----对象展开运算符

```javascript
computed: {  
    //第三种方式,不想所有东西都被原有的东西覆盖,要有自己能写的
   mapState:{
      reverse(){
        return this.color.splice('').reverse().join('')
      }, 
      ...mapState({    //...表示解构
        msg:'msg',
        con:() => this.state.con,
      }),  //这段要是不能运行,就放到外面声明试试看
   },
}
```

### getter

就像**计算属性**一样,getter的返回值会根据它的以来被缓存起来,且只有当它的以来发生了改变才会被重新计算.

getter接收state作为其第一个参数.

```javascript
xin(state){
  return function(val){   //如需传参,需要多写一层return
    return val + "123546" + this.state.age
  }
}
```

也可以通过方法访问.给getter返回一个函数来实现getter传参,在你对store里的数组进行查询时非常有用.  注意:getter在通过方法访问时,每次都会去调用而不会缓存结果.

#### mapGetters辅助函数

```javascript
 import { mapGetters,mapState } from 'vuex'
 let getterData = mapGetters(['raver',"xin"])
 let stateData = mapState(['msg',"age"])
 let actionData = mapAction(['sdg'])
 let mutationData = mapMutation(['add'])
 //辅助函数

computed: {
    ...getterData,  //直接解构
    ...stateData,
    mixi(){
        return this.$store.getters.mixi('12425423')
    }
},
```

### mutation

处理/修改状态.

mutation中的方法都是同步的,==必须是同步的==,没有异步方法,所以做不了ajax.

提交mutation是更改vuex的store中的状态的唯一方法.

每个mutation都有一个字符串的事件类型(type)和一个==回调函数(handler)==.**这个回调函数就是我们实际进行状态更改的地方**,并且它会接收state作为第一个参数.

向`store.commit`传入额外的参数,即==载荷(payload)==.在大多数情况下,载荷应该是一个==对象==,这样可以包含多个字段并且记录的mutation会更易读.

```javascript
mutations: {
  add(state){  //默认参数state
    state.num+=2
  },
  set(state,value){  //这个额外的参数叫载荷(payload)
    state.con = value
  }
},
```

提交的两种方式

```javascript
//第一种方式(常用)
this.$store.commit('add',{amount:10}) 
//调用mutation时一定要用this.$store.commit触发

//第二种方式,提交时直接试用包含type属性的对象,这种方式被称为对象风格的提交方式
this.$store.commit({
    type:'increat',  //名称
    amount:10  //参数
})
//当使用对象风格的提交方式时,整个对象都作为载荷传给mutation函数,因此handler保持不变
```

#### mutation需要遵守Vue的响应规则

一般state中的数据在最开始就已经写好,但是如果以后非要添加数据的话,就需要遵守一些响应式的规则,因为vuex的store中的状态是响应式的,当我们变更状态时,监视状态的Vue组件也会自动更新.

需要遵守:

1--最好提前在你的store中初始化好所有属性;

2--当需要在对象上添加新属性时你应该--->[不建议后手添加新属性]

- 使用`vue.set(obj , 'newProp' , 123)`或者
- 以新对象替换老对象,例如`state.obj = {...state.obj,newProp:123}`

#### 使用常量代替mutation的事件类型

这在各种flux中是很常见的模式,这样可以使linter之类的工具发挥作用,同时把这些常量放在单独文件中可以让你的代码和作者对整个App包含的mutation一目了然.多人合作项目中将会很有帮助.

### action

用来异步操作

## module

由于使用单一状态树,应用的所有状态会集中到一个比较大的对象.当应用变得非常复杂时,store对象就有可能变得非常臃肿.

为了解决这个问题,vuex允许我们==将store分割成模块(module)==.**每个模块拥有自己的state,mutation,action,getter,甚至是嵌套子模块**--**从上到下以同样的方式进行分割**.

直接在store文件夹下面新创建四个部分的单独文件,按照在`index.js`中的原格式写,然后直接在`index.js`顶部引入`import state from './state'`即可.

区分模块:store下新建模块的文件夹,写入`import default{ state:{}, getters:{}, mutations:{}, actions:{} }`,然后再index下引入.

```javascript
const moduleA = {
	state:{...},
	mutations:{...},
	getters:{...},
	action:{...},
}

const moduleC = {
	state:{...},
	mutations:{...},
	getters:{...},
}
           
const store = new Vue.Store({
    modules:{
        a:moduleA,
        c:moduleC,
   }
})
store.state.a  //moduleA状态
stoore.state.c  //moduleC状态
```

嵌套子模块的话,子模块中的写法应该`this.$store.getters['account/prof']`这样,像写地址一样要写上下级嵌套的意思.

# vant

vant是基于Vue2.0的一套轻量的,可靠的mobile组件库.是个插件啦~不能用在PC端

`npm i vant -S`安装

手动按需引入组件--推荐  import - from - 

`vue.use(Vant);`调用

`<van-button> --Vue的组件`  ----  `type="default" -- 组件的props - 属性`

```html
<van-button type="default">默认按钮</van-button>
```

vant中slot插槽的两种用法*(用的较多)

```javascript
//第一种
<van-cell title="支付" icon="refunt-o" is-link to="/about">
  <van-icon name="phone-o" slot="right-icon" />
</van-cell>

//第二种
<van-cell title="支付" icon="refunt-o" is-link to="/about">
  <template slot="right-icon">
    <van-icon name="phone-o" />
  </template>
</van-cell>
```

# 网易严选案例

步骤:

一个页面分为一个view组件

一个view组件分配一个路由

每个页面的步骤:

1-每个页面需要的数据先获取

2-再根据功能和内容将UI组件导入

3-将数据渲染到UI 组件

4-加上交互和跳转







