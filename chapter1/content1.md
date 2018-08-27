# WebViewJavascriptBridge 方式

  该方式主要作用于Objective-C 和 javascript 相互通信，即 oc和 js 方法的互相调用，这是[marcuswestin](https://github.com/marcuswestin)公司开源的一个用于 iOS/OSX 平台 webview 与 JS 通信的方案，它在 webview 和 JS 之间“架了”一座桥梁，提供了非常便捷的通信方式。  
  android也可以统一使用，在初始的时候需要做些兼容处理。

### 原生处理

```objc
self.bridge = [WebViewJavascriptBridge bridgeForWebView:webView];
   [self.bridge registerHandler:@"testObjcCallback" handler:^(id data, WVJBResponseCallback responseCallback) {
      // 收到JS的调用
      NSLog(@"testObjcCallback called: %@", data);
      // 回调结果给JS
      responseCallback(@"Response from testObjcCallback");
   }];
```



