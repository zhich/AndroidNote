## 目录

- [IntentService可处理耗时操作](#intentservice可处理耗时操作)







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

