# html-app
h5与app交互

### H5调用APP本地方法
> js代码

```js
/**
 * @description html5CallApp 调起支付
 * @param data { String }  data  h5传给app的数据 需要APP定义格式
 * @return returnVal { String }  app返回给h5的值（如不需要可不返回）
 *  DTCJSObject.htmlOpenSlide  app本地方法
 */
var returnVal = window.DTCJSObject.htmlOpenSlide(data)
```
>android 代码

```android
webView.addJavascriptInterface(new JSMethod(), "DTCJSObject"

private class JSMethod {
        @JavascriptInterface
        public void htmlOpenSlide(String flag) {
            // runOnUiThread(new Runnable() {
            //    @Override
            //    public void run() {
            //        openDrawer();
            //    }
           // 有返回值  就加一个return
                return ‘返回给H5的值’
            });
        }
```
> ios代码

```ios
    //首先创建JSContext 对象（此处通过当前webView的键获取到jscontext）
    //js是通过对象调用的，我们假设js里面有一个对象 testobject 在调用方法
    //首先创建我们新建类的对象，将他赋值给js的对象
    JSContext *context=[self.webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    self.JSObject = [DTCJSObject new];
    context[@"DTCJSObject"] = self.JSObject;
    //同样我们也用刚才的方式模拟一下js调用方法
    NSString *jsStr1 = @"htmlOpenSlide()";
    [context evaluateScript:jsStr1];
```

### APP调用js

> js代码

``` js
    /**
      * @descripttion appCallHtml
      * app 可以直接调用 'appCallHtml' 这个方法
      * @param data  是app传给h5的数据
    */
      window.appCallHtml = function(data) {
            alter(data)
      }
```
> android代码

```android
private void callJSMethod() {  //调用js的方法
        new Handler().post(new Runnable() {
            @Override
            public void run() {
                webView.loadUrl("javascript:appCallHtml('这是传给h5的数据')");
            }
        });
    }
```
> ios代码

```ios
JSContext *context = [_webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
    //oc 调用 js
    NSString *textJS = @"appCallHtml('这是传给h5的数据')";
    [context evaluateScript:textJS];
```

## 常见问题
##### 1、webView “alert”无法使用
app没有打开允许webView弹框的功能

##### 2、TypeError: Cannot read property 'setItem' of null", source
是因为android 没有打开 允许使用Storage数据 
需要打开：
```android
webSettings.setDomStorageEnabled(true);//允许使用Storage数据 
webSettings.setJavaScriptEnabled(true); //支持js
可参考：http://www.jianshu.com/p/c13b394e1143
```
##### 3、webView 返回问题(较好的方法1)
> android代码

```android
@Override
public boolean onKeyDown (int keyCode, KeyEvent event)  {
	if(keyCode == KeyEvent,KEYCODE_BACK)  {
		if(webView.canGoBack())  {
		  webView.goBack();
		  return true;
		}
	}
}
```