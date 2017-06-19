## 目录

- [IntentService可处理耗时操作](#intentservice可处理耗时操作)
- [Service和Thread的区别](#service和thread的区别)







### IntentService可处理耗时操作

`IntentService` 使用队列的方式将请求的 Intent 加入队列，然后开启一个 worker thread (线程) 来处理队列中的Intent，对于异步的 startService 请求，IntentService 会处理完成一个之后再处理第二个，每一个请求都会在一个单独的 worker thread 中处理，不会阻塞应用程序的主线程，这里就给我们提供了一个思路，如果有耗时的操作与其在 Service 里面开启新线程还不如使用 IntentService 来处理耗时操作。



IntentService 继承 Service，使用工作队列来处理所有请求，一次处理一件。当有多个请求同时发出，可以保证线程的安全性。你所要做的只需要实现 `onHandleIntent()` 方法在后台处理从不同的 Intent 发来的请求。



Demo：

```Java
public class MyIntentService extends IntentService{  
  
    public MyIntentService() {  
        super("aaa");  
    }  
    @Override  
    protected void onHandleIntent(Intent intent) {  
        //经测试，IntentService里面是可以进行耗时的操作的.  
        //IntentService使用队列的方式将请求的Intent加入队列，  
        //然后开启一个worker thread(线程)来处理队列中的Intent.  
        //对于异步的startService请求，IntentService会处理完成一个之后再处理第二个.  
          
        System.out.println("睡眠开始");  
        try {  
            Thread.sleep(10000);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("睡眠结束");  
    }         
｝  
  
public class MainActivity extends Activity {  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
          
        //连续两次启动IntentService，会发现应用程序不会阻塞，而且最重要的是第二次的请求会在第一个请求结束   之后运行  
        //(这个证实了IntentService采用单独的线程每次只从队列中拿出一个请求进行处理)  
        startService(new Intent(this,MyIntentService.class));  
        startService(new Intent(this,MyIntentService.class));  
    }  
}  

```

### Service和Thread的区别

servie是系统的组件，它由系统进程托管（servicemanager）；它们之间的通信类似于client和server，是一种轻量级的ipc通信，这种通信的载体是binder，它是在linux层交换信息的一种ipc。而thread是由本应用程序托管。

* **Thread**：Thread 是程序执行的最小单元，它是分配CPU的基本单位。可以用 Thread 来执行一些异步的操作。
* **Service**：Service 是android的一种机制，当它运行的时候如果是Local Service，那么对应的 Service 是运行在主进程的 main 线程上的。如：onCreate，onStart 这些函数在被系统调用的时候都是在主进程的 main 线程上运行的。如果是Remote Service，那么对应的 Service 则是运行在独立进程的 main 线程上。

既然这样，那么我们为什么要用 Service 呢？其实这跟 android 的系统机制有关，我们先拿 Thread 来说。Thread 的运行是独立于 Activity 的，也就是说当一个 Activity 被 finish 之后，如果你没有主动停止 Thread 或者 Thread 里的 run 方法没有执行完毕的话，Thread 也会一直执行。因此这里会出现一个问题：当 Activity 被 finish 之后，你不再持有该 Thread 的引用。另一方面，你没有办法在不同的 Activity 中对同一 Thread 进行控制。  

举个例子：如果你的 Thread 需要不停地隔一段时间就要连接服务器做某种同步的话，该 Thread 需要在 Activity 没有start的时候也在运行。这个时候当你 start 一个 Activity 就没有办法在该 Activity 里面控制之前创建的 Thread。因此你便需要创建并启动一个 Service ，在 Service 里面创建、运行并控制该 Thread，这样便解决了该问题（因为任何 Activity 都可以控制同一 Service，而系统也只会创建一个对应 Service 的实例）。

因此你可以把 Service 想象成一种消息服务，而你可以在任何有 Context 的地方调用 Context.startService、Context.stopService、Context.bindService，Context.unbindService，来控制它，你也可以在 Service 里注册 BroadcastReceiver，在其他地方通过发送 broadcast 来控制它，当然这些都是 Thread 做不到的。