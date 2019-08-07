## 简介

之前在CSDN的时候出现了一个通知窗口，查了才知道HTML5新增了一个Notification的API。
Notification用于向用户显示通知信息，这个通知可以脱离浏览器，即使当你最小化浏览器后也可以给你一个通知（在桌面的右下角）。

## 权限

跟地理位置一样，Notification同样需要用户同意才能发送通知。

可以通过Notification.permission获取当前的授权状态，默认是"default"。

![image.png](https://upload-images.jianshu.io/upload_images/18646161-2d8402af9f80cc64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 状态

permission属性共有三个状态：

+ default:  默认，不知道用户的选择。
+ granted：用户运行发送通知。
+ denied: 用户拒绝发送通知。

当我们需要询问用户是否愿意接受通知的时候， 可以使用Notification.requestPermission()

       Notification.requestPermission().then((permission) => {
          //then的参数是用户的选择      
          if(permission === 'denied') {
                console.log('用户拒绝了')
           }
          else if(permission === 'granted') {
                console.log('允许发送通知')
          }
          else  
                console.log('用户没有进行选择')
      })

下面是调用 Notification.requestPermission() 后出现的
![image.png](https://upload-images.jianshu.io/upload_images/18646161-8bb7bc36bb7f7e70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 推送通知

Notification是一个函数，使用需要先实例化一个对象。
![image.png](https://upload-images.jianshu.io/upload_images/18646161-2ca5ec96029c4d7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，该构造函数有两个参数，`title`是通知的标题，`options（可选）`是一些设置项，以下为options的属性：

+ body：通知的内容。
+ tag：相当于通知的窗口的id，相同tag时会在同一个通知窗口。
+ icon：要在通知中显示的图标的URL。
+ image：要在通知中显示的图像的URL。
+ data：想要和通知关联的任务类型的数据（我也不知道干嘛的）。
+ requireInteraction：通知保持有效不自动关闭，默认为false。

  	  function notice() {
		  var notification = new Notification('你好，欢迎来到梦工厂', {
		       body: '订阅号',
		       tag: '2333',
		       requireInteraction: true,
		       icon: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1563792305638&di=c187c8625bfacda9c1a481623dea2956&imgtype=0&src=http%3A%2F%2Fs13.sinaimg.cn%2Fmw690%2F003uKqCuzy6L3jJ7bB29c'
			})
			
			//关闭通知
			notification.close();	
		}

![image.png](https://upload-images.jianshu.io/upload_images/18646161-212568afce4a385c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 兼容性

兼容性很差，IE完全不兼容。

![image.png](https://upload-images.jianshu.io/upload_images/18646161-4f5162013eb4bace.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
