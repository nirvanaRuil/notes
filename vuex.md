# vuex

它相当于一个公共仓库,保存着所有组件都能用的公共数据.

vuex的运行过程:组件派发任务到actions，actions触发mutations中的方法，然后mutations来改变state中的数据，数据变更后响应推送给组件，组件重新渲染.

vuex保存和获取数据全流程:

**第一步**---state.js保存所有数据,以对象的方式导出

```javascript
 export default {
  pathName: '', // 路由
  currDbSource: {}, // 当前数据源
 };
```

**第二步**:mutations.js ---保存所有方法，用来改变state的数据

```javascript
 export const savePath = (state, pathName) => {
  state.pathName = pathName; // 保存当前菜单栏的路径
 };
 export const saveCurrDbSource = (state, currDbSource) => {  // 保存当前点击的数据源
  state.currDbSource = currDbSource;
 };
```

**第三步**:actions.js ---暴露给用户使用，借此触发mutations中的方法，保存数据（可执行异步操作） 这里有两种方式---第一种是通过 context上下文用来触发事件，一种是直接通过commit，为了保存数据，都需要加第二个参数payload，不然保存到vuex的数据就是空值

```javascript
 Export const savePath = ({ commit }, payload) => {
  commit('savePath', payload);
 };// 触发保存菜单栏的路径方法
 export const saveDbSource = (context, payload) => {
  context.commit('saveDbSource', payload);
 };// 触发获取当前点击的数据源方法
```

**第四步**:index.js---引入响应模块,暴露出store,供vue注册后全局使用

```javascript
 import state from './state';
	 import * as actions from './actions';
	 import * as mutations from './mutations';
```

***\*第五步\****:在main.js中引入index.js

```javascript
 import store from './store/index';
```

**第六步**:保存数据 这里也有2种保存数据的方法,第一种就是---直接操作方法,通过disptach派发给actions,让actions去触发; 第二种就是----通过在methods中添加映射关系，数组方式意味着我们可以在数组中写多个方法(这里数组中的每一个方法名是actions.js文件中所定义的方法名),然后在需要使用的地方直接this.方法名即可。当然,也可以直接绑定给html中的某个事件。 

```javascript
 import { mapActions } from 'vuex';
	 // 2种方式
	 this.$store.dispatch('saveDbSource', this.DbSource); // 方法一（dispatch）
	 methods: {  	 // 方法二（映射）
  		 ...mapActions(['saveDbSource'])
 	 }  // 1、通过methods方法添加映射关系 
 this.saveDbSource  // 2、使用
```

**第七步**:获取数据 通过计算属性，当数据发生改变，我们可以保证拿到的是响应过后的数据,也是数组形式,意味着我们可以拿到多组数据，这里数组中的数据也是state.js中定义的变量. 使用的时候,可以直接通过this.变量名拿到,我们可以把它赋给我们自定义的变量,也可以直接使用.

```javascript
mapState mapAction这种叫辅助函数,可以辅助获得多个值

 import { mapState } from 'vuex';
 computed: {
   ...mapState(['DbSource']),
 },
 watch: {
   DbSource(currVal) {
 // 监听mapState中的变量，当数据变化（有值、值改变等），
 // 保证能拿到完整的数据,不至于存在初始化没有数据的问题,然后可以赋给本组件data中的变量
    this.currVal = currVal;
   }
 }
```

值的注意的是，要避免和自己定义的其他变量,方法的名字冲突

 