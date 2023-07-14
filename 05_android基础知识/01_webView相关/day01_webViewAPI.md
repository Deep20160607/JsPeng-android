直接看这篇文章:https://blog.csdn.net/carson_ho/article/details/79999529

- ***WebView***   
loadUrl()、goBack()

- ***WebViewSettings*** 配置管理   
setCacheMode()缓存、setJavaScriptEnabled()与js交互

- ***WebViewClient*** 处理通知、请求事件   
shouldOverrideUrlLoading()、onPageStarted()、onPageFinished()

shouldOverrideUrlLoading拦截的是url加载阶段，主要拦截url

shouldInterceptRequest加载的是响应主体阶段，可拦截url、js、css等

- ***WebViewChromeClient*** 辅助WebView处理Javascript对话框(图标,标题、jin du等)   
onProgressChanged()、onReceivedTitle()、onJsAlert()、onJsConfirm()、onJsPrompt()

## WebView与JS交互方式
对于Android调用JS代码的方法有2种：

- 通过WebView的loadUrl（）
- 通过WebView的evaluateJavascript（）


对于JS调用Android代码的方法有3种：

- 通过WebView的addJavascriptInterface（）进行对象映射
- 通过 WebViewClient 的shouldOverrideUrlLoading ()方法回调拦截 url
- 通过 WebChromeClient 的onJsAlert()、onJsConfirm()、onJsPrompt（）方法回调拦截JS对话框alert()、confirm()、prompt（） 消息


## 漏洞
https://bbs.kanxue.com/thread-273867.htm


## 缓存机制构建
