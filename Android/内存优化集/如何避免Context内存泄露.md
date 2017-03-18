## 目录
- [Activity类Context的内存泄露](#activity类context的内存泄露)
- [static修饰的Activity](#static修饰的activity)
- [static间接修饰Activity类Context](#static间接修饰activity类context)
- [单例引用Activity类Context](#单例引用activity类context)
- [非静态或匿名内部类执行耗时任务](#非静态或匿名内部类执行耗时任务)
- [静态内部类持有外部类静态成员变量](#静态内部类持有外部类静态成员变量)
- [Handler引起的内存泄漏](#handler引起的内存泄漏)
- [总结](#总结)






### Activity类Context的内存泄露

Android 中的 Activity Context 内存泄露，简单说就是 Activity 调用 `onDestroy()` 方法销毁后，此 Activity 还被其他对象强引用，导致此 Activity 不能被 GC（JAVA 垃圾回收器） 回收，最后出现内存泄露。

原因主要有两个：

1. 静态的 Activity Context 或 任何包含 Activity Context 的对象（如，View）没有在此 Activity Context 销毁的时候置空引用。
2. 非静态内部类或匿名内部类拥有外部类的引用，在 Activity Context 销毁后，依然执行耗时任务。



### static修饰的Activity

**问题代码：**

```Java
static Activity activity;

void setStaticActivity() {
    activity = this;
}
```

> 这里使用了 static 来修饰 Activity，静态变量持有 Activity 对象很容易造成内存泄漏，因为静态变量是和应用存活时间相同的，所以当 Activity 生命周期结束时，引用仍被持有。

**解决方法：**

1. 去掉 static 关键字，使用别的方法来实现想要的功能。任何时候不建议 static 修饰 Activity，如果这样做了，Android Studio 也会给出警告提示。

2. 在 onDestroy 方法中置空 Activity 静态引用

   ```Java
   @Override
   public void onDestroy() {
       super.onDestroy();
       if (activity != null) {
           activity = null;
       }
   }
   ```

3. 也可以使用到软引用解决，确保在 Activity 销毁时，垃圾回收机制可以将其回收。像下面这样做：

   ```Java
   private static WeakReference<MainActivity> activityReference;

   private void setStaticActivity() {
       activityReference = new WeakReference<MainActivity>(this);
   }
   // 注意在使用时，必须判空
   private void useActivityReference(){
       MainActivity activity = activityReference.get();
       if (activity != null) {
           // ...
       }
   }
   ```



### static间接修饰Activity类Context

**问题代码：**

```Java
public class LoadingDialog extends Dialog {

    private static LoadingDialog mDialog;
    private TextView mText;
}
```

> 这里 static 虽然没有直接修饰 TextView（拥有 Context 引用），但是修饰了 mDialog 成员变量，mDialog 是 一个 LoadingDialog 对象， LoadingDialog 对象 包含一个 TextView 类型的成员变量，所以 mText 变量的生命周期也是全局的，和应用一样。这样，mText 持有的 Context 对象销毁时，没有 GC 回收，导致内存泄露。

**解决方法：**

1. 不使用 static 修饰

2. 在适当的地方如，`dismissLoadingDialog()` 方法中置空 mDialog，这样虽然可以解决，但是也存在风险，如果 `dismissLoadingDialog()` 没有或忘记被调用，同样也会导致内存泄漏。

   ```Java
   public static void dismissLoadingDialog() {
       if (mDialog != null && mDialog.isShowing()) {
           mDialog.dismiss();
           mDialog = null;
       }
   }
   ```



### 单例引用Activity类Context

**问题代码：**

```Java
public class AppManager {

    private static AppManager instance;
    private Context context;

    private AppManager(Context context) {
        this.context = context;
    }

    public static AppManager getInstance(Context context) {
        if (instance == null) {
            instance = new AppManager(context);
        }
        return instance;
    }
}
```

> 这是一段典型的单例模式，不同的是 AppManager 需要 Context 作为成员变量，和上面例子一样，这里延长了 Context 的存活时间，Context 如果是 Activity Context 的话，必然会引起内存泄漏。

**解决方法：**

1. 使用 Applicaion Context 代替 Activity Context (推荐)

   ```Java
   private AppManager(Context context) {
       this.context = context.getAppcalition();
   }
   ```

   或者在 App 中写一个获取 Applicaion Context 的方法。

   ```Java
   private AppManager() {
       this.context = App.getAppcalitionContext();
   }
   ```

2. 在调用的地方使用弱引用

   ```Java
   WeakReference<MainActivity> activityReference = new WeakReference<MainActivity>(this);
   Context context = activityReference.get();
   if(context != null){
       AppManager.getInstance(context);
       // ...
   }
   ```



### 非静态或匿名内部类执行耗时任务

**问题代码：**

```Java
public class MainActivity extends Activity {  
    @Override
    protected void onCreate(Bundle savedInstanceState) {    
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        test();
    }  

    public void test() {    
        new Thread(new Runnable() {     
            @Override
            public void run() {        
                while (true) {          
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }
}
```

> 由于非静态内部类或匿名内部类都会拥有所在外部类的引用，上边的代码，由于 `new Thread`是匿名内部类，并且执行了长时间（一直）的任务，当 Activity 销毁后，该匿名内部类还在执行任务，导致外部的 Activity 不能被回收，导致内存泄露。

**解决方法：**

1. 静态化匿名内部类

   ```Java
   // static 修饰方法
   public static void test() {
       new Thread(new Runnable() {     
               @Override
               public void run() {        
                   while (true) {          
                       try {
                           Thread.sleep(1000);
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                   }
               }
           }).start();
   }
   ```

   如果是内部类，这样写：

   ```Java
   static class MyThread extends Thread {
       @Override
       public void run() {
           // ...
       }
   }
   ```



### 静态内部类持有外部类静态成员变量

虽然静态内部类的生命周期和外部类无关，但是如果在内部类中想要引入外部成员变量的话，这个成员变量必须是静态的了，这也可能导致内存泄露。

**问题代码：**

```Java
public class MainActivity extends Activity {  

    private static MainActivity mMainActivity;
    @Override
    protected void onCreate(Bundle savedInstanceState) {    
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mMainActivity = this;
    }  

    private static class MyThread extends Thread {
        @Override
        public void run() {
           // 耗时操作
           mMainActivity...
        }
    }
}
```

**解决方法：**

使用弱引用。

```Java
public class MainActivity extends Activity {  

    private static WeakReference<MainActivity> activityReference;
    @Override
    protected void onCreate(Bundle savedInstanceState) {    
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        activityReference = new WeakReference<MainActivity>(this);;
    }  

    private static class MyThread extends Thread {
        @Override
        public void run() {
           // 耗时操作
           MainActivity activity = activityReference.get();
           if(activity != null){
               activity...
           }
        }
    }
}
```



### Handler引起的内存泄漏

**问题代码：**

```Java
public class MainActivity extends Activity {

    private final Handler mLeakyHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            // ...
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mLeakyHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                // ...
            }
        }, 1000 * 60 * 10);

        finish();
    }
}
```

> 这种情况和 **非静态或匿名内部类执行耗时任务** 的原因一样。 在 MainActivity 中发送了一个延迟10分钟执行的消息 Message，mLeakyHandler 将其 push 进了消息队列 MessageQueue 里。当该 Activity 被 finish() 掉时，Message 还会继续存在于主线程中，Handler 是非静态内部类，会持有该 MainActivity 的引用，所以此时 finish() 掉的 Activity 就不会被回收了从而造成内存泄漏。

**解决方法：**

自定义静态的 Handler

```Java
public class MainActivity extends Activity {

    private final MyHandler mHandler = new MyHandler(this);

    private static final Runnable mRunnable = new Runnable() {
        @Override
        public void run() { /* ... */ }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mHandler.postDelayed(mRunnable, 1000 * 60 * 10);
        finish();
    }

    private static class MyHandler extends Handler {
        private final WeakReference<MainActivity> mActivityReference;

        public MyHandler(MainActivity activity) {
            mActivityReference = new WeakReference<MainActivity>(activity);
        }

        @Override
        public void handleMessage(Message msg) {
            MainActivity activity = mActivityReference.get();
            if (activity != null) {
                // ...
            }
        }
    }
}
```

> 首先定义一个静态的 MyHandler 类，它将不会隐式的持有 MainActivity 的引用，并且内部利用弱引用获取外部类的引用，这样在 MainActivity 被 finish 掉后，弱引用不会影响 MainActivity 被回收，也就避免了内存泄露。另外，成员变量 mRunnable 也是静态的，生命周期和应用一样，并且不持有外部类的引用。



### 总结

Android 的 Context 内存泄露，其实就是因为 Activity 是有生命周期的，所以在 Activity 销毁后，必须释放掉所有对其的强引用，否则 GC 将不会及时回收已经不再使用的 Activity，导致内存泄露。所以，我们在使用 Activity Context 的时候，应该注意判断下在 Activity 销毁时此变量是否依然引用 Activity。

[原文](https://juejin.im/post/58cb97e1128fe1006c84aafe)