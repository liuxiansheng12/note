
# webpack使用前的准备工作

1. 进行初始化
   1) 方式: `npm init -y`
   2) 注意: 初始化文件有一个name属性，是根据项目文件夹的名称命名的。如果与npm下载的插件同名的话，则本次安装就会终止，所以项目的命名(初始化文件的name属性)要注意

2. 下载webpack使用到的两个包
   1) 全局下载方式: `npm install webpack webpack-cli -g`
   2) 局部下载方式: `npm install webpack webpack-cli -D`
   3) webpack包是核心包，webpack-cli包是辅助包，通过该包，可以使用核心包中的API接口。
   4) 扩展: 在webpack3中，webpack-cli是不需要下载的，下载webpack会自动下载下来webpack-cli，但是webpack升级后就把webpack-cli给分离出去了。


3. 建立配置文件
   1) 默认配置文件`webpack.config.js`
   2) 可以用其它的js文件作为配置文件



# webpack执行打包指令的操作
 
1. webpack
使用默认配置文件和全局安装的webpack工具进行打包


2. npx webpack
使用局部安装的webpack工具，使用默认配置文件进行打包


3. npx webpack --config **.js 或者 webpack --config **.js
使用指定的配置文件进行打包


4. 在初始化文件中写上
```json
scripts: {
    webpack: webpack --progress --colors
}
```
在命令行中写入`npm run webpack`可以进行打包，webpack名进行自定义。
该方法默认使用局部webpack进行打包。


5.  在初始化文件中写入
```json
scripts: {
    run: webpack
}
```
在命令行输入`npm run`进行打包，使用全局webpack工具进行打包。
并且也可以配置环境: `run: webpack --mode production`


6. 按照需求多环境打包
使用局部webpack进行打包，按下需求调用 `npm run pro / npm run deve`
```json
scripts: {
    pro: webpack --progress --colors --mode production,
    deve: webpack --progress --color --mode development
}
```




# 局部插件的下载方式
`npm install jquery -D`
`npm install jquery --save-dev`

这两种方法下载下来的插件都是局部插件，区别在初始化文件中，插件的信息存储位置不同。



# webpack环境的配置
1. 在配置文件中通过mode属性进行配置，属性值有两个
   1) production: 生产环境
   2) development: 开发环境


2. 在命令行中进行配置，会造成配置文件中的配置环境失效(产生了覆盖)
   1) npx webpack --mode production: 生产环境
   2) npx webpack --mode development: 开发环境



# path包的作用

1. path是webpack的内置包，在下载webpack的时候，会自动下载该包

2. 作用
   1) 该包默认导出一个对象，对象中提供了很多方法，并且提供的方法都是对地址进行操作
   2) 比如: resolve方法。作用为合并路径
      1) 比如: `path.resolve("a", "b", "c")` 得到的是 `"项目文件根路径/a/b/c/d"`
         1) 文件根路径: 启动node的文件路径，与程序运行的路径无关。
      2) 注意: 传入的路径中不能写`/`，如果写了，前面传入的路径不在进行拼接，
         1) 比如: `path.resolve("d", "/f", "/a", "c")` 得到的是 `"盘路径/a/c"` 


# __dirname的作用

1. 在node环境下运行，得到的是**运行该程序的文件所处的位置，不是启动node的路径**。

2. 该值可以与path中的resolve方法配合使用。 




# webpack模块化分析

1. webpack支持commonjs模块化开发和Es6模块化开发，甚至还可以混合使用
   1) 在打包的过程中分析依赖，两个引入依赖的方式都替换成一个自定义的函数
   2) 抛出数据也进行替换
   3) 所以webpack可以兼容两种模块化开发(分析完替换成同一个)

2. 模块抛出
   1) `module.exports = { a: 123 }` 不进行替换。
   2) `export default 123`  替换为 `module.exports.default = 123`
   3) `export var a = 123` 替换为 `module.exports.a = 123`
   4) 替换完，都变成了给module.exports对象赋值，该对象外界传入
   5) 最终，抛出一个对象

3. 模块引入
   1) `require(?)` 替换为 `自定义(路径)`
   2) `import a from ?` 替换为 `var a = 自定义(路径).default`  取出Es6的默认导出值
   3) `import { a } from ?` 替换为 `var {a} = 自定义(路径)`


4. webpack打包完成后，只有一个文件，不存在模块的引入，所以把引入方法进行了替换。





# 入口

1. 通过entry属性进行设置，属性值是对象格式。
   1) 如果设置的是`entry: "./index.js"`，在运行时自动转换成`entry: { main: "./index.js"}`
   2) 可以配置多个入口
      1) 配置多个入口，必须写成对象格式
      2) 配置单个入口，可以写成属性值直接是路径字符串，自动转换成对象格式
   3) 每个入口，可以直接是路径字符串，也可以为一个数组，数组中的每一项为一个路径字符串
   4) 每个路径，表示一个chunk，即分析文件依赖的入口函数。
      1) 入口通过数组的方式进行多个入口分析，分析出的代码最终会打包到同一个文件中。


2. 如果没有配置该属性，默认为`entry: "./src/index.js"`


# 出口

1. 通过属性output进行设置，属性值是一个对象。

2. output常用属性
   1) `path属性`: 设置打包后文件输出的路径。
      1) 路径必须要写全，不能写相对路径。
      2) 可以使用__dirname动态设置，
      3) 规范点，借助path中的resolve方法，利用__disname或者使用默认根路径动态设置
      4) 如果设置的路径没有找到对应的文件夹，会自动进行创建
         1) 比如: `path: __dirname + "/dist/d"`
   2) `filename属性`: 设置打包后的代码输出到那个文件中。
      1) 一个入口文件对应一个出口文件。
      2) 该属性的属性值，是一个字符串。
         1) 如果想要实现多入口打包，需要借助`[name]`
            1) 如果还是直接写指定的文件名，多入口打包出的出口文件名就会相同，打包报错。
            2) 多入口对应的属性名(entry属性)会自动替换掉`[name]`，这样打包出的文件名就会不同。
            3) `[name]`字符，可以写在任意的地方。
            4) 比如: `filename: ab[name].c.js`。
         2) 如果赋值字符串存在/，会先创建文件夹，然后在创建对应的文件，保存打包代码。
         3) `[hash]`关键字
            1) 作用产生一个随机的hash字符串，用来作为打包输出文件名。
            2) 浏览器在申请代码时，会进行缓存，如果多次申请同一个文件，会直接从缓存中读取。如果此时代码进行了修改重新进行了打包，由于文件名相同，不进行重新申请，使用的还是之前的代码。
            3) 如果借助hash，当代码发生变化，hash值就会变化，导致打包后的文件名与之前的不同，再次申请时重新申请。当代码没有发生变化，重新打包，hash不变。重新申请，从缓存中取代码
            4) 默认的hash值比较长，可以使用`[hash:5]`的方式进行截取。
         4) `[chunkhash]`关键字
            1) 作用: 与hash的作用相同。不同点: 
               1) hash: 当一个入口文件代码发生变化，所有的出口文件hash都重新生成
               2) chunkhash: 当某个入口文件代码发生变化，只有对应出口文件的hash重新生成，其它的不变。
            2) 可以通过[chunkhash:5]进行截取。
            3) 注意: chunkhash不能与热更新共存
         5) `[id]`关键字(不建议使用)
            1) 开发环境下，打包后的作用相当于`[name]`关键字。
            2) 生产环境下，打包后的替换字符按照入口文件的引入顺序，替换成数字(0、1、··· ···)
         6) output中hash使用的是webpack打包时产生的hash，所有js出口文件相同。chunkhash是重新生成的，每个文件都不同。


# * webpack的编译过程(工作原理)

1. 打包过程
   1) 读取文件(从第一个入口文件开始读取)
      1) webpack打包工作在node环境下，使用fs进行文件的读取。
   2) 对读取到的文件内容进行代码分析，构建抽象语法树。
      1) webpack只能分析js代码，构建抽象语法树。
      2) 但是require或者import可以引入其它类型的文件(一切皆模块)
      3) 不是js代码，无法进行分析就会报错。此时loader就起到了关键的作用
      4) loader函数的返回值替换掉读取到的数据，如果返回值为js代码，就可以正常的语法分析。
   3) 抽象语法树创建完成，开始分析模块依赖。
      1) 根据依赖地址，先查看模块记录中是否有该模块的记录，如果有，不在进行重新分析(模块只会触发一次loader匹配)。
      2) 模块记录中没有该模块，从新读取该模块，进行上方步骤的重复。直至该入口所有的模块依赖分析完成。
   4) 模块依赖分析完成(没有依赖，或者依赖在模块记录中存在)，继续处理当前模块代码。
      1) 所有的依赖都分析完成。
      2) 替换依赖引入函数，执行时会自动传入。
   5) 当前模块依赖引入替换完成，进行当前模块的记录。
      1) 记录方式: **./src/对应路径: 最终模块代码(替换完的)**
   6) 当前模块分析完成，并且记录完成，表示依赖该模块的父级模块依赖处理完成。
      1) 如果存在其它依赖，继续分析。所有的依赖都分析完成，进行依赖函数的替换。
      2) 替换完成，表示模块分析完成，进行模块记录，继续向前退。
      3) 直至入口所有依赖分析完成。
   7) 入口所有依赖分析完成，形成一个对应的chunk
      1) 记录方式为一个键值对。 "chunk名(文件名): 文件内容"
      2) chunk创建完成后，开始分析下一个入口。
      3) 所有的模块入口分析完成后，根据chunk形成打包文件


2. 打包文件中代码的大体样式。
   1) 整体是一个立即执行函数
   2) 立即执行函数，传入一个对象，根据模块记录创建出的对象
      1) 对象格式: **./src/对应路径: () => {}**
      2) 属性值是一个函数，函数体有两种形式，与devtool属性的配置有关
         1) 完整的模块记录中对应的替换完成后的模块代码
         2) eval( `模块记录中对应的替换完成后的模块代码` )
         3) 函数的作用: 封闭模块作用域
      3) 属性值函数: 主要有两个重要的数据需要传入。
         1) 一个是用于替换依赖的自定义函数，用于引入模块抛出的数据
         2) 一个是module.export对象，模块需要抛出的数据，保存到该对象的内部
            1) 模块中有可能对module.export重新赋值(传入对象重新赋值)。
            2) 所以传入一个对象中的某个属性，这样即使重新赋值，也能通过module.export读取到
   3) 立即执行函数中，自动创建出替换依赖的函数
      

3. 替换依赖的函数的原理
   1) 引入其它模块抛出的数据，就是通过该函数完成。
   2) 该函数必须有返回值`module.export`对象
      1) 该对象传入具体的模块中，用于接收模块抛出的方法。
   3) 该函数接收一个地址，依赖地址。该地址为立即执行函数传入对象的属性值(模块地址)
      1) 利用该地址，就可以执行对象中对应的模块代码
         1) 传入module.export对象，用于接收模块抛出的数据
         2) 传入自定义的替换依赖的函数(本函数)，模块中可能引入其它模块，如果引入就需要使用该方法，所以进行传入。
      2) 模块代码一运行，module.export对象就填充模块抛出的数据。
         1) 又由于该函数把module.export对象抛出。实现了获取模块抛出数据的效果。
   4) 为了防止模块函数执行多次，也进行了缓存处理。
      1) 模块抛出的数据，是一个固定的对象。
         1) 对象数据可能发生变化，但是对象索引不变。外界获取的就是对象索引。
      2) 多次运行模块代码，不光浪费效率，还与模块化开发的本质不同
      3) 当使用自定义替换依赖函数第一次读取该模块中的数据时
         1) 把地址保存到一个对象中，属性值为module.export对象
      4) 下一次在使用自定义替换依赖函数执行当前模块代码。
         1) 先进行缓存判断，如果有缓存，直接返回缓存值
         2) 没有缓存，在进行模块代码的执行，并进行缓存
         3) 实现了同一个模块只会执行一遍的效果
         4) 自定义替换依赖函数该执行几次，就执行几次。
            1) 只不过在内部对具体的模块函数调用进行了限制。
   5) 最终，模块中的require或者import，实际执行的是自定义的替换依赖的函数。然后替换依赖的函数内部调用具体的模块代码(打包时读取出来的)


4. 示范代码
```js
// index.js模块中的代码，入口文件
import b from "./a/b.js";
import c from "./c.js";
console.log(123);

// a/b.js中的代码
import c from "./c.js";
console.log("b");
export default "b";

// c.js中的代码
console.log("c");
export default "c";
```


5. 示范代码打包后输出的文件中的简易代码
```js
( (obj) => {
    // 自定义require方法
    function __require(src) {
        // 创建module对象，保存模块输出数据
        var module = {
            export: {}
        };
        // 创建缓存对象
        var huan = {};
        
        // 判断缓存中有没有
        if(huan[src]) {
            // 存在直接返回缓存
            return huan[src].export
        }

        // 没有执行模块，获取抛出数据
        obj[src](module.export, __require);
        // 进行一次缓存
        huan[src] = module;
        
        // 抛出模块数据
        return module.export;
    }
    
    // 触发入口函数
    __require("./src/index.js")
} )({
    // c先分析完(c引用了两次，第二次(入口文件)不做处理)
    "./src/c.js": function (module, __require) {
        // 对应模块函数体
        console.log("c");
        // 默认导出，放在对象的default属性中
        module.default = "c";
    },
    // a/b.js再分析完成
    "./src/a/b.js": function (module, __require) {
        // 对应模块函数体(方法替换)
        var c = __require("./src/c.js");
        console.log("b");
        export default "b";
    },
    // 入口文件最后分析完成
    "./src/index.js": function (module, __require) {
        // 对应模块函数体(方法替换)
        var b = __require("./src/a/b.js");
        var c = __require("./src/c.js");
        console.log(123);
    },
})
```



# source map源码地图

1. 作用
   1) 在开发环境下调试代码
   2) 当代码在运行时报错，由于最终运行的代码是webpack打包后的代码，无法定位出现错误的位置

2. 通过`devtool`属性进行设置，常用属性值如下(生产和开发环境都能使用，作用以及效果相同):
   1) `none`
       1) 生产环境下的默认值
       2) 不进行任何操作，出现问题定位打包文件
       3) 读取到的模块代码，直接放在对应的封闭函数中(封闭作用域)，进行执行。
   2) `eval`: (效率最高)
       1) 开发环境下的默认值
       2) 读取模块代码，作为字符串，传入eval函数中。然后eval放在对应的封闭函数中。
       3) 该属性读取完模块的默认代码，并且转换完成，会在最后拼接一段字符串。
          1) 比如: `//# sourceURL=webpack:///./src/index.js?`，即一个注释
          2) 浏览器在解析打包后的文件后，会自动创建一个webpack文件夹
             1) 然后在创建一个src文件夹，然后在创建一个index环境
             2) 读取转换后的代码(最终代码)作为环境中的内容。
             3) 对应实际代码中的src文件夹中的模块。
             4) 当浏览器报错的时候，会定位到浏览器创建的具体模块中
             5) 相当于定位在实际代码中，便于错误的调试。
   3) `eval-source-map`: (效率一般)
      1) 形成的模块代码作为eval中的内容，会在最后拼接一段字符串，用于浏览器创建对应的模块环境
      2) 拼接的字符串是两个注释，最后一个和eval的相同，用于声明创建环境的路径。
      3) 第一个注释，就是该模块对应的源码，转换成base64编码的字符串，作为环境内容。
      4) 当浏览器出现问题，进行错误定位时，显示的就是原始的代码，而不是转换后的代码。
   4) `cheap-eval-source-map`: (效率稍高)
      1) 和eval-source-map的作用相同。也是形成两个注释，一个声明环境路径，一个是模块原始代码
      2) 效率比eval-source-map高的原因: 该属性出现错误只定位到行，列不进行处理。
   5) `source-map` (效率最慢，形成单独的文件)
      1) 打包出一个新的文件，用来定义和创建环境。以及声明环境与打包文件的关系
      2) 文件名为，定义的出口文件名，加上.map。比如: "index.js.map"
      3) 打包出的文件中的模块代码，和使用none相同，不放在eval中执行
      4) 在打包文件的最后添加一行注释。当代码报错进行定位时，会从该注释找到对应的map文件
      5) 由于使用到map文件，所以在请求时会自动申请。
      6) 不建议使用，有暴露源代码的风险。
   6) `hidden-source-map`
      1) 形成一个单独的文件，但是打包文件不添加注释，也就是形成的文件不能使用
      2) 主要为了一些第三方工具服务，有些第三方工具可以读取到该文件。




# loader原理以及作用

1. 作用
   1) 对读取到的模块代码进行拦截、替换，创建出可以进行js分析的js代码
   2) 处理一些非js文件，这样就形成了webpack一切皆模块的效果
   3) 非js模块如果没有使用loader进行处理，就会报错。无法进行js语法分析
   

2. 原理
   1) loader就是一个方法，可以自定义或者使用第三方插件
   2) 自定义:
      1) 规范化，在根目录下创建一个loaders文件夹，内部定义具体的loader方法
      2) 定义好的loader方法，在配置文件中引入，由于配置文件运行在node环境下，所以loader方法的导出必须使用`module.exports`。
   4) loader引入的方法，运行在模块读取完成之后，但是进行语法分析构建抽象语法树之前。
      1) 读取到的模块代码，作为参数传入到loader方法中，返回值参与语法的分析。
      2) 相当于替换源代码。

3. 配置文件引入loader路径的方式
   1) 通过`module属性`，属性值是一个对象，对象中有一个`rules属性`。属性值是一个数组格式
   2) 数组的每一项，为一个匹配规则，格式为一个对象。
   3) 规则对象中有两个属性，一个为test，一个为use。
      1) test属性，属性值为一个正则表达式。
         1) 正则用于匹配读取模块的地址(require和import中的地址字符串)
         2) 地址匹配成功，合并use。
      2) use属性，属性值为一个数组。数组的每一项有两种赋值方式
         1) 简化模式，直接书写loader方法对应的地址字符串(底层自动使用require方法读取)
         2) 完整模式，格式为一个对象。
            1) loader属性，对应loader方法的地址字符串(底层自动使用require方法读取)
            2) options属性，用于定义对应loader方法中使用的外界参数(相当于执行传参)
               1) 该属性的属性值为一个对象。
               2) 该属性定义的属性值，并不会真正的作为loader函数的参数传入(只接受一个参数)。
               3) loader函数通过this进行获取(在执行时已经绑定了this指向)。
               4) this是一个非常庞大的数据，不光有options定义的数据，还有其他的数据
               5) 有一个第三方库专门用于处理this，`loader-utils`
               6) 通过该库的`getOptions方法`，传入this，就可以获取到options对象。
            3) loader路径后面可以跟`?`号。?后面跟参数，和url传参相同。
               1) 该方法就相当于options属性，最终也放入this中。
                  1) ?后面的字符串，会整个放入存放options属性的位置，不进行处理
                  2) 如果使用完整方法定义options，该方法失效。
               2) 通过`loader-utils`中的getOptions方法，也可以获取，并且把字符串转换成对象。
      3) 当test匹配成功后，use会进行合并(可能有多个规则的test都匹配成功)
         1) 合并完成后，`从数组的最后开始执行loader方法`
            1) 底层自动使用require方法，获取到loader方法，进行执行。
            2) require地址有两种(loader引入地址)
               1) 相对路径，从开启webpack的路径(项目根路径开始查询)
               2) 绝对路径，从node-modules文件夹中读取
         2) 由于webpack打包模块时，存在缓存。所以一个模块只能触发一次loader匹配
            1) 一个模块可以执行多个loader函数，一次匹配匹配多个规则和loader地址数组
            2) 一个loader函数可能触发多次，一个规则匹配到了多个模块


4. 代码示范
```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                // 引入自定义的loader
                use: ["./loaders/a.js?a=123"]
            }
        ]
    }
 }
```




# plugin的原理


1. plugin的作用
   1) 扩展webpack的功能


2. 原理(注册事件)
   1) 在webpack打包的过程中，存在很多钩子函数(事件)，这些钩子函数分为两种，与两个对象有关。
   2) compiler对象
      1) webpack初始化完成后，该对象创建。
      2) 该对象贯穿整个webpack。
   3) compilation对象
      1) 该对象在compiler对象的环境下创建而成的对象
      2) 该对象贯穿整个打包过程(编译、输出)
      3) 当webpack启动监听状态(watch)，当文件发生变化，重新打包，重新构建compilation对象
         1) 重新打包，是一个新的编译、输出过程
         2) 重新打包，并没有跳出webpack，所以还是位于compiler对象下。
         3) compiler对象，只创建一次。compilation对象可能创建多次。
      4) 总结: 编译、输出(打包过程)位于compilation对象下，而compilation对象位于compiler对象下。
   4) 在这两个对象下，存在非常多的webpack事件，plugin就是给这些事件绑定上对应的处理函数。
      1) 这些事件，会在特定的位置自动触发。进行一些业务的处理。

      
3. 自定义plugin的写法(注册事件的方式)
   1) plugin就是一个对象。对象中有一个属性`apply`，属性值是一个函数。
   2) apply函数会在compiler对象构建完成后执行，并且只会执行一次，重新打包不会执行。
   3) 并且apply函数接收compiler对象，利用该对象就可以绑定compiler对应的一些事件。
   4) 示范代码如下:
    ```js
    class Plugin {
        apply(compiler) {
            compiler.hooks.事件名称.事件类型( name, function (compilation) { })
        }
    }
    ```
   5) 事件名称对应具体的事件(类似click)，有很多中，具体可以查看官方文档。
   6) 事件类型有三种(类似addEventListener函数)，主要控制事件执行完成的时间。
       1) tap: 绑定的事件函数中是同步操作，执行完成意味着事件处理完成
       2) tapAsync: 绑定的事件函数中有异步操作，事件函数执行完成，意味值事件处理完成，不会等异步也执行完成。
       3) tapPromise: 绑定的事件函数是一个promise对象，当触发成功状态，表示事件处理完成，也是异步操作。
   7) name
       1) 该数据主要用于调试，一般不使用，可以随便传递。
       2) 通常传入当前plugin对应的名称。
   8) 事件函数，接收compilation对象。
   9) compilation对应的事件注册方法
       1) 需要借助compiler上的beforeRun事件(编译之前触发)
       2) 然后利用函数接收到的参数compilation对象进行事件的绑定，方式如上。
   10) apply函数相当于window.onload。  
   11) 由于plugin是一个对象，所以通常使用Es6的class构造器进行创建，这也是第三方plugin的写法。
    ```js
    module.exports = class Plugins {
        apply(compiler) {

        }
    }
    ```


4. plugin的使用 
```js
// 如果地址填写绝对路径，表示从node-modules文件夹中使用第三方plugin。
const A = require("./plugin的地址");
const B = require("./plugin的地址");
module.exports = {
    plugins: [
        new A(),
        new B()
    ]
}
```
多个plugin的apply函数，在打包的时候会顺序执行，依次绑定上对应的事件。


5. compilation对象的常用属性(辅助plugin实现一些功能)
   1) 不同事件，接收到的compilation对象中的属性是不同的。
   2) `emit事件`(编译完成触发)，接收到的compilation对象中的常用属性
      1) `assets属性`
         1) 通过该属性，可以获取到chunk的一些属性(出口文件)，比如文件大小，文件名
         2) 通过该属性，可以新创建一个chunk，重新创建一个输出文件。
            1) assets属性是一个对象，对象的属性就是输出文件的名称
            2) 如果给一个没有的文件名赋值(新创建一个属性)，会认为创建一个新的chunk。
            3) 属性值为一个对象，对象中有两个必填属性。
            4) `source`: 属性值为一个函数，返回值设置文件内容
            5) `size`: 属性值为一个函数，返回值设置文件大小，可以随意返回一个数据
      2) assets属性的使用示范代码
    ```js
    module.exports = {
        plugins: [
            {
                apply: (compiler) => {
                    compiler.hooks.emit.tap("a", function(compilation) {
                        // 获取输出文件的大小，存在一定的误差
                        console.log( compilation.assets["main.js"].size() / 1000 + "KB" );
                        // 新创建一个输出文件
                        compilation.assets["123.js"] = {
                            source() {
                                return "console.log(123)";
                            },
                            size() {
                                return ""
                            }
                        }
                    })
                }
            }
        ]
    }
    ```




# 配置文件的函数式写法

1. 配置文件除了导出一个对象外，还可以导出一个方法。但是方法需要有一个返回值，返回值为配置对象


2. 配置函数可以接收参数，参数为一个对象。对象中的数据需要从命令行中输入。如果没有输入为undefined。


3. 作用
   1) 可以动态配置打包环境


4. 示范代码
```js
module.exports = function (env) {
    console.log(env);
    return {
        mode: env.mode ? "development" : "production",
        plugins: [
            {
                apply() {
                    console.log("启用了");
                }
            }
        ]
    }
}
```

5. 命令行操作
   1) 命令代码: `npx webpack --env.a=123 --env.c=456 --env.mode`
      1) --env就是为函数接收到的参数对象添加属性值
      2) 直接书写没有赋值，赋一个默认值true。
   2) 配置函数接收到的参数`{ a: 123, c: 456, mode: true }`


6. 配置文件甚至可以单独提取一部分，然后通过require进行引入即可。
   1) webpack打包，配置文件就是普通的nodejs代码的运行




# 配置文件context属性

1. 改变配置文件中相对路径的查看规则，主要影响(入口和loader)
   1) 正常情况下，从开启node的路径(工程根路径)进行相对路径的查询。
   2) 使用该属性，指定一个路径，相对路径在进行处理时，相对于该路径，而不是默认路径了。
   3) 需要与__dirname配合使用，甚至path.resolve方法配合使用


2. 使用
```js
module.exports = {
    context: __dirname + "/src"
}
```


# output.library属性

1. 作用
   1) 保存入口模块抛出的数据，默认保存到全局window中指定的属性中
   2) 指定属性名，就是通过该属性

2. 原理
   1) webpack打包后的文件是一个立即执行函数。默认是没有返回值的
   2) 一旦使用该属性进行设置，就会存在返回值(`require("入口文件")`)
   3) 外界使用定义好的属性进行接收
   4) 打包文件中的代码
    ```js
    var abc = ( (obj) => {
        function __require() {};
        // 默认是没有返回值的
        return __require( obj["./src/index"]() ); // 执行入口模块中的代码，启动工程
    } )({
        "./src/index.js": (module, __require) => {
            // 替换后的index.js模块代码
        }
    })
    ```

3. 配置文件中的操作
```js
module.exports = {
    output: {
        library: "abc"
    }
}
```




# output.libraryTarget属性


1. 作用
   1) 设置入口模块抛出数据的保存位置
   2) 通常与library属性配合使用，不配合也能使用

2. 该属性可以设置的属性值以及作用
   1) `var`: 默认值，暴露给普通变量
      1) 比如: `var abc = ()()`
   2) `this`: 暴露给this 
      1) 比如: `this["abc"] = ()()`
   3) `window`: 暴露给window 
      1) 比如: `window["abc"] = ()()`
   4) `commonjs`: 暴露给export(打包文件把入口模块抛出的数据在进行抛出)
      1) 比如: `export["abc"] = ()()`
      2) 由于使用的是commonjs模块抛出的方式，所以在node环境下可以通过require的方式，执行打包文件，然后获取入口模块抛出的数据。
      3) 通常用于服务器开发

3. 配置文件中该属性的使用方式
```js
module.exports = {
    output: {
        library: "abc",
        libraryTarget: "commonjs"
    }
}
```      


4. 没有和library连用的情况
   1) library就是在指定环境(对象)中创建一个属性，然后接收入口模块抛出的数据对象
   2) 如果没有使用该属性指定接收属性，就会遍历入口模块导出的数据对象，然后每个属性名作为指定对象的属性名，对应的属性值作为其属性值，类似对象合并。
   3) 表现样式(以export为例)
    ```js
    var a = require("./src/index.js");
    for(var i in a) {
        // 遍历赋值
        export[i] = a[i]
    }
    ```




# target属性

1. 作用
   1) 设置打包文件将要运行的环境


2. 常用属性值
   1) `"web"`: 默认值，打包后的文件运行在web环境下(浏览器环境)
   2) `"node"`: 打包后的文件运行在node环境下


3. 原理
   1) 对打包的输出文件没有什么大的影响，只有个别情况下存在影响
   2) 比如:
      1) 某个模块引入了`fs`模块，而该模块是node内置模块，在node-modules文件夹下是找不到的
      2) 此时如果还是在web环境下进行打包，会从node-modules文件夹下读取文件，读取不到报错
      3) 如果是位于node环境下。在打包的过程中，分析是node内置模块，不在进行读取。
         1) 虽然不读取fs模块，但是依赖引入依旧会进行替换，此时fs就读取不到。
         2) 于是自动创建了一段代码，相当于loader的功能。
         3) `"fs": () => { module.exports = require("fs") }`
         4) 原require函数，经过替换，执行fs对应的代码，然后在进行读取
         5) 由于工程文件运行在node环境下，支持require函数，可以正常运行
   3) 只有一些环境的内置模块进行特殊的处理
      1) 其它模块(工程模块和node-modules下的模块)，处理方式相同，读取、分析、替换
      2) 如果工程中没有使用内置模块，不同环境下的打包文件是相同的(通用)
   4) **该属性影响的是打包分析过程**




# module.noParse属性

1. 作用
   1) 减少打包时间

2. 原理
   1) 该属性的属性值是一个正则表达式
   2) 当需要解析的模块地址，符合定义的正则。则读取文件内容，直接把文件内容进行缓存，不进行模块依赖和代码替换的操作
   3) 如果匹配的模块存在模块依赖，在执行时就会出错，并没有require方法(没有进行替换)
      1) 即使打包成node环境，文件地址也发生变化，无法引入

3. 使用场景
   1) 一些第三方库的引入
      1) 这些第三方库，代码一般非常庞大，并且都是单文件，内部并没有依赖(已经是打包后的文件)
   2) 没有模块依赖的模块



# resolve属性

1. 作用
   1) 修改模块依赖组件读取的位置

2. 原理
   1) webpack在分析模块依赖时，是需要读取文件的。
   2) 虽然webpack打包过程运行在node环境下，但是读取文件是webpack在做，而不是node。
   3) 读取文件，就需要有读取路径，而resolve就可以**修改模块依赖的读取路径**
   4) 该属性的属性值是一个对象，对象中有三个常用的属性。

3. 常用属性
   1) `modules属性`: 属性值为数组。设置打包过程中模块依赖的绝对路径查询位置
      1) 比如: `require("jquery");`，打包时默认从node-modules下读取文件。
      2) 属性默认值: `modules: ["node-modules"]`
      3) 使用案例: `modules: ["abc", "cc"]`  
         1) 比如: `require("jquery");`
         2) 查询时从abc下查询，查询不到再去cc下查询，如果都没有报错。
   2) `extensions属性`: 属性值为数组，用来webpack自动补全后缀名的操作。
      1) 比如: `require("./a");`，会自动拼接为`./a.js`或者`./a.json`。
      2) 属性默认值: `extensions: ["js", "json"]`
      3) 使用案例: `extensions: ["js", "json", "css"]`
         1) 当模块依赖没有写后缀名时，先拼接.js后缀名
         2) 如果没有找到，在拼接.json后缀名
         3) 如果还没有找到，在拼接.css后缀名
         4) 如果最终都没有找到，表示文件不存在，打包报错。
   3) `alias属性`: 属性值为对象，给模块依赖的引入路径中的某个字符起一个别名。
      1) 比如: `alias: { "a": "abc", "@": __dirname + "/src" }`
      2) 比如模块的引入路径为: `./a/b.js` 会替换成 `./abc/b.js`
      3) `@`也一样，最常用的就是`@`，可以简化相对路径的书写(针对回退层级过多的情况)
      4) 实际读取文件的路径，就是替换后的路径




# externals属性

1. 作用
   1) 处理模块依赖与script标签引入产生冲突的问题
   2) 产生冲突也没有问题，只不过打包时间稍长，对运行没有任何影响
   3) 可以减少某些情况下打包文件的大小

2. 原理
   1) 比如，在html文件中，通过script标签引入了jQuery，此时全局已经注入了$。
   2) 某个模块中使用到了jQuery中的代码，正常情况下引入依赖，然后使用。
      1) 由于通过script标签引入，全局已经存在，可以正常使用
      2) 如果在进行模块依赖引入，在打包的过程中会读取文件，进行分析，然后使用分析后的代码
         1) 此时全局中的(script标签引入的)就会失效
         2) 同时增加了打包文件的大小(第三方库打包进来)
      3) 如果直接使用(可以使用)，没有进行模块依赖，不容易理解
         1) 搞得像模块内部定义的属性，不是模块依赖
   3) 如果配置文件中配置了该属性
      1) 当引入的模块依赖地址，符合配置，将不再读取依赖文件
      2) 而是自动创建一段代码，直接抛出全局对应的数据
      3) 比如: `"jquery": () => { module.exports = $ }`


3. 具体使用
```js
module.exports = {
    externals: {
       "jquery": "$",
       "./a.js": "c"
    }
}
```
分析: 
   1) 当模块依赖为jquery时，生成module.exports = $，
   2) 当模块依赖为a.js时，生成module.exports = c，
   3) 不进行实际依赖模块的读取，使用全局变量




# stats属性

1. 作用
   1) 用来设置webpack打包的工具显示页面的输出内容

2. 具体使用
```js
module.exports = {
    stats: {
        colors: true   // 控制台带上颜色，比较好看
        modules: false // 控制台不显示依赖模块的分析信息显示，如果模块过多，显示出来会显得信息有些庞大。
    }
}
```




# 打包html

1. 借助的插件(是一个plugin): `npm install html-webpack-plugin -D`


2. 使用
   1) 该插件`导出一个构建plugin对象的构造函数`，可以接收一个参数，参数格式为对象
      1) 通过参数对象可以实现对输出html文件的一些配置
   2) 由于是使用plugin处理html文件，所以打包出的html是不受webpack环境的控制的。
      1) 要想打包成生产环境，需要借助传参对象中的minify属性中的collapseWhitespace属性，属性值设置为true


3. 配置文件中的使用代码
```js
// 引入构建plugin的构造函数
let HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
    plugins: [
        new HtmlWebpackPlugin({
            // 给输出文件设置文件名，默认为index.html
            filename: "abc.html",
            // 设置读取的html，存在默认模板
            template: "./src/index.html",
            minify: [
                // 去除空格
                collapseWhitespace: true,
                // 去除注释
                removeComments: true
            ],
            // 设置引入那个chunk(打包文件)，默认["all"]，所有
            chunks: ["index", "a"]
            // chunks数组的数据，并不是最终打包的文件名，而是入口属性名(chunk名)
        })
    ]
}
```


4. 原理
   1) 该插件在输出html文件时，会自动添加打包成的js文件或者css文件。
   2) 并且自动添加的时候，路径并不是读取打包文件，然后符合条件的添加。
   3) 而是根据chunk中的数据获取到对应模块将要输出的位置，进行添加。


# 复制静态文件

1. 借助的plugin插件: `copy-webpack-plugin`

2. 使用分析
   1) 该插件导出的plugin构造函数，接收一个参数，格式为数组
   2) 数组的每一项为一个复制规则，格式为对象
   3) 复制规则对象中有两个属性
      1) from属性: 指定需要复制的文件夹(不赋值文件夹，而是该文件夹下所有的内容)
      2) to属性: 表示复制到什么位置，相对于打包文件夹dist，如果位置没有自动创建文件夹
   4) 如果复制过去的文件名，与dist打包输出的文件名相同(产生冲突)
      1) 保留打包文件，即舍弃复制的文件


3. 配置文件夹中的配置
```js
const CopyWebpackPlugin = require("copy-webpack-plugin");
module.exports = {
    plugins: [
        new CopyWebpackPlugin([
            // 复制规则
            {
                from: "./src/",  to: "./"
            }
        ])
    ]
}
```




# 打包css
需要借助的插件 `npm install style-loader css-loader -D`
要想解析css，需要在入口js中引入`import "./index.css"`
html和js的入口都在配置文件中声明，css和less或者scss都在入口js文件中通过import的方式引入。

**注意: 如果把css样式打包成文件，则一个入口js下通过import引入的所有样式文件(css、less、scss)，最终会打包成一个css文件。多入口文件名为js入口的名字 或者 单入口自定义的css文件名**
如果打包成行间，则一个import引入的样式文件的样式放入一个style标签中，并不会合并。

**css动态设置文件名[name]取决于entry中配置的入口属性名，而不是取决于将要打包的css或者less或者scss文件名**

**如果没有使用多入口定义入口名，打包后的js和css使用[name]根据入口名定义文件名(此时没有取默认值main)**
html使用[name]，取得是0(不受js多入口入口名的控制，它有自己的入口,template是不能写成{}打包多个html的，要想打包多个html，只能多次使用插件new HtmlWebpackPlugin())


## 打包成行间style

```js
let path = require("path");
let HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    entry: "./src/index.js",
    mode: "development",
    output: {
        filename: "index.js",
        path: path.resolve(__dirname, "dist")
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {loader: "style-loader"},
                    {loader: "css-loader"}
                ]
                //简写 use: ["style-loader", "css-loader"]
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: "[name].css"
        }),
        new HtmlWebpackPlugin({
            filename: "index.html",
            template: "./src/index.html",
            minify: {
                removeComments: true,
                collapseWhitespace: true
            }
        })
    ]
}
```

## 单独输出css文件
除了style-loader、css-loade、less-loader、less外，还需要借助一个插件
`npm install mini-css-extract-plugin -D`

```js
let path = require("path");
let HtmlWebpackPlugin = require("html-webpack-plugin");
let MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    entry: "./src/index.js",
    mode: "development",
    output: {
        filename: "index.js",
        path: path.resolve(__dirname, "dist")
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {loader: MiniCssExtractPlugin.loader},
                    {loader: "css-loader"}
                ]
                // 简写  use: [MiniCssExtractPlugin.loader, "css-loader"]
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: "[name].css"
        }),
        new HtmlWebpackPlugin({
            filename: "index.html",
            template: "./src/index.html",
            minify: {
                removeComments: true,
                collapseWhitespace: true
            }
        })
    ]
}
```

## 把less打包成css

需要依赖的新插件 `npm install less less-loader -D`;

也需要在入口中映射less的地址 `import "./index.less"`;


```js
let path = require("path");
let HtmlWebpackPlugin = require("html-webpack-plugin");
let MiniCssExtractPlugin = require("mini-css-extract-plugin");


module.exports = {
    entry: {
        index: "./src/index.js",
        demo: "./src/demo.js"
    },
    mode: "development",
    output: {
        filename: "[name].js",
        path: path.resolve(__dirname, "dist")
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "less-loader"]
            }
        ]
    }
    plugins: [
        new MiniCssExtractPlugin({
            filename: "[name].css"
        }),
        new HtmlWebpackPlugin({
            filename: "index.html",
            template: "./src/index.html",
            minify: {
                collapseWhitespace: true,
                removeComments: true
            }
        })
    ]
}
```

**less中的语法(定义函数和数据)**
```less
@color: #f0f;
.font(@color) {
    font-size: 14px;
    color: @color;
}
html{
    div{
        color: @color;
    }
    span{
        .font(@color);
    }
}
```

## css的压缩
css是不受mode控制的，要想对css进行压缩，需要借助另外的插件
`npm install cssnano -D`
如果没有进行自动添加前缀(下载辅助插件)，还需要下载
`npm install postcss postcss-loader -D`

压缩是在使用css-loader之前。

**通常与自动添加前缀做适配 配合使用，自动添加前缀和压缩css都需要借助postcss插件的辅助才能完成**

```js
{
    test: /\.less$/,
    use: [MiniCssExtractPlugin.loader, "css-loader", 
    // 压缩css
    {
        loader: "postcss-loader",
        options: {
            ident: "postcss",
            plugins: [
                require("cssnano")()
            ]
        }
    }, "less-loader"]
}
```

## css自动添加前缀
需要借助的插件
`npm install postcss-cssnext -D`
如果没有进行css压缩处理(下载对应的插件)，此处还需要下载几个辅助的插件
`npm install postcss postcss-loader `

自动添加前缀，需要在使用css-loader之前

**通常与压缩css配合使用，自动添加前缀和压缩css都需要借助postcss插件的辅助才能完成**

```js
{
    test: /\.less$/,
    use: [MiniCssExtractPlugin.loader, "css-loader", 
    // 压缩css
    {
        loader: "postcss-loader",
        options: {
            ident: "postcss",
            plugins: [
                require("postcss-cssnext")()
            ]
        }
    }, "less-loader"]
}
```


# css的抖动

需要借助的插件
`npm install purifycss-webpack purify-css -D`

1. 根据HTML文件静态抖动(如果js中动态创建了新的dom，同样会抖掉)
```js
let PurifycssPlugin = require("purifycss-webpack");
let glob = require("glob");

plugins: [
    // 根据src下所有的html文件进行抖动
    new PurifycssPlugin({
        paths: glob.sync(path.join(__dirname, "./src/*.html"))
    }),
    new CleanWebpackPlugin()
]
```

2. 根据html文件和js文件进行抖动
要想根据两种文件进行抖动，不能引入glob。而需要引入glob-all(该组件的sync可以传递数组--多种文件)，glob的sync只能传递一种文件。
而glob不需要下载，glob-all需要进行下载`npm install glob-all -D`

```js
let PurifycssPlugin = require("purifycss-webpack");
let globAll = require("glob-all");

module.exports = {
    plugins: [
        // 根据两种类型的文件进行css抖动
        new PurifycssPlugin({
            paths: globAll.sync([
                path.join(__dirname, "./src/*.html"),
                path.join(__dirname, "./src/*.js")
            ])
        })
    ]
}

```


**抖动的原理: 使用正则匹配的方式进行抖动，所以即使注册掉的代码也可以匹配到，保留样式。另外，css抖动的时候不是按照结构进行抖动，只要html和js中创建了该标签，该标签的所有样式都会保留(不考虑谁是谁的子级)**



# js抖动
需要借助的插件
`npm install webpack-deep-scope-plugin -D`

```js
let WebpackDeepScopePlugin = require("webpack-deep-scope-plugin").default;

module.exports = {
    // 要放在css抖动的后面
    new WebpackDeepScopePlugin();
}
```

**注意点: js抖动一定要放在css抖动的后面**





# 每次打包时删除打包文件夹下上次打包的文件(清空打包文件夹)
需要依赖的插件 `npm install clean-webpack-plugin -D`

```js
// 老版本的写法(现在会进行报错)
// let CleanWebpackPlugin = require("clean-webpack-plugin");
// 新版本的引入方法
let {CleanWebpackPlugin} = require("clean-webpack-plugin");

// 在plugins中
module.exports = {
    plugins: {
        new CleanWebpackPlugin();
    }
}
```


# 开启本地服务器

1. 借助的插件: `npm i webpack-dev-server -D` 
   1) 该插件即不是plugin，也不是loader，而是webpack官方提供的一个插件
   2) 通过`npx webpack-dev-server`开启服务器，然后进入监听状态

2. 原理
   1) 该指令不会造成webpack的打包，即不会生成打包文件
   2) 其实底层也进行了打包处理(调用webpack)
   3) webpack打包的过程中，先进行打包处理，然后建立暂存区，保存每个chunk的数据
      1) 比如: "index.js": 文件代码
      2) 然后根据暂存区，创建对应的文件。
   4) 该指令先进行暂存区的保存，然后清空暂存区，这样就无法输出文件
   5) 把保存的暂存区数据，抛给开启的服务器
      1) 比如: 申请`index.js`，保存的有该键值，然后就把数据推送给浏览器(数据就是该文件对应的代码)
   6) 由于该指令进入监听状态，当源代码发生变化时，会进行重新打包处理，并且浏览器自动刷新。
      1) 底层开启了watch。

3. 配置
   1) webpack存在一些默认配置，可以直接通过`npx webpack-dev-server`开启服务，也可以进行一些配置然后在开启服务
   2) 利用配置文件`devServer`属性进行配置(独立于plugin和loader的配置)，常用配置属性如下:
      1) `port属性`: 配置端口号
      2) `open属性`: 自动打开浏览器，访问初始页面
      3) `index属性`: 属性值是一个字符串。默认值index.html
         1) 如果直接访问/，默认推送index.html页面。就是通过该属性进行设置的
         2) 如果没有该属性，访问/，保存的数据中没有/对应的数据，无法正常访问
      4) `proxy属性`: 属性值为一个对象，设置服务器代理，解决跨域问题。
         1) 对象属性名: 为一个字符串，作为正则表达式
         2) 对象属性值: 有两种赋值对象，一种为对象，一种为普通字符串
            1) 对象: `{ target: "域名", changeOrigin: true }`
            2) 字符串: `"域名"`
         3) 示范属性: `"/api": "https://www.baidu.com"`
         4) 当发送的ajax请求，路径带有/api(进行正则匹配判断)
            1) 实际请求发送到localhost下，然后localhost把域名换成设置的域名，进行申请
            2) 路径不变，由于是服务器代理请求不存在跨域，把请求的数据返回
         5) 对象格式的作用
            1) 对象格式，可以修改代理请求的请求头，默认使用原封不同的请求头，然后进行代理请求
            2) 尤其是请求头中的host: 还是为localhost，表示跨域发过来的，有些服务器对其有要求
            3) `changeOrigin`设置的就是请求头中的host，设置为将要请求的域名
            4) 如果服务器对其没有要求，可以使用简化版字符串。


4. 作用
   1) 该插件只作用于开发环境下，便于调试代码，如果部署服务器，还是需要使用webpack进行打包部署
   2) 即使开启服务器代理模式，也没有任何影响
      1) 代码中发送的请求没带有域名，自动拼接域名
      2) 当部署后，在进行请求，就不存在跨域问题(位于同一域下)


5. 配置示范代码
```js
module.exports = {
    devServer: {
        contentBase: "dist",
        port: "9090",
        open: true,
        index: "index.html",
        proxy: {
            "/api": {
                target: "",
                changeOrigin: true 
            }
        }
    }
}
```

6. 扩展
   1) 由于要开启服务器，以及自动更新，所以在使用webpack-dev-server命令开启服务后。
      1) 会自动打包一些插件，手动使用webpack进行打包后，没有这些插件。
   2) 由于打包一些服务器的插件，所以打包页面就会显得很臃肿。
      1) 可以使用**stats属性进行限制**，只显示一些重要的信息



# 开启服务器的热更新事件

1. 作用
   1) 优化开发时的性能，不会造成部署上线后的性能优化
   2) webpack-dev-server开启监听，当代码发生变化时重新进行打包处理(不输出打包文件)
      1) 重新打包后，浏览器就会刷新页面，只要页面一刷新，就会重新申请所有的资源
   3) 热更新的作用
      1) 修改代码不会造成浏览器的刷新(打包从新开始打包)
      2) 只重新加载和渲染代码发生变化的部分，其它没有发生变化的部分不进行操作
      3) 优化了浏览器申请数据的效率


2. 热更新分为两种，一种是css(包括less和scss)热更新，一种是js的热更新。
   1) css热更新(在配置文件中进行配置开启)
      1) 配置完成后，再修改css时，页面就不会重新刷新，只加载发生变化的部分。
      2) 修改js依旧会造成整个页面的刷新(除非开启js热更新)
      3) 配置代码
      ```js
      let webpack = require("webpack");
      module.exports = {
          plugins: [
              // 开启热更新的事件
              new webpack.HotModuleReplacementPlugin();
          ],
          devServer: {  
              // 开启热更新功能(默认css更新)
              hot: true
          }
      }
      ```

   2) js热更新(在js文件中进行开启)
      1) 配置文件中的代码依旧需要设置
      2) js中开启热更新的代码
      ```js
      //判断是否开启了热更新功能
      if(module.hot){ 
          module.hot.accept();
      }
      ```

3. 注意点
   1) 必须使用style-loader打包成行间标签，css热更新才有效
   2) 使用MiniCssExtractPlugin.loader，是不具备热更新的效果，需要手动刷新(也不会自动刷新)
   3) html不具备热更新效果，需要手动刷新(也不会自动刷新)
   4) 总结手动添加的chunk，是不具备热更新效果的


# 开启自动打包功能
配置文件无需进行过多的操作，只要在命令行中输入`webpack -w`，就会进入一个监听状态。

只要代码进行了保存就会把代码重新打包到dist中(省去了手动打包的步骤，不具备浏览器自动刷新的功能，只是代替了代码保存自动执行webpack的步骤)

# js中引入图片问题(file-loader和url-loader)

1. 作用
   1) 输出一个单独的图片文件

2. 解决的问题
   1) js中如果要使用图片，比如手动创建img标签
   2) 如果直接使用图片路径，由于模块的位置问题，打包后位于同一个文件夹，所以相对路径不好设置
      1) 除非知道打包后js文件的位置，以及图片的位置
      2) 手动放在输出文件夹下或者使用copy-webpack-plugin插件克隆输出图片文件
      3) 设置相对路径的时候，直接相对于打包后的位置进行设置
      4) 该方法过于麻烦，且容易出现问题
   3) 此时就可以借助webpack的一切皆模块的功能
      1) 在js中通过模块依赖引入图片
      2) 由于webpack不识别图片，打包出现错误，所以需要借助loader进行处理
      3) 可以处理图片的loader一共有两个`(file-loader和url-loader)`

3. file-loader
   1) 作用
      1) 直接复制一张图片，进行打包输出
      2) 然后创建一个js代码，用于分析
      3) js代码，抛出打包输出图片的路径
         1) `"./a.jpg": () => { module.exports = 打包后的图片相对于打包输出文件夹的路径 }`
      4) 底层还是使用根据打包的输出的位置以及图片位置，进行路径的操作
         1) 只不过是代码自动完成，不需要手动操作，避免了路径不清楚容易出现错误的情况
      5) file-loader还可以使用一些参数，通过options进行配置
         1) 文件名，通过`name`进行配置，默认以hash值作为文件名
         2) 输出位置，通过`outputPath`进行配置，默认打包到输出文件夹下(dist)
   2) 使用
   ```js
   module.exports = {
       module: {
           rules: [
               {
                   test: /\.(png|jpg)$/,
                   use: [
                       {
                           loader: "file-loader",
                           options: {
                               name: "[name]-[hash:5].[ext]",
                               // 打包到那个文件夹下
                               outputPath: "img"
                           }
                       }
                   ]
               }
           ]
       }
   }
   ```


4. url-loader
   1) 作用
      1) 把图片转换成base64编码，然后抛出，不创建新的图片
         1) `"./a.jpg": () => { module.exports = 转换的base64位编码 }`
      2) 可以减少图片请求，但是增加打包文件的代码量
      3) 所以给小图片转换成base64位编码，大图片还是生成图片文件
         1) 如果小图片也打包输出，然后请求，却浪费了效率，所以转换成base64
         2) 由于图片过小，转换的base64也不会过于庞大
      4) 既然涉及到图片打包，就需要使用file-loader插件
         1) url-loader插件，默认全部转换成base64
         2) 要想小图片转换成base64，大图片生成文件，需要设置一个范围
         3) 就需要给url-loader传递参数(options属性)
            1) 传递的参数为`limis`: 图片最大值(单位kb)
            2) 如果大于limis设置的值，则打包成文件
            3) 如果小于limis设置的值，则转换成base64
            4) 打包输出文件时(大于)，url-loader会自动启动file-loader插件，然后把options设置的参数原封不动的传入file-loader中
            5) 所以就可以设置打包的文件名，如果转换为base64，则设置的文件名失效
         4) limis的默认值为false，表示所有的图片都转换成base64
   2) 使用
   ```js
   module.exports = {
       module: {
           rules: [
               {
                   test: /\.(png|jpg)$/,
                   use: [
                       {
                           loader: "url-loader",
                           options: {
                               limis: 1000,
                               name: "[name]-[hash:5].[ext]",
                               // 打包到那个文件夹下
                               outputPath: "img"
                           }
                       }
                   ]
               }
           ]
       }
   }
   ```

5. hash扩展
   1) hash与工程没有任何关系，是打包过程中，file-loader中生成的。
   2) options只是把数据传入，loader具体分析数据，然后生成哈希进行替换。
   3) 同一个图片，哈希相同，只有发生变化(名称相同，图片换成了另外一张)，hash才会发生变化




# 解决路径申请问题(output.publicPath)

1. 作用
   1) 解决路径问题


2. 原理
   1) 该属性没有什么用，只不过在打包文件中添加一个属性(`__webpack_require__.p = "?"`)
   2) 有些plugin插件和loader插件可能使用该属性。
      1) 使用方式: `__webpack_require__.p + 对应路径`
      2) 比如: file-loader和html-webpack-plugin会使用该属性
   3) html-webpack-plugin插件，打包html，然后自动引入css和js文件。
      1) 路径使用chunk对应的路径(相对于打包文件夹(dist)的路径)，是一个相对路径
      2) 该插件在形成路径时，会在前面自动拼接上`__webpack_require__.p + 相对路径`
   4) file-loader插件，打包js中的图片模块依赖，然后输出打包后相对于dist的相对路径
      1) 实际上输出的路径为__webpack_require__.p + 相对路径
   5) 由于`__webpack_require__.p`的默认值为`""`，所以拼接完还是相对路径。


3. 解决的问题
   1) 对应插件产生的路径是相对于dist文件夹的相对路径，然后作为地址引用
   2) 当访问html时，就会根据对应的地址引用请求数据
   3) 由于使用的是相对路径，所以就要考虑html文件的位置
      1) 如果html位于dist文件夹的根目录下
         1) 相对路径回退一级，从dist文件夹开始查询，不会出现问题
      2) 如果html位于某个文件夹下，比如: a/index.html
         1) 此时相对路径回退一级，从a文件夹下开始查询，就会出现问题，没有对应的资源
      3) 所以publicPath`的作用是定位到dist文件夹下，一般写成`"/?"`
         1) 经过拼接，会形成绝对路径，在申请时，直接从dist文件夹开始查询
         2) 可以写成相对路径，../在回退一级变成了dist文件夹

4. file-loader支持局部`publicPath`的定义
   1) 打包代码会发生变化，不在拼接`__webpack_require__.p`
   2) 直接返回由局部publicPath拼接完成的路径。比如: `export default /a/b.jpg`


5. 配置示范代码
```js
module.exports = {
    output: {
        publicPath: "/",
    },
    module: {
        rules: [
            test: /\.png$/,
            use: [{
                loader: "file-loader",
                options: {
                    publicPath: "/a",
                }
            }]
        ]
    }
}
```

6. 注意点
   1) 如果最后没写/，自动拼接/。比如: a -> a/
   2) 如果最后写了/，不拼接/。比如: a/ -> a/




