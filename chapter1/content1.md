# WebView与JS交互
* Android & iOS 调用 JS 的方法
* JS 调用 Android & iOS 的方法

Android & iOS 调用 JS 的方法，伪代码如下：
* Android

```java
webView.loadUrl("javascript:show('xxx');");

```

* iOS

```objc
NSString *result = [self.webView stringByEvaluatingJavaScriptFromString:@"showReturn('xxx');"];
```

webview 调用 JS 的方法比较简单，show('xxx') 方法是 JS 中定义的一个方法：



```javascript
<script language=javascript>
   function show(str) {
      alert(str);
   }
    
   function showReturn(str) {
      return "result";
   }
</script>
```





### WebViewJavascriptBridge 方式 android & ios

引言:
webview 与 JS 交互分为两种：

&emsp;&emsp;该方式主要作用于Objective-C 和 javascript 相互通信，即 oc和 js 方法的互相调用，这是[marcuswestin](https://github.com/marcuswestin)公司开源的一个用于 iOS/OSX 平台 webview 与 JS 通信的方案，它在 webview 和 JS 之间“架了”一座桥梁，提供了非常便捷的通信方式。  

##### 原生处理

```objc
self.bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
   [self.bridge registerHandler:@"testObjcCallback" handler:^(id data, WVJBResponseCallback responseCallback) {
      // 收到JS的调用
      NSLog(@"testObjcCallback called: %@", data);
      // 回调结果给JS
      responseCallback(@"Response from testObjcCallback");
   }];
```



