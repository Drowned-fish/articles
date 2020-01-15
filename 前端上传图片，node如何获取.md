
## 前言

图片上传这个功能，已经是第二次遇到。以下是这次功能的总结。

## 前端怎么上传图片

`input`标签的`type`设置为**file**，即可上传文件，设置`accept`为**image/***，即可上传图片。

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input1.png)

不过`accept`属性有一个不足就是，当点击浏览按钮后，文件类型可以选择所有文件。

![image](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/input2.png)

给`input`添加`onchange`属性，当选择完文件可以在`onchange`的函数中获取选择的文件，因此我们在`onchange`中判断文件是不是图片类型，如果不是再给出提示。

![image](https:/raw.githubusercontent.com/Drowned-fish/markdown-images/master/input3.png)

在**handleChangeFile**中，先判断文件大小、文件类型，接着把文件信息存到`FormData`上传。

```javascript
    let data = new FormData()
    data.append('img', file) // append可以添加第三个参数表示上传的文件名, 可以防止文件名冲突
    // data.append('img', file, name)
```
不管是上传图片还是上传文件，都必须设置请求头

```javascript
    axios.post('xxx', data, {
        headers: {'content-type': 'multipart/form-data'} //上传文件必须设置content-type为multipart/form-data，
    }).then(_ => {
        console.log('上传图片成功')
    }).catch(e => {
        console.error(e)
    })    
```

## node获取图片

这里以`express`为例，网上大多数是使用中间件  `multer`，这里我使用的是`express-fileupload`。

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
```

**routes/index.js**代码如下

```javascript
    var express = require('express')
    var router = express.Router()
    
    router.post('/xxx', (req, res) => {
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

