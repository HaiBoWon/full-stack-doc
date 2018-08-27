# WebViewJavascriptBridge 方式

该方式主要作用于Objective-C 和 javascript 相互通信，即 oc和 js 方法的互相调用，但是安卓也可以统一使用，在初始的时候需要做些兼容处理。

### 原生处理

```object c
var num = 0;
for (var i = 0; i < 5; i++) {
    num+=i;
}
console.log(num);
```



