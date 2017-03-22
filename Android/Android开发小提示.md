## 目录
- [UI](#ui)
- [数据](#数据)
- [系统](#系统)
- [优化](#优化)
- [其它](#其它)




### UI
- setBackgroundResource(0) 可以移除 View 的背景色。

- Resources.getSystem().getDisplayMetrics().density 可以不用 Context 也能获取屏幕密度。

- android:clipToPadding 设置父view是否允许其子view在它的padding（这里指的是父View的padding）中绘制。是不是有点绕？举    个实际场景吧：假如有个ListView，我们想要在初始位置时，第一项Item离顶部有10dp的距离，就可以在ListView的布局中加入android:clipToPadding="false" android:paddingTop="10dp"即可。是不是很方便呢？

- 使用 GridView或ListView时 android:padding 和 android:clipToPadding="false" 配合使用效果更好。

- 在布局文件中，如果只是为了占位，可以用 Space 来取代 View。 最棒的一点是Space可以跳过 Draw 这个过程。

- TypedValue.applyDimension(int unit, float value, DisplayMetrics metrics) 方便dp, px, sp 之间的转换。

- Html.fromHtml() 如果你对Html熟悉的话，可以很迅速通过这个方法处理一些富文本操作。比如超链接和图文排版等处理。

- TextView.setError() 设置文本框错误提醒。

- getWindow().setLayout(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT); 设置全屏方法一定要在setContentView之前。

- viewpager 的 setCurrentItem 一定要在 setAdapter 方法之后调用才会有效果。

- 谨慎使用Android的透明主题，透明主题会导致很多问题，比如：如果新的Activity采用了透明主题，那么当前Activity的onStop方法不会被调用；在设置为透明主题的Activity界面按Home键时，可能会导致刷屏不干净的问题；进入主题为透明主题的界面会有明显的延时感。

- android listview中的消息被软键盘遮挡了,在设置listview的时候加上android:transcriptMode="normal"就好了。

- listview有个footerDividersEnabled和headerDividersEnabled方法可以设置listview的顶部和底部divide，但是必须保证你设置了headview和footview才会有效果。

- view的isShown方法，只有当view本身以及它的所有祖先们都是visible时，isShown（）才返回TRUE。而平常我们调用if(view.getVisibility() == View.VISIBLE)只是对view本身而不对祖先的可见性进行判断。

- android:descendantFocusability，ListView的item中CheckBox等元素抢焦点导致item点击事件无法响应时，除了给对应的元素设置 focusable,更简单的是在item根布局加上android:descendantFocusability=”blocksDescendants”。

- includeFontPadding="false"，TextView默认上下是有一定的padding的，有时候我们可能不需要上下这部分留白，加上它即可。

- EditText.setImeOptions， 使用EditText弹出软键盘时，修改回车键的显示内容(一直很讨厌用回车键来交互，所以之前一直不知道这玩意儿)。

- TouchDelegate可用于更改View的触摸区域。场景：比如在RecyclerView的ItemView里包含了CheckBox组件, 然后想实现点击ItemView的时候，也可以触发CheckBox，就可以使用此类。

- Activity.recreate重新创建Activity。有什么用呢？可以在程序更换主题后，立马刷新当前Activity，而不会有明显的重启Activity的动画。

- View.post方便在非UI线程对界面进行修改，与Handler的作用类似。并且由于post的Runnable会保证在该View绘制完成的前提下才调用，所以一般也可以用于获取View的宽高。

- Activity.runOnUiThread与View.post类似，方便在非UI线程中对界面进行修改。

- RecyclerView在23.2.+的版本中新增了自动测量的功能，由于新增了自动测量，那么它的item的根布局在需要测量的方向上就不能写match_parent了，需要改成wrap_content

- getParent().requestDisallowInterceptTouchEvent(true);剥夺父view对touch事件的处理权。

- StateListDrawable，定义Selector通常的办法都是xml文件，但是有的时候我们的图片资源可能是从服务器动态获取的，比如很多app所谓的皮肤，这种时候就只能通StateListDrawable来完成了，各种addState即可。

- android:duplicateParentState="true"，让子View跟随其Parent的状态，如pressed等。常见的使用场景是某些时候一个按钮很小，我们想要扩大其点击区域的时候通常会再给其包裹一层布局，将点击事件写到Parent上，这时候如果希望被包裹按钮的点击效果对应的Selector继续生效的话，这时候duplicateParentState就派上用场了。

- 在自定义控件的时候,能用drawable来绘制圆，或者其他样式的时候,尽量用drawable,因为drawable的效果要远胜于canvas.drawXXX()。

- 当PopupWindow中有EditText控件时,因为Popupwindow 默认没有获取到焦点，需要手动设置焦点，这样子view才能获取到事件的监听。所以你需要在创建完popwindow后设置他的焦点，popupWindow.setFocusable(true);就可以让EditText获取焦点。

- tools标签可以很好的帮助开发者实时预览xml的效果,并且运行以后tools标签的内容不会展示出来.例如:

  ```xml
  <TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    tools:text="这段话只在预览时能看到,运行以后就看不到了" />
  ```

- 6.0之后getResources().getColor()方法被废弃了，大家可以用ContextCompat.getColor(context, R.color.color_name)替换，ContextCompat 是 v4 包里的，请放心使用，另外还有getDrawable()等方法。

- 图片的资源文件官方推荐只把launcher放在mipmap文件夹下面，而app用到的资源文件建议放在drawable下面。

- 为你的app添加默认布局样式,比如:每一个控件都需要写width和height属性,然而很多的控件的宽高属性都是wrap_content,那么我们可以通过在style文件添加如下样式：

  ```xml
  <style name="Theme.YourApp" parent="android:style/Theme.Light">
    <item name="android:layout_width">wrap_content</item>
    <item name="android:layout_height">wrap_content</item>
  </style>
  ```
  这样,控件的宽高默认都是wrap_content样式啦。

### 数据
- Pair 这个类 可以用来存储存储一”组”数据。但不是key和value的关系。

- SparseArray 目前有很多地方从性能优化方说使用SparseArray来替换hashMap，来节省内存，提高性能。

- 遍历HashMap的最佳方法：

  ```Java
  public static void printMap(Map mp) {
    Iterator it = mp.entrySet().iterator();
    while (it.hasNext()) {
        Map.Entry pair = (Map.Entry)it.next();
        System.out.println(pair.getKey() + " = " + pair.getValue());
        it.remove(); // avoids a ConcurrentModificationException
    }
  }
  ```

- 使用Java在一个区间内产生随机整数：

  ```Java
   public static int randInt(int min, int max) {
    Random rand = new Random();
    int randomNum = rand.nextInt((max - min) + 1) + min;
    return randomNum;
  }
  ```

- TextUtils 是一个非常好用的工具类，把 List 转成字符串，逗号分隔，逗号分隔的 String 字符串，切割成 List ，分别可以用 TextUtils 的 join 和 split 方法。如果要对 List 去重，则可以用 Collection 的 frequency 方法。

- System类中的arraycopy(src, srcPos, dest, destPos, length)方法，用来copy数组。

- WeakHashMap，直接使用HashMap有时候会带来内存溢出的风险，使用WaekHashMap实例化Map。当使用者不再有对象引用的时候，WeakHashMap将自动被移除对应Key值的对象。

- android中的序列化官方推荐Parceble,其实Parceble最好用于内存之间数据的交换,如果要把数据写入硬盘的话,推荐实现Serializable。

- 时间戳请使用long或者String类型接收，遇到的坑,由于项目中的model好多都是通过GsonFormat生成的，服务器给的json中的时间戳都是10位的，导致了GsonFormat自动解析成了int, 当测试人员选择时间为2100年的时候时间戳是4开头的十位 用int类型接收越界了,导致报错。

- SharedPreferences.Editor.commit这个方法是同步的，一直到把数据同步到Flash上面之后才会返回，由IO操作的不可控，尽量使用apply方法代替。apply只在API Level>=9才会支持，需要做兼容。不过，最新的 support v4 包已经为我们做好了处理，使用 SharedPreferencesCompat.EditorCompat.getInstance().apply(editor) 即可。

- 在多进程之间不要用SharedPreferences共享数据，虽然可以（MODE_MULTI_PROCESS），但极不稳定。

- 尽量不要通过Application缓存数据，这不稳定。

### 系统
- Build.VERSION_CODES 有些时候我们的app需要根据不同的SDK版本进行执行不同的操作。

- PackageManager.getInstalledPackages这个方法经常使用，你可能不知道，当获取的结果数量比较多的时候，在某些机型上面调用它花费的时间可能秒级的，所以尽量在子线程中使用。另外，如果结果太多，超过系统设置的Binder数据最大传输量的上限，则会发生TransactionException，如果你使用这个方法获取机器上的己安装应用列表，最好做一下预防。

- 判断手机是不是飞行模式 boolean isEnabled = Settings.System.getInt(context.getContentResolver(), Settings.System.AIRPLANE_MODE_ON, 0) == 1。

- 在Android 4.0以后，在Manifest.xml中静态注册的广播，程序安装后必须启动一次才能接收到广播，比如你的应用监听开机启动的广播，必须要你的程序被运行过才能监听到。

- Context类中的createPackageContext(packageName, flags)方法，可用来获取指定包名应用程序的Context对象。

### 优化
- 静态变量不要直接或者间接引用Activity、Service等。这会使Activity以及它所引用的所有对象无法释放，然后，用户操作时间一长，内存就会狂升。

- Handler机制有一个特点是不会随着Activity、Service的生命周期结束而结束。也就是说，如果你Post了一个Delay的Runnable，然后在Runnable执行之前退出了Activity，Runnable到时间之后还是要执行的。如果Runnable里面包含更新View的操作，程序崩溃了。

- 不少人在子线程中更新View时喜欢使用Context.runOnUiThread，这个方法有个缺点，就是一但Context生命周期结束，比如Activity已经销毁时，一调用就会崩溃。

- Application的生命周期就是进程的生命周期。只有进程被干掉时，Application才会销毁。哪怕是没有Activity、Service在运行，Application也会存在。所以，为了减少内存压力，尽量不要在Application里面引用大对象、Context等。

- onTrimMemory，在Activity中重写此方法，会在内存紧张的时候回调（支持多个级别），便于我们主动的进行资源释放，避免OOM。

- ViewStub，有的时候一块区域需要根据情况显示不同的布局，通常我们都会通过setVisibility的方法来显示和隐藏不同的布局，但是这样默认是全部加载的，用ViewStub可以更好的提升性能。

- Activity的onDestory方法调用时机是不确定的（有时候离开界面很久之后才会调用onDestory方法），应该避免指望通过onDestory方法去释放与Activity相关的资源，否则会导致一些随机bug。

- 2.X时代Bitmap对象虽然存储在堆内存中，但是用了一个byte数组存储其像素信息。通过计数器来记录该像素信息被引用的个数。有人认为这个byte数组在native堆中，但事实上它也在堆中。只有在使用者调用recycle()后，Bitmap对象才会释放像素信息，才会在失去引用后被垃圾回收机制销毁。再加上DVM的heap size有严格的阀值，所以在使用大量图片资源的时候，及其容易发生OOM。解决办法一般都是，用一个哈希表存储Bitmap对象的软引用，作为内存缓存，并在适当时机调用其recycle()。3.0以上版本Bitmap对象可以通过垃圾回收机制完全销毁，理论上不用再调用recycle()。

### 其它
- 模块间有消息需要传递时，使用LocalBroadcastManager替代Listener进行模块解耦。除了解耦，这样发送消息和执行消息差一个线程循环，可以减小方法的调用链，我这就碰到一次方法调用链太长导致StackOverflow的问题。

- 如果使用Context.startActivity启动外部应用，最好做一下异常预防，因为寻找不到对应的应用时，会抛出异常。如果你要打开的是应用内的Activity，不防使用显式Intent，这样能提高系统搜索目标Activity的效率。

- 如果子类实现Serializable接口而父类未实现时，父类不会被序列化，但此时父类必须有个无参构造方法，否则会抛InvalidClassException异常。

- transient关键字修饰变量可以限制序列化。

- 类继承之间的调用顺序 父类static成员 -> 子类static成员 -> 父类普通成员初始化和初始化块 -> 父类构造方法 -> 子类普通成员初始化和初始化块 -> 子类构造方法。

- 后台service经常因为重启之类的出现onStartCommand()中的Intent传递的参数为null， 通过在onStartCommand()中的返回值改成return super.onStartCommand(intent, Service.START_REDELIVER_INTENT, startId); 可以解决问题。下面介绍几个flag的意思。

  | flag                       | 解释                                       |
  | -------------------------- | ---------------------------------------- |
  | START_STICKY               | 如果service进程被kill掉，保留service的状态为开始状态，但不保留递送的   intent对象。随后系统  会尝试重新创建service，由于服务状态为开始状态，所以创建服务后一定会调用onStartCommand(Intent,int,int)方法。如果在此期间没有任何启动命令被传递到service，那么参数Intent将为null。 |
  | START_NOT_STICKY           | “非粘性的”。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统不会自动重启该服务。 |
  | START_REDELIVER_INTENT     | 重传Intent。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统会自动重启该服务，并将Intent的值传入。 |
  | START_STICKY_COMPATIBILITY | START_STICKY的兼容版本，但不保证服务被kill后一定能重启。     |

- 在activity中调用 moveTaskToBack (boolean nonRoot)方法即可将activity 退到后台，注意不是finish()退出。

- Throwable类中的getStackTrace()方法，根据这个方法可以得到函数的逐层调用地址，其返回值为StackTraceElement[]。StackTraceElement类，其中四个方法getClassName()，getFileName()，getLineNumber()，getMethodName()在调试程序打印Log时非常有用。

- apache提供的一系列jar包：commons-lang.jar，commons-collections.jar，commons-beanutils.jar等，里面很多方法可能是你曾经用几十几百行代码实现过的，但是执行效率或许要差很多，比如：ArrayUtils，StringUtils……。

- ActivityLifecycleCallbacks接口，用于在Application类中监听各Activity的状态变化。
  http://mp.weixin.qq.com/s?__biz=MzA3ODkzNzM3NQ==&mid=401277907&idx=1&sn=0b2246f5178292596fc3a8295283359c#rd

- SystemClock.sleep() 这个方法在保证一定时间的 sleep 时很方便，通常我用来进行 debug 和模拟网络延时。

- UrlQuerySanitizer——使用这个工具可以方便对 URL 进行检查。

- HandlerThread，代替不停new Thread开子线程的重复体力写法。

- View.getContext顾名思义，就不用解释了吧…以前在写RecyclerView的Adapter的时候，为了使用LayoutInflater，经常傻乎乎地在构造函数中传入一个外部的context….是不是只有我不知道而已（笑cry脸）。

- Activity.isChangingConfigurations ()——如果在 Activity 中 configuration 会经常改变的话，使用这个方法就可以不用手动做保存状态的工作了。

- SearchRecentSuggestionsProvider——可以创建最近提示效果的 provider，是一个简单快速的方法。

- 当Activity LauncherMode 为singleTask singleInstance时,使用startActivityForResult会立马返回，不能正常调用。具体请看：http://www.360doc.com/content/15/0123/14/12928831_443085580.shtml

- android studio 2.1起已经支持jdk8了,使用的时候要在gradle中加上,需要把buildToolsVersion更新到24以上的版本。

  ```groovy
  android {
    defaultConfig {
    ...
            jackOptions {
                enabled true
            }
        }
   ...
    compileOptions {
        targetCompatibility 1.8
        sourceCompatibility 1.8
    }
  }
  ```

- 如果你在 manifest 中把一个 activity 设置成 android:windowSoftInputMode="adjustResize"，那么 ScrollView（或者其它可伸缩的 ViewGroups）会缩小，从而为软键盘腾出空间。但是，如果你在 activity 的主题中设置了 android:windowFullscreen="true"，那么 ScrollView 不会缩小。这是因为该属性强制 ScrollView 全屏显示。然而在主题中设置 android:fitsSystemWindows="false" 也会导致 adjustResize 不起作用。

- .gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：

  ​

  ```sh
  git rm -r --cached .
  git add .
  git commit -m 'update .gitignore'        
  ```