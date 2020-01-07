
# 如何在express的路由中操作socket.io

## 背景

最近，遇到一个问题需要在`express`路由中操作`socket`，而网上的例子基本上是在主文件操作`socket`。

## 基本思路

给`router`添加一个`io`函数，接收参数`io` (`io`是引入的`socket.io`库)。

```javascript
    // routes/chat.js
    
    var express = require('express');
    var router = express.Router();
    
    var IO = null;
    router.io = function(io) {
        io.on('connection', (socket) => {
            socket.on('message', (data) => {
                // 操作data
            })  
        });
        
        IO = io;
        return io;
    }
    
    router.post('/xxx', (req, res) => {
        // 下面通过IO操作socket，具体socket操作请自行查看文档
        
        // 以下是例子
        IO.sockets.in(Id).emit('xxx', {
            ...
        })
    })
    
    module.exports = router;
```

咱把`router`导出，在`app.js`中导入，然后把`router.io`赋给`app.io`。

```javascript
    // app.js
    
    var express = require('express');
    var chatRouter = require('./routes/chat');
    
    var app = express();
    app.io = chatRouter.io; // 现在app.io是一个函数，可以接收socket.io
    ...
    app.use('/chat', chatRouter);
    
    module.exports = app; 
```

最后，在`bin/www`中导入`socket.io`并调用`app.io`。

```javascript
    // bin/www
    
    var app = require('../app');
    var http = require('http');
    ...
    var server = http.createServer(app);
    var io = require('socket.io')(server);
    app.io(io);
```
现在`chat.js`中的`router.io`成功接收到导入的`socket`。

可能有人会有疑惑，怎么不直接在`chat.js`中直接导入`socket.io`?

## socket.io基于http

用`express-generator`生成的`express`项目，目录结构如下

![express项目目录结构](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/express.png)

`http`模块是在`bin/www`下导入并生成`server`，而`socket.io`基于`http`(socket.io的参数就是server)，所以网上的示例大多数是写在该文件里。

```javascript
    // bin/www
    
    var app = require('../app');
    var http = require('http');

    var port = normalizePort('8080');
    app.set('port', port);
    
    var server = http.createServer(app);
    var io = require('socket.io')(server); // 注意这里的参数
    ...
```
