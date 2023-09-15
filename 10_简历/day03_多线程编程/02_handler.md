# 一 源码 Handle---Looper---Messge---MessageQueue

## 1 Looper源码(简要)
```java
public class Looper {
    // 用于存储每个线程的Looper对象
    private static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<>();

    // 创建Looper对象，并存储到sThreadLocal中
    public static void prepare() {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper());
    }

    // 获取当前线程的Looper对象
    public static Looper myLooper() {
        return sThreadLocal.get();
    }

    // 消息循环处理消息队列
    public void loop() {
        MessageQueue queue = myQueue();
        while (true) {
            Message msg = queue.next(); // 获取下一个消息
            if (msg != null) {
                msg.target.dispatchMessage(msg); // 分发消息给Handler
                msg.recycle(); // 回收消息对象
            }
        }
    }

    // 获取当前线程的消息队列
    private MessageQueue myQueue() {
        return mQueue;
    }

    // 构造方法私有，确保只有prepare方法可以创建Looper对象
    private Looper() {
        mQueue = new MessageQueue();
    }
}

```

## 2 MessageQueue 源码(简要)
```java
public class MessageQueue {

    private Queue<Message> queue = new LinkedList<>();

    public synchronized void enqueueMessage(Message message) {
        queue.offer(message);
    }

    public synchronized Message next() {
        return queue.poll();
    }
}

```

## 3 Handle 源码(简要)
```java
public class Handler {

    private MessageQueue messageQueue;

    public Handler() {
        Looper looper = Looper.myLooper(); // 获取当前线程的 Looper
        if (looper == null) {
            throw new RuntimeException("Can't create Handler inside thread that has not called Looper.prepare()");
        }
        messageQueue = looper.getMessageQueue();
    }

    public void sendMessage(Message message) {
        messageQueue.enqueueMessage(message);
    }

    public void handleMessage(Message message) {
        // 处理消息
    }

    public void dispatchMessage(Message message) {
        handleMessage(message);
    }
}

```

## 4 Message
```java 
public final class Message {
    // 表示消息的目标 Handler
    Handler target;
    
    // 消息类型
    int what;

    // 消息附带的数据
    Object obj;
    
    // 其他消息相关字段
    // ...
}
```

# 二 流程

## 创建handle
- 1 主线程handle
Handler handler = new Handler(Handler.getManLooper())
- 子线程:
Looper.prepare();//准备阶段:创建当前线程的TreadLocal.set(new Looper())=>new MessageQueue() 说明looper和messageQueue 一对一
Handler handler = new Handler()
Looper.loop();//开启消息轮询: 

## 创建Message
- 1 new Message()
- 2 Message.obtain(handle, message_id);//对象池复用 =>message.target = handle 此时message中的target持有handler
- 3 handler.obtainMessage(meeage_id);//同2

message.what、message.object

## 发送消息
handle.sendMessage();// 调用messageQueue.enqueueMessage(message);忘meesageQueue中添加了一条消息

## 接受
Looper.loop();轮询中通过mq.next()拿到队列头的消息,通过dispatchMessage()分发处理消息=》message.target.handlerMessage()处理消息


# 三 looper死循环为什么不会导致应用卡死
https://blog.csdn.net/yichen97/article/details/106367067

如果想要一个线程运行时间长，而且后边还可以让其进行处理事情，用Looper消息循环的方式来控制它最好，死循环也就是为了让这个线程保持生命，不然执行完一段代码就关闭回收了；再次使用线程就可以使用消息的方式发送一个中间带有执行代码的Runnable()消息给线程执行。

如果这个Android中的非UI线程如果没有事情做，应该是处于休眠状态，不会消耗太多资源。但是如果这个线程确实不用了，最好把looper消息循环停止掉，回收掉这个线程。

主线程的死循环一直运行是不是特别消耗CPU资源呢？ 其实不然，这里就涉及到Linux pipe/epoll机制，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。 Gityuan–Handler(Native层)

ActivityTread 的 main()入口中:
```java
...
Looper.prepareMainLooper();
...
Looper.loop()
```
