## 目录
- [性能调优工具Hugo](#性能调优工具hugo)





### 性能调优工具Hugo
在进行Android性能调优、减少应用卡顿时，寻找可优化的code是一个必要的过程。如何发现应用中的耗时任务甚至是耗时函数呢，如果可以在log中打印**每个方法的执行时间**，甚至把执行方法时的**输入输出参数**同时打印，绝对是非常棒的功能。

幸运的是jake Wharton大神已经做出了这样的工具：[Hugo](https://github.com/JakeWharton/hugo)。

**具体使用方法步骤：**

**1、添加依赖**

- 在Project的gradle中的buildscript领域中的dependencies领域里面添加如下代码：

```groovy
classpath 'com.jakewharton.hugo:hugo-plugin:1.2.1'
```

- 在Module的gradle的顶部添加如下代码：

```groovy
apply plugin: 'com.jakewharton.hugo'
```

**2、在代码里使用**

只需在需要打印信息的方法上增加@DebugLog即可：

```Java
public class MainActivity extends Activity {

    private ArrayList<User> mUserList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initUserList(3000);
    }

    @DebugLog
    private boolean initUserList(int count) {
        User user = null;
        for (int i = 0; i < count; i++) {
            user = new User("" + i, "test_" + i);
            mUserList.add(user);
        }
        return true;
    }
}
```

以上例子输出结果如下：

```Java
02-07 12:08:51.227 20972-20972/com.zhi.hello V/MainActivity: ⇢ initUserList(count=3000)
02-07 12:08:51.237 20972-20972/com.zhi.hello V/MainActivity: ⇠ initUserList [7ms] = true
```

> 打印日志只会在debug builds发生，注解也不会在任何编译后的class文件出现。完全不影响非debug builds。