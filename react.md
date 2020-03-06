<!-- [toc] -->
# 1 react的介绍
react是一种比较流行的前端框架。
介绍网址: https://reactjs.org/

# 通过script标签引入react
## 引入开发环境的文件
```html
<!-- 引入react的库 -->
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<!-- 引入react渲染dom的库 -->
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```


## 引入生产环境的文件
```html
<!-- 引入react的库 -->
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<!-- 引入react渲染dom的库 -->
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

# 使用脚手架搭建react项目 

1. 安装脚手架: `npm i create-react-app -D`

2. 使用脚手架创建项目: `npx create-react-app 自定义项目文件名`

3. 进入创建的项目文件: `cd 项目文件名`

4. 开启服务: `npm start`



# jsx语法
     
## jsx语法的介绍

## jsx语法使用的注意点
    
1. 最外围只能有一个父标签。
可以为实际的标签，也可以为`<></>或者<React.Fragment></React.Fragment>`，这两种标签最终不会被创建渲染，其中`<></>`是一个语法糖，底层使用的还是`<React.Fragment></React.Fragment>`。目的在于书写方便。

2. 每个标签必须有结束
`<**></**>` 这两个必须成对存在， 可以进行简写 `<** />`。
比如img标签，不可以写成`<img src="" alt="">`。必须写成`<img></img>`或者`<img />`

3. 可以写算数表达式
无论行间还是标签的子节点，使用表达式，都必须加上 {}。
比如: `<span className={ *** }> { *** } <>`。
{}中的表达式会进行运算，把运算完的结果进行填充。
{}中不可以写普通的对象，可以填写React.createElement()创建的对象，渲染出实际的标签。
{}中填写数组，最终会遍历该数组，把数组中的每一项作为一个子节点进行填充。可以实现列表循环渲染

4. 行间特殊的属性，需要使用别名，与关键字产生了冲突
class要写成className
for要写成htmlFor

5. 行间style的使用
    1)  style="color: #f0f; background: #ff0"
    2)  使用表达式 style={ { color: '#f0f', background: '#ff0'} }。内部是一个对象


6. 把字符串html渲染成正常的html
```js
var arr = "<div>123456</div>"

var div = (<div dangerouslySetInnerHTML={ { __html: arr} }></div>)
render(div, document.getElementById('root'));
```


# create-react-app脚手架搭建的项目使用scss的方式

1. 输入命令(创建完项目，立即执行该命令): `npm run eject`(该命令可以省略)
   1) 如果项目文件发生了变化
   2) 需要使用git先进行跟踪，才能使用该命令
   3) 先执行`git add .`和`git commit -m "?"`，然后在执行`npm run eject`
   4) 作用: 把webpack的打包配置文件，从隐藏的位置弹出(显示出来)
      1) 用脚手架生成的react项目，webpack的打包配置文件是隐藏起来的
   5) **该命令不是必须的，可以省略，只需要下载`node-sass`即可**
      1) 不需要重新配置webpack，脚手架构建项目的时候已经配置好了


2. 输入命令: `npm install node-sass -D`
   1) 下载解析scss的插件

3. 就可以正常使用scss文件，编写样式了

4. 样式通过`import`引入，比如: `import "./index.scss"`







# react中引入图片路径的问题

1. 不能直接使用相对路径。
在react中的各个组件中，引入图片。如果img直接使用相对路径，最终会出现问题。
原因: react最终会进行打包处理，打包完路径就会发生变化。

2. 正确引入的方法
    1)  首先通过`import a from "可以使用相对路径`"，引入图片。
    2)  该组件使用该图片时，直接`src={ a }`，就可以进行图片的引入。




# jsx的渲染流程以及仿写jsx渲染
## 3.1 渲染流程
jsx中的HTML语法，首先使用React.createElement()进行解析。所以React名字不可以修改，否则报错，
解析完成后返回一个虚拟dom，然后render利用虚拟dom渲染出实际的样式
解析方法，解析代码如下:
```js
var div = <div id='demo'>12345<spam>111</span></div>
// 实际执行的代码如下:  
var div = React.createElement("div", {id: 'demo'}, '12345', React.createElement("span", null, '111'))
```

## 3.2 仿写jsx渲染
关键两步，创建虚拟dom，使用render进行渲染
```js
// 创建虚拟dom的函数
const React = {
  // type(节点名), props(节点行间属性), ...children(节点里的内容)
  createElement(type, props, ...children) {
    // 返回创建好的虚拟dom
    return {
      type,
      props,
      children
    }
  }
}

// 渲染虚拟节点的函数
const render = (vNode, container) => {
  // 服务于回调，查看传入的是文本节点，还是标签节点
  if(typeof vNode === "string"){
    const text = document.createTextNode(vNode);
    container.appendChild(text);
    return;
  }
  // 标签节点执行的函数
  const {type, props, children} = vNode;     // 提取出虚拟节点中的一些属性
  const ele = document.createElement(type);  // 创建出实际的标签
  for(let key in props){                     // 对行间属性进行添加
    // 滤掉继承
    if(key.startsWith("__")){
      break
    }
    // 添加行间属性
    ele.setAttribute(key, props[key])
  }
  // 进行回调(添加节点下的子节点)
  children.forEach(element => {
    render(element, ele)
  });
  // 最终把创建好的标签进行插入到对应的位置
  container.appendChild(ele);
}
// 使用模拟的方法创建出一个虚拟dom
var div = <div id='demo'>12345<spam>111</span></div>
// window.root根据index.html中的id选中标签div
render(div, window.root);
```



# 列表循环渲染
由于react中没有for命令，要想实现列表循环渲染的效果，需要借助表达式引入数组的思想。

1. 原理: React中的表达式，如果引入一个数组，最终会把数组中的每一项作为一个子节点进行插入。
2. 方法: 按照数组遍历出的数据，创建出一个新的数组，新数组中的每一项为每个数据对应的jsx结构(html结构)。
而数组中的map方法正好符合该需求，由一个数组映射出另一个数组(数据数组映射出结构数组)。

3. 实例
```js
import React from 'react';
import { render } from 'react-dom';
// 模拟一个数据
var arr = [
  {name: "a", age: 18}, 
  {name: "b", age: 20}
];

var div = (
  <>
    {   
      arr.map( (item, index) => {
        return (
          <div key={index} className={ index == 1 ? 'a' : ""}>
            <span>{ item.name }</span>
            <span>{ item.age }</span>
            { index == 1 ? <span>1</span> : <span>0</span> }
          </div>
        )
      })
    }
  </>
)
render(div, document.getElementById('root'));
```


# 组件

## 原理

1. react底层:  
    1) 通过React.createElement()，创建一个react元素
    2) 通过ReactDOM.render()，把创建出的react元素转换成真实的dom，并且进行插入。
    3) React.createElement()传入的第三个参数往后，都是该dom的子节点。
    4) 子节点可以为普通的字符串，即innerText
    5) 子节点也可以传入React元素(再次通过React.createElement()进行创建，然后传入)

2. 根据子节点可以传递React元素，可以进行组件化开发。
    1) 把复杂，重复出现的子节点，提取出来，进行单独的封装。然后产生对应的React元素对象
    2) 要想使用该节点，只需要传入生成的React元素对象即可。

3. 底层代码封装组件:
```js
import React from "react";
import ReactDOM from "react-dom";

// 创建React元素( 手动创建一个React元素对象，使用两次封装好的React元素对象 )
let reactDom = React.createElement("div", {}, 
    React.createElement("span", {}, "123"),
    // 使用组件
    zujian(),
    zujian()
);

// 封装一个简单的组件(其实这种方法就是后面的函数组件)
function zujian() {
    // 返回一个React元素对象(下面可以继续含有子节点，或者引入其他组件React元素对象)
    return React.createElement("div", {}, 
    React.createElement("span", { style: {color: "#f0f"} }, "组件"), 
    React.createElement("span", { style: {color: "#0ff"} }, "组件"),)
}

ReactDOM.render(reactDom, document.querySelector("#app"));
```

4. jsx语法封装组件
底层还是使用React.createElement()，只不过简化了书写方式，更加简单易懂，使用了语法糖

```js
import React from "react";
import ReactDOM from "react-dom";

/**  底层代码(过于复杂)
// 创建React元素( 手动创建一个React元素对象，使用两次封装好的React元素对象 )
let reactDom = React.createElement("div", {}, 
    React.createElement("span", {}, "123"),
    // 使用组件
    zujian(),
    zujian()
);
*/

/** 使用jsx简化代码 */
let reactDom = ( 
    <div>
        <span>123</span>
        { zujian() }
        { zujian() }
    </div>
)


/**  底层代码(过于复杂)
// 封装一个简单的组件(其实这种方法就是后面的函数组件)
function zujian() {
    // 返回一个React元素对象(下面可以继续含有子节点，或者引入其他组件React元素对象)
    return React.createElement("div", {}, 
    React.createElement("span", { style: {color: "#f0f"} }, "组件"), 
    React.createElement("span", { style: {color: "#0ff"} }, "组件"),)
}
*/

/** 使用jsx简化代码 */
function zujian() {
    // 写的的jsx语法，但是函数实际执行的时候，会把jsx解析成对应的React元素对象
    // 并不是返回的还是jsx语法。只要是jsx语法，在函数实际执行的时候，都会转换成React元素对象
    return (
        <div>
            <span style={ {color: "#f0f"} }>组件</span>
            <span style={ {color: "#0ff"} }>组件</span>
        </div>
    )
}

ReactDOM.render(reactDom, document.querySelector("#app"));
```



## 官方给出的React组件定义方法

1.  函数组件(和自己写的一样)
组件函数内部的this指向undefined。
```js
import React from 'react';

function Abc() {
    return (
        <div>我是一个组件</div>
    )
}

// 组件的抛出
export default Abc;
```


2. 类组件
```js
import React from 'react';

class Abc extends React.Component{
  render () {
    return (
      <div> 123 </div>
    )
  }
}

// 组件的抛出
export default Abc;
```

3. 组件的使用
```js
import React from 'react';
import { render } from 'react-dom';

// 组件的引入
import Abc from './components/abc.js';

var div = (
    <div>
        <Abc></Abc>
    </div>
)

render(div, document.getElementById('root'));
```


4. 组件引入时的注意点
    1) 引入时的名字，要符合大驼峰式命名法(首字母必须大写)。
    2) 原因: `<Abc />`，jsx语法，使用组件。底层会使用React.createElement()，创建实际的React元素对象。
    在创建React元素的时候，就是根据首字母是否大写，来决定该jsx语法是否使用的是组件。如果是小写创建对应的React元素。如果是大写，使用组件创建出组件对应的React元素，即{ Abc() }。



# 组件状态(数据的定义)

其实类组件的本身是没有状态的，它的状态是靠继承React，这样就可以使用React中的一些方法了。

React有一个核心的概念: 只能修改state中自定义的状态(只属于该组件的状态)，如果是传入的状态，是绝对不能修改的。

1. 状态(数据)的定义
    
    1) 在`constructor`中通过`this.state = {}`定义;
    2) 在外部，通过`state = {}`定义
    3) 如果使用了constructor定义了state数据，则外部写的state就会失效，并不会合并和替换

```js
import React from 'react';
class Abc extends React.Component{
    constructor () {
        super();
        this.state = {
            list: [1, 2]
        }
    }
    // 无效
    state = {
        list: [1, 2],
        a: 3
    }
  
    render () {
        return ( ··· )
    }
}
```

2. 状态(数据)的使用

    1)  标签内通过 `{ this.state.*** }`，使用自定义的数据。
    2)  行间通过 `src={ this.state.*** }`，使用自定义的数据。行间属性不需要加:


3. 改变状态(数据)引发重新渲染
    
    1) 直接通过 `this.state.name = ?`，可以对state中定义的状态(数据)进行修改，但是无法进行重新渲染。
    2) 要想进行重新渲染，需要使用`this.setState()`，该方法传递一个对象，传入的对象会与当前的state对象进行合并处理(属性相同，进行替换，无属性进行添加，其它属性不动)。并不会替换state对象。

    3) setState方法，非常强大，无论数据是否发生了变化，只要该方法一经调用。该组件的render重新执行，创建新的React元素对象，并且引发重新渲染。
    如果render中引入了其它的组件，由于jsx的重新解析，所以引入的组件的render也会重新执行，创建出子组件的React元素对象。
    React元素对象重新创建完成，接下来就会重新渲染页面，和vue重新渲染一样，只要该组件中的dom(包括子组件)没有发生变化，是不会重新创建的，而是使用之前的。并且也受key的控制。
    


# 事件的绑定(以及函数的定义)

1. 事件的绑定就是React元素上的一个行间属性。比如绑定点击事件：`onClick = { this.func }`

2. 事件函数(React函数)的定义。有两种方法
    1) 在`constructor`中通过`this.func = ?`进行定义
    2) 在`class`中直接通过 `func = ?`进行定义

3. 事件函数在使用时的注意点
    1) 事件函数，如果使用`func = function`的方式定义，则内部的this指向undefined。指向的并不是触发该事件的dom。如果想要改变this的指向，需要通过`this.func.bind(this)`，这样内部this就指向该组件的环境。
    2) 事件函数，如果使用`func = () =>`的方式定义，则内部的this指向该组件的环境。指向的并不是触发该事件的dom。
    3) 如果想给事件函数传递默认值，也必须使用`this.func.bind(5)`，这样的话事件函数就无法使用event，已经被5给替换掉了。
    4) 如果想要获取到触发事件的dom，可以通过`e.target`获取。

```js
class Abc extends React.Component{
  render () {
    return (
      <>  
        <div onClick={ this.click }>我是一个组件</div>
      </>
    )
  }
  click = (a) => {
    console.log(a);
  }
}
```


# setState函数的详解

1. 同步和异步的判断
    1) 如果setState方法执行的触发源头是事件函数，或者周期函数，则setState对数据的修改是异步的。
    2) 如果setState方法执行的源头，是由定时器(setTimeout和setInterval)触发的，则setState对数据的修改是同步的。

示范: 
```js
class A extends React.Component {
    state = {
        a: 123
    }
    render() {
        return ( <div onClick={ this.func }>{this.state.a}</div> )
    }

    componentDidMount() {
        // 定时器调用
        setTimeout(() => {
            this.cc("sss");
        }, 0)
    }
    // 事件调用
    func = () => {
        this.cc("ddd");
    }

    cc = (c) => {
        // 一次同步执行，一次异步执行
        this.setState({
            a: c
        })
        // 定时器调用，打印出sss，同步执行
        // 事件函数调用，打印出sss，没有打印ddd，异步执行
        console.log(this.state.a);
    }
}
```

2. setState函数的第二个传参的作用(获取数据)

    1) 第二个参数传递的是一个回调函数，并且该函数的执行，是在`render`函数执行后才进行执行。
    2) 该方法可以在setState修改数据后，使用数据。
    3) 无论setState是同步执行还是异步执行，所传递的回调函数(第二个参数)都是在`render`执行后执行。

示范:
```js
class A extends React.Component {
    state = {
        a: 123
    }
    render() {
        return ( <div onClick={ this.func }>{this.state.a}</div> )
    }

    func = () => {
        this.cc();
    }
    cc = () => {
        this.setState({
            a: "dsfas"
        }, () => {
            // 在render后执行，此时数据以及发生变化，就可以使用该数据进行判断或者处理
            console.log(this.state.a);
        })
        // 由于是异步的，所以此时使用数据，还是之前的，如果进行处理就会出现问题
        console.log( this.state.a );
    }
}
```

3. setState的性能优化(只针对异步操作)
    1) 异步优化:  如果setState是异步执行的，并且在render执行前执行了多个setState，对数据进行了修改，会把所有的setState处理完，然后只调用一次render。这也是React优化性能的一种体现。

    2) 其实多个异步setState会进行合并，每个setState都有一个修改数据的对象，然后进行后面的合并前面的操作(相同替换，没有添加的方式，其它不动的方式)。这样就生成了，一个修改数据的最终对象，然后在根据该对象，对state进行修改。由于只修改了一次，所以render才会触发一次。
    从其它的周期函数也可以验证setState只对state操作了一次，其它的周期函数也只执行一次。

    3) 同步正常:  如果是多个setState函数是同步的，则执行多少次setState，render就会执行多少次。

4. setState异步操作的一种现象

```js
class A extends React.Component {
    state = {
        a: 1
    }
    render() {
        return ( <div onClick={ this.func }>{this.state.a}</div> )
    }

    func = () => {
        this.setState({
            a: this.state.a + 1
        })
        this.setState({
            a: this.state.a + 1
        })
        this.setState({
            a: this.state.a + 1
        })
        // 最终a，渲染成了2， 而不是4。
        // 原因: setState是异步执行，而对象中的表达式会立即执行，即this.state.a + 1会立即执行，三个得到的都是2。最终结果，a为2。
    }
}
```

5.  setState的第一个参数的另外一种写法，传递函数(可以解决上例问题)
    1) render执行前，所有异步setState传入的第一个函数参数，会放在一个队列中。在执行时，从队列中依次取出方法，进行执行。

    2)  并且该方法在执行的时候，会传入state对象，返回值也必须是一个对象(修改数据的对象)。

    3) 并且下一个队列中的方法，接收的参数，是上一个函数的返回修改数据的对象。然后根据上一次修改数据的对象，再次进行操作，返回一个修改对象，最终会根据所有的方法返回的对象进行合并操作，然后以合并的最终对象，对state进行数据的修改。然后触发一次render方法进行重新渲染。

    4)并且当所有的队列函数执行完成，才会执行render函数进行重新渲染。然后在触发每个setState函数传入的第二个回调函数(此时在获取a，获取的是4，而不是2，或者3)

```js
class A extends React.Component {
    state = {
        a: 1
    }
    render() {
        return ( <div onClick={ this.func }>{this.state.a}</div> )
    }

    func = () => {
        this.setState((val) => {
            return {
                a: val.a + 1
            }
        }, () => {
            // 获取的是4，因为该函数是在render函数执行完才会执行，但是render是在所有的setState处理完数据后才会执行，所有获取的是4。
            console.log(this.state.a);
        })
        this.setState((val) => {
            return {
                a: val.a + 1
            }
        })
        this.setState((val) => {
            return {
                a: val.a + 1
            }
        })
        // 最终a渲染成4
    }
}
```

6. **如果利用setState修改数据，是在原先数据的基础上进行修改，建议使用传递函数的方式**
可能多个setState设置的数据会有依赖关系，如果不使用传递函数的方式，则依赖会消失。
所以为了保险起见，只要是在原先的基础上修改数据，建议使用传递函数的方式，即使setState只使用了一次。


7. setState的执行，会重新开辟一个对象空间，然后进行属性的合并处理，并不是在state的基础上进行合并。



# 受控组件和非受控组件(表单元素表现明显)

## 受控组件和非受控组价的定义
1. 组件的定义方式有两种: 函数组件、类组件
2. 组件的类型也有两种: 受控组件、非受控组件
    1)  受控组件: 自身没有状态(state中没有自己的数据)，该组件使用的数据全靠外界(使用者)通过行间属性传入。即使用者传递的数据不同，该组价的表现形式不同，完全受使用者的控制。该类组件被称为受控组件。
    总结: 组件内使用的所有数据，都是从props中获取的，该类组件为受控组件
    
    2) 非受控组件: 所有的状态都是组件内通过state进行控制，使用者无法进行控制(所有的使用者使用该组件，表现形式都是一样的。无法通过外界行间传参的方式影响表现形式，即不受使用者的控制。该类组件被称为非受控组件)
    总结: 组件内使用的所有数据，都是从state中获取的，没有从props中获取一个数据，该类组件为非受控组件

## 表单元素的受控组件和非受控组件的表现形式

**表单元素默认为非受控组价**。
表单元素就是一个普通的标签，并不是自定义的组件，但是这种标签被称为内置组件。
但是表单元素的行间可以传递参数，从而影响到表单元素的展示样式，比如value。
如果表单元素的展示样式完全受外界使用者的影响，则该类表单元素就可以被称为受控组件。

### 输入框的受控组件和非受控组件的表现形式

1. 输入框的非受控组件的表现形式，直接书写`<input type="text" />`。
    1) 该标签的表现状态无法受外界的控制，就是一个普通的输入框，可以正常输入。
    2) 使用`defaultValue={ ? }`给input的value附一个初始值，但是后期是可以正常输入的。
    3) 无论哪个使用者使用该标签，它就是一个普通的输入框，顶多赋个初始状态，但是实现的功能完全一样(普通的输入框)，不受使用者的控制。所以该写法为非受控组件。

2. 输入框的受控组件的表现形式，在行间加入`value={ ? }` 和 `onChange={ ? }`
    
    1) 需要注意的是value和onChange必须成对出现，否则控制台报出错误，但是不影响使用。
    2) input标签的展示样式完全受value的控制，即使在输入框中输入内容，只要value赋值的状态没有发生变化，输入是没有效果的，和defaultValue不同。

    3) Change是input上的事件，当输入框中的内容发生变化时，该事件就会触发。
    在input中是可以输入内容的，只不过输入完成，在value属性的作用下有瞬间进行了重新赋值。所以视觉上input没有发生变化，但是Change事件是会触发的。利用该事件是可以获取到input中的内容(没有被替换前)，从而影响value，使输入框具有特殊的功能。

    4) input事件也可以实现效果，但是Change更贴合需求。
    
3. 用户就可以使用value和onChange控制input，定义出不同功能的input标签
    比如只能输入数字，或者只能输入字母，完全根据用户进行定义，这就是受控组件。

```js
class A extends React.Component {
    state = {
        val: "123"
    }
    render() {
        return ( <input text="func" value={ this.state.val } onChange={ this.fun } /> )
    }
    fun = (e) => {
        // 把非数字过滤掉，只能输入数字
        let val = e.target.value.replace(/\D/g, "");
        this.setState( {
            val: val
        } )
    }
}
```


### 选择框的受控组件和非受控组件的表现形式
1. 默认为非受控组件

2. 受控组件，只需要在行间添加上 `checked={ ? } onChange={ ? }` 
这样该选框的状态，受checked完全控制，为true选中状态，为false取消状态。
Change事件，当你点击的时候，触发该事件。可以进行一些操作。
这样使用者可以通过checked的赋值，完全控制该选项框，使其变成了受控组件

3. 案例(做个爱好复选框，点击获取可以获取到选中的所有爱好)
选中和未选中状态的切换，使用到了数组，因为复选框有多个选中状态，所以需要使用数组
数组的筛选(取消选中)，数组是否包含该选项(选中)，数组的添加(选中)
```js
class A extends React.Component {
    state = {
        // 所有爱好
        checkBoxDate: ["篮球", "足球", "排球", "其它"],
        // 选中的爱好
        checked: ["篮球"]
    }
    render() {
        let CheckBox = this.state.checkBoxDate.map( (el) => {
            return (
                <input type="checkbox" 
                    // 只要选中的爱好中有该属性，则该项选中
                    checked={ this.state.checked.indexOf(el) > -1 } 
                    // 点击触发事件，进行选中爱好的添加和移出，从而引发选中项的重新渲染
                    onChange={(e) => {
                        // 如果点击的选项已经存在，则需要进行移除，数组想要引发重新渲染需要赋新数组
                        if( this.state.checked.indexOf(el) > -1 ) {
                            this.setState( {
                                // 进行筛选，不同的保留，相同的移除
                                checked: this.state.checked.filter( (l) => {
                                    return l !== el
                                } )
                            } )
                        }
                        // 否则进行添加
                        else {
                            this.setState( {
                                // 在数组中添加一条选项
                                checked: [ ...this.state.checked, el ]
                            } )
                        }
                    } } />
            )
        } )
        return (
            <div>
                { CheckBox }
            </div>
        )
    }
}
```

### 下拉框
1. 默认是非受控组件
2. 通过给select标签添加 value={ ? } onChange={ ? }，使其变成受控组件。
只要value中给定的值与option中的value相同，默认选中那个。

3. 案例
下拉框只有一种状态，不需要使用数组，复选框需要借助数组。

```js
class A extends React.Component {
    state = {
        // 所有的下拉框
        selectOption: ["篮球", "足球", "排球", "其它"],
        // 标识那个被选中
        select: "篮球"
    }
    render() {
        let selectOption = this.state.selectOption.map( (el) => {
            return (
                <option value={ el }> { el } </option>
            )
        } )
        return (
            // 使其变成受控组件
            <select value={ this.state.select } onChange={ this.change } >
                { selectOption }
            </select>
        )
    }

    change = (e) => {
        // 改变状态
        this.setState({
            // 获取到点击的option对应的值
            select: e.target.value
        })
    }
}
```



# HOC(高阶组件)

1. 概念
    React中组件的写法就是函数。而函数存在一种方式，传入的参数是方法，返回值也是方法，该类组件被称为高阶函数。
    高阶组件的核心也是，传入一个组件，返回一个组件。

2. 作用
    对某个组件进行进一步的包装，使其功能变得更加的强大

3. 一个组件可以进行多次包装
    组件包装完成，返回的还是一个组件函数，我们还可以进一步对该组件进行包装，让其功能越来越强大。

4. 思想以及工作流程
   1) 利用函数，传入一个组件，返回一个新的组件。然后使用者在外界进行接收，这样就得到了一个新的组件(在原来的组件上添加了一些功能)。

   2) 当代码解析到`<新组件 />`时，新组件被激活(如果新组件时类组件，周期开始启动)。那些新添加的功能就会开始工作。

   3) 在新组件的返回值中，一定要存在旧组件(传入组件)的引用，即`<传入组件 />`。当新组件启动后，就开始解析新组件，当解析到`<传入组件 />`时，传入组件就会被激活，处理组件本来的功能。
   
   4) 这样，某个组件就被包裹了一层华丽的外衣，增强了其功能性(本身的功能在HOC中被激活)。

4. 示例
```js
function HOC(Element) {
    return class H extends React.Component {
        componentDidMount() {
            console.log(123);
        }
        render() {
            return <Element />
        }
    }
}

// 需要添加功能的组件
class C extends React.Component {
    render() {
        return ( <div>组件</div> )
    }
}

// 对C组件进行包装得到一个新的组件
let A = HOC(C);

// C组件本身没有打印123的功能，通过HOC给它包裹了该功能
```


# ref

1. 功能
    让js中的数据与使用ref的标签或者组件建立起一种联系。
    通过js中的数据，可以直接获取到标签的dom或者组件的环境。
    不用再通过document.***的方式获取标签

2. 注意
    自定义函数组件不能使用ref，只有内置组件或者自定义类组件可以在行间使用ref。

3. 旧版本的使用方法(性能不是很好)
    给ref赋值字符串，该方法已经不推荐使用。

```js
class A extends React.Component {
    componentDidMount() {
        // 获取
        console.log(this.refs.abc);
    }
    render() {
        // 使用
        return ( <div ref="abc">组件</div> )
    }
}
```

4. 新版本的使用方法
    
    1) 给ref赋值为对象(对象有特殊要求，React中提供了一种方法，可以直接创建出该对象)

```js
// 需要添加功能的组件
class A extends React.Component {
    state = {
        // 创建ref使用的专门对象，不用通过new的方式。
        ref: React.createRef()
    }
    componentDidMount() {
        // 获取
        console.log(this.state.ref.current);
        // ref使用的专门对象，其实就是一个普通的对象，只不过对象内部必须有current属性
        // 可以手动创建该对象 { current: null }
    }
    render() {
        // 使用
        return ( <div ref={ this.state.ref }>组件</div> )
    }
}
```

    2) 给ref赋值为函数(函数也有特殊的写法，需要按规定写)
    函数的执行时间: 
    创建时: componentDidMount前render后，函数执行，所以componentDidMount中可以获取并使用ref
    销毁时: 函数执行
    ref赋值的函数的地址引用发生变化(重新赋了个新函数):  旧函数执行一次，新函数执行一次。
    其中销毁时和旧函数执行时，函数执行传参为null。
    其它时候的执行，传入的参数为当前标签的dom或者当前组件的环境。

```js
class A extends React.Component {
    state = {
        // 用于接收ref
        ref: null
    }
    componentDidMount() {
        // 获取
        console.log(this.state.ref);
    }
    // 通常把函数写在外部，如果写在render中
    // 重新渲染的时候每次都会重新创建函数，新旧函数就会重新执行
    ref = (r) => {
        this.state.ref = r
    }
    render() {
        // 使用
        return ( <div ref={ this.ref }>组件</div> )
    }
}
```


# ref转发

1. 作用
    针对组件提出的一种思想，父组件获取子组件中的某个标签或者某个组件的状态。

2. 思想
    利用行间传参的方式，把父组件创建好的ref函数或者ref对象(新方法)通过行间进行传入。
    由于方法和对象都是引用类型，所以子组件中操作的和父组件中定义的是一个空间。
    这样父组件就可以获取到子组件中的某个标签或者某个组件的状态

3. 测试

```js
class A extends React.Component {
    state = {
        ref: React.createRef()
    }
    componentDidMount() {
        // 获取子组件使用转发ref的dom
        console.log(this.state.ref.current);
    }
    render() {
        // 通过行间传参的方式进行转发
        return ( <B forwardRef={ this.state.ref } /> )
    }
}

// 子组件
class B extends React.Component {
    render() {
        // 使用转发的ref
        return ( <div ref={ this.props.forwardRef }>子组件</div> )
    }
}  
```


4. React官方提出的方法`React.forwardRef()`
    1) 只适用于函数组件，类组件如果想要使用该方法需要进行包装。
    2) React.forwardRef()其实就是一个高级组件。对一个函数组件进行包装，让ref可以进行转发
    
    3) 会把行间ref，作为函数组件的第二个参数进行传入(第一个参数是props)
    4) 行间必须是ref，才能传入。如果写其它的，会放在props中进行传入。

```js
class A extends React.Component {
    state = {
        ref: React.createRef()
    }
    componentDidMount() {
        // 获取子组件使用转发ref的dom
        console.log(this.state.ref.current);
    }
    render() {
        // 调用包装好的组件，ref才起作用(必须写ref)
        return ( <ForwardRef ref={ this.state.ref } /> )
    }
}

// 子组件(必须是函数组件，使用forwardRef进行包装)
function B(props, forwardRef) {
    // 使用转发的ref
    return ( <div ref={ forwardRef }>子组件</div> )
}
// 进行包装
let ForwardRef = React.forwardRef(B);
```


5. ref转发的提出，是为了解决高级组件。
    高级组件是对一个组件进行了包装，所以在使用时通过ref获取到的是包装后的组件环境。原始的组件环境获取不到。为了获取到原始组件的环境，提出了ref转发。
    
    由于高级函数是使用函数组件进行包装，官方就是根据该特点提出的官方方法`forwardRef`



# react中的插槽功能

react和vue的插槽功能不同，没有slot专门替换的标签。而是把传入的内容放到了this.props.children中。具体的使用方式如下:

```js
// 组件的使用
<C>
  <div>123</div>
  <div>456</div>
</C>

// 组件中的处理代码
import React from 'react';
class C extends React.Component{
  render () {
    return (
      <>
         {/* 把接收到的html放在此处 */}
         { this.props.children }
         <div>22221111</div>
         {/* 把接收到的html放在此处 */}
         { this.props.children }
      </>
    )
  }
}
// 组件的抛出
export default C;
```


# context(执行期上下文，用于父子级组件间的传参)

## 旧API的执行期上下文

1. 创建上下文
只有类组件才可以创建执行期上下文。

    1) 限制上下文中数据的类型(需要借助prop-types插件进行限制类型)
    2) 创建上下文
    通过getChildContext函数创建，该函数会在每一次render函数执行完成后执行。
    如果上下文中使用了组件的属性和状态，该操作可以保持上下文与组件的一致性。属性或状态发生变化，render就会执行。然后getChildContext也会紧随其后的执行，更新上下文中的数据。

```js
class A extends React.Component {
    
    // 限制上下文中数据的类型(属性a的属性值必须为number类型的数据，属性b的属性值必须为字符串)
    static childContextTypes = {
        a: ReactPropTypes.number,
        b: ReactPropTypes.string
    }
    // 创建上下文
    getChildContext() {
        return {
            // 往上下文中填充数据
            a: 123,
            b: "shid",
            // c: "asdf"   childContextTypes中没有声明类型的属性，直接定义会进行报错
        }
    }

    render() {
        return ( <div>123</div> )
    }
}
```


2. 获取上下文
函数组件或者类组件都可以获取到执行期上下文。
    1) 子组件必须有一个静态属性`contextTypes`，内部定义需要获取的数据，限制其类型，
    2) 自动把定义好的需要获取的数据，从上下文中拉取下来，放入一个对象中。
    3) 把对象作为函数组件的第二个参数传入。
       类组件把该对象传入constructor中(第二个参数)，进而通过super传入React.Component中，把该对象赋值给`this.context`。
       即函数组件可以通过第二个参数使用，类组件通过this.context使用。

    4) 如果函数组件使用了forwardRef进行了包装(获取ref)，则该函数组件的第二个参数传入的就是     ref，不是获取的上下文对象，会被替换掉，第三个参数也不是上下文对象，无法获取上下文对      象。

```js
// 父组件创建执行期上下文
class A extends React.Component {
    
    // 限制上下文中数据的类型
    static childContextTypes = {
        a: ReactPropTypes.number,
        b: ReactPropTypes.string
    }
    // 创建上下文
    getChildContext() {
        return {
            // 往上下文中填充数据
            a: 123,
            b: "shid",
        }
    }
    render() {
        return ( <> <B /> <C /> </> )
    }
}


// 函数组件获取父组件的执行期上下文，并使用内部的数据
function B(props, context) {
    return <div>{ context.a }</div>
}
// 子组件必须定义的静态属性，内部定义需要获取的数据，限制其类型，
// 函数组件只能这样定义
B.contextTypes = {
    a: ReactPropTypes.number
}


// 类组件获取父组件的执行期上下文，并使用内部的数据
class C extends React.Component {
    // 子组件必须定义的静态属性，内部定义需要获取的数据，限制其类型，
    static contextTypes = {
        b: ReactPropTypes.string
    }

    // 该操作，类组件会自动完成，不需要自己操作
    constructor(props, context) {
        super(props, context);
        // 如果对context进行保存，组件中使用保存的数据。如果上下文发生变化，是不会进行更新的
        // 依旧是初始状态
        console.log(context);
    }

    render() {
        return <div>{ this.context.b }</div>
    }
}
```


3. 修改上下文中的数据
    1) 上下文中的数据不能直接被修改，只能通过属性或者状态间接的修改。
    2) 不在上下文中直接创建数据，而是把属性或者状态推送到上下文中，这样当运用setState对状态或者属性发生变化时，getChildContext函数会重新执行(在render后执行)，获取到新的数据，推送到上下文中。

```js
class A extends React.Component {
    state = {
        b: "789"
    }

    // 限制上下文中数据的类型
    static childContextTypes = {
        a: ReactPropTypes.number,
        b: ReactPropTypes.string
    }
    // 创建上下文
    getChildContext() {
        return {
            a: 123,
            b: this.state.b,
        }
    }

    render() {
        return ( <> <div onClick={this.clic}>sss</div> <C /> </> )
    }

    clic = () => {
        // 修改状态，影响上下文对象中的数据b
        this.setState({
            b: "hhhh"
        })
        
        // 执行setState，会造成render的重新执行
        // 函数组件会重新执行，第二个参数传入最新的上下文
        // 类组件，为了节约效率，不会重新构建对象。所以constructor不会重新执行
        // 所以函数组件直接使用第二个参数，使用上下文的数据，是会刷新的
        // 类组件的constructor使用第二个参数，使用上下文的数据，是不会刷新的
    }
}
```


4. 子组件修改上下文中的数据
    子组件是无法直接修改上下文中的数据的，但是父组件可以往上下文中推入一个方法，专门用来修改上下文中的数据，只要子组件调用该方法，就可以对上下文中的数据进行修改


5. 注意
    1) 子组件先从距离最近的父级上下文中取数据，查询到停止。
       如果某个属性查询不到，会继续向上查询，最终把查询到的数据和并成一个对象。
       查询原则遵循就近原则。

    2) 执行期上下文，能少用就少用，能不用就不用。使用执行期上下文会减少组件的灵活度与广泛性
    
    3) 限制类型，如果写错，控制台弹出警告，并不影响使用，但是必须要写。
    限制类型的作用: 可以让阅读代码者更加清楚的知道哪些数据从上下文中引入。


## 新API执行期上下文

1. 优点
    1) 性能变高
    2) 新执行期上下文，是一个独立的对象，完全脱离组件。如果组件想要使用上下文，只需要进行关联即可，使组件变得更加的纯粹。
    旧执行期上下文，需要在类组件的内部进行创建，没有脱离组件，增加了组件的负担。

2. 原理
    1) 新API执行期上下文，给封装了两个组件(生产者和使用者)。
    2) 生产者，其实就是一个父组件，在该组件的内部封装好了上下文的创建方法。
    和旧API父组件创建上下文，子组件可以使用一样。该组件创建的上下文，该组件的子组件都可以使用。
    3) 使用方法也封装了一个组件(上下文的使用者)。

3. 创建上下文
    1) 创建上下文对象: `let context = React.createContext({});`
       参数为向上下文中传入的初始值，可以不用传递
    2) 创建的上下文对象context中有两个组件(`Provider, Consumer`)，即生产者

    3) `Provider`可以通过行间value属性，向上下文中填充数据。
       属性值是对象，即上下文对象，如果更新替换掉之前的上下文对象


4. 获取上下文中的数据(两种方式)
    1) 通过静态属性contextType引入数据( `static contextType = 上下文对象` )
       通过this.context使用数据

    2) 使用Consumer进行包裹，然后在Consumer的内部写`{ ({b}) => {} }`，就可以获取到上下文中的数据b


5. 测试示范代码
```js
// 创建上下文
let context = React.createContext();

class A extends React.Component {
    state = {
        a: 123, 
        b: 456
    }
    render () {
        return (
            <>
                <context.Provider value={ {
                    a: this.state.a,
                    b: this.state.b
                } }>
                    <B />
                </context.Provider>
            </>
        )
    }
}


// 子组件(两种使用上下文的方式)
class B extends React.Component {
    
    // 获取上下文数据的方法一，必须借助静态属性contextType
    static contextType = context;    
    // 直接等于创建的上下文对象，会自动完成赋值，内部还是使用this.context的方式调用数据
    // 引入那个上下文，就只能获取那个上下文中的数据，如果不存在，并不会向上查询

    render () {
        return (
            <>
                <p> { this.context.a } </p>

                {/* 方法二，借助上下文提供了另一个组件Consumer来获取数据 */}
                {/* 使用那个上下文中的Consumer，就只能获取那个上下文中的数据，如果不存在，并不会向上查询 */}
                <context.Consumer>
                    {/* 内部必须写一个函数，进行数据的获取 */}
                    {
                        ({b}) => {
                            return <p> {b} </p>
                        }
                    }
                </context.Consumer>

            </>
        )
    }
}
```


6. 注意
    1) 一个React.createContext产生的上下文中的Provider不要多次使用。
       如果想多次使用Provider组价，就用React.createContext创建多个。

    2) 如果获取的数据上下文中没有，则为undefined。
    
    3) 如果一个上下文中的Provider组件被多次使用，在使用Consumer或者静态属性contextType=上下 文对象。查询上下文中的数据，遵循的也是就近原则，从距离最近的Provider创建的上下文中获取数据。
       如果没有获取到，是不会继续向上查询的，直接为undefined。
       子级的Provider不会影响父级的Provider创建的上下文。
       这种方式不推荐使用。


7. 新版的强制子组件重新渲染的功能

    1) 新版的Provider组件有一个功能，当Provider组件的行间属性value发生了变化(对象的引用值发生变化)。会造成Provider下的所有子组件强制重新渲染，即使是子组件中的子组件也会强制重新渲染。

    2) 当一个组件进行重新渲染的时候，是会先执行shouldComponentUpdate()，当返回false，会截止当前组件的重新渲染，即render不在执行。

    3) 但是Provider的强制重新渲染，会绕开shouldComponentUpdate，直接执行render，无法进行重新拦截，导致性能优化直接失效。

    4) 解决方式:
    方式1: 单独创建一个对象，该对象以后不在进行重新创建，把该对象赋给Provider的value。
    这样当组件进行重绘的时候，解析到value，发现索引没有发生变化，子组件正常重新渲染，正常走shouldComponentUpdate函数，可以正常拦截优化性能。
    方式2: 在state中单独定义一个属性，指向一个对象，然后把该对象赋给Provider的value。
    虽然setState会给state重新开辟一个空间，然后进行属性的合并。但是单独定义的对象没有重新构建对象空间，所以地址引用还是之前的。


# PureComponent(纯组件)

1. 作用
    避免不必要的重新渲染(减少render的使用次数)，从而提高效率。
    

2. 原理
    1) PureComponent是一个封装好的组件。
    2) 底层是借助shouldComponentUpdate进行实现，拦截render的执行。
    3) 原理
    类似封装库，把shouldComponentUpdate要实现的拦截功能写好，然后封装在一个组件中。
    让其它组件继承该组件，就会继承该组件写好的shouldComponentUpdate(原型上的方法)。
    当其它组件激活时，自己的shouldComponentUpdate不能定义，这样就会顺着原型链找到封装好的shouldComponentUpdate进行工作。

    4) 正常类组件在定义的时候，继承的是Component，如果想要其变成纯组件，需要继承PureComponent，PureComponent组件继承的是Component。

    5) 相当于，在原型链的中间又加了一个原型。当组件激活时，生命周期函数就会在适当时候执行，本组件中找不到该生命周期函数，就会沿着原型链找。


3. 使用示范代码
```js
// 使A组件变成纯组件，改变继承的组价
class A extends React.PureComponent {
    render() {
        return <div>123</div>
    }
}
```


4. 函数组件变成纯组件
    1) 函数组件没有继承关系，无法直接继承PureComponent。
       需要使用`React.memo()`进行包装，返回一个新的组件(高级组件的思想)。

    2) 原理
    返回的新组件是一个类组件，render中激活传入的函数组件。
    当新组件需要重新渲染时，执行shouldComponentUpdate(继承的方法)。发现达不到重新渲染的条件，返回false，阻断render的执行。则函数组件也不会被重新解析渲染，以此间接的实现函数组件具有纯组件的功能。

    3) 仿写memo方法
```js
function memo(Element) {
    return class C extends React.PureComponent {
        render () {
            return <Element {...this.props} />
        }
    }
}
```

4. 注意点
    1) PureComponent中的shouldComponentUpdate使用的是**浅层比较**，即只比较第一级属性的地址。

    2) 纯组件的现象
    如果状态或者属性是一个对象或者数组，但是状态和属性在发生变化时，只操作了内部的一个属性(比如push)，地址引用没有发生变化。
    这样shouldComponentUpdate在进行比较时，一看地址没有变化，直接返回false，阻断重新渲染。
    造成了数据变化，但是没有引发重新渲染的现象。

    3) 所以组件的状态或者属性发生变化时，最好整个给它改掉(重新构建数据空间)。

    4) PureComponent的产生就是为了优化性能，如果采用了深层比较，反而浪费了性能。



# render props

执行期上下文中的Consumer组件，就使用了该思想。

1. 要解决的问题
这是一种封装组件思想，作用是提取公共代码，相当于数据加工厂。
比如有两个组件，它们获取的初始数据相同，对数据的处理过程相同。但是使用最终数据的进行渲染的样式不同。

根据这一现象，解决的方式有多种: 第一、利用HOC进行包装，把相同的处理数据的过程进行提取。第二、使用render props进行解决。


2. 实现原理
    1) 定义一个组件
    2) 该组件的使用方式，子节点必须传入一个方法。该方法在组件内部的render会进行触发，这样父组件就可以接收到处理完成的数据。直接使用处理完成的数据进行显示不同的样式。
    3) 如果定义的组件是一个纯组件，为了优化性能，需要把子节点传入的方法定义在外部。
       这样使用该组件的父组件在render重新渲染的时候，传入的子节点函数索引是相同的，不会造成重新渲染。
       如果直接写在组件的子节点位置，父组件render重新执行时，函数会重新构建，传入的索引不同，即使其他数据没有发生变化(不应该重新渲染)，也会造成重新渲染。


3. 示范代码
```js
class A extends React.Component {
    render() {
        return <B>
            {
                // 传入子节点方法，获取数据，构建React元素结构
                // 如果是纯组件，需要把该方法定义在render的外部
                ({a}) => {
                    return <div>{ a }</div>
                }
            }
        </B>
    }
    
}

// 使用render props思想开发的处理数据的组件
class B extends React.Component {
    state = {
        a: 123
    }
    
    componentDidMount() {
        this.setState({
            a: 456789
        })
    }
    
    render () {
        // 调用子节点传入的方法，传入处理完成的数据对象
        return this.props.children({...this.state})
    }
}
```

4. 比如，有两个组件，一个组件显示鼠标相对于容器的坐标，一个组件做方块跟随鼠标运动。
这两个组件，对坐标的处理时相同的，最后都是经过种种处理得到当前鼠标相对于容器的位置。
这样就可以把坐标的处理提取出来，做成render props组件。


# portals( 插槽 )

1. 功能
    1) 该插槽并不是slot插槽，不是服务于组件。
    2) 功能更加强大，可以把某个组件插入到指定的位置。
    3) 但是并不会改变React元素树的结构层级。

2. 实现方法加分析
通过`ReactDOM.createPortal()`，把指定的组件插入到指定的位置。
ReactDOM.createPortal()返回的也是一个React元素对象，但是有了特殊的标识，指定了位置。

```js
class A extends React.Component {
    render () {
        return (
            <>
                <div className="bbb">
                    <B />
                </div>
            </>
        )
    }
}

// B组件在使用时，生成的真实的dom，就会放入指定的位置
class B extends React.Component {
    render () {
        // div元素处于.bbb的下面，作为子元素。在渲染的时候，生成的真实dom也会放入.bbb的下面
        // 但是使用createPortal给div元素指定了位置。
        // 虽然在React元素树上div元素依旧处于.bbb的下面，但是在渲染的时候，div会放入#aaa的下面
        return ReactDOM.createPortal(<div>123</div>, 
            document.querySelector("#aaa"));
    }
}
```

3. 原理

    1) 正常React组件在解析的时候，会从上到下构建一个React元素树
    2) 如果React元素没有进行特殊的操作，ReactDOM.render在解析React元素树生成真实dom的时候，遵循子元素放入父元素的下面，作为子节点的规律创建真实的dom树。
    3) 如果某个React元素在生成的时候，加入了特殊的标记，指定了一个位置
    4) ReactDOM.render在解析该React元素时，本来该元素生成的dom要放入父元素的下面，但是一看有特殊标识，指定了位置，则ReactDOM.render就会把生成的真实的dom，放入指定位置。
    5) 而该元素的子元素，在生成真实的dom的时候，还是遵循子元素放入父元素生成的dom的下面的规律。就会造成一整条分支都跟随该元素移动了位置。
    6) 但是React元素树的结构没有变化。即元素树与dom产生了不对应的关系。


4. 事件冒泡的注意点
    1)  在React中，对事件进行了封装处理，当事件进行冒泡时，不遵循真实的dom，而是遵循React元素树。
    2)  比如: 在真实dom中两个不是父子级元素的两个标签，却产生了冒泡现象。原因就是在React元素树上，它们处于父子级元素的关系，但是其中一个组件通过createPortal改变了真实dom的位置。
        




# 错误边界

1. 解决的现象
    1) 表现
    React中有一个强大的现象，当一个组件在渲染的过程中发生了错误，会导致整个React元素树的移除，页面上就什么都看不到，即使是一个非常不起眼的小组件出现问题，也会造成整个页面的崩塌。

    2) 原因
    组件渲染时出现问题，自己处理不了，会往上传递，看看父组件能否进行处理。
    父组件一看也处理不了，继续向上传递，直到传递到根组件，根组件一看也处理不了，组件就会崩塌。根组件一崩塌，下面即使是没有问题的分支，也显示不出来，整体进行了移除。

    3) 解决方式
       父组件进行错误的拦截，但是无法阻止错误的上传，组件会继续崩塌，解决方式父组件拦截到错误后进行一些特殊的处理。



2. 处理方式1
利用一个静态方法`getDerivedStateFromError`，进行错误的拦截。
该方法的执行是在渲染前并且子组件的渲染过程中出现了问题。
只需要在该函数的内部修改状态，此时render就会重新执行，重新渲染。
在本次渲染的时候，屏蔽掉出错组件的渲染，重新渲染没有错误，构建正常React树。
页面就可以正常显示。

由于getDerivedStateFromError是一个静态的方法，是无法通过this获取到state的。但是该方法有个返回值，可以覆盖掉之前的state。

```js
class A extends React.Component {
    state = {
        a: true
    }
    // 如果子组件渲染时发生了错误，该函数才会被激活，然后该函数在渲染前进行执行
    static getDerivedStateFromError() {
        // 修改状态，屏蔽出错的组件
        return {
            a: false
        }
    }

    render () {
        return (
            <div className="bbb">
                4444
                { this.state.a && <B /> }
            </div>
        )
    }
}

// 报错的子组件
class B extends React.Component {
    constructor () {
        super();
        
        // 没有aaa，进行报错
        console.log(aa);
    }
    render () {
        return <div>122</div>;
    }
}
```



3. 处理方式2

使用函数`componentDidCatch`进行处理，该函数的执行时间是，子组件出现问题并且页面重新渲染后。
处理错误的原理:
    子组件发生了错误，向上传递，父组件处理不了，React元素树崩塌。然后`componentDidCatch`运行，修改状态，重新渲染，跳过错误组件，重新构建正常的React元素树，页面正常显示。

```js
class A extends React.Component {
    state = {
        a: true
    }
    // 如果子组件渲染时发生了错误，该函数才会被激活，然后该函数在渲染完成后进行执行
    componentDidCatch() {
        // 修改状态，屏蔽出错的组件
        this.setState({
            a: false
        }) 
    }

    render () {
        return (
            <div className="bbb">
                4444
                { this.state.a && <B /> }
            </div>
        )
    }
}

// 出现问题的子组件
class B extends React.Component {
    constructor () {
        super();
        
        // 没有aaa，进行报错
        console.log(aa);
    }
    render () {
        return <div>122</div>;
    }
}
```


4. 方式2处理错误的缺点
由于`componentDidCatch`的执行周期是在页面渲染完成后运行。React元素树，先卸载后创建，浪费了效率。

所以通过使用`componentDidCatch`记录错误的信息，`getDerivedStateFromError`处理错误信息


5. 两种方式无法扑捉到的错误
    
    1) 本组件中出现的问题
    2) 异步错误
    3) 事件错误

本组件的错误可以用父组件处理。异步错误和事件错误，只能使用try - catch进行处理



# React中的事件机制

1. React中对事件的处理真实的过程
    1) React中对事件的绑定，是进行了优化的，采用了更高效的方式。
    React会先在document上绑定所有可以进行冒泡的事件。
    
    2) 开发者在React元素上通过on绑定的事件，比如onClick，其实并没有在真实的dom上绑定事件。
    
    3) React使用的方式是，点击该真实的dom，事件就会进行冒泡，最终冒泡到document上。
       触发React在document上绑定的点击事件，然后分析触发的事件源。
       随后查询React虚拟dom树，从React元素树中找到对应的React元素，直接执行注册的点击函数。
       然后顺着React虚拟dom树，向上查询，只要有父元素使用了onClick注册事件，就会执行注册函数。产生冒泡的现象。

2. 分析改变真实dom的位置，但是冒泡却依旧按照React虚拟dom树的结构进行冒泡的原因

    1) 某个元素绑定了事件，但是该元素通过createPortal改变了真实dom的插入位置。
    2) 当点击该元素的时候，其实是正常按照真实dom树的结构进行冒泡，直到document。
    3) 然后React手动调动对应的事件，然后从事件源的位置，顺着React元素树进行查询。
    4) createPortal虽然改变了真实dom的位置，但是在React元素树中的位置并没有发生变化。所以产生了该现象。
    如果React元素的位置也变到指定的位置，则React查询的方向和真实dom冒泡的方向就会相同。
    5) 所以这种现象都是假象，是React搞的鬼。


3. React中的事件对象event
    1) event的原理
    其实React中的事件对象event，也不是原始的事件对象event。
    原始的事件对象是document的事件对象，要来没什么用，所以React就舍弃了真实的event。
    自己创造了一个event，传入的就是React自己高度合成的event。

    2) event的优化
    并且React为了节约效率，先把event对象创建好了，平时的时候内部的每个属性都为null。
    当需要的时候生成对应的event对象，然后传入对应的事件中，当事件函数执行完，event对象中的属性就会进行初始化，为冒泡或者下一次事件的触发做赋值准备。


4. 根据React绑定事件的方式和event优化的原理，产生了一些现象

    1) 父元素事件先触发，子元素事件在触发，不符合冒泡原理。
       该现象产生的必要条件:
       父元素是通过dom，直接绑定的事件而不是通过React语法绑定的事件。
       子元素的事件是通过React语法进行绑定的事件。
       现象分析:
       通过React语法绑定事件，并不会真正的绑定到该dom上。触发时，进行进行冒泡。
       如果在真实dom冒泡的过程中，在某个父dom上直接绑定了事件，则先触发该事件。
       然后在继续冒泡，直到到达document，触发React绑定的点击事件。
       然后在反过来执行对应的事件函数。
       造成了表象上父组件的事件先执行，子组件的事件在执行的现象。
    
    2) 真实dom上绑定的事件，阻止事件冒泡，React绑定的事件就会失效。
       在冒泡过程中的某个真实dom绑定的事件上阻止了事件冒泡，则document上的事件无法执行，无法进行分析自动调动对应的方法，产生事件失效的现象。

    3) 事件函数中异步使用event，需要进行特殊的处理 
       由于React对event进行了优化，事件执行完，event就会初始化，为下次的传入做准备。
       如果异步使用event，使用的是初始化的even。
       不会获取到自己的event，和其它事件的event，只有当任务队列空时，异步函数才会执行，此时事件函数以及处理完毕，event已经进行了初始化。
       所以异步直接使用event会出现大问题。
       解决方式: 
       在事件中调用e.persist()，进行事件对象的固化。
       原理可以看成，把e克隆一份，然后在赋给e。React在操作React中创建的事件对象event时，就与该事件中的e没有关系了。

    4) stopPropagation阻止冒泡的原理
       stopPropagation阻止的是沿React元素树查询的过程，当查询到该位置，阻止查询，则上方的事件函数不在执行，造成阻止事件的表象，其实并没有组织真实dom的冒泡。



# React的渲染过程
    
## React初始化渲染
1. 创建React元素树(为了辅助React虚拟dom树的产生， 就是一个普普通通的对象)
    1) 使用React.createElement()创建
    2) 使用语法糖JSX创建
    3) 需要注意的是，React.createElement是没有执行组件函数的功能的。
    4) 组件函数的执行是在生成虚拟dom树的时候执行的。
    5) `<></>或<React.Fragment></React.Fragment>`不被添加在React元素中，只是起一个辅助作用

简单模拟React元素树的结构，大体表现一下:
![Image text](page/44.jpg)。


2. 根据React元素树创建React虚拟dom树

虚拟dom树中的节点类型有一下几种:
    1) 文件节点: 通过document.createTextNode()创建
    2) dom节点:  通过document.createElement()创建
    3) 空节点:   null、undefined、false、true。创建占位符(空对象)
    4) 组件节点: 创建组件节点对象，执行对应的函数
    5) **注意**； 每个节点都是一个React创建的对象，内部有个属性，指向document创建的dom。
       并不是，直接进行创建。
       该对象开发者是获取不到的。
       由于`<></>或<React.Fragment></React.Fragment>`不被添加在React元素中，所有创建节点对象时也忽略它，不会创建。
       类组件，render之前的周期函数照常执行，componentDidMount在创建完该组件的虚拟dom(子组件也处理完毕)，把该方法加入队列中。当页面渲染完成后，依次从队列中取方法执行。
       在生成虚拟dom的时候，遇到组件，先执行组件。子组件解析完成，本组件的虚拟dom才会创建完成，才会把本组件的componentDidMount放入队列中。
       **所以子组件的componentDidMount先执行**。


示范说明:
```js
function B() {
    return <>
        <div>
            { null }
            123
        </div>
    </>
}
class C extends React.Component {
    render () {
        return <div>789</div>
    }
}

let A = <div>
    lsz,
    <C />
    <B />
</div>
```
生成的React虚拟dom树如下:
![Image text](page/51.jpg)。


3. 把虚拟dom树转换成真实的dom树，渲染到页面上
    按照虚拟dom树的节点对象，把对象内部产生的dom放入指定的位置(可能存在位置的改变)。
    渲染完成后，对虚拟dom进行保存。
    执行队列中的componentDidMount方法。


## React重新渲染的过程
    当类组件的状态发生变化时，
    触发一系列的周期函数，执行render时，又会产生一个React元素树。
    又开始解析新生成的React元素树。

1. 从初始渲染时创建的虚拟dom树中找出该组件所在的位置进行分析。并且为了节约性能，进行了大量的复用。

    1) 查看React元素与对应位置(React元素的第一个子元素，与虚拟dom树的第一个子元素)的节点类型是否相同(type是否一样)。如果一样，dom复用(不在创建新的dom，直接使用之前的dom)。

    2) 如果复用的节点有属性发生了变化，则进行记录，不立即进行修改。

    3) 如果是类组件，类组件环境复用。
       如果render中生成的React元素，使用了组件的状态，从复用的环境中获取。

    4) 递归分析子节点:
        普通节点，递归处理。
        函数组件，重新执行函数组件获取其对应的React元素，进行递归处理。
        类组件，调用重新渲染的周期函数，render执行后，得到React元素，进行递归分析。
        **重点: 如果一个函数组件或者类组件，在重新渲染时没有产生新的React元素，而是使用之前的。函数组件或类组件的重新渲染生命周期函数是不会重新执行的，直接使用之前生成的虚拟dom，包括该组件的子组件。会认为没有变化，节约性能**
        比如: return 中没有使用`<B />`(重新渲染时解析成新的React元素)，而是使用{ b }(b是B组件的React元素，但是在重新渲染时b没有更新)，此时B组件就不会进重新渲染(函数不会执行)
    
    5) 如果节点类型不同，则该节点对象，以及该节点对象的子节点对象会全部卸载，并不会立即卸载。   也是先记录下来。并且整个节点以及子节点都不会再进行比较，而是根据React元素产生一条新的  节点对象。
       如果节点是函数节点(包括子节点)，会先创建新的函数节点，然后执行函数，产生对应React元素，进行分析，生成对应的节点。
       如果节点是类节点(包括子节点)，会重新执行创建生命周期，生成全新的类组件环境。根据render产生的对应React元素，进行分析，生成对应的节点。
       

    6) 如果没有找到对应的节点。
       虚拟dom树上有两个节点，新React元素中有三个节点，最后一个节点找不到，重新创建一个节点对象，记录挂载的位置，不会立即挂载。

    7) 如果有多余的节点
       虚拟dom上有三个节点，新React元素中只有两个节点，dom树上的最后一个节点没有找到对应的节点，记录卸载位置，不会直接进行卸载。

    8) 处理完成，虚拟dom树开始更新，复用dom属性更新，节点更新，节点卸载，节点挂载。
       节点卸载的方式是递归卸载，按照dom树的结构，把一条分支完全卸载完成，在卸另一个分支的结构。
       如果是类组件被卸载，触发componentWillUpMount周期函数，所以所有该函数的运行是在整个创建周期之后(包括子组件的创建)。
       然后按照卸载的顺序触发componentWillUpMount周期函数

    9) 把修改后的dom，渲染到页面上。


2. dom更新完成，触发对应类组件中重新渲染过程中render后面阶段的生命周期函数。
    
    1) 从队列中读取执行，**子节点的周期函数先执行**。

    2) 父节点的render一执行，产生新的React元素，分析React元素，替换节点。此时如果遇到子节点，会进入子节点函数进行处理。此时父节点的render后面一系列声明周期函数还没有解析到。

    3) 当子节点render解析完成，把对应的生命周期函数放入队列中(可能父组件的render还有其他子组件分支，还没有解析完，所以不会进行执行，先进行存放)。
    
    4) 当所有子组件解析完成，父组件的生命周期函数才会存入队列中，最后存入的。

    5) 所以子组件的先执行，父组件的后执行。


## 重新渲染过程中样式复用引发的一些Bug

    
1. dom的复用出现的问题，通常表现在input标签上

需求:  有两个输入框，一个输入框输入姓名，一个输入框中输入学号。这两个输入框不同时显示，点击按钮进行切换。

代码: 
```js
class C extends React.Component {
    state = {
        a: true
    }

    render () {
        // 创建两种输入框需要的React元素
        let inp = null;
        if(this.state.a) {
            inp =  <div> 姓名: <input type="text" value="adfa" /> </div>
        }else {
            inp =  <div> 学号: <input type="text" value="adfa" /> </div>
        }
        return (
            <div>
                { inp }
                <button onClick= { this.clic } >按钮</button>
            </div>
        )
    }

    clic = () => {
        this.setState({
            a: false
        })
    }
}
```

现象:  文字进行了切换，input标签没有被切换，还是之前的input标签，表现为输入的内容依然存在

原因:  进行重新渲染时，input产生了复用。
       最外界div与之前相同，进行复用，行间属性没有变化，不操作
       input的父级div也进行了复用，行间属性没有变化，不操作
       文本节点还是文本节点，没有发生变化，节点复用，但是内容发生了变化，进行记录
       input还是input标签，于是也进行复用，一看行间属性没有变化(value不考虑，即使写上也不考虑)。input标签进行复用，不进行操作。
       在最终渲染时，修改记录的文本，input不进行操作。



2.  组件复用出现的现象(在结构的前面新添加一个组件，表现为后面添加了组件)

需求: 列表循环渲染，数组中是相同的组件，当按键按下时，在数组的前面新添加一个组件。并且每个组件可以修改内部的状态，点击一下数字加1。

现象: 当改变两个组件的状态，点击添加按钮，本该在上面创建一个全新的组件，状态为0，另外两个往下移。实际为在最后新创建了一个组件，状态为0，其它两个组件没变化。

代码:
```js
class C extends React.Component {
    state = {
        a: [<D />, <D />]
    }
    render () {
        return (
            <div>
                { this.state.a }
                <button onClick= { this.clic } >按钮</button>
            </div>
        )
    }
    // 在前面新创建一个组件
    clic = () => {
        this.setState({
            a: [<D />, ...this.state.a]
        })
    }
}

class D extends React.Component {
    state = {
        a: 0
    }

    render () {
        return (
            <div>
                { this.state.a }
                <button onClick= { this.clic } >按钮</button>
            </div>
        )
    }

    clic = () => {
        this.setState({
            a: this.state.a + 1
        })
    }
}
```

原因: 类组件环境的复用，在React元素生成的时候，D组件从两个变成了三个。
      在创建真实的dom的时候，发现第一个和之前的是同一个类组件，于是进行环境的复用，这样在从环境中取值的时候，取得还是之前环境状态中的值。
      到最后，发现最后一个组件没有匹配到，于是才进行重新的创建，状态为0。

总结:
    需求中如果类组件需要重新创建(状态初始化)，或者在某个类组件的前面在重新添加一个新的全新的相同组件时，**需要特别的注意环境复用的问题**。


3. 容易忽略的空节点问题
还是输入框切换的案例，如果换个写法，会发现可以实现切换效果了。原因就是空节点在作怪，引发了节点的重构。

```js
class C extends React.Component {
    state = {
        a: true
    }
    render () {
        return (
            <div>
                { this.state.a && <input type="text" /> }
                { !this.state.a && <input type="text" /> }
                <button onClick = { this.clic } >按钮</button>
            </div>
        )
    }
    // 在前面新创建一个组件
    clic = () => {
        this.setState({
            a: false
        })
    }
}
```

分析: 
    共有两个{}，当一个满足的时候，另一个为false，而false创建的是空节点。
    当下一次状态改变后，满足条件切换，空节点变成input节点，经过对比发现节点类型不同，直接舍弃之前的空节点，创建input节点。
    所以input可以正常的切换。


## key值的作用
    改变同等级下查询的顺序。
    
1. 当重新渲染时，默认第一个元素与第一个查询对比，即默认从第一个开始按照顺序进行查询。
    如果React元素上有key值，就不会按照正常的顺序进行查询，而是从dom树上同一级的节点上获取相同key值的节点，如果查询的节点类型与React元素的类型相同，进行dom或者环境的复用。
    
2. 虽然此处进行了复用，但是复用的位置是不同的，比如元素是第一个，但是复用了Dom树上的第二个节点。
    就会dom树对应的第一个节点踹掉，把复用的节点拿过来，但是不会立即踹掉，先进行标记。把节点对象拿过来。
    为了以后的复用(踹到的节点可能也有key，后面的节点可能要进行复用，所有不会立即踹掉。

    和新创建相比，少了创建dom的步骤，一定程度上优化了性能。

3. 如果没有找到key对应的值，直接进行重新创建，在相同位置的节点做个标记，表示该节点以及子节点将要被卸载，换成新的节点。

4. 而那些没有key属性的元素，正常按照对应位置的DOM节点进行比对，如果正常位置的节点有key，会认为节点不同，直接进行创建。在该位置做标记，表示该节点要被卸载，换成新的节点

5. 如果查询的位置没有key值，则正常比对，按照类型比对，相同就复用。



## 渲染总结
    
1. 根据React元素，产生对应的节点，位置与React元素所处位置相同。
    即使使用了key值复用节点，把其它位置的节点拿过来。React就会认为，React虚拟dom树上对应位置的节点是不同的，使用复用的节点替换掉当前的节点。

2. 重新渲染时，对dom树的操作，都是先进行记录，然后最后统一变化。

3. **React在调用函数组件时，会默认传入两个参数，默认为两个空对象**



# 开发工具
    
## 严格模式

有一个组件`React.StrictMode`，该组件不会参与到UI渲染。
<></>即React.Fragment也不会参与UI渲染。

该组件的作用是: 在该组件的内部(子组件)，如果存在不合适的代码，会报出一些警告的消息，提示开发者。

不合适的代码:
1. 使用旧版的生命周期
2. 使用过时的ref赋值字符串的方式
3. 使用废弃的findDOMNode方法，该方法是很早的方法，用来获取dom。ref已经很好的解决了该问题，所以该方法被移除掉了。
4. 检测周期函数中意外的副作用
    1) 副作用:  一个函数中，做了一些影响函数外部数据的事情。如: 异步请求，改变参数值，setState，本地存储。 没有副作用的函数，被称为纯函数。
    如果开启了严格模式，副作用代码仅能出现在componentDidMount、componentDidUpdate、componentDidWillUnMount这三个声明周期函数中。

    2) 严格模式下，是无法监控到具有副作用的代码的，所以不会弹出警告。
    它的处理方式是，把那些不能写副作用代码的周期函数(除那三个之外)，故意调用两次。本来执行一次。
    如果它修改了外部的数据(产生了副作用)，调用两次和调用一次，差别是很大的，有可能出现问题。以这种方式提示开发者。
    把代码发布出去，是不会执行两遍的，只在开发环境下有效。


5. 使用过时的context API(旧版的执行期上下文)


## Profiler(性能分析工具)

该组件并不是在代码中使用，而是浏览器的一个开发工具。
作用: 分析每一个组件重新渲染所需要的时间，可以找到那些比较耗时的组件进行优化。


# 行间传参的使用介绍(props)
从props中获取的数据，都是只读的，不能进行修改，如果想要修改，就需要在state中在定义一个属性，对传递进来的数据进行保存，修改和使用都用state中保存的数据。
如果行间传进来一个对象或者数组，是可以对内部的某个属性进行修改的，但是不推荐使用该方式。

React开发的铁规律: 子组件不能操作父组件中的数据，实现想操作，只能父组件传递一个修改数据的接口，调用接口进行数据的修改。



# 属性默认值(给props赋默认值)
1. 通过 `static defaultProps = {}`，可以对props赋默认值。
底层是props与defaultProps对象进行混合。

2. 在组件外部，通过`组件名.defaultProps = {}`，可以对props赋默认值。


# props的数据校验

props的数据校检发生在props赋初始值的后面，constructor的前面。

要想完成数据的校验，需要借助一个新的插件: `npm install prop-types -D`
该插件的介绍网址(数据校检的方法)   https://github.com/facebook/prop-types


1. 常用的数据校检的方式

    1)  `PropTypes.string` 表示传入的数据必须是字符串类型
    2)  `PropTypes.number` 表示传入的数据必须是数字类型
    3)  `PropTypes.bool`   表示传入的数据必须是布尔类型
    4)  `PropTypes.func`   表示传入的数据必须是方法
    5)  `PropTypes.object` 表示传入的数据必须是对象
    6)  `PropTypes.array`  表示传入的数据必须是数组
    7)  `PropTypes.any`    表示传入的数据任意，配合isRequired的使用，设置必填

    8)  `PropTypes.node`   表示传入的数据是可以渲染的数据(jsx，字符串，数字 ···)
    9)  `PropTypes.element` 表示传入的数据必须是React元素对象( jsx和React.createElement() )
    10) `PropTypes.elementType` 表示传入的数据必须是React元素类型( 组件名，即组件函数 )
        使用方式:  `let V = this.props.V;   <V />`。
        把组件构造函数传入，子组件中创建传入组件的React元素对象
    11) `PropTypes.instanceOf(构造函数)` 表示传入的数据的原型链上必须有某个构造函数的原型。
        用于限制传入的对象，必须是由某个构造函数产生的。
    12) `PropTypes.oneOf(数组)` 表示传入的数据必须是数组中的某一项(相等)。
    13) `PropTypes.oneOfType(数组)` 表示传入的数据的类型必须是数组中的某一项。
        比如: `PropTypes.oneOfType([PropTypes.number, PropTypes.string])` 

    14) `PropTypes.arrayOf(PropTypes.XXX)` 表示传入的数据必须是一个数组，并且数组的每一项必     须为限制的类型。
        比如: `PropTypes.arrayOf(PropTypes.string)`  数组中的每一项必须为字符串，有一项不是字符串就会弹出警告。

    15) `PropTypes.objectOf(PropTypes.XXX)` 表示传入的数据必须是一个对象，并且对象的每个属      性的属性值必须为限制的类型。
        比如: `PropTypes.objectOf(PropTypes.string)`  对象的每一个属性值必须为字符串，有一项不是字符串就会弹出警告。
    
    16) `PropTypes.shape(对象)`  表示传入的数据必须是一个对象，并且每个属性的属性值的类型必     须与设置的类型相同。
        比如: `PropTypes.shape( { a: PropTypes.string.isRequired } )`  传入对象中的a属性对应的属性值的类型必须为字符串且必须传入，可以限制所有的属性。
        甚至可以对象嵌套对象：`PropTypes.shape( { b: PropTypes.shape{ c: XXX } } )`  表示传入的对象中，有一个b对象，该对象中的c属性对应的属性值的类型必须为设置的类型，还可以继续嵌套。
        结合数组的arrayOf使用: `PropTypes.arrayOf(PropTypes.shape({ a: PropTypes.XXX }))`
        表示传入的必须是一个数组，并且数组中的每一项必须为对象，然后对象中的属性值的类型必须符合设定的。 
    
    17) `PropTypes.exact(对象)`  和shape的功能一样，用法也一样，但是限制的更加准确。
        exact对传入的对象属性有严格限制，不能有多余的属性，每个属性都必须存在验证方法。
        而shape对传入对象属性没有限制，只验证规定的属性，其它属性不管。

    18)  `PropTypes.isRequired` 表示该属性必须传入，可以与其它的验证混合使用。
        比如: `PropTypes.number.isRequired`  表示传入的数据必须是数字类型且必须传入
        混合使用的方式 `PropTypes.***.isRequired`

    19) 自定义验证方法:  `function(props, propName, componentName) {}`。
        props为当前组件的props对象，propName为验证的属性名，componentName为当前的组件名
        该方法有返回值，返回new Error()，返回一个创建好的错误，控制台就会弹出该错误提示。说明数据不合理，该错误是自定义的。
        
 
2. 注意: 
    1)  如果行间传入的是null或者为undefined，React会认为没有传递，属性不同弹出警告。如果设置了必填项`isRequired`，则会弹出警告

    2)  数据校检，只发生在开发阶段，即在控制台打印出警告，提示开发者。对数据没有任何影响，传入的数据正常使用。

3. 使用示范代码如下
```js
import React from 'react';
// 引入数据校检的插件
import PropTypes from "prop-types";

class Abc extends React.Component{
  // 进行校验
  static propTypes = {
    name: PropTypes.string,  // 传入的name必须是字符串

    salary: function(props, propName, componentName) {
      if (props[propName] < 10000 ) {    // 如果传入的值小于10000，抛出错误
        return new Error(
          `${componentName}组件内，传入的${propName}小于10000`
        );
      }
    },
  }

  render () {
    return ( <div>{ this.props.name }</div> )
  }
}
// 组件的抛出
export default Abc;
```

4. prop-types组件的引入问题
    1) 有些版本可以通过`import Proptypes from "prop-types"`进行引入
    2) 有些版本需要通过`import ReactProptypes from "prop-types`进行引入。
       如果还是使用Proptypes进行引入，控制台会弹出'Proptypes' is not defined  no-undef这样一个错误



# 生命周期函数

## 旧生命周期

1. 创建阶段
    
    1) 状态和属性的初始化   `constructor()`  
    不能使用setState

    2) 虚拟dom数创建前     `componentWillMount()` 
    可以使用setState，但是不建议使用，容易出现一些问题，为了避免问题，新生命周期移出了该方法
    
    3) 创建虚拟dom树，并且生成真实dom，进行渲染   `render()`
    可以使用setState，但是不建议使用，容易出现一些问题

    4) 渲染完成    `componentDidMount()`
    发送ajax请求，初始化数据，操作setState。
    

2. 监听阶段(活跃阶段)
    1) 行间传入值发生变化，触发`componentWillReceiveProps()`
    传入一个参数，即新的props对象。
    由于有人在该函数内部通过this.setState() 把属性赋给状态，不符合组件控制的唯一性(属性控制或者状态控制)，所以官方也把该方法去除掉了

    2) 修改state中的数据或者行间传参值发生时触发:   `shouldComponentUpdate()`  
    该方法，有两个参数可以传递(state, props)。要修改的数据对象，state即setState中传入的对象，props即新的行间属性值对象
    该方法必须有返回值，返回true，可以向下进行重新渲染，state和props值发生变化。返回false，直接停止，不会向下执行进行重新渲染，并且state和props中的值不会发生变化。
    利用该方法可以优化性能。setState函数只要调用，就会触发重新渲染，该函数就会执行。就可以进行判断，如果没有发生变化，则返回false，进行拦截。
    由于该方法发生的时间是数据发生变化前，所以通过this.state或者this.props获取到的还是旧状态和属性。新状态和属性会当做参数传入。

    3) 属性或者状态发生变化前触发   `componentWillUpdate()`
    此时的state和props还没有发生变化
    由于没有什么用，所以官方也把该方法进行了移除。

    4) 进行重新渲染，重新构造虚拟dom，执行 `render()`

    5) 属性或者状态变化完成    `componentDidUpdate()`
    此时的state和props就变成了新的状态和属性了

3. 销毁阶段
    该组件从虚拟dom树中移除，就认为该组件被删除
    1) 移除前触发，`componentWillUpMount()`
    可以清除本组件中开启的定时器，组件已经被销毁，定时器没有什么用了。

    2) 移除后触发: `componentDidUpMount()`


## 新生命周期(解决一些问题，替换掉了一些旧生命周期函数)

1. 创建阶段，基本上没有什么变化，移除了一个周期函数，新执行了一个函数
    1) 状态和属性的初始化   `constructor()`  
    不能使用setState

    2) 创建虚拟dom树前执行，`static getDerivedStateFromProps()`
    该方法是为了替换componentWillReceiveProps，
    新方法是静态方法，内部的this无效，所以无法进行状态和属性的相互影响。

    3) 创建虚拟dom树，并且生成真实dom，进行渲染   `render()`
    可以使用setState，但是不建议使用，容易出现一些问题

    4) 渲染完成    `componentDidMount()`
    发送ajax请求，初始化数据，操作setState。


2. 监听阶段(活跃阶段)  
    1) 行间传入值发生变化，触发`static getDerivedStateFromProps()`

    2) 修改state中的数据或者行间传参值发生时触发:   `shouldComponentUpdate()`  

    3) 进行重新渲染，重新构造虚拟dom，执行 `render()`

    4) 把真实dom插入页面前触发  `getSnapshotBeforeUpdate()`
    实际名称为更新前获取快照，作用为: 在dom插入前可以对其在进行一步的加工。

    该方法也是新提出的，该方法也必须有返回值。
    并且该方法必须与componentDidUpdate方法配合使用，该方法的返回值，会作为componentDidUpdate的第三个参数传入。

    5) 属性或者状态变化完成    `componentDidUpdate()`
    此时的state和props就变成了新的状态和属性了

3. 销毁阶段，没有变化
    该组件从虚拟dom树中移除，就认为该组件被删除
    1) 移除前触发，`componentWillUpMount()`
    可以清除本组件中开启的定时器，组件已经被销毁，定时器没有什么用了。

    2) 移除后触发: `componentDidUpMount()`

4. shouldComponentUpdate可以优化性能



# HOOK简介

HOOK是React新提出的一种开发方法。

HOOK的作用，增强函数组件的功能，使之可以成为类组件的替代品。

并且React官方鼓励使用函数组件开发代码。

HOOK无法作用于类组件。

HOOK本质上是一个函数，该函数可以挂载任何功能。



# State HOOK
State HOOk是一个可以在函数组件中使用的函数，函数名称为`useState`。
该函数的作用: 让函数组件可以使用状态(定义数据)。

1. 使用方式
    1) 函数有一个参数，这个参数的值表示状态的默认值
    2) 函数的返回值是一个数组，该数组一定包含两项。第一项: 当前状态的值、第二项: 改变状态的函数

    3) 并且一个组件可以有多个状态，可以多次使用useState定义多个状态。

```js
// 引入该方法
import { useState } from "react";
function A() {
    // 状态1
    const [a, setA] = useState(0);
    // 状态2
    const [b, setB] = useState(5);
    
    return <div>
        <div>使用状态1 a: {a} </div>
        <div>使用状态2 b: {b} </div>

        <button onClick = { () => {
            setA(5)
        } } >修改状态1</button>

        <button onClick = { () => {
            setB(3)
        } } >修改状态2</button>
    </div>
}
```



2. 实现原理: 
    useState的调用，会在当前函数组件的组件节点对象上创建一个数据表，类似数组。
    第一个useState调用时: 看第0位有没有存入数据，如果没有存入初始值，并创建修改该值的方法。然后把该值与修改该值的方法放入数组中返回。
    第二个useState调用时，看第1位有没有存入数据，如果没有存入初始值，并创建修改该值的方法。然后把该值与修改该值的方法放入数组中返回。

    当使用返回的修改数据的方法时，会修改表中对应位置的数据。然后组件重新调用函数重新渲染。
    此时又会重新调用useState方法。
    第一个useState调用时，发现第0位有数据(修改的数据)，于是不再进行初始值的赋值，直接返回表中的数据，以及之前产生的修改数据的方法。即传入的参数不做处理。
    第二个useState调用时，发现第1位有数据(修改的数据)，于是不再进行初始值的赋值，直接返回表中的数据，以及之前产生的修改数据的方法。即传入的参数不做处理。

    依靠该方法实现了组件状态的切换。

3. 注意细节:
    1) useState最好写在函数组件的起始位置，便于阅读。
    2) useState严禁写在判断、循环中
       原因: 
       useState对数据的处理是根据执行的次数进行处理的，如果使用判断，改变useState执行的顺序，会造成状态的混乱。

    3) 如果两次的数据完全一样，不会导致重新渲染，达到了优化效率的目的

    4) 使用提供的方法修改数据，会直接覆盖之前的数据，并不会合并，和setState不同。
       所以对象，或者数组在改变状态时需要注意，最好使用`setA({...a, name: 12})`。
       把之前的属性展开，然后在修改具体的数据。目的:
       防止替换过程中，把一些属性给替换掉了

    5) 如果没有关联的状态，最好多次使用useState定义不同的状态，不要把它们定义在一个对象中，这样不便于操作状态，代码也不容易阅读。

4. 组件的强制刷新
    1) 类组件: 调用this.forceUpdate()方法，就可以进行组件的强制刷新。
    2) 函数组件没有提供专门的方法。可以通过定义一个空对象状态。当需要强制刷新的时候，在给该状态附一个空对象，由于两个空对象的地址索引是不同的，就会引发组件的刷新，达到强制刷新的效果


5. 修改状态方法的异步执行问题
    
    如果修改状态的方法处于异步函数中，则修改状态的方法也会异步的执行。和setState一样为了优化性能，但是，也具有setState的问题。


6. useState返回的修改数据函数的刷新机制
    1) 使用useState可以创建出一个数据表，为函数组件增加了状态
    2) 创建的函数表是挂载到组件节点空间上的，而不是挂载某个具体的函数上
    3) 函数组件的运行是在创建虚拟dom的时候，此时该函数节点对象已经创建完成。
    4) 当运行useState创建出数据表时，就会直接挂载到该节点上，表示是该节点的数据表
    5) 当使用useState返回的修改状态的函数修改状态时，需要引发组件的重新渲染，会从挂载的组件节点对象中找到该节点对应的组件函数，进行执行。
    并不是那个函数使用了useState创建数据表，该数据表与该函数建立联系，该函数修改状态重新运行的是该函数。
    6) 正式由于这一特点，可以对HOOK进行提取，形成自定义的HOOK。



比如:
```js
function A() {
    const [a, setA] = useState(0);

    return <div>
        <div>使用状态 a: {a} </div>

        <button onClick = { () => {
            setA(a + 1)
            setA(a + 1)
        } } >修改状态</button>
    </div>
}
```
点击按钮，状态并不会变成2，而是为1。原因就在于setA进行了异步执行，第一次调用setA时传入的是1，但是函数并没有立即执行。所以第二次调用setA的时候，传入的还是1。所以最终结果a变成了1。
虽然setA执行了两次，但是重新渲染只会执行一次。

解决方法:
采用回调的方式，具体的代码如下:
```js
function A() {
    const [a, setA] = useState(0);

    return <div>
        <div>使用状态 a: {a} </div>

        <button onClick = { () => {
            setA(a => a + 1)
            setA(a => a + 1)
        } } >修改状态</button>
    </div>
}
```
这样setA执行的时候传入的是方法，会把方法放入一个队列中。当执行时，先执行第一次存入的方法，传入初始值0，把返回值传入下一个执行的方法中。最后一个执行函数的返回值，覆盖对应的状态。然后在引发重新渲染，所以a的值变成了2。



# Effect HOOK
对应的函数为`useEffect`

1. 作用
    1) 专门用于处理函数组件的副作用。比如ajax请求、计时器、其它异步操作、操作真实对象、本地存储、其它操作外部数据的行为，这些都属于副作用。

    2) React为了使函数组件变得更加纯粹，提出了该方法，专门处理这些副作用。

2. 使用方法
    1) 该函数有一个参数，参数的类型为一个函数，函数中进行副作用的操作。
    2) 并且传入的函数是在页面渲染完成后执行，并且它的执行时异步的。
    3) 相当于类组件的componentDidMount和componentDidUpdate，但是也有些区别。
       该方法传入的函数，执行的时间稍晚，类组件的两个周期函数，是在真实dom更新，但是浏览器还没有重新渲染前执行。该方法传入的方法是渲染后执行。
    
    4) 可以在传入的方法中，获取到dom元素，因为该方法的执行时间为页面渲染完成，此时dom就已经可以获取到。

```js
// 引入该方法
import { useState, useEffect } from "react";
function A() {
    const [a, setA] = useState(0);

    useEffect( () => {
        console.log(123);
    } )

    return <div>
        <div>使用状态 a: {a} </div>
        <button onClick = { () => {
            setA(a + 1)
        } } >修改状态</button>
    </div>
}
```


3. 注意事项
    1) 该方法也不能使用在判断和循环中，
    2) 一个组件可以多次调用该方法，多次使用是，会把传入的方法放入队列中。到组件渲染完成后，依次执行队列中的方法。


4. 传入函数的返回值(清理函数)
    传入的函数是可以有返回值的，但是返回值必须也是一个函数。
    返回函数的作用为清理副作用，比如清理定时器。
    该函数的执行时间: 
    每一次传入函数执行前触发，执行的是上一个的销毁函数。
    第一次不会触发
    组件被卸载时触发，相当于类组件中的componentDidUnmount函数。

```js
// 引入该方法
import { useState, useEffect } from "react";
function A() {
    const [a, setA] = useState(0);

    useEffect( () => {
        console.log(123);
        // 返回清理函数
        return () => {
            console.log("清理，销毁");
        }
    } )

    return <div>
        <div>使用状态 a: {a} </div>

        <button onClick = { () => {
            setA(a + 1)
        } } >修改状态</button>
    </div>
}
```

5. useEffect函数的第二个参数(依赖数据)
    1) 该参数必须为数组
    2) 数组中记录副作用的依赖数据
    3) 当重新渲染后，当前传入的数组中的数据与上一次(渲染前)传入的数组中的数据完全相同(对象只看引用值，不会看内部)，传入的副作用处理函数，以及处理函数返回的清理函数都不运行。
       只要有一个不一样，副作用处理函数以及清理函数都会运行

    4) 第一次一定会运行副作用处理函数，组件销毁时一定会运行返回的清理函数。

    5) 如果数据依赖传入的是一个空数组，则React认为副作用处理函数没有任何依赖。
       这样副作用处理函数只在开始时执行一次，后面都不在执行。
       清理函数只在组件销毁时执行一次，其它时候都不在执行。
       可以实现一些需求。

```js
// 引入该方法
import { useState, useEffect } from "react";
function A() {
    const [a, setA] = useState({n: 123, c: 456});

    useEffect( () => {
        console.log(123);

        return () => {
            console.log("清理，销毁");
        }
    }, [a.n, a.c])   // 设置依赖数据

    return <div>
        <div>使用状态 a: {a.n} </div>

        <button onClick = { () => {
            setA({...a, n: 789})
        } } >修改状态</button>
    </div>
}
```


6. 组件的副作用处理函数中，如果使用了组件中的状态数据，由于闭包的影响，会导致数据不会进行实时更新。
    每一次组件函数的重新执行，之前的组件函数相当于产生了闭包。
    如果useEffect中涉及到了异步获取组件的状态数据，当前组件开启异步处理，但是该异步处理还没有获取到数据，组件就进行了重新渲染。
    由于异步操作是之前组件开启的，所以即使组件重新执行创建新的执行期上下文，但是异步操作还是处于之前的环境中，所以获取的还是旧的状态，而不是新的状态。

比如:
```js
import { useState, useEffect } from "react";
function A() {
    const [a, setA] = useState(0);

    useEffect( () => {
        setInterval( () => {
            setA(a + 1);
        }, 1000)
    }, [])     // 只让副作用处理函数执行一次，只开启一个定时器

    return <div>
        <div>使用状态 a: {a} </div>
    </div>
}
```
现象: a并不会进行累加。
原因: 定时器虽然一直在做a累加的工作，但是，a累加完，修改了状态。
      导致A组件进行重新渲染，函数重新执行得到的a是累加后的结果1，但是此时A重新执行得到的是一个新的执行期上下文，a为1在新的执行期上下文中。
      然后定时器继续累加，读取a做a+1的运算，由于定时器是在之前的执行期上下文中创建的，所以读取的a还是0。运算完，a为1，useState一看值与上次付的一样，之后组件不在进行重新渲染。
      定时器一直做 0 + 1的运算。
      这就是useEffect中异步使用状态，受闭包的影响。

解决方法: 
    1) 把副作用的依赖去掉，定时器采用setTimeout。
    setTimeout时间一到，a进行累加，当前环境的setTimeout消失，组件重新执行。
    由于没有依赖，就会继续新开一个setTimeout
    由于setTimeout是在新环境中开启的，所以此时获取的a就是上次累加完的值。
    这样就可以使用setTime，进行累加的接力。
    setInterval不能每次都新开一个，会造成它的累加，它不会工作完就销毁。
    
    2) 使用useState定义的状态，只能通过提供的专门的方法进行修改，不能通过赋值的方式进行修改，   所以无法操作旧环境的a，使其保持一致。

    3) 把每次修改完的状态保存到外部，每次累加从外部获取数据做累加效果。
       如果组件只使用一次没有问题，如果多个地方都使用了该组件，就会造成数据的覆盖。

    4) 可以在组件中，定义一个数据，保存每次累加完的状态。
       定时器使用该数据做累加，也是可以实现的。





# 自定义HOOK

1. 原理
    **当状态发生变化时，从组件节点找到对应的组件函数，进行执行，从而进行从新渲染**。

2. 分析
    组件函数的执行，是从上到下执行。
    可以把一些HOOK的处理操作进行单独的封装处理，是函数组件变得更加的纯粹。
    封装HOOK和写在组件函数内部的效果是一模一样的。
    1) 其一: 组件开始执行时，只会触发一次，从上到下，封装函数，进行函数调用，就相当于把HOOK写在函数调用的地方。
    
    2) 其二: 当封装的HOOK中涉及到了状态的改变，需要重新执行函数，引发组件的渲染。
    重新执行的函数并不是所处的封装的函数，然后以返回值影响函数组件，引发函数组件的重新执行。
    而是直接从HOOK挂载的节点对象中找到对应的组件函数进行重新执行。
    
    3) 既然封装的函数修改状态，也是直接使函数组件重新执行，然后调用封装函数，处理代码。
    这样和把封装的代码写在函数引用的地方是没有什么区别的。

    4) 总结，封装HOOK然后使用HOOK，和直接把代码写在函数引入的地方是没有什么区别的。

    5) 容易理解错误的是，修改状态，引发当前函数的重新执行，进而影响组件的重新渲染。
       React没有这么多黑科技，无法监控状态。
       修改状态，一定是重新调用了组件函数，使其重新执行，进行组件的重新渲染。


2. 注意事项
    1) 自定义HOOK和React定义好的HOOK一样，不要使用在判断或者循环中。
    2) 自定义HOOK的函数命名，前面一定要有use。其实在上线后没有什么影响。
       但是在开发环境中浏览器界面会显示错误。


3. 案例
```js
import { useState } from "react";
function A() {
    // 使用自定义HOOK，返回一个数据
    let a = useAbc();

    return <div>
        <div>使用状态 a: {a} </div>
    </div>
}

function useAbc() {
    const [a, setA] = useState(0);
    // 如果不加限制条件，直接使用setA修改状态，会陷入死循环中，最终报栈溢出的错误
    if(a !== 22) {
        setA(22);
    }
    return a;
}

// useAbc是在组件A的环境下运行的，所以修改状态，重新执行C函数
// 然后C函数重新调用useAbc，读取状态，读取的就是22
// 该函数共执行两次，第一次a为0，第二次a为22
```
相当于下面的写法
```js
import { useState } from "react";
function A() {
    // 使用自定义HOOK，返回一个数据
    // let a = useAbc();
    // 把useAbc的代码复制出来，写在该位置
    const [a, setA] = useState(0);
    if(a !== 22) {
        setA(22);
    }

    return <div>
        <div>使用状态 a: {a} </div>
    </div>
}
```

4. 解决场景
    1) 可以把一个组件的副作用，进行单独的拆分，然后封装成单独的HOOK
    2) 可以提取不同组件的相同的HOOK功能，进行封装。


# Reducer HOOK
对应的函数名为`useReducer`

1. 功能
    1) 专门管理状态的一个函数
    2) 相当于redux中的reducer函数

2. 使用
    1) 该方法传入三个参数，常用的是前两个参数，第三个参数可以不用传入
    第一次参数为具体的状态处理函数，第二个参数为状态的初始值
    该方法的返回值是一个数组，数组的第一项为状态，第二项为修改状态的函数dispatch。

    2) dispatch函数的用法为，传入一个修改状态的对象action(表示要怎么进行修改状态)
    该方法的原理，内部使用传入的状态处理函数，进行具体的状态修改。
    状态一发生修改，就会重新执行组件函数，然后组件函数通过useReducer获取的就是新的状态(第一次useState赋值加读取，之后都是读取)。

    3) 和自定义HOOK的原理一样。useReducer可以自定义一个，不用官方提供的。

3. 示范代码
```js
// 引入该方法
import { useState, useReducer } from "react";
// 自定义的处理函数
function abc(a, action) {
    switch(action.type) {
        case "a状态为12":
            return action.attrs;
    }
    return a;
}
function A() {
    // 传入具体的处理函数，状态的默认值，默认值的处理函数(把默认值当做参数传入，
    // 返回值作为实际的默认值)
    const [a, dispatch] = useReducer(abc, 1, (a) => { console.log(a); return 5} );
    return <div>
        <div>使用状态 a: {a} </div>
        <button onClick={ () => {
            // 传入action对象
            dispatch({
                type: "a状态为12",
                attrs: 12
            })
        } }>改变状态</button>
    </div>
}
```

4. 注意点
    1) 自定义处理函数，第一个参数传入的是当前的状态值，返回值会在useReducer中使用修改状态的函数修改状态，从而引发页面的重新渲染。
    2) 页面重新渲染，useReducer重新执行，返回的dispatch函数是同一个函数，并没有立即创建
    3) 一个组件中可以多次使用useReducer，管理多个状态，多次使用useReducer返回的dispatch是不同的，每个状态对应一个dispatch函数。
    4) 虽然每次重新渲染，都传入初始值处理函数，但是初始值处理函数，只在开始时运行一次。和dispatch一样，只在开始时进行创建。


5. 仿写useReducer
```js
function useReducer(tubeReasonFun, initial, initialFunc) {
    // 无法实现，只在第一次创建
    if(initialFunc) {
        initialFunc(initial);
    }

    // 创建状态(以后读取状态)
    const [state, setState] = useState(initial);

    // 创建dispatch函数
    function dispatch(action) {
        let data = tubeReasonFun(state, action);
        setState(data);
    }
    
    return [state, dispatch];
}
```

# Context HOOK
对应的函数名为`useContext`

1. 作用
    获取上下文中的数据(获取全部数据)，可以采用数据解构的方式获取到需要的数据

2. 使用
```js
// 引入该方法
import { useContext } from "react";
// 创建上下文对象
let context01 = React.createContext();

// 使用原始的方式获取执行期上下文
function A() {
    return <div>
        {/* 这样可以获取上下文中的数据，但是结构上多了一个层级 */}
        <context01.Consumer>
            {
                ({a}) => {
                    return <p>{a}</p>
                }
            }
        </context01.Consumer>
    </div>
}

// 使用useContext获取执行期上下文中的数据
function B() {
    // 传入创建的执行期上下文，就可以获取到内部的数据
    let { a } = useContext(context01);  
    return <div>
        {/* 操作简单了许多 */}
        <p>{a}</p>
    </div>
}

// 创建执行期上下文
function C() {
    return <context01.Provider value={ {a: "b", b: 11} }>
        <A />
        <B />
    </context01.Provider>
}
```


# Callback HOOK
对应的方法名`useCallback`

1. 作用
    固化函数的地址引用，优化重新渲染的性能。

2. 解决的问题
    1) 如果函数组件通过行间传参的方式，向一个纯组件(类组件)传入了一个方法。
       纯组件是可以进行数据的验证从而进行渲染的拦截，提高效率。
    
    2) 如果行间传入的函数直接写在函数组件的内部。
       则函数组件一进行刷新，则每次传入的函数都是新创建的。
       此时纯组件是无法拦截，所以纯组件进行重新渲染。
       明明变化的数据和纯组件没有关系，纯组件却进行了渲染，浪费了效率。
    
    3) 把函数定义在函数组件的外部是可以解决该问题的。
       但是如果传入的函数中使用到了函数组件中的状态。
       则函数定义在外部，就需要采用传参的方式，把状态传入，此时行间就需要借助bind。
       但是bind每次执行返回的也是一个新函数，纯组件此时又拦截不住了。
    
    4) 此时就可以使用useCallBack进行函数引用的固化。
       每次传入的都是一个函数索引，纯组件自然就可以进行拦截了。


2. 使用
    该方法有两个参数，一个是函数，一个是数据依赖数组
    只要数组依赖中的数据没有发生变化，则返回传入函数之前的地址引用，而不是新的地址引用。


3. 示范代码
```js
// 引入该方法
import { useState, useCallback } from "react";

// 函数组件
function A() {
    const [a, setA] = useState(5);
    const [b, setB] = useState(5);

    // 函数内部使用到了状态，不能定义在外部，所以进行索引的固化
    // 依赖发生了变化，类组件自然需要进行刷新，即使返回新的函数也没事
    let func = useCallback( () => {
        console.log(a);
    }, [a])

    return <div>
        <B func={ func } />
        {/* 函数组件重新渲染，纯组件进行了优化没有重新渲染(传入的函数索引一样) */}
        <div onClick={ () => {
            setB(10);
        } }>{ b }</div>
    </div>
}

// 纯组件
class B extends React.PureComponent {
    render() {
        console.log("重新渲染了");
        return <div onClick={ this.props.func }>点击</div>
    }
}
```



# Memo HOOK
函数名`useMemo`

1. 作用
    也是其一个固化地址的作用
    但是该方法比useCallbacks的功能更加强大
    useCallback只能固化函数引用。
    useMemo可以固化任何数据的引用，比如对象，数组，函数。
    
**useMemo最大的作用，通常使用该方法优化函数组件重新渲染时的效率**。

2. 使用方式
    该方法也有两个参数，第一次参数是一个函数，函数必须有返回值。
    第二个参数是数据引用。
    第一次执行时，传入的函数会立即执行，然后把返回值的索引进行固化。
    之后只要数据依赖数组中的数据没有发生变化，传入的函数就不会再执行，直接返回固化的地址索引
    只有当数据依赖数组中的数据发生变化，传入的函数才会重新执行，重新固化新的地址引用


3. 优化重新渲染性能的案例
    比如: 某个函数组件从外界得到了一个非常复杂，数据量非常多的数组。
    然后使用了列表循环的方式，产生了一个非常复杂的结构。
    除此之外，还有一个非常小的数据，当非常小的数据发生了变化，进行重新渲染。
    就会发生恐怖的事情，列表循环会重新执行，然后更新dom，虽然dom没有发生变化，进行了复用。
    但是循环数组会浪费大量的时间。
    明明修改的数据与数组没有关系，根本不需要重新遍历数组。
    此时就可以使用useMemo对数组循环产生的结构进行固化，然后数据依赖为当前数组。
    只要数组没有变化，不在重新循环构建React元素，直接使用之前构建好的。

```js
// 模拟恐怖的数组
let arr = new Array(1000)
arr.fill("ahdufiahidf");

function A() {
    // 一个小数据
    const [a, setA] = useState(5);

    let b = useMemo( () => {
        // 对大数组循环产生的结构，进行固化，防止重新渲染时，重新走该函数
        return arr.map( (k) => {
            return <B text={k} />
        } )
    }, [arr])

    return <div>
        {/* 结构不需要从新生成，dom进行复用，效率提升 */}
        { b }
        
        {/* 改变小状态 */}
        <div onClick={ () => {
            setA(10);
        } }>{ a }</div>
    </div>
}

function B(props) {
    console.log("1")
    return <div>{ props.text }</div>
}
```


4. 扩展
    可以通过该方法固化一个普通的对象或数组。用来存放一些可以修改，但不引发重新渲染的数据。
    并且组件从新渲染后，获取的还是之前进行的数据，而不是初始化的数据。
    如果直接通过 let obj = {}，每次组件重新渲染，数据就会重新覆盖，创建新的对象。
    此时就可以借助useMemo固化一个对象，虽然组件进行重新执行，但是使用的还是同一个对象。


# Ref HOOK
函数名`useRef`

1. 作用
   产生一个固化地址的特殊对象，即ref对象( React.createRef() )。
   用来获取组件的dom，或者子组件的环境。
   如果直接使用React.createRef()创建ref对象的话，虽然可以实现效果。
   但是每次组件重新渲染，都会重新创建新的，浪费了效率。
   用useRef，只会创建一次，之后组件刷新，直接返回之前的地址索引。

2. 使用
    只有一个参数，如果不传递，创建的对象和React.createRef()创建的对象一样。
    如果传递参数，给对象中的current属性附一个初始值

3. 示范代码
```js
import { useState, useRef } from "react";
function A() {
    let ref = useRef();
    let [a, setA] = useState(1);
    // 发现刷新后，打印的该对象有ref对应的dom，证明了该对象使用的是同一个
    console.log(ref);

    return <div>
        <div ref={ref}>标签</div>
        <button onClick={ () => {
            setA(22);
        } }>刷新</button>
    </div>
}
```

4. 扩展
    1) React.createRef()可以和useMemo结合使用，也能产生一个固化的对象。
    2) 也可以通过该方法固化一个对象。用来存放一些可以修改，但不引发重新渲染的数据。
    并且组件刷新后，并不会对该对象重新创建覆盖，使用的还是之前的数据。
    只不过会多一个属性current



# ImperativeHandle HOOK
方法的名称`useImperativeHandle`

1. 作用
    1) 给ref对象中的current属性赋值
    2) 要是只是单纯的赋一个值，直接.current = ?就可以实现。
    但是函数组件，只要一刷新就会多次执行，如果赋的值需要计算，且计算过程有些复杂。
    在使用 = 赋值有时就浪费了性能。

    3) 使用useImperativeHandle可以设置数据依赖数组。
    只有数据依赖数组发生变化时，才进行复杂的运算，重新给current赋值。
    如果没有发生变化，计算函数不在运行，直接赋之前计算出来的值。

2. 使用
    传递三个参数:
    1) 第一个参数: 为ref对象。
    2) 第二个参数: 是一个函数，会在初始时执行一次，返回值赋给ref的current属性
    如果没有设置数据依赖，则组件每次重新执行，该函数都会重新执行。
    **并且该函数的执行时异步执行**
    3) 第三个参数: 数据依赖，只有发生变化时，第二个参数传入的函数才会重新执行


3. 示范代码
```js
import { useImperativeHandle, useRef } from "react";
function A() {
    // 创建一个ref对象
    let ref = useRef();
    useImperativeHandle(ref, () => {
        console.log(111);
        // 返回值给ref中的current赋值
        return {
            a: () => {
                console.log("ref");
            }
        }
    }, [])


    console.log(222);

    return <div>
        <div>标签</div>
    </div>
}
```
先打印222后打印111，说明useImperativeHandle传入的第二个参数函数，是异步执行的


4. 扩展
    1) 函数组件的行间是不能写ref，如果想要使用ref，需要进行ref的转发
    2) 可以通过useImperativeHandle给ref转发到函数组件内部的ref对象的current赋值
    3) 比如赋一个函数，这样父组件环境中就可以通过ref使用赋的函数。



# LayoutEffect HOOK
函数名`useLayoutEffout`

1. 作用
    可以用来处理一些副作用
    常用于处理真实dom的操作

2. 使用方式
    使用方式与useEffect方式完全相同，只不过传入的函数触发时间不同。
    在真实dom改变后，但是浏览器还没有刷新时执行，而useEffect是浏览器刷新后执行。
    所以此时处理真实的dom，不会引发闪烁的效果。
    如果使用useEffect处理真实的dom，可能会闪一下，然后切换到设置的状态。


3. 示范代码
```js
import { useLayoutEffect } from "react";
function A() {
    useLayoutEffect( () => {
        console.log("副作用");
        // 返回一个函数，销毁时执行，副作用处理函数运行前执行(第一次除外)
        return () => {
            console.log(123);
        }
    }, [])

    return <div>
        <div>标签</div>
    </div>
}
```

4. 注意事项(执行顺序的问题)
    1) 如果多个useLayoutEffect连用。当传入的处理副作用函数重新执行时，会先执行返回的函数。
    并且所有的返回函数按照顺序执行完，才会处理副作用的函数。

    2) 如果useLayoutEffect与useEffect共用，执行顺序为: useLayoutEffect的返回函数、处理副作用函数、useEffect的返回函数、处理副作用函数。

    3) 如果组件销毁时，就与useLayoutEffect和useEffect没有关系，会按照顺序，从上到下依次执行。并不是先执行useLayoutEffect的，然后在执行useEffect的。



# React中的动画插件
需要下载外部插件 `npm install react-transition-group -D`。
该库中提供了四种组件。

## Transition组件

1. 使用案例
```js
import { Transition } from "react-transition-group";
import { useState } from "react";

function A() {
    let [boo, setBoo] = useState(true);

    return <>
        {/* 使用提供的第一个组件，行间需要传入两个必要的数据 */}
        <Transition in={boo} timeout={500} >
            {/* 内部必须传入一个函数，该函数有一个参数 */}
            {
                (state) => {
                    console.log(state);
                    return <div>123</div>
                }
            }
        </Transition>

        <button onClick={ () => {
            setBoo( !boo );
        } }>切换状态</button>
    </>
}
```


2. 过程分析
    1) Transition组件行间传入的两个参数，一个表示状态，一个表示动画的时间。
    2) 组件中传入方法的接收到的参数是实现动画运动的关键。
    3) Transition行间传入的in并不是控制组件的显示隐藏。而是控制向函数中传入什么值。
    4) 参数共有四个值(在不同的时间传入)
        `entered`: 初始时如果in为true，传入该值
        `exiting`: in发生了变化，从true变成了false，传入该值
        `entering`:  in发生了变化，从false变成了true，传入该值
        `exited`: 初始时如果in为false，传入该值


3. in发生变化，具体的流程分析
    1) 初始值不用分析，in为true，传入的是`entered`。in为false传入的是`exited`。
    2) 每次in发生变化，其实传入的函数都会执行三次，三次传入不同的值，设置不同的状态。
       并且这三个传入的值与设置的时间也有关系。
    
    3) true变成false，传值顺序为先传入`entered`，然后传入`exiting`。
       然后开始等待，等待时间为设置的时间，然后在传入`exited`。

    4) false变成true，传值顺序为先传入`exited`，然后传入`entering`。
       然后开始等待，等待时间为设置的时间，然后在传入`entered`。


4. 使用分析
    1) 传值模式与往复运动十分接近，比如通过opacity控制dom的显示隐藏。

    2) 当in为true时，传入entered(起点)。表示显示，opacity设置为1。
    3) 当in为false时，传入exited(终点)。表示隐藏，opacity设置为0。
    
    4) 当in发生变化时，从true变化成false(从起点到终点)，从显示过渡到隐藏
       1) 先传递`entered`(起点)。表示在设置一下起点的样式，确保从起点开始变化。
       2) 然后传入`exiting`(变化)。设置终点的样式，如果起点中设置了transition。
       此时就开始进行过渡动画，设置的过渡时间要小于等于in，最好等于in。
       3) 当到达设置的时间，传入`exited`(终点)。在设置一遍终点的样式。
       4) 虽然exiting中设置的样式过渡完就是终点的样式。
       但是中间可能发生新的变化，赋了新的终点样式。
       5) 所以在最后为了保险，传入终点标记，设置终点样式。
    
    5) 从false变化到true同理。

5. 注意点
    1) 要想实现动画，css样式需要自己写，尤其是动画的关键transition属性，并不会自己添加。
    组件只是提供了几个阶段的标记。
    
    2) 设置的时间没有到达，in的状态又发生了变化，则之前状态终点传入的值不在传入。
       直接进行下一次的切换周期，先执行传入切换初始传入的值，然后在执行传入第二个值。
       然后再次进入等待状态，等待时间一到，执行函数传入当前状态对应的值。


6. 扩展
    1) **通常使用传入的四个数据作为标签的class名，以此控制动画的产生**。
    2) 也可以在函数中判断传入的值，通过行间样式实现动画。



## CSSTransition组件

1. 功能
    1) 相对于Transition组件，该组件更加专注与处理css样式，直接给标签添加指定的class名。

2. 基础使用
    1) 和Transition使用方式一样。行间也传入in和timeout两个属性。
    2) 初始时，dom不添加任何特殊的class。只有in发生变化时才进行添加
    
    3) in从true变化到false
       1) 变化时，先添加`exit`，然后在下一帧在exit的基础上添加`exit-active`。
          可以在exit中添加transition，好处在于，运动完该属性销毁，不用一直监听属性
          在exit-active中添加过渡终点的样式。
       2) 时间运动完，class替换为`exit-done`。设置终点的样式。

    4) in从false变化到true
       1) 变化时，先添加`enter`，然后在下一帧在enter的基础上添加`enter-active`。
          可以在enter中添加transition，好处在于，运动完该属性销毁，不用一直监听属性。
          在enter-active中添加过渡终点(起点)的样式。
       3) 时间运动完，class替换为`enter-done`。设置起点的样式。


3. 子组件加className
    1) 标识性class与className中设置的进行合并，在原有的基础上添加标识性class，不会替换。
       这样标签可以有个初始class，设置初始时的样式，与标示性class的动画联系起来。


4. 注意点
    1) 第一次动画还没有运动完，in的状态就发生了变化
    如果等待的时间没有到达，in发生了变化，比如从true变成了false，然后又变成了true。
    则`exit-done`不在设置，直接进去下一次周期。
    从`exit exit-active`直接切换到`enter enter-active`。时间一到添加`enter-done`。
    不会等待上一次变化周期完成，下一次才能开始。

    2) CSSTransition组件的下面只能有一个节点，可以是组件节点，可以是普通节点
      如果是组件节点，CSSTransition在添加标示性class时。
      是给组件下可以渲染出真实节点的第一个节点添加。


5. 使用案例
```js
import { CSSTransition } from "react-transition-group";
import { useState } from "react";

function A() {
    let [boo, setBoo] = useState(true);

    return <>
        {/* 使用提供的第一个组件，行间需要传入两个必要的数据 */}
        <CSSTransition in={boo} timeout={10000}>
            <div className={ "a b" }>123</div>
        </CSSTransition>

        <button onClick={ () => {
            setBoo( !boo );
        } }>切换状态</button>
    </>
}
```
    

6. 更改标示性的class名称，换成自定义的。
    在组件的行间使用classNames，该属性有两种赋值方式:
    1) 赋值字符串，则子组件添加的标示性class与设置的字符串进行拼接。比如`a-exit`
    2) 赋值对象，可以指定某个标示性class名换成自定义的名字。
       比如{exit: 'abc'}。则exit在添加时就变成了abc，不再是exit了。
       如果想要改变exit-done名字，需要换成小驼峰式的写法。即exitDone


7. 添加初始class
    CSSTransition组件在刚开始渲染时，下面的第一个子节点是不会添加任何class的。
    如果想要添加初始的class，有两种方式:
    1) 一种是在子节点对应的React元素上直接使用className添加
       但是这种方式添加的class会一直存在。
    2) 另一种方式是在CSSTransition组件的行间写上`appear`这个属性。
       该属性会在初始时给第一个子节点添加两个class名，即`appear appear-active`。
       当in的状态发生变化进行重新渲染时，这两个class将会消失，以后也不再生成。
       即只存在初始阶段。
       **appear-active也是在appear的后一帧添加**。
       如果in的初始值赋的为false，这两个class不会添加。


8. 组件行间定义的各种事件以及作用
    


## SwitchTransition组件
    
1. 作用
    用于有秩序的切换内部组件，可以实现一个离开后另一个在进入的效果。

    **控制组件的创建和销毁，并且为销毁和创建添加了一个过程**。


2. 使用
    1) 该组件不能单独使用，需要配合Transition组件或者CSSTransition组件配合使用。
       并这两个组件上不在使用in，而是使用key。

    2) 效果先触发两个组件的离开效果，然后在触发组件的进入效果，最终展示为组件的进入状态。
    
    3) 以CSSTransition组件为例，切换状态。
       由于key的值发生了变化，此时整个组件以及下面的节点都将被卸载然后生成全新的。
       但是新旧节点的替换并不是立即完成的，在SwitchTransition组件的作用下。
       旧组件先触发离开状态，即节点添加class名`exit exit-active`，表示正在离开。
       到达设置的时间后，旧组件才被替换成新的组件。
       然后新的组件触发进入状态，即添加class名`enter enter-active`，表示正在进入。
       到达设置的时间后，新组建的节点设置为`enter-done`。表示已经进入。

    4) 无论从true变成false，还是从false变成true。经过的步骤都是旧组件先离开，新组件进入。

    5) 正是由于组件的创建和销毁多了一个过程，并且有对应的class名。
       我们就可以根据class名设置进入和离开时的样式，形成动画。
       然后根据key的值，显示不同的内容。
       这样就形成了一个页面离开后另一个页面进入的效果


3. 示范代码
```js
import { SwitchTransition, CSSTransition } from "react-transition-group";
import { useState } from "react";

function A() {
    let [boo, setBoo] = useState(true);

    return <>
        <SwitchTransition>
            <CSSTransition timeout={ 2000 } key={boo}>
                <div>123</div>
            </CSSTransition>
        </SwitchTransition>
        

        <button onClick={ () => {
            setBoo( !boo );
        } }>切换状态</button>
    </>
}
```


4. 使用appear设置初始class名
    1) 该属性给CSSTransition或者Transition组件进行设置，给SwitchTransition设置无效
    2) 比如给CSSTransition设置该属性，状态无论是true还是false。
       初始都为`appear appear-active`。
       然后经过设置的时间后变成`appear-done enter-done`。
    3) 变化的目的在于符合SwitchTransition组件的新组建进入的特点。
    appear appear-active表示组件创建，开始进入。appear-done enter-done表示进入完成。


5. 特点
    如果一个旧组件还没有离开页面，此时状态又发生了变化。
    Transition组件是不会进行重新解析的，只有当新的组件插入到页面，哪怕是刚插入。
    即旧组件已经离开，此时切换状态才有效。
    要考虑到旧节点是否已经被新节点进行了替换。



## TransitionGroup组件

1. 作用
    该组件的children，可以接收多个Transition或者CSSTransition组件
    该组件用于根据这些子组件的key，控制它们的销毁或者创建


2. 使用
    1) 该组件只能控制Transition或者CSSTransition组件的销毁，或者创建。
    2) 无法控制它们的的状态切换
    3) 只不过销毁和创建多了一个过程
       和SwitchTransition一样，销毁多了移出的过程，创建多了移入的过程。

    4) 销毁不会立即销毁，而是添加`exit exit-active`。
       等到了该组件设置的timeout(时间)后才进行组件的卸载。
       这样就可以利用这两个class做一些销毁离开的效果。

    5) 创建会立即插入，然后添加`enter enter-active`。
       等到了该组件设置的时间后，class替换为enter-done。
       可以利用这几个class做一些创建进入的效果。

    6) 如果使用appear添加默认的class
       先为`appear appear-active`，等到了组件设置的时间，变为`appear-done enter-done`。
       可以利用这几个class做一些创建进入的效果
       如果每个组件都有appear，可以把该属性添加在TransitionGroup上，会作用用于全部子组件

    7) TransitionGroup在各个子组件的外围默认创建一个div父级，把组件进行包裹。
       可以设置创建的父级dom，TransitionGroup利用行间属性component="?"，指定dom
       比如`component="ul"`，创建ul。如果设置为`{ null }`，表示不创建dom。
       如果在TransitionGroup上使用`className`，是给创建的组件父级标签添加class。



3. 示范代码
```js
import { TransitionGroup, CSSTransition } from "react-transition-group";
import { useState } from "react";

function A() {
    let [boo, setBoo] = useState(true);
    let [boo1, setBoo1] = useState(true);

    return <>
        <TransitionGroup appear> 
            {
                boo && <CSSTransition timeout={ 5000 }>
                    <div>123</div>
                </CSSTransition>
            }
            {
                boo1 && <CSSTransition timeout={ 1000 }>
                    <div>789</div>
                </CSSTransition>
            }
        </TransitionGroup>
        
        {/* 控制两个组件的销毁和创建 */}
        <button onClick={ () => {
            setBoo( !boo );
        } }>切换状态</button>

        <button onClick={ () => {
            setBoo1( !boo1 );
        } }>切换状态</button>
    </>
}
```
       


# 路由

## 准备工作(插件下载)
React使用路由需要依赖两个插件，一个是`react-router`、一个是`react-router-dom`。
使用npm下载的时候，只需要下载react-router-dom就可以。
它会自动的把它依赖的react-router也下载下来。
下载方式: `npm i react-router-dom`


## 路由模式

1. 分类
    路由共有两种模式，一种是哈希路由、一种是浏览器历史记录路由。
    现如今浏览器只支持这两种方式，跳转路径不刷新页面。


2. 使用
    在React的路由中，这两种模式其实就是两种组件，引用方式如下:

```js
// BrowserRouter组件对应浏览器历史记录路由(最常用的)
// HashRouter组价对应哈希路由
import { BrowserRouter, HashRouter } from "react-router-dom";

function A() {
    return (
        <>
            <BrowserRouter>
                <div>浏览器历史记录路由</div>
            </BrowserRouter>
            <HashRouter>
                <div>哈希路由</div>
            </HashRouter>
        </>
    )
}
```
通常让这两种模式(组件)作为最外层的父级，表示整个工程都位于路由环境的下面。
开发起来比较方便。
    

3. 作用
    创建上下文，传入一些参数。



## Route组件

1. 作用
    该组件是React实现路由功能的核心组件。
    该组件的作用就是匹配当前的路径，显示不同的组件内容。


2. 原理(工作方式)
    1) 初始工作
       1) React的核心是创建React元素，然后在根据React元素，生成对应的虚拟节点。
       2) 当React解析到Route组件时，创建组件节点。
       3) 然后在ReactDOM.render()的作用下，激活该组件的环境(执行相应的函数)
       4) 然后判断当前路径，是否满足该组件的匹配规则。
       5) 如果匹配成功，则立即处理传入的组件，生成对应的虚拟dom，挂载到该组件的下面。
       6) 如果匹配不成功，则组件不做任何处理。React就会跳出该组件，继续向下解析。

    2) 当路径发生变化时
       1) 整个Router组件就会被重新渲染
       2) 需要注意的是，由于整个Router下的children元素位于路由的执行期上下文环境中(Provider)。
       3) 当Router进行重新渲染的时候，底层其实是修改了执行期上下文的赋值对象，这才造成了Provider的重新渲染，从而使整个组件的重新渲染，但是Provider的重新渲染是进行了优化的
       4) **Provider的重新渲染，其实是Consumer组件的重新渲染，其它组件不会触发重新渲染**
       5) 比如: 
       ```js
       function A() {
           return <BrowserRouter>
               <B />
               <Route />
           </BrowserRouter>
       }
       ```
       其实，BrowserRouter重新渲染的时候，B组件和Route组件都没有触发重新渲染。
       由于Route下的children就是一个Consumer组件，重新渲染的是Consumer组件，重新进行路径的匹配，处理传入的component组件(造成后续一系列组件都会重新渲染)，这样就造成了Route组件重新渲染的现象。
       由于B不会重新渲染，所以B下一系列子元素都不会被重新渲染。


3. 使用方式

    1) 行间属性
        1) `path` 设置当前Route组件的路径匹配规则，当匹配成功，加载对应的组件。
           1) 可以直接赋值字符串
           2) 如果不写path，则所有路径都可以匹配成功，通常用于显示404界面
           3) 可以赋值数组
              数组的每一项，都为一个地址，只要有一个可以匹配成功，该组件都将进行加载。
           4) 动态路由，字符串的格式为/a/:?/:?，具体介绍在路由信息中。
              后面必须有`:`，这样才能进行动态匹配。
        2) `component` 路径符合所加载的组件
        3) `exact` 设置当前Route的路径匹配模式为完全匹配，默认是不完全匹配。
           比如: /a/b。不完全匹配: /a可以显示组件、/a/b也可以显示组价。
           完全匹配: 只有/a/b可以显示组件。
        4) `render`，该属性也是传递一个组件，与component属性的功能完全一样。
           匹配成功，加载对应的组件。但是不能与component属性共存，否则会失效。
        


    2) 子元素(children)
        与component、render属性功能一样
        当匹配成功时，对应Route下面的children才会被解析加载。


4. 注意点
    1) **Route组件可以写在任何的地方，但是必须位于BrowserRouter或者HashRouter环境的下面**
       该组件就是一个普通的组件，当路径符合，就解析对应的组件，不符合就不解析。


5. 示范代码
```js
import { BrowserRouter, Route } from "react-router-dom";

function A() {
    return (
        <BrowserRouter>
            <Route path="/" exact component={ B }></Route>
            <Route path="/c" component={ C }></Route>
            <Route path="/d" component={ D }></Route>
        </BrowserRouter>
    )
}

function B () {
    return <>我是B组件</>
}

function C() {
    return <>我是C组件</>
}

function D() {
    return <>
        {/* Route可以写在任意地方，当D组件被解析后，该Route就会被解析。
            然后与当前路径进行判断，符合就加载C组件，不符合就不加载 */}
        <Route path="/dadsf" component={ C }></Route>
        我是D组件
    </>
}
```



## Switch组件

1. 功能以及解决的问题
    1) 该组件需要配合Route组件来使用。
    2) Route为该组件的children属性。
    3) 作用: 循环遍历该组件下的children(即Route)，然后逐步与当前的路径进行匹配。
       如果匹配成功，则循环不在执行，直接显示该页面。

    4) 匹配方式: 从上到下，匹配成功就停止匹配。
    5) 解决的问题: 一个组件下面可能有多个Route，用来不同的地址显示不同的内容。
       如果Route使用的是不完全匹配，则一个路径可能匹配到多个组件进行显示，不符合要求。
       此时就可以使用Switch来进行限制，当匹配成功后，就不在继续向下匹配。


2. 注意点
    1) Switch下的第一级子节点必须为Route，不能随便乱写。
    2) 原因: Switch是按照children的顺序从上到下依次查询。
          如果在中间写了个div，无法处理，会直接报错。


3. 示范代码
```js
import { BrowserRouter, Route, Switch } from "react-router-dom";

function A() {
    return (
        <BrowserRouter>
            <Switch>
                <Route path="/" exact component={ B }></Route>
                <Route path="/c" component={ C }></Route>
                <Route path="/d" component={ D }></Route>
            </Switch>
        </BrowserRouter>
    )
}

function B () {
    return <>我是B组件</>
}

function C() {
    return <>我是C组件</>
}

function D() {
    return <>
        <Route path="/dadsf" component={ C }></Route>
        我是D组件
    </>
}
```



## Link组件

1. 作用
    生成一个可以进行无刷新跳转页面的a标签


2. 原理
    a标签默认是刷新跳转页面
    无刷新跳转页面，可以看成是阻止了a标签的默认事件。
    然后使用Router提供的push方法进行无刷新页面的跳转。


3. 使用方式
    1) 行间属性`to`, 设置跳转的地址，有两种赋值方式
       1) 字符串。可以写成/c?a=1#456这种格式，包括路径、数据、哈希
       2) 对象。
          1) 格式为{hash: ?, search: ?, pathname: ?, state: ?}
          2) 分别代表哈希、数据、地址、附加的状态信息(相当于push的第二个参数)
    2) 行间属性`innerRef`
       1) 该属性的属性值为一个自定义函数，并且函数可以接收一个参数。
       2) 接收的参数为内部的a元素对应的真实dom。


4. 示范代码
```js
import { BrowserRouter, Route, Switch, Link } from "react-router-dom";

function A() {
    return <BrowserRouter>
        <div>
            <Link to="/"> B </Link>
            <Link to="/a"> C </Link>
        </div>
        <div>
            <Switch>
                <Route path="/" component={ B } exact></Route>
                <Route path="/a" component= { C }></Route>
            </Switch>
        </div>
    </BrowserRouter>
}

function B() {
    return <div>B</div>
}

function C() {
    return <div>C</div>
}
```


## NavLink组件

1. 作用
    1) 是一种特殊的Link组件，Link组件有的功能该组件都有，除此之外还具有特殊的功能
    2) 组件通过设置的to与路径进行匹配，匹配成功，会添加特殊的class名`active`
       可以利用添加的class设置一些样式，表示当前进入的是该元素对应的页面
    3) 注意: 
       1) 添加class是使用匹配的方式，并不是点击那个NavLink，那个NavLink就添加class。
       2) 默认使用的也是不完全匹配。


2. 使用
    1) 行间属性`to`, 设置跳转的地址，有两种赋值方式
       1) 字符串。可以写成/c?a=1#456这种格式，包括路径、数据、哈希
       2) 对象。
          1) 格式为{hash: ?, search: ?, pathname: ?, state: ?}
          2) 分别代表哈希、数据、地址、附加的状态信息(相当于push的第二个参数)
    2) 行间属性`innerRef`
       1) 该属性的属性值为一个自定义函数，并且函数可以接收一个参数。
       2) 接收的参数为内部的a元素对应的真实dom。

    3) 行间属性`activeClassName`，设置匹配成功时添加的class，而不是active
    4) 行间属性`activeStyle`，设置匹配成功时添加的行间样式
       数据格式为对象，或者普通的字符串
    5) 行间属性`exact`，设置匹配方式为完全匹配



3. 示范代码
```js
import { BrowserRouter, Route, Switch, NavLink } from "react-router-dom";

function A() {
    return <BrowserRouter>
        <div>
            <NavLink to="/" exact> B </NavLink>
            <NavLink to="/a"> C </NavLink>
        </div>
        <div>
            <Switch>
                <Route path="/" component={ B } exact></Route>
                <Route path="/a" component= { C }></Route>
            </Switch>
        </div>
    </BrowserRouter>
}

function B() {
    return <div>B</div>
}

function C() {
    return <div>C</div>
}
```   



## Redirect组件

1. 作用
    该组件用于路径的重定向


2. 使用
    1) 放在Route组件的下面使用，并且Route组件和该组件必须被Switch组件包裹
    2) **当所有的Route组件都没有匹配到，进入Redirect组件进行路径重定向**
    3) 通过行间属性to设置重定向路径，有两种赋值方式
       1) 字符串。可以写成/c?a=1#456这种格式，包括路径、数据、哈希
       2) 对象。
          1) 格式为{hash: ?, search: ?, pathname: ?, state: ?}
          2) 分别代表哈希、数据、地址、附加的状态信息(相当于push的第二个参数)
    4) 行间属性`from`
       1) 该值也是设置路径，只有当前路径与from设置的路径匹配成功，才进行跳转。
       2) 默认为不完全匹配，可以通过设置exact，为完全匹配。
       3) 如果该属性不设置，则任何路径都可以进行跳转。
       4) 属性值还可以写成/a/:id的方式。只要是/a/?的路径都可以匹配成功
          1) 甚至from还可以把匹配到的id，传给to。to的写法为/?/:id，/:id要保持一致。
          2) 比如当前路径为/a/100abc，to设置的为/dd/:id。
          3) 则跳转的地址就变成/dd/100abc，from把匹配到的id传个了to，供to使用。



3. 示范代码
```js
import { BrowserRouter, Route, Switch, Link, Redirect } from "react-router-dom";

function A() {
    return <BrowserRouter>
        <div>
            <Link to="/"> B </Link>
            <Link to="/a"> C </Link>
        </div>
        <div>
            <Switch>
                <Route path="/" component={ B } exact></Route>
                <Route path="/a" component= { C }></Route>
                <Redirect to="/"></Redirect>
            </Switch>
        </div>
    </BrowserRouter>
}

function B() {
    return <div>B</div>
}

function C() {
    return <div>C</div>
}
```



## 路由信息(Route组件向引用组件的props传入的数据)

1. 向引用组件的props共传入三个参数，分别为:
    1) history对象
    2) location对象
    3) match对象


2. history对象，该对象主要提供一些方法
    1) **`push`方法(重要)**
       该方法最常用，作用为无刷新跳转路径(React实现路由的核心方法)
       1) 该方法与`window.history.pushState`方法的区别。
          1) 该方法内部使用了window.history.pushState实现无刷新跳转路径。
          2) 除此之外，还添加了其它的作用
          3) 可以让React感知到路径已经发生了变化，从而激活dom树上的所有Route组件。
          4) 该方法可以兼容哈希模式与浏览器历史记录模式两种情况
       2) 为什么不直接使用`window.history.pushState`
          1) 该方法只能适应一种模式，如果模式发生变化，还需要大量修改代码
          2) 该方法修改路径，React无法感知，页面不会发生变化
    
    2) 只要push一经调用，哪怕路径没有发生变化，还是之前的路径
       **虚拟dom树上挂载的所有Route组件依旧重新渲染判断**
       看似没有变化，其实已经向历史记录栈中压入了新的路径(和之前的一样)。
       所以Route组件会重新渲染判断。

    3) push中除了传入路径外，还有第二个参数，可以向将要加载的组件中传入数据。
       将要加载的组件通过location对象中的state属性获取。


3. location对象
    1) 该对象是对路径的处理，内部常用的有三个属性。
       1) `hash`。可以获取到当前路径的锚点
       2) `search`。可以获取到当前路径所传的参数、
       3) `pathname`。可以获取到当前的路径。

    2) 不同路径对应的不同组件间的传参通常使用路径传参的方式。
       使用的就是这三个属性进行数据的传递。

    3) 该对象通常与`query-string`组件配合使用
       1) `query-string`组件作用是解析hash或者search。
       2) 解析后的hash或者search从字符串变成了对象格式，方便了路由间的传参
       3) 该组件的下载方式`npm install query-string -D`
       4) 使用方式: `import qs from "query-string"`、`let a = qs.parse(?)`


4. match对象
    1) isExact属性，该属性有两个状态值，一个为false，一个为true。
       用来表示当前的Route组件的path设置的地址，是否与当前路径完全一致，数据锚点不算。
       即是否是完全匹配，这里的完全匹配与exact没有关系。

    2) **params属性**(重要)
       该属性可以获取地址上的数据(不是锚点和参数)，是实现动态路由的关键。
       1) 使用方式
          1) 如果当前路径为/dong/10/6/8/djis，想要获取dong后面的数据。
             1) Route行间path定义为: /dong/:a/:b/:c/:d。
             2) 这样Route加载的组件中的params属性为: { a: 10, b: 6, c: 8, d: djis}
          2) 如果想要实现，存在就获取，不存在就为null的效果。
             1) Route行间path定义为: /dong/:a?/:b?/:c?/:d?/:f?，在后面加上?
             2) f没有对应的地址，所以为null。
          3) 如果某位不想获取，但是还必须存在
             1) 可以使用，path可以使用/*代替一位，或者使用不完全匹配。
          4) path还可以使用正则来限制类型
             1) 比如/dong/:a(\d+)/:b，表示a对应的数据必须为1~位的数字，()中写正则表达式。




## 其它组件获取路由信息
路由信息，只能在Route组件通过component属性传入的组件中使用。
如果是传入组件的子组件，是无法使用到路由信息的，因为没有传入。


1. 方法1(常用)
    1) 方法
    可以使用react-router-dom中提供的一个方法`withRouter`进行包装。
    该方法是一个高阶组件，接收一个组件，返回一个组件。
    在Router环境下使用返回的组件，就可以向传入的组件传递路由信息。

    2) 原理
    withRouter返回的组件中，获取执行期上下文中的路由数据，然后打入传入的组件。
    路由的执行期上下文，开发者获取不到，但是内部的方法可以获取到，然后进行传入。

    3) 注意
       1) 使用withRouter进行包装的组件，每次路由发生变化，包装的组件都会进行重新渲染。
       2) 函数组件的重新渲染就是重新执行函数，使用时需要注意。
       3) 重新渲染的原因: 
          1) withRouter的底层使用上下文的Consumer组件包装了一下。如果没有位于Route组件中，Router重新渲染，其实是执行期上下文重新渲染，即Consumer组件重新渲染。这样就造成了被包装组件的重新渲染。
          2) 如果位于Route组件中，会因为Route组件的重新渲染而重新渲染。


2. 方法2(不常用)
    既然Route组件可以向某个组件中，自动注入路由信息
    我们就可以使用该组件对要使用路由信息的组件进行包装。



## 自定义特殊功能的Route组件

1. 应用
    1) 某个路径对应的组件，在加载的时候需要进行特殊的处理。
    2) 比如某个路径对应的页面在进入的时候，需要验证权限。
       如果没有权限，则跳转到指定的权限授权页面。
    3) 解决方法
        1) 如果在页面组件中进行权限的判断，可以实现。
           但是受限制的页面过多，每个页面都进行权限的判断，有些麻烦。
        2) 使用高级组件进行组件的包裹，或者自定义HOOK，可以实现。
        3) 自定义一个Route组件，该组件内部进行判断，符合条件，加载传入的组件。
           底层还是借助Route进行页面的匹配。

2. 开发思路
    1) 自定义Route要有Route的功能，即可以感知页面路径是否发生变化。
       所以还是需要借助Route组件。
    2) 利用Route组件的render属性或者component属性。
       属性值不在是模板函数，而是自定义函数。
    3) 这两个属性向Route组件中传入数据。
       当路径匹配成功，Route组件就会解析传入的数据，即执行传入的自定义函数。

    4) 传入模板，其实传入的就是一个函数，所以可以直接在这两个属性中自定义函数
       进行特殊的处理(比如权限的验证)。
       验证通过，则返回传入的组件，这样传入的组件就会挂载到Route下进行解析。
       验证失败，则返回Redirect组件，这样Redirect组件就会挂载到Route下进行解析。
       只要Redirect一解析，就会进行路径的跳转。


2. 具体的示范代码
```js
// 需要结构传入的参数，其中一些不需要的东西，可以提取出来
// 把component属性换个名字，变成组件的写法，这样可以通过<>进行使用。
function ZdyRoute({component: Component, render, ...prop}) {
    let a = false;
    // 返回Route组件，这样组件就挂载到了自定义组件下面，当自定义组件解析的时候
    // 解析到子组件，Route就会被解析，进行路径的匹配，实现功能
    return <Route {...prop} render={
        () => {
            // 表示验证通过，Route在解析子元素时，就会解析该元素
            if(a) {
                return <Component />
            }else {
                // 验证失败，Route在解析子元素时，就会解析该元素，进行路径的跳转
                // 并且把当前的路径传入，告诉权限验证页面，是从哪个路径跳过来的
                // 这样验证成功后，就可以立即在跳回来
                return <Redirect to={ {
                    pathname: "/c",
                    state: prop.location.pathname   // 传递的数据
                } } />
            }
        }
    }/>
}
```



## 一级路由(利用NavLink和Route开发一级路由)

```js
import { BrowserRouter as Router, Route, Switch, NavLink } from "react-router-dom";

class A extends React.Component{
    render () {
        return (
            <Router>
                <div className="nav">
                    <NavLink to="/b">香蕉</NavLink>
                    <NavLink to="/c">西瓜</NavLink>
                </div>
                <div className="route">
                    <Switch>  
                        <Route path="/b" component={ B }></Route>
                        <Route path="/c" component={ C }></Route>
                    </Switch>
                </div>
            </Router>
        )
    }
}

function B(props) { 
    return <>子组件B</>
}

function C(props) { 
    return <>子组件C</>
}
```

## 多级路由
在那一页使用多级路由，就在那一页像操作一级路由的方式写二级路由即可。

1. 原理: Route的匹配特性，以及可以用在任意的地方
    以二级路由为例。
    二级路由代码写在对应的一级路由的组件中。
    当路径符合一级路由时，就会解析一级路由组件，自然可以解析到内部的Route等组件。
    只要路径符合对应的Route，就会加载对应的组件，这样就形成了二级路由的效果。


2. 多级路由路径的问题
    1) 多级路由的路径肯定要依赖前一级路由的路径
       比如一级路由的路径为/a，则二级路由的路径就要为/a/?。
       
    2) 如果二级路由过多，后期一级路由的路径有发生了变化，这样修改起来就非常的麻烦。

    3) 如果有一个忘记修改了，就会发生下列问题
       1) 如果是Link或者NavLink忘记修改，当点击的时候，路径发生变化。
          此时父级路由的Route就匹配不到，此时组件就会被卸载。
          父级一卸载，子级也完了，路由自然就出现了大问题。
       2) 如果是Route忘记修改，则匹配不到，不显示。


3. 解决方法
核心为: 多级路由的路径，由前一级路由的路径进行自动拼接而成，方法有多种。
    
    1) 单独建立一个路径的js文件，建一个对象，统一对路径进行管理
       这样修改起来就不会漏掉，修改起来也有些麻烦

    2) 单独建立一个路径的js文件，建一个对象，统一对路径进行管理
       建的对象，符合路由的层级。
       然后在创建一个专门解析该对象的方法，按照层级进行路径的拼接，生成多级路由使用的路径
       这样在修改路径的时候，只需要修改一级，就可以。
    
    3. 在组件的内部，利用传入的路由参数(比如match.url或者location.pathname)。
       取出匹配成功的路径，即前一级路由的路径。
       然后在路径的地方使用字符串拼接的方式，拼接出对应的路径
    


## 动态路由

1. 原理分析
    1) 动态路由的核心为，一个路径固定，后面其它的路径可以随机，达到动态匹配的效果。
       比如: /a/1000、/a/10001。

    2) 动态路由的核心是Route组件的路径匹配规则，要达到动态匹配的功能。
       1) 路径使用`:`就能达到动态匹配的效果。比如/a/:id
       2) 只需要在组件内部判断:id对应的数据。
          不同数据显示不同内容，这样就产生了动态路由的效果。
       3) 不可以写/a进行不完全匹配，否则获取不到动态数据(id值)
       



2. **动态路由组件的使用非常容易出现问题的地方**
    1) 问题
       **动态路由对应的组件，如果是类组件**。
       当地址发生变化时，Route一解析，发现符合路径，继续加载该组件。
       此时类组件由于节约性能，会进行环境的复用，直接进入重渲染阶段。
       如果使用在componentDidMount中，获取id，然后进行判断，显示指定的内容。
       当路径发生变化，环境复用，componentDidMount是不会再执行的。

    2) 造成的后果: 动态路由切换，页面没有发生变化
    3) 解决的方式: 在componentDidMount和componentDidUpdate中都对id进行获取
       并且进行判断加载对应的内容。
    4) 如果是函数组件，就没有问题，函数组件每次都重新执行。


3. 组件中获取动态路由地址后面的id数据(动态路由开发的关键)
    通过`props.match.params.id`就可以获取到动态路由地址后面的id。
    **注意: 如果Route没有设置/:id，即利用不完全匹配的规矩匹配成功的，是无法通过params属性，获取id的**。


4. 示范代码
```js
import { BrowserRouter as Router, Route, Switch, NavLink } from "react-router-dom";

class A extends React.Component{
    render () {
        return (
            <Router>
                <div className="nav">
                    <NavLink to="/dong/1000" exact>香蕉</NavLink>
                    <NavLink to="/dong/1001">西瓜</NavLink>
                </div>
                <div className="route">
                    <Switch>  
                        <Route path="/dong/:id" component={ C }></Route>
                    </Switch>
                </div>
            </Router>
        )
    }
}

function C(props) {
    console.log(props.match.params.id);  
    return <>子组件</>
}
```



## 路由间的数据传递
两种方式，一种使用push的第二个参数进行数据的传递，一种使用路径进行数据的传递。



## 静态路由

1. 简单介绍
   1) 和动态路由的差别
      1) 以上的路由写法都是动态路由的写法，即路由的信息在组件中动态的写出来，位置不固定。
      2) 动态路由的好处在于灵活。
      3) 但是相对于静态路由来说，不容易维护。
   
   2) 静态路由，是把所有的路由信息统一写在一个js文件中，便于后期的维护。
      和vue中的路由使用十分相似。


2. 方式
    1) 需要自己单独进行封装，Router中并没有提供这样的写法。
    2) 方法: 在一个数组中，定义出所有的路由信息(和vue一样)
    3) 核心: 自定义数组解析方法，把对应的路由信息解析成对应的Route组件。
    4) 原理: 底层还是借助Route组件，只不过进行了统一管理。


3. 解析路由数组的常用方式，以涉及子路由为例
    1) 核心思想为: 子路由对应的Route组件
       在父路由传入的组件中进行定义，父路由匹配成功，才显示子路由。
    2) 方式: 
       1) 在统一解析路由信息数组的时候，如果该路由有子路由。
       2) 把生成的子路由对应的Route组件，通过行间传值的方式，传入组件中
       3) 这样路由加载的的组件中，就可以使用到传入的Route组件，进行子路由的匹配。


4. 具体的示范代码
```js
import { BrowserRouter, Route, Switch, NavLink } from "react-router-dom";

/**
 * 定义路由信息的数组(可以单独封装到一个js文件中)
 */
let RouterArr = [
    // 一级路由信息
    {
        path: "/",
        name: "home",
        exact: true,
        component: Home
    },
    {
        path: "/z",
        name: "z",
        component: Z,
        children: [
            // 子路由信息
            {
                // 这样写的目的在于，运算完子路由的第一个页面路径与父路由一样
                // 这样就能在一开始就加载第一个子路由信息。
                // 或者不写path。
                path: "",  
                name: "z",
                exact: true,
                component: Z1
            },
            {
                path: "z2",
                name: "z2",
                component: Z2
            },
        ]
    }
]

// 解析路由信息的方法(生成一级路由对应Route组件，子路由进行传入)
function a(data, url) {
    // 是回调生成子路由还是第一次调用生成一级路由
    let route = data || RouterArr;
    // 生成路径对应的Route组件
    let RouteM = route.map( (ele, i) => {
        // 结构信息
        let {path, component: Component, name, children, ...prop} = ele;
            
        return <Route {...prop} key={i}
            // 由于path中不能写函数，但是可以写表达式，所以需要变换立即执行函数
            path={ (() => {
                // 查看是否有path，没有使用父级路径，有就进行拼接，服务子路由。
                if(!path && url) {
                    return url
                }else if(url){
                    return url + "/" + path;
                }else {
                    // 一级路由
                return path;
                }
            })() }
            // 设置路由匹配成功加载的组件，如果有子路由，需要传入子路由对应的Route组件
            // 匹配成功，执行函数，进行组件的处理，也可以使用render函数
            component={ () => {
                // 如果存在子路由，传入子路由对应的Route组件
                if(children && typeof children === "object") {
                    return <Component>
                        {/* 进行递归创建子路由 */}
                        { a(children, !url ? path : url + "/" + path) }
                    </Component>
                }else {
                    // 如果没有不需要进行处理
                    return <Component />
                }
            } }
        />
    } )

    return <Switch>
        { RouteM }
    </Switch>;
}

function RouteJie() {
    // 解析路由文件
    return a();
}


// 主页面
function A() {
    return <BrowserRouter>
        <div>
            <NavLink to="/" exact> 主 </NavLink>
            <NavLink to="/z"> 子 </NavLink>
        </div>
        <div>
            {/* 启用解析路由信息的组件，开始解析路由信息，生成对应的一级路由Route组件
                子路由Route会进行传入 */}
                <RouteJie />
        </div>
    </BrowserRouter>
}

function Home() {
    return <div>主页面</div>
}
// 涉及到子路由
function Z(props) {
    return <>
        <div>子路由导航页面</div>
        <div className="nav">
            <NavLink to="/z">子1</NavLink>
            <NavLink to="/z/z2">子2</NavLink>
        </div>
        <div>
            {/* 使用传入的对应的子路由Route组件 */}
            { props.children }
        </div>
    </>
}
function Z1(props) {
    return <div>子路由页面1</div>
}
function Z2(props) {
    return <div>子路由页面2</div>
}
```


## 导航守卫函数
Router中并没有提供导航守卫函数，要想实现导航守卫函数，需要自己进行自定义
导航守卫，共分为三种情况
1. 当前组件感知到路由的变化，处理某些事情，但是并不会对切换造成任何阻塞
2. 当前组件感知到路由即将发生变化，判断是否达到了跳转条件
   达到才允许进行跳转，达不到，不进行跳转
3. 某个组件是否达到了进入的条件，达不到在退回到之前的路由

### 监控路径的变化

1. 借助的方法为，路由参数history中的`listen`方法。
   

2. 使用方式
    1) 该方法接收一个参数，参数为一个函数。
       1) 当路径发生变化时，传入的方法执行。
       2) 方法执行时会接收到几个参数
          1) 第一个参数，location对象，记录当前的地址信息
          2) 第二个参数，action，一个字符串，表示进入该地址的方式，共分为一下几种:
             1) POP: 出栈(页面回退)
                1) 通过点击浏览器后退，前进
                2) 调用history.go
                3) 调用history.goBack 
                4) 调用history.goForward

             2) PUSH: 入栈
                1) history.push
                2) 点击Link、NavLink组件生产的标签，底层调用的就是history.push方法

             3) REPLACE: 替换
                1) 调用history.replace

    2) listen方法存在返回值，返回值为一个函数，调用该函数可以销毁注册的方法

    3) 获取当前路径和即将要跳转的路径
       1) 当前路径: 通过props.location.pathname
          由于注册的方法是运行在路径即将发生变化，但是还没有进行变化的时候
          所以通过该方法获取的就是当前的路径
       2) 即将要跳转的路径: 通过注册方法的传参，location.pathname

3. 注意事项
    1) 该方法并不会随着组件的销毁而销毁，会一直工作。
        1) 只要该组件被执行，注册了函数。
        2) 哪怕路由切走，在别的组件进行路由的切换
        3) 本组件注册的方法还是会执行的。
    2) 该方法可以重复使用，处理方式为把注册的方法放入一个队列中
        1) 当路径发生变化，会一个一个的执行队列中的方法。
    3) 问题(重点): 
        1) 一个组件中注册了一次函数
        2) 但是路由切换时并没有把注册的方法销毁，下次路径在切回来时，就又注册了一次。
        3) 所以，每个组件使用listen注册方法时，一定要加上组件销毁也销毁注册函数的处理方式。
           如果想要监控多个路由页面，就把listen的使用往上提升，包裹住具体的Route组件。

    4) 注册的方法如果没有在组件销毁时进行销毁
       如果注册的方法中有获取当前路由的代码，则也受**闭包的影响**


 
4. 该方法通常用于打印路由日志
    1) 由于要监听整个路由，所以通过把该方法在最顶级组件中使用。

    2) 开发思路
       1) 路由的最顶级为BrowserRoute组件，或者HashRoute组件。
       2) 所以为了要监听整个路由，需要创建一个组件，包裹住整个Router组件下的内容。
       3) 又为了在写法上和正常的保持层级一致，把Router组件也封装进去。
       4) 这样外界只需要使用自定义组件即可，不需要再写Router组件，写法上保持了层级一致。

    3) 具体代码
    ```js
        import { BrowserRouter, Route, Switch, NavLink, withRouter } from "react-router-dom";

        function A() {
            return <RiZhi listen={ (pathnameD, pathnameX) => {
                console.log(`从${pathnameD}跳到了${pathnameX}`)
            } }>
               <div className="nav">
                   <NavLink to="/" exact>导航一</NavLink>
                   <NavLink to="/c">导航二</NavLink>
               </div>
               <div>
                   <Switch>
                        <Route path="/" exact component={ B }></Route>
                        <Route path="/c" component={ C }></Route>
                   </Switch>
               </div>
            </RiZhi>
        }

        // 封装打印日志文件
        function RiZhi(props) {
            return <>
                <BrowserRouter>
                    <Listen { ...props }/>
                    { props.children }
                </BrowserRouter>
            </>
        }
        // 注册listen方法的组件，由于该组件获取不到路由数据，所以需要使用withRouter进行包裹
        // withRouter会导致当路径发生变化，组件重新渲染
        // 所以使用函数组件注册listen，函数组件需要使用HOOK
        class Lis extends React.Component{

            componentDidMount() {
                // 如果没有传递该方法，说明不想监听路径变化
                if(this.props.listen) {
                    let a = this.props.history.listen( (location) => {
                        // 虽然listen会产生闭包的现象，但是由于组件被withRouter函数进行包装
                        // 每当路径发生变化时，组件进行重新渲染，传入的路由信息是最新的。
                        let pathnameD = this.props.location.pathname;
                        let pathnameX = location.pathname;
                        this.props.listen(pathnameD, pathnameX);
                    } )
                }
            }

            render() {
                return null
            }
        } 
        const Listen = withRouter(Lis);

        function B(props) {
            return <>B</>
        }
        function C() {
            return <>C</>
        }
    ```



### 对路径跳转加限制(阻塞路径跳转)

1. 基础设置，需要两个属性，配合使用
   1) 一个是history中的方法，`history.block()`
   2) 一个是Router组件上的行间属性`getUserConfirmation`传参(传递的也是一个方法)
   该属性有默认值


2. 使用方式
    1) 启用history.block，传入一个数据，只能传递字符串，传递其它类型的参数阻塞无效。
    2) getUserConfirmation中传入的方法可以接收到history.block中的方法
    3) getUserConfirmation中传入的方法接收到的第二个参数是一个函数
       1) 调用该函数，传入true，则允许本次跳转。
       2) 调用该函数，传入false，则阻止本次跳转。
       3) 不调用该函数，页面是无法进行跳转的
    4) history.block也有一个返回值，返回值为函数，调用该函数，可以取消页面阻塞
    5) history.block不写，getUserConfirmation是不起作用的


3. 注意点:
    1) history.block方法也是不会随着组件的销毁而销毁的
       即路由即使切换到了其它的路径，该阻塞也会对其产生影响
    2) 如果history.block没有被销毁，有调用了一次，则控制台弹出警告，阻塞以本次为准。
       getUserConfirmation中传入的方法接收到的第一个参数是新的block设置的。
    3) 为了防止组件间阻塞的相互影响，组件销毁的时候，调用block返回的函数，销毁阻塞
       如果是全局监控，则把block的范围往上提升，包裹住具体的Route组件。
    4) 在listen中启用block，对本次跳转无影响，但是对下一次跳转就开启了阻塞判断
       1) listen发生的时候，路由已经准备跳转，此时开启block已经没有用了
       2) 由于block不会销毁，本次没有用，但是却会作用于之后的路由切换

    5) Router组件的行间属性getUserConfirmation，**只能赋一次值**。
       即哪怕Router组件进行了重新渲染，getUserConfirmation中引入的函数发生了变化
       但是getUserConfirmation使用的还是初始赋的函数，和引用固化相同。
    
    6) getUserConfirmation属性传入的函数接收的第二个参数(是否允许跳转函数)
       **无论在那个地方调用，都可以进行是否跳转的操作**
       即通过调用，把该方法传入了其它的函数中进行调用，也能实现是否跳转。




4. 具体组件实现路径阻塞的效果(该组件满足条件，才允许跳转)
    1) 思路
       1) 封装出一个开启路径阻塞的组件，在需要的组件内使用
       2) 当监听的组件满足条件，就会进行跳转，则组件卸载
       3) 对应的封装组件也会被卸载，路径阻塞取消，下一次在进该组件的时候，重新创建。
          由于组件可能进行重新渲染，所以block函数需要在componentDidMount或者useEffect(, [])中创建，确保重新渲染不会再次调用block方法。
       4) 由于不同的子组件，处理页面跳转的方法不同
          由于getUserConfirmation重新渲染不会造成函数引用的改变
          所以不能直接赋值某个组件处理页面跳转的函数，而是应该进行调用

    2) 以输入框为例，当输入框中有内容时弹出是否进行跳转的提示框，点击确定进行跳转。
    ```js
    import { BrowserRouter, Route, Switch, NavLink } from "react-router-dom";
    import { useEffect } from "react";

    const block = {
        // 保存每个组件处理页面跳转的函数，存储方式有多种。
        confirmation: null,
        Block: (props) => {
            return <BrowserRouter getUserConfirmation={ (data, callBack) => {
                // 不能进行赋值，需要进行调用
                if(block.confirmation) {
                    block.confirmation(data, callBack);
                }else {
                    callBack(true)
                }
            } }>
                { props.children }
            </BrowserRouter>
        },
        Fblock: (props) => {
            // 确保某个组件开启页面阻塞的block只执行一次
            useEffect( () => {
                // 保存阻塞器
                let blockZ = null;
                // 是否开启阻塞
                if(props.confirmation) {
                    // 把组件具体的处理阻塞的方法进行传入
                    block.confirmation = props.confirmation;
                    // 开启阻塞
                    blockZ = props.history.block("");
                }
                
                // 组件卸载时，关闭阻塞处理
                return () => {
                    // 如果已经开启就关闭，没有开启不处理
                    blockZ && blockZ();
                }
            }, [])
            return null;
        }
    }
    // 分解组件
    const { Block, Fblock} = block;

    function A() {
        return <Block>
           <div className="nav">
               <NavLink to="/" exact>导航一</NavLink>
               <NavLink to="/c">导航二</NavLink>
           </div>
           <div>
               <Switch>
                    <Route path="/" exact component={ B }></Route>
                    <Route path="/c" component={ C }></Route>
               </Switch>
           </div>
        </Block>
    }

    class B extends React.Component {
        state = {
            value: ""
        }
        render () {
            return <>
                {this.state.value}
                <Fblock confirmation={ (date, callBack) => {
                    // 有内容不允许跳转
                    callBack(this.state.value ? false : true);
                }  } {...this.props} />
                <input type="text" value={ this.state.value } onChange={ (e) => {
                    this.setState({
                        value: e.target.value
                    })
                } }/>
            </>
        }
    }

    function C(props) {
        return <>C</>
    }
    ```

    3) react-router-dom中，提供一个组件`Prompt`，该组件有两种使用方式
       
       1) 行间属性`message`，传入一个函数。
          1) 该函数会在页面即将跳转时执行
          2) 如果执行返回false，则禁止跳转；如果返回true，则允许跳转
          3) 也能实现对一个页面进行离开守卫的工作
       2) 行间属性`when`传递true，开启阻塞，行间属性`message`传递一个字符串。
          表现形式为: 进行跳转时，询问是否进行跳转，点击确定可以跳转，点击取消禁止跳转
          行间属性message传递的字符串作为提示文字，其实就是getUserConfirmation传递的方法的第一个参数。


5. 监控全局的示范代码(和监控全局的listen封装在一块)
```js
import { BrowserRouter, Route, Switch, NavLink, withRouter } from "react-router-dom";

function A() {
    return <RiZhi listen={ (pathnameD, pathnameX) => {
        console.log(`从${pathnameD}跳到了${pathnameX}`)
    } }  block={ (date, callBack) => {
        console.log(date, 123);
        callBack(true);
    } }>
       <div className="nav">
           <NavLink to="/" exact>导航一</NavLink>
           <NavLink to="/c">导航二</NavLink>
       </div>
       <div>
           <Switch>
                <Route path="/" exact component={ B }></Route>
                <Route path="/c" component={ C }></Route>
           </Switch>
       </div>
    </RiZhi>
}

// 封装打印日志文件
function RiZhi(props) {
    return <>
        <BrowserRouter getUserConfirmation={ (date, callBack) => {
            props.block(date, callBack);
        } }>
            <Listen { ...props }/>
            { props.children }
        </BrowserRouter>
    </>
}


class Lis extends React.Component{
    componentDidMount() {
        // 如果没有传递该方法，说明不想监听路径变化
        if(this.props.listen) {
            let a = this.props.history.listen( (location) => {
                // 虽然listen会产生闭包的现象，但是由于组件被withRouter函数进行了包装
                // 每当路径发生变化时，组件进行重新渲染，传入的路由信息是最新的。
                let pathnameD = this.props.location.pathname;
                let pathnameX = location.pathname;
                this.props.listen(pathnameD, pathnameX);
            } )
        }
        // 如果没有传递该方法，说明不想进行页面的阻塞
        if(this.props.block) {
            let a = this.props.history.block("")
        }
    }

    render() {
        return null
    }
} 
const Listen = withRouter(Lis);

function B() {
    return <>B</>
}
function C() {
    return <>C</>
}
```



### 判断进入条件是否满足(不满足退回之前的路径)
使用listen监控全局的路由变化，当路由发生变化时，进行保存。
某个组件进入的判断可以在componentDidMount中完成。当不满足使用history.push()跳回之前的页面。



## 页面切换动画

1. 自定义Route组件实现动画切换
   1) 思路
       1) 不同页面的显示与否需要与路径进行匹配
          所以自定义Route组件需要实时监控路径的变化
          可以借助withRouter方法，对匹配路径完成组件显示的自定义组件进行包裹
          由于路径改变，所有的withRouter包裹的组件都会进行重新渲染
          此时就可以根据Route组件行间传参path，与当前点击的路径(location.pathname)做比较。
          相同的则为显示，不同的则为销毁。
       2) 动画控制的是一个页面的销毁，另一个页面的创建
          所以最好用的是TransitionGroup组件
          需要注意，该组件下只能跟CSSTransition或者Transition组件。

    2) 示范代码
    ```js
    import { BrowserRouter, NavLink, withRouter } from "react-router-dom";
    import { CSSTransition, TransitionGroup } from "react-transition-group"

    function A() {
        return <BrowserRouter>
           <div className="nav">
               <NavLink to="/" exact>导航一</NavLink>
               <NavLink to="/c">导航二</NavLink>
           </div>
           <div>
                <Route path="/" exact component={B}></Route>
                <Route path="/c" component={C}></Route>
           </div>
        </BrowserRouter>
    }
    
    class Route extends React.Component {
        state = {
            Component: null
        }
        componentDidMount () {
            // 使用withRouter创建组件，
            let component = withRouter(this.fuzhu);
            this.setState({
                Component: component
            })
        }
        render() {
            // 使用withRouter创建的组件
            const Component = this.state.Component;
            return <>
                { Component && <Component {...this.props} /> }
            </>
        }
        // 辅助组件(该组件被withRouter包装，所以当路径发生变化，该组件重新执行)
        fuzhu = (props) => {
            // 判断当前组件是显示还是隐藏
            let boo = props.path === props.location.pathname;
            // 启用动画，不渲染默认父级
            return <TransitionGroup component={ null }>
                {boo && <CSSTransition timeout={500} key={boo}>
                    <props.component />
                </CSSTransition>}
            </TransitionGroup>
        }
    }

    function B(props) {
        return <div>CDDD</div>
    }

    function C(props) {
        return <div>dfad</div>
    }
    ```



## 路由切换后滚动条复位

1. 问题
    1) 由于路由模式是无刷新更改页面，如果两个页面都存在滚动条
    2) 并且某个页面在浏览时，滚动条进行了滚动。然后进行页面的切换
    3) 切换后滚动条的位置是不会发生变化的


2. 解决方法
   1) 由于路由的切换，就是组件的卸载与重新解析
   2) 所以可以在组件的componentDidMount或者useEffect中进行滚动条的复位。




## 导航栏使用其它的标签
在react中的路由导航，默认是a标签，并且不能进行修改，不像vue一样可以随意修改。
如果想要使用其他标签实现导航栏的效果，需要重新封装一个组件。


1. 方式
   1) 如果想要实现Link标签的效果，比较简单，直接用withRouter包裹一下，获取到history对象，使用push方法就可以实现。
   2) 如果想要实现NavLink标签的效果，稍微麻烦一点，需要与当前的路径进行匹配。
      1) 可以借助path-to-regexp插件自己进行匹配。使用withRouter进行包装获取路由环境
      2) 可以借助Route标签，该标签自带匹配功能，把内容作为Route组件的children，这样无论匹配成功与否都会显示。
      然后把to设置为Route标签的path，如果匹配成功Route注入的路由数据的match不为空，如果没有匹配成功，则match为null，利用该属性可以动态添加class。
      当路由发生变化，Route进行重新渲染，又会重新匹配，设置class。


2. 借助Route组件，进行自定义导航标签的封装
```js
const MenuLink = ({to, ...props}) => {
  return (
    <Route path={to} {...props} children={ (p) => {
      console.log(p);
      return (
        <span onClick={ () => { p.history.push(to) } } 
              className={p.match ? "active" : ""}>
          { props.children }
        </span>
      )
    }} />
  )
}
```



# Router源码解析

## 路由数据源码的解析
### match对象的底层实现(路径匹配的实现)

1. 借助的辅助插件
   1) 在router中，Route组件和NavLink组件，都涉及到了路径的匹配。
   2) 而路径的匹配其实是对应的正则与当前路径进行匹配。
   3) 也就是router会把组件上的path和to转换成对应的正则。
   4) router中并没有写路径转换成正则的代码，而是借助的一个第三方库实现的
   5) 第三方插件的名称为`path-to-regexp`，下载方式`npm i path-to-regexp -D`
   6) 该插件不用自己下，router会自动下载


2. 辅助插件的用法
   1) 引入该插件，为一个方法，该方法返回成功解析的正则表达式。
   2) 该方法有三个参数
      1) 第一个参数为路径，多种写法，其中一种为/a/:id/:a，符合router环境
      2) 第二个参数为一个数组，当函数执行时，会向该数组注入关键字的信息(:后面的信息)
      3) 第三个参数为对象，设置匹配的模式，是完全匹配还是非完全匹配，以及很多
         这些模式有固定的属性，比如完全匹配为end: true，不完全匹配为end: false
         默认是完全匹配
   3) 测试代码
   ```js
    import regexp from "path-to-regexp";

    function pathMatch() {
        let arr = [];
        const regex = regexp("/a/:id/:a", arr);
        const a = regex.exec("/a/dasfd/fafdf");
        console.log(regex, arr, a);
    }

    pathMatch();
   ```


3. metch对象的构建方式
   1) 借助了正则匹配的结果，与第三方插件第二个参数，配合构建出该对象。
   2) 代码如下:
   ```js
    import regexp from "path-to-regexp";
    
    // 构建match对象的方法
    function pathMatch(path, pathname, options) {
        let keys = [];
        // 匹配模式的初始值
        let _options = {
            exact: false,
            strict: false,
            sensitive: false
        }
        // 匹配模式的合并
        _options = { ..._options, ...options}
        // 转换为regexp识别的匹配模式
        let __options = {
            end: _options.exact,
            strict: _options.strict,
            sensitive: _options.sensitive
        }
        const regExp = regexp(path, keys, __options);
        const result = regExp.exec(pathname);
        // 表示没有匹配成功
        if(!result) {
            return null
        }

        // 匹配成功，构建match对象
        const match = {};
        // 构建当前的匹配是否是完全匹配出的结果
        // 匹配结果的第一项就是匹配成功的路径，判断与传入的路径是否相同。
        const isExact = result[0] === pathname;
        // 构建params对象
        const params = {};
        for(let i = 0; i < keys.length; i ++) {
            params[keys[i].name] = result[ i + 1 ];
        }
        // 构建path
        const paths = path;
        // 构建url
        const url = result[0];
        
        // 填充match
        match.isExact = isExact;
        match.params = params;
        match.path = paths;
        match.url = url;
        
        return match
    }
    let match = pathMatch("/a/:id/:a", "/a/dfiad/adfadsf/adfadf");
    console.log(match)
   ```



### history对象的底层实现(核心)

1. 借助的插件
   1) history对象的构建，router也依赖了某个插件
   2) 该插件的运行结果，返回的就是一个history对象
   3) router中的路由参数history对象，就是该对象，其它的功能都是使用该对象中的方法进行实现的


2. 插件的使用方式
   1) 该插件中有三个最核心的方法。创建对应三种模式的history对象
   2) 三种模式分别为浏览器历史模式，hash模式，手机端模式(内存存放地址)
   3) 三种history对象虽然对应的模式不同，但是功能以及history对象的结构是一样的
   4) history对象的功能，维护同一个地址栈。


3. 插件创建history对象(三种模式)的怪异之处(扩展)
   1) 在history对象中，有一个action属性。
      1) 该属性，只要是使用createXXXHistory方法新创建出来的history，该属性都为POP。
      2) 使用history对象中的push方法，action变为PUSH
      3) 使用history对象中的replace方法，action变为REPLACE
   2) 多次使用方法创建history对象，会发现length属性对应的数据一样
      1) length标记着地址栈中存在的地址
      2) 相同说明，history对象的创建其实是根据地址栈中的数据，进而创建出来的
      3) length相同，说明底层操作的是同一个地址栈

   3) 既然是克隆，则action属性应该相同。但是新创建的由于特性，强制变成了POP
      之前可能通过puth添加路径，action变为PUSH，说明当前的地址栈通过添加了一条路径
      但是根据新history对象的action属性，看不出来当前地址栈中的地址是通过push方法添加的

   4) 之前创建的history对象，使用push添加路径，相对来说对新的history没有关系，所以history就统一把action置成了POP，目的是为了减少耦合。

   5) 正是由于这种操作方式，造成了创建出来的history对象中的action不可信，
      router中，对该属性进行了重新操作，操作方式使用history对象中listen方法，
      让action等于listen传入方法的第二个参数，当路径发生变化，重新赋值


4. history对象中的重要属性
    1) `push`，通过该方法向地址栈中新压入一个地址
    2) `listen`，该方法监听地址栈的变化，只要地址栈的指针指向发生变化，就会执行listen中传入的函数
       1) 传入一个方法，传入的方法有两个属性
          1) 一个是地址栈指针变化，指针将要前去的地址
          2) 另一个是操作地址栈的方式，该属性可以用来更新对象中的action属性
       2) router底层就是借助该方法实现的路由变化，重新渲染Route组件的效果
       3) 该方法可以多次使用，注册多个方法，当地址栈指针指向发生变化时，依次执行。
          这也是为什么，router用它设置了重新渲染，我们还可以使用该方法监听路径


5. 插件中`createBrowserHistory`(浏览器历史模式)使用方式
   1) 该方法有一个参数可以传递，格式为对象，但是也可以不进行传递，不是必传的属性
   2) 传入的对象被称为配置对象，内部参数的属性分别为
      1) `basename`，设置基路径，router中也支持该属性
         1) 介绍: 比如通过push跳转到/a，location中的数据是是/a。但是浏览器的地址栏中实际访问的是`基路径 + /a`，Route组件进行匹配时，也是`基路径 + path设置的路径`
         2) 作用: 可以用于开发多个组件共用的路由，只需要该一下基路径，就能完美匹配。
      2) `forceRefresh`，如果该属性设置为true，则使用history.push，会强制刷新页面
      3) `keyLength`，设置location对象中的key值的长度，默认是6。
         location对象中的key属性的作用
         地址栈中存放的并不是我们手写的/a，这样只是为了便于阅读。
         实际存储的是一个location对象，有可能存入的地址一样，则location对象相同，
         为了区分location对象，创建了一个随机数的key值
      4) `getUserConfirmation`，该属性传递的是一个函数，当history.block方法启用后
         然后当地址栈中的指针指向发生变化时，执行传入的函数，在函数中进行是否允许指针变化的操作。
         router中对页面进行阻塞，使用的就是该方法



6. 插件中`createHashHistory`(哈希模式)使用方式
1) 该方法有一个参数可以传递，格式为对象，但是也可以不进行传递，不是必传的属性
   2) 传入的对象被称为配置对象，内部参数属性分别为
      1) `hashType`: #号后给定的路径格式，该属性的属性值以及作用
         1) hashbang: 该方式已经被Chrome弃用，格式为#!路径，比如: #!/a/b/c
         2) noslash:  #号后面第一个路径没有/，比如: #a/b/c
         3) slash:    #号后面第一个路径有/(正常)，比如: #/a/b/c
      2) `basename`，设置基路径，router中也支持该属性
         1) 介绍: 比如通过push跳转到/a，location中的数据是是/a。但是浏览器的地址栏中实际访问的是`基路径 + /a`，Route组件进行匹配时，也是`基路径 + path设置的路径`
         2) 作用: 可以用于开发多个组件共用的路由，只需要该一下基路径，就能完美匹配。
         为了区分location对象，创建了一个随机数的key值
      3) `getUserConfirmation`，该属性传递的是一个函数，当history.block方法启用后
         然后当地址栈中的指针指向发生变化时，执行传入的函数，在函数中进行是否允许指针变化的操作。
         router中对页面进行阻塞，使用的就是该方法

2) 注意点
    1) 使用createHashHistory创建出的history对象，使用push操作的就是#后面的模拟地址，实际地址不会发生变化
    2) location对象中有一个hash，获取当前的哈希值，获取的不是实际的哈希，而是哈希中的模拟哈希。比如: #/a/b/c#123，获取到的是123。





## 实现BrowserRouter组件
1. 该组件要实现的功能：创建路由环境
   1) 其实路由环境的创建，并不是该组件完成的，而是下面的子组件Router完成的。
   2) 该组件只是创建了一个浏览器模式的history对象，然后传入Router组件中
   3) location对象，以及match对象都是在Router组件中创建的，然后Router根据这三个对象创建执行期上下文环境(路由环境)
   4) 由于三中模式生成的location对象和match对象都是相同的。所以把这两个对象的数据进行了封装。

2. 实现代码
```js
/** BrowserRouter组件中的代码 */
import { Router, pathHistory } from "../react-router/index";
import React from "react";

export default class BrowserRouter extends React.Component {
    render () {
        // 结构状态
        const {children, ...action} = this.props;
        // 创建history对象，并传入
        return <Router history={ pathHistory(action) }>
            { this.props. children }
        </Router>
    }
}

/** Router组件中的代码 */
import React from "react";
import ctx from "./context";
import pathMatch from "./pathMatch";

export default class Router extends React.Component {
    // 接收一个history对象

    // 定义状态是为了，路径发生变化重新渲染
    state = {
        location: this.props.history.location,
        listen: null
    }

    // 重新渲染的操作，就是监控路径
    componentDidMount() {
        this.state.listen = this.props.history.listen( (location, action) => {
            this.props.history.action = action;
            this.setState({
                location: location
            });
        } )
    }
    
    render() {
        const history = this.props.history;
        // 创建location对象，在history对象中就有
        const location = this.state.location;
        // 初始状态，认为对/进行匹配
        const match = pathMatch("/", location.pathname);
        return <ctx.Provider value={ {history, location, match} }>
            { this.props.children }
        </ctx.Provider>
    }
    
    // 组件销毁时，销毁监听，不太可能发生
    componentDidUnMound() {
        this.state.listen();
    }
}


/** 创建执行期上下文中的代码 */
import React from "react";
let context = React.createContext();
export default context;
```


## 实现Route组件
1. 该组件要实现的功能：匹配路径进行一系列的操作

2. 具体操作
   1) 获取执行期上下文中路由的数据，主要的是location，内部的pathname就是当前的路径
   2) 使用pathMatch进行path与pathname的匹配
   3) 匹配成功，创建新的执行期上下文，保存history(不变)，location(不变)，match(新的)
   4) 处理一系列内容

3. 实现代码
```js
import React from "react";
import { context as ctx, pathMatch } from "../react-router/index";

export default class Route extends React.Component {

    // 处理children
    children = () => {
        // children无论什么时候都会解析
        return this.props.children
    }

    // 处理component
    component = (value) => {
        // 只有匹配成功时才会执行，也就是只有match有值才会执行
        if(value.match && this.props.component) {
            let Component = this.props.component;
            return <Component {...value}/>
        }
        return null
    }

    // 处理render
    renders = (value) => {
        // 只有匹配成功时才会执行，也就是只有match有值才会执行
        if(value.match && this.props.render && !this.props.component) {
            let Render = this.props.render;
            return <Render {...value}/>
        }
        return null
    }

    // 处理ReactDom
    ReactDom = (value) => {
        return <>
            { this.children() }
            { this.component(value) }
            { this.renders(value) }
        </>
    }

    // 获取执行期上下文中的数据，先进行匹配(创建match对象)，构建新的执行期上下文
    getContext = (value) => {
        const history = value.history;
        const location = value.location;
        // 构建匹配对象
        const action = {
            exact: this.props.exact || false,
            strict: this.props.strict || false,
            sensitive: this.props.sensitive || false
        }
        const match = pathMatch(this.props.path || "/", location.pathname, action);
        return <ctx.Provider value={ {history, location, match} }>
            { this.ReactDom( {history, location, match} ) }
        </ctx.Provider>
    }

    render () {
        return <ctx.Consumer>
            { this.getContext }
        </ctx.Consumer>
    }
}
```


## 实现Link组件
1. 该组件要实现的功能：无刷新跳转页面

2. 方式
   1) 底层封装一个a标签，阻止默认行为，使用history.push进行跳转
   2) 需要获取执行期上下文中的history对象。
   3) 需要判断to的类型，可能是字符串，可能是对象，如果是对象，转换成字符串

3. 实现代码
```js
import React from "react";
import { context as ctx } from "../react-router/index"

export default class Link extends React.Component {
    
    state = {
        history: null
    }

    render() {
        return <ctx.Consumer>
            { this.consumer }
        </ctx.Consumer>
    }

    consumer = (value) => {
        this.to = this.toF();
        
        this.state.history = value.history;
        return <a onClick={ this.push } href={ this.to } 
               className={this.props.className}>{ this.props.children }</a>
    }

    push = (e) => {
        e.preventDefault();
        this.state.history.push(this.to);
    }

    // 专门处理to的方法
    toF = () => {
        let to = this.props.to;
        // 处理路径
        if(typeof this.props.to === "object") {
            to = this.props.to.pathname;
            if(this.props.to.search) {
                to += this.props.to.search;
            }
            if(this.props.to.hash) {
                to += this.props.to.hash;
            }
        }
        return to;
    }
}
```


## 实现Switch组件
1. 该组件要实现的功能：匹配成功，就终止Route组件的匹配

2. 方式
   1) 使用for循环，遍历children(Route)组件
   2) 通过Route组件的React元素对象，是可以读取到Route行间的path属性的
   3) 在Switch组件中先使用path，进行匹配，成功才处理Route组件，并立即终止for循环
   4) 需要获取上下文中的location，进行匹配

3. 实现代码
```js
import React from "react";
import { context as ctx, pathMatch } from "../react-router/index"

export default class Switch extends React.Component {
    render() {
        return <ctx.Consumer>
            { this.consumer }
        </ctx.Consumer>
    }
    consumer = (value) => {
        return this.children(value.location.pathname)
    }

    // 循环children，匹配显示
    children = (url) => {
        let children = [];
        if( Array.isArray(this.props.children) ) {
            children = this.props.children
        }else if(typeof this.props.children === "object"){
            children = [ this.props.children ]
        }

        for(let i = 0; i < children.length; i ++) { 
            // 构建匹配参数
            const action = {
                exact: children[i].props.exact || false,
                strict: children[i].props.strict || false,
                sensitive: children[i].props.sensitive || false
            }
            if ( pathMatch(children[i].props.path, url, action) ) {
                return children[i]
            }
        }

        return null
    }
}
```



## 实现NavLink组件

1. 组件作用: 匹配路径，给匹配成功的组件添加特定的class属性

2. 方式
   1) 底层还是借助了Link组件
   2) NavLink组件的作用就是，根据to匹配路径，匹配成功，向Link组件传递一个特殊的class名。其它属性照常传入，Link组件会生成对应的a标签，把样式添加上。
   3) 由于需要匹配路径，所以也需要获取到执行期上下文中的数据(Router重新渲染时，会重新渲染)
   4) 也需要处理to，to可能是字符串，可能是对象，如果是字符串可能含有数据或者锚点，
      而进行匹配，需要的是纯路径。为了便于处理，统一把字符串转换成对象格式，取内部的pathname。


3. 实现代码
```js
import React from "react";
import { context as ctx, pathMatch } from "../react-router/index";
import Link from "./Link";

export default class NavLink extends React.Component {
    render() {
        return <ctx.Consumer>
            { this.consumer }
        </ctx.Consumer>
    }
    

    consumer = (value) => {
        // 处理路径
        const to = this.to();

        const {activeClassName, ...props} = this.props;
        const action = {
            exact: props.exact || false,
            strict: props.strict || false,
            sensitive: props.sensitive || false
        }
        let className = null;
        if(pathMatch(to.pathname, value.location.pathname, action)) {
            className = activeClassName || "active";
        }
        return <Link {...props} className={ className }>
            { this.props.children }
        </Link>
    }

    // 专门处理to的方法
    to = () => {
        let to = this.props.to;
        if( typeof to === "string" ) {
            to = {};
            const searchIndex = this.props.to.indexOf("?");
            const hashIndex = this.props.to.indexOf("#");
            if(hashIndex > -1) {
                to.hash = this.props.to.substring(hashIndex);
            }
            if (searchIndex > -1 && hashIndex > -1){
                to.search = this.props.to.substring(searchIndex, hashIndex)
            }
            else if(searchIndex > -1) {
                to.search = this.props.to.substring(searchIndex)
            }
            if (searchIndex > -1){
                to.pathname = this.props.to.substring(0, searchIndex)
            }
            else if(searchIndex === -1 && hashIndex > -1) {
                to.pathname = this.props.to.substring(0, hashIndex)
            }
            else {
                to.pathname = this.props.to
            }
        }
        return to;
    }
}
```
  

## 实现高阶函数withRouter

1. 作用: 包装一个组件，使其可以获取到上下文中的数据

2. 方式: 使用上下文的Consumer组件包装一下就可以实现

3. 示范代码
```js
import React from "react";
import ctx from "./context";

export default function withRouter(Elem) {
    return function WithRouter() {
        return <ctx.Consumer>
            {
                (value) => {
                    return <Elem {...value} />
                }
            }
        </ctx.Consumer>
    }
}
```



# redux
需要下载的插件`npm i redux -D`;


## action数据

1. 作用
    action数据概念，是根据后端MVC思想衍生的一种适用于前端处理数据的思想。

2. 解决的问题(组件间共享数据的问题)
    1) 大型项目处理数据的问题
       1) 一个大型项目，伴随着有很多的组件。
       2) 如果很多组件需要共享大量的数据，就需要把数据提升到公共的父级(props向下传参，上下文)
       3) 但是这样容易出现一个非常严重的问题，公共父级中的数据，不知道被那个组件修改，如果出现问题，调试起来非常困难
    2) 借用后端MVC模式处理数据的不现实情况
       1) 前端是无法实现完整的MVC思想的。归根原因，前端处理数据的方式太多，太杂。
       2) 比如页面中有大量的组件，这些大量的组件中含有非常多的事件，用来处理数据。
       3) 如果把这些事件都写在控制器中，则控制器就会变得非常庞大。
    3) 后端可以使用MVC模式处理数据的原因
       1) 根据请求，把请求分发到控制器中，进行数据的处理。由于请求都是固定的，一共就那么几个，这样构造器就变得很整洁简单。
       2) 根据这一思想，就诞生了action数据。

3. action数据的使用
   1) action其实就是一个对象，就相当于请求地址
   2) action的好处在于，把视图与控制器的直接关系给断开了
   3) 视图在修改数据的时候，构建一个action对象(描述我要修改该数据了，如果修改)

4. action的使用
   1) action是一个对象，内部必须有一个type属性，表示约定的状态。
   2) 其它属性可以随意，但是通常action对象中只有两个属性
      另一个为payload属性，表示附加信息，比如给某个状态赋的新值
   3) action的示范样式
   ```js
   var action = {
      type: "REPLACE",   // 约定该状态为替换  
      payload: {a: 123}  // 替换的属性和新的数据
    }
   ```
   4) 注意: action对象只能是有Object构造函数直接生成的(即平面对象)，即它的原型为Object.prototype。不能通过自定义的构造函数进行创建。


5. action的使用需要依赖很多东西
   1) action就是一个修改数据的描述，并不能修改数据。
   2) 要想修改数据，需要专门解析action的函数，以及把action发送到解析函数的函数。
   3) 这就是redux的底层功能(发送action，解析action, 修改存放的数据)


6. 扩展
   1) 由于大型项目中，修改数据的规则有很多，action的type对应的属性值有很多种，为了防止出现问题，单独起一个js文件，专门定义type规则以及根据规则生成对应action对象的函数。


## reducer函数

1. 作用
   该函数需要进行自定义，是一个专门解析action对象，处理数据的函数。

2. 使用
   该函数接收两个参数，第一个是仓库中存放的数据，第二个是action对象

3. 注意点
   1) 该函数根据action修改状态时，最好不要直接修改第一个传入的参数(仓库数据)
   2) 而是应该克隆出一份仓库数据，修改克隆出的数据，然后在返回克隆出的数据，进行保存。
   3) 该函数中，不要出现异步操作，不要修改外部数据，不要有副作用。

4. 示范代码
```js
// 定义仓库中的初始数据
const mystate = {
  name: "lsz",
  age: 18
}

function reducer( state = mystate, action ) {
    // 克隆仓库数据
    const newState = JSON.parse( JSON.stringify(state) );

    switch (action.type) {
        case "CHANGE":         // 修改规则
            ··· ··· ···        // 修改数据的操作
            return newState    // 返回修改后的数据，进行保存
        default:
            return state;      // 如果啥都没修改，直接返回原数据
    }
}
```   


## createStore函数

1. 作用
   使用该函数，创建出一个操作仓库的对象，以及创建仓库

2. 使用
   1) 该函数，可以接收三个值，一个值为reducer函数，一个为仓库的原始数据，一个为中间键函数
   2) 该函数是插件redux中提供的一个方法，所以需要进行导入
   3) 由于该函数传递reducer函数，会在创建数据库的时候直接执行一次。
   传入的action是自定义的，type属性是随机的，所以与reducer中定义的状态不同，返回初始对象进行保存，所以该函数的第二个参数可以不用传入。


3. 示范代码
```js
import { createStore } from "redux";

const mystate = {
  name: "lsz",
  age: 18
}

const reducer = ( state = mystate, action ) => {}

// 创建仓库
const store = createStore(reducer);  

export default store;
```



## dispatch函数

1. 作用
   使用该函数，可以把创建好的action对象传入到reducer函数中，并且执行reducer函数处理action，更改数据

2. 使用
   该函数，是创建仓库时，得到的仓库对象中的一个方法。
   该函数，接收一个参数，action对象。


3. 使用方式
```js
import store from "./store"; // 引入数据库组价

var action = {}
    
store.dispatch(action);
```

## subscribe函数

1. 作用
   1) 该函数被称为发布订阅函数，通过该函数，可以订阅一个函数，当仓库发生变化时(dispatch函数被调用时)，会触发订阅函数。和router中的listen函数的功能差不多。
   2) 通过该函数，可以实现React监听仓库变化，从而刷新组件的功能。

2. 使用
   1) 该函数也是，创建仓库时得到的仓库对象中，提供的一个方法。
   2) 该方法接收一个参数，该参数必须是一个函数
   3) 该方法可以被多次使用，订阅多个函数，不会发生覆盖现象，会逐步执行
   4) 该方法返回一个方法，通过调用该方法，可以取消本次使用订阅的方法，通常在组件销毁时使用


3. 代码示范
```js
import store from "./store"; 

// 订阅函数
store.subscribe( () => {
    console.log("仓库数据发生了变化");
});
```



## getState函数

1. 作用
   用于获取仓库中的数据

2. 使用
   1) 该函数是，创建仓库时得到的仓库对象中，提供的一个方法
   2) 该方法没有参数，只有一个返回值，即仓库中的数据


3. 代码示范
```js
import store from "./store"; 

// 订阅函数
const state = store.getState();
console.log(state);
```



## bindActionCreators函数

1. 作用
   1) 该函数可以加强构造action函数的功能
   2) 在大型项目中，为了便于状态与action对象的管理，统一放在js文件中。并且有专门的函数生成action对象。
   但是这些函数只能创建action对象，要想使用reducer进行解析，还必须调用dispatch函数，把创建好的action对象进行传入。
   使用该函数进行包装后，会自动封装dispatch函数，不用再手动调用dispatch函数。

2. 使用
   1) 该函数是redux组件提供的方法，所以使用时需要从redux中引入。
   2) 该函数接收两个参数，一个是要包装的生成action对象的函数，一个是dispatch函数
   3) 如果想要包装多个生成action对象的函数，可以把这些函数放在一个对象中，作为该函数的第一个参数进行传入。
   4) 如果传入的是对象格式，该函数返回的也是一个对象格式的数据，并且属性值和传入对象的属性值相同(便于使用)，属性值对应的action对象不变
   但是对应的属性值发生了变化，变成新的函数，该内部集成创建action对象函数的调用，以及使用传入的dispatch发生action对象)
   5) 如果传入的是一个单独的函数，返回值是一个新函数，内部集成action的创建与dispatch发送。


3. 示范代码
```js
import store from "./store.js";
import {bindActionCreators} from "redux";
// 生成action对象的函数对象(传参要求必须放在一个对象中)
let obj = {
    a: function () {
        return {
            type: "a"
        }
    },
    b: () => ({
        type: "b"
    })
}
// 正常使用，第一步创建action，第二步使用dispatch
const action = obj.a();
store.dispatch(action);


// 使用bindActionCreators，传入对象，返回值也为函数
const objDispatch = bindActionCreators(obj, store.dispatch);
// 创建action对象加使用store.dispatch函数发送
objDispatch.b();   


// 使用bindActionCreators，传入单独的函数，返回值也为函数
const aDispatch = bindActionCreators(obj.a, store.dispatch);
// 创建action对象加使用store.dispatch函数发送
aDispatch();  
```



## combineReducers函数

1. 作用
   该函数的作用就是合并reducer函数，实现了仓库的组件化开发。


2. 原理
   1) 其实该函数的底层就是，创建了一个新的reducer函数，因为仓库的创建只能接收一个reducer函数，如果想要使用多个reducer函数进行功能模块化开发，必须把这些reducer函数封装在一个reducer函数中。
   2) 当使用dispatch传入状态时，调用封装好的reducer函数，但是在封装好的reducer函数中，会调用传入的所有模块化的reducer函数，然后把每一个reducer函数的返回值(数据)，保存到仓库中的一个属性中
   3) 相当于在仓库中开辟了一个子区域，由于所有的reducer函数都会运行，所有type在定义时，不要出现相同的。


3. 使用
   1) 该函数是redux组件中的函数，所以在使用时需要从redux中引入
   2) 该函数接收一个参数，参数格式为对象，对象的属性值就是每个模块的reducer函数


4. 代码示范
```js
// A模块对应的数据
const mystateA = {
    a: 1
}
function reducerA(state=mystateA, action) {}

// B模块对应的数据
const mystateB = {
    b: 2
}
function reducerB(state=mystateB, action) {}

// reducer的封装
const reducer = combineReducers({
    reducerA,
    reducerB
})

// 创建仓库时使用的是最终封装好的reducer函数
const store = createStore(reducer);

/** 最终仓库中保存的数据格式为
{
    该属性对应模块A的数据
    reducerA: {
        a: 1
    }
    该属性对应模块B的数据
    reducerB: {
        b: 2
    }

    所以要想获取某个模块中的数据，需要从具体的属性中获取，
    比如获取组件A中的a数据，方式为: store.getState().reducerA.a
}
*/
``` 



# redux源码

## 实现createStore函数的功能

1. 思路
   1) 该方法创建一个数据库，并且返回一个对象
   2) 在开始时就调用一次reducer方法，并且传入一个随机type
   3) 该方法可以传递三个参数，第三个参数先不考虑。第二个参数也分情况，如果是函数形式，则为中间键函数(默认值没有传递，第二个参数也可以传入中间键函数)
   4) 返回的对象中有一系列的方法
      1) getState方法: 调用该方法，直接返回内部的数据仓库
      2) dispatch方法，自动调用reducer方法，自动调用订阅的方法
      3) subscribe方法，使用该方法订阅一个方法，并且返回一个方法，取消订阅的方法


2. 代码示范
```js
export default function createStore(reducer, mystate, z) {
    // 对reducer函数进行保存，仓库中有一个方法，可以替换reducer函数
    const reduc = reducer;
    // 保存数据(仓库)
    let state = null;
    // 默认值位置不是中间键函数
    if(mystate && typeof mystate != "function") {
        state = mystate;
    }
    // reducer是不是个函数，如果不是需要抛出错误
    if(typeof reducer != "function") {
        new Error("reducer 必须为 function");
    }
    // 初始时调用一次reducer函数
    state = reduc(state, {
        // action的type随机
        type: `@@redux/INIT` + randomStr(6).split("").join(".")
    })
    
    // 如果reducer执行完(按理说应该匹配不成功)，如果得到的是undefined
    // 说明reducer中没有处理匹配不成功的情况，这种是错误的reducer函数，需要抛出错误
    if(state === undefined) {
        new Error("reducer 有问题")
    }

    // 保存订阅函数的数组，可以订阅多次
    const subscribeFun = [];

    return {
        getState: () => {
            // 返回仓库数据
            return state;
        },
        subscribe: (func) => {
            // 订阅的必须是一个方法
            if(typeof func !== "function") {
                new Error("订阅的不是一个函数");
            }
            // 进行订阅
            subscribeFun.push(func);

            // 返回取消订阅的函数
            return function () {
                // 查询到订阅函数的位置
                const index = subscribeFun.indexOf(func);
                // 移出订阅函数
                subscribeFun.splice(index, 1);
            }
        },
        dispatch: (action) => {
            // 使用reducer函数，重新保存修改后的数据
            state = reduc(state, action)

            // 触发订阅函数
            subscribeFun.forEach( (ele) => {
                ele();
            } )
        }
    }
}

// 创建指定长度的随机字符、数字
function randomStr(length) {
   return Math.random().toString(36).substr(2, length)
}
```


## 实现combineReducers函数

1. 思路
    1) 返回一个全新的reducer函数
    2) 对每个reducer函数也进行了验证，防止没有处理匹配不成功返回原始数据的操作
    3) 其实是验证了两次，防止内部强行定义type含有@@redux/INIT字符串，返回初始值的处理方式
    4) 具体的reducer函数在调用时，需要传入对应的数据，不能传入整个state数据
    5) 还需要考虑，可能reducer是创建仓库时初始调用，此时state为null，没有该属性
       

2. 代码示范
```js
export default function combineReducers(obj) {
    // 判断传入的是否为对象格式
    if(typeof obj !== "object") {
        new Error("传参错误")
    }
    for(let prop in obj) {
        // 逐渐执行reducer函数，进行reducer的第一次验证
        const a = obj[prop](null, {
            type: "@@redux/INIT" + randomStr(6).split("").join(".")
        })
        if(a === undefined) {
            new Error("reducer 有问题")
        }
        // 进行reducer的第二次验证，防止固定@@redux/INIT返回初始值
        const b = obj[prop](null, {
            type: "@@redux/PROBE_UNKNOWN_ACTION" + randomStr(6).split("").join(".")
        })
        if(b === undefined) {
            new Error("reducer 有问题")
        }
    }
    
    // 返回新的reducer函数
    return function (state, action) {
        // 判断传入的action有没有type属性
        if(!action.type) {
            new Error("action没有type属性")
        }
        // 定义仓库数据
        const stateObj = {};
        for(let prop in obj) {
            // 逐渐执行reducer函数，并且保存返回值
            // 注意，具体的reducer函数，需要传入对应的数据，不能传入整个state数据
            // reducer可能创建仓库时调用，需要考虑到没有该属性值的情况。
            stateObj[prop] = obj[prop](state && state[prop], action)
        }
        return stateObj
    }
}

// 创建指定长度的随机字符、数字
function randomStr(length) {
    return Math.random().toString(36).substr(2, length)
}
```    


## 实现bindActionCreators函数

1. 思路
   1) 对创建action的函数进行包装，生成自动调用dispatch的函数
   2) 该函数需要判断，传入的是方法还是对象的情况


2. 代码示范
```js
export default function bindActionCreators(actionFun, dispatch) {
    if(typeof actionFun === "function") {
        return creators(actionFun, dispatch)
    }
    else if(typeof actionFun === "object") {
        let obj = {};
        for(let prop in actionFun) {
            if(typeof actionFun[prop] !== "function") {
                new Error("错误");
            }
            obj[prop] = creators(actionFun[prop], dispatch)
        }
        return obj
    }
}

// 专门封装函数的函数
function creators(actionFun, dispatch) {
    // 在使用时可能传递一些参数，用于action对象的构建
    return function (...data) {
        dispatch( actionFun(...data) )
    }
}
```



# redux中间键

## 中间键的原理(如何进行自定义中间键)
1. 中间键的作用
    1) 增强了仓库中dispatch函数的功能
    2) 比如之前只能传入action对象，经过中间键的加强，可以传入方法，promise
    3) 重点，中间键并不对仓库造成任何影响，只是在已有的dispatch函数的基础上又套了一层

2. 中间键的原理
   1) 中间键就是一个函数，用来生成一个新的dispatch函数
   2) 然后把新生成的dispatch函数，替换掉仓库中的dispatch函数
   3) 在替换之前，把旧的dispatch函数先进行保存
   4) 新的dispatch函数，会在合适的时候，调用旧的dispatch函数，全靠旧的dispatch函数修改仓库中的数据
   5) 中间键可以连续使用(一层一层的增强dispatch的功能)
      1) 调用规则是，从后面开始包裹，最后一个调用仓库中原始的dispatch函数，倒数第二个调用倒数第一新生成的dispatch函数，以此类推。
      2) 这种方式被称为洋葱模式，一层一层的处理数据。


3. 示范代码，数据库创建完成后，进行dispatch的替换
```js
import store from "./store";

// 中间键1
function dispatch1(action) {
    dispatch2(action);
}

// 中间键2
function dispatch2(action) {
    dispatch(action)
}

// 进行保存
const dispatch = store.dispatch;
// 进行替换
store.dispatch = dispatch1;
```



## applyMiddleware函数

1. 功能
   该函数的作用，给某个仓库加入中间键

2. 使用
   1) 通过该函数就可以创建出一个注入中间键的仓库，并且返回一个函数(绑定仓库)
   2) 调用返回的绑定仓库的函数，会返回创建仓库的函数
   3) **中间键函数也有固定的写法**
   4) 使用方式
   ```js
   // 该函数传入中间键，然后会返回一个绑定仓库的函数
   let f1 = applyMiddleware(中间键函数)
   // 使用返回的绑定仓库的函数，传入仓库的创建函数，会返回具体的仓库创建函数
   const createSto = (reducer, default) => createStore(reducer, default);
   let sto = f1(createSto);
   // 创建出具体的使用中间键的仓库
   const store = sto(reducer, default);
   ```



3. 中间键函数的执行规则
   1) 具体的新的dispatch函数，外面被包裹了两层函数
   2) 执行规则
      1) 首先按照传入的顺序，执行最外层的函数，得到创建dispatch的函数
      2) 然后**倒着执行**创建dispatch函数的函数，创建出具体的dispatch函数
      3) 倒着执行的原因，是把dispatch函数往前传递
      4) 最后第一个中间键函数的dispatch可以调用第二个中间键函数的dispatch函数
      5) 这样在具体调用dispatch函数时，就会从左向右(第一个开始)，依次调用各个中间键的dispatch函数，比较符合开发逻辑


4. 代码示范
```js
import { createStore, applyMiddleware } from "redux" 

// dispatch接收的是一个对象，内部存放了仓库中的getState函数，和最终的dispatch函数
function logger1(dispatch) {
    // next接收的是下一个中间键返回的dispatch函数
    return function(next) {
        // 对应的dispatch
        return function (action) {
            console.log(1)
            next(action)
        }
    }
}
// dispatch接收的是一个对象，内部存放了仓库中的getState函数，和最终的dispatch函数
function logger2(dispatch) {
    // next接收的是下一个中间键返回的dispatch函数
    // 最后一个接收的就是仓库中原来的dispatch(初始的dispatch)
    return function(next) {
        // 对应的dispatch
        return function (action) {
            console.log(2)
            next(action)
        }
    }
}

// 创建仓库的函数
const storeFun = (reducer) => createStore(reducer);
// 创建仓库
const store = applyMiddleware(logger1, logger2)(storeFun)(reducer);
// 经过applyMiddleware函数的包装，logger1中的next就是logger2创建的dispatch。
// 由于logger2是最后传入的中间键，所以next为最原始的dispatch函数
```


5. 在创建仓库的函数中使用applyMiddleware函数注入中间键
   1) 在createStore函数的第二个参数或者第三个参数是可以传入applyMiddleware函数绑定中间键的
   2) 比如: `const store = createStore(reducer, applyMiddleware(logger1, logger2));`
   3) 原理
      1) 底层还是使用的`applyMiddleware(logger1, logger2)(storeFun)(reducer)`这种方式
      2) 在调用函数时传入的applyMiddleware，是执行情况，相当于创建了绑定数据库的函数
      3) 在createStore中的处理，直接是(storeFun)(reducer)这种方式，阻断了默认的创建方式
      4) 其实(reducer)，最终调用的还是数据库函数，创建数据库，由于没有传入第三个参数，此时进不去(storeFun)(reducer)，走默认创建仓库的方式
      然后把创建的仓库对象的dispatch属性进行替换


1. 功能
   1) 该函数接收的参数不固定，但是每个参数都必须为中间键函数
   2) 会倒着执行传入的中间键函数的返回函数(第一级返回函数)
      以便于把后面中间键函数创建的dispatch函数(第二级返回函数)，可以传入前一个中间键中




## 实现applyMiddleware函数

1. 思路
   1) 共返回两次函数
   2) 倒着执行中间键创建dispatch的函数
   3) 中间键函数，是在创建仓库时进行解析的，前两次的函数不进行解析
   4) 先执行创建仓库函数，在解析中间键函数
   5) 中间键的第一层函数执行时，传递的dispatch是最终的dispatch

2. 代码示范
```js
export default function applyMiddleware(...apply) {
    // 不知道接收多少个中间键函数，所以使用点点点的方式

    return function (createStore) {
        // 返回绑定数据库的函数，接收数据库函数

        return function(reducer, defaultDate) {
            // 返回创建数据库的函数，可以接收reducer，默认值
            
            // 创建数据库
            let store = createStore(reducer, defaultDate)

            // 解析中间键函数
            // 中间键第一层函数传入的数据(getstate，dispatch)
            const _store = {
                getState: store.getState,
                // 对dispatch重写，底层调用最新的dispatch函数。
                dispatch: (action) => {
                    store.dispatch(action)
                }
            }
            // 解析中间键最外层的函数，得到创建dispatch函数的函数
            const applyArr = apply.map( (ele) => {
                return ele(_store);
            } )
            // 保存dispatch
            let dispatch = null;
            // 倒着执行创建dispatch的函数
            for(let i = applyArr.length - 1; i >= 0; i --) {
                if(i === applyArr.length - 1) {
                    // 得到dispatch函数
                    dispatch = applyArr[i](store.dispatch)
                }else {
                    // 执行函数，传入dispatch，形成了闭包
                    dispatch = applyArr[i](dispatch)
                }
            }
            /*  高级写法
                let dispatch = applyArr.reduce( (a, b) => {
                    return (dispatch) => {
                       return a(b(dispatch))
                    }
                } );
                dispatch = dispatch(store.dispatch);
            */
            
            // 更改仓库中的dispatch函数
            store = {
                ...store,
                // 进行dispatch的替换，不要直接修改store.dispatch
                dispatch
            }
            return store
        }
    }
}
```



3. createStore中对应的代码
```js
export default function createStore(reducer, mystate, z) {
    // 对reducer函数进行保存，仓库中有一个方法，可以替换reducer函数
    const reduc = reducer;
    // 保存数据(仓库)
    let state = null;
    
    // 处理中间键
    if(typeof mystate === "function") {
        z = mystate;
        mystate = null;
    }
    if(typeof z === "function") {
        state = z(createStore)(reducer, mystate)
        return state;   // 直接截止
    }

    // 创建仓库的代码省略
}
```



## 官方提供的中间键库

### redux-thunk中间键

1. 作用
   该中间键创建的dispatch函数，不光可以传递普通的action对象，还能传递函数

2. 使用
   1) 需要通过npm下载，然后引用，下载方式`npm i redux-thunk -D`
   2) 该中间键会自动生成对应的dispatch函数，正常调用store.dispatch()，传入对应的数据即可
   3) 传递参数的类型
      1) 传递普通的action对象，不做任何操作，内部直接调用后面中间键的dispatch函数或者原始的dispatch函数进行数据的操作，即继续向下走。
      2) 传递函数，该函数接收一个参数，参数为store.dispatch函数(最终的dispatch函数)
         1) 当使用dispatch传递函数时，函数会在dispatch内部被调用
         2) 如果想要修改仓库数据，需要使用函数接收的dispatch函数，然后传入对应的action，会从头走一遍(因为传入的dispatch为最终的dispatch函数，不是初始的dispatch函数)
         3) 当再次走到该中间键定义的dispatch函数时，一看传入的是action对象，继续向下走
         4) 如果函数没有使用接收的dispatch，则状态不会进行变化(无法向后走，执行初始的修改状态的dispatch函数)


3. 代码示范
```js
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
// 使用redux-thunk中间键(创建对应的dispatch函数)
const store = createStore(reducer, applyMiddleware(thunk));  

function a(dispatch) {
    // 可以进行任意的处理
    console.log(123);

    // 合适的时间修改仓库状态
    let action = {
        type: "a",
        payload: ""
    }
    dispatch( action );  
}
// dispatch中传入函数
store.dispatch(a);
```



### 实现redux-thunk中间键

1. 原理
   1) 创建的dispatch函数，可以传入函数
   2) 传入的函数，可以接收到参数，为最终的dispatch函数
   3) 如果传入对象，直接后面的dispatch函数


2. 代码示范
```js
export default function thunk({dispatch}) {
    return function (next) {
        return function (action) {
            if(typeof action === "object") {
                next(action);
            }
            if(typeof action === "function") {
                action(dispatch);
            }
        } 
    }
}
```



### redux-promise中间键

1. 作用
   该中间键创建的dispatch函数，除了可以传递action对象，还可以传递Promise对象，还可以传递action对象但是payload属性是Promise对象

2. 使用
   1) 需要通过npm下载，然后引用，下载方式`npm i redux-promise -D`
   2) 该中间键会自动生成对应的dispatch函数，正常调用store.dispatch()，传入对应的数据即可
   3) 传递参数的类型
      1) 可以传递action对象，不做任何处理，直接运行后面的dispatch
      2) 可以传递Promise对象，当Promise对象的成功状态被激活时，自动调用then中的成功函数。
         1) 底层，后面的dispatch自动定义在then的成功回调函数中，所以只要触发成功状态，传入普通的action对象
         2) 就可以从头开始，解析到该中间键提供的dispatch函数后，一看是普通的action对象，自动向后运行
         3) 如果不触发成功状态，则后面的dispatch函数就不会执行，不会修改仓库中的状态
         4) 只能触发一次状态
      3) 可以传递action对象但是payload属性是Promise对象
         1) 当Promise对象的成功状态被激活时，自动调用then中的成功函数。
         2) 底层，后面的dispatch自动定义在then的成功回调函数中，所以只要触发成功状态，传入的值作为payload的属性值。
         3) **把当前的action进行传入，这种用法的promise，type是已经固定下来的，不能进行更改，并且成功状态的传入数据并不是新的action对象，只会替换payload属性**
         4) 就可以从头开始，解析到该中间键提供的dispatch函数后，一看是普通的action对象，自动向后运行
         5) 如果不触发成功状态，则后面的dispatch函数就不会执行，不会修改仓库中的状态
         6) 只能触发一次状态


3. 代码示范
```js
import { createStore, applyMiddleware } from "redux";
import promise from "redux-promise";


// 创建仓库的函数
const store = createStore(reducer, "ahdfoia", applyMiddleware(promise));

store.dispatch( new Promise( (rew, j) => {
        rew({
            type: "a"
        })
    } )
)
store.dispatch( {
    type: "d",
    payload: new Promise( (rew, j) => {
        rew("fadf")
    } )
} )
```


### 实现redux-promise中间键
  
1. 原理
   1) 接收到普通的action对象，直接向后执行
   2) 接收到promise对象，绑定上then函数，then函数内部使用dispatch函数
   3) payload是Promise对象，成功状态调用传入的值为payload的新值，通过dispatch函数进行发送
   4) 对象Promise对象进行判断


2. 代码示范
```js
export default function promise({dispatch}) {
    return function (next) {
        return function (action) {
            if(typeof action === "object") {
                if(action instanceof Promise) {
                    action.then( (action) => {
                        dispatch(action)
                    } )
                }else if(action.payload instanceof Promise) {
                    action.payload.then( (payload) => {
                        dispatch({
                            ...action,
                            payload
                        })
                    } )
                }else {
                    next(action)
                }
            }
        }
    }
}
```



### redux-saga中间键

#### saga使用
1. 作用
   1) 纯净、不会污染action。
      1) 像thunk、promise中间键，dispatch在调用时，可以传入其他类型的变量，不用传入action对象。这样做，在一定程度上造成了action和dispatch的污染。
      2) saga中间键，在使用dispatch时，传入的还是普通的action对象，不能传入其他的类型的数据，相对来说，保持了action和dispatch的纯净。
      3) **saga中间键，并不影响dispatch传入的action，reducer会正常处理**
         简单来说saga就是在dispatch发送途中装了一个监听器，当监听到监听的指令发送了，不会影响当前的发送，然后saga去处理自己的事情。
   2) 强大
      1) saga在开始时，就会启动一个任务，相当于对dispatch传入的action进行监控。当传入的action满足一定的条件，通过调用next，再次触发生成器阻断后的代码，进行一定功能的处理
      2) 定义action条件、以及阻塞生成器的就是saga指令，saga中间键提供的很多的指令，不同的指令对应不同的功能，所以功能非常强大。
   3) 灵活
      saga中间键，可以定义多个生成器，每个生成器可以使用saga指令，完成对应的功能，这样就可以对生成器函数进行封装，另起js文件。使用起来比较灵活方便。


2. 使用
   1) 下载对应的插件 `npm i redux-saga -D`
   2) saga的引入，然后使用引入函数创建一个saga中间键
   3) 定义一个saga任务(就是一个普通的生成器函数，但是内部有些要求)
   4) 当仓库创建完成后，立即启动saga任务。通过`run`函数开启saga任务
   5) saga可以定义多个生成器函数，但是需要进行生成器的合并。
      生成器的合并，需要在saga任务对应的生成器函数中，使用`all`指令，进行合并。
   

3. saga任务(生成器)的执行规则
   1) 生成器内部代码的解析是受到next控制的，所以saga中间键可以完全的控制saga任务的执行
   2) 当使用run开启saga任务后，saga中间键会立即调用一次next，开始saga任务的解析
      1) 也就是saga任务是从一开始就已经启动了的
      2) 由于生成器中存在yield语法，所以每一次next的执行，
      3) 只会执行一段代码，执行完yield后面的表达式，就会终止执行
      4) 当再次调用next后，才会继续向下执行，直到下一个yield或者函数结尾
      5) 所以saga中间键可以通过next函数的执行时间，控制整个saga任务
   3) saga中间键，触发next函数的规则
      1) 在一开始，调用一次next函数，当遇到yield，解析完yield后面的代码，返回一个数据对象，就会终止执行。
      2) 此时saga中间键就会解析next返回的数据对象，判断内部的value属性的属性值
      3) 如果该属性值不是saga指令对象(自定义代码)，继续触发next向下执行，再次进行判断。
      4) 如果该属性是saga指令对象，saga中间键不会立刻调用next函数，于是生成器被阻断
      5) 会在合适的时候，saga中间键再次触发next，继续判断。合适的时候通过指令定义
      6) 如果saga固定没有写在yield的后面，是无法起到阻断效果的(判断是否继续触发next，依据的是next执行的返回值，只有yield处才能创建返回数据，写在别处是无法得到指令对象的)
   4) 如果没有遇到指令阻塞，会把yield后面的表达式的值，作为下一次next的参数传入到生成器中。阻断位置定义的变量就可以接收到，比如const a = yield * ，a会接收到next传入的参数，继续向下执行。起到数据传递的工作，没啥用。



4. 代码示范
```js
import createSagaMiddleware from "redux-saga";

// 创建一个saga中间键
const sagaMid = createSagaMiddleware();
// 创建saga任务(要写成生成器函数的形式，便于saga中间键控制内部代码的执行)
function* sagaTask() {
    // run触发该任务，立即解析，不会阻塞直至结尾
    console.log(1);
    // 2，作为下一次next的参数传入(中间键手动传入的，并不会自动传入)，a就会得到2。
    cosnt a = yield 2;
    console.log(a);
}

// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);
```


5. **重点**
   1) 当生成器中的函数，通过next执行完。saga就会失去作用，如果想要实现一直工作，可以继续while循环，如果想要借助while循环，yield必须起到阻塞作用，即必须存在saga指令
   2) 由于saga指令众多，**通常一个生成器函数中只使用一次指令**，并且位于while下。
      此时就需要进行生成器的合并。
      它们都是位于阻断的情况下，哪一个指令的触发条件满足，哪一个就会继续向下解析，处理完对应的功能，在while的作用下，又回到了阻塞状态。
   3) while虽然是一个死循环，只要内部的yield处于阻断的状态，该循环就可以跳出。即并不影响其它代码的执行。



#### saga中常用的指令

saga中间键，提供了非常多的指令，每个指令其实就是一个函数。这些指令只能在yield处才能起作用。

所有的saga指令，都在redux-saga/effects该库中进行定义抛出，所以要想使用saga指令，需要从该库中进行引入。

**saga指令，相当于给yield设置一个阻塞条件，只要达到阻塞的条件，该yield就会放行**
**可能多个生成器都处于阻塞状态，那个满足阻塞的条件，那个就放行**


##### all指令

1. 作用
   1) 该指令可以合并多个生成器函数，作用于saga，
   

2. 使用
   1) 该指令，传值参数为一个数组，数组的每一项为一个生成器对象。
   2) 该指令会造成yield的阻塞，放行条件为只有传入的生成器函数全部解析完成，才会放行
   3) 如果传入的生成器中有一个死循环，则永远不会放行


3. 示范代码
```js
import createSagaMiddleware from "redux-saga";
import { all } from "redux-saga/effects"

// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

function* a() {
    yield 1;
    console.log("a");
}
function* b() {
    yield 1;
    console.log("b");
}

// 创建saga任务(要写成生成器函数的形式，便于saga中间键控制内部代码的执行)
function* sagaTask() {
    // 传递的是生成器对象，不是生成器构建函数，所以需要执行
    yield all([a(), b()]);
    console.log(1);
}

// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));

// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);
```


##### take指令
    
1. 作用
   1) 该指令，可以监听一个action对象中的type属性。


2. 使用
   1) 传入与需要监听action对象的type相同的数据，进行监听
   2) 放行条件，只要使用dispatch传入的action等于阻塞位置设置的属性，就会放行。可以放行多个
   3) 放行时间，仓库状态修改完成


3. 示范代码
```js
import createSagaMiddleware from "redux-saga";
import { all, take } from "redux-saga/effects"
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


// 生成器函数
function* a() {
    // 监听a
    yield take("a");
    console.log("a");
}
function* b() {
    while(true) {
        // 监听a
        yield take("a")
        console.log("b")
    }
}
function* c() {
    while(true) {
        // 监听b
        yield take("b")
        console.log("c")
    }
}

// 创建saga任务
function* sagaTask() {
    yield all([a(), b(), c()]);
    // 由于生成器存在死循环，所以该yield永远不会放行
    console.log(1);
}

store.dispatch( {type: "a"} )   // 会打印 a b
store.dispatch( { type: "b" } ) // 会打印c
store.dispatch( {type: "a"} )   // 会打印b
```



##### takeEvery指令

1. 作用
   监听一个action对象

2. 使用
   1) 该指令不会造成yield的阻塞，所以不能使用while进行包裹
   2) 由于该指令不会造成阻塞，所以没有放行条件
   3) 该指令第一个参数传入监听的action的type值，第二个参数传入一个函数
      1) 当调用dispatch传入的action对象符合监听的action对象，就会执行该指令传入的函数。
      2) **该指令传入的函数必须是一个生成器创建函数**。
   4) 由于监听效果一直存在，虽然不会造成阻塞，看生成器代码执行完成，但是当前生成器永远不会运行结束，会影响到all指令的放行(永远不会放行)。
   5) 由于该指令不会造成阻塞，所以**一个生成器中可以使用多次该指令**，监听不同的action对象
   6) 如果调用的生成器函数中也存在saga指令阻塞，会照常阻塞
      但是并不影响监听函数的运行，即当下一次满足条件时，依旧会调用传入的生成器函数，此时还是照常阻塞。
      当阻塞条件满足，每个阻塞会正常放行，继续向后执行。
   7) 如果第一个参数传入`"*"`，则发送任意的action都会被监听到，执行传入的生成器函数


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { all, takeEvery, take } from "redux-saga/effects"
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


function* a() {
    // 可以多次使用，甚至监听相同的属性
    yield takeEvery("a", aa1)
    yield takeEvery("a", aa2)
    yield takeEvery("b", bb) 
}
function* aa1() {
    console.log("aa1");
    // 照常阻塞，但是不影响aa1的下次执行
    yield take("b");
    console.log("放行了");
}
function* aa2() {
    console.log("aa2");
}
function* bb() {
    console.log("bb");
}
// 创建saga任务
function* sagaTask() {
    yield all([a()]);
    console.log(1);
}


store.dispatch( {type: "a"} )
store.dispatch( {type: "a"} )
store.dispatch( { type: "b" } )  // 即会运行bb，也会放行两次aa1的阻塞
```


##### delay指令

1. 作用
   1) 在生成器函数中，实现某段代码的延时执行。
   2) 虽然使用setTimeout也可以实现延时的效果，但是saga中是无法监听控制setTimeout中的代码的，3) 如果想要实现延时执行的代码中也有saga的阻塞与放行的效果，借助setTimeout是无法实现的。

2. 使用
   1) 该指令会造成阻塞，接收一个参数，就是延时的时间
   2) 放行条件，延时的时间一过，saga调用next()放行，继续向下执行，相当于实现了代码延时执行的效果。
   3) 由于delay指令是正常的阻塞与放行，所以下方的yield依旧会起作用，受saga的控制


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { all, delay, take } from "redux-saga/effects"
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


function* a() {
    yield take("a");
    // a放行后，在阻塞300ms，然后在执行下方的代码，相当于setTimeout
    yield delay(300);
    console.log("a");
    // 延时300ms后，解析到该代码，正常阻塞
    yield take("b");
    console.log("b");
}
// 创建saga任务
function* sagaTask() {
    yield all([a()]);
    console.log(1);
}

store.dispatch( {type: "a"} )
```


##### put指令

1. 作用
   重新触发dispatch，传入action，修改仓库状态。
   比如: 某个dispatch传入的action，放行了saga中的某个阻塞。可能会进行一些副作用的处理，处理完成可能需要重新修改仓库状态，就需要借助该指令


2. 使用
   1) 该函数不会造成阻塞，相当于dispatch函数，传入一个action对象，重新修改状态
   2) 使用put发送action对象，saga照样可以监听到，该放行的放行。
   3) 在while中take和put指令联合使用，即使设置相同的type，也不会造成死循环。
      put发送的时候，take还没有阻塞，代码还没有执行到哪，当put发送完，代码解析到take，正常阻塞，由于此时put已经发送完成，所以阻塞不会放行，死循环形成不了。


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { all, put, take } from "redux-saga/effects"
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


function* a() {
    yield take("a");
    // put就相当于dispatch函数
    yield put({type: "b"});
    console.log("a");
}
// 创建saga任务
function* sagaTask() {
    yield all([a()]);
    console.log(1);
}

store.dispatch( {type: "a"} )
```


##### call指令

1. 作用
   搞定生成器函数中的异步操作，但是只能监听promise对象。

2. 使用
   1) 可能会造成阻塞
   2) 传参的个数不定，第一个参数为**函数引用(必传)，可以只传递这一个参数**，第二个参数~最后一个参数为传入函数执行时传入的数据。
      1) 如果传入的是普通的函数，返回值作为next的参数传入，由于函数立即执行完成，所以并不会造成阻塞
      2) 如果传入的函数返回值是一个promise对象，会造成阻塞。只有promise触发成功或者失败状态，阻塞才会放行。
      3) 触发成功状态，传入的数据，会作为放行next执行时的参数传入。
      4) 触发失败状态，会在当前的yield处抛出错误，代码终止。
         如果使用try-catch进行监听，catch中接收的错误信息就是触发失败状态时的传参
   3) 如果想要给call，传入的函数绑定this。共有两种方式
      1) 一种为第一个参数变成数组格式，数组的第一项为函数执行时的this指向，第二项为函数引用
      2) 一种为第一个参数变为对象格式，对象的context属性设置函数执行时的this指向，fn设置函数的引用
   

3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { call, take } from "redux-saga/effects";
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


// 定义一个函数，返回promise
function promise(a, b, c) {
    console.log(a, b, c, this);
    return new Promise( (dis, dsj) => {
        setTimeout(() => {
            // 触发成功状态
            dis("123")   
            // 触发失败状态
            // dsj("789")   
        }, 1000)
    } )
}
// 创建saga任务
function* sagaTask() {
    yield take("a");
    // 为了方式触发失败状态
    try {
        // 不写call，直接写函数调用，效果一样
        const a = yield call({
            context: "a",
            fn: promise
        }, 4, 5, 6)
        // const a = yield call(["a", promise], 4, 5, 6)
        // 通常只穿一个函数引用
        // const a = yield call(promise)
        console.log(a);   // 接收到的a为，成功状态的传参，"123"
        
        // 通常与put配合使用，获取到数据，保存到数据库中
    } catch (e) {
        console.log(e)    // 接收到的e为，失败状态的传参，"789"
    }
}

store.dispatch( {type: "a"} )
```


4. 扩展
   1) 如果yield 后面跟的就是一个promise对象，即next执行完，接收到的对象参数的value属性值为promise对象，会自动进行阻塞
   2) 只有promise触发成功状态，或者失败状态，才会放行。
   3) 和call或者apply指令，实现的功能相同，可以不用借助这两个指令也能实现功能
   4) 为了保持相同性，或者进行代码检测时更加容易，通常加上call或者apply



##### apply指令

1. 作用
   和call的功能相同，管理promise对象，只有传递参数的不同。


2. 使用
   1) 使用方式，第一个参数为函数的this执行，第二个参数为函数引用，第三个参数为函数执行时传入的数据(格式为对象)
   2) 其它的使用方式相同，也有可能进行阻塞


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { apply, take } from "redux-saga/effects"
// 创建一个saga中间键
const sagaMid = createSagaMiddleware();

const mystate = {};
function reducer(state = mystate, action) {
    switch (action.type) {
        case "a":
            return {a: 789}
        case "b":
            return {a: 123}
        default:
            return state
    }
}
// 创建仓库的函数
var store = createStore(reducer, "ahdfoia", applyMiddleware(sagaMid, logger2));
// 启动一个创建好的saga任务(必须在仓库创建完成后启动saga任务)
sagaMid.run(sagaTask);


// 定义一个函数，返回promise对象
function promise(a, b, c) {
    console.log(a, b, c, this);
    return new Promise( (dis, dsj) => {
        setTimeout(() => {
            // 触发成功状态
            dis("123")   
            // 触发失败状态
            // dsj("789")   
        }, 1000)
    } )
}
// 创建saga任务
function* sagaTask() {
    yield take("a");
    // 为了方式触发失败状态
    try {
        const a = yield apply("a", promise, [4, 5, 6])
        // 通常只穿一个函数引用
        // const a = yield call(promise)
        console.log(a);   // 接收到的a为，成功状态的传参，"123"

        // 通常与put配合使用，获取到数据，保存到数据库中
    } catch (e) {
        console.log(e)    // 接收到的e为，失败状态的传参，"789"
    }
}

store.dispatch( {type: "a"} )
```


##### select指令

1. 作用
   获取数据库

2. 使用
   1) 只有一次参数可以传入，格式为一个函数。函数有一个参数可以接收，为获取到的数据库。返回值作为next执行时的参数传入。通常用于仓库数据的筛选
   2) 不传递参数，next执行时的参数传入就是整个仓库中的数据

3. 代码示范
```js
// 创建saga任务
function* sagaTask() {
    yield take("a");
    // 获取仓库数据，此时已经是a规则，修改完的仓库数据
    const state = yield select()
    console.log(state);
}
```


##### cps指令

1. 作用
   用于处理回调问题

2. 使用
   1) 传入的参数不固定，第一个为函数引用，后面的参数为函数引用执行时的传参
      1) 并且该指令会向函数引用中自动传入一个回调函数。回调函数saga已经写好，自动传入，不用引入
      2) 回调函数传入的位置，与传入的参数合并，放在最后一个，所以函数引用在最后要多定义一个参数用于接收回调函数。

   2) 当传入的函数中，调用回调函数时，回调函数可以传入两个参数，第一参数为错误信息，第二个参数为实际数据。
      1) 当错误信息为null时，yield放行，next传入的参数为回调函数的第二个参数(数据)
      2) 当错误信息存在时，yield的位置就会报错，代码终止。
         可以使用try-catch处理错误，catch接收到的错误信息，就是回调函数传入的第一个参数

   3) 会造成阻塞，放行条件为，传入函数中，调用回调函数。


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { cps, take } from "redux-saga/effects"

// 定义一个函数
function func(a, b, callBack) {
    console.log(a, b);
    setTimeout( () => {
        // 假设数据获取成功，调用回调，进行数据的处理
        callBack(null, "ahofd");
        // 数据获取出现问题
        // callBack("asjdfoasjpdf");
    }, 1000)
    return "adfas"
}

// 创建saga任务
function* sagaTask() {
    yield take("a");
    try {
        const a = yield cps(func, 1, 2)
        console.log(a);  // 接收到的参数为，回调函数的第二个参数，前提是第一个参数为null
    } catch (e) {
        console.log(e);  // 接收到的是回调函数的第一个参数(错误信息)，前提是第一个参数有值
    }
}

store.dispatch( {type: "a"} )
```



##### fork指令

1. 功能
   在启动一条新的任务线，在开发中不常用，在saga的底层，该方法使用比较频繁

2. 使用
   1) 不会造成阻塞，传入一个新的生成器函数，然后执行该生成器函数
   2) 该指令的返回值，可以取消本次新开的任务线，需要cancel指令的配合
      1) 新开的任务线，正在阻塞。当取消后，相当于在yield后直接调用了return
      2) 这样当阻塞放行的时候，一执行return，则生成器终止，后面的代码不在运行，任务线结束
   3) takeEvery指令的底层，就是利用该指令完成的

3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { take, fork, delay } from "redux-saga/effects";

// takeEvery的底层代码
function* func() {
    while(true) {
        yield take("a")
        // 监听到了a，其实是新开了一个任务线，处理对应的事情。
        // 如果不是新开一条任务线，造成阻塞，下一次a在触发，是无法监测的
        yield fork(function* () {
            yield delay(2000);
            console.log("a");
        } )
    }
}

// 创建saga任务
function* sagaTask() {
    yield fork(func);
    // 虽然func中存在阻塞，但是那是属于另一个任务线，并不影响当前任务线
    console.log(1);
}


store.dispatch( {type: "a"} )   
```


##### cancel指令

1. 功能
   取消一条任务线，生成器执行完，就会自动取消。但是如果任务线被阻塞，可以通过该指令取消。
   只能取消阻塞状态的任务线，并不会生成器正在解析，忽然间进行了停止。

2. 使用方式
   1) 传值，取消指定的任务线，传的值必须是fork指令或者takeEvery指令返回的值
   2) 不传值，取消当前任务线
   3) 父任务线取消，并不会影响子任务线
   4) 如果想要取消多个任务，可以传递多个参数

3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { take, fork, delay, cancel } from "redux-saga/effects"

function* func() {
    let a = null;
    while(true) {
        yield take("a");
        // 每次触发a，下面都将新开一个任务，然后进行等待
        // 如果在a触发后，取消上次新开的任务，则等待时间到达后只会打印一次a
        // 执行的最后一个任务，前面的两次任务都被取消了
        if(a) {
            yield cancel(a)
            // 如果不传值，会立马取消当前任务线，相当于该生成器函数执行完成，下面的fork将不在执行
            // yield cancel()
        }
        a = yield fork(function* () {
            yield delay(2000);
            console.log("a");
        } )
    }
}

// 创建saga任务
function* sagaTask() {
    yield fork(func);
}


store.dispatch( {type: "a"} )
store.dispatch( {type: "a"} )
store.dispatch( {type: "a"} )
```



##### takeLatest指令

1. 作用
   1) 该指令会自动取消之前开启的任务线，和takeEvery指令的作用一样，只不过takeEvery指令不会取消之前的任务。
   2) 简单介绍takeEvery指令
      1) takeEvery指令，内部其实是新开了一条任务线监听某个属性。当监听到符合属性，会立即在开启一条任务线进行处理(传入的生成器函数)。
      2) 所以takeEvery指令返回的是监听属性的那条任务线，使用cancel取消任务线，取消的是监听任务的任务线，即下次无法在进行监听，但是内部新开的任务线是无法取消的。
      3) 比如，如果想要监听a，当action来临的时候，如果type符合，就进行某项处理。
         1) 如果action在一段时间内多次触发(瞬间请求了多次)，只需要处理最后一次即可，
         2) 此时就需要取消前面，监听到的属性新开的任务。
         3) 如果使用takeEvery指令和cancel指令是无法做到的(获取不到任务)，使用fork指令和cancel指令可以办到，可能写起来稍微麻烦点。
   3) 简单介绍takeLatest指令
      1) 该指令也是新开一条任务线，监听某个属性，当属性监听到的时候，也会立即开启一条任务线进行处理(传入的生成器函数)
      2) 当下次监听到属性时，会自动取消上一次开启的任务线，取消的不是监听任务线


2. 使用
   1) 使用方式和takeEvery指令一样，第一个参数传入监听的属性，第二个参数传入生成器函数


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { delay, takeLatest } from "redux-saga/effects"

function* func() {
    // 在takeLatest指令的作用下，a只会打印一次，不是两次。之前的任务会自动取消
    yield delay(2000);
    console.log("a");
}
// 创建saga任务
function* sagaTask() {
    yield takeLatest("a", func);
}


store.dispatch( {type: "a"} )
store.dispatch( {type: "a"} )
```


##### cancelled指令

1. 作用
   判断当前任务线有没有被取消，可以处理一些任务线取消的后续操作。
   要想判断任务取消，只能在阻塞的yield后面进行判断，前面写任务肯定没有取消，由于任务线取消，yield后面的代码将不会再解析，所以就需要try-finally的配合。finally里面的代码是无论如何都会执行的。


2. 使用
   直接使用，不需要传参，如果返回true，说明当前任务已经被取消


3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { cancelled, delay, takeLatest } from "redux-saga/effects";

function* func() {
    // 在takeLatest指令的作用下，该任务会自动取消
    try {
        yield delay(2000);
        console.log("a");
    } finally {
        // 无论上方的yield阻塞是取消，还是正常工作，下方的代码都会执行
        // 所以可以用来判断，当前任务有没有被取消
        if( yield cancelled() ) {
            console.log("任务取消了");
        }
    }
}
// 创建saga任务
function* sagaTask() {
    yield takeLatest("a", func);
}


store.dispatch( {type: "a"} )
store.dispatch( {type: "a"} )
```


##### race指令

1. 功能
   1) 该指令和all指令的功能相同，传入多个生成器函数，开启多个任务，并且立即进入内部解析。
   2) 唯一不同之处在于，all指令，是当传入的所有生成器都解析完毕才会放行。
   3) 该指令，只要有一个生成器函数解析完成，就会立刻放行，并且其他生成器会立刻终止(任务销毁)
   4) 相当于竞赛指令，看谁跑的快


2. 使用
   1) 会造成阻塞，放行条件，只要有一个生成器函数解析完成，就会立刻放行
   2) 传参有两种形式，一种是传入数组格式，一种是传入对象格式的数据
   3) 该指令放行时，即next调用时，传入的是第一个执行完成的生成器函数的**return的返回值**，并且传值类型，与race指令的传值类型有关



3. 代码示范
```js
import createSagaMiddleware from "redux-saga";
import { cancelled, delay, race} from "redux-saga/effects"


function* a() {
    // 该任务被销毁，b先执行完
    try {
        yield delay(2000);
        console.log("a");
    } finally {
        if( yield cancelled() ) {
            console.log("a任务取消了");
        }
    }
}
function* b() {
    try {
        yield delay(1000);
        console.log("b");
        return "fadsf"
    } finally {
        if( yield cancelled() ) {
            console.log("b任务取消了");
        }
    }
}

function* c() {
    // 该方法先触发成功状态，阻塞放行
    const c = yield new Promise( (r, j) => {
        r("abc")
    }) 
    // c得到的是abc
    return c;
}
function* d() {
    const d = yield new Promise( (r, j) => {
        setTimeout( () => {
            r("ddd")
        }, 100)
    } ) 
    return d;
}


// 创建saga任务
function* sagaTask() {
    const ab = yield race([a(), b()])
    const as = yield race({
        c: c(),
        d: d()
    })
    // 打印的是{c: "abc"}   [undefined, "fadsf"]
    // 对象格式，next传递对象，数组格式，next传入数组
    console.log(as, ab)
}
```



# react-actions插件

1. 功能
   该插件，简化了action对象的创建，reducer函数的书写。
   该插件提供了六种方法。

## createAction

1. 作用
   调用该函数，返回action对象的创建函数。通过调用返回的函数，可以创建出对应的action对象。


2. 使用方式
   1) 传递一个参数，调用该方法返回的函数，创建一个action对象，传入的参数会作为type的属性值
   2) 传递两个参数，第二个参数是函数格式，传递一个数据，返回一个数据。
      调用该方法返回的函数。如果传参，会运行传入的函数(第二个参数)。
      返回的数据会作为action对象的payload属性的属性值。
   3) 传递三个参数，第三个参数会赋给创建的action对象的第三个属性(meta)，作为元数据(相当于请求头)。
      传参格式为一个函数，把要设置的对应数据通过返回值返回。
      **注意**: 不能向payload一样，实现动态传入，通过调用返回函数进行传参，这两个函数接收的都是传入的第一个产生，不太好实现分开处理，所以该函数返回一个固定的数据。


3. 代码示范
```js
import { createAction } from "redux-actions";

const aAction1 = createAction("a");
// 打印{ type: "a" }
console.log( aAction1() );

const aAction2 = createAction("b", b => b);
// 打印{ type: "b", payload: 2}
console.log( aAction2(2) );

// meta属性要返回具体的数据，必须要写成函数格式
const aAction3 = createAction("c", c => c, () => ({c: 123}));
// 打印{ type: "c", payload: 2, meta: {c: 123}}
console.log( aAction3(2) );
```


## createActions

1. 作用
   通过该函数可以创建多个action对象的创建函数

2. 使用
   1) 该方法传入一个对象，对象的属性名，作为action对象的type属性，属性值作为payload属性，格式也是函数。如果没有payload属性，直接写null。
   2) 该方法返回的也是一个对象，对象的属性值，为传入对象的属性值转换成小驼峰式的写法(单词分界线为_符号)。每一个对象属性对应一个action的创建函数


3. 代码示范
```js
import { createActions } from "redux-actions";

const action = createActions({
    "A": null,
    "A_B": null,
    "A_C": a => a
})
// 属性值变为a, aB, aC
console.log(action.a());  // 打印{type: "A"}
console.log(action.aB()); // 打印{type: "A-B"}
console.log(action.aC(2)); // 打印{type: "A-B", payload: 2}
```



## handleAction

1. 作用
   创建一个属性对应的reducer处理函数。

2. 使用
   1) 该方法接收三个参数
      1) 监听的属性，两种赋值方式
         1) 直接赋值，对应的type值
         2) 附上创建action对象的函数引用
            1) 注意: 此处生成的创建action对象的函数必须是借助当前组件的方法创建出来的
            2) 原因: 该组件创建生成action对象的函数的时候，会自动在函数上添加一个方法(toString)。通过该方法，可以获取到生成action对象的type值。
            3) 原理: 此处附上函数引用，在内部会自动调用该函数的toString方法，取出对应的type值。
      2) 监听到属性的处理函数，接收两个参数(state, action)
      3) 仓库的默认值
   2) 返回一个方法，即真正的reducer函数


3. 代码示范
```js
import { handleAction, createAction } from "redux-actions";
const a = createAction("A");

// "A"也可以替换成a，会自动调用a.toString()，取出对应的type值
const reducer = handleAction("A", (state, action) => {
    return {
        ...state,
        a: 456
    }
}, {a: 123})
/**
 * 相当于下面的写法
 * function reducer(state = {a: 123}, action) {
 *     switch(action.type) {
 *         case "A":
 *             return { ...state,  a: 456  }
 *         default:
 *             return state
 *     }
 * }
 */
```



## handleActions

1. 作用
   创建多个属性对应的reducer处理函数。

2. 使用
   1) 该方法接收两个参数
      1) 第一个参数: 对象格式
         1) 属性名，监听的属性，两种赋值方式
            1) 直接书写对应的action对象的type值
            2) 填写上对应的创建action对象的函数(该组件生成的)引用，会自动调用toString方法取出对应的type属性值。
               1) 注意: 由于是对象的属性值，所以要填写函数引用，需要用[]进行包裹，如果不进行包裹，就是一个正常的属性。
         2) 属性值，监听到的处理方法，接收两个参数(state, action)
      2) 仓库的默认值
   2) 返回一个方法，即真正的reducer函数


3. 代码示范
```js
import { handleActions, createAction, createActions } from "redux-actions";

const a = createAction("A");
const action = createActions({
    "B": null,
    "C": c => c
})


const reducer = handleActions({
    [a]: (state, action) => ({...state, a: 789}),
    B: (state, action) => ({...state, b: 456}),
    [action.c]: (state, action) => ({...state, c: action.payload})
}, {a: 123})
/**
 * 相当于下面的写法
 * function reducer(state = {a: 123}, action) {
 *     switch(action.type) {
 *         case "A":
 *             return { ...state,  a: 789  }
 *         case "B":
 *             return { ...state,  b: 456  }
 *         case "C":
 *             return { ...state,  c: action.payload  }
 *         default:
 *             return state
 *     }
 * }
 */
```


## combineActions


1. 作用
   简化handleActions的书写，多个监听属性共用同一个处理函数。

2. 使用
   1) 传参个数不定，每一个参数对应一个action对象的type属性
   2) 该方法返回一个对象，对象中有一个toString方法。

3. 代码示范
```js
import { createStore } from "redux";
import { handleActions, createAction, combineActions } from "redux-actions";

const a = createAction("A");

// 多个属性("D", "E")共用一个action处理函数
const ss = combineActions("D", "E");
// 可以传递创建action对象的函数，会自动调用toString函数，获取到实际的type属性值
// const ss = combineActions(d, e);

const reducer = handleActions({
    [a]: (state, action) => ({...state, a: 789}),
    B: (state, action) => ({...state, b: 456}),
    [action.c]: (state, action) => ({...state, c: action.payload}),
    // 监听到"D"或者"E"，触发的都是该函数
    [ss]: (state, action) => ({...state, d: action.payload})
    /**
     * 相当于下列写法，由于写法相同，所以进行了提取。
     * D: (state, action) => ({...state, d: action.payload}),
     * E: (state, action) => ({...state, d: action.payload})
     */
}, {a: 123})
const store = createStore(reducer);


store.dispatch(a())
store.dispatch({type: "D", payload: "DDD"})
```



# react-redux插件

1. 作用
   该插件的功能是，实现React与redux进行连接。
   不使用该库也能实现连接，但是代码繁琐。

2. 使用
   1) 该库提供了一个组件(Provider)和一个高级组件(connect)。它们的作用是:
      1) 组件: 创建执行期上下文，通过行间传参的方式，传入具体的仓库对象store，保存到上下文中
      2) 高阶组件。在外界定义好获取的仓库数据，和修改仓库状态的dispatch发送。然后通过行间传参的方式，传入被包装的组件中进行使用。并且加上了仓库状态改变，组件重新渲染的功能。


3. 代码示范
```js
import {Provider, connect} from "react-redux";
import store from "./store";

// 获取仓库中的那些数据
function mapStateToProps(state) {
    // 会自动传入整个仓库状态
    return {
        // 该对象会进行解构，然后把每一个属性传入被包装的组件
        a: state.a,
        b: state.b
    }
}

// 修改仓库状态
function mapDispatchToProps(dispatch) {
    // 会自动传入仓库对应的dispatch函数
    return {
        // 该对象会进行解构，然后把每一个属性传入被包装的组件
        toA: () => {
            dispatch({
                type: "A"
            })
        },
        toB: () => {
            dispatch({
                type: "B"
            })
        },
    }
}

// 组件包装
const A = connect(mapStateToProps, mapDispatchToProps)(A);


function App() {
    // 创建执行期上下文保存仓库
    return <Provider store={ store }>
        {/* 使用被包装的组件 */}
        <A />
    </Provider>
}
function A(props) {
    return <>
        <div onClick={ props.toA }> { props.a } </div>
    </>
}
```


4. 注意点
   1) mapDispatchToProps还有另外两种处理方式
      1) 格式是一个对象，对象中是一个一个的方法。和使用bindActionCreators进行创建action对象的函数，进行功能增强。
      2) 或者mapDispatchToProps写成对象格式，每个属性值就是action的创建函数。 connect组件会自动添加dispatch发送，和bindActionCreators函数的功能一样。




# connected-react-router插件

1. 作用
   建立路由与redux之间的关系
   redux中存放路由中的数据，其实存放的是`history对象的action属性，以及location对象`。其它的数据没有存放的必要。

2. 使用
   该组件抛出了很多方法，重用的方法如下(该插件要想起作用，前三个是必要的):
   1) `connectRouter`方法，用于管理仓库中的路由数据的状态。
      1) 调用该方法，传入history对象(初始值)。会返回路由数据在仓库中的管理函数reducer。
      2) 由于返回的是一个单独专门处理路由的reducer函数，所以需要借助combineReducers进行合并。
      3) 由于该方法需要传递一个history对象，所以需要借助history插件进行生成，不能通过路由获取。
   2) `routerMiddleware`方法，调用该方法返回一个中间键。
      1) 该方法在使用时，需要传入一个history对象，并且与connectRouter中传入的是同一个。
      2) 返回的中间键的作用为: 
         1) 监听一些特殊的action。实现对路由的操作
         2) 比如type为push，表示路径需要跳转，reducer中是不能进行跳转的。所以需要使用中间键进行拦截，拦截成功，则路由进行跳转。
         3) 由于该插件管理仓库中路由数据的reducer是自动生成的，所以内部使用了那些type是不知道的，所以不能通过手写的方式监听。

   3) `ConnectedRouter`组件，该组件将代替BrowterRouter或者HashRouter组件，创建路由上下文。
      1) 该组件行间传入`history="history对象"`，即connectRouter中传入的history对象(共用的)。
      2) 替换的原因:  
         1) 使用react-router-dom中提供的BrowserRouter组件，该组件会自动创建一个history对象，当使用NavLink或者Link组件进行路由的跳转，当前组件的history对象中的location对象会发生变化，进行更新。
         2) 但是由于组件创建的history对象是新的，与仓库中的history对象没有关系，所以仓库中的数据是无法发生变化的。
         3) connectedRouter组件，创建的执行期上下文，使用的history对象是行间传入的。如果传递的与connectRouter中传递的是同一个history对象，则路径再次发生变化时，history的location更新，由于仓库中使用的也是该location，所以仓库中的数据也会进行更新
      3) **该组件必须位于react-redux插件提供的Provider组件下面，否则不起作用**
    
   4) `push`方法，作用产生一个跳转路由的action对象(无法通过手写，只能使用插件提供的)。
      1) 当使用dispatch发送该方法产生的action对象时，中间键就可以检测到，进行路由的操作。
      2) 通常使用该方法，加上dispatch发送的方式，进行js跳转路由。
         1) 使用history中的push也能使用路由的跳转，但是不太好。
         2) 既然仓库中有路由的数据，与路由存在对应关系。所以在js进行跳转路由的操作，只需要修改仓库中的状态(发送action对象，表示要跳转路由)，仓库中的状态变化，实际路由也进行变化(这样才能实现仓库与路由的对应关系)。
      3) 如果使用了ConnectedRouter组件，则该组件之外的跳转失效。


3. 代码示范
```js
import React from "react";
import { Link, Route, Switch} from "react-router-dom";
import { createStore, applyMiddleware, combineReducers } from "redux";
import { Provider } from "react-redux"
import { createBrowserHistory } from "history";
import { connectRouter, routerMiddleware, ConnectedRouter, push } from "connected-react-router";

// 创建history对象
const history = createBrowserHistory();
// 创建路由的reducer方法
const routerReducer = connectRouter(history);
// 创建路由的中间键函数
const routerMiddlewareFun = routerMiddleware(history);


// 定义两个reducer方法
function reducerA(state={a: "A"}, action) {
    return state
}
function reducerB(state={b: "B"}, action) {
    return state
}
// 进行reducer的合并
const reducer = combineReducers({
    reducerA,
    reducerB,
    router: routerReducer
})
// 创建仓库
const store = createStore(reducer, applyMiddleware(routerMiddlewareFun));
console.log(store.getState());
store.subscribe( () => {
    console.log(store.getState());
} )


// 路由相关的react代码
export default function App () {
    // 此处不进行跳转，仓库状态也没有发生变化
    store.dispatch( push("/c") )
    return (
        <Provider store={store}>
            {/* 共用一个history对象 */}
            <ConnectedRouter history={history}>
                <div>
                    <Link to="/">首页</Link>
                    <Link to="/a">菜单</Link>
                    <div onClick={ () => {
                        // 进行动态跳转
                        store.dispatch( push("/a") )
                    }}>fasdfa</div>
                </div>
                <div>
                    <Switch>
                        <Route path="/" exact component={A} />
                        <Route path="/a" component={B} />
                    </Switch>
                </div>
            </ConnectedRouter>
        </Provider>
    )
}
function A() {
    return <div>123</div>
}
function B() {
    return <div>456</div>
}
```


      


# dva框架

1. 作用
   该框架是阿里巴巴提出的一种框架，内部整合和React开发依赖的一些插件，比如react，router，redux，saga。提出了一种固定的开发模式，简化了React的开发。

2. 使用
   1) 插件下载: `npm i dva -D`
   2) 该插件默认导出一个函数，通过调用该函数，可以得到一个dva应用程序对象。通过调用返回对象中的一些方法，进行dva的开发
   3) 在调用dva插件抛出的函数时，是可以传递参数的，参数格式为一个对象。通过该对象可以对dva进行一些配置


## dva在浏览器上显示React元素

1. 原生react显示React元素
   使用`ReactDOM.render(<App />, document.getElementById("app"))`
   其中，App为根组件，document.getElementById选中根组件将要插入的真实dom节点。

2. dva显示React元素
   1) 第一步: 调用`dva对象.router`，传入一个函数，函数返回一个React节点(根节点)。
   2) 第二步: 调用`dva对象.start`，传入节点选择器，比如"#app、.app、div ··· ···"。
      该方法为dva的启动程序。
   3) 原理
      1) start方法的底层借助的函数ReactDOM.render。第一个参数调用router中传入的方法，第二个参数使用document.querySelect(该方法传入的选择器)选出具体的dom进行插入。

3. 代码示范
```js
import React from "react";
import createDva from "dva";
// 引入根节点
import App from "./App";


// 创建dva对象
const dva = createDva();
// 第一步
dva.router( () => {
    return <App />
} )
// 第二步启动dav
dva.start("#app");

// 相当于把ReactDOM.render换了个写法
```


## dva中创建仓库

1. 前言
   1) dva中创建仓库，通常被称为创建一个模块(仓库中的某个模块)
   2) 一个仓库中可以有很多模块，dva底层借助的是combineReducers方法，合并各个模块
   3) 仓库中可以存放路由数据(action属性，以及location对象)，dva底层直接进行了创建。
      1) 底层借助的是react-router-redux插件。connected-react-router插件是该插件的升级版本。
         由于dva更新的不是很快，所以底层借助的各个插件依赖或许不是最新版本的。
      2) 直接进行了connectRouter(创建router的reducer函数)和routerMiddleware(创建router中间键)的处理。与仓库有关的操作，底层直接实现，减少开发者代码量。
      3) 由于connectRouter和routerMiddleware函数都需要传入一个history对象。由于该对象有三种模式，dva不知道开发者使用的是哪一个，所以需要传入，如果没有传入则`默认使用createHashHistory方法进行创建`。
      4) history的传入方式: **在dva的配置对象中`history: ???`**进行传入。


2. 借助`dva对象.model`方法，完成仓库模块的定义。该方法传入一个对象，配置仓库中的一些属性(比如reducer函数、默认值 ··· ···)，传入的对象一共有5个属性。
   1) `namespace`(必传): 命名空间，属性值是一个字符串，会作为仓库中的一个属性名。
   2) `state`(必传): 定义模块的默认值
   3) `reducer`(必传): 定义该仓库处理action对象的函数，属性值为对象
      1) 每个对象属性就相当于一个reducer方法
         底层借助的是redux-actions插件的handleActions方法
      2) 属性名为监听的type值
         1) 注意，为了防止不同模块的命名冲突，实际监听的type值为"**命名空间/属性名**"，所以dispatch需要触发该reducer处理函数，需要加上命名空间。
         2) 如果该模块的内部定义的子方法，使用dispatch函数，触发状态，不用加上命名空间。
         3) 如果触发其他模块定义的reducer方法，也需要加上对应模块的命名空间。
      3) 属性值为每一个监听type的处理方法。
   4) `effects`: 定义一些副作用操作的代码，属性值为一个对象。
      1) 该方法的底层是启动saga中间键进行处理
      2) 每一个属性名，相当于监听一个type属性。
         1) 外界触发定义的方法，传入的action也需要加上命名空间
      3) 属性值，为监听到对应的type，进行处理的方法
         1) 由于底层使用的是saga，所以需要把函数写成生成器的格式。
         2) 并且方法接收两个参数，一个是action对象，一个是saga对象(单独封装的一个对象，内部有些常用的saga指令，并不是原生的saga对象，该对象剔除了一些不常用的saga指令)。
      4) 底层相当于`yield takeEvery("命名空间/属性名", 属性值)`，监听属性
   5) `subscriptions`: 赋值格式为一个对象。
      1) 对象的每一个属性对应一个方法，属性名随意。
      2) 每个属性对应的方法，会在模块插入到仓库的时候进行运行，即只运行一次。
      3) 由于定义的方法只会运行一次，所以经常使用该方法进行订阅一些与仓库有关的事件
      4) 定义的方法在运行的时候，会接收一个数据，格式为对象。有两个属性，一个是dispatch方法，一个是history对象(路由数据)

3. 该方法必须在start之前使用，创建模块

4. 代码示范
```js
import React from "react";
import createDva from "dva";
import { createBrowserHistory } from "history"
// 引入根节点
import App from "./App";

const dva = createDva({
    // 传入history对象，不让dva使用默认的history对象
    history: createBrowserHistory()
});
dva.router( () => {
    return <App />
} )

// 创建a模块
dva.model({
    namespace: "a",
    state: 0,
    reducers: {
        // 不怕命名冲突
        ADD: (state, action) => state + 1
    }
})
// 创建b模块
dva.model({
    namespace: "b",
    state: 0,
    reducers: {
        // 不怕命名冲突
        ADD: (state, action) => state + 1
    }
})

// 启动dva
dva.start("#app");
```


## dva获取仓库数据
   
1. 前言
   由于dva的仓库是自动创建的，所以无法获取到仓库对象，也就无法使用dispatch函数和getState函数。

2. 方式
   借助dva抛出的高级插件`connect`，对要操作仓库的组件进行包装。
   1) 该方法的使用方式完全与react-redux插件中的connect方法相同，但是不是同一个方法。

3. 代码示范
```js
import React from "react";
import { connect } from "dva";

function App(props) {
    // 获取到两个模块中的数据
    console.log(props.a, props.b)
    return <div onClick={ () => {
        // a模块加一
        props.add()
    } }>App组件(根组件)</div>
}


function mapStateToProps(state) {
    return {
        a: state.a,
        b: state.b
    }
}
function mapDispatchToProps(dispatch) {
   return {
       add() {
           dispatch({
               // 要加上模块名
               type: "a/ADD"
           })
       }
   }
}

export default connect(mapStateToProps, mapDispatchToProps)(App)
```



## dva操作路由

### 正常操作
1. 前言
   1) dva中进行路由的开发，其实使用的就是react-router-dom组件进行开发。
   2) 只不过dva对该插件的接口抛出，进行了转发。通过dva也能引入对应的组件
   3) 不需要从react-router-dom组件中引入，给人一种完全依靠dva进行开发的感觉

2. 代码示范
```js
import React from "react";
import { BrowserRouter, Route, Link, Switch } from "dva/router"

export default function App(props) {
    return <BrowserRouter>
        <div>
            <Link to="/">根路径</Link>
            <Link to="/a">a路径</Link>
            <Switch>
                <Route path="/" exact component={Home}></Route>
                <Route path="/a" component={A}></Route>
            </Switch>
        </div>
    </BrowserRouter>
}
function Home() {
    return <div>根路径</div>
}
function A() {
    return <div>a路径</div>
}
```



### 与仓库状态进行关联

1. 前言
   1) 仓库在创建的时候，仓库中关于router数据的操作已经完成。
   2) 路由与仓库状态进行关联，需要借助ConnectedRouter组件，创建上下文，不再使用BrowserRouter或者HashRouter进行创建。
   3) 使用的dispatch进行传入，保持一致性。

2. ConnectedRouter组件的引入
   1) 该组件，也是从"dva/router中引入"。
   2) 但是并不是直接引入，而是该组件在一个对象中(`routerRedux`)。dva/router把对象进行了抛出，所以引入对象，然后在从对象中使用该组件

3. 共享history的注意点
   1) 共享history一共有两种方式
      1) 一种是在外部进行创建，dva配置中传入的history对象引入创建的history对象
      2) 一种是dva进行的优化。
         1) dva对象.router方法中，传入一个方法，用来声明dva渲染的根组件。
         2) 传入的方法中是有参数的，格式为一个对象，对象中有两个属性
            1) 一个是dva的配置对象
            2) 一个就是dva配置对象中传入的history对象
         3) router中传入一个方法，创建根组件，而路由上下文的建立通常都是在根组件中完成。
            根组件也是一个方法，这样就可以直接引入根组件函数，然后router进行传入该函数。
            这样根组件函数，直接接收到history数据，进行创建路由上下文的操作。
         4) 常用该方法，并且根组件文件的命名通常名为为routerConfig.js。表示dva开发的根组件。   

4. 代码示范
```js
import React from "react";
import { connect } from "dva";
import { Route, Link, Switch, routerRedux } from "dva/router"

function RouterConfig(props) {
    // 直接使用dva传入的history对象。
    return <routerRedux.ConnectedRouter history={props.history}>
        <div>
            <Link to="/">根路径</Link>
            <Link to="/a">a路径</Link>
            <Switch>
                <Route path="/" exact component={Home}></Route>
                <Route path="/a" component={A}></Route>
            </Switch>
        </div>
    </routerRedux.ConnectedRouter>
}
function Home(props) {
    console.log(props.router)
    return <div>根路径</div>
}
function A(props) {
    console.log(props.router)
    return <div>a路径</div>
}
function mapStateToProps(state) {
    return {
        router: state.routing
    }
}
Home = connect(mapStateToProps, null)(Home)
A = connect(mapStateToProps, null)(A)

export default RouterConfig


/** router方法的修改 */
// 引入根节点
import RouterConfig from "./RouterConfig";
dva.router( RouterConfig )
```



## dva的配置对象

1. dva配置对象中的属性
   1) `history`: 用于仓库中保存路由状态
   2) `initialState`: 仓库的默认状态(需要注意，属性名必须与模块名相同)
      可以看成给模块赋初始值，没什么用处，定义模块的时候，初始值已经附上了
   3) `onError`: 属性值为函数，当仓库运行中出现了错误，执行定义的函数
   4) `onAction`: 用于扩展中间键的功能
      1) 仓库是dva内部创建的，内部并没有启用过多的中间键函数，如果想要启动更多的的中间键函数，需要使用该属性进行传入。
      2) 属性值为函数(中间键函数)或者对象(对象的每个属性为中间键函数)，或者数组(数组的每一项为中间键函数)
      3) 当使用dispatch发送action对象时，就会执行定义的函数[中间键的功能(重写dispatch)]。
      4) 传入自定义的中间键函数
         1) 定义函数的格式，与定义中间键完全相同(就是一个中间键函数)
         2) 格式为`(store) => (next) => (dispatch, action) => { next(dispatch, action) }`。
   5) `onStateChange`: 属性值为函数
      1) 当仓库状态发生变化时，执行定义的函数，函数接收一个参数(仓库状态state)
   6) `onReducer`: 属性值为一个函数，用于扩展reducer的功能
      1) 对reducer函数进一步进行封装，传入原始的reducer函数，返回一个新的reducer函数。
         当使用dispatch发送action对象时，触发新的reducer函数，由于新的reducer函数并没有对状态的处理，所以还是需要借助旧的reducer函数
      2) 新的reducer函数中调用旧的reducer函数，并且把旧的reducer函数返回值(仓库的新状态)返回，修改仓库的状态。
      3) 相当于在reducer函数的外面又包了一层。
   7) `onEffect`: 属性值为一个生成器函数，对模型的effect(saga调用的函数)进行进一步封装。
      1) 函数接收四个参数， 第一个参数为旧的effect函数(模块中定义的)
      2) 要想实现功能，和onReducer一样，也必须调用旧的effect函数。
         由于旧的effect函数是生成器函数，所以使用yield ···进行调用。
   8) `extraReducers`: 属性值为一个对象
      1) 对象的每个属性值，都是一个reducer函数。
      2) 最终会把该对象中定义的每一个reducer函数，通过combineReducers函数进行合并
      3) 相当于在仓库中创建其它的模块
   9) `extraEnhancers`: 属性值为一个数组
      1) 数组的每一项为一个函数，用于增强仓库创建函数createStore的功能。
      2) 每个函数就接收一个参数，原始的createStore函数。
      3) 在内部调用传入的createStore函数，创建仓库，然后返回值为创建的仓库
      4) 数组中定义的函数，会顺着执行，只需要最后一个调用createStore创建仓库，并且返回即可
      5) 该数组解析时，会倒着解析，然后合并成一个方法，最终替换createStore函数。
         当创建仓库时，执行createStore方法，此时执行的就是替换过的方法，然后顺着执行数组中定义的方法，和中间键一个原理。



## dva插件

1. 前言
   1) dva插件其实就是一个对象(dva配置对象)
   2) 通过dva对象.use(插件对象)启动一个dva插件
   3) 通过use启动插件对象，最终会与dva的配置对象进行合并，形成最终的配置对象。

### dva-loading插件

1. 功能
   该插件，会在仓库中单独创建一个模块，并且有三个属性，用来表示模块中是否有副作用正在执行(比如ajax请求)

2. 使用
   1) 该插件默认导出一个方法，通过该方法，可以创建出一个dva插件对象，然后通过use启动插件对象
   2) 调用插件导出的方法，可以传递参数，格式为对象。对象中只有一个`namespace`属性可以设置，用来设置仓库中该模块对应的属性名，默认为loading


3. 分析该插件在仓库中创建的模块中的属性
   1) global: 当属性值为false时，表示所有的模块没有进行副作用的处理
      当某个模块中发生了副作用处理，该属性会从false变成true。处理完成在变成false
   2) effects: 属性值为一个对象，对象中的属性表示那个action的type触发了副作用处理，
      处理时，属性值为true，处理完成为false
   3) models: 属性值为一个对象，对象中的属性表示那个模块中发生了副作用处理。
      处理时，属性值为true，处理完成变为false


4. 作用
   用来处理一些ajax请求时，请求的过程中显示为正在请求，或者有个小圈圈在转。请求完成小圈圈消失。


5. 原理
   1) 模块的副作用处理都是在saga中间键中完成的。
   2) dva插件，最终会与配置对象进行合并，然后替换配置对象。
   3) 该dva插件，其实是利用`extraReducers`属性创建该插件对应的模块(reducer函数)
   4) 然后约定两个type类型，修改模块中三个属性的状态
   5) 然后利用`onEffect`属性，对原有的处理副作用的saga进行包装。
      1) 原有的处理副作用的saga，在触发前，先使用put触发约定好的模块状态变成true的type。表示将要触发副作用
      2) 然后触发原有的saga，有阻塞正常阻塞。
      3) 原有的saga触发完成，在使用put，触发约定好的模块状态变成false的type。表示副作用触发完成


6. 代码示范
```js
import createLoading from "dva-loading";
import createDva from "dva";
const dva = createDva();
dva.use( createLoading() )
```



# umijs脚手架

## 准备工作
1. 安装umi
   1) 可以全局安装或者局部安装
      1) 全局安装npm i umi -g
      2) 局部安装npm i umi -D
2. 启用工程
   1) umi dev  表示在开发环境中启用


## umi约定式路由的开发

1. umi约定
   1) umi为了便于开发，进行了一些约定，使用这些约定，可以减少路由的代码开发
   2) 约定
      1) 在项目文件夹下，建立一个src文件夹，内部建立一个pages文件夹
         1) 可以不用建src，直接建立pages文件夹也能使用，为了便于开发，建立src文件
         2) pages文件夹下创建的就是页面组件。
            1) 当时候umi会自动解析该文件夹，创建出一个js文件，为路由Route组件的配置文件
            2) umi根据解析的配置文件，创建对应的Route组件，然后直接插入到页面中进行匹配，不用自己插入。
      2) 根路径对应的展示页面，直接在pages文件夹下创建index.js文件
      3) 文件名(除index外)会直接作为该页面对应的路由地址
      4) 二级或者多级路由对应的页面，把父级路由作为文件夹，把最后一级路由作为文件名
         1) 比如: /a/b/c地址对应的页面组件创建，在pages文件夹下建立a文件夹，然后在a文件夹下建立b文件夹，然后在b文件夹下建立c.js，抛出对应的组件。
      5) 多级路由对应的跟路径展示的文件，也是在对应文件夹下创建index.js文件。
         1) 比如: /a下有子路由/a/b、/a/c。如果想要定义/a对应的展示页面组件，方式有两种
            1) 方法一: 可以通过在pages文件夹建立a.js，该方法不推荐使用。
               1) 原因: /a下有子路由，所以pages下有一个a文件夹，在写一个a.js文件不合适
            2) 方法二: 在a文件夹下建立index.js。表示a对应的根路径，即/a对应的页面组件
            3) 要注意文件名与文件夹名的冲突问题。
      6) 动态路由的创建，借助$符。
         1) 比如/a/:id。创建文件的方式为在pages下建立a文件夹，然后建立$id.js。
         2) 比如/a/:a/:b。创建文件的方式为在pages下建立a文件夹，然后建立$a文件夹，然后建立$b.js文件  
      7) 路由切换，一些组件一直显示(导航栏，页脚栏)
         1) 在src文件夹下(不是pages文件夹)，新建layouts文件夹
            1) 在layout文件夹下建立index.js文件，只能建该文件。
            2) 该文件抛出的是一级路由对应的共享组件(一级路由导航栏，会一直存在)
            3) **一级路由对应的Route组件会作为children进行传入，可以进行动态插入**
         2) 子路由共享组件，二级或者多级导航栏
            1) 在对应的二级路由或者多级路由的父级文件夹下创建layout.js文件。
            2) 会被该文件夹下所有的路由，子路由共享。
            3) 比如: /a/b、/a/c、/a/b/c共享一个组件。就可以在a文件夹下创建一个layout.js文件。
            4) **同样，对应的Route组件会作为children文件进行传入**。
         3) 由于共享组件中，Route会作为children进行传入，所以可以进行动态插入
            1) 比如开头作为导航栏，下面作为页脚。中间就可以动态插入Route组件，进行动态匹配，实现页眉页脚的开发。
         4) 原理
            1) 对应的Route组件，会作为children，传入共享组件中，利用的就是子路由的配置。
            2) 比如a文件夹下有一个layout.js文件，表示共享。
               1) 创建的路由信息为{ path: "/a" component=require("./a/layout").default exact="false" route=[{/a/b}{/a/b/c}] }
               2) umi在解析路由信息时，route会正常解析创建出对应的Route组件，然后传入component组件中。
               3) 当进行路径匹配时，由于/a是不完全匹配，所以共享组件就可以一直显示。
               4) 当使用children时，传入的Route组件就开始匹配
      8) pages文件夹下，直接创建一个404.js。
         1) 在开发模式下访问不存在的路径，使用的还是默认的404页面，便于调试
         2) 如果打包部署上线后，再访问一个不存在的路径，就会显示自定义的404页面   
         3) 可以直接访问/404，查看页面样式 
   3) 修改路由配置文件
      1) 路由配置文件，除了根据pages文件，创建出一些默认的配置属性外，还可以自定义属性(保存一些数据)   
      2) 自定义属性的方式
         1) 在pages文件夹下具体的页面文件中的开始部分(代码前面)，加上/** */。
            1) 内部写的代码会放入配置文件该页面对应的路由数据对象中。
            2) 比如: /** a: 123  b: 456 */ 
               1) 规范: 每一条数据独占一行，:后面加上一个空格，否则没用
            3) 定义数组，不能通过[ ]进行定义，:后面敲上换行(相当于[ ])
               1) 数组的每一项，前面最少有一个空格，空格后面加上-符号，后面再跟上具体的数据
               2) 数组的每一项独占一行。
      3) 配置文件的特殊属性`Routes`
         1) 该属性也是通过/***/进行定义，默认是没有的
         2) 数据格式是一个数组，数组的每一项为一个组件相对于src文件的路径地址
         3) 解析时，该路径对应的组件，就变成了该属性数组中的第一个路径对应的组件
            1) 之前的组件，会作为children传入该属性数组中的最后一个组件中
            2) 最后一个组件，会作为children传入前一个组件中，以此类推
         4) 作用
            1) 该属性可以对一个真正的页面组件进行层层的包装，进行一些功能的验证(是否有权限访问该页面)    
            2) 同意时，插入children，就会显示真正的组件。不同意时不处理对应children，则真正的组件不会加载，表示没有权限访问该页面。
   4) 路由的配置文件中的每一个路由对应的数据对象，会传入到对应的路由组件中，通过props可以获取到配置对象，从中使用自定义的一些数据。  
   5) 所有的自动生成的Route组件，exact默认都设置成了true(完全匹配)，可以通过修改配置文件来进行控制。    



## umi配置式路由的开发
1. 前言
   umijs路由的开发，其实就是配置式路由的开发。即使是约定式路由的开发，底层也是转成了配置式路由，只不过我们感受不到


2. 配置式路由开发的过程及注意点
   1) 在根路径下创建一个`.umirc.js文件或者config文件夹下config.js文件`
      1) 这两个配置文件，不光可以配置路由信息，它可以配置非常多的东西。
   2) 建立的文件，导出一个对象，对象中建立一个routes数组。用来配置路由信息
      1) 数组的每一项为一个对象，表示一个路由信息
      2) 只要该属性一经定义，则约定式路由就会失效，不定义约定式路由依旧工作
   3) 路由信息对象中常用的属性
      1) path: 用来设置匹配路径
      2) component: 用来设置匹配成功时显示的组件
         1) 该属性的赋值方式为一个字符串，对应组件的地址。**并且是相对于pages文件夹的地址**
      3) Routes: 属性值为一个数组，数组的每一项为一个组件的路径(相对于根路径)
         1) 该属性设置后，匹配成功后，显示的组件不在是component对应的组件，而是该数组的第一项路径对应的组件
         2) component对应的组件，会作为children放入数组的最后一项路径对应的组件中
         3) 该数组的后一项路径对应的组件，会作为children放入前一项路径对应的组件中
      4) route: 子路由，数据格式为数组，数组的每一项为路由的配置对象。
         1) 解析创建出的Route会作为children传入使用该属性的对象的component对应的组件中
         2) 使用route属性的路由对象，exact需要设置为false，默认设置的是true
         3) 使用route属性的路由对象的component对应的组件，作为共享组件，用来设置导航栏或者页脚
         4) 约定式路由的layout.js文件的原理，就是使用的该属性
      5) 可以填写自定义属性


3. 代码示范
```js
export default {
    routes: [
        {
            path: "/",
            // 用来配置导航栏
            component: "./link.js",
            // 该属性必须设置，默认是绝对匹配，这样/a，导航栏组件就匹配不成功
            exact: false,
            route: [
                {
                    path: "/",
                    component: "./Home.js",
                    // 自定义属性
                    a: 123,
                },
                {
                    // 动态路由
                    path: "/a/:id",
                    component: "./A.js",
                    Route: ["./src/a.js", "./src/b.js"]
                }
            ]
        }
    ]
}
```




## 路由组件与路由数据的导入

1. 在umi中，Route组件会根据路由配置文件自动创建
2. Link组件: 通过`umi/link`手动导入
3. NavLink组件: 通过`umi/navlink`手动导入
4. 获取路由信息history对象中的方法: 通过`umi/router`手动导入，数据格式为对象。对象中是一些history对象中的常用方法(不是全部方法)，比如push、replace。
5. withRouter: 通过`umi/withRouter`手动导入



## umi中启动dva定义模块(仓库)
1. 前言
   1) umi中，可以手写原生的创建仓库的代码，但是过于麻烦。
   2) dva中，对仓库的创建进行了封装，可以方便简洁的创建出仓库数据
   3) dva要想起作用，必须使用start启动dva，显示页面。但是umi已经把显示的工作给做了，如果在使用start，会导致页面文件放入两次，破坏了结构。所以在umi中不能通过直接引入dva插件的方式使用dva。

2. umi中使用dva的方式
   1) umi提供了一个插件集，内部有非常多的插件依赖，其中就有dva。
      1) 该插件集需要下载: `npm i umi-plugin-react -D`
   2) 要想使用插件集中的某个插件(比如dva)，需要在配置文件中进行插件的启动
      1) 配置文件就是配置式路由使用的配置文件(.umirc.js或者config文件夹下config.js)
      2) 配置文件不光配置路由，可以配置很多东西，开启插件集中的插件就是其中一项功能
   3) 开启的代码
   ```js
    export default {
        plugins: [
            // 第一项为插件的名称，第二项为插件的配置(启动插件集的某个插件功能)
            ["umi-plugin-react", {
                // 把数组中的每一项都生成一个script标签，放入到浏览器显示的HTML文件中
                // 如果是，js代码(写在字符串中)，会创建对应的script标签，把代码放入内部执行
                // 与dva无关，扩展
                scripts: [{src: "a.js"}, {src: "b.js"}, `console.log(123)`],
                // 开启dva
                dva: true
            } ]
        ]
    }
   ```
   4) dva开启后，就可以定义仓库模块了。
      1) 并不是引入一个dva对象，然后使用model方法定义模块，而是使用约定的方式，创建约定名称的js文件，然后抛出一个对象(model传入的对象)
      2) 最终umi在解析的时候，由于dva功能已经开启，底层会自动调用model方法，从约定的文件中引入对象，进行模块的创建
   5) 仓库模块共分为两种，一种是全局模块，一种是局部模块
      1) 全局模块的介绍
         1) 全面模块一开始就创建好模块放入仓库中，所有页面都能使用模块中的数据
         2) 所以，全局模块中通常放一些全局使用的数据
      2) 局部模块的介绍
         1) 局部模块，只有当对应的组件显示时，才会往仓库中加入模块
            1) 当前文件夹中的页面，或者子文件夹中的页面，显示时，加入模块。
         2) 没有对应页面显示时，仓库中是没有对应的模块的。局部模块定义的数据
         3) 只有当前文件夹中的页面，或者子文件夹中的页面可以使用局部模块中定义的数据，其它页面无法使用。
            1) 比如，a文件夹下定义了model，父级文件夹下也有model。
               1) 则a文件夹下的组件可以使用两个模块中定义的数据
               2) 父级文件夹下不在a文件夹下的组件，只能使用父级文件夹定义model，相当于继承。
         4) 所以，局部模块中通常放一些子路由共享的数据
         5) 注意，在开发环境下，局部模块也是会在一开始就加入到仓库中，和全局模块一样，所有模块都可以正常使用，在打包上线后就会恢复正常。
      3) 全局模块的创建
         1) 第一步: 在src文件夹下创建models文件夹
         2) 第二步: 在models文件夹下建立全面模块对应的js文件(可以创建多个文件，对应多个模块)
         3) js文件名，默认作为仓库属性名，可以使用namespace进行设置指定的模块名
      4) 局部模块的创建
         1) 第一步: 在pages对应的文件夹下，创建models文件夹
         2) 第二步: 在models文件夹下建立全面模块对应的js文件(可以创建多个文件，对应多个模块)
         3) js文件名，默认作为仓库属性名，可以使用namespace进行设置指定的模块名
         4) models文件夹可以省略，直接创建model.js文件(但是这样只能创建一个模块)   
   6) 由于局部模块的js文件是放在pages文件夹下，所以约定式路由在解析该文件夹的时候，并不认识该文件，所以正常处理成路由信息对象，生成对应的Route组件，虽然没有太大的影响，但是，会污染路由配置文件。解决办法: **在配置文件中进行配置，禁止约定式路由解析某个文件**，代码如下:
   ```js
    export default {
        plugins: [
            ["umi-plugin-react", {
                dva: true,
                // 排除掉某些文件
                routes: {
                    // 正则表达式，排除掉路径带有models的文件，数组中可以定义多个正则，只要满足一个就忽略
                    exclude: [/.*models.*/]
                }
            }]
        ]   
    }
   ```


3. 使用仓库中的数据
   1) 直接从dva模块中导入`connect`高阶组件，对要获取仓库状态的组件进行包装，使用方法一样
      1) dva插件不需要重新下载，在下载umi的时候已经下载完成，可以直接使用。



## umi引用样式文件

1. 全局样式
   1) 全局样式文件的定义方式
      1) 在src文件夹下，直接创建global.css文件，该文件就是全局样式文件。
   2) 全局样式文件，umi不会交给css-module插件处理
      1) 文件中定义什么选择器的名称，打包后还是什么名称，不会进行修改
      2) 可以直接使用全局样式的选择器，比如: className="a"
   3) 全局样式文件，由于不向局部样式一样，选择器是唯一的，所以**有时需要多个选择器进行限制**


2. 局部样式
   1) 局部样式文件的定义方式:
      1) 直接在pages文件夹下对应组件所在的文件夹下定义css文件，文件名没有要求
         1) 当通过import引入的时候，umi就会把该文件作为局部样式文件处理
      2) 在src文件夹下创建assets文件夹，然后在内部创建css文件夹，在内部创建具体的样式文件
         1) 当通过import引入的时候，umi就会把该文件作为局部样式文件处理
   2) 局部样式经过打包后，也会作用于全局。
   3) umi为了防止局部样式的选择器产生冲突，自动启用了css-module插件
      1) 比如: 
         1) 一个组件中使用了className="a"
            1) 在该组件对应的局部样式文件中直接使用了.a的方式定义样式
            2) 并没有父级选择器的限制。
         2) 其它组件也有className="a"
            1) 在该组件对应的局部样式文件中也是直接使用.a的方式定义样式
            2) 也没有父级选择器的限制。
         3) 由于umi最终会把所有的样式都打包到一个文件中，此时这两个样式就会产生覆盖问题。
      2) css-module插件，在打包样式文件后，会自动给每一个选择器添加随机的字符串，并且是唯一的，这样就避免了选择器冲突的问题。
         1) umi在打包css时，局部样式文件，使用css-module插件进行处理，全局样式文件不进行处理
         2) 由于局部样式文件中定义的选择器最终会被css-module进行修改，并且是随机的。
            1) 造成不能直接使用局部样式文件中定义的选择器，否则样式作用不上的问题
            2) 应该使用css-module修改后的对应的选择器的名称
            3) css-module修改名称，是随机的，不知道会变成什么名称，所以直接书写实现不了
            4) **被css-module修改的css文件，在引入的时候，会抛出一个对象**
               1) 该对象中的属性就是原始的选择器的名称
               2) 属性值就是对应选择器的名称被css-module插件修改后的名称
               3) 这样就可以通过对象属性的方式，给标签赋对应的选择器
         3) **同一个文件下相同的选择器，变成的随机名称也相同，不同文件下不同**
            1) 标签选择器是不会修改的，无法修改。
   4) 使用局部样式的示范代码
   ```js
   /**
    *  a.css样式中的代码
    *  .a{ ··· ··· }
    */
   import css from "./a.css";
   function A() {
       // 由于局部样式文件会经过，css-module插件处理
       // 打包后的css文件，.a就会变成其它名称
       // 直接写className="a"，样式是无法起作用的
       return <div className={css.a}>123</div>
   }
   ```
   5) **局部样式中选择器的使用，通常不使用父级选择器进行约束**
      1) 由于局部样式，会交给css-module插件进行处理
      2) 处理完选择器就是唯一的，直接使用，也不会产生冲突问题
      3) 可以使用父级选择器进行约束
         1) 没有任何影响，只不过样式文件中要多写些代码(约束的父级选择器)

3. less的使用
   1) 在umi中less可以直接使用，不需要进行特殊的配置。
      1) 并且也分全局和局部样式，局部样式依旧自动修改选择器的名称，保持唯一性。
      2) 局部和全局的定义方式和css相同，只需要把后缀名换成less即可。



## umi代理

1. 前言
   在开发过程中，umi工程是放在localhost本地服务器下的。如果在开发过程中，需要请求服务器中的数据进行调试，此时就涉及到了跨域的问题。虽然服务器中可以处理跨域的问题，允许访问，但是这样服务器中的数据就会变得非常不安全，只要是localhost都可以请求数据，此时就需要使用服务器代理。

2. umi代理操作
   1) umi在开发过程中，是工作在localhost服务器下的，umi是可以借助该服务器，使其进行代理进行数据的请求，然后在返回给浏览器，这样就不存在跨域问题。
   2) umi启动localhost的代理功能，代理那个请求，是需要在配置文件中进行配置的。
      1) 底层配置的是webpack
   3) 配置代码
    ```js
    export default {
        proxy: {
            // 接收到的请求地址以/api开头。
            "/api": {
                // 把对应的请求地址，打到指定的服务器下
                // 在localhost服务器中完成，被称为服务器代理
                target: "https://www.baidu.com",
                // 修改源(在localhost下发起的请求，修改成上方指定的服务器)
                // 请求源相同，不存在跨域问题
                changeOrigin: true 
            }
        }
    }
    ```



## umi数据模拟

1. 原理
   1) 当服务器中的代码还没有开发完成，此时前端开发就需要借助模拟数据的方式，模拟出一个假数据进行开发。
   2) 模拟数据的方式有多种
      1) 可以在文件中直接模拟数据，然后读取。
      2) 而umi中有一种更加高级的方式，是采用模拟服务器创建数据的方式。
         1) 前端请求可以写正常的ajax请求(当成服务器已经开发完成)
         2) 由于在开发过程中，是工作在localhost服务器下的，所以请求是直接打到该服务器下
            1) 没有写具体的域名，直接书写地址
         3) umi会在localhost下模拟出一个请求，然后返回对应的数据。
            1) localhost模拟的数据，需要自己创建，有约定式写法
               1) 只需要按照约定式创建文件，当umi工作的时候，该文件中的代码就会放在服务器下作为模拟代码


2. 模拟数据的创建方法
   1) 在根路径下创建mock文件夹，该文件夹下创建的所有js文件
      1) 创建的文件，umi会自动处理，然后把对应的代码放在localhost服务器环境下运行，作为服务器代码。
   2) 模拟文件中的代码示范
    ```js
    export default {
        // 属性名为请求类型和请求地址，请求类型和请求地址之间要有一个空间
        // 属性值为请求返回的数据
        "GET /a/b/c": {
            name: "a",
            arr: [1, 2]
        }
    }
    ```


3. 借助插件(mockjs)快速生成模拟数据
   1) 插件下载: `npm i mockjs -D`
   2) 插件使用(示范代码): 
    ```js
    // 引入组件
    import Mock from "mockjs";
    // 创建模拟数据
    const mock = Mock.mock({
        // 固定的数据
        a: 123,
        // 快速生成数组，arr为属性名，100表示生成的数组中有100项
        // 每一项都是给定的对象格式: 属性名相同，属性值可能随机生成(不同)，如果固定则相同
        "arr|100": [{
            // 固定数据
            a: 1,
            // 生成一个随机的中文名字(100项都是随机的)
            b: "@cname",
            // 生成一个随机的中文地址(100项都是随机的)
            c: "@city",
            // 按照定义的正则，随机生成一个符合的字符串(100项都是随机的)
            d: /demo\d{2}_\d{10}/,
            // 属性值为数字类型，表示生成一个随机数字，取值范围为1000~2000(100项都是随机的)
            "e|1000-2000": 0, 
            // 属性值从给定的数字开始增加，后一项都比前一项增加指定的数据(1)
            "f|+1": 0
            // 随机从数组中选出一位(100项都是随机的)
            "g|1": [1, 2, 3]
        }]
    })
    export default mock
    ```




## umi引入组件时路径的简化
  
umi中定义组件的层级可能会非常深，如果使用相对路径，一级一级的返回，过于麻烦。
umi中提供了一个@符，相当于从src开始查询，如果没有src，则从根路径开始查询。简化了相对路径的书写。




# css样式文件的引入
在js文件中引入
```js
import "路径"
```
在css文件中引入其它的css文件
```css
@import "路径"
```


# axios发送请求以及数据拦截(数据返回先走数据拦截函数进行处理)
要想使用axios，需要进行下载。npm i axios -D
```js
import React from 'react';
import axios from "axios"
// 对数据进行拦截处理，会先进入该方法，然后该方法的返回值在传入then中的方法中
axios.interceptors.response.use( (res) => {
  res.data.b = 222;
  return res
})
// 对请求进行拦截处理(发送前进行拦截，然后处理，把处理好的进行发送)
axios.interceptors.request.use( (config) => {
  console.log(config)
  // 通常操作config.headers，必须返回config
  return config
})

class Abc extends React.Component{
  // 进行数据的请求
  componentDidMount() {
    axios.get("/a.json").then( (res) => {
      console.log(res.data.code);
    })
  }
  render () {
    return (
      <div>123</div>
    )
  }
}
export default Abc;
```




# antDesign库(UI库)的引入与使用

1. 作用
   该库提供了非常多的常用组件，并且这些组件的样式也已经定义完成。


2. create-react-app脚手架搭建的项目使用该库的方式
   1) 下载插件，同时会下载下来对应的css文件(打包好的，底层使用的less开发的)
      1) 下载方式: `npm i antd -D`
      2) 找一个初始时就加载的文件，加载该插件对应的css样式
         1) 引入代码: `import "antd/dist/antd.css"`
      3) 样式文件引入成功后，就可以在组件文件中使用该UI库中的组件
         1) 引入方式和引入自定义的组件的方式相同，代码如下
         2) `import { 具体的组件 } from antd`


3. umi脚手架搭建的项目使用该库的方式
   1) 按照create-react-app搭建的项目使用方式进行使用
      1) 下载插件
      2) 在src文件夹下的global.js文件中引入下载下来的css文件
         1) 引入代码: `import "antd/dist/antd.css"`
         2) global.js会在一开始的时候就执行，然后加载样式文件
      3) 样式文件引入成功后，就可以在组件文件中使用该UI库中的组件
         1) 引入方式和引入自定义的组件的方式相同，代码如下
         2) `import { 具体的组件 } from antd`
   2) 利用配置文件启用该插件(不需要下载antd插件)
      1) 由于该库和umi是同一家公司的，所以umi的插件集(umi-plugin-react)中包含了该插件
         1) 启用后就可以直接使用，不需要导入css文件，自动导入，直接导入对应的插件就可以使用
         2) 但是umi-plugin-react没有下载的话，需要下载umi-plugin-react。
      2) 启用代码如下
    ```js
    export default {
        plugins: [
            ["umi-plugin-react", {
                antd: true
            }]
        ]
    }
    ```
    


# react使用webpack进行打包的操作

下载react需要的插件 `npm install react react-dom -D`;
下载webpack需要的插件 `npm install webpack webpack-cli -D`;

下载打包react需要的插件 `npm install @babel/core @babel/preset-env @babel/preset-react babel-loader @babel/plugin-proposal-class-properties -D`


@babel/plugin-proposal-class-properties。 该插件的作用是对语法进行降级，解决react中使用了新语法(比如 state = {} )，如果不下载该组件，直接打包会进行报错。


注意版本号(不同的版本，使用方式不同):
> babel-loader@8.0.6
> @babel/preset-react@7.7.4
> @babel/core@7.7.5
> @babel/preset-env@7.7.6


1. 建立.babelrc文件(打包react必须的文件，设置babel的一些数据)
内部代码(新旧版本的代码不同)
```js
{ 
    "presets": [
        "@babel/react",
        "@babel/env"
    ],
    // 如果没有下载@babel/plugin-proposal-class-properties该插件，下方代码可以不用写
    "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

2. webpack中的代码
```js
let path = require("path");
let HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    mode: "development",
    entry: "./src/main.js",
    output: {
        filename: "react-bundle.js",
        path: path.resolve(__dirname, "dist")
    },
    module: {
        rules: [
            // 打包react的代码
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader'
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            // 内部需要定义一个标签，用来react的插入
            filename: "index.html",
            template: "./index.html",
            minify: {
                removeComments: true,
                collapseWhitespace: true
            }
        })
    ]
}
```

3. react主入口文件的代码
```js
import React from "react";
import { render } from "react-dom";
// 总组件
import App from "./App";

render(<App />, document.getElementById("app"));    
```