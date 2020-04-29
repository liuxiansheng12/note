

# 仿写核心
1. 提供了一个组件，一个高级组件




# 仿写代码
```js
// react-redux中的部分代码
import React, { useLayoutEffect } from "react";
import { useState, useContext } from "react";

// 创建执行期上下文
const context = React.createContext();

function Provider(props) {
    return <context.Provider value={ {store: props.store} }>
        {props.children}
    </context.Provider>
}


function connect(mapStateToProps, mapDispatchToProps) {
    // 对mapDispatchToProps的处理

    // 判断mapDispatch传入的是函数还是对象
    // 是对象，自动调用combineActionCreators，完成dispatch的封装
    let dis = null;
    if( typeof mapDispatchToProps == "object") {
        dis = (dispatch) => {
            // combineActionCreators的源码的实现
            const o = {};
            for(const prop in mapDispatchToProps) {
                o[prop] = () => {
                    dispatch(mapDispatchToProps[prop]())
                }
            }
            return o;
        };
    }
    else dis = mapDispatchToProps;

    return (Children) => {
        // 返回一个新的组件函数
        return () => {
            // 获取执行期上下文中的数据
            const {store} = useContext(context);
            // 定义状态
            const [t, setT] = useState({});

            // 发布订阅，引发组件的重新渲染，只发布一次
            useLayoutEffect( () => {
                const clearSubscribe = store.subscribe(() => {
                    setT({});
                })
                return () => {
                    clearSubscribe();
                }
            }, [])

            return <Children {...mapStateToProps(store.getState())} 
                { ...dis(store.dispatch) } />
        }
    }
}

export {
    Provider, connect
}
```