
# http包的作用

1. 该包可以构建出一个前端与服务端交互的服务器

2. 该包是node内置的一个包，无需通过npm下载，可以直接使用


# 构建服务器的方式

1. 代码示范
```js
// 引入包
const http = require("http");

// 使用包中的方法，构建服务器
http.createServer( (request, response) => {
  
} ).listen(`端口`, `域名`);

// 域名可以不传递，使用默认的当前电脑的域名
```


# 服务器构建的具体分析

1. 使用`http.createServer`构建服务器时，传入了一个函数
   1) 传入的函数，就是每次请求来临时，执行的处理该请求的函数
   2) 传入的函数，在执行时，接收两个参数
      1) 第一个: 前端发送过来的请求
      2) 第二个: 服务端向前端发送的响应

2. 具体流程
   1) 服务端，调用回调函数，接收到`request`参数，为前端传向服务端的数据
      1) 然后服务端，对`request`参数进行解析，取出内部本次请求的路径
      2) 其实就是一个字符串，然后服务端，对该字符串进行分析
      3) 触发不同的函数，然后不同的函数利用`response`参数，向前端发送数据
      4) 这样，不同的请求，就会得到不同的数据
      5) 路径，就像一个普通的参数
      6) 服务端对该参数进行类似if的判断，然后不同的参数就会调用不同的方法
      7) redux仓库中的dispatch方法的原理，就是受该方式的影响
   2) 根据请求路径的分析，通常把它们分成两类
      1) 一种是静态请求，该请求获取的都是一些文件，比如js、css、html、图片等等
         1) 根据请求路径是否带静态文件的后缀名，判断当前请求是否为静态请求
         2) 如果是静态请求，直接读取文件数据，进行返回
      2) 一种是动态请求，该请求获取的一般都是一些数据
         1) 不是静态请求，就是动态请求
         2) 如果是动态请求，该请求获取的是数据，这就需要有专门的处理函数
         3) 根据对应的请求，调用专门的处理函数，然后利用`response`参数返回对应的数据
         4) 为了使用方便，通常把对应的方法放在`Map对象`中
            1) 键名为对应的请求路径，键值为对应的请求处理方法
         5) 这样调用起来非常方便
            1) 直接根据属性名，找到对应的方法，就可以调用
            2) 调用时需要传入`request`和`response`参数
      3) 为了动态请求方法，实现模块化，需要建立很多的文件
         1) 这些文件中，单独创建出对应的`Map对象`集合
         2) 单独存放，对应的请求处理函数
         3) 然后定义一个特殊的属性，把该Map对象进行抛出，通常为`path`属性
         4) 定义一个专门的文件，专门解析这些接口文件，然后引入对应文件抛出的`path属性`
            1) 该属性，就是各个请求的专门的处理函数
            2) 把这些单独的处理函数，合并到一个`Map集合`中
            3) 然后把集合好的`Map集合`进行抛出，这样主文件，引入方便



3. 分析静动态请求的示范代码
```js
const http = require("http");
const url = require("url");
const fs = require("fs");
// 引入动态接口对应的方法集合
const dynamicAggre = require("./creaDynamicMap");

http.createServer(
    (request, response) => {
        // 对请求进行分析处理
        fenXiRequest(request, response);
    } 
).listen("10086", "192.168.0.105");


/**
 * 对请求进行分析，不同的请求进行不同的处理
 * @param {*} request 
 * @param {*} response 
 */
function fenXiRequest(request, response) {
    // 得到请求的路径
    const pathName = url.parse( request.url ).pathname;
    // 得到请求的数据
    const pathData = url.parse( request.url, true).query;
    // 对路径进行分析，查看当前请求是静态请求还是动态请求
    const pathNameType = fenXiPathName(pathName);
    // 如果是静态路径，直接读取文件
    if(pathNameType) {
        try {
            const data = fs.readFileSync("./static" + pathName);
            response.end(data);
        }
        catch (err) {
            response.writeHead(302, { "location": "/404.html" });
            response.end();
        }
    } 
    // 如果是动态路径，调用对应的函数
    else {
        if( dynamicAggre.get( pathName.substr(1) ) ) {
            dynamicAggre.get( pathName.substr(1) )(request, response, pathName, pathData)
        }
        else {
            response.end( JSON.stringify({
                type: "接口不存在"
            }) )
        }
    }
}


/**
 * 分析请求是动态请求还是静态请求
 */
// 保存静态标识
const staticTypeAggre = [".html", ".js", ".css", ".jpg", ".png"];
function fenXiPathName(pathName) {
    for(let i = 0; i < staticTypeAggre.length; i ++) {
        if( pathName.indexOf(staticTypeAggre[i]) > -1 &&
            pathName.indexOf(staticTypeAggre[i]) === pathName.length - staticTypeAggre[i].length
        ) return true;
    }
    return false;
}
```



4. 合并动态请求处理函数存放位置的示范代码
```js
const fs = require("fs");
const map = new Map();
const webFileAggre = fs.readdirSync("./web");

function createNynamicAggre(arr, fileName) {
    for(let i = 0; i < arr.length; i ++) {
        // 路径合成
        const url = fileName + "/" + arr[i];
        // 没有点的话，表示还是个文件夹，需要继续向下读取处理
        if(arr[i].indexOf(`.`) === -1) {
            createNynamicAggre( fs.readdirSync(url), url );
        }
        // 是普通的文件，就要进行引入然后进行分析
        else {
            const fileData = require(url).path;
            // 进行接口的汇总
            if(fileData) {
                for(const prop of fileData) {
                    map.set( prop[0], prop[1] );
                }
            }
        }
        
    }
}
createNynamicAggre(webFileAggre, "./web");


module.exports = map;
```




# 开发服务器建立的几个基础文件夹以及作用
1. `Web`        存放动态请求处理文件
2. `Service`    存放业务处理文件
3. `Dao`        存放与数据库通信文件
4. `Page`       存放静态文件
5. `Log`        存放执行日志
6. `Filter`     存放检验文件





# 获取前端发送过来的数据的方法

1. 获取GET请求，发送过来的数据
   1) 需要借助一个包: `url`，该包也是node内置的一个包，无需下载
      1) 该包的作用，就是解析url路径
      2) GET请求发送的数据，就是拼接在url路径中
      3) 该包可以，把路径中的数据解析出来，并且转换成对应的对象格式
   2) 使用方法
   ```js
   const url = require("url");
   var parame = url.parse(request.url, true).query; 
   ```
   


2. 获取POST请求，发送过来的数据
   1) 该数据服务端接收到后，请求参数会触发一个data事件
      1) 执行事件函数，并且把接收到的数据作为参数传入
      2) 没有专门的包进行解析
   2) 代码示范
   ```js
   function getData(request, response) {
       // GET请求是无法触发该事件的，只有存在请求体，即POST数据，才会触发该事件
       request.on("data", (data) => {
           console.log(data.toString());  
       })
   }
   ```
   3) 接收到的`data`，是一个二进制的数据，使用toString方法可以转换成字符串格式
      1) 数据间的传输，把字符串转换成二进制格式进行传输
         1) 所以服务端接收到的是一个二进制的数据，需要使用`toString()`，在转换成字符串格式
         2) GET传参，使用`url`包，进行了转换
         3) POST请求，没有进行处理，所以接收到的是原始的二进制数据
            1) 需要手动调用`toString()`，进行转换
      2) 数据传输: **只能传输普通的字符串和JSON格式的字符串**
      3) 由于POST请求发送过来的数据无法进行解析，这样就造成了`http`包构建服务器的缺陷
      4) 比如前端向服务端发送文件数据，由于文件数据过大，只能使用POST请求
         1) 并且发送的文件数据，是借助form表单进行发送的
         2) 而form表单的数据，会自动拼接一个头和一个尾，然后在进行发送
         2) 文件数据拼接一个头和一个尾，然后在进行发送，中间才是文件数据
         3) 并且拼接的头和尾比较复杂，`http`包又不会对`POST`请求发送过来的数据进行解析
            1) 可以使用`toString()`，把二进制数据，转换成字符串，就可以看到拼接的头和尾
         4) 这样服务端，提取文件数据会非常复杂
   4) 所以，`http`协议搭建的服务器，只能接收普通的数据，很难实现文件的接收与发送
      1) 需要借助另外一个包`express`，搭建服务器



3. POST请求，可以使用GET的方式传参，GET请求不可以使用POST方式传参



# 服务端向前端发送数据的方式

1. 只能发送字符串或者字符串格式的JSON数据，或者是二进制数据
   1) 读取文件进行发送，就是直接把读取出的二进制数据进行发送
   2) 发送字符串，也是先转换成二进制数据，然后在进行发送，自动进行转换

2. 可以通过`response.write()`进行发送，也可以通过`response.end()`进行数据的发送

3. 代码示范
```js
// 标准发送
function a(request, response) => {
    // 发送状态码
    response.writeHead(200);
    // 发送数据
    response.write('{"a": "456"}');
    // 断开请求
    response.end();
}

// 简写发送
function b(request, response) => {
    // 断开请求时，顺便把数据也发送出去
    response.end('{"b": "789"}');
}
```





# 读写cookie

1. 并不是私有的方法，而是对request和response进行分析，从而操作cookie
   1) 只要函数可以接收到这两个参数，都可以使用下面的方法操作cookie


2. 读cookie
   1) 通过`request.headers.cookie`，可以获取到随着请求一起发过来的cookie中的数据
   2) 该属性，获取的是原始的cookie，即没有进行过分析的cookie，格式为一个字符串
      1) 比如: `a=1; b=2`，这表示两条cookie，每条cookie间使用`; `进行隔开
      2) 想要使用的话，需要手动进行分析，即拆分字符串


3. 写cookie
   1) 写cookie，其实是把对应的cookie信息放在响应头中，然后进行发送
      1) 前端在解析响应头时，会把表示cookie的数据提取出来，写进cookie中
   2) 一次写一个
      1) 方法: `response.writeHead(200, {"Set-Cookie": "a=1"})`
   3) 一次写多个
      1) 方法1: `response.writeHead(200, [["Set-Cookie", "a=1"], ["Set-Cookie", "b=2"]]);`
         1) 一个数组表示一条cookie信息
      2) 方法2: `res.setHeader('Set-Cookie', [ 'a=1', 'b=2']);`
         1) 数组中的每一项为一条cookie信息
         2) 该方式，可以向响应头中添加其它的数据，比如重定向
   4) 重定向时写cookie
      1) 方法: `response.writeHead(302, {"location": "main.html", "Set-Cookie": "a=1"})`





# 重定向

1. 通过设置响应头，进行重定向的设置

2. 重定向的流程
   1) 设置响应头: `response.writeHead(302, { 'location': '/a.html' })`
   2) 关闭请求: `response.end()`




# 请求路径的介绍
浏览器请求一张页面，如果页面中涉及到了其它的请求，比如src。

1. 相对路径
如果这些请求使用的是相对路径(./a/b或者a/b)。
实际请求的地址是当前的url，往前退一个/。然后拼接上/a/b。
即从当前文件的位置开始查询(向上或者向下)。

2. 根路径
如果直接使用/***进行路径的查询，则从本盘的根路径下开始查询，而不是本文件的根路径。

3. 模块化相对路径的用法
    (1) 读取文件使用的相对路径: 以入口文件为起点开始计算，并不是以该模块所处的位置开始计算

    (2) 引入接口使用的相对路径: 是以具体的模块所处的位置开始计算。



