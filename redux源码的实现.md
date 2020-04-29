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



