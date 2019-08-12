
首先，`eval`是什么？MDN对`eval`的解释是：

> eval() 函数会将传入的字符串当做 JavaScript 代码进行执行。

如今JSON格式的数据流行于服务端与浏览器之间，有些人喜欢把服务器端传过来的数据在浏览器通过`eval`转换成可以直接使用的对象。或者拼接字符串，然后把得到的字符串扔进`eval`方法中执行。这也是`eval`唯一的优点，就是方便。

    eval('1+1') //2, 这应该是唯一的办法，如果用JSON.parse会报错
    eval("alert(2)");  //弹出2
 
当我在eval里面声明一个函数时，发生了什么？

    eval("function f() {console.log(2)}");

`eval`把字符串变成可执行代码(运行了一遍编译器)，然后执行。你以为完了吗？

上面代码执行完毕后，`window`上多出一个变量，就是f。

首先，`eval`没执行前，全局没有f函数：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-ab719612e2e47727.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行`eval`之后，`window`上多次一个f函数：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-6c9103b2799200ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也就是说，在eval使用新声明函数都会绑定到window上。而window上的属性如果没有手动`delete`或者设置`window.xxx = null`，这些变量会一直占据内存空间常驻内存直到页面被销毁才释放。

上面所说皆属于显示调用`eval`，下面来看看隐式调用`eval`的情况。

### 隐式调用eval

`Function`构造器与`eval`具有相同的字符串参数特性，应该避免使用它。

Function构造函数语法：

      var func = new Function(arg1, arg2, ..., functionBody);

例：

      var add = new Function('a', 'b', 'return a+b;');
      console.log( add(2, 3) );    // 5



第二种隐式调用是，当传递字符串参数给`setTimeout`和`setInterval`函数时，会像`eval`那样去处理。

     
      function f() {
	    console.log(1);
      }
      //推荐写法
      setTimeout(f, 0)
  
![image.png](https://upload-images.jianshu.io/upload_images/18646161-1f1b1da330076d9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      //隐式调用eval
      setTimeout("f()", 0)

![image.png](https://upload-images.jianshu.io/upload_images/18646161-9d700c4cab098814.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意，隐式调用需要自己执行。

### 缺点

+ 使用eval形式的代码可读性差（其实我觉得差不到哪去）。
+ 代码性能显著降低，需要先运行编译器编译字符串为js代码，然后在执行，如果js代码有误，eval也是会报错的。
+ 可能无意中定义了全局变量，占用内存空间。
+ 减弱了Web应用的安全性，因为eval赋予了将要执行的字符串太多的权限，可能会引起XSS，会不会引起看你对自己代码的把握。
