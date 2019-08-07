JS提供了3个截取字符串的方法：`slice()`、`substr()`、`substring()`。它们有一个共同点就是不会改变原来的字符串，而是返回一个新的字符串。

## slice()
`slice`的用法跟数组的slice一样，可接受一个或两个参数:

    slice(start);
    slice(start, end+1);
   
### 一个参数

如果slice只有一个参数，会返回该参数开始(包括)之后的所有字符形成的字符串，如

    let str =  "0123456789";
    console.log(str.slice(1)); //"123456789"

参数可为负数，返回以`字符串的长度+参数` 开始之后的所有字符：
      
    let str =  "0123456789";
    console.log(str.slice(-1)); //"9"
    console.log(str.slice(-5)); //"56789"
    console.log(str.slice(-10)); //"0123456789"
    
    //参数的绝对值超出字符串的长度, 返回整个字符串
    console.log(str.slice(-12)); //"0123456789"

### 两个参数

第二个参数指定的是子字符串最后一个字符后面的位置。

    let str =  "0123456789";
    console.log(str.slice(0, 3)); //"012"
    console.log(str.slice(1, 3)); //"12"

如果第二个参数是负数，则转化为`字符串长度+该参数`：

    let str =  "0123456789";
    console.log(str.slice(3, -2)); //"34567"
    console.log(str.slice(6, -2)); //"67"

如果第一个参数大于第二个参数（包括负数转为正数），则返回空字符串:
    
    let str =  "0123456789";
    console.log(str.slice(3, 2) === "") ; //true
    console.log(str.slice(6, -5) === ""); //true
    console.log(str.slice(-3,-5) === ""); //true

## substring()

`substring`同样分一个参数和两个参数，第二个参数指定的是子字符串最后一个字符后面的位置，同`slice`一样。

### 一个参数

返回以该参数为起点（包括）之后的所有字符形成的字符串。
  
    let str =  "0123456789";
    console.log(str.substring(3)) ; //"3456789"
 
如果是负数，会返回整个字符串，等同于`substring(0)`
    
    let str =  "0123456789";
    console.log(str.substring(-3)) ; //"0123456789"
    console.log(str.substring(0)); //"0123456789"

如果是整数且超过给定字符串的长度，返回空字符串:

     let str =  "0123456789";
     console.log(str.substring(11) === "") ; //true

### 两个参数

两个都是正数且第一个小于第二个，同slice：
  
       let str =  "0123456789";
       console.log(str.substring(2, 4)) ; //"23"

有一个为负数，把该负数转为0并作为截取字符串的起点，另一个为终点+1：
      
       let str =  "0123456789";
       console.log(str.substring(-2, 4)); //"0123"
       console.log(str.substring(4, -2)); //"0123"

两个都是负数，两个都转为0，所以结果是空字符串：
    
         let str =  "0123456789";
         console.log(str.substring(-2, -4) === ""); //true

## substr()

substr()同样可接受一或两个参数，第二个参数指定返回的字符的个数。

### 一个参数

同slice相同，如果是正数则以该索引开始形成字符串；如果是负数，先转化为`字符串的长度+参数`，再截取：
      
       let str =  "0123456789";
       console.log(str.substr(1)); //"123456789"
       console.log(str.substr(-5)); //"56789"
       console.log(str.substr(-12)); //"0123456789"

### 两个参数

如果第二个参数为0或者负数，返回空字符串：
    
      let str =  "0123456789";
      console.log(str.substr(3, 0) === ""); //true
      console.log(str.substr(3, -2) === ""); //true
      console.log(str.substr(-5, -4) === ""); //true

如果第二个参数是正数，返回从第一个参数开始形成的字符串：
  
      let str =  "0123456789";
      console.log(str.substr(3, 3)); //"345"
      console.log(str.substr(-6, 2)); //"45"

