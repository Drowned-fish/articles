# vue移除第三方库
Vue 上线移除第三方库
## 开发阶段
首页，因为有了vue-cli每次构建一个vue项目都只需要输入以下几行命令
	
```shell
	cd ...
	npm init
	vue init webpack yourProjectName 	
```
然后在项目开发过程中，我们可能需要用到各种第三方库，这时我们可能会直接通过npm安装
```shell
	npm install axios animate.css --save-dev
```
然后在main.js中引入并use，这在开发阶段是非常方便的。

## 生产阶段
那如果项目要上线呢？我们先打包看看

![1.png](https://raw.githubusercontent.com/Drowned-fish/MarkdownImages/master/1.png)

可以上看打包后的vendorjs文件有1.46M，后面还有个big标注文件过大。
### 移除依赖
1. 在config/index.js添加和修改

```javascript
    //添加一下代码，根据自己项目情况添加想要移除的第三方库
    externals: {
      'element-ui': 'ELEMENT',
      'vue': 'Vue',
      'axois': 'axios',
      'animate': 'animate',
      'vue-router': 'VueRouter',
      'vuex': 'Vuex',
      'highlight.js': 'highlight.js'
  }
   //修改build
   {
	assetsPublicPath: './'，
	productionSourceMap: false, //关闭代码映射
	productionGzip: true //开启GZIP需服务端支持
   }
```
2. 修改main.js

```javascript
	//以下注释掉的代码是本来有的代码，现在不再需要

	// import Vue from 'vue'
	import App from '../src/views/App'
	import router from './router'
	import store from './store/index'
	// import ELEMENT from 'element-ui'
	// import hljs from 'highlight.js'
	// import 'highlight.js/styles/googlecode.css'
	import commond from './assets/style/common.css'
	Vue.config.productionTip = false
	Vue.use(ELEMENT)
	Vue.use(axios)
	// Vue.use(animate)
	Vue.use(commond)
	Vue.prototype.$axios = axios
```
3. 修改store/index.js

```javascript
	//import Vue from 'vue';
	//import Vuex from 'vuex';
	Vue.use(Vuex);
	export default new Vuex.Store({
    		state:{
    		}
	})
```
4. 修改router/index.js

```javascript
	// import Vue from 'vue'
	//import Router from 'vue-router'
	import hello from '@/views/hello'
	Vue.use(VueRouter)
	const router = new VueRouter({
 		routes: [
		//这里该怎么样还是怎么样，部分路由我用了懒加载
		]
	})
	export default router;
```
5. 在index.html通过link和script标签引入第三方库

```javascript
	<link href="https://cdn.bootcss.com/element-ui/2.6.1/theme-chalk/index.css" rel="stylesheet">
    	<link href="https://cdn.bootcss.com/animate.css/3.7.0/animate.min.css" rel="stylesheet">
    	<link href="https://cdn.bootcss.com/highlight.js/9.15.6/styles/googlecode.min.css" rel="stylesheet">

    <script src="https://cdn.bootcss.com/vue/2.5.20/vue.min.js"></script>
    <script src="https://cdn.bootcss.com/vue-router/3.0.1/vue-router.min.js"></script>
    <script src="https://cdn.bootcss.com/element-ui/2.6.1/index.js"></script>
    <script src="https://cdn.bootcss.com/vuex/3.1.0/vuex.min.js"></script>
    <script src="https://cdn.bootcss.com/axios/0.18.0/axios.min.js"></script>
    <script src="https://cdn.bootcss.com/highlight.js/9.15.6/highlight.min.js"></script>
```
6. 打包

![2.png](https://raw.githubusercontent.com/Drowned-fish/MarkdownImages/master/3.png)

vendorjs明显降低了好多，从原来的1.46M到现在的805B

7. 可以上线了
