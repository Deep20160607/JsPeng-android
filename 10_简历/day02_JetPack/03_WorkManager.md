WorkManager API 是一个适合用来替换所有先前的 Android 后台调度 API(包括 FirebaseJobDispatcher、GcmNetworkManager和 JobScheduler的组件)

implementation 'androidx.work:work-runtime-ktx:2.7.1'

链式调用 内部维护Room数据库 +监听广播(电量/内存等)+ 线程池executor


简单实用及原理:https://juejin.cn/post/7041834655818514440

https://www.jianshu.com/p/fc7bc5234912
- Request初始化 OneTimeWorkRequest.Builder(PublishWorker::class.java).build()
- Builder构造器
    ```java
    Builder(@NonNull Class<? extends ListenableWorker> workerClass) {
    mId = UUID.randomUUID();
    mWorkerClass = workerClass;
    mWorkSpec = new WorkSpec(mId.toString(), workerClass.getName());
    addTag(workerClass.getName());
}
    ```
- WorkManager执行Request
    - WorkManager.getInstances(Context)
    - WorkManager.enqueue(request)
- 初始化WorkManagerInitializer
    ```java
      <provider
            android:name="androidx.work.impl.WorkManagerInitializer"
            android:exported="false"
            android:multiprocess="true"
            android:authorities="com.nene.workmanagerdemo.workmanager-init"
            android:directBootAware="false" />
    ```
- WorkManager.getInstance(this).beginWith(request).then(request).then(request).enqueue()



```java
class ChangeDataWorker(val appContext: Context, val workerParams: WorkerParameters): Worker(appContext,workerParams){

    override fun doWork(): Result {
        //接收传递过来的数据
        val data = workerParams.inputData.getString("Data")
        Log.d("ChangeDataWorker","data:$data")
        //将数据返回出去
        var returnData=Data.Builder().putString("returnData","doWork").build()
        return  Result.success(returnData)
    }
}

----------------------

 val changeDataWorker=OneTimeWorkRequestBuilder<ChangeDataWorker>()
        .setInputData(data)
        .build()
    //想接收任务回馈的数据，需要状态机 livedata
    WorkManager.getInstance(this).getWorkInfoByIdLiveData(changeDataWorker.id)
        .observe(this,
            {
                Log.d("MainActivity","状态:${it.state.name}")
                if(it.state.isFinished){
                    Log.d("MainActivity","回传的数据:${it.outputData.getString("returnData")}")
                    Log.d("MainActivity","状态:isFinished = true 后台任务完成")
                }
            })
    //将任务提交给WorkManager
    WorkManager.getInstance(this).enqueue(changeDataWorker)


    ----------多任务
    var task1=OneTimeWorkRequestBuilder<TaskWorker1>().build()
    var task2=OneTimeWorkRequestBuilder<TaskWorker2>().build()
    var task3=OneTimeWorkRequestBuilder<TaskWorker3>().build()

    //第一种 按顺序执行 task1 task2 task3
    WorkManager.getInstance(this)
        .beginWith(task1)
        .then(task2)
        .then(task3)
        .enqueue()
   //第二种
   var list= mutableListOf<OneTimeWorkRequest>()
    list.add(task1)
    list.add(task2)

    // 先执行task1 task2 最后执行task3
    WorkManager.getInstance(this).beginWith(list)
        .then(task3).enqueue()

```

暂时没有细看原理,只会使用
