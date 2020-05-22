
# GET请求的ajax函数
```js
function ajax(url) {
    return new Promise((res, rej) => {
        const xhr = new XMLHttpRequest();
        xhr.open("GET", url, true);
        xhr.send();
        xhr.onreadystatechange = () => {
            if( xhr.readyState == 4 && xhr.status == 200) {
                res( xhr.responseText )
            }
        }
    })
}
```


# POST请求的ajax请求
```js
function ajax(url) {
    return new Promise((res, rej) => {
        const xhr = new XMLHttpRequest();
        xhr.open("POST", url, true);
        xhr.send( "789" );
        xhr.onreadystatechange = () => {
            if(xhr.readyState == 4 && xhr.status == 200) {
                res( xhr.responseText )
            }
        }
    })
}
```



# 五层网络模型(由里而外、由下而上)
1. 应用层
2. 运输层
3. 网络层
4. 数据链路层
5. 物理层