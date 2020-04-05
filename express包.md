
# express包的作用

1. 该包也是创建服务器的一个包，并且比`http`包更能快速高效的搭建出一个服务端

2. 该包搭建出的服务器，可以解析POST请求发送过来的数据

3. 该包需要进行下载，下载方式: `npm install express -D`

4. 该包搭建出的服务端，可以自动判断静动态的请求，不用自己手动进行判断



# 搭建服务

1. `express`包，导出一个构造方法，通过该方法，可以创建出一个服务的对象
   1) 对象上有一些方法，通过这些方法，可以为创建的服务进行设置，搭建最终的服务

2. 具体的代码
```js
// 引入包
const express = require("express");
// 创建服务对象
const server = new express();
// 给服务指定端口和域名
server.listen(10086, "192.168.0.105");
```


3. 通过`express`包搭建的服务器，会自动区分是静态请求还是动态请求
   1) 但是静态请求和动态请求的处理方式还是需要借助创建的服务对象进行设置




# 静态文件的请求

1. 虽然`express`包搭建的服务器可以自动区分静态请求，但是具体的读取文件的地址是需要进行设置的

2. 示范代码如下
```js
// 搭建服务器
const express = require("express");
const server = new express();
server.listen(10086, "192.168.0.105");

// 指定静态请求，文件的加载根路径
server.use( express.static("./a") );

// 比如: 前端的请求为/demo/index.html，表示前端需要获取到该html文件
// 该服务器就会读取路径为，./a/demo/index.html的文件
// 相当于，把设置的路径，拼接上前端传来的路径，作为文件的读取路径
```

3. `use`函数，指定静态文件的读取根路径外，还有一个功能
   1) **当前端访问的是根路径时，会自动加载指定根路径下的`index.html`文件**
   2) 相当于: 访问`/`，得到的数据为`./a/index.html`文件的数据






# 动态数据的请求


1. 虽然`express`包搭建的服务器可以自动区分动态请求
   1) 但是具体的动态请求的处理函数，还是需要进行自定义



2. 示范代码如下
```js
// 搭建服务器
const express = require("express");
const server = new express();
server.listen(10086, "192.168.0.105");

// 指定某个动态请求，执行的处理函数，只有GET类型的请求才能触发对应的请求处理函数
server.get( "/a", function ( request, response) {
    response.end();
} );

// 指定某个动态请求，执行的处理函数，只有POST类型的请求才能触发对应的请求处理函数
server.post( "/a", function ( request, response) {
    response.end();
} );

// 当动态请求的路径为，/a时，就会执行传入的处理该请求的函数
// 并且get和post定义的接口没有任何冲突，即使接口名相同
// get触发get定义的，post触发post定义的，它们间相互没有任何影响
```



3. 也可以采用`http`包的设计模式，模块开发，进行分析，把所有的请求处理函数放在一个集合中
   1) 然后遍历该集合，把内部的每一个方法放入`get`或者`post`中，进行传入










# 请求的拦截

1. 利用的是`get`和`post`设置请求的处理函数，这两个函数的特点，实现请求的拦截
   1) 这两个函数，都可以对某个请求，设置多个处理函数
   2) 比如:
   ```js
   // 搭建服务器
   const express = require("express");
   const server = new express();
   server.listen(10086, "192.168.0.105");

   server.get( "/a/b", function ( request, response, next) {
       next();
   } );
   server.get( "/a/b", function ( request, response, next) {
       response.end();
   } );

   // 这两个都能用，但是后面触发的条件，与前一个是否执行next有关
   // 只有next执行，后面定义的处理函数才会触发，不执行就不会触发
   // 这样就可以利用next()，调用的时机，实现请求拦截的效果
   ```
   3) `get`和`post`设置的对应的请求路径，还可以带有`*`
      1) 比如: `/a/*`，表示，只要是`/a/?`的请求，都可以触发本次定义的处理请求的函数
      2) 即: `/a/b`可以触发，`/a/c`或者`/a/d`都可以触发，只要是`/a/?`的格式都可以触发


2. 具体拦截的示范代码
```js
// 搭建服务器
const express = require("express");
const server = new express();
server.listen(10086, "192.168.0.105");

// 拦截/c
server.get( "/c", function ( request, response, next) {
    if(!***) {
        // 不满足进行重定向，或者直接返回数据
        // 直接返回数据
        response.writeHead(200);
        response.write('{"a": "123}');
        response.end();
    }
    else {
        // 满足的话，才继续执行后面的处理函数进行处理
        // 实现了请求的拦截
        next();
    }
} );
server.get( "/c", function ( request, response, next) {
    response.end();
} );


// 拦截/a/?，只要是该格式的请求路径，都会触发传入的回调函数，进行判断拦截
// 然后在执行后面具体的路径的处理函数
server.get( "/a/*", function ( request, response, next) {
    if(!***) {
        // 不满足进行重定向，或者直接返回数据
        // 重定向，不满足重定向到/loader.html
        request.redirect("/loader.html");
    }
    else {
        // 满足的话，才继续执行后面的处理函数进行处理
        // 实现了请求的拦截
        next();
    }
} );
server.get( "/a/b", function ( request, response, next) {
    response.end();
} );
```


3. 注意
   1) 一定要先定义拦截的处理函数，在定义具体请求的处理函数
   2) 原因: 后面的受前一个的控制
   3) **无法拦截静态请求**





# 读写cookie

1. 可以使用http包中处理cookie的方法，也可以使用express包提供的处理cookie的方法
   1) express包操作cookie更加方便
   2) http包，操作cookie，其实操作的是response和request这两个属性
   3) express包中，处理请求的方法，也会接受到这两个参数
      1) 所以可以使用http包中处理cookie的方式


2. 下面是express包提供的操作cookie的方式

3. 读cookie
   1) 需要借助一个`cookie-parser`包的辅助，该包需要手动下载
   2) 需要借助`use`，启用该包，进行`cookie`的解析
   3) 每一个请求，都会进行`cookie`的解析，解析为一个对象格式
      1) 通过`request.cookies`可以获取到解析出的`cookie`对象
   4) 具体的示范代码
   ```js
   const express = require("express");
   // 引入解析cookie的包
   const cookie = require("cookie-parser");

   const server = new express();
   server.listen(10086, "192.168.0.105");
   server.use( express.static("./a") );
   // 启用解析cookie的包，每一个请求，都会被该包进行cookie的解析，解析为一个对象
   server.use( cookie() );


   server.get("/a/b", function a(request, response, next) {
       // 查看解析好的cookie对象
       console.log(request.cookies);
       response.end();
   } )
   ```
   5)  这样就可以利用`cookie`，配和请求的拦截，完成一些拦截和放行的功能






4. 写cookie
   1) 直接通过`response.cookie("name", "123")`
   2) 这样，就向浏览器种入了一个`name=123`的`cookie`
   3) cookie方法，是express包，往response上添加的一个方法，不是原生的
      1) 即http包中的处理请求的方法，无法通过该函数往cookie中添加数据
   4) 该方法可以调用多次，表示一次向cookie中写入多个数据





# 文件的上传和下载

1. 文件的上传
   1) 需要借助`multer`的包，对前端发送过来的文件数据进行解析，该包需要进行下载
      1) 去除首尾，获取中间文件的数据
      2) 使用该包，会自动把数据解析完成，然后存放到指定的位置
      3) 对应的文件数据，是观察不到的，即获取不到解析出的文件数据
   2) 使用示范代码
   ```js
   // 引入解析文件数据的包
   const multer = require("multer");
   // 指定解析完文件数据后，生成的对应的文件存放的位置
   // 相对于根路径
   upload = multer({dest: "./file/"})

   // 发送文件是post请求，所以要使用post方法，加入请求的处理函数
   server.post("/a", upload.single("myfile"), function (request, response) {
       // 获取根据文件数据，创建的文件的文件名
       // 使用的并不是发送文件的文件名，为了避免不同的人传入的文件名相同
       // 自动生成一个新的名称
       const fileName = request.file.originalname;
       // 获取根据文件数据，创建的文件的大小，单位是字节
       const size = request.size;
       // 获取根据文件数据，创建的文件的存放位置，即./file/文件名，./file是上方指定的
       const path = request.path;
       // 获取form表单中的其它数据
       // 文件数据就是放在form表单中，进行传输的
       // from表单除了可以传输文件数据，还可以传输其它的数据，要想获取到其它的数据
       // 需要使用下面的方法，进行获取
       const a = request.body.a;
   } )


   /**
   * upload.single("myfile")会自动解析form表单数据中对应的文件数据
   * 然后直接生成一个文件，存放到指定的位置中去
   * single中的参数"myfile"，就是指定文件数据的，即名称
   * form表单在发送数据的时候，会定义一个名称
   * 
   * 比如(文件上传的代码): 
   */
   // 构建form
   const res = new FormData();

   // myfile就是定义的本条数据的名称
   // 服务端根据这个名称就会明白，该数据是一个文件数据，就会生成对应的文件
   res.append("myfile", document.getElementById("a").files[0]);

   // single没有指定a，就会认为该数据是普通的数据，不会创建文件
   // 可以通过request.body.a，进行获取
   res.append("a", 123);

   // form可以传递很多数据，不一定要传文件数据，普通的数据也是可以传递的


   // 构建完成后，res就是要通过POST请求，向服务端发送的数据，发送代码如下
   await fetch("/a", { 
       method: "POST",
       // 创建的form数据 
       body: res
   });
   ```
   3) 文件上传，前端具体的操作，在Es6的fetch的使用方法中进行了介绍
   4) 上传文件时，如果想要上传其它的数据，有两种方法
      1) 放在url上，即GET请求方式的传参格式
         1) 虽然请求类型为POST，但是服务端也可以使用`url包`，解析出路径中传入的数据
         2) `url包`提取数据，就是对路径进行拆分，提取中路径中的数据
      2) 放在form表单中，进行传输


2. 文件的下载
   1) 使用的是a标签的下载功能
      1) a标签，可以发送请求，然后服务端接收到对应的请求，使用`fs`把对应的文件数据读取出来
      2) 服务端把读取出的数据，返回给前端，这就是一个文件二进制数据流
      3) 不用过多的处理，直接读取文件进行返回
      4) 前端接收到对应的文件数据流，就会创建对应的文件，实现了文件的下载功能
   2) a标签的下载功能: `<a href="?" download="a">点击下载文件</a>`
      1) 只要该a标签被点击，就会发送请求，请求路径为href指定的路径
      2) 然后就会把服务端返回的数据，创建成文件，文件名为download设置的名称
      3) 如果服务器返回的是对应的读取出的文件数据，前端根据数据创建出对应的文件
         1) 如果返回其他的数据，也会创建出文件，但是文件数据会发送问题
      4) 读取文件数据，传输文件数据，根据文件数据创建文件，相当于进行了复制粘贴的操作




# 对动态路径的处理问题

1. express，对于动态请求的处理方式为，先判断有没有对应的静态资源文件
   1) 即对应路径下的index.html文件，如果有的话，直接读取返回，不在执行动态请求的处理函数
      1) 如果没有对应的静态文件，才会触发动态请求的处理函数
   2) 比如: 根路径`/`，先判断有没有静态资源
      1) 路径为: `./指定的静态文件根路径/index.html`
      2) 有的话，直接读取返回，没有的话，才会执行动态请求`/`的处理函数
   3) 比如: 根路径`/a/b`，先判断有没有静态资源
      1) 路径为: `./指定的静态文件根路径/a/b/index.html`
      2) 有的话，直接读取返回，没有的话，才会执行动态请求`/a/b`的处理函数


2. 上面的情况只针对`get`类型的请求，`post`请求不会先查看是否有静态资源文件
   1) `post`类型的请求，会直接执行对应的动态请求的处理函数





# 重定向

1. 可以使用设置响应头的方式，进行重定向，在`http`包中进行了介绍

2. 可以使用`express`包向`response`上添加的重定向方法
   1) 方式: `response.redirect('/a.html')`，其它的不用操作
   2) 底层，也是设置响应头，然后关闭请求，所以也不需要手动关闭
   3) `http`包构建的服务端，没有该方法