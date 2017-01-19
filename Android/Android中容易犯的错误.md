- [Frgment重叠解决方案](#frgment重叠解决方案)
- [解决CheckBox的button和文字在不同Android版本间距不一致的问题](#解决checkbox的button和文字在不同android版本间距不一致的问题)




## Frgment重叠解决方案
如果只是消除重叠，其实很简单，重叠的原因是因为Activity在重建前保存了状态，重建后程序又新建了Fragment，导致重叠。
那么，不让Activity保存状态不就好了？
重写下以下的方法，里面什么也不做。

```Java
@Override
protected void onSaveInstanceState(Bundle outState) {

}
```

## 解决CheckBox的button和文字在不同Android版本间距不一致的问题
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
