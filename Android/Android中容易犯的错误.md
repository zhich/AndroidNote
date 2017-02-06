## 目录
- [Frgment重叠解决方案](#frgment重叠解决方案)
- [解决CheckBox的button和文字在不同Android版本间距不一致的问题](#解决checkbox的button和文字在不同android版本间距不一致的问题)
- [你需要知道的Android拍照适配方案](#你需要知道的android拍照适配方案)
- [Android快速读取联系人示例](#android快速读取联系人示例)
- [Android搜索把软键盘上的回车键改为搜索](#android搜索把软键盘上的回车键改为搜索)



### Frgment重叠解决方案
如果只是消除重叠，其实很简单，重叠的原因是因为Activity在重建前保存了状态，重建后程序又新建了Fragment，导致重叠。
那么，不让Activity保存状态不就好了？
重写下以下的方法，里面什么也不做。

```Java
@Override
protected void onSaveInstanceState(Bundle outState) {

}
```

### 解决CheckBox的button和文字在不同Android版本间距不一致的问题
Checkbox设置完button属性后，文本和button之前在2.3以下没有padding，在4.0设备中确有相当大的padding。当然可以通过设置paddingLeft属性调整这个间距，但是这样4.0没问题，2.3问题又出来了。

​那怎么样来设置Checkbox的自定义选框且不产生padding的问题呢？

那就是不要使用button属性(button=”@null”)，通过drawableLeft 来设置自定义的选框，并且把backgroud设置为@null, 选框和文字的padding可以用过drawablePadding属性来进行设置。这样就不会出现在不同OS下，padding表现完全不一致的情况了。

参考代码如下：

```Java
<CheckBox
    android:id="@+id/login_auto_login"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="10dp"
    android:background="@null"
    android:button="@null"
    android:checked="false"
    android:drawableLeft="@drawable/uc_auto_login_check_selector"
    android:drawablePadding="6dp"
    android:text="@string/login_auto_login"
    android:textColor="@color/gray"
    android:textSize="18sp"/>
```

[原文地址](http://www.jianshu.com/p/0f464a2722de)

###  你需要知道的Android拍照适配方案
http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0602/4323.html

### Android快速读取联系人示例
http://www.apkbus.com/android-95266-1-1.html

### Android搜索把软键盘上的回车键改为搜索
在xml的EditText控件添加如下代码：

```xml
android:imeOptions="actionSearch"
android:singleLine="true"
```

在Java类添加如下代码：

```Java
edtKeyword.setOnKeyListener(new View.OnKeyListener() {
            @Override
            public boolean onKey(View v, int keyCode, KeyEvent event) {
                if (keyCode == KeyEvent.KEYCODE_ENTER && event.getAction() == KeyEvent.ACTION_UP) {
                    // 先隐藏键盘
                    ((InputMethodManager) getSystemService(INPUT_METHOD_SERVICE))
                            .hideSoftInputFromWindow(CustomerSearchActivity.this.getCurrentFocus().getWindowToken(), InputMethodManager.HIDE_NOT_ALWAYS);
                    //进行搜索操作的方法，在该方法中可以加入edtKeyword的非空判断
                    // search();
                }
                return false;
            }
        });
```