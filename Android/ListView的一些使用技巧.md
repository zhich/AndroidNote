- [设置 item 间的分割线](#设置 item 间的分割线)
- [取消 item 的点击效果](#取消 item 的点击效果)
- [设置 ListView 显示位置](#设置 ListView 显示位置)
- [遍历 ListView 中所有 item](#遍历 ListView 中所有 item)







## 设置 item 间的分割线

divider: 设置分割线，可以为颜色或者图片资源。

dividerHeight: 设置分割线的高度。



## 取消 item 的点击效果

android:listSelector=”#00000000” 或者 android:listSelector=”@android:color/transparent”



## 设置 ListView 显示位置

mListView.setSelection(N);



## 遍历 ListView 中所有 item

ListView 作为一个 ViewGroup，为我们提供了操纵子 View 的各种方法。

最常用的就是通过 getChildAt() 来获取第 i 个子 View.

```java
For(int i = 0 ; i<mListView.getChildCount(); i++){
	View view =mListView.getChildAt(i);
}
```