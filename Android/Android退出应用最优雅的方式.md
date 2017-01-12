**Receiver+singleTask**可以优雅地退出应用 。我们知道Activity有四种加载模式，而`singleTask`就是其中的一种，使用这个模式之后，当startActivity时，它先会在当前栈中查询是否存在Activity的实例，如果存在，则将其置于栈顶，并将其之上的所有Activity移出栈。


我们打开一个app，首先是一个splash页面，然后会finish掉splash页面。跳转到主页。然后会在主页进行N次的跳转，期间会产生数量不定的Activity，有的被销毁，有的驻留在栈中，但是栈底永远是我们的MainActivity。这样就让问题变得简单很多了。我们只需两步操作即可优雅的实现app的退出。

- 在MainActivity注册一个退出广播，只需要在MainActivity一个页面注册即可。设置MainActivity的启动模式为singleTask。
- 当我们需要退出的时候只需要startActivity(this,MainActivity,class)， 再发送一个退出广播。

上面代码首先会把栈中MainActivity之上的所有Activity移出栈，然后接到广播finish自己。没有弹框，不用考虑机型Rom适配。不会有内存问题。


**疑问：退出时startActivity会不会重新走一遍MainActivity的生命周期？**

我们来验证一下：
在MainActivity中有一个按钮，点击它将跳转到SecondActivity。在SecondActivity中也有一个按钮，点击它将跳转到MainActivity，同时发出退出应用的广播。

MainActivity的代码如下：

```Java
public class MainActivity extends Activity {

    private static final String TAG = "MainActivity";
    private static final String EXIT_ACTION = "exit_action";

    private ExitReceiver mExitReceiver = new ExitReceiver();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(EXIT_ACTION);
        registerReceiver(mExitReceiver, intentFilter);

        findViewById(R.id.main_btn_toSecondActivity).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this, SecondActivity.class));
            }
        });
        Log.d(TAG, "onCreate");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(mExitReceiver);
        Log.d(TAG, "onDestroy");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart");
    }

    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        Log.d(TAG, "onNewIntent");
    }

    class ExitReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            MainActivity.this.finish();
        }
    }

}
```

SecondActivity的代码如下：

```Java
public class SecondActivity extends Activity {

    private static final String EXIT_ACTION = "exit_action";

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        findViewById(R.id.second_btn_toMainActivity).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(SecondActivity.this, MainActivity.class));//跳到MainActivity
                sendBroadcast(new Intent(EXIT_ACTION));//发送退出应用广播
            }
        });
    }

}
```

AndroidManifest.xml的代码如下：

```xml
<activity android:launchMode="singleTask" android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
<activity android:name=".SecondActivity" />
```
> 注意android:launchMode="singleTask"。

执行以上程序的代码后，当点击SecondActivity的按钮跳转到MainActivity时，MainActivity打印的日志如下：

```Java
01-08 21:06:55.321 8405-8405/com.zch.demo D/MainActivity: onNewIntent
01-08 21:06:55.629 8405-8405/com.zch.demo D/MainActivity: onDestroy
```

说明退出时startActivity**不会**重新走一遍MainActivity的生命周期。


另外，如果把SecondActivity中的以下代码注释掉

```Java
sendBroadcast(new Intent(EXIT_ACTION));//发送退出应用广播
```

MainActivity打印的日志如下：

```Java
01-08 21:11:23.688 18293-18293/com.zch.demo D/MainActivity: onNewIntent
01-08 21:11:23.688 18293-18293/com.zch.demo D/MainActivity: onRestart
01-08 21:11:23.690 18293-18293/com.zch.demo D/MainActivity: onStart
01-08 21:11:23.690 18293-18293/com.zch.demo D/MainActivity: onResume
```

这样也说明了singleTask模式只是复用MainActivity，而不会执行onCreate去重建MainActivity。