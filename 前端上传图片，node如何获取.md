
## 前言

图片上传这个功能，已经是第二次遇到。这里介绍两种经过验证的方法。

## 使用express-fileupload中间件

一直以来，我使用的都是express这个框架，而express-fileupload相对multer的写法也简单的多。

### 前端怎么上传图片

`input`标签的`type`设置为**file**，即可上传文件，设置`accept`为**image/***，即可上传图片。

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input1.png)

不过`accept`属性有一个不足就是，当点击浏览按钮后，文件类型可以选择所有文件。

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input2.png)

因此我们在`onchange`中判断文件是不是图片类型，如果不是再给出提示。给`input`添加`onchange`属性，当选择完文件可以在`onchange`的函数中获取选择的文件。

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input3.png)

在**handleChangeFile**中，先判断文件大小、文件类型，接着把文件信息存到`FormData`上传。

```javascript
    let data = new FormData()
    data.append('img', file) // append可以添加第三个参数表示上传的文件名, 可以防止文件名冲突
    // data.append('img', file, name)
```

### express获取图片

安装并引入`express-fileupload`

```javascript
    npm install --save express-fileupload 
    // 或者 yarn add express-fileupload
    
    // app.js
    
    var express = require('express')
    var fileUpload = require('express-fileupload')
    var indexRouter = require('./routes/indx')
    
    var app = express()
    
    app.use(express.static('static')) // 此处存图片
    app.use(fileUpload()) 
    app.use('/', indexRouter)
    ...
```
上面代码中，我在**static**文件夹下存放照片，如果没有该目录，node会报错

```javascript
    Error: ENOENT: no such file or directory
```

**routes/index.js**代码如下

```javascript
    var express = require('express')
    var router = express.Router()
    
    router.post('/upload', (req, res) => {
        let file = req.files.img // 这里的img对应上面append()的第一个参数'img'
        
        // 假设图片最后存储的文件名为fileName，记得带后缀
        file.mv('static/' + fileName, (err) => {
            if(err) {
                console.error(err)
            } else {
                // 成功
                ...
            }
        })
    })
    
    module.exports = router
```

### 接受多文件

关于怎么接受多文件[express-fileupload](https://github.com/richardgirges/express-fileupload/tree/master/example#multi-file-upload)文档中给出了详细的介绍。

接下来，是第二种方法，也是网上文章比较流行的`multer`获取上传的图片。

## multer获取图片

项目仍然采用`express`脚手架生成，安装`multer`中间件。

```javascript
  npm install --save multer 

    // app.js
    
    var express = require('express')
    var indexRouter = require('./routes/indx')
    
    var app = express()
    app.use('/', indexRouter)
    ...
```
在**app.js**中，我们并没有设置存放目录，而是由具体路由中**multer**来定义。

```javascript
    var express = require('express')
    var router = express.Router()
    
    var fs = require("fs")
    var multer = require('multer') //接收图片
    
    var upload = multer({
        dest: './static'
    })//定义图片上传的目录
    
    router.post('/upload', upload.single('img'), function(req, res) {
      console.log(req.file)
      fs.rename(req.file.path, "static/" + req.file.originalname, function(err) {
          if (err) {
              throw err
          }
          console.log('上传成功!')
      })
      res.send(JSON.stringify(...))
})
```
前端选择图片上传，后端成功获取

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input4.png)

## 参考资料

+ [multer文档](https://github.com/expressjs/multer/blob/master/doc/README-zh-cn.md)
+ [express-fileupload文档](https://github.com/richardgirges/express-fileupload/tree/master/example#multi-file-upload)
