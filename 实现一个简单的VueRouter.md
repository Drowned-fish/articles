上次网易实习面试问到了VueRouter的原理，现在手写一个简单的Router（hash模式）。

首先，需要在新建文件index.html，然后引入vue.js文件。

      <script src="https://cdn.jsdelivr.net/npm/vue"></script>    

## 准备工作

定义三个组件：
    
      const Home = { template: '<div>这是home组件</div>' }	
      const mine = { template: '<div>这是个人信息组件</div>' }		
      const book = { template: '<div>这是书籍首页</div>' }
    
定义一个路由数组，每个元素包括path和component属性：

      const routes = [
				{
					path: '/',
					component: Home
				},
				{
					path: '/mine',
					component: mine
				},
				{
					path: '/book',
					component: book,
				}
	          ]
     
新建路由对象：
      
      const router = new Router(Vue, {routes});
     
       var vue = new Vue({
				el: '#app',
				router
			})

所以，上面的new Router()就是我们要实现的。

## 实现Router

根据上面的代码，可以看到Router是一个构造函数，接受两个参数。第一个参数是Vue，第二个是路由数组。这里我使用ES6的class来实现Router。

新建文件Router.js

      class Router {     
              constructor(vm, options) {
              this.map = {};
              this.app = new Vue({
                    data: { currentPath: '/' }
              })
              this.init(options);
              this.initComponent(vm);
          }
    }

这里为什么要把currentPath挂在vue实例上？这是因为每次改变hash之后，需要通过自定义组件router-view重新渲染，如果是直接定义在Router上，是不会重新渲染的。也就是说，如果改成`this.currentPath = '/'`, 改变hash页面不会发送变化。
  
在`init`方法中监听`hashchange`，这也是hash模式的核心。同时把path到component的映射存放到map中以便查找。

   
          init(options) {
              window.addEventListener('hashchange', this.onHashChange.bind(this));
            
              //map存放path到component的映射
              options.routes.forEach(item => { 
				  this.map[item.path] = item.component;
			  })
          }
          
          onHashChange() { this.app.currentPath = this.getHash(); }
          getHash() { return window.location.hash.slice(1) || '/'; }

`initComponent`方法实现了两个全局自定义组件 `router-link` 和 `router-view`。

          //定义router-link 和 router-view组件
          initComponent(vm) {
              Vue.component('router-link', {
                    props: {to: String},
                    template: '<a :href="to"><slot></slot></a>'
              });
              const _this = this;
              Vue.component('router-view', {
                  render(h) {
                      /*
                      *  如果currentPath不是定义在vue实例上，
                      *  render将不会执行。
                      */
                      var component = _this.map[_this.app.currentPath];
                      return h(component);
                  }
              });
          }
         
最后定义了`push`和`replace`方法，`push`通过`window.location.hash`实现，`replace`通过`window.location.replace()`实现。
 
          /*
          *  router.push() 这里只实现了接受字符串和含有query的对象形式
          */
          push() { this.setLocation('push', arguments[0]); }
         
         //同上
          replace() { this.setLocation('replace', arguments[0]); }
          
          setLocation(type, args) {
                //参数为字符串形式
                if(typeof args === 'string') {
                      this.setHash(type, args);
                }
                //参数为对象
                else if(typeof args === 'object') {
                    if(args.path) {
                         var url = args.path; 
                         if(args.query) {
                             url += '?';
                             //遍历query所有属性，拼接到url
                             for(let k in args.query) {
                                url += `${k}=${args.query[k]}&`
                             }
                            //把最后一个&截掉
                              url.splice(url.length-1, 1);
                          }
                          this.setHash(type, url);
                    }
               }
          }
           /*
          *  修改hash
          */
           setHash(type, url) {
               if(type === 'push') window.location.hash = url;
               else {
                  var location = window.location；
                  if(location.href.indexOf('#') !== -1) {
                       url = location.href.slice(0, location.href.indexOf('#') + 1) + url;
                   }
                     //这是在刚进入首页，url中还没有#
                   else {
                      url = location.href + '#' + url;
                   }
                   window.location.replace(url);
                    /*
                    *  执行replace可以修改hash但不会触发hash的变化，原因是url是由我们拼接而来
                    *  所以需要手动执行一次onHashChange()
                    */
                      this.onHashChange();
                 }		
             }
      }

由于在执行`replace()`修改hash时，是通过拼接的方式修改hash，并不会触发hashchange事件，所以最后手动执行了一个`onHashChange()`修改内部的`currentPath`。

##  效果
    
    <div id="app">
        <router-link to="#/">首页</router-link>
        <router-link to="#/mine">个人</router-link>
        <router-link to="#/book">书籍</router-link>
        <router-view></router-view>
    </div>
    
进入首页，此时url中没有hash，显示的是home组件。

![图片.png](https://upload-images.jianshu.io/upload_images/18646161-5af202dd17189d0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击个人进入mine组件。

  ![图片.png](https://upload-images.jianshu.io/upload_images/18646161-bb6f138b6eeefc0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击书籍进入book组件。

![图片.png](https://upload-images.jianshu.io/upload_images/18646161-6d3e2b231a75e12a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过push('/')修改hash。

![图片.png](https://upload-images.jianshu.io/upload_images/18646161-0d20f995f13c76ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过push({path: '/book'})修改hash。

![图片.png](https://upload-images.jianshu.io/upload_images/18646161-067227a40fb250da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 结尾

如果文章对你有所帮助，请给个star。
