# 概述WebView与JS交互
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

* iOS 两种方式：
   - JS 里面直接调用方法
   - JS 里面通过对象调用方法
   
方式一：JS 里面直接调用方法



```objc
- (void)webViewDidFinishLoad:(UIWebView *)webView {
    //网页加载完成调用此方法
    //iOS调用js
    //首先创建JSContext 对象（此处通过当前webView的键获取到jscontext
    JSContext *context = [webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    //js调用iOS
    //第一种情况
    //其中test1就是js的方法名称，赋给是一个block 里面是iOS代码
    //此方法最终将打印出所有接收到的参数，js参数是不固定的 我们测试一下就知道
    context[@"jsMethod"] = ^() { 
        NSArray *args = [JSContext currentArguments];  
        for (id obj in args) {  
            NSLog(@"%@",obj);  
        } 
    }

    context[@"jsMethodReturn"] = ^() { 
        return "your result";
    }
}
```
JS调用iOS


```javascript
// 没有返回结果
var paramFromJS = "xxx";
jsMethod(paramFromJS);

// 有返回结果
var returnResult = jsMethodReturn(paramFromJS);
```

方式二：JS 里面通过对象调用方法  
凡事添加了JSExport协议的协议，所规定的方法，变量等 就会对js开放，我们可以通过js调用到  
如果js是一个参数或者没有参数的话 就比较简单，我们的方法名和js的方法名保持一致即可  

首先创建一个类 继承NSObject 并且规定一个协议  
```objc
#import <Foundation/Foundation.h>
#import <JavaScriptCore/JavaScriptCore.h>
 
//首先创建一个实现了JSExport协议的协议
@protocol TestJSObjectProtocol <JSExport>
 
//此处我们测试几种参数的情况
-(void)TestNOParameter;
-(void)TestOneParameter:(NSString *)message;
-(void)TestTowParameter:(NSString *)message1 SecondParameter:(NSString *)message2;
 
@end
 
//让我们创建的类实现上边的协议
@interface TestJSObject : NSObject<TestJSObjectProtocol>
 
@end
```
类的实现  
```objc
#import "TestJSObject.h"
 
@implementation TestJSObject
 
//一下方法都是只是打了个log 等会看log 以及参数能对上就说明js调用了此处的iOS 原生方法
-(void)TestNOParameter
{
    NSLog(@"this is ios TestNOParameter");
}
-(void)TestOneParameter:(NSString *)message
{
    NSLog(@"this is ios TestOneParameter=%@",message);
}
-(void)TestTowParameter:(NSString *)message1 SecondParameter:(NSString *)message2
{
   NSLog(@"this is ios TestTowParameter=%@  Second=%@",message1,message2);
}
@end
```
JS调用iOS

```javascript
window.testobject.TestNOParameter();
window.testobject.TestOneParameter('参数1');
window.testobject.TestTowParameterSecondParameter('参数a','参数b')
```













