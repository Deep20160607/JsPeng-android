支持 http2，对一台机器的所有请求共享同一个 Socket

内置连接池，支持连接复用，减少延迟

支持透明的 gzip 压缩响应体

响应缓存可以完全避免网络重复请求

请求失败时自动重试主机的其他 ip，自动重定向

丰富的 API，可扩展性好

```java
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder()
                .get()
                .url("https:www.baidu.com")
                .build();

FormBody formBody = new FormBody.Builder()
                .add("username", "admin")
                .add("password", "admin")
                .build();

final Request request = new Request.Builder()
                .url("http://www.jianshu.com/")
                .post(formBody)
                .build();                

Call call = client.newCall(request);

//同步调用,返回Response,会抛出IO异常
Response response = call.execute();

//异步调用,并设置回调函数
call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        Toast.makeText(OkHttpActivity.this, "get failed", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onResponse(Call call, final Response response) throws IOException {
        final String res = response.body().string();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                contentTv.setText(res);
            }
        });
    }
});
```

使用看这一篇就够了(包括上传下载):https://cloud.tencent.com/developer/article/1910477

原理及常问的面试题:https://blog.csdn.net/jaynm/article/details/112002901

okhttp内部通过责任链模式,将网路请求的各个阶段封装倒各个链条中,实现了各层的解耦.

- 使用流程:
HttpUrlClient#Builder + Request => call => execute()/enqueue() =》Dispatcher(线程池分配请求任务)分发(三个队列 readyAsyncCalls-准备/RunningAsyncCalls/RuningSyncCalls
) =》intercepter拦截器(应用拦截器(RetryFollowUpIntercepter网络重试/CacheIntercepter缓存/BridgeIntercepter添加信息cookie Header user-agent等/ConnectIntercepter连接池-连接复用/创建连接三次握手等,释放 socket流)/网络拦截器CallServerIntercepter网络监控)

- 用到设计模式
构造者、工厂模式、单例模式、策略模式、责任链、享元模式