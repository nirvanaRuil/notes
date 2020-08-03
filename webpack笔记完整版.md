# webpack初始化项目

此流程简单迅速,不会出错,后续案例中老师copy项目的时候,建议直接使用此套流程创建项目,

不要去像老师那样复制,复制问题很多,如果缺了那个loader或者那个rules,后面在``` npm i xx -D```

自己根据需要添加,活学活用;



## html main文件准备

安装指令  npm i xxx ==-D==  最后这个d 大小写是有区别的, 用大写,切记

***



在src下新建index.html main.js

`npm init -y`
用npm包管理工具把项目管理起来,这一步做完就会出现package.json文件

`npm i jquery -s` 
安装Jquery,出现node_modules文件 出现package-lock.json

html中写

```html
<body>
<ul>
    <li>this is the 1 li</li>
    <li>this is the 2 li</li>
    <li>this is the 3 li</li>
    <li>this is the 4 li</li>
    <li>this is the 5 li</li>
    <li>this is the 6 li</li>
    <li>this is the 7 li</li>
    <li>this is the 8 li</li>
    <li>this is the 9 li</li>
    <li>this is the 10 li</li>
</ul>
</body>
</html>
```

main.js写 用到了es6的语法

```js
import $ from 'jquery'

$(function () {
    $('li:odd').css('backgroundColor','lightblue')
    $('li:even').css('backgroundColor',function () {
        return '#CB7731'
    })
})
```

## webpack.config.js准备

在```webpack.config.js```中配置dev  ==这一步是为了把前面json中那一串dev的配置规范的写到配置文件中==

```js
const path = require('path')
module.exports = {
    entry: path.join(__dirname, './src/main.js'),
    output: {
        path: path.join(__dirname, './dist'),
        filename: 'bundle.js'
    },
    mode: 'development',
    devServer: { //这部分与下面的自动编译配置有关,此处一起写入
        open:true, //项目启动自动打开浏览器
        port:8880, //自定义端口号
        contentBase:'src',//定向到首页,即默认打开就是index.html,不设置这个打开还要手点src
        hot:true, //开启热部署,即局部刷新,这样每次编译系统开销更小
    }

}
```



与在package.json的dev中相比,这样要更规范一些,推荐采用这种方式

==视频中老师关于热部署的报错 webpack4.0之后已经没有这个问题了,也不需要再配置关于hot的plugins==

## 引入bundle.js路径准备

新建dist文件夹,项目输出根目录(不创建运行下面代码它也会自动生成dist文件夹)

命令行执行```webpack```

会生成一个bundle.js文件在dist文件夹里

引入到index.html中

```html
<script src="../dist/bundle.js"></script> 	
```

点idea右上角浏览器小图标,看效果是ok的

然后修改```main.js```中的颜色,控制台输入```webpack```回车,显示有编译,刷新浏览器,正常显示

==注意此时依旧是采用老的打开方式,url是如下的形式==

```http://localhost:63342/webaa/hello/index.html?_ijt=uqcorni5f8jueite2a3ef3kb1g```



### 关于webpack命令的一些说明

当命令行形式运行webpack或者//webpack-dev-server(这个dev-server下面会配置,此处只用webpack指令)

工具会发现我们没有提供要打包的目标文件(main.js)和结果文件(bundle.js),此时它会检查项目根目录中的配置文件webpack.config.js

并且读取这个文件,就拿到了配置对象,即我们填写的```module.exports```相关配置,然后根据webpack.config.js里面这个对象进行打包构建编译出```bundle.js``` 



如上这部分操作完毕一切ok之后,继续往下做将引用路径改为,这个修改是为下一步操作做准备,关掉浏览器

```html
<script src="/bundle.js"></script>
```





****

## package.json文件准备

此时每次都需要输入```webpack```回车,很麻烦,下面配置```webpack-dev-server```工具,实现自动打包编译

==这个工具依赖webpack,且要求项目中必须安装webpack==

```package.json```文件中

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server " //此处是与npm run dev绑定,运行时就会调用webpack-dev-server
  },
```



依赖添加为

```json
"dependencies": {
  "jquery": "^3.5.1"
},
"devDependencies": {
  "webpack": "^4.43.0",
  "webpack-cli": "^3.3.12",
  "webpack-dev-server": "^3.11.0"
}
```

执行```npm i``` 这个命令会安装```devDependencies```中添加的三个依赖

**这样做的好处是,我们不知道这三个的安装顺序,单个安装又会卡死,写好依赖信息,执行```npm i```让npm自动帮我们一次性装好这三个...**

安装完成后,执行```npm run dev```  

==项目启动成功==

此处这个工具就用到了我们在webpack.config.js中配置的```devServer```代码块,项目run起来直接打开就在index.html,到这里,就已经实现了自动编译,热部署,自定义端口号,定向到首页

修改main.js中的颜色,点一下控制台或者切换窗口,就会触发自动编译,浏览器也会自动刷新, 但是此时bundle.js用的并不是我们项目dist目录中的那个;

## 关于自动编译后bundle.js的说明

到这里html还是真实的我们项目结构中的html,但是```bundle.js```在页面中的加载的已经是电脑运行内存中的虚拟js文件了,这个虚拟的文件路径是:

```http://localhost:8880/bundle.js```  项目启动之后在地址栏输入这个url是可以直接看到这个文件内容的

我们项目结构中的那个```bundle.js```其实已经失效了,删掉是没任何影响的,所以才会页面没有任何效果,所以在前面才==修改html中的引用路径==,指向电脑运行内存中虚拟的那个文件.

==webpack-dev-server自动编译工具==帮我们编译生成的这个```bundle.js```并没有放到物理磁盘中,存在于电脑的运行内存,所以在项目中根本找不到这个文件,把原本的dist中的那个删掉也没影响

### 关于热部署

```hot```属性是热部署,是==局部更新,==不再整体编译,加上之后每次修改点控制台信息大体如下

```
  5e27bdae5430dd03ccac.hot-update.json   46 bytes          [emitted] [immutable] [hmr]
                              bundle.js    714 KiB    main  [emitted]                    main
main.5e27bdae5430dd03ccac.hot-update.js  942 bytes    main  [emitted] [immutable] [hmr]  main
Entrypoint main = bundle.js main.5e27bdae5430dd03ccac.hot-update.js

```

这个对js代码的修改意义不大,但是修改css的话,页面可以无刷新更新

## 关于-save-dev指令

–save ： dependencies 键下，发布后还需要依赖的模块，譬如像jQuery库或者Angular框架类似的，我们在开发完后后肯定还要依赖它们，否则就运行不了。

–save-dev ： devDependencies 键下，开发时的依赖比如安装 js的压缩包gulp-uglify 因为我们在发布后用不到它，而只是在我们开发才用到它。

```json
 "dependencies": {
    "vue": "^2.2.1"
  },
  "devDependencies": {
    "babel-core": "^6.0.0",
    "webpack": "^2.2.0",
  }
}```
```

# webpack Loader&Vue

## css less sass url loader

==idea右下角弹```npm install``` 点dont ask again== 别点install 否则会很卡

```cnpm i style-loader css-loader -D```安装css-loader

```cnpm i less-loader less -D```安装less-loader

```cnpm i sass-loader node-sass -D```安装sass-loader



```webpack.config.js```文件中配置

```js
 plugins:[

    ],
    module:{
        //配置loader
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use:['style-loader','css-loader','less-loader']},
            {test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
            {test:/\.(jpg|png|gif|bmp|jpeg)$/,loader:'url-loader?limit=35649&name=[hash:8]-[name].[ext]'},
         //limit给定的值是图片大小,单位字节,如果引用图片大于limit,则不会转为base64格式的字符串
    //name=[name].[ext] 让保持原来的名字不实用默认哈希名,ext是保持原本的后缀格式
            //8位哈希,解决同名图片覆盖问题
        ]
    }

```

写了相应的样式文件 ```main.js```中做引入

```js
console.log("ok")
import './index.css'
import './index.scss'
import 'bootstrap/dist/css/bootstrap.css' //这样写会自动去node_module中找,省略了
```

index.scss中url引入图片之后

```css
html,body{
  .box{
    width: 220px;
    height: 120px;
    background:url('./images/11.jpg');//注意这个要写./
    background-size: cover;
  }
}
```



```cnpm i url-loader file-loader -D```安装url loader

```cnpm i bootstrap@3 -D```  安装bootstrap 3



```
<span class="glyphicon glyphicon-king" aria-hidden="true"></span>
```

```
{test: /\.(ttf|eot|svg|woff|woff2)$/,loader: 'url-loader'} //bootstrap 字体文件
```

json==中不能写注释==

## babel loader 处理es6语法

main.js如下

```js
console.log("ok")
import './index.css'
import './index.scss'
import 'bootstrap/dist/css/bootstrap.css'
class Person {
    static info = { name:'zs',age:20}
//static 关键字定义静态属性,静态资源可以Person.info  这样通过类名访问,这种属性又叫做类变量
}
let p1 = new Person()
console.log(Person.info)
//webpack 默认只能处理一部分es6语法,别的要用第三方loader来处理 Babel
//第一套包 cnpm i babel-core babel-loader babel-plugin-transform-runtime -D
//第二套包 cnpm i babel-preset-env babel-preset-stage-0 -D
/*在webpack配置文件添加rules 排除默认的包 否则打包非常非常慢且没有必要
* {test:/\.js$/,use:'babel-loader',exclude:/node_modules/}
* 在根目录新建一个 .babelrc 的Babel配置文件,这个文件是Json格式 ,其中写的配置如下
    * {
    *   "presets":["env","stage-0"],
    *   "plugins":["transform-runtime"]
    * } */



/*class关键字是es6语法,实现面向对象编程*/
//以前的写法如下
//  function Animal(name) {
//      this.name = name
//
//  }
//
// Animal.info = 123
// console.log(Animal.info)
// 这是静态属性
//
// let a1 = new Animal('大黑狗')
// console.log(a1.name)
// 这是实例属性
```



==这里会有报错==解决如下

```npm i babel-loader@7 -D``` 装7版本,之后run    然后提示那个有问题就装那个

如果scss有问题就执行```cnpm i sass-loader node-sass -D``` 不要使用控制台报错建议的命令,那个下载很慢

## webpack中导入vue

```npm i vue -S```

```main.js```中写

```js
console.log('hello')

// import Vue from 'vue'
//这个导入的vue版本功能不全 是个 runtime-only的vue版本
/*回顾包的查找规则:
*  1.找node_modules文件夹,
*  2.根据包名找对应的vue文件夹
*  3.在vue文件夹中,找一个叫package.json的包配置文件
*  4.在package.json文件中,查找一个main属性, 指定了这个包被加载的时候,的入口文件
* 第一种解决办法就是去修改这个json里面的指向  */
//如下引用是第二种解决办法
import Vue from '../node_modules/vue/dist/vue.js'
let  vm = new Vue({
    el:'#app',
    data:{
        msg:'123'
    }

})
```

```Cannot find element: #app``` 

错误原因：把对应js放在了head标签里面，页面没有加载完成就进行渲染，导致找不到#app

把```bundle.js```的引用不要放在head标签里

//==第三种==解决办法 在```config```中配置  ==修改配置文件需要重新启动生效==

```js
const path = require('path')
module.exports = {
    entry: path.join(__dirname, './src/main.js'),
    output: {
        path: path.join(__dirname, './dist'),
        filename: 'bundle.js'
    },
    mode: 'development',
    devServer: { //这部分与下面的自动编译配置有关,此处一起写入
        open: true, //项目启动自动打开浏览器
        port: 8880, //自定义端口号
        contentBase: 'src',//定向到首页,即默认打开就是index.html,不设置这个打开还要手点src
        hot: true, //开启热部署,即局部刷新,这样每次编译系统开销更小
    },
    resolve:{
        alias:{ //修改vue导入的时候的路径
            "vue$":"vue/dist/vue.js"
        }
    }
}
```

## runtime only渲染组件

使用```import Vue from 'vue'```导入runtime only版的vue

新建一个```login.vue```文件

```html
<template>
    <div>
        <h1>这是登录组件,使用.vue文件定义出来的</h1>
    </div>
</template>

<script>
    export default {
        name: "login"
    }
</script>

<style scoped>

</style>
```

在```main.js```中



==render函数会替换掉整个div#app==

```js
console.log('hello')

import Vue from 'vue'
//这个导入的vue版本功能不全 是个 runtime-only的vue版本
/*回顾包的查找规则:
*  1.找node_modules文件夹,
*  2.根据包名找对应的vue文件夹
*  3.在vue文件夹中,找一个叫package.json的包配置文件
*  4.在package.json文件中,查找一个main属性, 指定了这个包被加载的时候,的入口文件 */

// import Vue from '../node_modules/vue/dist/vue.js'
// let login = {
//     template:"<h1>这是用一个组件</h1>"
// }
import login from "./login.vue";
//cnpm i vue-loader vue-template-compiler -D
let  vm = new Vue({
    el:'#app',
    data:{
        msg:'123'
    },
    render:function (createElement) {
        return createElement(login)
    }
    // components:{
    //     login
    // }

})
```

```html
cnpm i vue-loader vue-template-compiler -D
安装vue的loader
```

在配置文件中配置

```json
const VueLoaderPlugin = require('vue-loader/lib/plugin')
```

```json
module:{
    rules:[
        {test:/\.vue$/,use:'vue-loader'}
    ]
},
plugins:[
    new VueLoaderPlugin(),
]
```

run 一切正常

render函数可以简写为

```
render: c => c(login)
```

## webpack vue总结

安装vue的包

```cnpm i vue -S```

安装能解析.vue的loader

```cnpm i vue-loader vue-template-compiler -D```

main.js中导入vue模块

```import Vue from 'vue'```
定义一个.vue组件文件, 组件有三部分```template script style```

导入组件

```improt login from './login.vue' ```

创建vue实例vm

```js
let vm = new Vue({
	el:'#app',
	render:c=>c(login)
})
```

在html中创建```div#app```

## export default 和export

```js
// 这是node中向外暴露成员的形式;
// module.exports = { }
//
// 在ES6中,也采用这种规范的形式,规定了如何导入和导出模块
// import 模块名 from '模块标识符'
// export default { }
export  default {
    name:'zs',
    age: 20,
}
// 在node中使用 let 名称 = require('模块标识符')
// module.exports 和 exports来暴露成员
```

main.js中导入

**export default 暴露的成员可以用任何变量来接受,比如下面的m1**

```js
let info ={
    name:'zs',
    age: 20,
}
export default info
```

哪怕转个手,只要是export default 这个语法暴露的,都可以随便接收

```js
import m1 from './test.js'
console.log(m1)
```

==export default 只允许一次,等于是个默认对象,默认对象只能有一个==

***

export可以暴露多个成员

```js
export let title = '小星星'
export let content = 'fine'
```

```js
import m1,{title as tt,content} from './test.js'
console.log(m1)
console.log(tt,content)
```

要使用它的名字来接收  或者用as语法起个别名,但是还是要用到它原来的名字

==Vue文件把数据跟方法暴露出去,方便的解决了组件之间数据交互的问题==

## vue-router

```cnpm i vue-router -S```

main.js中

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
```

src下新建main,main里面新建两个.vue组件,account goodslist

![image-20200620020008284](https://raw.githubusercontent.com/affirmatives/Picbed/master/img/20200620020010.png)



mian.js添加 (注意下面的routes 没有r)

```js
import login from "./login.vue";
import account from "./main/account.vue";
import goodslist from "./main/goodslist.vue";
let router = new VueRouter({
    routes:[
        { path:'/account',component:account},
        { path: '/goodslist',component: goodslist}
    ]
})


let  vm = new Vue({
    el:'#app',
    data:{
        msg:'123'
    },
    render: c => c(login),
    router, //将路由对象挂载到vm上

})
```

因为render函数是将整个app标签渲染为login

所以我们只能把后面两个组件加到login.vue文件中, 不能加到index里

login中添加

```html
<template>
    <div>
        <h1>这是登录组件,使用.vue文件定义出来的==={{msg}}</h1>
        <router-link to="/account">Account</router-link>
        <router-link to="/goodslist">goodslist</router-link>
        <router-view></router-view>
    </div>
</template>
```

run   一切正常, 找不到app 或者找不到这个那个,就是字打错了,好好检查,这里很多打错字是不报错的

**注意**:

==render函数渲染login,只能放到el:指定的app这个元素中==

==后两个组件是通过路由匹配监听的,所以这两个玩意只能展示到router-view标签占的那个坑里去==

## children router

新建sub文件夹,里面新建log reg两个子组件

main里面添加

```js
import log from "./sub/log.vue";
import reg from "./sub/reg.vue";
let router = new VueRouter({
    routes:[
        { path:'/account',
          component:account,
            children:[
                {path:'log',component:log},
                {path:'reg',component:reg}//注意子组件path前不加斜杠,否则不显示内容
            ]
        },
        { path: '/goodslist',component: goodslist}
    ]
})
```

account.vue中添加

```html
<template>
    <div>
        <h1>this is account component</h1>
        <router-link to="/account/log">登录</router-link>
        <router-link to="/account/reg">注册</router-link>
        <router-view></router-view>
    </div>
</template>
```

run  一切正常

## style

在子组件log中添加



```html
<style scoped>
    div{
        color: red;
    }
</style>
```

```json
module:{
    rules:[
        {test:/\.css$/,use:['style-loader','css-loader']},
        {test:/\.vue$/,use:'vue-loader'}
    ]
},
```

配置文件中添加css的规则, 同时安装css的loader

```scoped```关键字控制样式是只对本组件生效,还是触发之后全局生效,类似与之前的阻止冒泡

***

以下在account组件中

```html
<style  lang="scss">
    /*普通的style标签只支持普通的样式,想弃用scss或者less 需要为style元数设置lang="" 属性*/
    body{
        div{
            font-style: italic;
        }
    }
</style>
```

在config中配置

```
module:{
    rules:[
        {test:/\.css$/,use:['style-loader','css-loader']},
        {test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
        {test:/\.vue$/,use:'vue-loader'}
    ]
},
```

```cnpm i sass-loader node-sass -D```安装sass-loader

才可以正常运行,此时全局字体都斜了

## router单独抽出

新建router.js文件,将原本main中的相关内容抽出

```js
import VueRouter from 'vue-router'
import account from "./main/account.vue";
import goodslist from "./main/goodslist.vue";
import log from "./sub/log.vue";
import reg from "./sub/reg.vue";
import Vue from "vue";
Vue.use(VueRouter)
let router = new VueRouter({
    routes:[
        { path:'/account',
            component:account,
            children:[
                {path:'log',component:log},
                {path:'reg',component:reg}
            ]
        },
        { path: '/goodslist',component: goodslist}
    ]
})
export default router
```

将main中原有的重复内容注释掉,添加引用

```js
import router from "./router.js";
```

这个router挂载到vm对象上

运行, 一切正常

## scoped

这个关键词的本质是,加上这个关键词,vue自动的会给这个组件添加一个class属性,从而实现只对这个组件生效;

这一点在运行起来的页面中检查元素可以看到



# webpack环境配置

https://blog.csdn.net/m0_47379359/article/details/105917257

以下流程参考上面这个链接的博客写的

## 安装node.js

https://nodejs.org/en/download/

下载node安装包,然后无脑下一步

之后按键盘上的win+R打开运行,输入cmd 回车,打开命令窗口

输入 ```node -v ```可以查看是否有node版本,有就安装成功了

## 安装npm

node中已经涵盖了npm

命令行输入```npm -v ```查看版本,有就ok

npm是一个自动下载包的工具,所以我们要设置以后包存储的路径

在存放项目的盘符比如H盘下新建一个文件夹,名字用英文,可以叫```nodenpm```

建好之后

命令窗口输入

```npm config ls```

查看npm原本默认的一些路径信息,ls是list的缩写

命令窗口输入(==注意此处路径盘符改成你自己新建文件夹的那个盘符路径==)

```npm config set prefix "H:\nodenpm\nodejs\npm_modules" ```回车
```npm config set cache "H:\nodenpm\nodejs\npm_cache" ```回车

再次命令窗口输入

```npm config ls```

查看是否修改成功

## 安装cnpm

npm默认会去国外的镜像去下载js包，在开发中通常我们使用国内镜像，因为国外下载资源慢，这里我们使用淘宝镜像下边我们来安装cnpm：输入命令，进行全局安装淘宝镜像。

命令窗口执行(-g就是全局安装的意思 此后的-g -D之类的命令要注意大小写也要与教程一致)

```npm install -g cnpm --registry=https://registry.npm.taobao.org```

安装完成之后命令窗口输入

```cnpm -v```

查看版本,是否安装成功

## 安装nrm

nrm就是个保存了下载地址的一个小插件,基本等价于一个存了5个下载地址的json文件

命令窗口输入

```cnpm install -g nrm```

装完之后

```nrm ls```

查看nrm默认仓库,是*号指向的那个,我们要改成国内的淘宝云端仓库路径,淘宝仓库里放了我们常用的几乎所有包

```nrm use taobao```

切换镜像 再次执行

```nrm ls```

查看是否修改成功

## 安装webpack

```cnpm install webpack -g```

全局安装,这个安装可能没什么卵用, 后面课程要用的,每次还是都要在新建的项目中安装,具体见本文开头的```一个不会出错的webpack流程```

# 安装vue-cli(两个版本)

```npm install -g vue-cli```   安装老版本的vue-cli 2.0  可以不用这个了, 直接用下面的新版



```cnpm i -g @vue/cli```  安装新版本的vue-cli 4.0



[Vuecil新版官网文档](https://cli.vuejs.org/zh/guide/)



https://www.cnblogs.com/murenziwei/p/10930810.html

全局安装,按理说此后都不需要再安装这个了, 这个是vue官方做的一个针对vue的webpack工具,

学完原生webpack之后要单独学习一下vue-cli再复习一下,基本上大量内容是重合的,也跟vue基础内容重合,

只是换了一种项目构建的方式,

所以后续再学习vue-cli,等于再复习了一遍webpack的工作原理和vue基础

# 用vue-cli2.0创建一个项目

## 创建项目

以下内容参照https://blog.csdn.net/weixin_43421060/article/details/95409010

打开idea,

creat new project

选择左侧列表中最下面的Empty Project,点击next

输入projectname,这个name就是你的项目名,最大的那个文件夹的名字

比如可以叫vuecli01

点finish,然后我们会有一个空项目,此处会弹出project setting窗口,先关闭这个窗口

点击idea 下面的Terminal 打开内置的idea命令窗口,在这里输入

```vue init webpack hello```   回车 (此处的hello就是你的module的名字,==记住这个名字,后面要用到==)

等待下载模板,然后会显示如下信息:

这些信息是vue cli向你确认你项目构建的信息,确认无误可以直接按回车,或者按y 再按回车,是一样的;

?project name(你刚输入的那个module名)  回车

? Project description (A Vue.js project) 回车

?Ahthor  作者信息 此处可以输入你的名字Lin 回车 (最终项目中会有个文件记录着这些信息)

  Runtime + Compiler: recommended for most users  

出现Runtime这条的时候,可以按键盘方向键上下选择, 此处就选择默认的这个 ,回车

? Install vue-router? (Y/n) 回车 这个是vue路由相关的包

? Use ESLint to lint your code? (Y/n)   按n 回车 这个暂时用不到

? Set up unit tests (Y/n)  n 回车  单元测试模块 不需要

? Setup e2e tests with Nightwatch? (Y/n) n 回车 同上

Yes ,use NPM  这里可以按键盘方向键上下选择,此处就选择默认的这个 ,回车

==Installing project dependencies== 

正在安装项目依赖,等,直到出现如下信息, 项目初始化完成

```
Project initialization finished!

To get started:

  cd hello
  npm run dev

```

此时点击idea setting按钮旁边的有三个蓝色小方格的文件夹图标(project structure) 

或者从左上角File中打开project structure也是一样的

在project structure窗口中选择左边的Modules

点击上面的➕加号, 选择new module 

选择java Enterprise > web application   next

module name 输入前面初始化的时候那个让你记住的名字  本例使用hello

输入hello点击 finish

可以看到页面中生成了一个项目结构

点击ok

到这里项目就创建完成了, 但是项目里的包还没有导入, 只是在package.json文件中有依赖信息而已;

要导入请看下面

***

## 启动项目

此时看idea内置Terminal,本例中显示如下

F:\workspace\base\vuecli01>

我们在vuecli01这个文件夹中,

执行```cd hello```回车

cd就是转跳你命令操作的文件夹,我们的module文件叫hello ,此时就进入了这个hello文件夹

F:\workspace\base\vuecli01\hello>

在左边项目接口中找package.json 双击打开,

可以看到里面有很多依赖信息  还有项目初始化的时候 Vue cli问你的那些信息

到这里你应该理解了这个文件就是记录着所有的信息, 就像你的购物车,它不是什么实体,只是个清单

```
"dependencies"
"devDependencies"
```

这个就是我们项目默认要用到的一些包名和版本号信息

在Terminal执行```npm i``` 回车 等待完成

这个命令将所有依赖的包全部导入我们的项目中的node_modules文件夹 i 是install的简写

是让npm 帮我们导包, 并不是安装npm ,  npm 在最开始装node的时候已经装好了,别理解错了

```
28 packages are looking for funding
  run `npm fund` for details
出现这个就是包导完了,这些破玩意都在淘宝仓库,因为我们之前的设置,所以才下载这么快
```

执行

```npm run dev``` 回车

可以看到项目在编译,

```
 DONE  Compiled successfully in 3228ms                                                                                           上午4:22:08

 I  Your application is running here: http://localhost:8080

```

出现这个,就是项目编译成功了,并且已经==启动==了,运行在8080端口,点击Terminal中的http://localhost:8080

就能看到vue的模板页面,  到这里 一个初始化的项目就启动了

## 关停项目

在Terminal窗口点一下, 按ctrl + c 

按一下会出现 终止批处理操作吗(Y/N)?   

如果没出来,就再按一下,  然后y 回车 就项目关闭了

此时再去浏览器刷新http://localhost:8080 页面, 会一直转圈,

之后显示连接失败

xx浏览器 无法建立到 localhost:8080 服务器的连接。

就是项目没启动,关闭了,这个8080端口自然是没有任何服务在运行, 你就访问不到任何东西;



要了解vue cli项目结构和基础概念,可以看下面这个链接的前四个视频,讲的很详细

但是里面部分内容涉及后端, 只关注vue cli相关的内容即可

后面六个视频   之后可以选择性的了解, 具体我也没看,不是很清楚里面讲了什么

https://www.bilibili.com/video/BV1oV411o71h



# Vue-cli 4.0创建一个项目

## 创建项目

```vue create xxx```  xxx就是项目名

```
  default (babel, eslint)
> Manually select features

```



选择自选模式, 按方向键上下选行, 按空格选中或者取消



```
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection)
>(*) Babel
 ( ) TypeScript
 ( ) Progressive Web App (PWA) Support
 (*) Router
 ( ) Vuex
 ( ) CSS Pre-processors
 ( ) Linter / Formatter
 ( ) Unit Testing
 ( ) E2E Testing

```

此处我们只选择Babel Router两项 回车

```
? Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n)

```

回车

```
? Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys)
> In dedicated config files




  In package.json

```

我选了第二个```package.json```  这个好像没什么影响

```? Save this as a preset for future projects? (y/N)```问是否把你刚才选的存为一个模板  n 回车

就开始自动创建项目了 出现如下内容表示项目创建好了

```
 $ cd xxx
 $ npm run serve


```

此时点击idea setting按钮旁边的有三个蓝色小方格的文件夹图标(project structure) 

或者从左上角File中打开project structure也是一样的

在project structure窗口中选择左边的Modules

点击上面的➕加号, 选择new module 

选择java Enterprise > 什么都不勾选,直接点 next

到输入modile name 的时候, 

第二行content root最右边,点打开根目录,选择我们刚创建的xxx 确定, 就发现上面的name自己改好了, finish

此时能看到已经把module添加到项目结构中了, 点击ok

## 创建vue.config.js文件

在我们的module xxx上面右键  new 一个js文件,文件名就叫```vue.config```

在这文件中输入如下内容 , 这个作用跟```webpack.config.js```文件一样

当然如果不配的话,就不能方便的自定义一些配置, 

不过vue已经内置了一套默认配置, 不单独配这个也可以正常运行

```
module.exports = {
    publicPath: './', 
    devServer: { //这部分与下面的自动编译配置有关,此处一起写入
        open: true, //项目启动自动打开浏览器
        port: 8880, //自定义端口号
        // contentBase: 'src',//定向到首页,即默认打开就是index.html,不设置这个打开还要手点src
        // hot: true, //开启热部署,即局部刷新,这样每次编译系统开销更小
    },
}
```

与这个文件配置相关可查阅

[vue-config-js](https://cli.vuejs.org/zh/config/#vue-config-js)



## 配置idea内置npm启动项目

idea 上面的工具栏(第二行)  有个绿色锤子  还有个绿色小三角标志, 

中间有个格子```edit configuration``` 点这个打开``Run/Debug configuration``页面

点左上角的加号➕ 选择```npm```

配置npm启动器的name (如果不同的module是车, 这个大概就是车钥匙) 

此处我们在name输入 ```myxxx```      xxx=module名(为了方便区分车钥匙)

```package.json```  选择我们刚创建的这个module```xxx\package.json```  

(启动的时候npm是根据这个json文件判断它要启动那一个项目)

```command``` 此处默认为``run  `` 我们就配置的是run指令  不用改

 `scripts`选择 ``serve``点ok 就配置好了 ,

我们就可以==点上面的绿色小三角启动项目==了, 

==点红色小方格停止运行项目==

前面的格子中如果我们配置了多个module的json, 就是有多个车钥匙,   启动的时候要在格子中选择对应的车钥匙

别启动错了,  一个module配一个npm车钥匙





# idea做webpack案例卡顿

如果卡死了,就结束任务重新打开项目

如果有点卡,可以关闭idea重新打开项目

不要长时间挂着run,否则会出现idea报 low memory 然后就开始卡卡卡卡

控制台ctrl+c 按一下  再按y 停掉项目,修改之后再执行npm run dev 看看效果ok了就再停掉项目,不要一直run

如果还不行 ,尝试点idea左上角file>Invalidate catch/restart 清理缓存

清理之后重新打开项目构建可能比通常打开要久一点