如果只是消除重叠，其实很简单，重叠的原因是因为Activity在重建前保存了状态，重建后程序又新建了Fragment，导致重叠。
那么，不让Activity保存状态不就好了？
重写下以下的方法，里面什么也不做。

```Java
@Override
protected void onSaveInstanceState(Bundle outState) {

}
```
