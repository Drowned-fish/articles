
## 背景

使用搜狗截图功能截图并复制到输入框，发现图片是base64类型。使用 **FormData** 传输图片到后端，查 *MDN* 发现 **FormData.append()** 对第二个参数类型有限制。
![图片暂时无法显示](https://raw.githubusercontent.com/Drowned-fish/markdown-images/master/formData.png)
因此决定把base64转为Blob。

## 什么是Blob

Blob 对象表示一个不可变、原始数据的类文件对象。。Blob是一个大文件，典型的Blob是一张图片或一个声音文件，由于它们的尺寸，必须使用特殊的方式来处理（例如：上传、下载或者存放到一个数据库）。

## base64转Blob

使用搜狗截图工具截图之后的base64是这样的：

**data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAN0AAACWCAYAAABaW421AAABt0lEQVR4nO3TMQEAMAyAsPo3vbpgRxMFPMwDUvM7AK4xHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EDMdxEwHMdNBzHQQMx3ETAcx00HMdBAzHcRMBzHTQcx0EFtMrhBF9pJRCAAAAABJRU5ErkJggg==**

在逗号前面包含图片的类型，需要截取逗号前部分并使用正则表达式获取mime。

```javascript
    let arr = url.split(',')
	let mime = arr[0].match(/:(.*);/)[1] 
	// arr[0].match(/:(.*);/)  =>  Array [ ":image/png;", "image/png" ]
```
逗号后面是base64编码字符串，window对象提供了一个用于解码base64的方法 **window.atob()**，解码后（如果用字符串转base64再用atob转回来还是字符串）是一串 Unicode 编码。

```javascript
    let bstr = window.atob(arr[1])
```
接着使用charCodeAt把每个 Unicode 编码存放到 Unit8Array 中。

```javascript
    let n = bstr.length,
        u8arr = new Uint8Array(n)
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n)
    }
```
最后使用 **Blob构造函数** 生成一个blob对象。

```javascript
    return new Blob([u8arr], { type: mime })
```
返回的Blob是这样的：

```javascript
    Blob { size: 404, type: "image/png" }
```
因为图片上传涉及改名称（防止命名冲突，以及携带一些个人信息）以及后端方便操作，所以需要把 blob 转为 file。

## Blob转file
 
 文件(file) 接口提供有关文件的信息，并允许网页中的 JavaScript 访问其内容。
 
 通过 `<input type="file"/>` 选择图片得到的file对象是这样的：
 
 ```javascript
    File { 
        name: "1693837573fb05f8.jpg", 
        lastModified: 1576769913928, 
        webkitRelativePath: "", 
        size: 18657, 
        type: "image/jpeg" 
    }
 ```
 对比File和Blob，发现File多出 **name**、**lastModified** 和 **webkitRelativePath** 三大属性，其中 **webkitRelativePath** 不需要。
 
 ```javascript
     // 接着上面的return , 多blob加入文件名和修改时间
     let nBlob = new Blob([u8arr], { type: mime })
     nBlob.lastModifiedDate = new Date()
     nBlob.name = fileName
     return nBlob
 ```
 ## 结语
 
 base64转File涉及到的内容点还是蛮多的，其中比较关键的是base64转为blob对象这一步，window对象提供的atob实在方便。其中也发现了自己对 Uint8Array 的操作和Unicode并不熟悉。
 
 
 
