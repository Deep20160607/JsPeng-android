!!!已经被弃用
```
public abstract class AsyncTask<Params, Progress, Result> {
}
```
- execute(Params... params)
- onPreExecute()
- doInBackground(Params... params) 子线程
- onPostExecute(Result result)
- onProgressUpdate(Progress... value) -> publishProgress(Progress... value)调用


!! 生命周期结束前一定要调用: xxTask.cancel(true);

## 解析

线程池+handle(InternalHandler)

用户调用task.execute() =>
onPreExecute()=>
交给线程池sDefaultExecutor调度=>
mFuture 配合 mWorker开启子线程=>
doInBackground()=>
交给内部单例InternalHandler处理返回结果并返回到主线程=>
根据Message处理onProgressUpdate()或onPostExecute()


- 优点: 使用线程池管理多线程，资源利用和效率高，管理方便.
- 缺点：AsyncTask的default Exectuor是一个Serial_Executor并且这个线程池是设为Static的串行管理线程池。 无论你开了多少个AsyncTask对象，所有这些对象其实是共用一个线程池的，而且这个线程池的策略只是很简单地按序一个个处理。
这样会出现什么问题？假设你有一个大文件要下载，网络不怎么好，需要很长时间来完成。如果这个下载任务是用asynctask来实现的，那么所有其他的用asynctask的任务将会被这个下载任务阻塞住，直到它完成才能运行。因此，如果使用默认线程池，asynctask是不适合处理长时间后台任务的。

