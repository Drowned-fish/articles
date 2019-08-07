在逛博客的时候经常会遇到好文章，特别是看到想保留下来的信息时，会用CTRL+C把它复制，就像这样：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-2c444b6e5f063d00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   
我复制了一篇文章中选择的部分，然后CTRL+V粘贴：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-fe5c2c1bb7181c11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，粘贴出来的内容在复制内容加了作者、链接等信息。

那么，这是怎么实现的呢？

## copy

> 当用户通过浏览器UI启动复制操作并响应允许的document.execCommand('copy')调用时触发`copy`事件。

copy事件可作用于任何可被选中或可编辑的元素，如body、div、input、textarea等。因此，可以通过监听函数来监听copy事件：

    document.addEventListener('copy', handler);

可以通过`window.getSelection(0).toString()`获取复制的内容。


    function handler(e) {
        console.log(window.getSelection(0).toString()); //输出复制内容
    }

![image.png](https://upload-images.jianshu.io/upload_images/18646161-513260a81e9a4d9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然，我们的目标不是输出复制的内容，而是修改内容。

##  clipboardData.setData()

> 调用`setData(format, data)`可以修改`ClipboardEvent.clipboardData` 事件的默认行为

调用setData()，就可以修改剪贴板的内容，当然必须把默认事件取消（必须的，如果没有取消复制出来的内容不会发生变化）。

      //修改handler，给复制的内容添加内容
      function handler(e) {
               e.preventDefault();
              var content = `${window.getSelection(0).toString()}\n作者：路一直在_走` ;
              if(e.clipboardData) {
                  e.clipboardData.setData('text/plain',  content);
              }
              else if(window.clipboardData){
                  return window.clipboardData.setData("text", content);
              }
      }

现在复制然后输出看看效果：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-96bd3f38a42031a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果忘记取消默认事件，结果是内容不会修改：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-148a79617f479d45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以下是来自MDN的介绍：

> 事件的默认行为与事件的来源和事件处理函数相关：
1.如果默认事件没有取消，就复制到选区（如果有选中内容）到剪切板；
2.如果取消了默认事件，但是调用了`setData()`方法：就复制`clipboardData`的内容到到剪切板；
3.如果取消了默认行为，而且没有调用`setData()`方法，就没有任何行为。

## 使用

既然知道了原理，现在我们在本地把掘金的添加内容去掉。打开掘金某个页面，在控制台输入以下代码：

    document.addEventListener('copy', function(e) {
        e.preventDefault(); 
        //原封不动的输出
        e.clipboardData.setData('text/plain', window.getSelection(0).toString()); 
    })

看看复制效果：

![image.png](https://upload-images.jianshu.io/upload_images/18646161-896d6f1119614a5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们已经完美的去掉掘金添加的自定义内容。

同样，打开百度文库（你会发现复制了之后没内容），输入上述代码，可以实现复制粘贴（注意输出的内容不会保留格式）。

