几天前，群上忽然有人提出一个问题，如下：
    
      <div class="parent">
          <div class="child"></div>
      </div>
      
      window.onload = function() {
			let parent = document.querySelector('.parent');
			let child = document.querySelector('.child');
			
			parent.addEventListener('click', function() {
				console.log(1);
			}, true);
			parent.addEventListener('click', function() {
				console.log(2);
			});
			
			child.addEventListener('click', function() {
				console.log(4);
			});
			child.addEventListener('click', function() {
				console.log(3);
			}, true);		
		}

考虑到事件流是先捕获后冒泡，我毫不犹豫地说是 `1342`。那对了吗？

看一下输出结果：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-18ba9188008604e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在我点一下中间的div：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-a4c1a9ca50c7257b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果是`1432`。当我们的事件绑定在非目标元素时（这里的parent），它是按照事件流的顺序来的，而目标元素的事件执行顺序确是按照代码的书写顺序来的。下面把代码顺序换一下：

    	
	      child.addEventListener('click', function() {
			  console.log(3);
          }, true);
	      child.addEventListener('click', function() {
			  console.log(4);
          });
			
现在再次点击child：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-961b730d41e3e228.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

顺序换过来了。

然而，如果要兼容iE8及以下，只能使用attachEvent来绑定事件，虽然也可以绑定多个事件，但是事件的执行顺序确是与代码顺序相反。
