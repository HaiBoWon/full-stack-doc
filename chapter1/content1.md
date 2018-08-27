# WebView与JS交互
 * Android & iOS 调用 JS 的方法 
 * JS 调用 Android & iOS 的方法

** Android & iOS 调用 JS 的方法，伪代码如下：**

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
   function show(str) {
      alert(str);
   }
    
   function showReturn(str) {
      return "result";
   }
```

这种方式，缺陷很明显：

Android 没法拿到返回值；但是，iOS是可以拿到返回值的，这是最重要的区别！！！另外，我们也无法传递一个回调接口Callback用于回调，也就是说此方法调用成功与否，是无法知道的。

show方法必须是 JS 中存在的，即使不存在你调用了也不会报错；另外，随着业务的增长，我们不得不增加许许多多类似show的方法，来处理其他业务。

** JS 调用 Android & iOS 的方法，伪代码如下：**


```java
private class JsToNative {
  // 没有返回结果        
  @JavascriptInterface 
  public void jsMethod(String paramFromJS) { 
  
  } 

  // 有返回结果
  @JavascriptInterface 
  public String jsMethodReturn(String paramFromJS) { 
     return "your result";
  } 
}

// JsToNative就是一个别名，你可以随意
webView.addJavascriptInterface(new JsToNative(), "JsToNative");
```

JS 调用 Android


```javascript
// 没有返回结果
var paramFromJS = "xxx";
window.JsToNative.jsMethod(paramFromJS);

// 有返回结果
var returnResult = window.JsToNative.jsMethodReturn(paramFromJS);
```

* iOS
<br/>两种方式：
- JS 里面直接调用方法
- JS 里面通过对象调用方法

### WebViewJavascriptBridge 方式 android & ios
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



