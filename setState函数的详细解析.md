

# 使用以及作用
1. 该函数的作用，主要是用来修改状态，并且引发重新渲染的函数
2. 该函数，在使用的时候，需要传入一个对象
   1) 传入的对象，会与原始的state状态对象进行合并，即替换和新增，删除不了
   2) 并不是进行替换
3. 只要该函数一经调用，即使传入了一个空对象
   1) 虽然状态没有改变，但是也会引发组件的重新渲染
4. 如果该函数没有传递数据，是不会触发重新渲染的，与传入一个空对象不同

5. setState的执行，会重新开辟一个对象空间，然后进行属性的合并处理
   1) 并不是在state的基础上进行合并



# 执行方式

1. 该函数的执行方式有两种，一种是同步执行，一种是异步执行
   1) 并且常见的是异步执行

2. 这两种执行的方式，由该函数的触发源所在的位置有关
   1) 如果setState方法执行的触发源头是事件函数，或者周期函数
      1) 则setState对数据的修改是异步的
   2) 如果setState方法执行的源头，是由定时器(setTimeout和setInterval)触发的
      1) 则setState对数据的修改是同步的


# 同步执行和异步执行的特点

1. 异步执行
   1) 由于该函数不是立即执行，所以会进行一定的性能优化，会进行合并处理
   2) 多个`setState`要修改的状态，进行合并，变成一个，然后在统一进行设置
      1) 不是立即执行，一会排一个，一会排一个，到达时间后，统一进行处理
      2) 既然要统一处理，为了节省效率，会合并成一个状态对象，然后在进行修改
      3) 这样，只会造成一次重新渲染
   3) 由于异步执行的问题，所以该函数调用后，状态并没有立即发生变化
      1) 如果想要立即使用修改后的状态，获取的还是之前的状态
      2) **可以借助该函数的第二个参数，进行处理**


2. 同步执行
   1) 由于是同步执行的，所以不存在合并现象，并且每执行一次，都会引发重新渲染的现象
   2) 过多调用，会浪费性能


3. 示范代码
```js
class App extends React.Component {
    state = {
        a: 123
    }
    render() {
        console.log(this.state.a)
        return <>
           <div onClick={this.a}>aaa</div>
        </>
    }
    // 异步执行(三次调用setState，共重新渲染一次)
    a = () => {
        this.setState({})
        this.setState({})
        this.setState({})
    }
    // 同步执行(三次调用setState，共重新渲染三次)
    a = () => {
        setTimeout(() => {
            this.setState({})
            this.setState({})
            this.setState({})
        } )
    }
}
```


4. 异步执行的一种现象(修改完，紧接着使用，出现与预想不同的效果)
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



# setState的第二个参数
1. 该参数，是一个函数，它的执行之间为`render`之后
   1) 此时，对应的状态，就已经修改完成，可以使用

2. 代码示范
```js
class App extends React.Component {
    state = {
        a: 123
    }
    render() {
        console.log(this.state.a)
        return <>
           <div onClick={this.a}>aaa</div>
        </>
    }
    a = () => {
        this.setState({a: 456})
        // 立即使用，还是之前的状态123，异步执行
        console.log(this.state.a);

        this.setState({a: 789}, () => {
            // 此时就是修改完的状态789
            console.log(this.state.a);
        })
    }
}
```


# setState的第一个参数的另一种传法

1. 除了传递对象外，还可以传递函数

2. 解决的问题
   1) 多个setState同时使用，并且是异步执行的
   2) 然后后面的setState需要使用前面setState设置的状态
   3) 如果采用普通的方式，传入对象修改状态，后面的是无法使用前面修改的状态的

3. 原理
   1) 会把setState中传入的函数，放在一个队列中，当到达执行的时间
   2) 会依次执行，第一个函数，接收的参数是初始的状态
      1) 前一个函数的返回值，与传入的状态合并后，形成一个新的状态
      2) 把合成后的状态对象，传入下一个函数中
   3) 最后一个函数的返回值，作为最终将要修改的状态，进行状态的修改，引发重新渲染
   4) 由于每个函数返回值，都需要与状态进行合并，所以，返回值必须是一个对象
      1) 如果返回值不是对象，则状态不参与合并，则下一个函数接收的还是上一个函数传入的状态
      2) 即原封不动的，再向下传递

4. 代码示范
```js
class App extends React.Component {
    state = {
        a: 123
    }
    render() {
        console.log(this.state.a)
        return <>
           <div onClick={this.a}>aaa</div>
        </>
    }
    a = () => {
        this.setState((state) => {
            console.log(state);
            return {
                b: "sss"
            }
        })
        this.setState((state) => {
            console.log(state);
            return 888
        })
        this.setState( (state) => {
            console.log(state);
            return {
                a: 888
            }
        } )
    }
}
```